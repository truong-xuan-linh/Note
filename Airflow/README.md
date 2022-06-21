Step 1: Update the System.
```
apt-get update
```
Step 2: Install the required packages.
```
apt-get install software-properties-common
apt-add-repository universe
```
Update the packages.
```
apt-get update
```
Step 3: Install the python3-pip.
```
apt-get install python-setuptools
apt install python3-pip
```
Step 4: Install the required dependencies for Apache Airflow.
```
apt-get install libmysqlclient-dev
apt-get install libssl-dev
apt-get install libkrb5-dev
```
Step 5: Install the Apache-Airflow on system.
```
Install the python-virtual Environment.
apt install python3-virtualenv
virtualenv airflow_example
```
Change the directory.
```
cd airflow_example/bin/
```
Activate the source.
```
source activate
```
Now ,install the apache-airflow.
```
export AIRFLOW_HOME=~/airflow
pip3 install apache-airflow
```
Install typing_extension.
```
pip3 install typing_extensions
```
Run the following command.
```
airflow db init
```

Step 6: Set the Apache-Airflow login credentials for airflow web interface.
```
airflow users create --username admin --firstname FIRST_NAME --lastname  LAST_NAME --role Admin --email admin@example.org
```
For example:
```
airflow users create --username admin --firstname admin --lastname testing --role Admin --email admin@domain.com
```
Step 7: Start the Apache-Airflow web interface.
```
airflow webserver -p 8080
```
