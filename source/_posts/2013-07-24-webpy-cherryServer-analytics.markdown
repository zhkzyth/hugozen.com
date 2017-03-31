---
layout: post
title: "webpy-cherryServer-analytics"
date: 2013-07-24 08:59
comments: true
tags:
- webpy
- python
- tips
- tech
categories: tech
---

Introdcution
------------
So you want to how [web.py][0] works?

In this article,i will walk through the web.py's simple server,which is adopted from [CherryPy][1].

Things you need to know
--------------
- read some stuff about the technical terms
    - [cgi][2]
    - [wsgi][3](and its implementation,[uswgi][4])
    - [http specification][5]

<!--more-->
what is a web server?
--------------------

In my words, a web server is simply a request/reponse manager.
It listens comming requests from the client,and dispatches requests to the appropritate application based on the rules.
Then it gets responses back from the same application,and send back to the client later.

There are a couple of things we need to go deeper.

- What is a request ?

When a user click a link or post a form,a request has been created.How?

Well,the client will try to make a connection to the server based on the TCP/IP protocal.(sorry,i won't go down HTTP level at this article.)

When the connection has been established,it means that we can talk to the server.What?you can think that we get a bridge to the server.So we can send things to the server or  get things from it.

We divide these requests into different groups based on their types,like POST,GET,HEAD and so on.It may be more clear when we talk about it in a different angle.Let's see what makes a GET request.

Suppose we use wget to make a GET request.

`wget http://douban.com`

And here is the request data:

```
GET / HTTP/1.1\r\n
User-Agent: Wget/1.4 (darwin12.3.0)\r\n
Accept: */*\r\n
Host: douban.com\r\n
Connection: Keep-Alive\r\n
\r\n
```

- How does a request come to the server?

The request would be packaged in a packet via the internet protocal,being encoded into bits,then transformed into optical signal ,finally sended to the server with the help of optical cable.

When the request packet arrives at the server,the packet would be unpackaged back to the request data.

Have a look at [OSI_model][7].

- what are the rules?

The rules are defined by us.You can simply judge that if the request data contain 'Host: douban.com',then the server should dispatch the request to the A application.

- what is the response?

    1. when the A application gets the request,it would change it to a dict (in python words).And the application would do some stuff on it based on the request type,Host info and so on.

    2. When all these have been done,it would give a dict back.And we call this dict 'a response'.

    3. The server gets the response,and gives it back to the client.

- How does the server and application communicate with each other?

It may be strange that we ask such a question.Why?

Just imagine that you are the framework maker,and you write a func or a class to get a request or push back a response to the server,like Nginx.
And some other framework maker also write some func(which may be so different from yours) to do the same things.

Things are going to be a mess!!We need a specification to guide people to write such code.And now we have some stuff like cgi,wsgi.

How does these things happen in webpy?
--------------------------------------

- CherryPyserver in general
In fact, webpy does not implement its own server.It just adopts another framework's server,the crazy `CherryPyWSGIServery`.

From the image below, you can see that there are serveral classes which workes together to make a server.
![CherryPyWSGIServer UML][6]

From the above chat we can tell that the CherryPyWSGIServer is inherited from HTTPServer.

And it uses the HTTPConnection class to represent comming requests,
and handles them by the ThreadPool class.

The WSGIGateWay1.0 is used to get the request enviroment information,and correctly responses to the server according to the wsgi specification.

- In a little detail

In fact, when we develop a webpy app,we would write some code like these:

```
import web, cgi, settings
import storage, search, db
import simplejson

urls = (
    '^/$', 'do_index',
)

app = web.application(urls, globals())

render = web.template.render(settings.TEMPLATE_FOLDER, base='base')

class do_index:
    def GET(self):
		files = db.slave.select('ms_files', order='date desc', limit=5)
		return render.index(files)

if __name__ == "__main__":
    app.run()
```

When `app.run()` is excuted,a web server has been setted up,which is our `CherryPyWSGIServer` instance in default.

And the wsgi specification has defined rules about the talk convention between the server and the application.In my words,it would be like these:
```
Server: hey,App.When we talk to each other,you should give me a func.

App: ok,Server.But what is a func?

Server: well,the func should accpet a enviroment dict and a callback func.
        It is supposed to be like `func(env,callback)`.
        And what? The func result should be a yield like object.
        And the callback should be called like `callback(status,headers)`.

App: Ok,you mean that i can get all the request info from the env dict?

Server: Not all the request info.The wsgi has told me
        that i should put key-value pair like the path_info
        ,request_address,post_data etc in it.
        So you can always expect that the same things without worries.

App: Hmm....So,after i have finished the request process,i should call the callback?

Server: Yes!But don't forget to return a yield like object.

App: well,it's easy.
```

ref links
----
- [fastcgi](http://www.fastcgi.com/drupal/)
- [uswgi](http://projects.unbit.it/uwsgi/)
- [request](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol).A request means more thing than what i have talked.

[0]: http://webpy.org
[1]: http://www.cherrypy.org/
[2]: http://www.ietf.org/rfc/rfc3875
[3]: http://www.python.org/dev/peps/pep-0333/
[4]: http://uwsgi-docs.readthedocs.org/en/latest/
[5]: http://tools.ietf.org/html/rfc2616
[6]: /images/webpy/webpy-server-uml.png "CherryPyWSGIServer UML"
[7]: http://en.wikipedia.org/wiki/OSI_model
