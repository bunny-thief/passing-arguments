---
title: 'Spring Devtools: Automatic Restart & LiveReload'
slug: spring-devtools
date: 2024-02-19T09:00:00+01:00
draft: false
categories: ['Tools']
tags: [Spring Boot, IntelliJ]
ShowCodeCopyButtons: true
Summary: Speed up Spring Boot application development with Automatic Restart and LiveReload from developer tools.

cover:
  image: 'img/004-spring-devtools/barn-images-t5YUoHW6zRo-unsplash.jpg' # https://unsplash.com/photos/assorted-handheld-tools-in-tool-rack-t5YUoHW6zRo
  alt: 'SDKMAN blue groovy logo' # alt text
  caption: 'SDKMAN blue groovy logo'
---

## Overview

Developing Spring Boot web applications requires constant testing to ensure a feature that is currently being implemented functions as expected. This guide will cover two components of Spring Boot's [developer tools](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using.devtools 'Developer Tools') that can make this job easier.

The first one is [_Automatic Restart_](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using.devtools.restart 'Automatic Restart'), which senses when a change is made to files in your Spring Boot project and automatically restarts the application in IntelliJ so your changes are reflected in the running app. Automatic restart works great with REST API applications that don't have visual elements.

For applications that serve static content, such as Thymeleaf templates, an additional tool that enables [_LiveReload_](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using.devtools.livereload 'Live Reload') is required. _LiveReload_ works together with _Automatic Restart_ and refreshes an embedded server that triggers browsers to update and reflect any updates.

## Requirements

The following are required to follow along with this guide:

1. A Spring Boot application that uses Maven.
2. The application needs to expose a REST API endpoint. It also needs Spring MVC and a simple view with Thymeleaf. If you don't have a Spring Application handy, you can follow along with a Spring MVC project that I made called [devtools-demo](https://github.com/bunny-thief/devtools-demo 'devtools-demo Github Repo').
3. [IntelliJ Idea Community Edition](https://www.jetbrains.com/idea/download/).
4. You also need to have Firefox. We will be installing the [LiveReload Extension](https://addons.mozilla.org/en-US/firefox/addon/livereload-web-extension/ 'LiveReload - Web extension by Todd Wolfson').

This guide will be broken down into three main sections. Getting Developer Tools into a Spring Boot project, enabling _Automatic Restart_ and installing the _LiveReload_ browser extension for Firefox.

## Developer Tools

To add developer tools to an existing Spring Boot project, we need to add the following devtools dependency to the Maven POM file.

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-devtools</artifactId>
</dependency>
```

Maker sure to reload the POM file after adding the **devtools** dependency by pressing the Maven reload button.

![⚠️Maven Reload Button!](/img/001-java-jar-file/04-reload-button.png 'Maven Reload Button')

Now that we have developer tools in our Spring Boot project, we can enable Automatic Restart. Let's do that in the next section.

## Enable Automatic Restart

In order to enable Automatic Restart we need to adjust two settings in IntelliJ. First, we need to tell _IntelliJ_ to build the project automatically. The next setting tells IntelliJ that we want to "Allow auto-make to start even if developed application is currently running."

The first setting is located in the **Compiler** settings window. We can access it here by going through the _Settings_ sub-menu: `File > Settings > Build, Execution, Deployment > Compiler` Once there, we need to make sure "Build project automatically" is checked off.

![⚠️Enable Automatic Restart](/img/004-spring-devtools/enable-automatic-restart.gif 'Enable Automatic Restart')

Next, we have to tell IntelliJ to **auto-make** the project even if it is currently running. That setting is located in the **Advanced Settings** window of the _Settings_ menu which is located here: `File > Settings > Advanced Settings`.

![⚠️Enable Automatic Restart](/img/004-spring-devtools/allow-auto-make.gif 'Enable Automatic Restart')

Once Automatic Restart has been anabled, IntelliJ will restart your application automatically and reflect changes you introduce. It takes about one second for IntelliJ to act. You can tell it's working if you see the output from the _Run_ windown being cleared and reloaded.

### Test Automatic Restart

Let's start the application inside of IntelliJ and enter the following address into our browser.

```
http://localhost:8080/api/date
```

The browser should load up the current date in the following format "**YYYY-MM-DD**".

We can test that Automatic Restart is working by editing the _DateService_ class in the _services_ package. Currently, the `/api/date` endpoint returns the current **local-date** from the _toLocalDate()_ static method in the _LocalDateTime_ package.

{{< 004-spring-devtools/timeservice>}}

Let's change the return value so it returns the current **date-time** instead. Just remove the _.toLocalDate()_ so it matches the following snippet.

```
return LocalDateTime.now().toString();
```

If everything goes well, you should see the application restart in the **Run** window. Once you see that you can try hitting the api endpoint to get the date-time. You should now see a string representation of LocalDateTime which has the following format **YYYY-MM-DDThh:mm:ss**. In my case, after making the changes, I see "2024-02-19T09:00:01.985596660" when I hit the endpoint.

> ### Enable Automatic Restart for All New Projects
>
> We've just enabled Automatic Restart for this project. This setting applies only to the current IntelliJ project in which are currently in. To enable Automatic Restart for new projects we need to edit the place a checkmark in the _Build project automatically_ inside of the **New Projects Setup** setting which is found here:
>
> `File > New Projects Setup > Settings for New Projects... > Build, Execution, Deployment > Compiler`

Keep in maind that _Automatic Restart_ only restarts the application in IntelliJ. If you are working with an application that has a front end and are using a browser to preview it, you need to install a browser extension to refresh the browser when a change is made to your code.

## Install LiveReload Extension

This step is quite easy and doesn't require any coding or additional Spring Boot configuration. We just have to add a broser extension to Firefox. Just visit the following link and click on the "Add to Firefox" button.u

[LiveReload Firefox Extension](https://addons.mozilla.org/en-US/firefox/addon/livereload-web-extension/ 'LiveReload - Web extension by Todd Wolfson')

![⚠️LiveReload Extension](/img/004-spring-devtools/live-reload-extension-page.png 'LiveReload Extension')

After the extesion is installed, just make sure it is enabled. You can manage Firefox extensions by pasting th following into your the address bar.

```
about:addons
```

![⚠️Current Date](/img/004-spring-devtools/enable-livereload.png 'Current Date')

### Test LiveReload

Let's test the LiveReload extension to make sure changes in our code are reflected in the browser. First, make sure the application is running in IntelliJ. Then launch Firefox and open localhost with the default port.

```
http://localhost:8080/
```

We should see the following text: "The current date is:" being displayed along with the current date.

![⚠️Add LiveReload](/img/004-spring-devtools/current-date.png 'Add LiveReload')

We can edit the **main.html** file in the _resources/templates_ directory and watch the changes in the browser window.

{{< 004-spring-devtools/date-template>}}

Let's remove the text **current** from "The current date is:". The browser should refresh automatically.

## Troubleshooting

Ensure the following steps have been completed if the application doesn't restart automatically after making changes:

1.  Make sure the [_devtools_ dependency was added](#developer-tools) and the POM file was reloaded.
2.  Edit the IntelliJ settings to [enable Automatic Restart](#enable-automatic-restart).
3.  [Install the LiveReload](#install-livereload-extension) Firefox extension.

## Main Takeaway

With Automatic Restart and LiveReload we don't have to manually restart a Spring Boot application during development. These feautures are included in the Spring Boot developer tools and will restart an application that is running in IntelliJ when we make changes to code in our project.
