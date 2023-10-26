What is Debezium?
Debezium is a set of distributed services that capture row-level changes in your databases so that your applications can see and respond to those changes. Debezium records in a transaction log all row-level changes committed to each database table. Each application simply reads the transaction logs they’re interested in, and they see all of the events in the same order in which they occurred.

What is Change Data Capture?
Change Data Capture, or CDC, is an older term for a system that monitors and captures the changes in data so that other software can respond to those changes. Data warehouses often had built-in CDC support, since data warehouses need to stay up-to-date as the data changed in the upstream OLTP databases.

Debezium is essentially a modern, distributed open source change data capture platform that will eventually support monitoring a variety of database systems.



![alt text](https://github.com/Lord-of-the-Pods/debezium-minikube/blob/main/images/debezium-architecture.png?raw=true)
