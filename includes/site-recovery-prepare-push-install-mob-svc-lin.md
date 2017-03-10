### Prepare for push installation on Linux servers

1. Ensure that there’s network connectivity between the Linux computer and the process server.
2. Create an account that can be used by the process server to access the computer. The account should be a **root** user on the source Linux server. (This account is used only for the push installation and updates.)
3. Check that the /etc/hosts file on the source Linux server has entries that map the local hostname to IP addresses associated with all network adapters.
4. Install the latest openssh, openssh-server, and openssl packages on the machine that you want to replicate.
5. Ensure SSH is enabled and running on port 22.
6. Enable SFTP subsystem and password authentication in the sshd_config file, as follows:
  1.  Sign in as **root**.
  2.  In the file /etc/ssh/sshd_config file, find the line that begins with **PasswordAuthentication**.
  3.  Uncomment the line and change the value from **no** to **yes**.
  4.  Find the line that begins with **Subsystem** and uncomment the line.

     ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

7. Add the account you created in CSPSConfigtool.
    1.  Sign in to Configuration Server.
    2.  Start **cspsconfigtool.exe**. (It's available as a shortcut on the desktop and in the %ProgramData%\home\svsystems\bin folder.)
    3.  On the **Manage Accounts** tab, click **Add Account**.
    4.  Add the account you created. After you add the account, enter the credentials you use when you enable replication for a computer.
