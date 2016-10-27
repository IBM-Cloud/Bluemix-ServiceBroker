# Overview
In this "How to" I am going to explain the steps to register your own Service Broker on Bluemix Public, Dedicated and Local. Service Brokers are one option to extend Cloud Foundry using your own functionality and to integrate existing (on-premises or cloud) infrastructure and services.

##Table of Contents
* [Bluemix and Brokers](#bluemix-and-brokers)
* [Register And Manage Service Brokers in Bluemix](#register-and-manage-service-brokers-in-bluemix)
* [Documentation Links](#documentation-links)
* [Contact Information](#contact-information)

# Bluemix and Brokers

## Bluemix Public vs. Bluemix Dedicated and Bluemix Local
[IBM Bluemix allows to run applications on Cloud Foundry](https://console.eu-gb.bluemix.net/docs/overview/whatisbluemix.html). It comes in three flavors: Bluemix Public provides a shared cloud infrastructure whereas Bluemix Dedicated is a customer-specifc dedicated cloud. Lastly, Bluemix Local is a private cloud in the customer data center. Because of the delivery model the individual user privileges differ which may impact what kind of service brokers can be added to Bluemix.

## Brokers: Standard Private vs. Space-Scoped Private
Cloud Foundry Service Brokers distinguishes between Standard Private Brokers and Space-Scoped Private Brokers. The service offered by a space-scoped private brokers is only visible in the space where the broker has been registered. Services offered by standard private brokers can be made available to organizations. By default all services are private until enabled. Read here (until we expand this section...):
http://docs.cloudfoundry.org/services/managing-service-brokers.html

Regardless of the type of broker, there are some **important requirements**:

1. Each broker is accessible via an URL and this URL needs to be unique across the entire Cloud Foundry instance.
2. Each offered service has an ID and it needs to be unique the entire Cloud Foundry instance.
3. Each service plan has an ID and it needs to be unique the entire Cloud Foundry instance.  

Depending on the broker, the service ID and plan ID need to be changed in the source code or configuration file for that broker. In some cases the code already uses automatically generated [UUIDs](https://en.wikipedia.org/wiki/Universally_unique_identifier) to avoid possible errors.


# Register And Manage Service Brokers in Bluemix
Create, list, update and delete service brokers. Enable their service plans for use.

## Bluemix Public: Space-Scoped Private Brokers

To register your broker, run the following CLI command:   
`cf create-service-broker yourBrokerName userID password URL4yourBroker --space-scoped`   
It registers your broker in the current space.


## Bluemix Dedicated and Bluemix Local: Standard Private Brokers
Users of Bluemix Dedicated and Bluemix Local can register space-scoped private brokers. For the instructions see the previous section. In addition, users with administrator privileges to modify the catalog can create standard private brokers. Though Cloud Foundry documentation suggests to use the same command broker-related cf commands, Bluemix requires to use the BluemixAdminCLI 
Registration of private service

1. Make sure the BluemixAdminCLI plugin is installed
2. Use that plugin to manage the broker lifecycle. Its sub-commands are invoked either with `cf ba` or `cf bluemix-admin` followed by the specific command.

The following command registers the broker:
`cf ba add-service-broker yourBrokerName userID password URL4yourBroker`  

To remove an existing standard private service broker:
`cf ba delete-service-broker yourBrokerName`  
Only those brokers that don't have any related provisioned services can be deleted. 


# Documentation Links
You can find background material on the Cloud Foundry and Bluemix pages:
* Cloud Foundry Service Broker API: https://docs.cloudfoundry.org/services/api.html
* Bluemix instructions on how to manage the catalog, including how to register a service broker: https://new-console.eu-gb.bluemix.net/docs/admin/index.html#oc_catalog

# Contact Information
If you have found errors or some instructions are not working anymore, then please open an GitHub issue. 

You can find more tutorials and sample code at:
https://ibm-bluemix.github.io/
