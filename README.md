# Gradle 3.x Fundamentals

## Resources

Gradle:

* [Gradle Build Language / DSL Reference](https://docs.gradle.org/current/dsl/)
* [Gradle User Guide](https://docs.gradle.org/current/userguide/userguide.html)
* [Gradle Javadoc](https://docs.gradle.org/current/javadoc/)

Groovy:

* [Groovy Documentation](http://groovy-lang.org/documentation.html)
* [Groovy Style Guide](http://groovy-lang.org/style-guide.html)
* [Groovy API](http://groovy-lang.org/api.html)

## Basic commands

* `gradle help` - Print basic help information
* `gradle -?` - Print command line options
* `gradle tasks` - Print the available tasks in a project

Hint: Prefer the gradle wrapper `gradlew` over `gradle` command:

* `gradle build` - Requires the correct Gradle version to be installed, calls build
* `./gradlew build` - Downloads correct Gradle version if necessary, then calls build

Generate gradlew scripts (project setup):

```bash
gradle wrapper --gradle-version=3.1
```

Scan builds (needs the [script](https://scans.gradle.com/get-started) `~/.gradle/init.d/buildScan.gradle`):

```bash
gradle build -Dscan
```

Build daemon:

* `gradle build` - Starts gradle build with daemon (default)
* `gradle --no-daemon build` - Starts gradle build without daemon
* `gradle --status` - Shows daemon processes
* `gradle --stop` - Stops daemon processes

## [Build scripts](https://docs.gradle.org/current/userguide/tutorial_using_tasks.html)

* `build.gradle` - Build script (by convention)
* `settings.gradle` - Controls many aspects of the build

Functionality is added via plugins (which contains  tasks):

```groovy
apply plugin: "java"
apply plugin: "idea"
apply plugin: "maven"
```

## [Properties](https://docs.gradle.org/current/userguide/build_environment.html)

System properties:

```groovy
// gradle hello -Ddisable.hello

task hello << {
  println "hello"
}

if (System.properties['disable.hello']) {
  tasks.hello.enabled = false
}
```

Project properties:

```groovy
// gradle hello -Pdisable.hello

task hello << {
  println "hello"
}

if (/*project.*/hasProperty('disable.hello')) {
  tasks.hello.enabled = false
}
```

## [Tasks](https://docs.gradle.org/current/userguide/more_about_tasks.html)

The build may contain 'ad-hoc' tasks:

```groovy
task name { // <-- initializer

  // executed in initialization phase
  property_1 value_1
  property_n = value_n

  doFirst { // <-- deferred code / lambda
    // executed in execution phase
  }
  doLast {
    // ...
  }
}
```

Syntactic sugar (without doFirst):

```groovy
task name(property_1: value_1, property_n: value_n) << {
  // doLast code here
}
```

Run a task:

```bash
gradle name
```

Shortcuts for camel-case names are possible, e.g. `sOT` or `sOtherT` for `someOtherTask`.

Task Types (examples: [Task](https://docs.gradle.org/current/javadoc/org/gradle/api/Task.html) and [Copy](https://docs.gradle.org/current/javadoc/org/gradle/api/tasks/Copy.html)):

```groovy
task copyFiles(type: Copy) {

  // Task (default for every task)
  onlyIf { System.getProperty("be.nice") == "true" }

  // Copy (declared above)
  from "sourceDir"
  into "targetDir"

}
```

Depending tasks:

```groovy
task foo

task bar {
  dependsOn foo
}
```

## [Build lifecycle](https://docs.gradle.org/current/userguide/build_lifecycle.html)

* **Initialization Phase**
  * Configure environment (init.gradle, gradle.properties)
  * Find projects and build scripts (settings.gradle)
* **Configuration Phase**
  * Evaluate all build scripts
  * Build object model (Gradle -> Project -> Task, etc.)
  * Build task execution graph
* **Execution Phase**
  * Execute (subset of) tasks
