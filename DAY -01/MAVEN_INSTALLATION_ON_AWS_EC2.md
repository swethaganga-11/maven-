# Apache Maven Installation on AWS EC2 Linux

## 1. What Is Maven?

**Apache Maven** is a build automation and project management tool mainly used for Java projects.

Maven helps developers to:
- Compile Java source code.
- Download and manage project dependencies.
- Run unit tests.
- Package applications into JAR or WAR files.
- Build applications consistently.
- Integrate projects with CI/CD tools such as Jenkins and GitHub Actions.

Maven uses a configuration file called **`pom.xml` (Project Object Model)** to manage project information, dependencies, plugins, and build settings.

### Simple Example

Suppose we are developing a Java application that requires external libraries.

Without Maven, we may need to download the required JAR files manually and configure them.

With Maven, we specify the dependencies in `pom.xml`, and Maven downloads the required libraries from a repository.

### Why Do We Use Maven in DevOps?

Maven is commonly used in CI/CD pipelines to build and test Java applications before packaging and deploying them.

**Example workflow:**

Developer → GitHub → Jenkins → Maven Build → Testing → JAR/WAR Artifact → Deployment

---

## 2. Prerequisites

Before installing Maven, we need:

1. An AWS account.
2. An EC2 Linux instance.
3. SSH access to the instance.
4. Java Development Kit (JDK).
5. Internet access to download Maven and its dependencies.

**Important:** Maven requires Java to run. Therefore, we must install and verify Java before installing Maven.

---

## 3. Step 1: Create an EC2 Instance

1. Log in to the AWS Management Console.
2. Open the EC2 service.
3. Click **Launch Instance**.
4. Enter an instance name, such as `Maven-Server`.
5. Select an Amazon Linux AMI.
6. Select an instance type, such as `t2.micro` or an available equivalent.
7. Create or select an SSH key pair.
8. Configure the security group to allow SSH on port `22` from your IP address.
9. Launch the instance.
10. Wait until the instance is running and its status checks have passed.

### Connect to EC2

Connect using SSH from your terminal:

```bash
ssh -i "your-key.pem" ec2-user@YOUR_EC2_PUBLIC_IP
```

Replace `your-key.pem` with your private key filename and `YOUR_EC2_PUBLIC_IP` with the public IP address of your instance.

**Explanation:**

- `ssh` establishes a secure connection to the remote server.
- `-i` specifies the private key used for authentication.
- `ec2-user` is the usual default username for Amazon Linux EC2 instances.
- `YOUR_EC2_PUBLIC_IP` is the public IP address of your EC2 instance.

---

## 4. Step 2: Switch to the Root User

Run:

```bash
sudo -i
```

**Explanation:**

- `sudo` executes commands with elevated privileges.
- `-i` starts a login shell for the root user.
- Root access allows us to install software and modify system directories such as `/opt`.

Verify the current user:

```bash
whoami
```

Expected output:

```text
root
```

**Note:** Using `sudo` for individual administrative commands is generally preferable to staying logged in as root. If you switch to root, remember that the root user's environment configuration is separate from the `ec2-user` environment.

---

## 5. Step 3: Update Linux Packages

For Amazon Linux 2:

```bash
yum update -y
```

For Amazon Linux 2023:

```bash
dnf update -y
```

If you are following a traditional Amazon Linux 2 tutorial, `yum update -y` is appropriate.

**Explanation:**

- `yum` or `dnf` manages software packages.
- `update` updates installed packages when newer versions are available.
- `-y` automatically answers yes to confirmation prompts.

Updating packages helps ensure the operating system has available package updates and security fixes.

**Note:** Updating packages does not necessarily upgrade the operating system to a different major release.

---

## 6. Step 4: Check the Available Java Packages

For Amazon Linux 2, you can search for OpenJDK packages:

```bash
yum search java | grep -i openjdk
```

For Amazon Linux 2023:

```bash
dnf search java | grep -i openjdk
```

**Explanation:**

- `yum search java` or `dnf search java` searches the package repositories for Java-related packages.
- `|` is a pipe that passes the first command's output to the next command.
- `grep` searches the output for matching text.
- `-i` makes the search case-insensitive.
- `openjdk` refers to an open-source implementation of the Java platform.

This command helps us identify Java packages available in the configured repositories.

### What Is OpenJDK?

OpenJDK is an open-source implementation of the Java platform. A JDK includes the tools needed to develop and compile Java applications.

---

## 7. Step 5: Install Java

For Amazon Linux 2, you can install Java 17 using:

```bash
yum install java-17-amazon-corretto-devel -y
```

For Amazon Linux 2023, use:

```bash
dnf install java-17-amazon-corretto-devel -y
```

Amazon Corretto is an OpenJDK distribution provided by AWS.

Java 17 is a suitable choice for many Java projects, although the Java version required by a particular application may differ.

### Verify Java Installation

Check the Java runtime version:

```bash
java -version
```

Check the Java compiler version:

```bash
javac -version
```

**Explanation:**

- `java -version` displays the installed Java runtime version.
- `javac -version` displays the Java compiler version.

If both commands work, the Java runtime and development tools are available.

**Important:** Maven requires a JDK. Installing only a Java runtime may not provide the compiler needed to build Java applications.

---

## 8. Step 6: Switch to the Optional Software Directory

Run:

```bash
cd /opt
```

Check the current directory:

```bash
pwd
```

Expected output:

```text
/opt
```

**Explanation:**

- `cd` changes the current working directory.
- `/opt` is commonly used to store additional or third-party software.

We will install Maven in `/opt` so that its installation files are kept separate from ordinary application files.

---

## 9. Step 7: Install Required Utility Tools

For Amazon Linux 2:

```bash
yum install wget git tree unzip tar gzip -y
```

For Amazon Linux 2023:

```bash
dnf install wget git tree unzip tar gzip -y
```

### Explanation of the Tools

| Tool | Purpose |
|---|---|
| `wget` | Downloads files from URLs. |
| `git` | Manages source code and version control. |
| `tree` | Displays directories and files in a tree structure. |
| `unzip` | Extracts ZIP archives. |
| `tar` | Creates and extracts archive files. |
| `gzip` | Compresses or decompresses files using gzip. |

We need `wget` to download Maven and `tar` to extract the Maven `.tar.gz` archive.

**Note:** These tools are useful for Linux and DevOps practice, but Maven itself does not require all of them.

---

## 10. Step 8: Download Maven

Check the official Apache Maven download page for the latest stable release before installing:

https://maven.apache.org/download.cgi

In this example, we use Maven `3.10.0`.

Move to the installation directory:

```bash
cd /opt
```

Download the binary distribution:

```bash
wget https://dlcdn.apache.org/maven/maven-3/3.10.0/binaries/apache-maven-3.10.0-bin.tar.gz
```

**Explanation:**

- `wget` downloads a file from a URL.
- The URL points to the Maven binary distribution.
- `apache-maven-3.10.0-bin.tar.gz` is the compressed Maven installation archive.

The binary distribution contains the files required to run Maven.

### Verify the Download

Run:

```bash
ls -lh apache-maven-3.10.0-bin.tar.gz
```

This displays the downloaded file and its size.

---

## 11. Step 9: Extract the Maven Archive

Run:

```bash
tar -xzf apache-maven-3.10.0-bin.tar.gz
```

**Explanation:**

| Option | Meaning |
|---|---|
| `-x` | Extract files from an archive. |
| `-z` | Process a gzip-compressed archive. |
| `-f` | Specify the archive filename. |

After extraction, Maven will be available in this directory:

```text
/opt/apache-maven-3.10.0
```

Check the directory:

```bash
ls -l /opt
```

Check the Maven installation files:

```bash
ls -l /opt/apache-maven-3.10.0
```

You should see directories and files such as `bin`, `boot`, `conf`, `lib`, and `README.txt`.

The `bin` directory contains the Maven executable.

---

## 12. Step 10: Configure Maven Environment Variables

We need to configure Maven's installation path so that we can run the `mvn` command from any directory.

### Open the Bash Profile

If you are logged in as the root user:

```bash
vi ~/.bash_profile
```

If you are logged in as `ec2-user`, this edits that user's profile instead.

### Add the Following Content

```bash
export M2_HOME=/opt/apache-maven-3.10.0
export PATH=$M2_HOME/bin:$PATH
```

Save the file and exit the editor.

In Vim:

1. Press `Esc`.
2. Type `:wq`.
3. Press `Enter`.

### Explanation of Environment Variables

**M2_HOME**

```bash
export M2_HOME=/opt/apache-maven-3.10.0
```

This sets `M2_HOME` to the Maven installation directory.

**PATH**

```bash
export PATH=$M2_HOME/bin:$PATH
```

This adds Maven's `bin` directory to the existing `PATH`.

The operating system uses `PATH` to locate executable commands. Adding Maven's `bin` directory allows us to run `mvn` without typing its full path.

### Important Syntax Correction

Incorrect:

```bash
export M2_HOME = /opt/apache-maven-3.10.0
export PATH=$PATH:M2_HOME/bin
```

Correct:

```bash
export M2_HOME=/opt/apache-maven-3.10.0
export PATH=$M2_HOME/bin:$PATH
```

In Bash, do not put spaces around `=` when assigning a variable. Also, `$M2_HOME` expands the variable's value, while `M2_HOME` without `$` is just literal text in this context.

---

## 13. Step 11: Reload the Bash Profile

Run:

```bash
source ~/.bash_profile
```

**Explanation:**

- `source` reads and executes the commands in the specified file in the current shell.
- `~/.bash_profile` is the Bash login profile of the current user.

This makes the newly configured environment variables available in the current shell without requiring you to log out and log in again.

Verify the Maven home:

```bash
echo "$M2_HOME"
```

Expected output:

```text
/opt/apache-maven-3.10.0
```

Check the PATH:

```bash
echo "$PATH"
```

The output should contain:

```text
/opt/apache-maven-3.10.0/bin
```

---

## 14. Step 12: Verify Maven Installation

Run:

```bash
mvn -v
```

You should see output containing information similar to:

```text
Apache Maven 3.10.0
Maven home: /opt/apache-maven-3.10.0
Java version: 17...
Java home: ...
```

The exact Java version, vendor, and other details depend on your installed environment.

### Explanation

- `mvn` invokes the Maven command-line tool.
- `-v` displays the Maven version and environment information.

This confirms that Maven can run and identifies the Java installation Maven is using.

**Maven installation is complete!**

---

## 15. Complete Installation Commands

The following commands summarize the installation for **Amazon Linux 2** using Java 17 and Maven 3.10.0.

Run them after connecting to EC2.

```bash
# 1. Switch to root
sudo -i

# 2. Update packages
yum update -y

# 3. Search for Java packages
yum search java | grep -i openjdk

# 4. Install Java JDK
yum install java-17-amazon-corretto-devel -y

# 5. Verify Java
java -version
javac -version

# 6. Install required tools
yum install wget git tree unzip tar gzip -y

# 7. Move to the software installation directory
cd /opt

# 8. Download Maven
wget https://dlcdn.apache.org/maven/maven-3/3.10.0/binaries/apache-maven-3.10.0-bin.tar.gz

# 9. Extract Maven
tar -xzf apache-maven-3.10.0-bin.tar.gz

# 10. Configure environment variables
cat >> ~/.bash_profile <<'EOF'
export M2_HOME=/opt/apache-maven-3.10.0
export PATH=$M2_HOME/bin:$PATH
EOF

# 11. Reload the profile
source ~/.bash_profile

# 12. Verify Maven
mvn -v
```

**Note:** This summary assumes that the download URL is accessible and the selected package is available in your configured repositories. Check the official Maven download page if the release URL changes. For Amazon Linux 2023, use `dnf` instead of `yum` in the package-management commands.

---

## 16. Common Errors and Troubleshooting

### Error 1: `mvn: command not found`

**Possible cause:** Maven's `bin` directory is not in the current user's `PATH`.

Check:

```bash
echo "$M2_HOME"
echo "$PATH"
```

Reload the profile:

```bash
source ~/.bash_profile
```

Try again:

```bash
mvn -v
```

If you changed users, ensure that you configured the correct user's profile.

### Error 2: `JAVA_HOME` Is Not Defined Correctly

Check the Java executable:

```bash
which java
```

Check the JDK compiler:

```bash
which javac
```

Check whether Java is installed:

```bash
java -version
```

If necessary, determine the actual JDK location and configure `JAVA_HOME` to point to the JDK root directory. Do not assume that the path is identical on every Linux installation.

### Error 3: Permission Denied

Check directory permissions:

```bash
ls -ld /opt/apache-maven-3.10.0
```

Check whether the current user can execute Maven:

```bash
ls -l /opt/apache-maven-3.10.0/bin/mvn
```

Use appropriate permissions and ownership rather than applying unrestricted permissions to the installation directory.

### Error 4: Maven Download Fails

Check network connectivity and the download URL.

Open the official Apache Maven download page and confirm that the selected binary archive is available.

If a download is incomplete, remove the incomplete archive and download it again.

### Error 5: `mvn -v` Shows the Wrong Java Version

Run:

```bash
which java
java -version
echo "$JAVA_HOME"
```

Verify that the intended JDK is installed and that the environment variables and PATH point to the correct installation.

---

## 17. Interview Questions and Answers

### Q1. What is Maven?

**Answer:** Maven is a build automation and project management tool mainly used for Java applications. It helps compile code, manage dependencies, run tests, and package applications into JAR or WAR files.

### Q2. Why do we use Maven?

**Answer:** We use Maven to automate the build process, manage project dependencies, maintain a standard project structure, and integrate Java applications with CI/CD pipelines.

### Q3. What is `pom.xml`?

**Answer:** `pom.xml` stands for Project Object Model. It is the main Maven configuration file that contains project information, dependencies, plugins, and build settings.

### Q4. What is a Maven dependency?

**Answer:** A dependency is an external library or component required by a project. Maven downloads and manages the required dependencies based on the configuration in `pom.xml`.

### Q5. What is the difference between JDK and Maven?

**Answer:** JDK provides the Java development tools required to compile and run Java applications. Maven automates the build process and manages dependencies for the project. Maven requires Java to run.

### Q6. What is the purpose of `M2_HOME`?

**Answer:** `M2_HOME` is an environment variable that identifies the Maven installation directory. It can be used to configure Maven's location in the system environment.

### Q7. Why do we add Maven to the PATH?

**Answer:** We add Maven's `bin` directory to the PATH so that we can execute the `mvn` command from any directory without specifying its full path.

### Q8. Why do we use `source ~/.bash_profile`?

**Answer:** We use this command to reload the Bash profile in the current shell. It makes newly configured environment variables available without requiring a new login session.

### Q9. What is the purpose of the `mvn -v` command?

**Answer:** It displays the installed Maven version, Maven home directory, Java version, Java home, and other environment details.

### Q10. What is the difference between `yum` and `dnf`?

**Answer:** Both are Linux package managers used to install, update, and remove software. Amazon Linux 2 commonly uses `yum`, while Amazon Linux 2023 uses `dnf`.

### Q11. What is the difference between `mvn clean` and `mvn package`?

**Answer:**
- `mvn clean` removes generated build output, usually the `target` directory.
- `mvn package` executes the required build phases to compile, test, and package the application.

### Q12. What is the difference between JAR and WAR?

**Answer:** A JAR file packages Java classes and resources into an archive. A WAR file packages a web application for deployment to a compatible Java web server or application server.

### Q13. How is Maven used in a CI/CD pipeline?

**Answer:** In a CI/CD pipeline, a tool such as Jenkins checks out the source code and invokes Maven to compile the application, run tests, and generate a build artifact. The artifact can then be published or deployed.

### Q14. Where does Maven download dependencies from?

**Answer:** Maven normally downloads dependencies from configured remote repositories, such as Maven Central, and caches them in the local repository, which is usually located at `~/.m2/repository`.

### Q15. How do you check whether Maven is installed correctly?

**Answer:** I run `mvn -v` to verify the Maven version, Maven home directory, and Java environment. If the command is not found, I check the Maven installation directory and PATH configuration.

---

## 18. Interview Explanation: How I Installed Maven on EC2

**Question: How did you install Maven on a Linux EC2 instance?**

**Answer:**

First, I created an Amazon Linux EC2 instance and connected to it using SSH. Then, I obtained administrative privileges and updated the system packages.

Next, I installed the Java Development Kit and verified the Java version. After that, I installed the required utility tools and downloaded the Maven binary archive into the `/opt` directory.

I extracted the archive and configured the `M2_HOME` and `PATH` environment variables in the appropriate Bash profile. Then, I reloaded the profile using the `source` command.

Finally, I executed `mvn -v` to verify that Maven was installed correctly and could identify the Java environment.

---

## 19. Quick Revision

| Step | Command | Purpose |
|---|---|---|
| 1 | `ssh -i "your-key.pem" ec2-user@IP` | Connect to EC2 |
| 2 | `sudo -i` | Switch to root |
| 3 | `yum update -y` | Update Amazon Linux 2 packages |
| 4 | `yum search java \| grep -i openjdk` | Search for Java packages |
| 5 | `yum install java-17-amazon-corretto-devel -y` | Install Java JDK |
| 6 | `java -version` | Verify Java |
| 7 | `cd /opt` | Open the software installation directory |
| 8 | `yum install wget git tree unzip tar gzip -y` | Install utility tools |
| 9 | `wget <MAVEN_DOWNLOAD_URL>` | Download Maven |
| 10 | `tar -xzf <MAVEN_ARCHIVE>` | Extract Maven |
| 11 | `vi ~/.bash_profile` | Configure environment variables |
| 12 | `source ~/.bash_profile` | Reload the Bash profile |
| 13 | `mvn -v` | Verify Maven |

*The package commands in this table are for Amazon Linux 2. Use `dnf` for Amazon Linux 2023.*

---

## 20. Official References

- Apache Maven Installation Guide: https://maven.apache.org/install.html
- Apache Maven Downloads: https://maven.apache.org/download.cgi
- Apache Maven Project: https://maven.apache.org/
- AWS Documentation: https://docs.aws.amazon.com/

---

## Conclusion

Apache Maven is an important tool for Java development and DevOps automation. Installing Maven on an EC2 Linux instance involves preparing the server, installing a JDK, downloading and extracting Maven, configuring environment variables, and verifying the installation.

The key verification command is:

```bash
mvn -v
```

This procedure provides a foundation for using Maven in Java builds and CI/CD pipelines.
