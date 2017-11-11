Pre-installation and Upgrade Notes
==================================

This section includes Attunity Server pre-installation requirements for
z/OS and upgrade notes.

What Steps are performed for an installation or an Upgrade
----------------------------------------------------------

Before starting the installation, make sure that you have the following
information available, which is prompted for during the installation:

-   The output class for the installation output if you don’t want to
    use the default.

    -   To enable access to DB2:

    -   The DB2 database high-level qualifier.

    -   The name of the DB2 subsystem identifier name.

-   To enable access to Adabas:

    -   The Adabas SVC number (the default is 249).

    -   The Adabas database number.

    -   The Adabas load library location (the default is ADA622.LOAD)

Installation and Configuration
==============================

This section describes how to install to configure Attunity Server using
Attunity Studio.

This section includes the following topics:

-   IBM z/OS Hardware and Software Requirements

-   Installing Attunity Server on an IBM z/OS Series Platform

-   Updating an Existing Attunity Server Installation with CICS

IBM z/OS Hardware and Software Requirements
-------------------------------------------

This section describes the following requirements for installing
Attunity Server on an IBM z/OS platform:

Hardware Requirements

Software Requirements

Hardware Requirements
---------------------

The following table summarizes the hardware requirements for Attunity Server:

|Hardware Component     | Requirements            |
|-----------------------|-------------------------|
|Processor              | An IBM zSeries computer |
|Disk Space (3390 disk) | 150 cylinders           |

Software Requirements
---------------------

The following table summarizes the software requirements for Attunity
Server:

|Software Component | Requirements |
|-------------------|----------------------------------------------------------------|
|Operating System   | See the customer support section at www.attunity.com for the currently supported operating systems and versions.    |
|TCP/IP             | TCP/IP needs to be active on the system.                       |
|C Runtime Library  | The C runtime library has all the standard C runtime programs. |


Installing Attunity Server on an IBM z/OS Series Platform
---------------------------------------------------------

**Note:** If you have an AIS back-end adapter already installed on the
IBM z/OS platform, then follow the instructions described in Updating an
Existing Attunity Server Installation with CICS on page 2-13.

This section explains how to install Attunity Server. This section includes the following:

-   Installation Worksheet
-   Preinstallation Instructions
-   Kit Installation Instructions
-   Installation Instructions
-   Data Source Configuration Instructions
-   Postinstallation Instructions


Installation Worksheet
----------------------

Verify that you have all the information detailed in the following installation worksheets, so you can refer to it during the configuration process.

Table 2–1 Preinstallation Information


|Topic            | Required Information        |Default             |Comment         |
|-----------------|-----------------------------|--------------------|----------------|
|General          | Operating system            | -                  | See the customer support
|                 |                             |                    | section at www.attunity.com for the currently supported operating
|                 |                             |                    |systems and versions.|
|-                | Disk space                  | -                  | 150 cylinders |
|-                | Installation high-level     | -                  |             |
|                 | qualifier                   |                    |               |
|-                | Volume                      | -                  |             |
|-                | Unit                        | 3390               | SMS only: unit where SMS resides.|
|-                | Output class                | A                  | - |
|-                | JCL job card                | -                  | An optional card (up to 6 lines) to replace the  |
|                 |                             |                    | prefix job (entered as it will appear in the job)|
|-                | ISPF load library name      | ISP.SISPLOAD       | -              |
|CICS             | CICS EXCI load library name | CICS.CICS.SDFHEXCI | -              |


Table 2–2 Required Permissions

Permission
----------

Permission to define an APF-authorized library  
Permission to write to an active proclib, such as user.proclib  
Permission to read the CICS EXCI library  
Permission to update the security manager, such as RACF  
Optionally, permission to specify an output class for Attunity Server output  

Table 2.3 Installation Checklist

| Step                | Comment/Outputs |
|---------------------|---------------------------------|
|tso profile noprefix | Ensures that the user name is not used as part of
|                     | the dataset name allocated in the next steps
| Allocate dataset: {HLQ}.TRANSMIT.KIT              | 130 tracks (3390), format=FB,
|                                                   | record length=80,
|                                                   | block size=3120
|Allocate dataset: {HLQ}.TRANSMIT.LOAD              | 600 tracks (3390), format=FB, record
|                                                   | length=80,block size=3120
|FTP files to z/OS                                  | FTP using binary mode
|RECEIVE INDSNAME('{HLQ}.TRANSMIT.KIT')             | - |
|da('{HLQ}.TRANSMIT.LIB') UNIT(unit) VOLUME(volume) | - | 
|EX {HLQ}.TRANSMIT.LIB(PREPARE)                     | Successful MAXCC is 0, 4 or 8  | 
|                                                   | BUILDKIT.SRC, BUILDKIT.LOAD, and|
|                                                   | BUILDKIT.GENDEMO created  |
|EX {HLQ}.BUILDKIT.SRC(NAVINST)                     | Successful MAXCC is 0 or 4|


Preinstallation Instructions
----------------------------

Before starting the installation, ensure that the following information
is available:

-   The output class for the installation output if you do not want to
    use the default value, which is A.

Before starting the installation, ensure that you have the following
permissions:

-   Permission to define an APF-authorized library.
-   Permission to write to an active proclib, such as user.proclib.
-   Permission to read the CICS EXCI library.
-   Permission to update the security manager, such as RACF

Note: It is recommended to specify an output class with HOLD status to
prevent the loss of Attunity Server log information.

Attunity Server for the IBM z/OS platform is contained in the following datasets:

&lt;HLQ&gt;.TRANSMIT.KIT  
&lt;HLQ&gt;.TRANSMIT.LOAD  

Kit Installation Instructions
-----------------------------

Perform the following on the mainframe:

1\. Run the following command:

> tso profile noprefix

The user name will not be used as part of the dataset name. On some systems this is the default.

2\. If HLQ does not exist, you might want to define an alias for it now.

3\. Allocate datasets with the following space for each of these files:

\[HLQ\].TRANSMIT.KIT = 130 tracks (3390 disk)  
\[HLQ\].TRANSMIT.LOAD = 600 tracks (3390 disk)

For each dataset: RECFM=FB, LRECL=80 and DSORG=PS. The block size is 3120.

Transferring the Kit to the Dataset
-----------------------------------

You need to copy the contents of the kit to the z/OS datasets just
allocated.

Perform the following steps to copy the AIS installation kit contents to
the mainframe:

1\. Extract the compressed file to a folder on your computer.

2\. Perform the transfer using one of the following methods:

Method 1:

If you have a standard procedure for file transfer, use it to perform a
binary transfer of the following files:

ac5340.transmit.kit to '\[HLQ\].transmit.kit'

ac5340.transmit.load to '\[HLQ\].transmit.load'

Method 2:

a\. Open a Windows command prompt.

b\. Type FTP followed by a space and the name of the z/OS to which you wish to connect. This creates an FTP connection to the mainframe machine. Enter your z/OS userid and password when prompted for them.

c\. Type bin to indicate a binary transfer.

d\. Enter the following command to transfer the data from the .KIT file
to z/OS:

put ac5340.transmit.kit '\[HLQ\].transmit.kit' \[replace\]

Where:

– ac5340.transmit.kit is the name of your installation file on Windows

– \[replace\] overwrites the old installation dataset on z/OS (should
one exist)

e\. Enter the following command to transfer the data from the .LOAD file
o z/OS:

put ac5340.transmit.load '\[HLQ\].transmit.load' \[replace\]

f\. Exit FTP by typing quit.

Installation Instructions
-------------------------

Perform the following steps to install Attunity Server:

1\. Run the following command in the Data Set List screen.

TSO RECEIVE INDSNAME('&lt;HLQ&gt;.TRANSMIT.KIT')

Where &lt;HLQ&gt; represents the high-level qualifier that you assign
for the Attunity Server installation. Assign the high-level qualifier
you used in the preinstallation procedure.

Note: You can use more than one high-level qualifier, such as

ACME.DEV.&lt;HLQ&gt;, with the following conditions:

The total length must be less than or equal to twenty characters.

The words transmit and buildkit cannot be used as high-level qualifiers.

2\. Enter the following when prompted for the restore parameters:

da('&lt;HLQ&gt;.TRANSMIT.LIB') \[UNIT(unit) VOLUME(volume)\]

This extracts the &lt;HLQ&gt;.TRANSMIT.LIB library from the
&lt;HLQ&gt;.TRANSMIT.KIT kit to the specified unit and volume. If a unit
and volume are not specified then the library is extracted to the
default unit and volume.

3\. Enter the following TSO command:

TSO EX '&lt;HLQ&gt;.TRANSMIT.LIB(PREPARE)'

Follow the instructions in the Response column in Table 2–4 for each
entry in the

Screen column.

Table 2–4 Installation Prepare Job Prompts and Responses

  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  Screen Response                                                            |      vvvvv        
  ---------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  DO YOU WANT TO USE SMS MANAGED STORAGE FOR THIS INSTALLATION Y/N \[N\] :   | If you want to manage the storage using SMS, then answer Y, otherwise answer N.
  ENTER THE STORCLASS FOR INSTALLATION TEMP DATASETS \[ \] :                 | This prompt is displayed only if SMS is used to manage the installation (you answered Y to the first prompt).
                                                                              |Enter the storage class
  ENTER THE UNIT NAME FOR INSTALLATION TEMP DATASETS \[3390\] :               |If a storage class is not specified, then enter the unit name for temporary datasets used during the installation procedure
  ENTER THE VOLUME NAME FOR INSTALLATION TEMP DATASETS :                      |This prompt is displayed only if SMS is not used to manage the installation (you answered N to the first prompt).
                                                                              |The volume name for temporary datasets used during the installation procedure
  ENTER THE OUTPUT CLASS FOR INSTALLATION OUTPUT \[A\] :                      |Enter the output class only if you do not want the default class used (the default is A)
  DO YOU WANT TO USE THE DEFAULT JOB CARD Y/N \[Y\]                           |A job card is displayed. If you want to use a replacement card, then it must be entered as it will appear in the job. You can enter up to six lines. Enter a blank card to end input. If you do not enter a card, then the Attunity Server default card is used.
  DO YOU WANT TO PERFORM A MANUAL (M) OR AUTOMATIC (A) INSTALLATION \[A\] :   |If you want to review the JCL used to install Attunity Server, before it is submitted, then respond M for a manual installation.
  PLEASE REVIEW AND SUBMIT FOR EXECUTION THE HLQ.TRANSMIT.LIB(INSTJO)         |This prompt is displayed only if a manual installation is
  ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

4\. Verify that all steps on the INSTJO job ended with return code 0.

5\. Enter the following TSO command:

TSO EX '&lt;HLQ&gt;.BUILDKIT.SRC(NAVINST)'

Follow the instructions in the Response column in Table 2–5 for each entry in the Screen column.

  -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  DO YOU WANT TO USE SMS MANAGED STORAGE FOR THIS INSTALLATION Y/N \[N\]:                                                                                                                                                     If you want to manage the storage using SMS, then answer Y, otherwise answer N.
  --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  THE SOFTWARE WILL BE INSTALLED UNDER THE HIGH LEVEL QUALIFIER THAT YOU WILL CHOOSE.                                                                                                                                         The high-level qualifier for the installation (referred to as INSTROOT throughout this guide)

  ENTER THE HIGH LEVEL QUALIFIER                                                                                                                                                                                              You can use more than one high-level qualifier (such as ACME.DEV.VA10). The total length must be less than or equal to twenty characters. The qualifiers can be the same as the ones used for the installation (step 1).
                                                                                                                                                                                                                              
  \["QUALIFIER"\]:                                                                                                                                                                                                            The words transmit and buildkit cannot be used as high-level qualifiers.

  ENTER THE STORCLASS FOR TEMP DATASETS                                                                                                                                                                                       This prompt is displayed only if SMS is used to manage the installation (you answered Y to the first prompt).
                                                                                                                                                                                                                              
  \['STORCLASS'\]:                                                                                                                                                                                                            Enter the storage class

  ENTER THE UNIT NAME FOR INSTALLATION                                                                                                                                                                                        The unit name for temporary datasets used during the installation procedure.
                                                                                                                                                                                                                              
  TEMP DATASETS \[3390\] :                                                                                                                                                                                                    

  ENTER THE VOLUME NAME FOR INSTALLATION TEMP DATASETS:                                                                                                                                                                       This prompt is displayed only if SMS is not used to manage the installation (you answered N to the first prompt).
                                                                                                                                                                                                                              
                                                                                                                                                                                                                              The volume name for temporary datasets used during the installation procedure

                                                                                                                                                                                                                              

                                                                                                                                                              Page 14

  PLEASE CONFIRM (YES/NO/QUIT) \[YES\]:                                                                                                                                                                                       Confirm the entered details

  ENTER THE OUTPUT CLASS FOR INSTALLATION OUTPUT \[A\]:                                                                                                                                                                       Enter the output class for Attunity Server output. Assigning a device which is set on HOLD prevents the loss of log information when the Attunity Server started tasks finish (the default is A).

                                                                                                                                                                                                                              

  ADDING AND UPDATING ATTUNITY CONNECT FOR CICS CONFIGURATION ON THIS MACHINE, FROM A REMOTE ATTUNITY ADMINISTRATION CONSOLE, CAN ONLY BE DONE BY SOMEONE DEFINED AS AN ADMINISTRATOR FOR ATTUNITY CONNECT ON THIS MACHINE.   In order to manage Attunity Server on this computer from Attunity Studio, you need to enter a user account of a user who will have administrative authorization, or press Enter to enable any user to administer Attunity Server on this computer. The administrative rights can be changed from within Attunity Studio after the installation.

  ENTER A VALID USER NAME FOR AN ATTUNITY CONNECT ADMINISTRATOR                                                                                                                                                               
                                                                                                                                                                                                                              
  \[ALL\]:                                                                                                                                                                                                                    

  DO YOU WANT TO USE THE DEFAULT JOB CARD Y/N \[Y\]                                                                                                                                                                           A job card is displayed. If you want to use a replacement card, then it must be entered as it will appear in the job. You can enter up to six lines. Enter a blank card to end input. If you do not enter a card, then the Attunity Server default card is used.

  DO YOU WANT TO PERFORM A MANUAL (M) OR AUTOMATIC (A) INSTALLATION \[A\] :                                                                                                                                                   If you want to review the JCL used to install Attunity Server, before it is submitted, then respond M for a manual installation.

  PLEASE REVIEW AND SUBMIT FOR EXECUTION THE DSN1 (INSTJBOR)                                                                                                                                                                  This prompt is displayed only if a manual installation is requested (you answered M to the previous prompt).
                                                                                                                                                                                                                              
                                                                                                                                                                                                                              DSN1 is the dataset name where INSTJBOR is located.

                                                                                                                                                                                                                              
  -----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

The installation is completed. All JCL jobs and REXX procedures are
written to the INSTROOT.USERLIB library. INSTROOT is the high-level
qualifier for the installation.

Data Source Configuration Instructions
--------------------------------------

Enter the following TSO command:

TSO EX '&lt;HLQ&gt;.BUILDKIT.SRC(CUST)'

Follow the instructions in the Response column in Table 2–6 for each
entry in the Screen column.

***Table 2–6 Installation Prompts and Responses***


| Screen                            | Response                          |
|-----------------------------------|-----------------------------------|
| DO YOU WANT ATTUNITY SERVER TO WORK WITH DB2     | If you want to use DB2, then answer Y, otherwise answer N      |
| ENTER THE DB2 HLQ                 | This is displayed only if you     |
|                                   | answered Y to using DB2. Enter    |
|                                   | the HLQ where DB2 is installed.   |
| ENTER THE DB2 SUBSYSTEM NAME      | Enter the DB2 subsystem.          |
| THE DATABASE DB2 RESIDES ON THIS  | This is a summary of the DB2      |
| MACHINE\                          | questions. Check the summary to   |
| THE DB2 SUBSYSTEM NAME IS :       | be sure all is correct. You can   |
|                                   | select:                           |
| PLEASE CONFIRM (YES/NO/QUIT)      |                                   |
| :YES: :                           | Yes, to use the DB2 database as   |
|                                   | configured                        |
|                                   |                                   |
|                                   | No, to not use the DB2 database   |
|                                   |                                   |
|                                   | Quit, to leave the CUST process   |
| DO YOU WANT ATTUNITY SERVER TO    | If you want to use Adabas, then   |
| WORK WITH ADABAS                  | answer Y, otherwise answer N.     |
| ENTER THE ADABAS SVC NUMBER       | This is displayed only if you  answered Y to using Adabas.       |   
|                                   | Enter the Adabas SVC number for   Adabas on your system.            |



  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  ENTER THE ADABAS LOAD LIBRARY NAME                                          |Enter the Adabas Load Library name for Adabas on your system.
  --------------------------------------------------------------------------- |---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  YOUR ADABAS SVC IS:                                                         |This is a summary of the Adabas questions. Check the summary to be sure all is correct. You can select:
                                                                              |
  THE ADABAS LOAD LIBRARY NAME IS:                                            |Yes, to use the Adabas database as configured
                                                                              |
  PLEASE CONFIRM (YES/NO/QUIT)                                                |No, to not use the Adabas database
                                                                              |
                                                                              |Quit, to leave the CUST process
                                                                              |
  DO YOU WANT ATTUNITY SERVER TO WORK                                         |If you want the server to work with IMS, then answer Y, otherwise answer N.
                                                                              |
  WITH IMS                                                                    |
                                                                              |
  ENTER IMS ID \[IMS1\]:                                                      |Enter the IMS subsystem ID
                                                                              |
  ENTER DBD LIBRARY NAME                                                      |Enter the IMS DBD Library name for IMS on your system
                                                                              |
  ENTER PSB LIBRARY NAME                                                      |Enter the IMS PSB Library name for IMS on your system
                                                                              |
  ENTER RES LIBRARY NAME                                                      |Enter the IMS RES Library name for IMS on your system
                                                                              |
  ENTER PROC LIBRARY NAME                                                     |Enter the IMS PROC Library name for IMS on your system
                                                                              |
  ENTER PGM LIBRARY NAME                                                      |Enter the IMS PGM Library name for IMS on your system.
                                                                              |
  ENTER YOUR PSB NAME                                                         |Enter the name of the PSB file with the name of your IMS
                                                                              |
  YOUR DBDLIB IS:                                                             |This is a summary of the IMS questions. Check the summary to be sure all is correct. You can select:
                                                                              |
  YOUR PSBLIB IS:                                                             |Yes, to use the IMS database as configured
                                                                              |
  YOUR RESLIB IS:                                                             |No, to not use the IMS database
                                                                              |
  YOUR PROCLIB IS:                                                            |n Quit, to leave the CUST process
                                                                              |
  YOUR PGMLIB IS:                                                             |
                                                                              |
  YOUR PSBNAME IS:                                                            |
                                                                              |
  PLEASE CONFIRM (YES/NO/QUIT) :YES: :                                        |
                                                                              |
  DO YOU WANT ATTUNITY SERVER TO WORK                                         |f you want the server to work with IMS/DB under CICS,
                                                                              |
  WITH IMS/DB UNDER CICS                                                      |then answer Y, otherwise answer N.
                                                                              |
  ENTER THE CICS EXCI LOAD LIBRARY NAME                                       |Enter the CICS EXCI load library name only if you do not
                                                                              |
  \[CICSTS13.CICS.SDFHEXCI\]:                                                 |want the default
                                                                              |
  PLEASE CONFIRM (YES/NO/QUIT) \[YES\]:                                       |Confirm the entered details
                                                                              |
  DO YOU WANT ATTUNITY SERVER TO WORK                                         |f you want the server to work with the CICS APP
                                                                              |
  WITH CICS APP ADAPTER                                                       |ADAPTER, then answer Y, otherwise answer N.
                                                                              |
  ENTER THE ISPF LOAD LIBRARY NAME \[ISP.SISPLOAD\] :                         |Enter the ISPF load library name only if you do not want the default
                                                                              |
  PLEASE CONFIRM (YES/NO/QUIT) \[YES\]:                                       |Confirm the entered details
                                                                              |
  ENTER THE OUTPUT CLASS FOR INSTALLATION OUTPUT \[A\]:                       |Enter the output class for Attunity Server output. Assigning a device which is set on HOLD prevents the loss of log information when the Attunity Server started tasks finish (the default is A).
                                                                              |
  DO YOU WANT TO USE THE DEFAULT JOB CARD Y/N \[Y\]                           |A job card is displayed. If you want to use a replacement card, then it must be entered as it will appear in the job. You can enter up to six lines. Enter a blank card to end input.
                                                                              |
                                                                              |If you do not enter a card, then the Attunity Server default card is used.
                                                                              |
  DO YOU WANT TO PERFORM A MANUAL (M) OR AUTOMATIC (A) INSTALLATION \[A\] :   |It is recommended that you choose automatic, which is the default.
                                                                              |
                                                                              |
  -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Postinstallation Instructions
-----------------------------

The following postinstallation tasks must be done to work with Attunity
Server:

-   Copy and Install Programs into CICS

-   Postinstallation Procedures

-   Starting the Daemon

-   Setting Up Attunity Server for Reentrancy

Copy and Install Programs into CICS

You must copy and install the some supporting programs if you are using
a CICS session. The following table describes the programs and when you
must use them.

Cics programs


The following describes the steps necessary to copy and install these
programs under CICS.

1\. In the Data Set List Utility screen, type the name of the &lt;HLQ&gt;
on the Dsname Level line. This will return a list of the files.

2\. In the Command column next to the &lt;HLQ&gt;.LOAD, type M.

3\. Type C next to the programs that you wish to use. The list of
available programs is in the CICS Programs table.

4\. Hit the &lt;enter&gt; key and specify the name of the target CICS
load library (One concatenated to the DFHRPL DD statement).

5\. Log on to CICS.

6\. Enter the following transaction:

CEDA INS PROG(&lt;PROG NAME&gt;) G(ATY)

The &lt;PROG NAME&gt; is on one of the programs from the CICS Programs
table. Run this for each of the programs you are using.

After installation, the following steps are required:

n Copy ATTDAEMN from &lt;HLQ&gt;.USERLIB to a system procedure library
(such as SYS1.PROCLIB) and edit the following line as required:

// PARM='-B -L &lt;ip\_address&gt;:&lt;port&gt; START IRPCDINI'

Where ip\_address is ip address of the computer, 2551 is the default
port for starting the daemon and IRPCDINI is the default daemon
configuration.

Copy &lt;HLQ&gt;.USERLIB (ATTSRVR) to a system procedure library (such
as SYS1.PROCLIB). If you are using IMS, copy NVIMSSRV and NVBMPSRV as
well.

If you decide to change the name of the ATTSRVR member when you move it
to a general high-level qualifier, then change the name specified in the

StartupScript parameter in the daemon configuration to the new name:

Run &lt;HLQ&gt;.USERLIB(NAVCMD) and enter EDIT DAEMON IRPCDINI at the
prompt.

Change the startupScript parameter from ATTSRVR to the new name for the
server:

&lt;Workspace name="Navigator"

startupScript="NEW\_NAME"

serverMode="reusable"

... /&gt;

Exit and save the changes.

Define the LOADAUT library as an APF-authorized library.

If RRS is going to be used, define LOAD library as APF-authorized.

Ask your z/OS system programmer to add &lt;HLQ&gt;.LOADAUT as an
APF-authorized library.

Using your security manager (such as RACF), assign a user to the
following started tasks: ATTDAEMN, ATTSRVR and NVIMSSRV (if you are
using IMS/DLI).

The user should have the following authority:

Permission to issue master console commands.

Permission to start ATTSRVR.

Access to an OMVS segment.

ALTER authority on datasets under INSTROOT to access to read, write,
allocate, and delete datasets under INSTROOT.

The input during the installation procedure is written to

.BUILDKIT.SRC(PARS). You can use this file to provide the same inputs if
you rerun the installation, where nnn is the high-level qualifier you
assign for the installation.

For information about specifying Attunity Server as the service using
port 2551 in the TCP/IP configuration parameters, refer to z/OS
Communications Server IP Configuration Reference.

Starting the Daemon
-------------------

Start the daemon by issuing the following z/OS command:

S ATTDAEMN

If you have changed the name of the started task, use the new name
instead of ATTDAEM.

To submit the daemon as a job, uncomment the first two lines of the
ATTDAEMN JCL, change the PARM line as described earlier, and run the job
using the subcommand.

The ATTDAEMN JCL is similar to the following:

>  //\*ATTDAEMN JOB 'RR','TTT',MSGLEVEL=(1,1),CLASS=A,  
>  //\* MSGCLASS=A,NOTIFY=&SYSUID,REGION=8M  
>  //STEP1 EXEC PGM=IRPCD,  
>  // PARM='-B START IRPCDINI'  
>  //\* PARM='-B -L :8883 START'  
>  //STEPLIB DD DSN=INSTROOT.LOADAUT,DISP=SHR  
>  //SYSPRINT DD SYSOUT=A  
>  //GBLPARMS DD DSN=INSTROOT.DEF.GBLPARMS,DISP=SHR  
>  // EXEC PGM=IRPCD,COND=((1,EQ,STEP1),(2,EQ,STEP1)),  
>  // PARM='-KATTDAEMN START ''INSTROOT.DEF.IRPCDINI'''  
>  //STEPLIB DD DSN=INSTROOT.LOADAUT,DISP=SHR  
>  //SYSPRINT DD SYSOUT=A  
>  //GBLPARMS DD DSN=INSTROOT.DEF.GBLPARMS,DISP=SHR  
>  //SYSDUMP DD DUMMY  

---

Note: When the language is set to JPN, KOR, SPA, or SCHI, the daemon may
stop responding. To be sure that the daemon responds add HLQ.LOAD to the
STEPLIB. To do this, add:

DD DSN=HLQ.LOAD

to the end of the following line:

//STEPLIB DD DSN=INSTROOT.LOADAUT,DISP=SHR

---

Setting Up Attunity Server for Reentrancy
-----------------------------------------

All Attunity Server load modules are reentrant and therefore eligible
for LPA. Placing these modules in the LPA improves response time and
saves virtual memory. It is recommended to place INSTROOT.LOA in the LPA
concatenation. Ask your z/OS programmer to do this.

Setting Up Attunity Server for Reentrancy
-----------------------------------------

All Attunity Server load modules are reentrant and therefore eligible
for LPA. Placing these modules in the LPA improves response time and
saves virtual memory. It is recommended to place INSTROOT.LOA in the LPA
concatenation. Ask your z/OS programmer to do this.

---

Note: If you intend using impersonation, so that you can run in a
security context that is different than the context of the process that
owns the server, then do the following:

Place the INSTROOT.LOAD(ATYSVCW) member in an APF-authorized library
outside the LPA.

Change the ATTSRVR member (located in the active proclib), by adding the
following to the STEPLIB list:

// DD DSN=apf\_library,DISP=SHR

Where apf\_library is the APF-authorized library outside the LPA where
the ATYSCVW member was moved.

---

Updating an Existing Attunity Server Installation with CICS
-----------------------------------------------------------

Verify that you have all the information detailed in the following
installation worksheets, so you can refer to it during the configuration
process.

Table 2–8 Preinstallation Information

| Topic | Required Information         | Default            | Comment
|-------|------------------------------|--------------------|-------
|CICS   | CICS EXCI load library name | CICS.CICS.SDFHEXCL | -

Permission
----------
Permission to read the CICS EXCI library

In the nnn.BUILDKIT.SRC library, run the CUST member, as shown:

ex CUST

Follow the instructions in the Response column in Table 2–10 for each
entry in the Screen column.

---

  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  **Screen**                                                                                                                                        |**Response**
  ------------------------------------------------------------------------------------------------------------------------------------------------- |-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
                                                                                                                                                    |
                                                                                                                                                    |
  DO YOU WANT ATTUNITY CONNECT FOR LEGACY ADAPTER TO WORK WITH IMS/DB                                                                               |Answer YES to this prompt if you have AIS for IMS/DB already installed.
                                                                                                                                                    |
  (YES/NO) \[YES\] :                                                                                                                                |
                                                                                                                                                    |
  ENTER DBD LIBRARY NAME \[IMS.DBDLIB\] :                                                                                                           |If you responded YES to working with IMS/DB, then enter the library where database definition (DBD) files are located.
                                                                                                                                                    |
  ENTER PSB LIBRARY NAME \[IMS.PSBLIB\] :                                                                                                           |If you responded YES to working with IMS/DB, then enter the library where Program Specification Blocks (PSBs) are located.
                                                                                                                                                    |
  ENTER YOUR PSB NAME \[HOSPPSB\] :                                                                                                                 |If you responded YES to working with IMS/DB, then enter the name of the PSB file to use.
                                                                                                                                                    |
  PLEASE CONFIRM (YES/NO/QUIT) \[YES\] :                                                                                                            |If you responded YES to working with IMS/DB, then confirm the entered details.
                                                                                                                                                    |
  DO YOU WANT ATTUNITY CONNECT FOR LEGACY ADAPTER TO WORK WITH IMS/DB                                                                               |If you want to access IMS/DB data under CICS, using AIS adapter for IMS/DB, then respond YES.
                                                                                                                                                    |
  UNDER CICS (YES/NO) \[NO\] :                                                                                                                      |
                                                                                                                                                    |
  ENTER THE CICS EXCI LOAD LIBRARY NAME \[CICSTS13.CICS.SDFHEXCI\] :                                                                                |If you responded YES to working with IMS/DB under CICS, then enter the CICS EXCI load library name only if you do not want the default.
                                                                                                                                                    |
  PLEASE CONFIRM (YES/NO/QUIT) \[YES\] :                                                                                                            |If you responded YES to working with IMS/DB under CICS, then confirm the entered details.
                                                                                                                                                    |
  DO YOU WANT ATTUNITY CONNECT FOR LEGACY ADAPTER TO WORK WITH CICS APP ADAPTER (YES/NO) \[YES\] :                                                  |Answer YES to this prompt
                                                                                                                                                    |
  ENTER THE CICS EXCI LOAD LIBRARY NAME \[CICSTS13.CICS.SDFHEXCI\] :                                                                                |Enter the CICS EXCI load library name only if you do not want the default.
                                                                                                                                                    |
  PLEASE CONFIRM (YES/NO/QUIT) \[YES\] :                                                                                                            |Confirm the entered details
                                                                                                                                                    |
  THE VSAM DRIVER IS INSTALLED AUTOMATICALLY. DO YOU ALSO WANT ATTUNITY CONNECT FOR LEGACY ADAPTER TO WORK WITH VSAM UNDER CICS (YES/NO) \[NO\] :   |Answer YES to this prompt if you have AIS for VSAM already installed and you want to access VSAM data under CICS.
                                                                                                                                                    |
  ENTER THE CICS EXCI LOAD LIBRARY NAME \[CICSTS13.CICS.SDFHEXCI\] :                                                                                |If you responded YES to working with VSAM under CICS, then enter the CICS EXCI load library name only if you do not want the default.
                                                                                                                                                    |
  PLEASE CONFIRM (YES/NO/QUIT) \[YES\] :                                                                                                            |If you responded YES to working with VSAM under CICS, then confirm the entered details.
                                                                                                                                                    |
  ENTER THE ISPF LOAD LIBRARY NAME \[ISP.SISPLOAD\] :                                                                                               |Enter the ISPF load library name only if you do not want the default.
                                                                                                                                                    |
  PLEASE CONFIRM (YES/NO/QUIT) \[YES\] :                                                                                                            |Confirm the entered details
                                                                                                                                                    |
  ENTER THE OUTPUT CLASS FOR INSTALLATION OUTPUT \[A\] :                                                                                            |Enter the output class for the Attunity Server output. Assigning a device which is set on HOLD prevents the loss of log information when the Attunity Server started tasks finish (the default is A).
                                                                                                                                                    |
  DO YOU WANT TO USE THE DEFAULT JOB CARD Y/N \[Y\]                                                                                                 |A job card is displayed. If you want to use a replacement card, then it must be entered as it will appear in the job. You can enter up to six lines. Enter a blank card to end input.
                                                                                                                                                    |
                                                                                                                                                    |If you do not enter a card, then the Attunity Server default card is used.
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

---

The installation is completed. All JCL jobs and REXX procedures are
written to the INSTROOT.USERLIB library. INSTROOT is the high-level
qualifier for the installation.

After completing the installation, perform postinstallation tasks, as
described in "Post-Installation Instructions", as required.


Post-Installation Instructions
==============================

This section describes procedures that are performed after the
installation was successfully performed.

It includes the following topics:

* Overview

General Post Installation Procedures  

Setting Up Security  
Setting Up Attunity Server to Work with CICS  
Setting Up Attunity Server to Work with IMS/TM  
Setting Up Attunity Server for Reentrancy  
Setting Up Attunity Server to Access DB2 CLI  
Setting Up Attunity Server to Update VSAM Data  
Setting Up Attunity Server to Access IMS/DB  
Installing Attunity Server Natural/CICS Agent   
Setting the Language  

Overview
--------

This section describes post installation tasks that must be performed to
work with Attunity Server.

In addition, if you are upgrading Attunity Server, see Updating an
Existing Attunity Server Installation with CICS.

Attunity Server version for mainframe includes a PS,
NAVROOT.DEF.GBLPARMS, which contains global environment information.
This PS is read at startup and the correct version of Attunity Server is
used, based on the details provided in the Attunity Server startup task.

The information stored in this member includes the dataset qualifier
where Attunity Server is installed, and other information for Attunity
Server use.

If you change the location of NAVROOT.DEF.GBLPARMS, you must also change
the relevant cards in the following jobs to the new locations:

ATTSRVR: Located in an active proclib (such as USER.PROCLIB), as
described in Setting APF-Authorization and Started Tasks.

ATTDAEMN: Located in an active proclib (such as USER.PROCLIB), as
described in

Setting APF-Authorization and Started Tasks.

NVIMSSRV: Located in an active proclib (such as USER.PROCLIB), as
described in

Setting APF-Authorization and Started Tasks.

NAVSQL: Located in NAVROOT.USERLIB

General Post Installation Procedures
------------------------------------

This section includes the following topics:

Registering Attunity Server

Setting APF-Authorization and Started Tasks

Setting 2-Phase Commit

TCP/IP

Workspace for z/OS Server Environment File

Registering Attunity Server
---------------------------

Registration is available from within Attunity Server, after adding a
machine in the

Design perspective Configuration explorer.

**To register Attunity Server**

1\. In Attunity Studio, right-click the machine and select License
Management.

2\. Select Register Product.

3\. In the Register Product window, browse to the license file supplied
by Attunity.

The details of the license are displayed.

4\. Click Register to register Attunity Server on this machine.

Note: The success message that is displayed relates to the license

being loaded to the repository. It does not validate the license itself.

Thus, an invalid license can still generate a success message.

Setting APF-Authorization and Started Tasks
-------------------------------------------

All JCL jobs and REXX procedures are written to the NAVROOT.USERLIB
library. After the installation procedure completes, do the following:

Define the LOADAUT library as an APF-authorized library as follows:

1\. Enter the following command in the SDSF screen:

"/setprog apf,add,dsn=navroot.loadaut,volume=ac002" Where ac002 is the
volume where you installed Attunity Server and navroot is the high-level
qualifier where Attunity Server is installed.

2\. If the Attunity Server installation volume is managed by SMS, then
when defining APF-authorization, enter the following command in the SDSF
screen:

"/setprog apf,add,dsn=navroot.loadaut,SMS"

3\. Ensure that the library is APF-authorized, even after an IPL of the
machine.

Move the NAVROOT.USERLIB(ATTDAEMN), NAVROOT.USERLIB(ATTSRVR), and if you
are using IMS/DLI the NAVROOT.USERLIB(NVIMSSRV) members to any active
proclib (such as USER.PROCLIB). ATTDAEMN, ATTSRVR, NVIMSSRV are run as
started tasks.

ATTDAEMN controls the Attunity Server daemon. A daemon runs on every
machine running Attunity Server and manages communication between
machines running Attunity products.

The daemon authenticates clients, authorizes requests for a server
process within a certain server workspace and provides clients with the
required servers. When a client requests a connection, the daemon
allocates a server process (or where applicable, a thread) to handle
this connection, and refers the client to the allocated process.

This may be a new process (dedicated to this client) or an
already-started process. Further communication between the client
session and the server process is direct and does not involve the
daemon. The daemon is notified when the connection ends and the process
is either killed or remains available for use by another client. The
server processes are managed by ATTSRVR.

If you decide to rename the ATTSRVR member when you move it to a general
high-level qualifier, then change the name specified in the
StartupScript parameter in the daemon configuration to the new name, as
follows:

1\. Run NAVROOT.USERLIB(NAVCMD).

2\. Enter the following at the prompt:

EDIT DAEMON IRPCDINI

3\. Change the startupScript parameter from ATTSRVR to the new name for
the server. For example:

&lt;Workspace name="Navigator"

startupScript="NEW\_NAME"

serverMode="reusable"

... /&gt;

4\. Exit and save the changes.

5\. If you get following error:

ISPS118L SERVICE NOT INVOKED. A VALID ISPF ENVIRONMENT DOES NOT EXIST.

then run the following procedure:

– Run NAVROOT.USERLIB(NAVCMD) and enter ‘export all sys xml.data.set’ at
the prompt.

– Using the z/OS text editor, change the startupScript parameter from
ATTSRVR to the new name for the server. For example:

&lt;Workspace name="Navigator" startupScript="NEW\_NAME"
serverMode="reusable"

... /&gt;

– Rerun NAVROOT.USERLIB(NAVCMD) and enter ‘import sys xml.data.set’ at
the prompt.

The ATTDAEMN, ATTSRVR, and NVIMSSRV (if you are using IMS/DLI) started
tasks need permission to use an Open Edition TCP/IP stack. The owner
must be a user with OMVS segment defined.

Change the following line in the ATTDAEMN job to include the IP address
of the z/OS machine.

For example, before:

// PARM=’-B START IRPCDINI’

After:

// PARM='-B -L ip\_address START IRPCDINI'

The following table lists the protected system functions that are used
by AIS. AIS always requires the functions listed for the LOADAUT
library. The LOAD library functions listed require authorization only
when you need those functions. See the Used for: column in the table for
an explanation of when and if you need to authorize the LOAD library.

Table 3–1 APF Authorized Functions in AIS

|Authorized |

|Function         |Library   | Mandatory |Component      | Used for:  |
|-----------------|----------|-----------|---------------|------------| 
|MGCRE            | LOADAUT  | Yes       | Daemon        | Starting servers by the daemon
|RACROUTE         | LOUDAUT  | Yes       | Daemon        | Authentication of credentials
|RACROUTE         | LOAD     | No        | Impersonation | Used when a reusable server
|                 |          |           |               | needs to impersonate different
|                 |          |           |               | users.
|MCSOPER, MGCRE   | LOAD     | No        | IMS CDC       | Used to set up automatic
|                 |          |           |               | syncpoints with IMS CDC
|IFI functions    | LOAD     | No        | DB2 CDC       | Using the DB2
|                 |          |           |               | instrumentation facility 
|                 |          |           |               | requires authorization.
|RRS              | LOAD     | No        | 2PC           | Used when setting up two-phase commit.

Setting 2-Phase Commit
----------------------

To use two-phase commit capability to access data on the z/OS machine,
define every library in the ATTSRVR JCL as an APF-authorized library
(see Setting APF-Authorization and Started Tasks).

TCP/IP
------

For information about specifying Attunity Server as the service using
port 2551 in the TCP/IP network services file, consult TCP/IP
documentation.

Workspace for z/OS Server Environment File
==========================================

You can configure the daemon server subtask for a workspace through a
z/OS environment file. The file must be named NAVROOT.DEF.workspaceDEF
where NAVROOT is the high level qualifier where Attunity Server is
installed. On startup, a server process for workspace abcd looks for the
file NAVROOT.DEF.abcdDEF.

Note: If the workspace name is longer than five characters, then the
z/OS environment file name must be truncated to five characters. This
means that the default workspace, Navigator, will look for a file called
NavigDEF.

The worspaceDEF server environment file has the following format:

&lt;ENVIRONMENT&gt;

SUBSYSTEM=paramvalue1

PLAN=paramvalue2

Setting Up Security
===================

Make the following definitions in the security manager (the following is
phrased specifically for RACF, although the same principles also apply
to TopSecret and ACF/II).

Define ATTDAEMN and ATTSRVR with a started task class and a general
profile that enables the following:

Provide NVIMSSRV access to ATTDAEMN.

START authority for the ATTSRVR job.

Access to an Open z/OS segment, which defines access to TCP/IP OE
sockets.

Access for the ATTSRVR.\* profile to the following:

– Adabas: ATTSRVR requires READ authority to the Adabas load library.

– DB2: ATTSRVR requires the following authority:

\* EXECUTE for the used plan.

\* READ, UPDATE, ALTER on the DB2 tables to the level you want to
provide the client application.

You must grant ATTSRVR with READ access to the sys-tables (SYSTABLES,
SYSCOLUMNS, etc.).

\* TRACE(MON) and MONITOR2 for DB2 CDC.

– IMS/DB and IMS/TM: ATTSRVR requires READ authority for the following

IMS resources:

\* IMSXCF.OTMACI facility.

\* Any facilty defined for the Attunity Server XCF members. For example,
on SY07, if the XCF member is called ATTXCFMB, then set up
IMSXCF.OTMAI07A.ATTXCFMB with READ access in the FACILITY class and
specify the ATTSRVR userid in the access list.

\* IMS.RESLIB

\* IMS.PGMLIB

\* IMS.PROCLIB

ALTER authority on datasets under NAVROOT (for access to read, write,
allocate and delete under NAVROOT).

Attunity Server Stream and DB2 Security
---------------------------------------

All the libraries in the STEPLIB must be APF-authorized. Grant DB2
DISPLAY GROUP authorization to the ATTSRVR started task. Issue the
following commands to the owner of the ATTSRVR started task:

GRANT TRACE(STAT)

GRANT TRACE(MON)

GRANT MONITOR2

The owner of the ATTSRVR strarted task must have privileges in DB2 to
run offline. Issue the following command:

-start trace(stat)

Setting Up Attunity Server to Work with CICS
============================================

If you did not set up CICS during the installation, do the following to
work with CICS:

1\. Edit the ATTSRVR member to include the CICS EXCI load library name.
ATTSRVR is located in both NAVROOT.USERLIB and an active proclib (such
as USER.PROCLIB).

For example, edit ATTSRVR to add the following lines:

// DD DSN=&cicspfx.SDFHEXCI,

// DISP=SHR

Where &cicspfx is the CICS system prefix (CICSTS13.CICS for example) and
NAVROOT is the high-level qualifier where Attunity Server is installed.

2\. Edit the NAVROOT.USERLIB(NAVCMD) and NAVROOT.USERLIB(NAVSQL) to
include the CICS EXCI load library name to the TASKLIB. For example, add
the following lines:

ALLOCATE DDNAME(TASKLIB) DA('NAVROOT.LOAD'

'&cicspfx.SDFHEXCI' 'ADA622.LOAD'

'ISP.SISPLOAD') SHR

Where &cicspfx is the CICS system prefix (CICSTS13.CICS for example)
NAVROOT is the high-level qualifier where Attunity Server is installed.

3\. IRCSTRT=YES must be specified in the CICS initialization parameters,
so that the IRC (Inter Region Communication) starts, as specified in
Software Requirements.

4\. AIS provides a VTAM netname, ATYCLIEN, for the specific connection
used by EXCI (and MRO) to relay program calls to the CICS target system.
Set up ATYCLIEN as follows:

Use the JCL in the NAVROOT.USERLIB(CICSCONF) member to submit the
DFHCSDUP batch utility program to add the resource definitions to the
DFHCSD dataset (see the IBM CICS Resource Definition Guide for further
details).

Or,

Use the instream SYSIN control statements in the
NAVROOT.USERLIB(CICSCONF) member as a guide to defining the resources
online using the CEDA facility.

Setting Up Attunity Server to Work with IMS/TM
==============================================

In order to execute an IMS transaction with Attunity Server, you need to
set OTMA as described below.

### To set OTMA to work with Attunity Server

1\. Install OTMA with OTMA C/I where IMS resides, since OTMA is not

automatically installed using the IMS INSTALL/IVP Dialog.

2\. In the IMS system definition, set the startup parameter in the IMS
procedure to OTMA=Yes.

In addition, set additional OTMA-related parameters such as GRNAME= for
the XCF group name, and OTMANM= for the IMS member name in that XCF
group, as in the following example:

EDIT IMS.PROCLIB(DFSPBIV1) - 01.03

command ===&gt;

000082 AOIS =,

000083 GRNAME=IMSATT01,

000084 OTMA=YES,

000085 MAXPST=

000086 OTMANM=

3\. Start the OTMA Callable Interface (C/I).

4\. Add an entry in the program properties table (PPT) for the OTMA C/I
initialization program, by editing the SCHEDxx member of the
SYS1.PARMLIB dataset, and adding the following entry:

PPT PGMNAME(DFSYSVI0) /\*PROGRAM NAME =DFSYSVI0 \*/

CANCEL /\*PROGRAM CAN BE CANCELED \*/

KEY(7) /\*PROTECT KEY ASSIGNED IS 7 \*/

SWAP /\*PROGRAM IS SWAPPABLE \*/

NOPRIV /\*PROGRAM IS NOT PRIVILEGED \*/

DSI /\*REQUIRES DATA SET INTEGRITY \*/

PASS /\*CANNOT BYPASS PASSWORD PROTECTION \*/

SYST /\*PROGRAM IS A SYSTEM TASK \*/

AFF(NONE) /\*NO CPU AFFINITY \*/

NOPREF /\*NO PREFERRED STORAGE FRAMES \*/

5\. Do one of the following to make the SCHEDxx changes take effect:

-   Re-IPL the system.

-   Issue the MVS SET SCH= command.

6\. Edit and submit the following JCL procedure to run DFSYSVI0:

//OTMAINIT PROC RGN=3000K,SOUT=A,

/PARM1=

//\*

//IEFPROC EXEC PGM=DFSYSVI0,

//REGION=&RGN

//\*

//STEPLIB DD DISP=SHR,UNIT=SYSDA,

//DSN=IMSVS.RESLIB

//\*

//SYSPRINT DD SYSOUT=&SOUT

//SYSUDUMP DD SYSOUT=&SOUT

//\*

7\. Run DFSYSVI0 after the IPL, to initialize OTMA C/I.

Setting up Attunity Server to work with IMS/DB CDC
==================================================

To ensure that events are written to the log, in the
NAVROOT.USERLIB(NVIMSCMD) the following remove the comment marks (/\*)
from the following:

/\* "ALLOCATE DDNAME(IEFRDER) DA('JOHNW.AS5120R."USERID()".TMP.IMSLOG')
NEW \*/

/\* DELETE SPACE(1,1) CYL \*/

/\* BLKSIZE(1920) LRECL(1916) RECFM(U) REUSE" \*/

The lines should look like the following:

"ALLOCATE DDNAME(IEFRDER) DA('JOHNW.AS5120R."USERID()".TMP.IMSLOG') NEW

DELETE SPACE(1,1) CYL \*/

BLKSIZE(1920) LRECL(1916) RECFM(U) REUSE"

Setting Up Attunity Server for Reentrancy
=========================================

All AIS load modules are reentrant to enable subtasking. Therefore, move
INSTROOT.LOAD to the Link Pack Area (LPA).

Where INSTROOT is the high-level qualifier where AIS is installed.

Using the LPA reduces real storage usage (because everyone shares the
LPA copy) and fetch time.

If you intend using impersonation, so that you can run in a security
context that is different than the context of the process that owns the
server, then do the following:

1\. Place the INSTROOT.LOAD(ATYSVCW) member in an APF-authorized library
outside the LPA.

2\. Change the ATTSRVR member (located in the active proclib), by adding
the following to the STEPLIB list:

// DD DSN=apf\_library,DISP=SHR

Where apf\_library is the APF-authorized library outside the LPA where
the

ATYSCVW member was moved.

Setting Up Attunity Server to Access DB2 CLI
--------------------------------------------

In order to access DB2 CLI, you must have DB2 CLI installed on the
machine. For details of DB2 CLI, refer to the IBM Call Level Interface
Guide and Reference.

The AIS DB2 CLI driver uses an ODBCINI file. During installation of
Attunity Server, an ODBCINI file is defined as a member in
NAVROOT.USERLIB.

The ODBCINI file is similar to the following:

; This is a comment line...

; Example COMMON odbcini

\[COMMON\]

MVSDEFAULTSSID=DSN1

; Example SUBSYSTEM odbcini for DSN1 subsystem \[DSN1\]

MVSATTACHTYPE=CAF

PLANNAME=DSNACLI

The PLANNAME value is the default DB2 Calling Level Interface (CLI) plan
name. If a different plan is used, change the name to the correct plan.

Note: In the example, CAF is used, so only one-phase commit transactions
are supported. Attunity Server also supports two-phase commit
transactions, by setting the MVSATTACHTYPE parameter to RRSAF.

You can specify other parameters in the ODBCINI file, as described in
the IBM ODBC

Guide and Reference.

Note: The AUTOCOMMIT initialization parameter is set automatically by
Attunity Server at runtime, and thus should not be set in the ODBCINI
file.

You must bind to the plan specified in the PLANNAME parameter. The bind
to the plan, check that the job DSNxxx.SDSNSAMP(DSNTIJCL) includes the
following line:

BIND PLAN(DSNACLI)

Where DSNxxx is the DB2 high-level qualifier (the default is DSN610) and
DSNACLI is the plan name specified for the PLANNAME parameter.

For details about setting DB2 security with AIS Stream, see Attunity
Server Stream and DB2 Security.

Setting Up Attunity Server to Update VSAM Data
----------------------------------------------

In order to set up Attunity Server to be able to update VSAM data from a
CICS transaction, copy the UPDTRNS load module from NAVROOT.LOAD to a
CICS DFHRPL library (such as 'CICS.USER.LOAD') and then define the
UPDTRNS program under CICS using any available group such as ATY group:

CEDA DEF PROG(UPDTRNS) G(ATY) LANG(C) DA(ANY) DE(ATTUNIT VSAM UPDATE
PROG)

After defining the UPDTRNS program to a group, install it as follows:

CEDA IN G(ATY)

Setting Up Attunity Server to Access IMS/DB
-------------------------------------------

In the NVIMSSRV, NVIMSSQL, NVIMSCMD jobs that call IMS, add DD cards for
every dataset referenced by one of the DBDs for the PSB.

Note: You cannot run multiple versions of NVIMSCMD in the same session
using split screen.

Example 3–1 DD Card

The following DD card can be specified when one of the DBD cards uses
the DD card NAVDD. Note the other cards, which are built based on the
entries specified for IMS during installation.

"ALLOCATE DDNAME(IMS) DA('ATTUNITY.CONNECT.PSBLIB'
'ATTUNITY.CONNECT.DBDLIB') SHR" – location of IMS libraries"ALLOCATE
DDNAME(DFSVSAMP) DA('IMS.PROCLIB(DFSVSMDB)') SHR" – VSAM index
file"ALLOCATE DDNAME(PROCLIB) DA('IMS.PROCLIB') SHR"

"ALLOCATE DDNAME(NAVDD) DA('ATTUNITY.IMS.DATA.MYDS') SHR" – DD card for
data of

the DBD"ALLOCATE DDNAME(IEFRDER) DA('ATTUNITY.CONNECT.TMP.IMSLOG') NEW

DELETE SPACE(1,1) CYL

BLKSIZE(1920) LRECL(1916) RECFM(U) REUSE" – IMS log"CALL

'IMS.RESLIB(DFSRRC00)' 'DLI,NAVUTIL,NAVPSB'" – PSB name for this call

Note: This DD card is appropriate when using HIDAM.

For details about setting up the daemon to work with IMS/DB, see the AIS
User Guide and Reference.

In the security manager, define NVIMSSRV with a started task class and a
general profile that enables the following:

Access to an Open z/OS segment that defines access to TCP/IP OE sockets.

Access to read, write, allocate and delete for datasets under NAVROOT.

Note: The above is phrased specifically for RACF, although the same
principles also apply to TopSecret and ACF/II.

Setting Up IMS/DB Under IMS/TM
------------------------------

Perform the following to access IMS/DB data under IMS/TM:

1\. Copy the ATYDBDC program from NAVROOT.LOAD to an IMS/TM program
library (such as 'IMS.PGMLIB') with the name of the PSB used to access
the IMS/DB data.

2\. Define the transaction to point to the program, using statements such
as in the following:

APPLCTN PSB=ATYDBDC,SCHDTYP=PARALLEL

TRANSACT CODE=ATYIMSTM,PRTY=(7,10,2),INQUIRY=NO,MODE=SNGL,EDIT=ULC

The default transaction name is ATYIMSTM. If you use a different
transaction, the transaction name must be less than or equal to eight
characters and you must specify this value in the imsTransname driver
property in the binding.

3\. Set up OTMA, as described in Setting Up Attunity Server to Work with
IMS/TM. Installing Attunity Server Natural/CICS Agent

Installing Attunity Server Natural/CICS Agent
---------------------------------------------

The installation copies the following libraries (source and load) and
the Natural INPL dataset to disk:

-   A source library (NAVROOT.NATAGENT.SRCLIB).

-   A load library (NAVROOT.NATAGENT.LODLIB).

-   An INPL dataset containing Natural source programs, object programs,
    and data areas (NAVROOT.NATAGENT.INPL).

The following summarizes the installation procedure for the Attunity
Server agent for Natural/CICS Remote Procedure Calls:

1\. Configuring the Agent.

2\. Installing the CICS Resource Definitions.

3\. Loading and Cataloging the Natural Programs.

4\. Making the Agent Load Modules Available to the CICS Region.

5\. Verifying the Agent Installation is Successful.

6\. Verify the installation is successful from a client machine.

Configuring the Agent
---------------------

To configure the Natural/CICS Agent

1\. Specify the parameters of the Agent Control Block and perform an

Assembly/Link.

See System Parameters for the Natural Agent for a description of all the
parameters available for tailoring the Agent to the specific
installation requirements.

Note: Specifically, determine which security strategy is best for the
site and how you will implement it. See Parameters for Specific Needs
for information on the SECMODE and AUTO parameters.

2\. Edit the JOBCBLK in the NAVROOT.NATAGENT.SRCLIB source library, as
follows:

> 1\. Specify the parameter values you have chosen.
>
> 2\. Edit dataset names in the assemble/link JCL as appropriate.
>
> 3\. Submit the job.
>
> 4\. Check the output to verify that a ATYLCBLK load module has been
> successfully link-edited into the LOAD library.

Installing the CICS Resource Definitions
----------------------------------------

If any program names, transids, etc. were modified in the previous step,
edit the CICSDEF member in the NAVROOT.NATAGENT.SRCLIB source library
and make the appropriate changes to the CICS resource definitions. You
may also wish to modify the RECEIVECOUNT parameter in the ATYS sessions
definition (for EXCI communication) in this member, if the default value
(20) is too small for your installation requirements.

The default value set for RECEIVECOUNT is 20.

The CICSDEF member can be used as input to the DFHCSDUP batch utility
program to add the resource definitions to the DFHCSD dataset. A source
library member named JOBCICS with sample JCL has been supplied for
convenience. See the IBM CICS Resource Definition Guide for further
details.

For example, edit the LOBCICS member as follows:

//CICSDEF PROC CSDDSN='CICS.DFSCSD',

SRCLIB='NAVROOT.NATAGENT.SRCLIB',

CICSPRF='CICS.CICS'

After editing the file, save and submit it.

Alternatively, the CICSDEF member can be used as a guide to defining the
resources online, using the CEDA facility.

After the definitions have been successfully added (either via DFHCSDUP
or by manual online definition), logon to CICS and issue the following
commands to install the resource definitions under CICS:

CEDA INST GROUP(ATYL)

CEMT SET IRC CLOSE

CEDA INST GROUP(ATYI)

CEMT SET IRC OPEN

Loading and Cataloging the Natural Programs
-------------------------------------------

Either use the JCL contained in JOBNLOAD member in the
NAVROOT.NATAGENT.SRCLIB library, or refer to the in-house Natural
administrator, who should have standard JCL available to load Natural
programs from an INPL file to the Natural user system file using the
NATLOAD utility.

Note: For Natural version 2.3 and higher, use the NATLOAD utility
instead of the INPL utility.

Specify the supplied INPL dataset in the JCL (ddname: CMWKF01) and
submit the job.

The Natural programs and data areas are loaded to the NATAGENT library
on the user system file. If Natural Security is installed, then the
NATAGENT library should be defined to Natural Security.

Start a Natural online session and perform the following to catalog all
of the Attunity

Server programs and data areas:

1\. Logon to the NATAGENT library.

2\. Issue the CATALL command and enter \* for the program name.

3\. Mark the Catalog all Source programs option and press Enter.

4\. Add the NATAGENT library to the STEPLIB of all libraries containing
subprograms executed by the Natural agent. Alternatively, move the
following four catalogued modules to SYSTEM library:

-   ATYETA

-   ATYNDISP

-   ATYNDIS2

-   ATYPARMS

Note: The TESTAG01 source subprogram is used to verify a successful
installation. This subprogram should have been cataloged after
performing the previous steps.

Making the Agent Load Modules Available to the CICS Region
----------------------------------------------------------

Make the Natural agent load modules available to the CICS region in one
of following

ways:

-   Concatenate the agent load library to the DFHRPL ddname in the CICS
    region JCL.

-   Copy the required load modules to a user load library already
    defined in DFHRPL.

If the CICS region is currently active, the NEWCOPY function of CEMT
must be executed to make the load modules accessible for this session of
CICS.

As a rule, peruse the list of resources being defined to CICS in the
CICSDEF member in the Natural agent source library (programs,
transactions, connections, etc.) and decide what to authorize in order
for the Natural agent to work.

In particular note that the ATYN, ATYP, and ATYM transactions are all
being started as asynchronous non-terminal tasks and should be defined
to the security tool (such as RACF) accordingly. Also the ATYCLIEN
special connection for EXCI should be defined to the security tool.

Note: Be cautioned that the ATYN transaction is not intended to be
issued directly from a terminal, and will usually terminate abnormally
when issued from a terminal (It is designed to be attached and executed
asynchronously as a non-terminal background task by the Natural agent).

Verifying the Agent Installation is Successful
----------------------------------------------

Test remote procedure calls are included in the kit to verify a
successful installation. The two remote procedure calls perform simple
arithmetic manipulations on the input parameters and return the results
as output parameters.

The first test verifies that the ATY transaction and the Natural agent
function correctly. The second verifies that the Attunity Server can be
used with Natural.

### To verify the installation on the Mainframe is successful

1\. Edit the TESTINST member in the provided source library and make any
necessary changes. In particular, change the \\&APPLID variable to the
value of APPLID in the CICS region.

Note: Other variables enable you to modify the arithmetic operands and
find-criteria of the subprograms being called. Do not change these
variables when initially verifying the installation.

2\. Save the member.

3\. Edit the JOBAGENT member in the provided source libraries and make
any necessary changes for the site. In particular, set the TESTNUM
variable to 01.

4\. Submit the job.

The JOBLOG of the batch job should show the following results:

+RESP = 00000000

+RESP2 = 00000000

+ABCODE =

+OPERAND1 = 0128

+OPERAND2 = 0016

+SUM = 00144

+DIFFERENCE = 0112

+PRODUCT = 00002048

+QUOTIENT = 0008

+ERRCODE = 00000000

+MESSAGE = TESTAG01 EXECUTED SUCCESSFULLY

---

Note: The following error is commonly received (on the JOBLOG) when the
APPLID in the TESTINST member has not been set properly:

RESP = 00000008  
RESP2 = 00000203  

If you receive this error, return to step 1 above and set the name of
the CICS APPLID correctly, then re-submit. If you still receive the
error, make sure that the specified CICS region is in fact active.

---

Verifying the Installation is Successful from a Client
------------------------------------------------------

Test remote procedure calls are included in the kit to verify a
successful installation. These remote procedure calls perform simple
arithmetic manipulations on the input parameters and return the results
as output parameters. To verify the installation is successful from a
client machine

1\. Specify an entry in the binding configuration similar to the
following:

&lt;datasources&gt;  
&lt;datasource name=’NATPROC’ type=’NATURAL’  
connect=’CICS,ATYCLIEN’ /&gt;  
&lt;/datasources&gt;  

2\. Set up the metadata for the TESTAG01 procedure. The XML definition
for the

metadata is as follows:

&lt;?xml version='1.0'?&gt;  
&lt;navobj&gt;  
&lt;procedure name="testag01"&gt;  
&lt;dbCommand&gt;  
PROGRAM=ATYLSTN;TRANSID=ATYI;LIBRARY=NATAGENT  
SUBPROGRAM=TESTAG01  
&lt;/dbCommand&gt;  
&lt;fields&gt;  
&lt;dbCommand&gt;  
PROGRAM=ATYLSTN;TRANSID=ATYI;LIBRARY=NATAGENT  
SUBPROGRAM=TESTAG01  
&lt;/dbCommand&gt;  
&lt;fields&gt;  
&lt;field name="SUM" datatype="ada\_numstr\_s" size="5" scale="0"/&gt;  
&lt;field name="DIFFERENCE" datatype="ada\_numstr\_s" size="4" scale="0"/&gt;  
&lt;field name="PRODUCT" datatype="ada\_numstr\_s" size="8" scale="0"/&gt;  
&lt;field name="QUOTIENT" datatype="ada\_numstr\_s" size="4" scale="0"/&gt;  
&lt;/fields&gt;  
&lt;parameters&gt;  
&lt;field name="OPERAND1" datatype="ada\_numstr\_s" size="4" scale="0"/&gt;  
&lt;field name="OPERAND2" datatype="ada\_numstr\_s" size="4" scale="0"/&gt;  
&lt;/parameters&gt;  
&lt;/procedure&gt;   
&lt;/navobj&gt;  

3\. Import the ADD metadata XML file to Attunity Server by running the
NAVCMD IMPORT utility. For example:

Run NAVROOT.USERLIB (NAVCMD) and enter ’IMPORT NATPROC

NATAGENT.SAMPLE.ADD.TSTAG01X’ at the prompt, where

NATAGENT.SAMPLE.ADD.TSTAG01X is the dataset which contains the XML

4\. Execute calls from a client to confirm that the software is
functioning as specified. For example, on a non-z/OS machine, running
Attunity Server, use NAV\_UTIL EXECUTE NATPROC.

-   TESTAG01: Receives two arithmetic operands and returns their sum,
    difference, product and quotient.

-   Input parameters: OPERAND1 (numeric, 4 bytes,0 precision), OPERAND2
    (numeric, 4 bytes, 0 precision).

-   Output parameters: SUM (numeric, 5 bytes, 0 precision), DIFFERENCE
    (numeric, 4 bytes, 0 precision), PRODUCT (numeric, 8 bytes, 0
    precision), QUOTIENT (numeric, 4 bytes, 0 precision).

Example 3–2

Set the input parameters as follows:

OPERAND1=128  
OPERAND2=16  

The following output parameters should be returned:

SUM=144  
DIFFERENCE=112  
PRODUCT=2048  
QUOTIENT=8  

Setting the Language
====================

National Language Support (NLS) is provided by Attunity Server for the
following languages:

-   English (the default)
-   Hebrew
-   Japanese
-   Korean
-   Simple Chinese
-   Traditional Chinese

The language is specified in the following Attunity Server environment
settings:

-   language
-   codepage

For more information on NLS, see "National Language Support (NLS)" in
the AIS User Guide and Reference.
