---
layout: post
title:  "Setting up a Spring projet with Java and Gradle"
date:   2014-05-07 13:20:20
categories: tutorial java spring gradle
---

I am starting work on a new project soon called NewsReel. My partner and I are trying to change the way people read and interact with the news, but I won’t go into too much detail about the project besides the technical stuff. We would like for this to be a mobile app, primarily, but as it will require some amount of computing power, I feel this will be best handled on a server. 
For this back-end I have chosen a Java environment and Spring based application. I chose Java for the obvious reasons of it being fast, reliable, and portable. I chose Spring because I wanted to learn something new and, from what I have read it is easy to use and set up. To make the spring development a bit easier I am using Gradle, a versatile Java build tool. This blog post is going to be about how to build a working web application using Gradle and Spring. By the end of this post I will be serving up a single route to [http://localhost:8080/][localhost].

To start off we will make sure that we have everything installed. For now all we will need is Java and gradle. On my Linux machine running Ubuntu 13.04 it is as simple as running 

`$ sudo add-apt-repository ppa:cwchien/gradle`  
`$ sudo apt-get update`  
`$ sudo apt-get install gradle`  

to install the latest version of gradle, according to [this thread on stack exchange][stack-exchange-link]. Java should already be installed. 

Now we can get started creating the project. To do this we need to create the root directory of our project and then can run a simple gradle command

`$ mkdir newsreel`  
`$ cd newsreel`  
`$ gradle init --type java-library`  

This series of commands will generate a full file system for your java project, a build.gradle file, a gradlew bash script, which will allow you to easily run your application and a gradle directory for storing all of your gradle dependencies. Now we can check that everything is fine by running

`$ gradle build`

![Gradle Build]({{ site.url }}/assets/images/gradle_build.png)  

and seeing that everything checks out. Now we are going to steal the `gradle.build` file from Spring’s [tutorial][spring-tutorial] for setting up a web application. 

Next we will create the java classes that will run our application. For now it will be two classes, a `src/main/java/newsreel/Application.java` class that will run the entire application, and a `src/main/java/newsreel/NewsReelController.java` class in which we will define our routes. 

The Application class should look like this

{% highlight java %}
package newsreel;

import java.util.Arrays;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@EnableAutoConfiguration
@ComponentScan
public class Application {

    public static void main(String[] args) {
        ApplicationContext ctx = SpringApplication.run(Application.class, args);

        System.out.println("Let's inspect the beans provided by Spring Boot:");

        String[] beanNames = ctx.getBeanDefinitionNames();
        Arrays.sort(beanNames);
        for (String beanName : beanNames) {
            System.out.println(beanName);
        }
    }

}
{% endhighlight %}

And the Controller should look like this

{% highlight java %}
package newsreel;

import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;

@RestController
public class NewsReelController {

    @RequestMapping("/")
    public String index() {
        return "Greetings from Spring Boot!";
    }

}
{% endhighlight %}

Finally, we can build and run our application with the command

`$ ./gradlew build && java -jar build/libs/gs-spring-boot-0.1.0.jar`  

And it should run without an error giving us the following output in the terminal

![Build successful]({{ site.url }}/assets/images/gradle_build_success.png)

And navigating to [localhost:8080][localhost] in the browser should show us the output of the “/” route “Greetings from Spring Boot!” in the browser. Now you have a running Spring application, created using Gradle.

[localhost]: http://localhost:8080/
[stack-exchange-link]: http://askubuntu.com/a/328181/93075
[spring-tutorial]: https://spring.io/guides/gs/spring-boot/#scratch

