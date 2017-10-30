IMS/DB CDC on z/OS Platforms
============================

This section describes the Attunity IMS/DB CDC agent. It includes the
following topics:

Overview

Functionality

Configuration Properties

Change Metadata

Transaction Support

Security

Data Types

Configuring the DFSFLGX0 Exit

Setting-up the IMS/DB CDC Agent in Attunity Studio

Troubleshooting

Overview
--------

The Attunity Stream CDC solution for IMS/DB captures changed IMS/DB
segments that are passed to the DFSFLGX0 (LOGWRT) IMS user exit, and
saves them in an MVS logstream. The IMS/DB CDC Agent polls the logstream
for the changes.

Attunity supports two IMS LOGWRT exit modules in the &lt;HLQ&gt;.LOAD
library: DFSFLGXS and DFSFLGX0. DFSFLGXS should be used with IMS/DB
across multiple ZOS LPARS. Otherwise, DFSFLGX0 should be used.

Creating a CDC solution for IMS/DB is executed in Attunity Studio.

A staging area is used, enabling the elimination of uncommitted changes
from being captured, and to reduce the number of the change events
generated. For more information, see The Staging Area.

Functionality
-------------

The Attunity Stream CDC IMS/DB Batch solution uses its own DFSFLGX0
IMS/DB exit routine for capturing the IMS/DB changes. If another
DFSFLGX0 user exit is already used, the Attunity CDC solution cannot be
implemented. The IMS/DB CDC agent supports the basic functionality for
all CDC agents.

### Supported Versions

See the customer support section at www.attunity.com for the currently
supported

IMS/DB versions.

Configuration Properties
------------------------

This section describes the configuration properties for the IMS/DB CDC
agent. You can edit the properties using Attunity Studio. The IMS/DB CDC
has the following types of properties:

-   CDC Logger Properties
-   CDC\$PARM Properties
-   CDC Agent Properties

### CDC Logger Properties

Logger Name: the name of the MVS logstream used for the data capture.

### CDC\$PARM Properties

member that contains the parameters for a DFSFLGX0 user exit. For an explanation on how to create this and its syntax see Creating and Configuring the CDC\$PARM Data Set.

The following list describes the CDC\$PARM properties:

BUFFER\_NUM: The logstream buffer number. The valid values are Default-30.

BUFFER\_SIZE: The logstream buffer size. The valid values are Default-22550 bytes.

DEBUG: If this is ON the debug information is printed using WTO. The default value is OFF.

LOGSTREAM: The logstream name. The default value for the DFSFLGX0 module is ATTUNITY.IMS.DCAPDATA. The default value for the DFSFLGXS module is ATTUNITY.IMS.DCAPSYST.

DATASHARING: Set this to ON when IMS is used in a SYSPLEX environment.
In this case, the MVS Logger defined using the LOGSTREAM property should
be created as the MVS System Logger on Coupling Facility. The default
value is OFF for DFSFLGX0 and ON for DFSFLGXS.

### CDC Agent Properties

To edit the CDC agent properties, open the Attunity Studio Design
perspective and find the binding for the CDC solution you created. The
binding contains the name of the CDC solution with the suffix \_ag added
to it. Open the adapter with the name of the solution and the suffix
\_ag to edit the properties. For information on how to edit adapter
properties in Attunity Studio, see Configuring Application Adapters.

The agent properties described below are configured if you want the
CHECKPOINT to be sent to IMS/DB instance. If the checkpoint is not
configured, the changes may be captured by DFSFLGX0 exit with a delay,
if the IMS/DB Control Region executes a small amount of updates.

There are also additional properties that are common to all Attunity CDC
agents. For a description of the common Attunity CDC agent properties,
see Common CDC Agent Properties.

- envImsBatch: When this property is set to true (the default), the IMS/DB
CHECKPOINT command will not be issued. Set this property to false if you
want the last changes in the IMS/DB REGION to be written to the MVS
Logger. In this case, the agent sends the IMS/DB CHECKPOINT command
using MCS or by replying to an IMS/DB DFS996I message.

- checkPointFrequency: The frequency for issuing checkpoints. The default
value is 60 (seconds). The smallest time frequency supported is 10
seconds.

- consoleCheckPoint: Set to true to use and extended MCS console. If false
a reply to WTOR is used. The default value is true.

- imsJobName: IMS job name for the IMS/DB for which the WTOR reply to the
message DFS996I should be sent. This must be provided if be provided if
more than one IMS/DB instances run on a Z/OS box.

- consoleCheckPointCommand: The command that should be sent to the MCS
console. The default value is "/CHE."

- returnLastContextOnIdle: If true, the precise machine timestamp is set
as the last context if no relevant updates have occurred.

Change Metadata
---------------

Changes are captured and maintained in a change table. The table
contains the original table columns and CDC header columns. The header
columns are described in the following table:

---------------------------------------------

Table 71–1 Header Columns

|Column Name |Description                                                  |
|---------------|----------------------------------------------------------|
|context        | The record’s current context.                            |
|operation      | This column lists the operations available for the CDC agent.|
|               | The available operations are:                            |
|               | INSERT                                                   |
|               | DELETE                                                   |
|               | UPDATE                                                   |
|               | BEFOREIMAGE                                              |
|               | COMMIT                                                   |
|               | ROLLBACK                                                 |
|               |                                                          |
| transactionID |  The operation’s transaction ID.                         |
| tableName     |  The name of the table where the change was made.        |
|               |  For INSERT, UPDATE, and BEFOREIMAGE operations, the owner name and then the table name are displayed.                                |
|               |   For COMMIT and ROLLBACK operations, this value is the same as the OPERATION value.                                                    |
|            |                                                             |
|timestamp   | The date and time of the occurrence.                        |


Transaction Support
-------------------

IMS/DB CDC supports transactions within IMS/DB transaction boundaries.
However, no compensating records are available in the log in case of
rollback.

Security
--------

The IMS/DB CDC adapter connects to the MVS logstream with an
authorization level of READ. The DFSFLGX0 user exit connects to the
logstream with an authorization level of WRITE. To determine the proper
security authorizations see the MVS Auth Assm Services Reference ENF-IXG
IBM manual.

***
>Notes:

>>To access a logstream in an application with a READ authorization level,
set the READ access to RESOURCE(&lt;logstream name&gt;) in SAF class
CLASS(LOGSTRM).

>>To update a logstream in a program with a WRITE authorization level, set
the ALTER access to RESOURCE(&lt;logstream name&gt;) in SAF class
CLASS(LOGSTRM).
***


### Data Types

All data types supported by the IMS/DB data source are supported by the
IMS/DB CDC solution.

Configuring the DFSFLGX0 Exit
-----------------------------

To use the DFSFLGX0 exit, carry out the following procedures:

- MVS Logstream Creation
- Creating and Configuring the CDC\$PARM Data Set
- Update the IMS Environment
- Adjust the DBD for the Relevant Databases

### MVS Logstream Creation

A sample job for the creation of the DASD MVS logstream called
ATTUNITY.IMS.DCAPDATA is supplied in the &lt;HLQ&gt;.USERLIB(LOGCRIMS)
member. For additional information, see the MVS Setting Up a Sysplex IBM
manual.

### Managing the MVS Logstream

The ATYLOGR program that is provided is used to manage MVS logstreams.
It provides the following options:

- Delete all events
- Delete events to a specific timestamp
- Print events between two timestamps
- Print all events from the oldest to a selected timestamp
- Print all events from the newest to a selected timestamp
- Print all events

A sample job for managing MVS Logstreams called ATTUNITY.CDC.VSAMBTCH is
supplied in the &lt;HLQ&gt;.USERLIB(RUNLOGR) member.

Creating and Configuring the CDC\$PARM Data Set
-----------------------------------------------

The CDC\$PARM is the DD card name used for configuring the DFSFLGX0
exit. It can be any QSAM data set or member with the LRECL=80
definition. For example, you can build it as a member of the
&lt;HLQ&gt;.USERLIB library.

The data set contains parameters, one parameter on a line, according to
the follow syntax:

&lt;parameter name&gt;=&lt;parameter value&gt;

The parameters and their valid values are described in CDC\$PARM
Properties.

Update the IMS Environment
--------------------------

If you use IMS Data Sharing, copy the supplied DFSFLGXS exit module from
the Attunity supplied &lt;HLQ&gt;.LOAD library to the IMS SDFSRESL
library and rename it to DFSFLGX0. Otherwise, copy the supplied DFSFLGX0
exit module.

If you need to change the default value of any CDC\$PARM property, you
must update the IMS Environment as follows:

1\. Define the CDC\$PARM data set and provide the required properties.

2\. Add the CDC\$PARM DD card to the IMS Control Region and batch jobs.

3\. Restart the IMS Control Region.

4\. Adjust the DBD for the Relevant Databases

You must do the following to adjust the DBD for the relevant databases:

Adjust the DBD for each IMS/DB database that is included in your CDC
solution, defining the usage of DFSFLGX0 exit, by adding the following
parameter to the DBD macro:

EXIT= (\*, KEY, NOPATH, DATA, LOG, (CASCADE, KEY, NODATA, NOPATH))

Note: The value NODATA in the cascade above indicates that no data is
returned from non-key fields. If you delete a parent record, the child
records are also deleted, but the data is returned only for the key
field. In this case, the non-key fields for the child records are empty.

Recompile DBD and the corresponding PSB and ACB objects, then restart
the IMS Control Region.

### Setting-up the IMS/DB CDC Agent in Attunity Studio

You set-up the IMS/DB CDC agent by creating a CDC solution in the
Attunity Studio CDC Solution Perspective. Follow the directions for
Creating a CDC Solution with Attunity Studio. The IMS/DB agent
configuration uses the standard solution except for:

### Configuring the CDC Service

After you set up the IMS/DB CDC agent, follow the directions in Setting
the envlmsBatch Property. This is done in the Attunity Studio Design
perspective.

Before setting-up the IMS/DB CDC agent, make sure that:
- The IMS system and logstream are properly configured, as described in
Configuring the DFSFLGX0 Exit.
- The security measures are implemented, as described in Security.
- If you did not import the metadata while creating the CDC solution, see
Setting the envlmsBatch Property.

### Configuring the CDC Service

For configuring the IMS/DB CDC service, carry out the following procedure.

==========================

Note: When you set up an IMS/DB CDC solution, you must know what type of IMS data source you are using.

For more information on using the IMS/DB DLI data source, see Defining the IMS/DB DLI Data Source Connection.

For more information on using the IMS/DB DBCTL data source, see, Defining the IMS/DB DBCTL Data Source Connection

For more information on using the IMS/DB DBDC data source, see Configuring the IMS/DB DBDC Data Source

===========================

To configure the CDC Service

1.  In the CDC Solution perspective, click Implement.

2.  Click the CDC Service link.

> The CDC Service dialog box opens.
>
> -----------------
>
> Figure 71–1 CDC Logger Definition Window
>
> -----------------

1.  Under CDC Logging, select the CDC logging level:
  |  ---------------|
> |None           |
> |internalCalls  |
> |Info           |
> |Debug          |
> |API            |
> |               |

1.  Under Stream Position, select one of the following:

> now: The stream processor begins to process requests and return events
> from the time the stream position is set.
>
> manual: The stream processor is paused and waits until a statement is
> sent to the Control Table to begin processing requests and returning
> events.
>
> You can use the Attunity Query Tool to send the required query.
>
> If you are using an event-based CDC solution, see Setting the Stream
> Position for infomation on how to manually set the stream position.
>
> If you are using an SQL-based CDC solution, the information is found
> here:
>
> Setting the Stream Position.

1.  Under Change Capture Event Setting, select the Include a capture of
    before-image records check box if you want to include the before
    image information.

2.  Under Logger name, enter the Logger Name and specify the name for
    the logger, as entered in the IMS system fix 80 file. This is
    configured when Configuring the DFSFLGX0 Exit. the default name for
    the logger is ATTUNITY.IMS.DCAPDATA. If you changed the name when
    configuring IMS, then enter the new name in this field.

3.  Click Finish.

To set up the stream service, follow the instructions in Stream Service.

### Setting the envlmsBatch Property

If using IMS/DB DBDC or IMS/DB DBCTL data sources, then set the
envImsBatch agent property to False to receive the latest changes to the
data. Carry out this procedure in the Design perspective of Attunity
Studio. Before you begin, make sure you are in the Design perspective.

To set the envImsBatch property to False

1.  Expand the new binding created when you set up the IMS CDC solution
    and expand the Adapter, as shown in the following figure:

> ------------------------------------
>
> Figure 71–2 The Configuration view
>
> --------------------------------------

2.  Right-click the adapter for the change data capture, and select Edit
    Adapter.

3.  Select the Properties tab from the adapter editor.

4.  Change the value for the envImsBatch property to False, as shown in
    the following figure:

> ----------------------------------------
>
> Figure 71–3 The Adapter Properties tab
>
> ----------------------------------------

5.  Click the Save button in the toolbar to save the change.

### Working with Metadata

If you do not carry out a full import in the metadata as part of the CDC
solution, you must make sure that the capturedTable-&gt;dbdName
attribute is set explicitly in the table dbcommand if full import has
not been done. You do this using the following Nav\_Util
command:Nav\_Util edit table.

Edit the table, and set the dbdName attribute in the dbCommand element
for each captured table.

To import the metadata from another source, click the Metadata link when
Creating a CDC Solution with Attunity Studio. For more information on
importing IMS metadata, see Setting Up IMS/DB Metadata.

Troubleshooting
---------------

This section describes how to troubleshoot the IMS/CDC agent.

Review the following checklist:

Look for any errors in the IMS job.

Ensure a message similar to the following appears under the IMS:

DD JESYSMSG:

DFSFLGX0 Attunity CDC \*Active\*

Use the IMS RUNLOGR utility which is available at:

HLQ.USERLIB (RUNLOGR)

Where HLQ is the high-level qualifier where Attunity Server is
installed, as shown in the following example:

```
//RUNLOGR JOB 'RR','TTT',MSGLEVEL=(1,1),CLASS=A,
// MSGCLASS=X,NOTIFY=&SYSUID,REGION=8M
//\*
//LOGR EXEC PGM=ATYLOGR,
//\*PARM=('/DEBUG ATTUNITY.IMS.DCAPDATA MAXLEN 1024 ',
// PARM=('/NAME ATTUNITY.IMS.DCAPDATA MAXLEN 1024 ',
// 'PRINT FROM 2005-03-13,02:13:57 TO 2007-10-27,02:38:23')
//\* 'DELETE ALL')
//\* 'DELETE TO 2004-10-28,02:17:11')
//\* 'DELETE TO YOUNGEST')
//\* 'PRINT FROM 2003-12-23,22:07:16 TO 2004-10-27,02:38:23')
//\* 'PRINT FROM OLDEST TO 2007-10-27,02:38:23')
//\* 'PRINT FROM 2004-10-27,02:51:57 TO YOUNGEST')
//\* 'PRINT FROM OLDEST TO YOUNGEST')
//STEPLIB DD DISP=SHR,DSN=TEST.AC4800.LOADCDCY

```
To use RUNLOGR, un-comment the option you want to use and submit the
member. The following options are available:

-   Delete all events.
-   Delete events to a specific timestamp.
-   Delete the newest events.
-   Print all the events from the oldest to a specified timestamp.
-   Print events between two timestamps.
-   Print all the events from the newest to a specified timestamp.
-   Print all the events.


