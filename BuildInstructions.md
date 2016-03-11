Building spring4gwt from source requires Subversion 1.5+, Java 5+, Ant 1.7.1+, GWT 1.6.4+. An environment variable for GWT\_HOME must also be defined and reference
a valid GWT install location.

To build spring4gwt and/or the example projects from source, first check out the project from svn:

svn checkout http://spring4gwt.googlecode.com/svn/trunk/ spring4gwt

Next define GWT\_HOME, e.g. export GWT\_HOME="C:\gwt-windows-1.6.4".

To build the spring4gwt library and all examples run "ant" in the project directory. The gwt4spring library jar will be produced in build/dist. Example projects are contained in examples/ and will produce runnable wars the build/dist directory of each. The examples have been tested on Tomcat 6 and Jetty 6 but are very simple and should work in any container.

If desired, after building the project you may run all of the example applications locally on jetty from ant by running "ant jetty". The examples will be deployed at http://localhost:8080/.

To build only the spring4gwt library without examples run "ant core".

In addition to spring4gwt.jar, the project's lib/ directory contains third-party libraries used by spring4gwt and its example projects. Spring4gwt is intentionally very lightweight so that it will work in as many environments as possible. At a minimum, you'll need log4j,
gwt-user, gwt-servlet, spring-2.5+, and commons-logging (the only Spring dependency needed by spring4gwt, though your Spring project will likely need more!). You may copy them from the lib/ directory or use versions already present in your project. Those included have their versions labeled to help expose any conflicts.

See the example projects and wiki pages for step-by-step instructions on using spring4gwt inside various types of projects.