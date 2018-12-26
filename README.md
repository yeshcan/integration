
Selenium Web Browser Automation 
===================

This example project demonstrates using the Selenium WebDriver web browser automation capabilities with Workday. 

Recommendations
-------------
Here are some suggestions for designing Workday automation.

#### Limit Automated Testing 
Workday provides DOM data-automation-* attributes in the Workday HTML code that can be used to anchor automated actions on to such as data-automation-id and data-automation-label. Even though this capability is available there are several reasons why it should be used sparingly:

- The HTML elements relationships between each other are subject to change as the UI evolves, especially when new UI technologies are implemented like the move from Adobe Flex to GWT. 
- Attempting to automate UI actions programmatically is time intensive and involves quite a bit of trial and error
- Workday has their own extensive battery of unit tests and quality assurance checkpoints and this is part of the value of the service. Automated UI testing is typically used to capture regression problems but this is not applicable for Workday since they own the UI and are constantly updating it.
- Ostensibly automated testing could aid in Workday release testing however the UI is guaranteed to change and having someone manually execute business critical tasks can lead to the discovery of new features to uptake and identify issues that automated tests may miss.

All of these factors extremely limit the return on investment for maintaining automated testing.  


#### Integration Testing
If the objective of automated testing is data entry for testing downstream integrations utilizing Workday web services is a much better approach. Workday has web services for most necessary tasks and these APIs are managed for backwards compatibility for several releases. If getting raw data into the tenant is the objective then creating streamlined business process definitions without manual intervention is straight forward. Simply create the minimal rule based BP definition, create a condition rule of "external load"="yes" and "performed by"="some ISU", and then add the rule to the business processes rule based configuration. Whenever a web service request comes into Workday by that ISU the special purpose BP will be utilized. 

####Repetitive Tasks only Available in the UI
This is the only use case where building automation makes sense. I have personally used automated scripts to delete errantly created calculated fields like in the example scenario below and to close duplicate credit card transactions that were accidentally loaded due to a custom credit card integration reference ID format change. Tediously clicking on dozens if not hundreds of items can be circumvented by using automation.

####Automation Abstraction
Follow the example provided in the automation.AutomationUtil.java class and build Workday action primitives that can be reused in automation tasks. Workday concepts such as global search, related actions, task execution, and prompt selection will probably not change in the near future but the underlying HTML will certainly fluctuate. Reusing a standard abstraction that mirrors common Workday user activity helps reduce maintenance and limits where breaking changes can occur.

Scenarios
-------------
All scenarios involve automated login into Workday. These scenarios were tested against a GMS tenant.

1. Integration Screenshot - Navigate to the Worker Payroll Input (Generic), display the Launch Integration related action, and take a screenshot.
2. XpressO Benefit Enrollment Count - Run a XPressO based report and download the result as an Excel file.
3. XpressO Integration Exception Audit - Run a XPressO based report and stream the table results for programmatic access.
4. Delete Unused Fields - Execute a task only available via the UI programmatically
5. Create Position - Demonstrate performing a UI task programmatically using form input

Running the Examples
-------------
[Apache Maven](http://maven.apache.org/download.cgi) and [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) are required to build and run the examples.

The tenant.properties configuration file is used to manage the execution of the examples. Update the properties file with the following information:

1. geckodriver.path or chromedriver.path = Selenium requires a W3C WebDriver executable be available on the path. This executable is responsible for launching the browser and providing a service for remotely accessing the browser. [Chrome Driver Download](https://sites.google.com/a/chromium.org/chromedriver/downloads) [FireFox Download](https://github.com/mozilla/geckodriver/releases)
2. workday.url - The URL of the Workday tenant one would like access. This is the browser URL, not to be confused with the web service URL.
2. workday.username - Workday login ID
3. workday.password - Workday login password
4. fileDownloadPath - Where to automatically download files to
5. mode - which example to run. Valid values are:
 - integrationScreenshot
 - xpressoEnrollmentCount
 - xpressoIntegrationAudit
 - deleteUnusedCalcFields
 - createPosition

 To execute an example run this command from the base directory:
``` 
mvn clean compile exec:java
```

