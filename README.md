# Private Brokers and Bluemix - Getting Started
In this "Getting Started" we are going to explain the steps to register your own Service Broker on IBM Bluemix Public, Dedicated and Local. Service Brokers are one option to extend Cloud Foundry using your own functionality and to integrate existing (on-premises or cloud) infrastructure and services. The repository contains code for a very simple broker, our sample broker. Once deployed as a Bluemix app, it can be used to create/delete a broker, provision a pseudo service through that broker, bind and unbind it to/from an application, and to delete the service again. Neither our code nor the instructions below will touch topics such as managing service plans or how to enable certain service plans to organizations.

## Table of Contents
* [Bluemix and Brokers](#bluemix-and-brokers)
* [Register And Manage Service Brokers in Bluemix](#register-and-manage-service-brokers-in-bluemix)
* [Example: Manage a Simple Broker](#example-manage-a-simple-broker)
* [Documentation Links](#documentation-links)
* [Contact Information](#contact-information)

# Bluemix and Brokers
If you have already looked at [IBM Bluemix](http://ibm.com/bluemix) and Service Brokers, you will have noticed that there are different flavors of both Bluemix and of brokers. So let's take a look at them.

## Bluemix Public vs. Bluemix Dedicated and Bluemix Local
[IBM Bluemix allows to run applications on Cloud Foundry](https://console.eu-gb.bluemix.net/docs/overview/whatisbluemix.html). It comes in three flavors: Bluemix Public provides a shared cloud infrastructure whereas Bluemix Dedicated is a customer-specifc dedicated cloud. Lastly, Bluemix Local is a private cloud in the customer data center.

The Bluemix service catalog is based on Cloud Foundry. [Using brokers new services can be published to the Bluemix catalog and provisioned by users](http://docs.cloudfoundry.org/services/overview.html). Because of the Bluemix delivery model (Public/Dedicated/Local) the individual user privileges differ which may impact what kind of service brokers can be added to Bluemix.

## Brokers: Standard Private vs. Space-Scoped Private
Cloud Foundry Service Brokers distinguishes between Standard Private Brokers and Space-Scoped Private Brokers. The service offered by a space-scoped private brokers is only visible in the space where the broker has been registered. Services offered by standard private brokers can be made available to organizations. By default all services are private until enabled.

Read more about brokers in the Cloud Foundry documentation (until we expand this section...):
http://docs.cloudfoundry.org/services/managing-service-brokers.html

Regardless of the type of broker, there are some **important requirements**:

1. Each broker is accessible via an URL and this URL needs to be unique across the entire Cloud Foundry instance.
2. Each offered service has an ID and it needs to be unique the entire Cloud Foundry instance.
3. Each service plan has an ID and it needs to be unique the entire Cloud Foundry instance.  
4. If provided, then the ID for the service dashboard needs to be unique, too.

Depending on the broker, the service ID and plan ID need to be changed in the source code or configuration file for that broker. In some cases the code already uses automatically generated [UUIDs](https://en.wikipedia.org/wiki/Universally_unique_identifier) to avoid possible errors.

# Register And Manage Service Brokers in Bluemix
Create, list, update and delete service brokers. Enable their service plans for use.

Cloud Foundry provides an [API for managing service brokers](http://docs.cloudfoundry.org/services/api.html). It defines mandatory and optional data is exchanged between Cloud Foundry and the broker. Bluemix uses that Cloud Foundry API and details which of the optional data it would like to have in order for the Bluemix UI to work properly. The [Bluemix documentation on managing the catalog has a description and a sample](https://console.eu-gb.bluemix.net/docs/admin/index.html#oc_catalog).


## Bluemix Public: Space-Scoped Private Brokers

To register your broker, run the following CLI command. "yourBrokerName" is a name you give to your broker, userID/password are those to access the broker and URL4yourBroker is the address under which the broker is accessible to the Bluemix/Cloud Foundry cloud controller (the piece of software managing the services and more):   
`cf create-service-broker yourBrokerName userID password URL4yourBroker --space-scoped`   

It registers your broker in the current space. Because the catalog in the Bluemix Console (the Web interface) is managed and shown for the organization, services offered through space-scoped private brokers are not listed. They are however visible in the Bluemix marketplace accessible via the CLI:   
`cf marketplace`   

The above command lists all services available to you, including the service brought in by the space-scoped private broker.

Another option to see whether the broker has been successfully created is to list the brokers:   
`cf service-brokers`

To use a service offered via a broker it needs to be provisioned. This is done by creating a service instance of a specific service and one of its offered plans. "yourServiceName" is the name you want to give to the service:   
`cf create-service your-offered-service selected-plan yourServiceName`

A provisioned service can be removed by deleting it, referencing its name:   
`cf delete-service yourServiceName`   

If you want to get rid of the broker, simply delete it:   
`cf delete-service-broker yourBrokerName`   
Note that only brokers which do not have any associated services provisioned (read: "the service is in use") can be deleted.


## Bluemix Dedicated and Bluemix Local: Standard Private Brokers
Users of Bluemix Dedicated and Bluemix Local can register space-scoped private brokers. For the instructions see the previous section. In addition, users with administrator privileges to modify the catalog can create standard private brokers. Though Cloud Foundry documentation suggests to use the same command broker-related cf commands, Bluemix requires to use the BluemixAdminCLI 
Registration of private service

1. Make sure the Bluemix-Admin CLI plugin is installed. See the [Bluemix Documentation on the admin plugin](https://console.eu-gb.bluemix.net/docs/cli/plugins/bluemix_admin/index.html) for instructions.   
2. Use that plugin to manage the broker lifecycle. Its sub-commands are invoked either with `cf ba` or `cf bluemix-admin` followed by the specific command.

The following command registers the broker. Similar to above, "yourBrokerName" is a name you give to your broker, userID/password are those to access the broker and URL4yourBroker is the address under which the broker is accessible to Bluemix:   
`cf ba add-service-broker yourBrokerName userID password URL4yourBroker`  

To remove an existing standard private service broker:
`cf ba delete-service-broker yourBrokerName`  
Only those brokers that don't have any related provisioned services can be deleted. 

List registered brokers:   
`cf ba service-brokers`   

Services offered by the broker can be provisioned, i.e., an instance of it created, in the same way as for space-scoped private brokers. See above for the instructions.


# Example: Manage a Simple Broker
In this section we are going to explain how to work with the sample broker included in this repository.

The code for the sample broker consists of a single file only (bmx-sample-broker.py). The broker is written in Python and the file contains functionality to handle all the requests from Bluemix to try out the lifecycle of a private broker. The code is kept as simple as possible. Once the sample broker has been created, it advertises a "Pseudo Service" ("pseudo-service") in the Bluemix catalog. The service has two plans, "small" and "big", that can be provisioned. The provided code does not need any configuration because generated UUIDs are used for the service and plan IDs.

### Download and Deploy the Sample Broker
1) Clone or download and unpack this repository.  
2) Change into the directory with the cloned/unpacked files.   
3) Login to Bluemix and push the application:    
   `cf push myBroker`   
   "myBroker" is the name you want to give to the broker. No other configuration necessary :)   
4) Now you should have a new Bluemix/Cloud Foundry application. This is YOUR new private broker. Start testing, see the next block of instructions.
### Manage the Broker
Once you have your broker up and running, let's try to use it. For the following commands we assume that the broker was deployed with the name "myBroker". "broker-address" refers to the specific address of where your deployed broker is accessible, i.e. the URL for the app. It could be "http://mybroker.ng.mybluemix.net" or "http://mybroker.eu-gb.mybluemix.net" or something entirely different, depending on your specific Bluemix or Cloud Foundry environment.

#### Bluemix Public
If you are on Bluemix Public, your only option is to use the sample broker as "space-scoped private broker". The instructions are as already described above:

* Register the sample broker as space-scoped private broker:   
   `cf create-service-broker yourBrokerName test test broker-address --space-scoped`
* List Service Broker:   
   `cf service-brokers`
* Create an instance of the advertised "pseudo-service" and name it "myservice", choose the small plan:   
   `cf create-service pseudo-service small myservice`
* Delete the provisioned service:   
   `cf delete-service myservice`
* Remove the broker:   
   `cf delete-service-broker yourBrokerName`



#### Bluemix Dedicated or Bluemix Local
Depending on whether you are catalog administrator or regular user, you can either register the sample broker as "standard private broker" or only as space-scoped broker. If the latter, see the instructions in the previous section.

Make sure that the BluemixAdminCLI plugin for the "cf" tool is installed. 

* Register the sample broker  
   `cf ba add-service-broker yourBrokerName test test broker-address`
* List Service Broker   
   `cf ba service-brokers`
* Create an instance of the advertised "pseudo-service" and name it "myservice", choose the small plan:   
   `cf create-service pseudo-service small myservice`
* Delete the provisioned service:   
   `cf delete-service myservice`
* Remove the broker:   
   `cf ba delete-service-broker yourBrokerName`


# Documentation Links
You can find background material on the Cloud Foundry and Bluemix pages:
* Cloud Foundry Service Broker API: https://docs.cloudfoundry.org/services/api.html
* Bluemix instructions on how to manage the catalog, including how to register a service broker: https://new-console.eu-gb.bluemix.net/docs/admin/index.html#oc_catalog

# Contact Information
If you have found errors or some instructions are not working anymore, then please open an GitHub issue. 

You can find more tutorials and sample code at:
https://ibm-bluemix.github.io/
