# Python project for processing Admin Monitoring emails in RabbitMQ and save them to a specified directory.
# Classification (U)

# Description:
  Python program that processes administration monitoring emails in RabbitMQ.  The program will process a number of different data types in the report, and will be able to save the report to a specified directory.


###  This README file is broken down into the following sections:
 * Features
 * Prerequisites
 * Installation
 * Configuration
 * Running
 * Program Help Function
 * Testing
   - Unit
   - Integration
   - Blackbox


# Features:
 * Process monitoring reports in RabbitMQ and write the reports to a specified directory.
 * Run the monitor program as a service/daemon.
 * Setup the program up as a service.


# Prerequisites:
  * List of Linux packages that need to be installed on the server.
    - git
    - python-pip

  * Local class/library dependencies within the program structure.
    - lib/gen_class
    - lib/arg_parser
    - lib/gen_libs
    - rabbit_lib/rabbitmq_class


# Installation:

Install the project using git.
  * Replace **{Python_Project}** with the baseline path of the python program.

```
umask 022
cd {Python_Project}
git clone git@github.com:deepcoder42/rmq-sysmon.git
```

Install/upgrade system modules.

```
cd rmq-sysmon
sudo bash
umask 022
pip install -r requirements.txt --upgrade --system
exit
```

Install supporting classes and libraries.

```
pip install -r requirements-python-lib.txt --target lib --system
pip install -r requirements-rabbitmq-lib.txt --target rabbit_lib --system
```

# Configuration:

Create RabbitMQ configuration file.

```
chmod 777 logs message_dir tmp
cd config
cp rabbitmq.py.TEMPLATE rabbitmq.py
```

Make the appropriate changes to the RabbitMQ environment.
  * The "user", "passwd" and "host" is connection and host information to a RabbitMQ node.
    - user = "USER"
    - passwd = "PASSWORD"
    - host = "HOSTNAME"
    - exchange_name = "EXCHANGE_NAME"
      -> Name of the exchange that will be monitored.
    - to_line = "EMAIL_ADDRESS"|None
      -> Is the email address/email alias to the RabbitMQ administrator(s) or None if no emails required.
    - message_dir = "DIRECTORY_PATH/message_dir"
      -> Is where failed reports/messages are written to.
    - log_dir = "DIRECTORY_PATH/logs"
      -> Is where failed log files are written to.
  * Do not change these unless you are familar with RabbitMQ.
    - port = 5672
    - exchange_type = "direct"
    - x_durable = True
    - q_durable = True
    - auto_delete = False
  * The next entry is the queue_list.  This is a list of dictionaries.  Each dictionary within the list is the unique combination of queue name and routing key.  Therefore, each queue name and routing key will have its own dictionary entry within the list.  Make a copy of the dictionary for each combination and modify it for that queue/routing key setup.  Below is a break out of the dictionary.
    - "queue": "QUEUE_NAME"
      -> Name of queue to monitor.
    - "routing_key": "ROUTING_KEY"
      -> Name of the routing key for the queue.
      -> NOTE:  A single queue can have multiple routing keys, but each routing key will have it's own dictionary entry.
    - "directory": "DIRECTORY_PATH"
      -> Directory path to where a report will be written to.
    - "prename": ""
      -> A static pre-file name string.
      -> Default: "", nothing will be added to file name.
    - "postname": ""
      -> Default: "", nothing will be added to file name.
      -> A static post-file name string.
    - "key": ""
      -> Is the name of a key in a dictionary.  If the key is present then value will be part of the file name.
      -> Only applies if message is a dictionary.
      -> Default: "", nothing will be checked or added to file name.
    - "mode": "w"|"a"
      -> Write mode to the file: write or append to a file.
      -> Default: "a"
    - "ext": ""
      -> Extension name to the file name.
      -> Default: "", nothing will be added to file name.
    - "dtg": True|False
      -> Add a date and time group to the file name.
      -> Format is: YYYYMMDD_YYMMSS, example: 20200619_112012
      -> Default: False, no datetime group will be added to file name.
    - "date":  True|False
      -> Add a date to the file name.
      -> Format is: YYYYMMDD, example: 20200619
      -> Default: False, no date will be added to file name.
    - "stype": "any"|"dict"|"list"|"str"
      -> Format of the messages that are allowed in the message.  Only one type is allowed.
      -> Any - Any format is allowed, Dict - Dictionary, List - List, Str - String.
      -> Default: "any", allow all data types to be processed.
    - "flatten": True|False
      -> Flattens a dictionary format.
      -> Only applicable to dictionary format.
      -> Default: True, will flatten a dictionary.

```
vim rabbitmq.py
chmod 600 rabbitmq.py
```

(Optional)  Setup program to be ran as a service.

Modify the service script to change the variables to reflect the environment setup.
  * Change these entries in the rmq_2_sysmon_service.sh file.
    - BASE_PATH="PYTHON_PROJECT/rmq-sysmon"
    - USER_ACCOUNT="USER_NAME"
  * Replace **PYTHON_PROJECT** with the baseline path of the python program.
  * Replace **USER_NAME** with the userid which will execute the daemon and the account must be on the server locally.
  * MOD_LIBRARY is references the configuration file above (e.g. rabbitmq).

```
cp rmq_2_sysmon_service.sh.TEMPLATE rmq_2_sysmon_service.sh
vim rmq_2_sysmon_service.sh
```

Enable program as a service.
```
sudo ln -s PYTHON_PROJECT/rmq-sysmon/rmq_2_sysmon_service.sh /etc/init.d/rmq_2_sysmon
sudo chkconfig --add rmq_2_sysmon
sudo chown USER_NAME config/rabbitmq.py
```


# Running
  * Replace **{Python_Project}** with the baseline path of the python program.

### Running as a service.
  * Starting the service.

```
service rmq_2_sysmon start
```

  * Stopping the service.

```
service rmq_2_sysmon stop
```

### Running as a daemon.
  * Starting the daemon.

```
{Python_Project}/rmq-sysmon/daemon_rmq_2_sysmon.py -a start -c rabbitmq -d {Python_Project}/rmq-sysmon/config -M
```

  * Stopping the daemon.

```
{Python_Project}/rmq-sysmon/daemon_rmq_2_sysmon.py -a stop -c rabbitmq -d {Python_Project}/rmq-sysmon/config -M
```

### Running from the command line.
  * Stating the program.

```
{Python_Project}/rmq-sysmon/rmq_2_sysmon.py -c rabbitmq -d {Python_Project}/rmq-sysmon/config -M
```

  * Stopping the program.
```
<Ctrl-C>
```


# Program Help Function:

  All of the programs, except the command and class files, will have an -h (Help option) that will show display a help message for that particular program.  The help message will usually consist of a description, usage, arugments to the program, example, notes about the program, and any known bugs not yet fixed.  To run the help command:
  * Replace **{Python_Project}** with the baseline path of the python program.

```
`{Python_Project}/rmq-sysmon/rmq_2_sysmon.py -h`
```


# Testing:

# Unit Testing:

### Installation:

Install the project using git.
  * Replace **{Python_Project}** with the baseline path of the python program.
  * Replace **{Branch_Name}** with the name of the Git branch being tested.  See Git Merge Request.

```
umask 022
cd {Python_Project}
git clone --branch {Branch_Name} git@github.com:deepcoder42/rmq-sysmon.git
```

Install/upgrade system modules.

```
cd rmq-sysmon
sudo bash
umask 022
pip install -r requirements.txt --upgrade --system
exit
```

Install supporting classes and libraries.

```
pip install -r requirements-python-lib.txt --target lib --system
pip install -r requirements-rabbitmq-lib.txt --target rabbit_lib --system
```

### Testing:
  * Replace **{Python_Project}** with the baseline path of the python program.

```
cd {Python_Project}/rmq-sysmon
test/unit/rmq_2_sysmon/unit_test_run.sh
test/unit/daemon_rmq_2_sysmon/unit_test_run.sh
```

### Code coverage:
```
cd {Python_Project}/rmq-sysmon
test/unit/rmq_2_sysmon/code_coverage.sh
test/unit/daemon_rmq_2_sysmon/code_coverage.sh
```

# Integration Testing:

### Installation:

Install the project using git.
  * Replace **{Python_Project}** with the baseline path of the python program.
  * Replace **{Branch_Name}** with the name of the Git branch being tested.  See Git Merge Request.

```
umask 022
cd {Python_Project}
git clone --branch {Branch_Name} git@github.com:deepcoder42/rmq-sysmon.git
```

Install/upgrade system modules.

```
cd rmq-sysmon
sudo bash
umask 022
pip install -r requirements.txt --upgrade --system
exit
```

Install supporting classes and libraries.

```
pip install -r requirements-python-lib.txt --target lib --system
pip install -r requirements-rabbitmq-lib.txt --target rabbit_lib --system
```


# Configuration:
  * Please note that the integration testing will require access to a rabbitmq system to run the tests.

Create RabbitMQ configuration file.

```
chmod 777 tmp
cd test/integration/rmq_2_sysmon
chmod 777 logs message_dir sysmon
cd config
cp ../../../../config/rabbitmq.py.TEMPLATE rabbitmq.py
```

Make the appropriate changes to the RabbitMQ environment.
  * Change these entries in the rabbitmq.py file.  The "user", "passwd", and "host" variables are the connection information to a RabbitMQ node, the other variables use the "Change to" settings.
    - user = "USER"
    - passwd = "PASSWORD"
    - host = "HOSTNAME"
    - exchange_name = "EXCHANGE_NAME"            -> Change to:  exchange_name = "intr-test"
    - to_line = "EMAIL_ADDRESS"                  -> Change to:  to_line = None
    - message_dir = "DIRECTORY_PATH/message_dir" -> Change to:  message_dir = "message_dir"
    - log_dir = "DIRECTORY_PATH/logs"            -> Change to:  log_dir = "logs"
  * Have one entry in the queue_list list:
    - "queue_name":                              -> Change value to:  "intr-test"
    - "routing_key":                             -> Change value to:  "intr-test"
    - "directory":                               -> Change value to:  "sysmon"
    - "postname":                                -> Change value to:  "\_pkgs"
    - "key":                                     -> Change value to:  "Server"
    - "ext":                                     -> Change value to:  "json"
    - "stype":                                   -> Change value to:  "dict"

```
vim rabbitmq.py
chmod 600 rabbitmq.py
```

### Testing:
  * Replace **{Python_Project}** with the baseline path of the python program.

```
cd {Python_Project}/rmq-sysmon
test/integration/rmq_2_sysmon/integration_test_run.sh
```

### Code coverage:
```
cd {Python_Project}/rmq-sysmon
test/integration/daemon_rmq_2_sysmon/code_coverage.sh
```


# Blackbox Testing:

### Installation:

Install the project using git.
  * Replace **{Python_Project}** with the baseline path of the python program.
  * Replace **{Branch_Name}** with the name of the Git branch being tested.  See Git Merge Request.

```
umask 022
cd {Python_Project}
git clone --branch {Branch_Name} git@github.com:deepcoder42/rmq-sysmon.git
```

Install/upgrade system modules.

```
cd rmq-sysmon
sudo bash
umask 022
pip install -r requirements.txt --upgrade --system
exit
```

Install supporting classes and libraries.

```
pip install -r requirements-python-lib.txt --target lib --system
pip install -r requirements-rabbitmq-lib.txt --target rabbit_lib --system
```

# Configuration:

Create RabbitMQ configuration file.

```
chmod 777 tmp
cd test/blackbox/rmq_2_sysmon
chmod 777 logs message_dir sysmon
cd config
cp ../../../../config/rabbitmq.py.TEMPLATE rabbitmq.py
```

Make the appropriate changes to the RabbitMQ environment.
  * Replace **{PYTHON_PROJECT}** with the baseline path of the python program.
  * Change these entries in the rabbitmq.py file.  The "user", "passwd", and "host" variables are the connection information to a RabbitMQ node, the other variables use the "Change to" settings.
    - user = "USER"
    - passwd = "PASSWORD"
    - host = "HOSTNAME"
    - exchange_name = "EXCHANGE_NAME"            -> Change to:  exchange_name = "blackbox-test"
    - to_line = "EMAIL_ADDRESS@DOMAIN_NAME"      -> Change to:  to_line = None
    - message_dir = "DIRECTORY_PATH/message_dir" -> Change to:  message_dir = "{PYTHON_PROJECT}/test/blackbox/rmq_2_sysmon/message_dir"
    - log_dir = "DIRECTORY_PATH/logs"            -> Change to:  log_dir = "{PYTHON_PROJECT}/test/blackbox/rmq_2_sysmon/logs"
  * Have one entry in the queue_list list:
    - "queue_name":                              -> Change value to:  "blackbox-test"
    - "routing_key":                             -> Change value to:  "blackbox-test"
    - "directory":                               -> Change value to:  "{PYTHON_PROJECT}/test/blackbox/rmq_2_sysmon/sysmon"
    - "key":                                     -> Change value to:  "Server"
    - "postname":                                -> Change value to:  "\_pkgs"
    - "ext":                                     -> Change value to:  "json"
    - "stype":                                   -> Change value to:  "dict"

```
vim rabbitmq.py
chmod 600 rabbitmq.py
```

### Testing:
  * Replace **{Python_Project}** with the baseline path of the python program.

```
cd {Python_Project}/rmq-sysmon
test/blackbox/rmq_2_sysmon/blackbox_test.sh
```

