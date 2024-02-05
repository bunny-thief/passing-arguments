---
title: 'SDKMAN FTW'
date: 2024-02-09T09:00:00+01:00
draft: false
categories: ['Tools']
tags: [Java, JDK, SDK]
ShowCodeCopyButtons: true
summary: If you are manually downloading and installing JDKs and other Java related software libraries and packages, you need to check out SDKMAN, the Software Development Kit Manager.

cover:
  image: 'img/sdkman/bubble-logo-sdkman-groovy-color.png' # https://github.com/dmesu/sdkman-website/blob/master/site/src/site/assets/img/bubble-logo-sdkman-groovy-color.png
  alt: 'SDKMAN blue groovy logo' # alt text
  caption: 'SDKMAN blue groovy logo'
  cover.caption: 'test'
---

## Overview

If you are manually downloading and installing JDKs and other Java related software libraries and packages, you need to check out [SDKMAN](https://sdkman.io/ 'sdkman.io'), the Software Development Kit Manager. With SDKMAN you can manage JDKs and SDKs from the comfort of your command line.

What benefit does SDKMAN offer? It allows you to download, install, update and uninstall JDKs and SDKs with just a few clicks of your keyboard on a cli interface. Its killer feature is that you can switch between multiple versions without needing to uninstall the ones you are not currently using. It does this in a very seamless way which doesn't require you to learn different commands for each individual JDK or SDK you use. With SDKMAN you no longer need to scour across the internet looking for download URLs. SDKMAN knows where to get the version you are looking for that is compatible with your platform. If any of those haven't convinced you to give it a try, I'm sure this will. If you use SDKMAN you will no longer have to muck around with your system's PATH environment variable!

> SDK vs JDK
>
> A Software Developement Kit (SDK) contains the tools you need to make software applications all in one package.
>
> A Java Development Kit (JDK) is a specific type of SDK that is made for developing Java applications. A JDK contains development tools like the Java compiler and a Java Run-time which includes the Java Virtual Machine (JVM) as well as the Java Class Library. SDKMAN offers many JDKs from various vendors.
>
> Some of the available SDKs fall under different categories such libraries, build tools, frameworks and full-blown language specific SDKs for languages such as Kotlin and Scala. In order to keep things simple, libraries, packages and frameworks and non-Java language specific SDKs will be reffered to as an SDK and JDK will be used for Java specific SDKs.

While SDKMAN is a great tool that helps you set up software so you can focus on coding, there are a few use cases which it won't help you with.

1. Install non-LTS JDKs\*
2. Install Early Access JDK builds\*
3. Manage air gapped computers

The the following Long-term support (LTS) JDKs are available from various vendors: 8, 11, 17 and 21. If you want to use a non-LTS version of a JDK you'll have to download it manually. At the time this guide was written, it was possible to download Azul Zulu version 7 and 6 with SDKMAN. JDK 8 is technically not LTS but it has become a defacto one because it's still widely used and supported by the Java community. According to the JetBrains State of Developer Ecosystem 2023 survey, [50% of respondents still regularly use Java version 8](https://www.jetbrains.com/lp/devecosystem-2023/java/#java_versions 'The State of Developer Ecosystem 2023').

If you need to test out a feature in the absolute latest version of your JDK, then JDKMAN is probably not the right tool for you, unless you are only insterested in early access builds from OpenJDK, Liberica Native Image Kit and Red Hat Mandrel. These vendors offer Early Access builds of upcoming JDKs. As far as SDKs go, some offer alpha/release candidates but not many.

The target computer needs to be connected to the internet in order to take advantage of SDKMAN's main features. So if you have a server or Raspberry Pi that is not connected to the internet and you want to install a shiny new JDK on it, you'll have to do download it the old fashioned way on a computer that is online and somehow move it over to the target. While SDKMAN does have an "offline" mode, you still need to be online to download and upgrade an SDK.

In summary, SDKMAN lets you easily install, update and unistall JDKs and SDKs, easily switch between multiple versions without needing to uninstall them. You don't need to search the internet and best of all, you don't need to touch the your system's PATH. The only caveat is that the JDK or SDK you want needs to be available in SDKMAN which is done by the software vendors themselves. The SDKMAN team makes tools which vendors need to use in order to make their software available for users like us.

## Requirements

Although this guide was written for and tested on Ubuntu 22.04 LTS, all commands should work on Windows, if you have WSL instaled, and macOS on Z shell. This is a feature of SDKMAN as all [commands](#commands 'SDKMAN commands') are identical across all platforms.

## Installation

Installing SDKMAN requires running a bash script which you can download from their website. Open up the command terminal by pressing `Ctrl + Alt + T`. Now copy and paste the following command and press `Enter` to execute it. It will download the script and immediately run it on your command shell.

```
curl -s "https://get.sdkman.io" | bash
```

That bash script will do a number of things like check for previous installations of SDKMAN, make sure Zip and SED are installed, create the directory structure, detect the target platform, set up default settings and set up environment variables so you operating system knows where SDKMAN keeps SDKs that you have installed.

After the installation is completed, you will see the folowing message on the screen:

```
Please open a new terminal, or run the following in the existing one:
```

Copy and paste the command below.

```
source "$HOME/.sdkman/bin/sdkman-init.sh"
```

It's a one time initialization that sets up a list of the available "candidates" what SDKMAN uses to refers to SDKs and enables auto-completion on the command line.

You can make sure the installation went smoothly by checking the SDKMAN version.

```
sdk version
```

## Usage

### Commands

Using SDKMAN is a breeze. Just type in `sdk` and then `Enter`. That will display a list of all 16 commands which are available. To use a command, you need to type `sdk` followed by a command.

{{< sdkman/commands >}}

### Command Usage

#### Optional Parameters

Remember, you must type `sdk` before any command. Anything surrounded by square brackets is `[optional]`. So typing `sdk help` is valid. It prints out all available commands; it's the same as typing `sdk` by itself.

```
sdk help
```

You can also add anyone of the 15 other commands as the optional parameter to display the manpage for that command. Typing `sdk help install` displays the manpage for the install command. In this case, `install` is an optional _parameter_ of the `help` command.

```
sdk help install
```

> ### Manual Pages
>
> manpage is short for [Manual Page](https://wiki.debian.org/ManualPage 'debian.org/ManualPage') and is the customary method used to document Linux programs. It describes what the command does, what parameters it requires if any and the different option flags that are available. Type in `man man` at the terminal if you would like to see the manpage for Manual Pages. While regular manpages contain the entire documentation for one program, SDKMAN has broken out each command into a seperate manpage.

The manpage for all other commands will have the same structure. **NAME**, **SYNOPSIS**, **DESCRIPTION**, etc. I find the description and the examples are the most useful when first staring to use SDKMAN.

{{< sdkman/help-install>}}

#### Required Parameters

While square brackets denote parameters that are optional, diamond brackets tell us the parameter is `<required>`. If we try to use a command that requires a parameter without providing any, SDKMAN will complain. Let's try entering `install` without specifying a candiate.

```
sdk install
```

SDKMAN prints **"No candidate provided."** to the screen and displays the help menu because we obviously need it.

#### Candidates

The proper way to use `install` is to provide a _candidate_ name. A few commands, `uninstall`, `use` and `home` need two required parameters; a candiate and the version.

> ### Candidates
>
> Candidates are the names of the SDKs which can be managed with SDKMAN. Typing `sdk list` will display a long list of every available candidate in SDKMAN, along with some additional information in alphabetical order. There were 70 candidates at the time this guide was written.

#### Candidate Listing

Let's try the `list` command.

```
sdk list
```

We'll take the first entry in that list to go over the information each entry provides.

{{< sdkman/activemq-listing >}}

The listing for Apache ActiveMQ has the project name, a version number, the URL for the project's website and a nice long description. Nothing special right? Near the bottom we do have someting that does help us though. It's the install command for this candidate! Not only that but `sdk install activemq` also tells us the name SDKMAN uses internally to refer to this candidate, **activemq**. So whenever we want to manage _Apache ActiveMQ_ in SDKMAN we need to use `activemq` as the candidiate name.

#### Default Install Version

Before we move on let's also look at that version number listed at the top. It turns out `5.17.1` is not just any version number but I refer to as the **default Install version** for the `activemq` candidate. To see why that is important let's look at the first sentence in the description of the install command:

> "Invoking this subcommand with only the candidate as parameter will install the currently known **default version** for that candidate."

Ok, that's important and worth repeating. If we run the install command with only the candidate name, SDKMAN will install the default version.

What if we want to install a different version? Let's recall what the install command looks like from the **SYNOPSIS** section in the MAN page for install command.

`sdk install <candidate> [version] [path]`

So **version** is an optional parameter but how do we find which are versions are available? We can see all versions which are available for a particular candidate by providing the candidate name as the optional parameter to the `list` command. Here's what that looks like for the `activemq` candidate.

```
sdk list activemq
```

And here's the output. That's quite a few options for just one single SDK. It's a good idea to use the `sdk list` command to find the name for other candiats and then use the candidate name to find which versions are available for that candidate.

{{< sdkman/activemq-versions >}}

We now know what commands are avilable in SDKMAN, how to find help for those commands, what a candiate is, how to find a candidate's name and which versions are available for each candidate. It's time to put SDKMAN through it's paces by installing a candidate.

## Installing a Candidate

In this part were are going to install a JDK. Specifically, we are going to install [Temurin JDK 21.0.2](https://adoptium.net/temurin/releases/ 'Eclipse Temurin™ Latest Releases') which is the open source build of OpenJDK from [Eclipse Adoptium](https://adoptium.net/ 'Adoptium Working Group'). Feel free to install a JDK from a different vendor or a different candidate altogether.

Here are the steps we need to follow in order to install a candidate.

1. Display all available candidates
2. Get the SDKMAN candidate name from the candidate listing
3. List all available versions for the candidate
4. Install candidate

### Display available candidates

```
sdk list
```

Listing all candidates is not feasible so I'm only going to display the listing for the Java candidate. Scroll your way down until you see the Java listing.

### Get candidate name

We see the candidate name for all JDKs is **java** and that the default version is 21.0.2-tem. Note that this matches the JDK we want to install which is Temurin JDK 21.0.2. We could at this point run the install command with just the candidate name and omit the version and SDKMAN would install the correct version we are looking for. We'll skip that because we want to type the entire command out to get familiar with the full process.

{{< sdkman/java-listing >}}

### List available versions for java candidate

Type out the following command to list all available versions of the java candidate.

```
sdk list java
```

The list is quite long. Here, I'll list only versions of the Adoptium Temurin distribution. The top entry in the "Identifier" column is the one we need, **21.0.2-tem**. That is the latest available version of Temurin.

{{< sdkman/java-versions >}}

> ### JDK Versions
>
> JDK versions are different compared to the versions of other SDKs because they also need include the name of the distribution. The distribution follows the dash after the version.
>
> | Vendor           | Distribution | Distribution Code | Latest LTS Vesion |
> | ---------------- | ------------ | ----------------- | ----------------- |
> | Amazon           | Corretto     | -amzn             | 21.0.2-amzn       |
> | Java.net         | OpenJDK      | -open             | 21.0.2-open       |
> | Eclipse Adoptium | Temurin      | -tem              | 21.0.2-tem        |
> | Azul             | Zulu         | -zulu             | 21.0.2-zulu       |

### Intall java candidate

We managed to find the candidate name for installing JDKs; **java**, as well as the latest version available for Temurin; **21.0.2-tem**. Let's put it all together with the install command to begin the installation process.

```
sdk install java 21.0.2-tem
```

Hit "Enter" to begin Downloading. If you happen to lose your connection while downloading or you stop the process before it's finished, you can resume where it left off by running the same command you entered to begin the installation.

{{< sdkman/java-download >}}

If you have more than one version of a particular candidate installed, SDKMAN will ask you if you would like to make the newly installed version the **default** version. Note that the word _default_, in this context, has a different meaning to what I referred to earlier as [_default Install version_](#default-version 'Default Install Version') in the candidate listing section. SDKMAN's documentation uses the same term to refer to two different features. Making a version the **default** makes that version the one that is referred to by the OS in all command shells. This is what I would call _default shell version_. You cannot change the _default install version_, it always points to the latest release of a particular SDK.

```
sdk list java
```

I ran the `list` command to display all the java candidates again. Below you'll see list of the JDKs I **currently have installed on my computer**. I have three different versions of the Temurin JDK so I'm only display the Temurin section here to keep things brief.

{{< sdkman/temurin-versions>}}

The **`>>>`** in the **Use** column indicates the _default shell version_ which the OS will refer to. Under "Status" we see **installed** and **local only**. A version labeled _installed_ means that it was installed with SDKMAN; _local only_ versions are those that where installed manually outside of SDKMAN.

> ### JAVA_HOME
>
> Normally when we install a JDK, we need to [manually set](https://docs.oracle.com/en/cloud/saas/enterprise-performance-management-common/diepm/epm_set_java_home_104x6dd63633_106x6dd6441c.html#GUID-7D734C69-2DE8-4E93-A3C8-9C3F6AD12D1B 'Setting JAVA_HOME') the **JAVA_HOME** environment variable to the location where the JDK is installed. SDKMAN takes care of this step for us. SDKMAN also uses this variable to point to different versions when we change the **default** version to something else.
>
> If set correctly, we can display where the **JAVA_HOME** points to by using echo, a dollar sign **$** and the name of the environment varilable on the terminal:
>
> `echo $JAVA_HOME`
>
> With SDKMAN installed however, it points to `home/<username>/.sdkman/candidates/current` which is a symbolic link that SDKMAN points to different JDKs of our choosing.

It's also possible to select a different version as active inside of a terminal shell by running the `use` command. Which ever SDK is selected will only be active inside of _that_ terminal shell. The operating system (OS) will continue to point to the _default shell version_ you in other terminal windows and in any references that point to the **JAVA_HOME** environment variable.

We've reached the end of the this guide. I hope you found it usefull and begin using SDKMAN to manage your SDKs.

## Main Takeaways

1. Managing SDKs is tedious and erro prone.
2. SDKMAN simplyfies installing and managing SDKs.
3. It lets you switch between multiple versions of individual SDKs quickly.
4. SDKMAN works across Windows, macOS and Linux enveronments with a common set of commands.
