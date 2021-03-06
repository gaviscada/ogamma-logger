Known issues.
=============

1. When InfluxDB time-series data is used to store data, and value in the field ``Database specific settings/json``, property ``precision`` value is set to either ``us`` (for microsecond) or ``ns`` (for nanosecond), data is written, but cannot be read back. It seems that the issue is on InfluxDb side: it works fine in the instance of InfluxDB hosted by InfluxData, but does not work with Docker image version quay.io/influxdb/influxdb:2.0.0-beta.
2. When ``Refresh Data`` field in the ``Logged Variables`` table is set to automatic refresh mode, it is not possible to edit records in that table. Workaround is to turn data refreshing off before editing of records.
3. Table ``Logged Variables`` has too many columns and not all of them fit well into the screen, and horizontal scrolling is not available, which causes problem viewing/editing them. Workaround: use ``Column Chooser`` button in the right top corner of the table and select columns which need to be visible or hidden (usually not all columns need to be visible).
4. When OPC UA Server connection settings or logged variables settings are changed, in order to apply them, connections to all OPC UA Servers are closed and re-opened, subscriptions and monitored items are re-created. When an application instance has large number of server connections and logged variables, this might be inconvenient.
5. When communication with the time-series database is slow or broken, and there are Grafana sessions querying data via *ogamma* Visual Logger's REST endpoint, this can cause issues with configuration GUI responsiveness. As a workaround, separate instance can be created to serve REST endpoint, or number of threads in the same instance can be increased (option ``Web Server Settings / Number of threads`` in the ``Instance Settings`` dialog window).
6. Reading of historical data from Apache Kafka type database (at processing of Grafana requests) can be slow when within the requested time range there are large number of records for variables with the same topic and partition as topic and partition for the variable for which data is being requested. The reason for this is because although they might have different keys, it is not possible to filter records by a key value, they are filtered in Kafka broker only by topic name and partition. So records for multiple keys are read, and then from them records with desired key value are selected, which takes considerable time.

Release History.
================

Version 1.2.1 2020-Sep 7
-------------------------

* Fixed issue: The field ``Key Name Generation Mode`` is not visible when it should be according to the selected TSDB type in the Time-Series database configuration dialog window.
* Modified how default key value is generated for TSDB type ``Apache Kafka``: removed part ``n=`` from it.
* For TSDB types ``InfluxDB 2.0`` and ``InfluxDB 1.7`` added new option in JSON field: ``numberOfConnections``, to increase performance of writing values to the target database. As a result, now it is possible to write 100,000 values per second and more to the instance of InfluxDB database hosted in cloud, from instance of ogamma Visual Logger for OPC running in local netwrork.
* For TSDS of InfluxDB type (all versions) improved handling of communication errors and fixed issue with invalid timestamps (which was causing InfluxDB return error 400, Bad Request).
* Minor change in the Login dialog window: now pressing Enter key when either Login or Password field is in focus is equivalent to clicking on the Login button.
* Added new feature to display some statistical data about application performance, accessible via menu ``Tools / Statistics``. 
* Minor improvement in the dialog window ``Time-sereis Database configuration Settings``: when a new record is being edited, and in the field ``Type`` database type is selected, other fields are assigned default values, so no need to click on the button ``Reset to defaults``.
* For MQTT type databases, disabled using of the option ``persistType`` in the JSON field. As a result, the same in-memory buffer meachanizm is used for this type of database too, as for others.
* For Apache Kafka type databases, into the Json field added option ``Producer /  message.send.max.retries`` with default value 0, to eliminate retries by the underlying library (retries can be handled at the application level).
* Fixed issue: when time-series database type is ``Apache Kafka``, values for the field ``partition`` in the ``Logged Variables`` table are not read correctly after editing them.
* For InxluxDB 2.0 type database now connection token value can be saved in the ``password`` field, in encrypted format. Before it was saved in the Json field's option ``token``.
 
Version 1.2.0 2020-Aug-04
-------------------------

* Added support to publish data to MQTT Broker. Verified with the following MQTT Brokers:
  * Eclipse Mosquitto
  * Microsoft Azure IoT Hub
  * AWS IoT Broker
  * Google Cloud IoT Core MQTT Bridge.

* Modified logic on connections to SQL family of TSDB databases: now if an attempt to check if the database exists and/or create it fails, this error is ignored. This allows us to connect with a user account with restricted permissions.
* Upgraded to use the newer version of the licensing library. As a result, installations with the older versions will require reactivation of the license key. The same keys should be re-used. 

Version 1.1.1 2020-Jun-22
-------------------------

* Added support to store data in MemSQL database (uses the same client library as MySQL, with connection settings specific for MemSQL). 
* Added new feature: now all children variables from the node in the address space tree panel can be added using context menu. Note that the parent node should be expanded first.
* Improved performance on adding large number of variables from the address space.
* Fixed connectivity issue happening when OPC UA messages are large and split into chunks. One use case when it was happening is when number of logged variables per server connection exceeds 1000 or more tags.
* Fixed re-connection issue (in some cases was not able to establish connection to OPC UA Server after communication failure).


Version 1.1.0 2020-Jun-06
-------------------------

* Added support to store data in Microsoft SQL as a time-series database.
* Added support to store data in MySQL as a time-series database.
* Added support to store data in SQLite as a time-series database.
* Default configuration database type and time-series database type in all distribution packages (Windows, Ubuntu, Docker) is set to SQLite type, so the instance is ready to use immediately without installing of additional database components.
* Improved handling of connection interruptions with the time-series database of PostgreSQL/TimescaleDB type.
* Fixed bug: in the case when a time-series database type is PostgreSQL, data values are written twice (was introduced in version 0.8.1).

Version 1.0.1 2020-May-13
-------------------------

* Fixed issue: ActivateSession call fails at attempt to connect to KepServerEX, if connection security mode none-secured is used in combination with username/password type of user identity token.
* Fixed issue: Failes to connect to the configuration database of type PostgreSQL in encrypted mode;
* Fixed issue: Failes to connect to the time-series database of type PostgreSQL(TimescaleDB) in encrypted mode;
* Significantly improved performance of writing values to the time-series database when it's type is ``PostgreSQL``. As a result, now TimescaleDB/PostgreSQL database can be located not only in the local network, but also in the cloud too.
* Added feature to download application instance certificate as well as CA certificates and CRL via menu Settings / Download Certificate.
* Fixed issue: writing values to TimescaleDB/PostgreSQL or Apache Kafka might fail if data value has source timestamp not set or status code is bad.
* Licensing conditions for Community Edition changed: 
  * now on instances of the application which are activated after May 14, 2020 maximum number of logged variables is reduced to 64 after end of 1 month trial period. Existing installations activated before May 14, 2020 have limit as it was before: up to 256 variables.
  * periodic license validation with connection to the license server over the Internet is required. The date when the license was last time validated and the date before which the next validation should be performed are displayed in the dialog window opened via menu License / Status.
 

Version 1.0.0 2020-Apr-29
-------------------------

* First production release.
* Role ``Collector Agent`` now can be disabled/enabled in dialog window used to edit application instances settings.  
* Fixed issue: If real time value of a variable is empty, it is displayed in Logged Variables table, column "value" as value "true" of boolean type.

Version 0.9.1 2020-Apr-27
-------------------------

* Improved offline license activation workflow: now generated offline activation file can be downloaded via configuration GUI, and licence file can be uploaded via GUI too.
* Optimized license re-activation: now after entering activation key GUI is adjusted accordingly to expected action: activation or re-activation.
* Fixed error ``index out of range error`` reported by logging subsystem.
* Modifications in licensing to support Standard, Enterprise and Academic editions.
* In docker-compose.yml file added one more container: ``portainer/portainer``, the tool with web GUI to manage Docker environments. Portainer GUI is available at port 9000.
* Added support to pass name of configuration file in environment variable ``OVL_CONFIG_FILE``, which simplifies running multiple docker containers with *ogamma* Visual Logger for OPC.
* Added column ``Log to TSDB`` into the table ``Variable Groups``, for group of variables which should not be logged into time-series database by default. The aim is to simplify creation of records in the ``Logged Variables`` table which are used to serve queries from Grafana to read real time data directly from OPC UA Server.
* Adjusted feature ``Refresh Data`` to display last read values for variables, which are not logged into TSDB. (They are read directly from OPC UA Server to fulfill queries from Grafana).
* Rebuilt with newer version of the OPC UA SDK, with the following change: Message sequence numbers start from 1 after disconnection (to solve issue with CodeSys OPC UA Server).
* Fixed issue "ActivateSession request might fail when connections to multiple OPC UA Servers are created, due to using wrong identity token policy id".
* Fixed issue with high (~ 10% at low load) CPU usage.

Version 0.9.0 2020-Apr-20
-------------------------

* More memory leaks fixed.
* Added support for https.
* Passwords in settings, used to connect to the configuration database or to time-series databases, now stored in encrypted fromat.
* Added user authentication feature to access configuration GUI, using simple built-in identity provider.
* Some cosmetic changes in GUI (added button to change width of the Address Space panel, separated buttons used to change OPC UA Server connection settings from Log button, added icons to main menu items).

Version 0.8.7 2020-Mar-30
-------------------------

* Fixed issue: connection to the OPC UA Server is not restored after network connectivity issue.
* Fixed issue: when connection with OPC UA server is lost, ``Status`` column values in the ``Logged Variables`` table aren't changed to ``Bad``.
* Fixed issue: memory leak (was caused by in-memory debug logging accidentally left turned on).

Version 0.8.6 2020-Mar-21
-------------------------

* Fixed issue: in some cases size of sent OPC UA message chunks exceeds negotiated limit, which causes closing of connection by the server side.
* Fixed issue: if target OPC UA server has multiple endpoints with UserName type Identity token, wrong security policy can be selected for password encryption, which causes failure of ActivateSessoin call with "User Identity token invalid" error.
* Fixed issue: fields in the OPC UA applicaton instance certificate does not match with ApplicationDescription structure sent to the server on CreateSession request, which causes connection failure if the server is configured to reject such requests.
* Fixed issue: if server returns in FindServers or GetEndpoints response endpoint URL with IP address different than in the original request and which is not accessible from network where ogamma Visual Logger instance is running, connection is not possible. This might happen for example if UA Server is accessed via VPN, or running in Docker container with IP address which is not accesible from external machines. To fix this issue, in-accessible IP address in the endpoint URL is substituted by original request's IP address. If server returns host name, connection is established too.
* Default value for number of threads in web server increased from 1 to 4, for better responsiveness.
* Fixed issue: application crashes when OPC UA Server or TSDB or Instance seetings are changed.
* Using newer version of the OPC UA Client SDK, with fixes for crash and deadlock issues. Also it uses larger OPC UA message chunk sizes by default.
* Revised and added more log messages for easier troubleshooting and diagnosys of possible issues.
* Earliest date for start of the 1 month trial period with enabled Enterprise Edition features in Community Edition of the product is moved from April 1, 2020 to May 1, 2020.
* Added experimental support for writing of values to OPC UA Server (by changing values in the ``Value`` field in the ``Logged Varaibles`` table).
* Fixed issue: if Apache Kafka is used as TSDB, values for fields ``Topic Name Generation Mode`` and ``Key Name Generation Mode`` set in the GUI ignored, instead values set in the ``Database specific settings / Json `` are used. Now values set in the Json fieid will be ignored.
* Fixed issue: when Apache Kafka is used as TSDB by the instance, saving changes of that TSDB record settings causes application crash.
* In the ``Logged Variables`` table value of the Refresh Data selector widget now is saved between browser sessions. 
* Fixed issue: Ubuntu version crashes due to incorrect using of licensing library.

Version 0.8.5 2020-Mar-05
-------------------------

* Fixed issue: application log file size grows greater than set by the max. file size option.

Version 0.8.4 2020-Mar-03
-------------------------

* Fixed issue: when there are multiple OPC UA Server connections configured in the address space, and there are multiple variable groups  with variables, and all of them are collapsed, and when group selection is changed, this causes infinite calls from browser to the backend. Can also cause application crash.
* Fixed issue: upgrade from older versions (prior 0.8.1) (Ubuntu and Docker versions) with configuration database of SQLite type does not completely upgrade tables, and does not report error.
* Upgraded some dependencies (web framework and ODB library) to newer versions.
* Docker image changed to stop container when application process stops.
* To support upgrades from older versions, when InfluxdB settings are loaded from ./data/config.json file, its mapping settings configured to be the same as in older versions (i.e. ``measurement`` will be set to ``d`` and tag will be set to ``n=[variable id]``).


Version 0.8.3 2020-Feb-29
-------------------------


* Fixed issue: Application crashes at attempt to activate or re-activate license.
* Fixed issue: Instance configuration changes sometimes not saved or not applied without restart.
* To be consistent with older versions, for InfluxDb modified values set by button ``Reset to defaults`` to the same settings as in versions prior 0.8.1 (i.e. ``measurement`` will be set to ``d`` and tag will be set to ``n=[variable id]``).
* Improvements in logging messages formatting and runtime error handling.
* Ubuntu executable binary is built without debug info to reduce size.

Version 0.8.2 2020-Feb-26
-------------------------

* Web server port number and number of threads now can be changed at runtime via menu ``Settings/Instances``.
* Fixed issue: application crashes if configuration database type is changed at runtime.
* Fixed issue: If more than one Collector Configuration is used, then in Grafana SimpleJson tag selector all variables from all configurations are displayed, while only those which belong to the configuration assigned to the instance should be displayed.
* Modified column names in Logged Variables table to match with time-series database type.
* Fixed issue: Column ``Status`` in ``Logged Variables`` table is writable, should be read only.
* Added text field displaying current instance name and ID.
* Fixed issue: in the Instances table link to open the instance page was not correct.

Version 0.8.1 2020-Feb 23
-------------------------

* Introduced limitations for Community Edition, which come into effect after 1 month since license activation, but not earlier than May 1, 2020. Refer to the User Manual for details.
* All configuration settings now can be modified via web GUI. Most changes are applied at runtime, without restart of the application.
* Added concept of Instances. Multiple instances of the application, running in the same machine or on different machines in the same network, will be manageable from single page. In this release database has support for this, but implementation is not complete yet. Refer to User Manual for details.
* Some default values for configuration settings are changed. For example, now log file and PKI (certificates) locations are changed:  their new location is in sub-folder of ./data folder, with unique name, which allows to run multiple parallel instances with the same work folder.
* For easier management of variables, added concept of grouping using Variable Groups. Variable Groups have global scope and available from all instances and OPC UA Server connections. They define default data collection parameters such as sampling and publishing interval. When variables are added from OPC UA address space, currently active group's settings are assigned to them.
* Added support to select multiple nodes from the address space and add them to the Logged Variables table.
* Added support for sorting by different columns in the Logged Variables table.
* InfluxDB measurements and tags now configurable via web GUI (columns Topic Name and Key Name). Default values can be generated automatically in different configurable ways.
* Log level changes applied at runtime without restart.
* Added feature to display real time value/status/timestamp for OPC UA variables in the Logged Variables table.
* Fixed few runtime crash issues.
* Added support for browsing of OPC UA Servers address space in cases when server does not return all references in one call, now consequitive BrowseNext requests are made and all references can be retrieved.
* Added support to store non-numeric data type values as strings in InfluxDB and Apache Kafka.
* Implemented support for microsecond resolution for InfluxDB.
* JavaScript files minified.

Version 0.7.1 2020-Jan-16
-------------------------

* Fixed issue of building address space tree in GUI, occurring when the same node is referred from multiple parent nodes.

Version 0.7.0 2020-Jan-12
-------------------------

* Added support for licensing. Now application requires activation using license key, which can be obtained for Community Edition using One-Way Automation on-line store at https://onewayautomation.com/online-store. License can be activated via Web GUI (menu `Setup/License`).
* Improved error handling.
* Changes in configuration file, related to time-series database connections. now appied without re-start, at runtime.
* Fixed issue "Log files cannot be accessed via Web GUI on Windows".
* Fixed issue "Reading data from InluxDB to Grafana fails".
* In the Logged Variables grid added feature `Column Chooser` to show or hide columns.
* Added link to Release Notes into the `Help and Links` menu.
* Added link to open configuration settings into `Setup` menu (currently not enabled though).
* Improved handling of temporary disconnections from time-series databases: now failed write batch is kept in the in-memory queue and written when connection is restored, eliminating data lost. If time-series database is not available at start time, data also cached in memory buffer.


Version 0.6.1 2020-Jan-01
-------------------------
* Maintenance release. Fixed runtime error issue (occurring when some fields in the Logged Variables table are edited).

Version 0.6.0 2019-Dec-28
-------------------------

* Added support for Apache Kafka.   
  Tested with different Kafka clusters:
  * Cloud instances, managed by https://www.confluent.io/confluent-cloud and running at following providers:
    * Amazon Web Services;
    * Microsoft Azure;
    * Google Cloud Platform. 
  * Running locally in Docker container (image https://hub.docker.com/r/bitnami/kafka)
* Parsing of json configuration file modified: now single line and muti-line comments are allowed.
* Windows version is now built with newer version of Visual Studio (2019), therefore version 2019 of Visual C++ redistributables are required to be installed.
* Newer version of dependency libraries are used.
* Size of log files is reduceed from 16 Mb to 8 Mb, for faster downloading with Internet browser.

Version 0.5.2 2019-Nov-09
-------------------------

* Added support for UserName identity token type on connections to OPC UA Servers;
* Added support for OPC UA deadband, so now data changes can be reported only when change of the value is greater than defined by deadband settings. 
* Re-build with the latest version of the OPC UA SDK, so now it can connect to OPC UA Servers running within containers, when Visual Logger is running outside of container, using host machines' name in endpoint URL.

Version 0.5.1 2019-Nov-06
-------------------------

Fixed issue: was not connecting to InfluxDB in secured mode (https://github.com/onewayautomation/ogamma-logger/issues/6)

Version 0.5.0 2019-Nov-02
-------------------------

* Added support for InfluxDB database (versions 1.7 and 2.0, https://www.influxdata.com/) (GitHub issue https://github.com/onewayautomation/ogamma-logger/issues/5)
* Fixed bug: After restart, tries re-create TSDB database, but fails and exits, if OS language is not English (GitHub issue https://github.com/onewayautomation/ogamma-logger/issues/4)
* Web framework upgraded to the newest version;

Version 0.4.2, 25 Aug, 2019
---------------------------

* Re-built with newer versions of dependencies;
* Added distribution package for Debian Stretch;
* Updated User Manual (available at https://onewayautomation.com/visual-logger-docs/html/)
