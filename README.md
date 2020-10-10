# coteji

### Tool for tests migration

Coteji is the tool that allows you to keep your test cases as a code and syncronize them with your Test Management System (JIRA, TestRail, etc.). So, for example, instead of creating and maintaining the same test both in the Java code and in JIRA, you just maintain it in the code and run Cotegi from time to time to have it up to date in JIRA.

## How it works
Coteji is the plug-in system. There are (will be) different implementations for tests sources (Java code, Python code, BDD files, etc.) and targets (JIRA, TestRail, etc.). You should be able to use any combination of the source and the target. Configuration is done using a Kotlin script file, and then you have the following options to run the syncronization:
- Command line tool
- Gradle/Maven plugin  

Let's say you have the following TestNG test:
```java
@Test(dataProvider = "deleteReminderData")
public void deleteReminder(Reminder reminder) {
    NavigationSteps.openRemindersApp();
    ReminderSteps.addReminder(reminder);
    ReminderSteps.deleteLastReminder();
    CommonUiSteps.refreshPage();
    ReminderSteps.checkReminderIsAbsent(reminder);
}
```
And the following Coteji configuration script:
```kotlin
@file:DependsOn("io.github.coteji:coteji-source-java:1.0")
@file:DependsOn("io.github.coteji:coteji-target-jira:1.0")
import io.github.coteji.sources.JavaCodeSource
import io.github.coteji.targets.JiraTarget

val source = JavaCodeSource(
        testsDir = "path/to/dir",
        isTest = method { withAnnotation("Test") }
        lineTransform = { line -> 
            if (a.contains(".")) {
                return a.split(".")[1]
                .split(Pattern.compile("(?=\\p{Lu})"))
                .joinToString(separator = " ")
                .capitalize()
                .replace("(", " [ ")
                .replace(");", " ]")
            }
            return line
        }
)

val target = JiraTarget()
setSource(source)
setTarget(JiraTarget())

```
