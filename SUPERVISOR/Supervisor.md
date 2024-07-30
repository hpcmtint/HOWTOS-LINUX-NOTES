What is Supervisor
------------------

It is a client/server system that allows users to control several processes running on a UNIX-like operating system. It is a free and open-source process management system. However, It restarts the processes if it got crashes for any reason.

To know more about the supervisor and its various version releases [Click here!](http://supervisord.org/ "Click here!")

### Step 1- Update all the packages

### Step 2- Install Supervisor

By default supervisor package is present in the ubuntu 20.04 repository. Install it with the below-mentioned command



[](https://cloudkul.com/blog/wp-content/uploads/2023/01/image-27.png)

After installing it you can check its version by running the below command

[](https://cloudkul.com/blog/wp-content/uploads/2023/01/image-28.png)

Verify the status of the service by running the below command



[](https://cloudkul.com/blog/wp-content/uploads/2023/01/image-29.png)

### Step 3- Manage the Nginx process with the supervisor

First, we need to install the nginx server with the below command

You must halt and disable the nginx service after installing it since we will use a supervisor to manage the nginx process.

You can stop and disable the nginx service with the following command:



### Step 4- Create a conf file under the supervisor to serve nginx process

Now, for each service that you wish to manage, you must generate a separate configuration file. The following command will generate an nginx configuration file:



Add the following lines:



After adding the above lines to the file. Now save and close the file.

Next, tell the supervisor to know about the new config:

You should get below output

[](https://cloudkul.com/blog/wp-content/uploads/2023/01/image-30.png)

Next, tell the supervisor to update its configuration:

[](https://cloudkul.com/blog/wp-content/uploads/2023/01/image-31.png)

To verify whether the supervisor has started the nginx service or not run the below command

You should get the below output

[](https://cloudkul.com/blog/wp-content/uploads/2023/01/image-33.png)

To stop the nginx service run the below command



[](https://cloudkul.com/blog/wp-content/uploads/2023/01/image-34.png)

To start the nginx service run the below command



[](https://cloudkul.com/blog/wp-content/uploads/2023/01/image-35.png)

You can also verify the nginx process by the below command

[](https://cloudkul.com/blog/wp-content/uploads/2023/01/image-36.png)

### For Magento 2 Elastic search, please follow the –

###### Our Cloudkul Blogs

**[Elasticsearch, Fluentd, and Kibana (EFK)](https://cloudkul.com/features/elastic-search-fluentd-kibana/)** 

**[Setting up Elasticsearch, Logstash, and Kibana for centralized logging](https://cloudkul.com/blog/setting-elasticsearch-logstash-kibana-centralized-logging/)**

**[Managing and Monitoring Magento 2 logs with Kibana](https://cloudkul.com/blog/managing-and-monitoring-magento-2-logs-with-kibana/)**

###### Our store modules –

**[Magento 2 Elasticsearch](https://store.webkul.com/Magento2-Elasticsearch.html "Magento 2 Elasticsearch")**

**[EFK Setup for Magento 2](https://store.webkul.com/magento2-efk-setup.html "EFK Setup for Magento 2")**

**You may also visit our Magento development services and quality**  **[Magento 2 Extensions](https://store.webkul.com/Magento-2.html "Magento 2 Extensions")**.

**For further help or query, please [contact](https://cloudkul.com/contact/) us or raise a [ticket](https://webkul.uvdesk.com/en/customer/create-ticket/ "ticket").**

. . .
