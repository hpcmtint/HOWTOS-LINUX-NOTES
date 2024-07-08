Here's the text formatted in Markdown:

**# Step 1: Install Java on Ubuntu 22.04**

There are two main options for installing Java. You can install OpenJDK which is the open-source implementation of Java. This is available on the default Ubuntu repository. Optionally, you can install Oracle JDK which is fully maintained by Oracle.

Both options are developed by Oracle. The difference between the two is that OpenJDK is free and opensource and enjoys support from the community. However, Oracle JDK 17 and future versions are free of charge for commercial use according to Oracle No-Fee Terms and Conditions (NFTC) license.

Right off the bat, log into your server instance via SSH. To verify if Java is installed, run the following command:

```bash
$ java -version
```

The output clearly indicates that Java is not installed. In addition, you get a few suggestions on how to install Java.

As you have seen, there are multiple ways of installing Java. However, we recommend the installation of OpenJDK (Open Java Development Kit).

**# Option 1: Install JAVA using OpenJDK**

OpenJDK provides all the tools you need to develop Java-based applications and microservices, including the Java compiler, Java Runtime Environment (JRE), and Java class library.

Ubuntu repositories provide the OpenJDK package by default, and you can search for its availability as shown.

```bash
$ sudo apt-cache search openjdk
```

At the time of writing, the most recent release is OpenJDK 20. The latest LTS ( Long Term Support ) release is OpenJDK 17 and will be supported until 30 September 2026.

To install OpenJDK 11 using the APT package manager, execute the command:

```bash
$ sudo apt install openjdk-17-jdk -y
```

Once the installation is complete, verify the version of Java installed by running the command:

```bash
$ java -version
```

The output shows that we have successfully installed OpenJDK 17.0.6.

That's one way of installing Java on Ubuntu.
```

Would you like me to explain or break down the formatting?
