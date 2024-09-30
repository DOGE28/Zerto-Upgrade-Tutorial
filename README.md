# Zerto ZVM Upgrade Documentation and Tutorial

This tutorial will walk you through the necessary steps to upgrade your current Windows ZVM to the newest Linux ZVM appliance.
The versions we suggest upgrading from/to is Zerto 9.7 U4 (any patch) to Zerto 10.0 U2. Zerto requires there be at most a two version gap when upgrading. Please upgrade your existing Windows ZVM to this version before continuing.

> [!IMPORTANT]
> Before proceeding, please consult with the [Zerto Compatibility Matrix](https://www.zerto.com/myzerto/support/interoperability-matrix/) to ensure that your environment is compatible with the upgrade process

> [!CAUTION]
> This migration is only possible using vCenter and vCenter Cloud Director. If you are using a different hypervisor, this upgrade path WILL NOT be possible for you. Additional platforms will be supported in future migration tool releases.

## Pre-Migration

Before we begin, there are a few considerations we must take into account.

* This is a full migration from Windows to Linux, and is achieved through the use of a migration tool that Zerto provides. The Windows ZVM can be decommissioned once the migration has been completed and verified.
* If your ZVM currently uses an external database you will need to ensure the Linux appliance can connect to it before beginning the migration. (Steps to verify are included here)
* There will be a total of three IPs needed as part of this migration. These must be in the same subnet and allow interconnectivity between them all.

### External Database Connection

If your ZVM does not use an external database, you may skip to the next section.

If your ZVM does use an external database, you will need to log into your database as an administrator and ensure there is a local account with the System Administrator (SA) role and take note of its credentials.

You will then go into your current Windows ZVM, then using the search bar near the start icon look for "Zerto Diagnostic Tool". Start this, then look for the "Change SQL Server Credentials" option.

> [!Note]
> The diagnostic tool can also be found at: `C:\Program Files\Zerto\Zerto Virtual Replication\Diagnostics\ ZertoDiagnostics.exe`

![alt text](image-1.png)

Follow the steps and make sure that your Windows ZVM can connect using the SA credentials. Once verified, you may move on to the next step.

### IP Addresses

You will need a total of three IPs to perform the migration; the IP currently being used by the Windows ZVM, one for the Linux ZVM, and a floater used during the migration.

These must all be in the same subnet and be allowed to connect to each other. Now is the time to ensure there are no firewall rules preventing communication within the subnet you plan to use. Consult your IP management data to avoid any conflicts. We also used the `ping` command to make sure that the IPs we wanted to use were not being used by another device before commiting to using them. 

>[!TIP]
> If you already have the Linux ZVM set up, you can run a `ping` command to ensure connectivity to the Windows ZVM.

Keep the IPs you've found somewhere close by as they'll be used shortly.

During the migration, the Linux ZVM will steal the Windows IP, then the Windows ZVM will be given the floater IP. The original Linux ZVM IP will no longer be used immediately after migration.

### Linux ZVM Setup

The Linux ZVM appliance VM must be setup prior to using the migration tool. This includes the following:

* Deploying the VM
* Setting up networking
* Making sure SSH is enabled

Deploying the VM is as simple as downloading the [Zerto 10.0 U2 ZVM OVF](https://www.zerto.com/myzerto/support/downloads/) from the support site, and deploying it as any other VM.

>[!WARNING]
> We choose not to change any of the preconfigured settings when deploying the OVF. If you do change anything please know that you are deviating from this tutorial and Zerto support may be the only ones that can fix any potential issues. Consult the Zerto documentation for options that are safe to change.

Once deployment is complete, you may power it on. 

You may now connect to the ZVM in your web browser at  ht<span>tps://</span>ZVM-IP  to verify it was deployed correctly. The port :9669 is no longer needed.

The ZVM comes with a preconfigured user specifically for the ZVM:

* ZVM User/Password
    - admin
    - admin

You will be asked to change the default password when you first log in.

The VM comes with a preconfigured Linux user:

* Linux User/Password (Used for SSH)
    - zadmin
    - Zertodata123!



Open up a console in the VM and log in using the above credentials.
You will be asked to change the password before proceeding.

You will then be greeted by the appliance manager menu.

![alt text](image-2.png)

You will want to enter number 2, then 2 again to configure a static IP. Fill out the necessary information and verify that it can connect to the internet. You can do this by entering 0 on the main manager menu to exit to shell, then use `ping google.com` to make sure packets are making it out to the internet and DNS is working correctly.

You can go back to the appliance manager menu from the shell by typing `app` and tab-completing to the `appliance-manager` command.

Next, enter number 7 and enable SSH. You will want to confirm that SSH is working by using the `ssh zadmin@<ZVM-IP>` command and logging in. If you ever need to change the password for this account in the future, you may exit to shell and use the `passwd` command to change it.

Your Linux ZVM appliance is now ready for migration.

>[!TIP]
> It is highly suggested to also setup authentication through Keycloak at this point, but is not required. See the [Authentication](#Authentication) section for additional information.





### Authentication








