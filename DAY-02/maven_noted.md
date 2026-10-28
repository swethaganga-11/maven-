# Maven Notes and Interview Questions

## 1. What is Maven?

Maven is a build automation tool mainly used for Java projects.

### Advantages of Maven
- Downloads required libraries automatically.
- Compiles Java code.
- Runs test cases.
- Packages applications into JAR or WAR files.
- Manages project dependencies.

**Example:** If our Java project needs JUnit for testing, Maven downloads the required library using the configuration in `pom.xml`.

---

## 2. What is a Maven Repository?

A Maven repository is a place where Maven stores libraries, plugins, and project artifacts.

There are three types of Maven repositories.

### A. Local Repository
- Stored on our local computer.
- Stores downloaded dependencies.
- Reuses libraries for different projects.

**Default location:**

Linux:
```bash
~/.m2/repository
```

Windows:
```text
%USERPROFILE%\.m2\repository
```

### B. Central Repository
- An online repository.
- Contains many Java libraries and plugins.
- Maven can download dependencies from it automatically.

### C. Remote Repository
- A repository hosted on a remote server.
- Companies may use it to store internal libraries.
- It can be a private repository.

### How Does Maven Download Dependencies?

1. Maven checks the local repository.
2. If the dependency is missing, Maven checks configured remote repositories.
3. Maven downloads the dependency.
4. Maven stores it in the local repository.

---

## 3. What is pom.xml?

`pom.xml` stands for **Project Object Model**.

It is the main configuration file of a Maven project.

It contains:
- Project information.
- Dependencies.
- Plugins.
- Build configuration.
- Project version.

### Important pom.xml Elements

| Element | Description |
|---|---|
| `groupId` | Identifies the project group or organization |
| `artifactId` | Name of the project |
| `version` | Version of the project |
| `dependencies` | Libraries required by the project |
| `build` | Build configuration |
| `plugins` | Tools used to perform build tasks |

### Example pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>my-first-maven-project</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.release>17</maven.compiler.release>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>5.11.4</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.5.2</version>
            </plugin>
        </plugins>
    </build>

</project>
```

**Note:** This example uses Java 17. Install a compatible JDK to build the project.

---

## 4. How to Create a Maven Project

### Step 1: Install Java

Check the Java version:

```bash
java -version
```

### Step 2: Install Maven

Check the Maven version:

```bash
mvn -version
```

### Step 3: Create a Maven Project

Run:

```bash
mvn archetype:generate \
-DgroupId=com.example \
-DartifactId=my-first-maven-project \
-DarchetypeArtifactId=maven-archetype-quickstart \
-DinteractiveMode=false
```

### Step 4: Enter the Project Directory

```bash
cd my-first-maven-project
```

### Step 5: Check Project Files

```bash
ls
```

You should see `pom.xml` and the project source directories.

### Step 6: Run the Tests

```bash
mvn test
```

Maven downloads the required dependencies, compiles the code, and runs the configured tests.

### Maven Project Structure

```text
my-first-maven-project/
├── pom.xml
├── src/
│   ├── main/
│   │   └── java/
│   └── test/
│       └── java/
└── target/
```

- `pom.xml`: Project configuration.
- `src/main/java`: Main Java source code.
- `src/test/java`: Test source code.
- `target`: Generated build output.

---

## 5. What Are Testing Frameworks?

A testing framework helps developers write and execute tests to check whether their code works correctly.

### Common Java Testing Frameworks and Tools

| Name | Purpose |
|---|---|
| JUnit | Used for Java unit testing |
| TestNG | Used for automated testing and test organization |
| Mockito | Creates mock objects for testing |
| Selenium | Automates browser interactions for web testing |

### JUnit Example

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;

class CalculatorTest {

    @Test
    void testAddition() {
        assertEquals(5, 2 + 3);
    }
}
```

Save this file as `CalculatorTest.java` under `src/test/java`.

Run the test:

```bash
mvn test
```

Maven uses the Surefire plugin to run tests during the test phase.

---

## 6. What Is the Maven Build Life Cycle?

The Maven build life cycle is a sequence of phases used to build and test a project.

### Important Maven Phases

| Phase | Explanation |
|---|---|
| `validate` | Checks whether the project is valid |
| `compile` | Compiles the main Java source code |
| `test` | Runs automated tests |
| `package` | Creates a JAR or WAR file |
| `verify` | Performs checks on the package |
| `install` | Stores the built artifact in the local repository |
| `deploy` | Publishes the artifact to a configured remote repository |

### Maven Life Cycle Flow

```text
validate
   |
compile
   |
test
   |
package
   |
verify
   |
install
   |
deploy
```

**Important:** When you run a later phase, Maven normally executes the earlier phases in the same life cycle first.

### Example

```bash
mvn compile
```

Compiles the main Java source code after running the required earlier phases.

```bash
mvn test
```

Compiles the code and runs the tests.

```bash
mvn package
```

Runs the required earlier phases and creates the application package.

```bash
mvn install
```

Builds the project and installs the artifact into the local repository.

```bash
mvn deploy
```

Builds and publishes the artifact to a configured remote repository.

### Other Maven Life Cycles

**Clean Life Cycle**

Removes previous build output.

```bash
mvn clean
```

**Default Life Cycle**

Builds, tests, and packages the application.

```bash
mvn package
```

**Site Life Cycle**

Generates project documentation when the required plugins and configuration are available.

```bash
mvn site
```

### Commonly Used Command

```bash
mvn clean package
```

This command removes previous build output and then builds and packages the project.

---

## 7. How to Change the Default Maven Repository Location

By default, Maven stores downloaded dependencies in the `.m2/repository` directory.

### Default Location

Linux:

```bash
~/.m2/repository
```

Windows:

```text
%USERPROFILE%\.m2\repository
```

We can change the local repository location using the `settings.xml` file.

### Step 1: Create the Maven Settings Directory

On Linux:

```bash
mkdir -p ~/.m2
```

### Step 2: Open the settings.xml File

```bash
vi ~/.m2/settings.xml
```

### Step 3: Add the Configuration

Example:

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.2.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.2.0
          https://maven.apache.org/xsd/settings-1.2.0.xsd">

    <localRepository>/opt/maven-repository</localRepository>

</settings>
```

Here, `/opt/maven-repository` is the new local repository location.

You can replace it with your preferred directory.

**Important:** Make sure your user has permission to write to the new directory.

### Step 4: Verify the Configuration

Run:

```bash
mvn help:effective-settings
```

Check the effective settings to confirm the configured local repository location.

**Remember:** Changing the local repository location changes where Maven stores downloaded dependencies. It does not change your project folder location.

---

## 8. Important Maven Commands

| Command | Purpose |
|---|---|
| `mvn -version` | Displays Maven and Java versions |
| `mvn validate` | Validates the project |
| `mvn compile` | Compiles Java source code |
| `mvn test` | Runs tests |
| `mvn package` | Creates the application package |
| `mvn clean` | Removes previous build output |
| `mvn clean package` | Cleans and packages the project |
| `mvn install` | Installs the artifact in the local repository |
| `mvn deploy` | Publishes the artifact to a remote repository |
| `mvn dependency:tree` | Displays project dependencies |
| `mvn help:effective-settings` | Displays effective Maven settings |

---

# 9. Maven Interview Questions and Answers

### 1. What is Maven?

Maven is a build automation tool mainly used for Java projects. It manages dependencies, compiles code, runs tests, and packages applications.

### 2. What is pom.xml?

`pom.xml` is the main configuration file of a Maven project. It contains project details, dependencies, and build settings.

### 3. What is a Maven repository?

A Maven repository stores libraries, plugins, and build artifacts used by Maven.

### 4. What are the types of Maven repositories?

There are three common types:

1. Local repository.
2. Central repository.
3. Remote repository.

### 5. What is the default local repository location?

On Linux, the default location is `~/.m2/repository`.

On Windows, it is `%USERPROFILE%\.m2\repository`.

### 6. What is a dependency in Maven?

A dependency is a library required by our project. Maven downloads it from a configured repository when needed.

### 7. What is a Maven build life cycle?

It is a sequence of phases Maven follows to validate, compile, test, package, and publish a project.

### 8. What is the difference between package and install?

`mvn package` creates the application package, such as a JAR file.

`mvn install` runs the required build phases and also stores the built artifact in the local repository.

### 9. What is the difference between clean and compile?

`mvn clean` removes previous build output.

`mvn compile` compiles the main Java source code.

### 10. What is JUnit?

JUnit is a Java testing framework used to write and execute automated tests.

### 11. What is the purpose of mvn test?

It compiles the test code and runs the configured automated tests.

### 12. What is the difference between JUnit and Selenium?

JUnit is commonly used for unit testing. Selenium automates browser interactions for web application testing.

### 13. What is a Maven plugin?

A Maven plugin provides additional build tasks, such as compiling code, running tests, and creating packages.

### 14. What is the target directory?

The `target` directory stores generated build output, such as compiled classes, test reports, and JAR files.

### 15. How can you check Maven dependencies?

Run:

```bash
mvn dependency:tree
```

### 16. How can you change the Maven local repository location?

Edit the `settings.xml` file and configure the `<localRepository>` element with the desired path.

### 17. What is the difference between a dependency and a plugin?

A dependency is a library used by the application or its tests. A plugin performs tasks during the build process.

### 18. Why do we use Maven in projects?

Maven simplifies dependency management, provides a standard project structure, and automates build and testing tasks.

---

## 10. Quick Revision

- **Maven:** Builds and manages Java projects.
- **pom.xml:** Maven project configuration file.
- **Repository:** Stores dependencies and build artifacts.
- **JUnit:** Java testing framework.
- **mvn test:** Runs tests.
- **mvn package:** Creates a JAR or WAR package.
- **mvn install:** Installs the artifact into the local repository.
- **mvn clean:** Removes previous build output.
- **Local repository:** Stores downloaded dependencies on the computer.
- **settings.xml:** Configures Maven settings, including the local repository path.

---

