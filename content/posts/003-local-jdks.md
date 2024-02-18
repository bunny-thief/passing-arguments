---
title: 'Manage Local JDKs with SDKMAN'
slug: local-jdks
date: 2024-02-12T09:00:01+01:00
draft: false
categories: ['Tools']
tags: [Java, JDK, SDKMAN, APT]
ShowCodeCopyButtons: true
summary: Make JDKs that have not been installed with _SDKMAN_, visible to it so they can be managed with _SDKMAN_.

cover:
  image: 'img/local-jdks/shunya-koide-1emWndlDHs0-unsplash.jpg'
  # https://unsplash.com/photos/brown-wooden-book-shelves-in-library-1emWndlDHs0
  alt: 'Book Shelves in Library' # alt text
  caption: 'Book Shelves in Library'
---

## Overview

In the [previous post]({{< ref "002-sdkman-ftw" >}} ) we installed SDKMAN and learned how we can use it to help us manage SDKs. This post will go over how we can make JDKs that have not been installed with _SDKMAN_, visible to it so they can be managed with _SDKMAN_. We will also uninstall a JDK, using the APT package manager once we no longer need or want it on our system.

## Requirements

This guide was written and tested on _Ubuntu 20.04 LTS_. The _SDKMAN_ commands should however work across all platforms. You also need to have a JDK installed; one which was **not installed using _SDKMAN_**. Don't worry if don't have one installed though as it will also cover how to install the latest _OpenJDK_ LTS version using Ubuntu's package manager. If you already have a JDK installed, then you can skip over to the [next section](#make-jdk-visible-in-sdkman).

## Install JDK

[OpenJDK 17](https://openjdk.org/projects/jdk/17/ 'OpenJDK 17') is the latest LTS version that is supported by _Ubuntu 20.04 LTS_ out-of-box. That means that we can use _APT_, [Ubuntu's package manager](https://ubuntu.com/server/docs/package-management 'Ubuntu Package Management'), to download, install and also add the JDK to to our system's **PATH** for us.

Let's first make sure _Ubuntu_ is up-to-date by first copying, pasting and running `update` and then the `upgrade` commands.

```
sudo apt update && apt upgrade
```

Now that everything is up-to-date, let's install _OpenJDK 17_ with the following command.

```
sudo apt install openjdk-17-jdk
```

It's a good practice to check whether a newly installed JDK works by checking the Java version at the command line. If however, you have _SDKMAN_ installed **and** you have used it to install a JDK, then checking the _Java_ version will display whatever current JDK, _SDKMAN_ points to.

```
java -version
```

In my case running the `java -version` command points to _Temurin_ version 21.0.2. What you see on your screen will depend on what JDK is installed and set as the default in _SDKMAN_.

{{< local-jdks/temurin-version >}}

Let's also run the equivalent command to check the version of the default _Java_ candidate in _SDKMAN_.

```
sdk current java
```

We should see that it also points to _Temurin_ 21.0.2.

{{< local-jdks/sdkman-current-temurin >}}

So we have installed a JDK without using _SDKMAN_ but we can't access it because _SDKMAN_ doesn't know it even exists. Let's now move on to the next section to find how we make _SDKMAN_ point to _OpenJDK 17_.

## Manage OpenJDK 17 with SDKMAN

In this section we are first going to tell SDKMAN where our newly installed JDK is located. Once that is done we'll see how we can use _OpenJDK 17_ in _SDKMAN_ in the **current shell** and then cover how it can be made the default version **system wide**.

### sdk install

Here's the command need to run to make _OpenJDK 17_ visible to SDKMAN.

`sdk install <candidate> <version name> <path to jdk>`

We have to use **java** for the candidate. The version name can be anything we'd like. I'll use **openjdk-17-jdk** to keep things consistent with the package name that was [installed](#install-jdk) in _Ubuntu_. The path is where the JDK is located on my computer; `/usr/lib/jvm/java-17-openjdk-amd64/`. If you used the `apt install` command to install your JDK, then it should be in the same location. If you installed another another JDK or if you installed it manually, then you need to provide the path where it is located.

```
sdk install java openjdk-17-jdk /usr/lib/jvm/java-17-openjdk-amd64/
```

If everything went well, you should see the following message printed on the screen.

{{< local-jdks/install-openjdk17 >}}

We can also check that OpenJDK 17 now appears in the list of _Java_ candidates.

```
sdk list java
```

We have to scroll all the way down to the bottom of the list and see **openjdk-17-jdk** under the "Identifier" column.

{{< local-jdks/sdkman-list-java  >}}

Although this example used _OpenJDK 17_, keep in mind that we can use this method to make _SDKMAN_ aware of any JDK. All we need is to know where it is located.

### sdk use

With _SDKMAN_ now aware of the location of our JDK, we can run the `use` command to enable _openjdk-17-jdk_ in the **current shell**.

```
sdk use java openjdk-17-jdk
```

We should see the following confirmation on the screen.

{{< local-jdks/use-confirmation>}}

We can now check the Java version to confirm OpenJDK 17 is available on our shell.

```
java -version
```

Hopefully we see _OpenJDK_ version "17.0.9" as the current Java candidate in the current shell.

{{< local-jdks/openjdk-version >}}

That's great, SDKMAN is now pointing to _openjdk-17-jdk_! We can now manage other JDKs which are not installed with SDKMAN. Remember that by running the `use` command we can only access _openjdk-17-jdk_ within the terminal shell in which we ran that command. If we open another shell, then the default Java candidate will be active.

Next we'll go over how we can make _SDKMAN_ use _openjdk-17-jdk_ across our entire system including any new shells we may open up and any other program which points to Java using the **JAVA_HOME** environment variable.

### sdk default

Let's start out by confirming which JDK _SDKMAN_ currently uses system wide.

```
sdk current java
```

I'm seeing Temurin 21.0.2 but you will see whichever JDK is set as the default in _SDKMAN_ for your system.

{{< local-jdks/sdkman-current-temurin >}}

In order to set _OpenJDK 17_ as the default version we need to run the following command.

```
sdk default java openjdk-17-jdk
```

SDKMAN's confirmation makes it very clear that that OpenJDK 17 should now be set for all shells.

{{< local-jdks/set-jdk >}}

If we run `java -version` now, we should see that _OpenJDK 17_ is the current Java candidate, even if we open up a brand new shell.

In the last section of this guide we are going to uninstall OpenJDK 17 using _APT_.

## Uninstall JDK

Before we uninstall _OpenJDK 17_, we should switch the default Java candidate in SDKMAN to a different JDK. This is just a precaution as I'm not sure how SDK handles deleting the current JDK and I don't want to cause any problems with my system.

```
sdk default java 21.0.2-tem
```

Let's use the `dpkg` to list all packages which are currently installed in our system. We can also use `grep` to highlight only packages which contain **jdk** in the package name.

```
dpkg --list | grep jdk
```

> The "Version" and "Architecture" columns have been removed from the output of the `dpkg --list` command so the content fits on one line.

{{< local-jdks/dpkg-list >}}

We have to make sure we remove all four packages.

```
sudo apt purge --auto-remove openjdk-17*
```

Type `y` and press `Enter` to uninstall the packages and remove the files.

{{< local-jdks/delete-jdk >}}

If we list all packages which are currentyl installed and have **jdk** in the name again, we won't see any output.

```
dpkg --list | grep jdk
```

## Main Takeaways

1. We can install JDKs which are not available in _SDKMAN_ but they will not be accessible in our operating System.
2. We need to make _SDKMAN_ aware of their location using the `install` command.
3. Once _SDKMAN_ knows where a JDK is located, we can manage it with either the `use` or `default` commands.
