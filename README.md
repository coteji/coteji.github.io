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
@UserStories({"COT-110", "COT-115"}) // example of your annotation
public void deleteReminder(Reminder reminder) {
    NavigationSteps.openRemindersApp();
    ReminderSteps.addReminder(reminder);
    ReminderSteps.deleteLastReminder();
    CommonUiSteps.refreshPage();
    ReminderSteps.checkReminderIsAbsent(reminder);
}
```
And the following Coteji configuration script (`config.coteji.kts`):
```kotlin
@file:DependsOn("io.github.coteji:coteji-source-java:1.0")
@file:DependsOn("io.github.coteji:coteji-target-jira:1.0")
import io.github.coteji.sources.*
import io.github.coteji.targets.*

val source = JavaCodeSource(
        testsDir = "path/to/dir",
        isTest = method { withAnnotation("Test") },
        getId = methodAnnotation("TestCase"),
        lineTransform = separateByUpperCaseLetters
)

val target = JiraTarget(
        url = "https://your.jira.com",
        user = "service-user",
        password = "your_password",
        project = "COT",
        issueType = "Task"
)

setSource(source)
setTarget(target)

```
