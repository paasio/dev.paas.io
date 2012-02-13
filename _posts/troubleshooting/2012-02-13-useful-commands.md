---
layout: post
title: Useful Commands
category: troubleshooting
---

When trying to troubleshoot an application issue, there are a number of
useful commands you can use to get insight into the problem.

Almost all of these are using the `paasio` command line tool. To install
and configure the command line tool, do the following:

{% highlight console %}
$ gem install paasio
$ paasio login
Attempting login to [https://api.paas.io]
Email: person@example.com
Password: ************
Successfully logged into [https://api.paas.io]
{% endhighlight %}

## Application Health


{% highlight console %}
$ paasio apps

+-----------------+----+---------+----------------------------------------+-------------+
| Application     | #  | Health  | URLS                                   | Services    |
+-----------------+----+---------+----------------------------------------+-------------+
| my-rails31-app  | 1  | 0%      | my-rails31-app.paas.io                 |             |
+-----------------+----+---------+----------------------------------------+-------------+
{% endhighlight %}

From here we can see the application is configured for 1 instance,
however its health (number of running instances to number of configured
instances) is 0%.

We can use some of the other commands to look into why the instances
failed to start.

## Application Logs

The best way to navigate the log files for an application is through the
`paasio files` command.

To browse the base directory do the following:

{% highlight console %}
$ paasio files my-rails31-app
app/                                         -
logs/                                        -
{% endhighlight %}

All application will have an `app` and a `logs` directory.  The `app`
directory can be used to browse the application code that is being
loaded, while the `logs` directory contains various log files from
different peices.

Specify the path on the end of the `paasio files` command to dig deeper.

{% highlight console %}
$ paasio files my-rails31-app logs
staging.log                               832B
migrations.log                            3.7K
stderr.log                               17.2K
stdout.log                               21.5K
{% endhighlight %}

Here we can see that we have 4 logs available.

To view an individual log file, simply add it on to the command:

{% highlight console %}
$ paasio files my-rails31-app logs/staging.log
{% endhighlight %}

This will output the entire contents of the file.

Some of the common log files include:

<table class="table">
  <tbody>
    <tr>
      <td><code>staging.log</code></td>
      <td>This is the output of the staging phase which is run
  when an application is deployed and packaged (the `Staging
Application` line in the deploy output). Issues with installing
dependencies will be captured in this log file.</td>
    </tr>
    <tr>
      <td><code>migrations.log</code></td>
      <td>On startup, Rails applications will run any pending
  migrations and capture the output to this file. If the application
failed to start up due to a database configuration issue, it will be
shown here. Double check the correct database adapter is installed and
you have attached the database to the application.</td>
    </tr>
    <tr>
      <td><code>stderr.log</code></td>
      <td>Anything written to `STDERR` within the application will
  be stored here.</td>
    </tr>
    <tr>
      <td><code>stdout.log</code></td>
      <td>Anything written to `STDOUT` within the application will
  be stored here. If your application is behaving incorrectly, you might
try and instrument some logging and watch the output in this file.</td>
    </tr>
  </tbody>
</table>
