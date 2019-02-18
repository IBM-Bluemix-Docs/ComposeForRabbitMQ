---

Copyright:
  years: 2017,2018
lastupdated: "2017-11-20"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:tip: .tip}

# Settings
{: #dashboard-settings}

These features allow you to adapt your {{site.data.keyword.composeForRabbitMQ_full}} service to better suit your needs and requirements.


## Upgrade Version

If there is a new version of the database available, a drop-down menu appears, allowing you to select which version you would like to upgrade to. Otherwise, your service is on the newest version available, and the panel displays the current version information.

![The Version panel](./images/rabbitmq-version-show.png "The Version panel")


## Scale Resources

If your service needs extra memory, or you want to reduce the amount of memory that is allocated to your service, you can do this by scaling resources.

1. Navigate to your service's dashboard overview page.
2. In the _Deployment Details_ pane, click **Scale Resources**. The Scale Resources page opens.
    ![The Scale Resources page](./images/rabbitmq-scale-show.png "The Scale Resources page")
3. Adjust the slider to raise or lower the memory that is allocated to the {{site.data.keyword.composeForRabbitMQ}} service. Move the slider to the left to reduce the amount of memory, or move it to the right to increase the memory.
4. Click **Scale Deployment** to trigger the rescaling and return to the dashboard overview. 

When the scaling is complete the _Deployment Details_ pane updates to show the current usage and the new value for the available memory.


## Change Password

You might find it necessary to change the password of your service. You can do so by using _Update Password_. 

A new, randomly generated password appears, or you can type your own password into the field. To regenerate another password, click the dice to the right of the field. 
  
![Updating the RabbitMQ password](./images/rabbitmq-update-password.png "The automatic password generator")

Click **Update Password**. You will be asked to confirm the change. Click **Update Password** in the dialog to confirm the new password, or cancel to cancel the change. The _Deployment Details_ pane will show the progress of the running job.

**Note:** Changing the password changes the credentials that you and your services use to connect, and invalidates your service's connection string. It can also result in downtime.

### Updating Connected Applications
Changing the password invalidates the existing connection string and generate a new one. This will cause a service interruption until connected applications are updated with the new connection string. You will have to do this by suppling the new connection string to your applications.

For more information about connecting your applications, see [Connecting an {{site.data.keyword.cloud}} Application](/docs/services/ComposeForRabbitMQ?topic=compose-for-rabbitmq-ibmcloud-cf-app) and [Connecting an external application](/docs/services/ComposeForRabbitMQ?topic=compose-for-rabbitmq-external-app).


## Using Whitelists

If you want to restrict access to your databases, you can whitelist specific IP addresses or ranges of IP addresses on your service. When there are no IP addresses in the whitelist, the whitelist is disabled and the deployment will accept connections from any system on the internet.

![Whitelisting IPs](./images/rabbitmq-whitelist-show.png "The whitelist fields.")

### IP Addresses
The *IP* field can take a single complete IPv4 address or IPv6 address with or without a netmask. Without a netmask, incoming connections must come from exactly that IP address. 

Note that although the IP entry allows for IPv6, no Compose deployments are currently available to IPv6 networking and so these addresses cannot be filtered on.

### Netmasks
To allow a connection from a specified range of IP addresses, use a netmask. The IP address must be fully specified when using a netmask. That means entering, for example, 192.168.1.0/24 rather than 192.168.1/24.

### Description
The *Description* can be any user-significant text for identifying the whitelist entry - a customer name, project identifier or employee number, for example. The description field is required.

### Compose Services
Whitelist entries are automatically added to Compose's servers to allow them to connect.

### Removal
To remove an IP address or netmask from the Whitelist, click the *Remove* entry displayed next to it.
When all entries on the whitelist are removed, the whitelist will be disabled and all IP addresses will be accepted by the TCP access portals.
