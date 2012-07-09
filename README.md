# Lift Template for CloudBees.
## To deploy this on CloudBees, follow those steps:

Create application:

    bees app:create MYAPP_ID

Create database:

    bees db:create -u DB_USER -p DB_PASSWORD DBNAME

Bind database as datasource:

    bees app:bind -db DBNAME -a MYAPP_ID -as LiftDB

Create a new maven project in Jenkins, changing the following:

* Add this git repository (or yours with this code) on Jenkins
* Also check "Deploy to CloudBees" with those parameters:

        Application Id: MYAPP_ID
        Filename Pattern: target/*.war

## To build this locally:

In the lift_template directory, open a command line, and invoke maven by typing "mvn package" to build the war file, then deploy it on cloudbees typing:
	
    bees app:deploy -a MYAPP_ID target/*.war

## To run this locally:

Modify the scala/bootstrap/liftweb/Boot.scala file by commenting the following line:

    DefaultConnectionIdentifier.jndiName = "jdbc/LiftDB"

And uncommenting the following:

    val vendor = new StandardDBVendor(Props.get("db.driver") openOr "org.h2.Driver", 
    Props.get("db.url") openOr "jdbc:h2:lift_proto.db;AUTO_SERVER=TRUE",
    Props.get("db.user"), Props.get("db.password"))
    LiftRules.unloadHooks.append(vendor.closeAllConnections_! _)
    DB.defineConnectionManager(DefaultConnectionIdentifier, vendor)

Then finally run with jetty type "sbt update ~jetty-run" in the project directory, and then browse to localhost:8080