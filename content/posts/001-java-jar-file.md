---
title: 'How to Create and Import a Java Archive file'
slug: java-jar-file
date: 2024-01-29T09:00:00+01:00
draft: false
categories: ['Development']
tags: [Java, Maven]
ShowCodeCopyButtons: true
summary: Do you want to share your Java application with others or reuse your own code without copying and pasting? Then you need to learn about Java Archives (JAR) files.

cover:
  image: 'img/001-java-jar-file/c-m-X_j3b4rqnlk-unsplash.jpg' # https://unsplash.com/photos/books-in-shelves-in-room-X_j3b4rqnlk
  alt: 'Archive Shelfs at Sächsisches Staatsarchiv in Dresden, Saxony, Germany' # alt text
  caption: 'Archive Shelfs at Sächsisches Staatsarchiv in Dresden, Saxony, Germany'
---

[Cover Image](https://unsplash.com/photos/books-in-shelves-in-room-X_j3b4rqnlk): Archive Shelfs at Sächsisches Staatsarchiv in Dresden, Saxony, Germany [@ubahnverleih](https://unsplash.com/@ubahnverleih)

## Overview

Do you want to share your Java application with others or reuse your own code without copying and pasting? Then you need to learn about Java Archives (JAR) files. We are going to discuss what JAR files are, how they are created and finally, how to import it into a Java application.

Before getting into all the details, let's first discuss what problem JAR files help solve. Let's imagine a scenerio, you are happily coding along when you come up with a problem that requires a to code a solution. You start to sketch out one way in which you might solve this when you suddenly realize that you've already encoutered this exact problem in the past. So you track down your old code, copy it and then paste it in your current project. Voila, problem solved! Well, not exactly.

At some point you decide you want to refactor your old trusty code or add a new feature to it but right before you commit your changes reality smacks you in the face. You're going to have to copy and paste these changes in the other code base as well. Now you're maintaining the same code in two different places! Not only that but if you want to add this code to other projects you'll just be adding it to a pile of projects you'll have to update each time you make a change. This doesn't sound [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) at all. In fact it feels like Big O of N problem. We need to make this O(1). This is where JAR files come to the rescue.

Instead of copying your existing solution in every project that requires it, you can use Maven to create a library. Doing so will let us keep our source code in one place and still be able to re-use it in different projects. Maven can do all heavy lifting of managing the dependency for us. All we have to do is tell Maven where it's located.

## Requirements

We need the following items installed. No perticular version is needed to work with JAR files just be aware that the current version of Maven, version 3.9.6 at the time this was written, requires a JDK of 8 or higher. If you don't have any of these installed, please download and install them before continuing to the next section. I'm running Ubuntu 22.04 so this guide assumes your are as well.

1. Java Development Kit (JDK 8+). I recommend the latest LTS release from [Eclipse Temurin](https://adoptium.net/)
2. [Apache Maven](https://maven.apache.org/)
3. [IntelliJ Ultimate or Community Edition](https://www.jetbrains.com/idea/download/?section=linux)
4. Linux Command line (Launch it by Pressing `Ctrl+Alt+T`)

## All About JAR Files

It's now time talk about what JAR files are before we move on creating one.

A JAR file is a compressed archive that contains one or more compiled Java files, directories and metadata. What's an archive? It's simply a file that contains other files. Compiled Java files are what you get after of compiling `.java` files. You can identify them because have the `.class` extension at the end. JAR files are similar to tar files except they used in the Java ecosystem. You can read more about the Tar file format over at [gnu.org](https://www.gnu.org/software/tar/manual/html_node/Standard.html) if you are interest.

Here is the structure of a JAR file that contains only one class file. Luckily, we don't need to know how it's organized as Maven takes care of putting everything in the right place. More on that later. The main takeaway is that inside of a JAR file we will find at least one Java class file, a Maven POM file and metadata files inside of a defined directory structure.

{{< 001-java-jar-file/jar-file-structure >}}

_JAR file directory structure._

One last thing before we finish this section. The JAR files's directory structure above is determined by the [Maven Standard Directory Layout](https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html). When you create a Maven project it creates the following nested directories `src/main/java`. It also marks the `/java` directory as a source directory and then nests a set of packages which were specified when the project was created. Marking the `/java` directory as source directory is a way of letting Maven know where to look for `.java` files that need to be compiled.

{{< 001-java-jar-file/primeNumbers-structure >}}

_Partial directory structure of a Maven project._

In this project, the `groupId` name follows the Maven naming convention because it minimizes the likelyhood that two Maven repositories collide with each other. This is because domain names are a unique combination of a domain and Top-level domain (TLD). There can only be one google.com. If that were not the case then you or I could theoretically purchase an existing domain and slap it on a web site. Doing so would break the internet as it would be impossible to route traffic to the correct destination. Again, this is only a convention and there is nothing stopping someone from actually naming their package using a domain name that is owned by someone else. Nevertheless, it's a convention we should stick to when naming our packages.

> ### Maven Naming Convention
>
> The [Maven naming convention](https://maven.apache.org/guides/mini/guide-naming-conventions.html), which in turn follows [Java's package naming convention](https://docs.oracle.com/javase/tutorial/java/package/namingpkgs.html), has two main requirements.
>
> 1.  Take the "reversed Internet domain name"
> 2.  Use only lowercase letters
>
> My internet domain name is _passingarguments.dev_ so that means the groupId name is `dev.passingarguments`. You can also append subgroups in order to organize your Java code into different sections which is what I did with this project so the package name ends up being `dev.passingarguments.primenumbers`.

## Create JAR File

In this section we are going to create a JAR from an existing Github repo. We'll clone the repo then we'll create a JAR. After that, we'll create a new Java project that depends on (uses) the code that is in the JAR file.

Here are the steps we need to follow:

1. Clone the `primeNumbers` Github repo.
2. Create JAR file from the `PrimeNumbers` class
3. Create a new Java project and add Maven dependency  
   i. Add the Maven dependency for `primeNumbers` library in the POM file  
   ii. Add an import statement inside a .java file  
   iii. Make calls to library methods

### Clone PrimeNumbers Github Repo

Launch the command line by pressing `Ctrl + Alt + T`.

Use the `cd` command to change the to a directory in which you would like to clone the project into. I suggest you do so in the Desktop.

```bash-session
cd ~/Desktop
```

Now execute the git clone command to pull a copy of the primeNumber repository to your desktop.

```bash
git clone git@github.com:bunny-thief/primeNumbers.git
```

`cd` in to the project directory.

```bash-session
cd primeNumbers
```

> ### Github Docs - Cloning a Github Respository
>
> Github has instructions [here](https://docs.github.com/en/repositories/creating-and-managing-repositories/cloning-a-repository) on how to clone a repository directly from the repo page.

### Create PrimeNumbers JAR file

In this section we are going to create a JAR file inside the Java project and then we'll also install a copy in the local repository. We'll be using two different Maven commands to accomplish this. First we'll use `mvn package` and then `mvn install`. What's the difference? The `mvn package` command takes all of the `.java` files in your project and compiles them. It also creates a JAR file and then saves everything inside the `/target` directory in the top-level of your project. Remember, a JAR file contains a compressed archive file of every `.class` file along with metadata. Because the `primeNumbers` project only has one Java class, the resultant JAR will only contain one `.class` file. The `mvn install` command does everything the package does and it also saves a copy inside of the local Maven repository. Having a copy there is what makes it possible to easily re-use your code in various Java projects by including it in your POM file.

#### PrimeNumber class

Here's code we are going to be working with. It's a Java class named `PrimeNumbers` with three static methods that, suprise, suprise, work with prime numbers. We are going to create a JAR file from this project so we can call methods in that are in the `PrimeNumbers` class from inside other Java projects. For the sake of brevity, this snippet only shows the `isPrime()` method; the other methods have been omitted. The second method returns the next prime number given an input and the last one accepts a starting and ending number and returns an array of all prime numbers in between the start and end number.

{{< highlight java >}}
package dev.passingarguments.primenumbers;

public class PrimeNumbers {

    public static boolean isPrime(int n) {
        if (n == 2) {
            return true;
        }

        int midpoint = (int) Math.sqrt(n);

        for (int i = 2; i <= midpoint + 1; i++) {
            if (n % i == 0) {
                return false;
            }
        }

        return true;
    }
    // rest of class excluded...

}
{{< / highlight >}}
_PrimeNumbers class._

The code the PrimeNumbers class can be found at [Github](https://github.com/bunny-thief/primeNumbers).

#### Testing Maven

Let's run the Maven clean command to make sure Maven is installed and working.

```bash
mvn clean
```

Hopefully you see the following output which indicates Maven is in good working order. If we see `BUILD SUCCESS` then we know all is alright and we can move on the the next part. Otherwise, if instead we see `BUILD FAILURE`, then something went wrong and you'll have to trouble shoot your Maven installation. Keep in mind that your output will only match what you see here if you are working with the [cloned repo](#clone-github-repo). If you are using your own Java project, then the `groupId` and subgroup will be different.

{{< 001-java-jar-file/mvn-clean >}}

_mvn clean screen ouput._

#### Create JAR File - `package` command

It' finally time to create the `.jar` file. For that we run the `package` command. This command compiles `.java` files, runs tests if they are present in the project and then creates the JAR file in the `/taget` directory.

```bash
mvn package

```

Check the output of this command. Near the bottom, you should the following three lines.

{{< 001-java-jar-file/mvn-success >}}

The middle line is the one we are most interested in: `/home/user/Desktop/primeNumbers/target/primeNumbers-0.3.0.jar`. It tells us the location where the JAR file was saved `/home/user/Desktop/primeNumbers/target/` as well as the name given to the file `primeNumbers-0.3.0.jar`. The location is the default used by Maven. The name of the file is taken from the `artifactId` and `version` of our project's POM file. You can grab that file and send it to anyone in order to share your Java application with them. Let's now try the `install` command.

> ### mvn clean package
>
> ---
>
> You can combine `clean` and `package` commands. Doing so ensures a previously created JAR is first removed before a new one saved in the same locaion.

#### Create JAR File - `install` command

In our last step we navigated to the root folder of our Java application. If you are not there `cd` into the project now before running the next command.

```bash
 cd ~/Desktop/primeNumbers
```

And now execute the Maven install command.

```bash
 mvn install
```

> ### Maven Build Lifecycle
>
> ---
>
> Maven has a concept called Buid Lifecycle which is made up of various ordered phases. Calling the `mvn compile` command will execute the build lifecycle up to the compile phase. Calling `mvn install` executes all phases starting from the first one, up to and including, the install phase. The list of phases below is a shortened version which only shows the ones that are used the most. See [Introduction to the Build Lifecycle](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html) in the Maven docs for a full list.
>
> - compile
> - test
> - package
> - install

Here is the output from executing `mvn install`:

{{< 001-java-jar-file/mvn-install >}}

The second line and third lines tell us where in the local Maven repository the POM and JAR file are saved.

> ### Local Maven Repository
>
> `/home/user/.m2` is the default location for local Maven repositories, where _user_ is the Ubuntu username. Maven will search inside the `/.m2` directory whenever a new external dependency is added to the POM file. The local repository is also used to store 3rd-party libraries. These are libraries which are created by others and which are included in your project such as the [PostgresQL JDBC driver](https://central.sonatype.com/artifact/org.postgresql/postgresql).
>
> These libraries exist on remote repositories such as [Maven Central](https://central.sonatype.com/) or one that is hosted in an internal server inside of a company. If a 3rd-party library is not found in our local repostory, Maven searches in the remote repository and downloads it to your `/.m2` directory before adding it to the project. The next time you reload the Maven or import the same dependency into another project, Maven will find it in the `/.m2` directory and won't need to download it from the remote repository.

Now that we have a JAR file in our local repository, it's time to create a new project, add the Maven the dependency and import it.

### Create new Java project

In this section we are going to create a new Java project, import the `PrimeNumbers` class into the newly created project and then call the `isPrime()` method to check if a given input is a prime number.

Start IntelliJ IDEA and create a new Java project. Make sure to select Maven as the "Build System".

![⚠️IntelliJ IDEA new Project Dialogue](/img/jar/00-create-app.png 'IntelliJ IDEA new Project Dialogue')
_IntelliJ IDEA new Project Dialogue._

Here's what the starting code looks like.

{{< highlight java >}}
package dev.passingarguments;

public class Main {

public static void main(String[] args) {
System.out.println("Hello world!");
}
}
{{< / highlight >}}
_PrimeNumberApp starter code._

Our focus is to reuse the functionality of the `PrimeNumbers` class inside of this project. To that end, we are going to keep the code really simple. We just need to call the `isPrime()` method.

Let's add an instance of the Scanner class so the application accepts user input and print a message to let user know we are waiting for their input.

{{< highlight java >}}
package dev.passingarguments;

import java.util.Scanner;

public class Main {
public static void main(String[] args) {

        Scanner scanner = new Scanner(System.in);

        System.out.println("Prime Number Checker");
        System.out.print("Enter number: ");

        int userInput = Integer.valueOf(scanner.nextLine());

        // check if the number is prime

    }

}
{{< / highlight >}}
_Code to accept user input._

At this point our starter code is ready. Our application accepts a number as input and now we need to check if that number is prime. Before we can call the `isPrime()` method, we first need to let Maven know how to locate the `PrimeNumbers` JAR file. We do this by adding it as a dependency in the POM file of the current project.

There's three pieces of information we need to add the dependency:

1. `groupId`
2. `artifactId`
3. `version`

We can get all three from the POM file inside the `primeNumbers` project. The one used to create the JAR file. Here's what the top section of POM file looks like.

> ### Maven Coordinates
>
> According to the [Maven documentation](https://maven.apache.org/pom.html#The_Basics), together these three elements are called coordinates because they "point to a specific version of a project, letting Maven know who we are dealing with".

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>dev.passingarguments</groupId>
    <artifactId>primeNumbers</artifactId>
    <version>0.3.0</version>

    <!-- rest of file excluded -->
</project>
```

_PrimeNumbers Maven coordinates._

We need to copy the lines with the `groupId`, `artifactId` and `version` into the POM file of the _primeNumberApp_ project. As this is being added as dependency, all three lines have be inside of a `<dependency>` tag which itself must me inside of a `<dependencies>` tag. Watch the spelling of these two tags. The first one is singular, while the second one is plural. One `<dependecy>` tag may have only contain one set of `groupId`, `artifactId` and `version` which points to a library in a Maven repository. While a `<dependencies>` tag can contain one or more `<dependecy>` tags. If that sounds confusing have a look at the snippet below.

```xml
<dependencies>
  <dependency>
    <groupId>dev.passingarguments</groupId>
    <artifactId>primeNumbers</artifactId>
    <version>0.3.0</version>
  </dependency>
</dependencies>
```

_Maven dependency list with dependency reference to primeNumbers library._

We can copy and paste this entire snippet into the primeNumberApp's POM file. Here's what the entire POM file should look like.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>dev.passingarguments</groupId>
    <artifactId>primeNumberApp</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>17</maven.compiler.source>
        <maven.compiler.target>17</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>dev.passingarguments</groupId>
            <artifactId>primeNumbers</artifactId>
            <version>0.3.0</version>
        </dependency>
    </dependencies>

</project>
```

_PrimeNumberApp POM file with dependencies list._

We are almost ready to insert a call to the `isPrime()` method. Before we go back to writing more code we need to make sure we **reload** the POM file. If we don't reload it, it won't add the JAR file won't be added to the project. In the screencapture below we can see on the right side a small white "M" with two blue arrows. We need to click that little log to load our changes.

![⚠️Maven Reload Button](/img/jar/04-reload-button.png 'Maven Reload Button')
_Maven Reload Button._

The following gif shows our library being added to the project's External Library after reloading the Maven POM file.

![⚠️Add primeNumbers library to 'External Libraries'](/img/jar/02-reload-maven-changes.gif "Add primeNumbers library to 'External Libraries'")
_Add primeNumbers library to 'External Libraries'._

### Add an import statement inside a .java file

Let's go back to IntelliJ and add the following import statement above the class name: `import dev.passingarguments.primeNumber.PrimeNumbers;`. As you begin typing the import statement you see IntelliJ showing auto-completiong hints. That's a good sign.

```java
package dev.passingarguments;

import java.util.Scanner;

import dev.passingarguments.primenumbers.PrimeNumbers;

public class Main {
    ...
}
```

_PrimeNumbers import statement._

### Make calls to library methods

With the import statement added in the previous step, we can now call the `isPrime()` method and save the return value in a `boolean`.

```java
public static void main(String[] args) {

        Scanner scanner = new Scanner(System.in);

        System.out.println("Prime Number Checker");
        System.out.print("Enter number: ");

        int userInput = Integer.valueOf(scanner.nextLine());

        // check if the number is prime
        boolean userInputIsPrime = PrimeNumbers.isPrime(userInput);

    }
```

_Call to isPrime() method._

Finally, let's add logic that checks if `userInput` is a prime number and prints a message to the console.

```java
public static void main(String[] args) {

        Scanner scanner = new Scanner(System.in);

        System.out.println("Prime Number Checker");
        System.out.print("Enter number: ");

        int userInput = Integer.valueOf(scanner.nextLine());

        // check if the number is prime
        boolean userInputIsPrime = PrimeNumbers.isPrime(userInput);

        if (userInputIsPrime) {
            System.out.println(userInput + " is a prime number.");
        } else {
            System.out.println(userInput + " is a composite number.");
        }

    }
```

_Complete code for PrimeNumberApp._

Here's what the output to the console looks like when we run the application and submit 37 as the input.

{{< 001-java-jar-file/primeNumbers-output >}}

_Screen output after running PrimeNumberApp._

And with that we have reached the end of this guide. The code to the _primeNumberApp_ can be found at the [Github repo](https://github.com/bunny-thief/primeNumberApp).

## Main Takeaways

1. Sharing/re-using code with JAR files is better than copying and pasting.
2. Maven can be used to create a JAR file in the `/target` directory of our project with the `mvn package` command.
3. Running the `mvn install` command does everything `package` does and additionally saves it in the local Maven repository which is located at `~/user/.m2/repository`.
4. Libraries in the local Maven repository can imported into other projects if we add the Maven coordinates in the project's POM file.
