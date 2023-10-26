What is Debezium?
Debezium is a set of distributed services that capture row-level changes in your databases so that your applications can see and respond to those changes. Debezium records in a transaction log all row-level changes committed to each database table. Each application simply reads the transaction logs they’re interested in, and they see all of the events in the same order in which they occurred.

What is Change Data Capture?
Change Data Capture, or CDC, is an older term for a system that monitors and captures the changes in data so that other software can respond to those changes. Data warehouses often had built-in CDC support, since data warehouses need to stay up-to-date as the data changed in the upstream OLTP databases.

Debezium is essentially a modern, distributed open source change data capture platform that will eventually support monitoring a variety of database systems.

![alt text]([https://github.com/Lord-of-the-Pods/debezium-minikube/blob/main/images/debezium-architecture.png](https://github.com/Lord-of-the-Pods/debezium-minikube/blob/fb52c1de8eac288c5cd51da56f0da0d1de3afc93/images/debezium-architecture.png)?raw=true)


1. Start your minikube cluster

```
minikube start
```

2. Create a new Namespace for your debezium example setup .

```
kubectl create ns debezium-example
```

3. Deploying Strimzi Operator
   For the Debezium deployment we will use Strimzi, which manages the Kafka deployment on Kubernetes.
   The simplest way for installing Strimzi is through the Operator Lifecycle Manager (OLM). 
   If you don’t have OLM installed on your cluster yet, you can install it by running the following command: 

```  
curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.20.0/install.sh | bash -s v0.20.0
```

   Now, install Strimzi operator itself:

```
$ kubectl create -f https://operatorhub.io/install/strimzi-kafka-operator.yaml
```

4. Next, deploy a (single-node) Kafka cluster:
  
https://github.com/Lord-of-the-Pods/debezium-minikube/blob/1e68a1118b1366ca6d5acb6e4182ab03a794e084/yamls/kafka.yaml#L1-L44

5. Deploying a Data Source
   As a data source, MySQL will be used in the following. Besides running a pod with MySQL, 
   an appropriate service which will point to the pod with DB itself is needed. It can be created e.g. as follows:

   5.1. Create a Mysql Deployment :
   
      https://github.com/Lord-of-the-Pods/debezium-minikube/blob/7ed3d3634bc14441307a19bab84be1b7a109ce77/yamls/mysql/deployment.yaml#L1-L28

   5.2. Create a Service for MySql

      https://github.com/Lord-of-the-Pods/debezium-minikube/blob/7ed3d3634bc14441307a19bab84be1b7a109ce77/yamls/mysql/service.yaml#L1-L10


6. Kafka Connect

https://github.com/Lord-of-the-Pods/debezium-minikube/blob/636ae6f5c7e26c87b6e39c0fc608e0c08daea394/yamls/KafkaConnect.yaml#L1-L31

7. Kafka Connector :

https://github.com/Lord-of-the-Pods/debezium-minikube/blob/7ed3d3634bc14441307a19bab84be1b7a109ce77/yamls/KafkaConnector.yaml#L1-L20

8. Verifying the Deployment
  8.1. To verify the everything works fine, you can e.g. start watching mysql.inventory.customers Kafka topic:

```
$ kubectl run -n debezium-example -it --rm --image=quay.io/debezium/tooling:1.2  --restart=Never watcher -- kcat -b debezium-cluster-kafka-bootstrap:9092 -C -o beginning -t mysql.inventory.customers
```

  8.2. Connect to the MySQL database:

```
$ kubectl run -n debezium-example -it --rm --image=mysql:8.0 --restart=Never --env MYSQL_ROOT_PASSWORD=debezium mysqlterm -- mysql -hmysql -P3306 -uroot -pdebezium
```

  Do some changes in the customers table:
```
sql> update customers set first_name="Sally Marie" where id=1001;
```
