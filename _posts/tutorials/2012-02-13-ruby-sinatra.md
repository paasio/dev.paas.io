---
layout: post
title: Ruby - Sinatra
category: tutorials
---

In this tutorial, we will cover how to deploy a basic Hello World
Sinatra application to PaaS.io.

Please note that this tutorial has a few distinct differences from the
Rack tutorial. A Sinatra application doesn't need a `config.ru` file and
instead runs the main Sinatra application file directly.

## Prerequisites

* Basic Ruby knowledge, including Bundler
* Ruby already installed
* Basic knowledge of Git, or Mercurial if you wish to use Mercurial

## Create Your Application

{% highlight bash %}
$ mkdir myapp
$ cd myapp
{% endhighlight %}

#### Gemfile

In your Gemfile, add the following entries:

{% highlight ruby %}
source :rubygems
gem 'sinatra'
gem 'thin'
{% endhighlight %}

After creating the Gemfile, be sure to run `bundle install` to install
the gems and to generate the `Gemfile.lock` file.

It is necessary to specify `thin` in the Gemfile or else the application
will be started using WebBrick instead.

#### Main Application

Create a file called `hello.rb` containing the following:

{% highlight ruby %}
require 'rubygems'
require 'sinatra'

get '/' do
   "Hello from PaaS.io"
end
{% endhighlight %}

The Sinatra framework will directly run the first file it sees
contianing the line `require 'sinatra'`.  Because of this, you can't
have Bundler take care of the requiring.  If you want to use Bundler to
require all the files, then the Rack framework is recommended instead,
and it will load the application directly using the `config.ru` rackup
file.

## Adding to Source Control

If using Git, you can now create a repository:

{% highlight bash %}
$ git init
$ git add .
$ git commit -m "Initial Commit"
{% endhighlight %}

## Deploying Your Application

If you don't have it already, would recommend installing the PaaS.io
command line tool:

{% highlight bash %}
$ gem install paasio
{% endhighlight %}

To create your application, use `paasio create`

{% highlight bash %}
$ paasio create my-sinatra-app
Using application name "my-sinatra-app"...
Application Deployed URL [my-sinatra-app.paas.io]:
Detected a Sinatra Application, is this correct? [Yn]:
Memory Reservation (256M, 512M, 1G, 2G) [256M]:
Creating Application: OK
Git remote paasio added
{% endhighlight %}

Since it saw we were using a Git
repository, it automatically added a `paasio` remote to our repository:

{% highlight bash %}
$ git remote show paasio
* remote paasio
  Fetch URL: deploy@paas.io:my-sinatra-app.git
  Push  URL: deploy@paas.io:my-sinatra-app.git
  HEAD branch: (unknown)
{% endhighlight %}

The HEAD branch portion says "(unknown)" because we haven't yet pushed
anything to it.

Now we're ready to deploy it.

{% highlight bash %}
$ git push -u paasio master
Counting objects: 63, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (48/48), done.
Writing objects: 100% (63/63), 25.21 KiB, done.
Total 63 (delta 2), reused 0 (delta 0)
remote: Checking out code: OK
remote:
remote: Uploading Application:
remote:   Checking for available resources: OK
remote:   Packing application: OK
remote:   Uploading (4K): Push Status: OK
remote: Staging Application 'my-sinatra-app': OK
remote: Starting Application 'my-sinatra-app': OK
To deploy@paas.io:my-sinatra-app.git
 * [new branch]      master -> master
Branch master set up to track remote branch master from paasio.
{% endhighlight %}

Your application is now live! When deploying, it may sit at the "Staging
Application" stage for a little while at first.  It is working on
installing all of the necessary gems. On future deploys, it will be much
quicker since it will cache compiled gems for future use.

We specify the `-u` parameter in the push command because this sets up
tracking in Git. With that, when you run `git status`, it will show you
how many commits haven't been pushed to PaaS.io yet.

#### Verifying it is live

We can test it out with a simple `curl` command:

{% highlight bash %}
$ curl http://my-sinatra-app.paas.io
Hello from PaaS.io
{% endhighlight %}

