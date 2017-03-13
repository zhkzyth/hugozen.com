---
title: the-thought-of-yii2-rbac
date: 2017-03-13 13:40:33
tags: rbac yii2 notes
---

# YII2的rbac思路

最近在做一个基于yii2的项目，需要用到权限控制的功能，于是考察了yii2原生的权限解决方案。

一般来说，权限系统有简单做，也有复杂做。我在做的时候，主要有3个点考虑：资源、用户、关系。资源有粒度的概念，比如某种资源、特定资源、部分资源等。用户也有粒度的概念，单个用户、角色、用户组等。而关系，就是描述用户与资源之间，用户是否能够执行某个动作，典型如增删改查。

总体来看，yii2的原生解决方案实现得比较简单，不过思路比较轻巧。

### 特性
因为我考察的是基于数据库的解决方案，所以下面主要围绕这个实现来说。这个权限控制的特点：
- 基于角色模型，[role base access control][1]
- 支持角色（Role）、权限（Permission）、规则（Rule）三种控制层次
- 角色可以继承
- 一个用户可以拥有多个角色
- 不支持直接赋予权限给用户，即用户只能有角色，不能直接拥有某个权限
- 规则只能添加到权限上

<!-- more -->

### API使用
```
// 获取yii的权限管理对象
$auth = Yii::$app->authManager;

// 添加 "createPost" 权限
$createPost = $auth->createPermission('createPost');
$createPost->description = 'Create a post';
$auth->add($createPost);

// 添加 "updatePost" 权限
$updatePost = $auth->createPermission('updatePost');
$updatePost->description = 'Update post';
$auth->add($updatePost);

// 添加"author"角色，并赋予"createPost"权限
$author = $auth->createRole('author');
$auth->add($author);
$auth->addChild($author, $createPost);

// 添加"admin"角色，并赋予"updatePost"权限
// 同时让"author"角色继承"admin"
$admin = $auth->createRole('admin');
$auth->add($admin);
$auth->addChild($admin, $updatePost);
$auth->addChild($admin, $author);

// 把特定的角色赋给用户
$auth->assign($author, 2);
$auth->assign($admin, 1);
```

在这里，我们完成了权限的初始化。目前系统的权限是下面这样的（yii2官网直接拿过来）：
![yii2校验流程](http://www.yiiframework.com/doc-2.0/images/rbac-hierarchy-1.png)

那程序在校验的时候，流程具体是怎么样的呢？

先看下api：
```
if (\Yii::$app->user->can('updatePost', ['post' => $post])) {
    // update post
}
```
在这里，假设用户想操作一个post，我们直接调用`user`类的`can`方法，然后去查表。我们这里先讲流程。大致的思路是。查找用户所有的角色，然后把这个角色下面的权限找出来，做一个并集，然后再问这个权限有没在用户的权限集里面。

### 数据结构
基于上述的考虑，数据库实现的权限控制方案，采用了下面4张表：
- rule表
Rule表对应"规则"，主要用来存储规则信息。

- auth_item表
这个表比较特殊，yii2的实现是把`Role`和`Permission`两个信息也存在一起。

- auth_item_child表
这个表用来存储父子关系，包括`Role`之间的继承关系，`Role`和`Permission`之间的包含关系。

- auth_assignment
最后这张表，存储的是角色和用户之间的多对多关系了，一个用户可以拥有多个角色。

具体的sql语句：
```
create table `auth_rule`
(
   `name`                 varchar(64) not null,
   `data`                 blob,
   `created_at`           integer,
   `updated_at`           integer,
    primary key (`name`)
) engine InnoDB;

create table `auth_item`
(
   `name`                 varchar(64) not null,
   `type`                 smallint not null,
   `description`          text,
   `rule_name`            varchar(64),
   `data`                 blob,
   `created_at`           integer,
   `updated_at`           integer,
   primary key (`name`),
   foreign key (`rule_name`) references `auth_rule` (`name`) on delete set null on update cascade,
   key `type` (`type`)
) engine InnoDB;

create table `auth_item_child`
(
   `parent`               varchar(64) not null,
   `child`                varchar(64) not null,
   primary key (`parent`, `child`),
   foreign key (`parent`) references `auth_item` (`name`) on delete cascade on update cascade,
   foreign key (`child`) references `auth_item` (`name`) on delete cascade on update cascade
) engine InnoDB;

create table `auth_assignment`
(
   `item_name`            varchar(64) not null,
   `user_id`              varchar(64) not null,
   `created_at`           integer,
   primary key (`item_name`, `user_id`),
   foreign key (`item_name`) references `auth_item` (`name`) on delete cascade on update cascade
) engine InnoDB;
```

### 代码层面（算法实现）

相关的代码，在框架的路径下面，`yii2/framework/rbac`，具体的文件有如下几个，我们逐一来看看：
```
├── Assignment.php  // 对应auth_assignment表，描述用户与角色的关系
├── BaseManager.php  // 忽略
├── CheckAccessInterface.php // 权限校验接口
├── DbManager.php  // 基于数据库实现的权限校验类
├── Item.php  // Role和Permission的基类
├── ManagerInterface.php // 忽略
├── Permission.php // 权限类
├── PhpManager.php // 忽略
├── Role.php // 角色类
├── Rule.php // 规则类
```

 整个代码的思路，比较简单，框架先提出了3个对象，分别是`Role`,`Permission`,`Rule`，这映射到我们之前提到过的RBAC概念，分别对应角色、权限、规则。

主要的权限校验，其实是在`DbManager.php`实现，而关键的方法，就是下面这个。
```

    /**
     * Performs access check for the specified user.
     * This method is internally called by [[checkAccess()]].
     * @param string|int $user the user ID. This should can be either an integer or a string representing
     * the unique identifier of a user. See [[\yii\web\User::id]].
     * @param string $itemName the name of the operation that need access check
     * @param array $params name-value pairs that would be passed to rules associated
     * with the tasks and roles assigned to the user. A param with name 'user' is added to this array,
     * which holds the value of `$userId`.
     * @param Assignment[] $assignments the assignments to the specified user
     * @return bool whether the operations can be performed by the user.
     */
    protected function checkAccessRecursive($user, $itemName, $params, $assignments)
    {
		// 参数分析：user是用户类，itemName是具体要查询的权限名（或者角色名）,ssignments是用户的角色集

        // 获取这个itemName对应的数据，可能是role，也可能是permission
        if (($item = $this->getItem($itemName)) === null) {
            return false;
        }

        Yii::trace($item instanceof Role ? "Checking role: $itemName" : "Checking permission: $itemName", __METHOD__);

		// 暂时先忽略对rule的考虑
        if (!$this->executeRule($user, $item, $params)) {
            return false;
        }

		// 角色名是否在用户的assignment里面，或者在系统的默认角色列表里面
        if (isset($assignments[$itemName]) || in_array($itemName, $this->defaultRoles)) {
            return true;
        }

		// 构造查询函数对象
        $query = new Query;
        $parents = $query->select(['parent'])
            ->from($this->itemChildTable)
            ->where(['child' => $itemName])
            ->column($this->db);

		// 递归查询
        foreach ($parents as $parent) {
            if ($this->checkAccessRecursive($user, $parent, $params, $assignments)) {
                return true;
            }
        }

        return false;
    }
```

整个函数关键点，其实理解这个递归查找本身。我们在代码层面，比如更新某个post，可能会写如下代码：
```
if (\Yii::$app->user->can('updatePost', ['post' => $post])) {
    // update post
}
```
这个时候，系统会跑到这个函数里面，调用这个递归查找，但代码并不是一开始就查找`updatePost`这个`Permission`本身是否匹配用户的`assignments`列表的，而是查找这个`Permission`的父级，把它归属的那些`Role`找出来，然后去匹配`assignments`列表。可能一级找不到，那就多级查找。

那基于Rule的情况，又如何理解呢？

你可以理解为，在`Permission`上面，写入自己的逻辑校验，实现粒度更小的控制。

我们看下官方给的代码：
```
$auth = Yii::$app->authManager;

// add the rule
$rule = new \app\rbac\AuthorRule;
$auth->add($rule);

// add the "updateOwnPost" permission and associate the rule with it.
$updateOwnPost = $auth->createPermission('updateOwnPost');
$updateOwnPost->description = 'Update own post';
$updateOwnPost->ruleName = $rule->name;
$auth->add($updateOwnPost);

// "updateOwnPost" will be used from "updatePost"
$auth->addChild($updateOwnPost, $updatePost);

// allow "author" to update their own posts
$auth->addChild($author, $updateOwnPost);
```

图片示意：
![rbac-rule-based](http://www.yiiframework.com/doc-2.0/images/rbac-hierarchy-2.png)

具体的实现，可以看看上面的`checkAccessRecursive`函数。

### 最后

写得比较仓促，读者还可以参考以下链接：
- [guide-security-authorization][2]
- [如何设计网站权限系统][3]

[1]: https://www.wikiwand.com/zh/%E4%BB%A5%E8%A7%92%E8%89%B2%E7%82%BA%E5%9F%BA%E7%A4%8E%E7%9A%84%E5%AD%98%E5%8F%96%E6%8E%A7%E5%88%B6
[2]: http://www.yiiframework.com/doc-2.0/guide-security-authorization.html
[3]: https://www.zhihu.com/question/20313385
