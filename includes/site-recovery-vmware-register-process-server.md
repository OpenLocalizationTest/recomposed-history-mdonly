1. Connect to the process server virtual machine using Remote Desktop Connection.
2. Once the login in complete, you see the process server Configuration tool is automatically launched and requires you to input the following
  * Configuration Server's fully qualified name (FQDN) or IP Address
  * Port on which the Configuration server is listening on. The value should be 443
  * Connection Passphrase to connect to the configuration server.
  * Data Transfer port to be configured for this process server. Leave the default value as is unless you have changed it to a different port number in your environment.

    ![Register process server](./media/site-recovery-vmware-register-process-server/register-ps.png)
3. Click the save button to save the configuration.
4. Once the registration is completed, the process server starts showing up under your Configuration server and now can be used for failback.

> [!TIP]
> The process server configuration utility can be launched by double-clicking the **cspsconfigtool** shortcut available on the desktop of the virtual machine.
