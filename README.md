# Remote Groovy Shell
Remote Groovy Shell is a light weight debugging/management tool embbeded in Java servlet web applications.

You can interact with you application with groovy language in a bash like shell, jush like using groovysh.

Through this Remote Groovy Shell, you can do a lot of fantastic things to you application when its running, with the power of groovy language.

## Motivations

- I want to inspect bean properties without attach & debug jvm
- I want to call specific method of a bean without add any fxxking jsps
- I want to mock some behavior conveniently 
- I want to modify specific property of a bean and see what happens without editing the code and restarting the application

## Features

- Execute groovy command or local script file at remote server 
- Lightweight, no additional dependencies(except groovy runtime), easy to embed into your project
- Bash like client, no manual installation required, just one command to get everything done
- Easy to extend to support various web application frameworks

## TODOs

- Shell command line completion

## User Guide

### Embbed rgsh in you project

1.Add following jars into you classpath

- [Remote Groovy Shell](http://search.maven.org/remotecontent?filepath=com/github/safrain/remote-gsh-server/0.1/remote-gsh-server-0.1.jar)
- [Groovy Runtime](http://groovy.codehaus.org/Download) Any version greater than 1.8.6 is OK 

For maven projects, add below content into you pom

	<dependency>
		<groupId>com.github.safrain</groupId>
		<artifactId>remote-gsh-server</artifactId>
		<version>0.1</version>
	</dependency>

	<dependency>
		<groupId>org.codehaus.groovy</groupId>
		<artifactId>groovy-all</artifactId>
		<version>1.8.9</version>
	</dependency>
            
2.Add RgshFilter configuration into your *web.xml*

**Attention:Exposing this filter may cause serious security problems, make sure you have ACL on this**

	<filter>
		<filter-name>Rgsh</filter-name>
		<filter-class>com.github.safrain.remotegsh.RgshFilter</filter-class>
	</filter>
	<filter-mapping>
		<filter-name>Rgsh</filter-name>
		<url-pattern>/admin/rgsh</url-pattern>
	</filter-mapping>

**Filter init params**

*charset* Request and Response character encoding, 'utf-8' as default.

*scriptExtensions* Script extension path, you can set multiple classpath here, separated by comma, 'com/github/safrain/remotegsh/server/extension/spring.groovy' as default.

*scriptExtensionCharset*  Charset of script extensions, 'utf-8' as default.


### Using shell client

Assume that you configured RgshFilter in you application at http://localhost/, '/admin/rgsh' as url pattern:

#### Show help screen

	curl -s http://localhost/admin/rgsh

Then you can follow the instructions shown on the screen.

#### Install

	curl -s http://localhost/admin/rgsh?r=install | bash

Two file will be downloaded to your current folder, an executable jar file and a bash script  *rgsh*.

Your server host and RgshFilter charset setting will be write into *rgsh* as its default settings.

#### Starting interactive shell

	./rgsh

Then feel free to use as a common groovy shell.

Use -s switch to specify server host url.

Use -c switch to specify request charset.

#### Upload script and run

You can upload a local script file and execute it on server.

	./rgsh foobar.groovy

#### Default Settings

Just Edit *rgsh* and modify DEFAULT\_SERVER, DEFAULT\_CHARSET variable

### Extending

#### Script extension
*Script Extensions* will be executed before script you uploaded(or before your first shell command),
by default the script is *com/github/safrain/remotegsh/server/extension/spring.groovy*, adding support to Spring framework
and you can customize it using filter init parameter *scriptExtensions*
In the extension script, you can put some variables or function into the script context to add support to various frameworks
Or you can simply put some of your favorite utilities in it.

**Built-in variables**

*_request* Just the request

*_response* Just the response

*_charset* Request and response charset, same as *RgshFilter.charset*

**Spring support**(com/github/safrain/remotegsh/server/extension/spring.groovy)

*_context* Just the spring ApplicationContext in your ServletContext

*beans* Shortcut to access your beans, use 'beans.beanName' to access your bean

## Example

You can find an example project in *remote-gsh-example/* directory, the example project is a very very simple PetStore project, you can build it and run the war file
in any type of servlet container, or you can just run *mvn run:jetty* to start the built-in jetty
server and find whats going on, One thing to say is that the project contains several problems,
which made the application unavailable, but with the power of Remote Groovy Shell and Groovy language,
you can even fix those problems without restarting your application

*examples.groovy* will show you what you can do with you application using Remote Groovy Shell, it is very simple, just read it!

Use command below to run this script on the server(Assume that you are using the buily-in Jetty)

	curl -X POST "http://localhost/admin/rgsh" -T example.groovy

Of course you can use the *rgsh* client :D

	./rgsh example.groovy


