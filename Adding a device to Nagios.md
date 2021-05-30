---
tags: [github]
title: Adding a device to Nagios
created: '2021-05-30T21:49:41.173Z'
modified: '2021-05-30T21:58:26.678Z'
---

## ***Adding a device to Nagios***

In the next two sections, we will be showing you how you can add a device for Nagios to check. This process is relatively simple but does involve modifying a couple of configuration files.

### ***Adding your Host to Nagios***

In this section, we will be showing you how to add your host to a configuration file that Nagios will read.

To do this, we will need to create a configuration file where we will define the various details of the device we want to monitor.

### **1. Let us start by changing into the “objects” configuration folder for Nagios.***

This folder, by default, contains some sample configuration files. These files can be useful for seeing how to configure Nagios.

      cd /usr/local/nagios/etc/objects

### **2. Let us now create a config file where we will specify our host’s configuration.**

For this example, we will be calling this file pimylifeuphost.cfg, however its best to use a name that best describes your device.

      sudo nano pimylifeuphost.cfg

### **3. Within this file, we need to enter the following lines.**

You will need to make sure that you specify the IP address of the device you want to perform checks on.

      define host {
          use                     linux-server            ; Host group to use
          host_name               Pi My Life Up Host      ; Name of this host
          alias                   pimylifeuphost          ; Alias
          address                 [YOURHOSTIPADDRESS]     ; IP Address
      }

This basic configuration will allow us to at least monitor whether the device is online by sending a ping request to it.

### **4. Once done, save the file by pressing CTRL + X, followed by Y, then ENTER**

### **5. Next, we need to modify our Nagios configuration so that it knows to read our new config file.**

Begin modifying the config file by running the following command.

      sudo nano /usr/local/nagios/etc/nagios.cfg

### **6. Within this file, find the following line and add our new config line below it.**

Find

      cfg_file=/usr/local/nagios/etc/objects/templates.cfg

Add Below

      cfg_file=/usr/local/nagios/etc/objects/pimylifeuphost.cfg

Now when we restart Nagios, it will know to read this file and load in our settings defined within it.

### **7. Save the configuration file by pressing CTRL + X, followed by Y, then ENTER**

### **8. We can now restart the Nagios service on our Raspberry Pi by using the command below.**

      sudo systemctl restart nagios

### **9. You should now be able to see your new host by going to the “Hosts” page on the Nagios dashboard.**

      Nagios Listing Available Hosts

Currently, Nagios will only be doing a simple ping check to see whether the hosts are online.

In our next section, we will be showing you how you can add services to the host to check things like the status of SSH or its web interface.


## **Adding a Service to your Host**

In this section, we will be showing you how to add a couple of additional checks to your host.

For this guide, we will be setting up two services that will use the check_http and check_ssh commands on our host.

Both commands can be used to do a fair amount, but we will be using their most basic usages for our tutorial.

### **1. To be able to add these new commands, we need to go and modify the host file we created earlier.***

We can begin modifying this file by running the following command on our Raspberry Pi.

    sudo nano /usr/local/nagios/etc/objects/pimylifeuphost.cfg

### **2. Within this file, we will need to add a new section to the bottom that defines our services.**

Each service must be defined in a separate block, so for this tutorial, we will be creating two of these blocks.

      define service {
          use                     local-service
          host_name               Pi My Life Up Host
          service_description     SSH
          check_command           check_ssh
      }

      define service {
          use                     local-service
          host_name               Pi My Life Up Host
          service_description     HTTP
          check_command           check_http
      }

With these two blocks, Nagios will automatically check the status of the SSH and HTTP ports running on our specified host.

### **3. Once you have finished adding the new blocks, you can save the file by pressing CTRL + X, then Y, followed by the ENTER key.**

### **4. We should now verify that our configuration changes are valid. We can do that by running the following command.**

This command will tell the Nagios software to verify the contents of the configuration files.

    sudo /usr/local/nagios/bin/nagios -v /usr/local/nagios/etc/nagios.cfg

If everything has been entered correctly, you will see the text “Things look okay” appear in the command line.

### **5. We can now restart Nagios so that our changes get loaded in on our Raspberry Pi.**

    sudo systemctl restart nagios

### **6. Now when you open the Nagios web interface, go to the “Services” menu.**

Within this page, you will be able to see the status of your SSH and HTTP services of your new host.

Hopefully, at this stage, you will now have the Nagios software up and running on your Raspberry Pi.

You can learn more about how to use Nagios by checking out the official Nagios Core documentation. ( https://assets.nagios.com/downloads/nagioscore/docs/nagioscore/4/en/quickstart.html )

