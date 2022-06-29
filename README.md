# Quick Introduce Laravel 
Laravel is framework php for web app. In 2022, 1,547,319 websites that are Laravel Customers. We know of 730,966 live websites using Laravel and an additional 816,353 sites that used Laravel applicable. Laravel is not high performance framework but it's one of the most popular web frameworks in the world. Laravel has the advantage of creating fast, full-featured code, which is a great source of keywords for web developers.

#  Introduce DissectLaravel doc
There's tons of laravel documentation all over the place, it floods every web page and every discussion. However, I did not find any document that is deep and extensive enough, the knowledge system of laravel. As you know, from a technical perspective, when you read the doc and use it, you partially understand it. But you really get insight when you read the source code, understand how it's implemented, how it's designed. The source code will provide and reflect the software as it is, pure and straightforward. This is the most in-depth and intuitive approach to opensource. Of course it is not easy and takes a lot of resources. When you understand the source code, you will learn a lot from that opensource, you will debug problems very quickly without finding answers or unclear. You have a systematic thinking about the product, able to solve and fix very difficult bugs. This document provides a general analysis of the theory, the keyword laravel implementation, it is the most general, it is true for web technology in general, regardless of the language. I will try to analyze the laravel source code on the mind of web technology these days. I believe it is a useful reference for web developers, as well as devops who want an overview of the web app system.

# Who need doc
This document is for anyone passionate about web app, passionate about php, laravel for all level. If you don't like php, approach it by keyword, but if you love php, enjoy and relax with it. Hope it brings value and helps someone improves level, or simply solve your problem.

# License
All copyrights of the material belong to me. You can read, use, share for many people but no commercial rights or anything related business about it. I want to share it for free to the community

# Contacts
gmail: minhnghia.pham.it@gmail.com

# Quick Look (HowTo): Scenarios - Hands-on LAB
- [Overview and layout sourcecode Laravel](https://thisislink.com)


# Table of Contents
- [How To Understand Big Project](#HowToUnderstandBigProject)
- [Preview Laravel Layout](#LayoutLaravel)
    - [Php and index.php ](#PhpAndIndexPhp)
    - [index.php in laravel ](#IndexPhpLaravel)
    - [Public folder in laravel ](#PublicFolderIndexPhpLaravel)
    - [When save file in public folder ](#WhenSaveFileInPublicFolder)
    - [How to disect Laravel source](#HowToDisectLaravelSource)

- [List Modul](#ListModul)
- [Modul Session](#ModulSeSsion)
  - [Session](#Session)
  - [Define session](#DefineSession)
  - [Session in current web app](#SessionInCurrentWebApp)
  - [Session default in php is not good](#DefaultSSPhpNotGood)
  - [Session in laravel](#SessionInLaravel)
  - [Preview session in laravel](#PreviewSessionInLaravel)
  - [Dissect session in laravel](#DissectSessionInLaravel)
  - [Concat session](#ConcatSession)
  - [Detail session](#DetailSession)
  - [Csrf](#Csrf)
        


## How To Understand Big Project <a name="HowToUnderstandBigProject"></a>
A large project always has a lot of lines of code, with laravel 7.X being around 400 000 lines of code. So how do you approach it? My approach is top down thinking, approach from layout code ==> autoload ==> module ==> detail. One question is do you need to know the entire line of code of an opensource to understand it? The answer is no. Any module or source has main components and options. The main component represents the main feature of the project. You only need to understand the whole main component, most of the options are based on the main component. That's how I and this document approach the Laravel source. I know that's the same way most programmers choose to approach a very large project.
## Preview Laravel Layout <a name="LayoutLaravel"></a> 
## Php And Index.php <a name="PhpAndIndexPhp"></a>
In any programming language, there is usually a startpoint to start a project. Print c, go, c++, it's main() function, print php, it's index.php

## Index.php in Laravel <a name="IndexPhpLaravel"></a>
The vesion I use is laravel 7.5, this is source framework https://github.com/laravel/framework/tree/7.x.
This is source index.php https://github.com/laravel/laravel/blob/7.x/public/index.php

Inline 24, you see code:
"""require __DIR__.'/../vendor/autoload.php';"""

Print php, use compose control version. In line 24, laravel require autoload of project. Laravel will load all Class Loader, including Laravel core and third editor packet.


InLine 38:
"""$app = require_once __DIR__.'/../bootstrap/app.php';"""
Laravel mapping and bilding interface of laravel with application.


print line 52 to 60:

"""$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);

$response = $kernel->handle(
$request = Illuminate\Http\Request::capture()
);

$response->send();

$kernel->terminate($request, $response);"""

Laravel make one instance of Kernel Laravel, Laravel get incoming request from webserver, push to endpoint and response request. Laravel terminate index.php, and terminate the process handle request generated by the webserver


## Public folder in laravel <a name="IndexPhpLaravel"></a>
Why laravel has public folder? Why is index.php in there. Benefits and security of public folders. <br/>

Let's refer to a simple config of apache2 vs laravel:

"""<VirtualHost *:80>
ServerAdmin admin@test.com
ServerName test.com
DocumentRoot /home/anonymous/Desktop/Laravel/test/src/public

    <Directory/home/anonymous/Desktop/Laravel/test/src/public >
        Options -Indexes +FollowSymLinks +MultiViews
        AllowOverride All
        Require all granted
        <FilesMatch \.php$>
            # Change this "proxy:unix:/path/to/fpm.socket"
            # if using a Unix socket
            #SetHandler "proxy:fcgi://127.0.0.1:9000"
        </FilesMatch>
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/myapp.com-error.log

    # Possible values ​​include: debug, info, notice, warn, error, crit,
    # alert, emerg.
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/myapp.com-access.log combined

First, Laravel creates a public folder to contain the system's public files or resources, SymLinks. +FollowSymLinks Config folder public allows webserver FollowSymLinks, "+MultiViews" : A MultiViews search is where the server does an implicit filename pattern match,and choose from amongst the results , “AllowOverride” which allows you to override some Apache settings via a . htaccess file you can place in a director. This config allows the webserver to do a lot of things with the public folder, it allows the webserver to access the files in the public folder and return it to the browser. Therefore, only files that are public will be placed in the public folder.

## When save file in public folder <a name="WhenSaveFileInPublicFolder"></a>
With the public file used for all users, you should save it in the public folder: css, js, images... </br>
The files are specific to the request or the specific session, not stored in the public folder. Let's imagine with user1, request 1 you create 1.txt file, you save that file in public folder. File 1.txt is created at server handle request 1, called server 1. at Request 2, user1 get 1.xlsl, but loadbalance is not forward to server1, but is forwarded to server2. Clearly, server2 don't have the file 1.txt, confused. For this problem, use the same remote server as S3, don't use Laravel's public folder


## [How to disect Laravel source](#HowToDisectLaravelSource) 
Laravel source code has many modules, many interfaces and many design patterns. Before reviewing the source, the first thing you need to do is determine which interface x is used by and for which class it is binding, like with Facade. There are many ways to detect this, view file autoload, use ide_helper support loading endpoint,... <br/>

the way me used in this doc is using file : https://github.com/laravel/framework/blob/7.x/src/Illuminate/Foundation/Application.php#L1234. It gives you all information about alias mapping interface and binding class when starting laravel app. That's the bare minimum of information you need to dissect Laravel.

## [List Modul](#ListModul)
## [Modul Session](#ModulSeSsion)

## [Define session](#DefineSession)
![](img/img.png)

session = session_id + data mapping;  </br>

Simply put, a session is a memory area that is directly mapped to the user upon login. With any memory area, when using it, it is necessary to identify (session_id) and data (data mapping). sesson_id is unique.


## [Session in current web app](#SessionInCurrentWebApp)
In the modern world of web apps, especially microservices, it is not necessary to need a memory area that holds all the data when the user logs in. Data in microservices directly depends on services and events, tending to be independent and staless. However, when there is a need and need to use, or use any model like session, apply this simple rule: session = session_id + data mapping; </br>

Session in web apps these days can use a lot of drivers, files, caches, DBs, cookies, etc. This is flexible and suitable for today's needs.

##  [Session default in php is not good](#DefaultSSPhpNotGood)
Session default in php not good enough? Exactly, it's not good enough for most web apps these days. Php auto generate session Id and save in cookie, drive of this default is file. There is almost no way to interfere with this process. This is obviously not good enough, use Laravel's session, it's a trend as most php frameworks are no longer using php's default session. Other frameworks Django, Flask, Gin, ... have different approaches to sessions but the basic idea is still the same general formula in the Session definition.

## [Session in laravel](#SessionInLaravel)
## [Preview session in laravel](#PreviewSessionInLaravel)
Laravel Session is built by Laravel itself, completely independent of default php session. It fully supports all popular drives: cache (redis/memcache, file, DB, cookie, ...). In a modern web application that needs high performance, I recommend drive cache(redis/memcache).

## [Dissect session in laravel](#DissectSessionInLaravel)
## [Concat session](#ConcatSession)
Session has many drives, the main operations with memory are read and write, so it is easy to guess the main interface is read(), write(), getDefaultDriver() </br>

In line https://github.com/laravel/framework/blob/7.x/src/Illuminate/Support/Manager.php#L66 :
/**
* Get the default driver name.
*
* @return string
*/
abstract public function getDefaultDriver();

Session Manager extern Manager.php and it implements the function getDefaultDriver() to determine the driver configured in the system. </br>

Print file: https://github.com/laravel/framework/blob/7.x/src/Illuminate/Contracts/Session/Session.php. It saves all interfaces for session interaction, including push(), get().


### [Detail session](#DetailSession)
/**
* Register the session manager instance.
*
* @return void
*/
protected function registerSessionManager()
{
$this->app->singleton('session', function ($app) {
return new SessionManager($app);
});
}

From snippet: https://github.com/laravel/framework/blob/7.x/src/Illuminate/Session/SessionServiceProvider.php#L34-L39. Laravel registers a SessionManager() instance representing the laravel session.
in SessionMager, Laravel implements abstract public function getDefaultDriver() using https://github.com/laravel/framework/blob/7.x/src/Illuminate/Session/SessionManager.php#L240-L243. It implements the interface and returns the instance of the session drive configured in the system. </br>

How to laravel implement all driver and wrap it in code. After getting instance for session config in system, Laravel pass instance to :
https://github.com/laravel/framework/blob/7.x/src/Illuminate/Session/Store.php#L57-L62
public function __construct($name, SessionHandlerInterface $handler, $id = null)
{
$this->setId($id);
$this->name = $name;
$this->handler = $handler;
}
The SessionHandlerInterface represents one of the drives that Laravel has. The rest of the functions of the Store.php class are a wrapper for the SessionHandlerInterface. </br>

+) In drive: https://github.com/laravel/framework/blob/7.x/src/Illuminate/Session/CacheBasedSessionHandler.php, it implements the most basic functions like get(), set(), distroy (). Session in laravel with any drive is saved to a session key, this operation is quite simple. </br>

===> to summarize there are two class blocks, code block 1 gets the instance drive configured, Code block 2 implements the functions of each drive, this is the main job of the Laravel session.
