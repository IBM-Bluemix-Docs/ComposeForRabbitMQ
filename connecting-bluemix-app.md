---

copyright:
  years: 2016,2018
lastupdated: "2017-06-16"

subcollection: compose-for-rabbitmq

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Connecting an {{site.data.keyword.cloud_notm}} application
{: #ibmcloud-cf-app}

To connect an {{site.data.keyword.cloud}} application to your service, use the service credentials that are created when the service is provisioned.

## Connecting using the 'Hello World' sample app

The [sample app](https://github.com/IBM-Bluemix/compose-rabbitmq-helloworld-nodejs) demonstrates how to use Node.js to connect to a {{site.data.keyword.composeForRabbitMQ}} service by using the provided credentials. The application creates, reads from, and writes to a database

Download the sample app and follow the instructions in the readme file. Then, in your application details page in {{site.data.keyword.cloud_notm}}, click **View APP** to view the contents of the *examples* table.

## Available credentials

Field Name|Description
----------|-----------
`uri`|The URI to be used when connecting to the service. Includes the schema (`amqps:), admin user name and password, host name of server, port number to connect to and vhost name.
`uri_direct_1`|A secondary URI that can be used when connecting to the service. The format is the same as `uri`.
`uri_admin`|A URI that should be visited in a browser to access the database's administration interface. Access requires the admin user name and password from the `uri` field.
`uri_admin_1`|An alternative administration URI - see `uri_admin`.
`ca_certificate_base64` `(optional)`|A base64 encoded, self-signed certificate that is used to confirm that an application is connecting to the appropriate server. The certificate is only present on services that have a self-signed instead of a Let's Encrypt certificate. You need to decode the key before you can use it, as shown in the sample application.
`deployment_id`|An internal identifier for the service as created within Compose.
`db_type`|The type of database that is offered by the service; in this case `rabbitmq`.
`name`|The database deployment name.
{: caption="Table 1. Compose for RabbitMQ credentials" caption-side="top"}

**Note:** Two `haproxy` portals provide access to the RabbitMQ service. Both `uri` and `uri_direct_1` can be used to connect. In your applications, switch between `uri` and `uri_direct_1` to manage responses to connection failures.
