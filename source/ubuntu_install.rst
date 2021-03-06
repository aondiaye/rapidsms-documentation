.. _PyGSM: http://github.com/rapidsms/pygsm/tree/master
.. _synaptic: https://help.ubuntu.com/community/SynapticHowto
.. _apt: http://www.debian.org/doc/manuals/apt-howto/ch-apt-get.en.html
.. _RapidSMS: http://www.rapidsms.org
.. _Get Ubuntu: http://www.ubuntu.com/getubuntu 
.. _Ubuntu: http://www.ubuntu.com
.. _GitHub: http://github.com
.. _RapidSMS email group: http://groups.google.com/group/rapidsms

Installing RapidSMS on Ubuntu 9.04 Jaunty
==========================================

Intro
-----

These instructions explain how to install RapidSMS_ with a PyGSM_ backend for communicating with GSM handsets and modems.

There are many options in how to install the software so a few comments on why to do it as described below:

* **Stay as close to standard Ubuntu Software install processes**: 
    In Ubuntu software is installed from Ubuntu central repositories using a package manager like synaptic_ or the command line apt_ commands. 
    
    In setting up a RapidSMS server using *standard* Ubuntu packages makes set-up and maintenance easier. 

.. NOTE::
    The command-line is really useful for managing remote servers, 
    so this documentation will use apt_ . 

    If you aren't comfortable with the command-line feel free to 
    substitute synaptic_. 

* **Install Paths**: 
    On Ubuntu, ``/usr/local`` is a good place to put custom software packages. If you are installing RapidSMS to deploy or develop an application, ``/usr/local`` is a good spot. 

    If you are installing to develop the platform itself, ``~/home/dev`` is probably a better spot. 

.. NOTE::
    If you choose to install any of the non-mainline forks,
    it's a good idea to name your install with the fork name
    so that you can easily remember where it came from.
    
    For example, if you use the 'ewheeler' fork, 
    put RapidSMS_ in /usr/local/ewheeler-rapidsms. 

1 Install Ubuntu Jaunty
-----------------------
Install *either* Jaunty Desktop or Jaunty Server. 

The advantage of Desktop is that you get a full graphical desktop to work with. 

The advantage of Server is that you *don't* get a full desktop, which can discourage people from mucking with your server. Server is a little quicker to install as it has fewer packages.

If you don't know how to get and install Ubuntu_, visit the `Get Ubuntu`_ website for instructions on downloading or ordering a CD.

2 Update Jaunty
---------------
The install images and CDs for Ubuntu are always a little out of date. Before installing, it's a good idea to update the system. This requires an Internet connection and can take some time if there are a lot of changes::

    > sudo apt-get update
    > sudo apt-get upgrade


3 Install required and useful packages
--------------------------------------
The following packages are required to complete the install:

* **git-core**: the 'git' command line for retrieving code from GitHub_
* **python-django**: the Django application server (the basis of RapidSMS_)
* **python-serial**: Python serial libraries needed to run PyGSM_
* **python-tz**: Python timezone libraries
* **python-setuptools**: To download, build, install, upgrade, and uninstall Python packages -- easily!

You will also need at least one of the following database systems:

* **mysql-server**: The MySQL server
* **python-mysqldb**: Python library access to MySQL

OR

* **python-pysqlite2**: Support for Sqlite3 in Python 
    (I know, the numbering is confusing, but pysqlite2 *is* for Sqlite3) 

.. NOTE::
   Currently SQLite should be used for development and
   testing only. The multi-process nature of RapidSMS_
   does not interact stably enough with SQLite for use
   in production.
       
The following packages are OPTIONAL but useful to have, though you can leave them out if you want to create a minimal system and avoid downloading any more packages than you absolutely need:

* **picocom**: a program for talking to modems over serial ports. *Very* useful for debugging modem/handset connection problems.
* **sqlite3**: a command line program for accessing sqlite3 databases. If you use Sqlite3 as the datastore for RapidSMS, you will want this for debugging.
* **sqlite3-doc**: documentation for sqlite3 tool.
* **emacs22-nox**: the Emacs text editor. 

.. NOTE:: 
    EMACS is *big*. If you are happy with Vi or Nano (installed by default), skip this.

This apt command will install *all* the packages listed above::

    > sudo apt-get install git-core python-pysqlite2 mysql-server python-mysqldb python-django python-serial python-tz picocom sqlite3 sqlite3-doc emacs22-nox python-setuptools
    

4 Retrieve and Install PyGSM from GitHub_ 
--------------------------------------------
The source code for PyGSM_ is stored at GitHub_. You use the 'git' command to retrieve it.

Cloning PyGSM code from GitHub_
++++++++++++++++++++++++++++++++
Once you have selected your fork, you can do the following to *clone* it (copy it) to your local machine::

    > cd /usr/local
    > sudo git clone git://github.com/rapidsms/pygsm.git pygsm
    
Compile and install PyGSM
++++++++++++++++++++++++++++

::

    > cd /usr/local/pygsm
    > sudo python setup.py install
    

5 Retrieve and Install RapidSMS from GitHub_ 
--------------------------------------------
The source code for RapidSMS_ is stored at GitHub_. You use the 'git' command to retrieve it.

Choosing the correct Fork
+++++++++++++++++++++++++
The most confusing part of downloading RapidSMS is decide *which version* to download! With all the development happening right now there are more than **10** versions of RapidSMS. In GitHub_ terminology, each version is called a *fork*

.. _RapidSMS Forks: http://github.com/unicefinnovation/rapidsms/network/members
__ `RapidSMS Forks`_

You can view all the `RapidSMS Forks here`__

The **main** fork is ``rapidsms/rapidsms``. Unless you *know* you need something else, this is the one you should use.

.. IMPORTANT:: If you don't know which fork to use, please ask for help on the `RapidSMS email group`_

Cloning the code from GitHub_
+++++++++++++++++++++++++++++
Once you have selected your fork, you can do the following to *clone* it (copy it) to your local machine::

    > cd /usr/local
    > sudo git clone git://github.com/<fork name>/rapidsms.git <local folder name>

Make sure to replace <fork name> with your fork and <local folder name> with a name for the folder that the content will go into. To download the main fork, I do the following::

    > cd /usr/local
    > sudo git clone git://github.com/rapidsms/rapidsms.git rapidsms

Compile and install RapidSMS
++++++++++++++++++++++++++++

.. NOTE:: 
    If you named your rapidsms directory differently than I did (maybe you used a different fork) you need to change my example command below to 'cd' into the folder that holds the RapidSMS code that you retrieved in step 6 above.

::

    > cd /usr/local/rapidsms
    > sudo python setup.py install
    

6 Test your install
-------------------

Test PyGSM
++++++++++

PyGSM includes a small demo program that will connect to a modem and respond to incoming SMSs.

The program is called `pygsm_demo` and it takes as arguments:
* The device the modem is connected to. E.g. `/dev/ttyUSB0` or `/dev/ttyACM0`
* Modem configuration settings

The following will run the demo connecting to a MultiTech modem on `/dev/ttyUSB0`::

    > pygsm_demo /dev/ttyUSB0 baudrate=115200 rtscts=1

While running, the demo will show all the commands it is sending the modem. Output will look something like::

    pyGSM Demo App
      Port: /dev/ttyUSB0
      Config: {'baudrate': '115200', 'rtscts': '1'}

    Connecting to GSM Modem...
       debug Booting
       debug Connecting
       write 'ATE0\r'
        read '\r\n'
        read 'OK\r\n'
       write 'AT+CMEE=1\r'
        read '\r\n'
        read 'OK\r\n'
       write 'AT+WIND=0\r'
        read '\r\n'
        read 'OK\r\n'
       write 'AT+CSMS=1\r'
        read '\r\n'
        read '+CSMS: 1,1,1\r\n'
        read '\r\n'
        read 'OK\r\n'
       write 'AT+CMGF=0\r'
        read '\r\n'
        read 'OK\r\n'
       write 'AT+CNMI=2,2,0,0,0\r'
        read '\r\n'
        read 'OK\r\n'
       write 'AT+CMGL=0\r'
        read '\r\n'
        read 'OK\r\n'
    Waiting for incoming messages...
       write 'AT\r'
        read '\r\n'
        read 'OK\r\n'
       write 'AT+CMGL=0\r'
        read '\r\n'
        read 'OK\r\n'

Test RapidSMS
+++++++++++++
The following commands create a test project (remember to replace `rapidsms` with the folder that has your RapidSMS source code in it from step 5 above)::

    > mdkir ~/rapidsms-projects
    > cd ~/rapidsms-projects
    > rapidsms startproject test-project
    > cd ~/rapidsms-projects/test-project
    > cp -a /usr/local/rapidsms/apps/* ./apps/
    > cp rapidsms.ini.example rapidsms.ini
    > chmod a+x ./manage.py
    > ./manage.py syncdb
    > ./manage.py route &
    > ./manage.py runserver &


Now open a browser and connect to http://localhost:8000

You should see a RapidSMS dashboard.
