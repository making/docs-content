+++
title = "Your first application — in Ruby"
description = "Your first Ruby application on Giant Swarm, using your own Docker container and connecting multiple components."
date = "2015-03-18"
type = "page"
weight = 55
categories = ["basic"]
+++

# Your first application — in Ruby

This tutorial guides you through the creation of an application using two interlinked components and a custom Docker image. The core is a Ruby web application. Additionally, a Redis server is used as a temporary data store and we connect to an external API.

## Prerequisites

* You should have done the [Quick Start](/) or the [Annotated Hello World Example](/guides/annotated-helloworld/) and everything should have worked fine.

* We assume that you have a basic understanding of Docker and you have Docker installed. Docker provides extensive [installation instructions](https://docs.docker.com/installation/) and [user guides](https://docs.docker.com/userguide/).


## For the impatient

The sources code for this tutorial can be found [on GitHub](https://github.com/giantswarm/giantswarm-firstapp-ruby). To facilitate following this guide, we recommend you clone the repository using this command:

```nohighlight
$ git clone https://github.com/giantswarm/giantswarm-firstapp-ruby.git
$ cd giantswarm-firstapp-ruby
```

If you're not the type who likes to read a lot, we have a [Makefile](https://github.com/giantswarm/giantswarm-firstapp-ruby/blob/master/Makefile) in the repository. This file helps you to get everything described below going using these commands:

```nohighlight
$ swarm login <yourusername>
$ make docker-build
$ make docker-run-redis
$ make docker-run
$ make docker-push
$ make swarm-up
```

Everybody else, follow the path to wisdom and read on.

## Testing Docker and getting some base images

We have a Docker task ahead of us that could be a little time-consuming. The good thing is that we can make things a lot faster with some preparation. As a side effect, you can make sure that `docker` is working as expected on your system.

We need to pull two images from the public Docker library, namely `redis` and `ruby:2.2-onbuild`. Together they can take a few hundred MB of data transfer. Start the prefetching using this command:

```nohighlight
$ docker pull redis && docker pull ruby:2.2-onbuild
```

__For Linux users__: You probably have to call the `docker` binary with root privileges, so please use `sudo docker` whenever the docker command is required here. For example, initiate the prefetching like this:

```nohighlight
$ sudo docker pull redis && sudo docker pull ruby:2.2-onbuild
```

We won't repeat the `sudo` note for the sake of readability of the rest of this tutorial. Docker warns you if the privileges aren't okay, so you'll be reminded anyway.

While your terminal and network connection are kept busy with loading Docker images, let's have a look on what exactly we are going to build.

## Overview of our application

This diagram depicts how our application components will be set up.

![Application schema diagram](/img/your-first-application-schema-ruby.png)

We have one component which we call `ruby` as the core piece. It will provide a little HTTP server. When accessed by a user, it should display the current weather at our home town, Cologne/Germany.

We get the weather data from the [openweathermap.org](http://openweathermap.org/) API. Since we want to be good citizens and not call that API more often than necessary, we cache the API responses locally for a while. For this we use a Redis cache, which is our second component, called `redis` in the diagram above.

## The Ruby server component

Our application server consists of only one little file: A Ruby file called [current_weather_service.rb](https://github.com/giantswarm/giantswarm-firstapp-ruby/blob/master/current_weather_service.rb) which contains all our application logic. If you're interested in the internal workings of the server, check it's content. For our tutorial, it's not too important, so we cut the details here.

## Building our Docker image

We now create a Docker image for our Ruby server. Here is the `Dockerfile` we use for that purpose:

```Dockerfile
FROM ruby:2.2-onbuild

EXPOSE 4567

ENTRYPOINT ["bundle", "exec", "ruby", "current_weather_service.rb"]
```

As you can see, we use a [ruby base image](https://registry.hub.docker.com/_/ruby/) as a basis. We don't have to add or install anything. The Docker base image takes care that our dependencies and script are bundled into our final image.

The prefetching of Docker images you started a couple of minutes ago should be finished by now. If not, please wait until it's done.

Assuming that your Giant Swarm username is `yourusername`, to build the image for your Docker container, you then execute:

```nohighlight
$ docker build -t registry.giantswarm.io/yourusername/currentweather ./
```

## Testing locally

To test locally before deploying to Giant Swarm, we also need a Redis server. This is very simple to set up, since we can use a standard image here without any modification. Simply run this to start your local Redis server container:

```nohighlight
$ docker run --name=redis -d redis
```

Now let's start the server container for which we just created the Docker image. Here is the command (replace `yourusername` with your username):

```nohighlight
$ docker run --link redis:redis -p 4567:4567 -ti --rm registry.giantswarm.io/yourusername/currentweather
```

It should be running. But we need proof! Let's issue an HTTP request.

Accessing the server in a browser requires knowledge of the IP address your docker host binds to containers. This depends on the operating system.

__Mac/Windows__: with `boot2docker` you can find it out using `boot2docker ip`. The default here is `192.168.59.103`.

__Linux__: the command `ip addr show docker0|grep inet` should print out a line containing the correct address. The default in this case is `172.17.42.1`.

So one of the following two commands will likely work:

```nohighlight
$ curl 192.168.59.103:4567
$ curl 172.17.42.1:4567
```

Your output should look something like this:

```nohighlight
Current weather in Cologne: moderate rain, temperature 10 degrees, wind 42 km/h
```

Try at least two requests within 60 seconds to verify your cache is working.

In the server console you will see an output like this:


```nohighlight
[2015-02-10 07:13:04] INFO  WEBrick 1.3.1
[2015-02-10 07:13:04] INFO  ruby 2.2.0 (2014-12-25) [x86_64-linux]
== Sinatra/1.4.5 has taken the stage on 4567 for development with backup from WEBrick
[2015-02-10 07:13:04] INFO  WEBrick::HTTPServer#start: pid=1 port=4567
#<Redis client v3.2.0 for redis://172.17.0.205:6379/0>
I, [2015-02-10T07:13:09.591611 #1]  INFO -- : Requesting live weather data for Cologne...
192.168.59.3 - - [10/Feb/2015:07:13:09 +0000] "GET / HTTP/1.1" 200 76 0.1702
192.168.59.3 - - [10/Feb/2015:07:13:09 UTC] "GET / HTTP/1.1" 200 76
- -> /
#<Redis client v3.2.0 for redis://172.17.0.205:6379/0>
I, [2015-02-10T07:13:16.924905 #1]  INFO -- : Using cached weather data for Cologne...
192.168.59.3 - - [10/Feb/2015:07:13:16 +0000] "GET / HTTP/1.1" 200 76 0.0020
192.168.59.3 - - [10/Feb/2015:07:13:16 UTC] "GET / HTTP/1.1" 200 76
- -> /

```

Awesome. Now let's deploy it to the cloud.

## Bringing it to Giant Swarm

### Uploading to the registry

To use this Docker image on Giant Swarm it has to be uploaded to a Docker registry. Giant Swarm offers you a such a registry. Here we briefly explain how to use it. For more information on using the Giant Swarm registry, see our [registry reference](/reference/registry/).

Before pushing to the registry, you have to log in with the `docker` client. Use this command:

```nohighlight
$ docker login https://registry.giantswarm.io
```

You will be prompted for username, password and email. Use your Giant Swarm account credentials here.

Still assuming that your username is `yourusername`, you can now push the image like this:

```nohighlight
$ docker push registry.giantswarm.io/yourusername/currentweather
```

### Configuring your application

Applications in Giant Swarm are [configured using a JSON configuration file](/reference/swarm-json/) that is usually called `swarm.json`. For this application, we create a configuration containing one service with two components.

Pay close attention to how we create a link between our two components by defining a dependency in the `ruby-web-component` component pointing to the `redis` component.

```json
{
  "app_name": "currentweather-app",
  "services": [
    {
      "service_name": "currentweather-service",
      "components": [
        {
          "component_name": "ruby-web-component",
          "image": "registry.giantswarm.io/$username/currentweather:1.0",
          "ports": ["4567/tcp"],
          "dependencies": [
            {
              "name": "redis",
              "port": 6379
            }
          ],
          "domains": {
            "currentweather-$username.gigantic.io": "4567"
          }
        },
        {
          "component_name": "redis",
          "image": "redis",
          "ports": ["6379/tcp"]
        }
      ]
    }
  ]
}
```

### Starting the application

With the above configuration saved as `swarm.json` in your current directory you can now create and start the application using the `swarm up` command below. As always, replace `yourusername` with your actual username. The flag `--var=username=yourusername` will take care of placing your username in the positions where the `$username` variable is used in `swarm.json`.

```nohighlight
$ swarm up --var=username=yourusername
```

You will see some progress output during creation and startup of your application:

```nohighlight
Creating 'currentweather-app' in the 'yourusername/dev' environment...
Application created successfully!
Starting application currentweather-app...
Application currentweather-app is up.
You can see all services and components using this command:

    swarm status currentweather-app

```

Seeing is believing, they say. So let's do the final test that your application is actually doing what it should. Open the URL (with your username in the right place) in the browser:

[http://currentweather-yourusername.gigantic.io/](http://currentweather-yourusername.gigantic.io/)

If you watched closely, after starting our app we got the recommendation to check it's status using

```nohighlight
$ swarm status currentweather-app
```

So here is what we get when doing so (your output will vary slightly):

```nohighlight
App currentweather-app is up

service                 component           instanceid    created              status
currentweather-service  ruby-web-component  TdfsdckAwoS3  2015-02-10 07:20:03  up
currentweather-service  redis               VrvpFsjGOLyq  2015-02-10 07:20:02  up
```

Here you have them, your two components, running on Giant Swarm. If you want to, you can check the logs using the instance IDs you see in the `swarm status` output. The syntax for the command is `swarm logs <instance-id>`.

Now if you like, you can stop or even delete the application again.

```nohighlight
$ swarm stop currentweather-app
$ swarm delete currentweather-app
```

We hope you enjoyed this tutorial. If yes, feel free to tweet and blog it out to the world. If not, please let us know what bugged you (see chat and support info at the bottom of this page).

If you're still hungry, why not continue with a more advanced tutorial?

## Further reading

* [Your first application - in your language](../): this guide in other languages
* [Swarmify Ruby on Rails](/guides/ruby_on_rails/) - a simple Rails example involving MySQL
* [Getting started with Docker and MeanJS](http://blog.giantswarm.io/getting-started-with-docker-and-meanjs) - Easily adaptable to Giant Swarm with the experience you have by now.
