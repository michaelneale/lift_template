<a href="https://grandcentral.cloudbees.com/#CB_clickstart=https://raw.github.com/CloudBees-community/lift_template/master/clickstart.json"><img src="https://s3.amazonaws.com/cloudbees-downloads/clickstart/clickstart-now.png"/></a>

# Scala and Lift ClickStart.

<img src="http://upload.wikimedia.org/wikipedia/commons/b/b7/Lift-logo.jpg"/>

The [Lift Framework](http://www.liftweb.net/) is a popular web framework for the [Scala](http://scala-lang.org) Programming language. 
It is known for being very secure out of the box (notably so for preventing XSS flaws), scalable, and mature. 
It is handy for ajax/comet applications, and is "full stack" (ie comes with pretty much everything you need, including ORM). Lift is in use by some large public sites such as [Foursquare](http://www.foursquare.com). 

<img src="http://upload.wikimedia.org/wikipedia/en/8/85/Scala_logo.png"/>


This ClickStart bootstraps you with a working lift web application, a database, a source repository 
(populated, ready to go), a Jenkins build service running continuous deployment from the source repository
(push a change, your project will be built and deployed). 

You can use this as a starting point for your own lift application 
(remember the source repository will be private to your account). 

Click here to launch this right now.

Feel free to fork and make this your own - pull requests welcome !



## Manual steps for deploying on CloudBees:

Create application:

    bees app:create MYAPP_ID

Create database:

    bees db:create -u DB_USER -p DB_PASSWORD DBNAME

Bind database as datasource:

    bees app:bind -db DBNAME -a MYAPP_ID -as LiftDB

Create a new maven project in Jenkins, changing the following:

* Add this git repository (or yours with this code) on Jenkins
* Also check "Deploy to CloudBees" with those parameters:

        Applications: First Match
        Application Id: MYAPP_ID
        Filename Pattern: target/*.war

## To build this locally:

In the lift_template directory, open a command line, and invoke maven by typing "mvn package" to build the war file, then deploy it on cloudbees typing:
	
    bees app:deploy -a MYAPP_ID target/*.war

## To run this locally:

Modify the src/main/scala/bootstrap/liftweb/Boot.scala file by commenting the following line:

    DefaultConnectionIdentifier.jndiName = "jdbc/LiftDB"

And uncommenting the following:

    val vendor = new StandardDBVendor(Props.get("db.driver") openOr "org.h2.Driver", 
    Props.get("db.url") openOr "jdbc:h2:lift_proto.db;AUTO_SERVER=TRUE",
    Props.get("db.user"), Props.get("db.password"))
    LiftRules.unloadHooks.append(vendor.closeAllConnections_! _)
    DB.defineConnectionManager(DefaultConnectionIdentifier, vendor)

Then finally run with jetty type "sbt update ~jetty-run" in the project directory, and then browse to localhost:8080