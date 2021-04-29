# ssh-keys-for-multiple-github-accounts

## Description
Use ssh agent, host aliases and keys in ssh config file for handling two or more Github accounts: 'personaluser' and 'workuser' on signle computer


## Detail Steps
1. [Generate ssh key pairs for accounts](https://help.github.com/articles/generating-a-new-ssh-key/) and [add them to GitHub accounts](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/).
2. Edit/Create ssh config file (`~/.ssh/config`):

   ```conf
   # Default github account: personaluser
   Host github.com
      HostName github.com
      IdentityFile ~/.ssh/personaluser_private_key
      IdentitiesOnly yes
      
   # Other github account: workuser
   Host github-workuser
      HostName github.com
      IdentityFile ~/.ssh/workuser_private_key
      IdentitiesOnly yes
   ```
   
3. [Add ssh private keys to your agent](https://help.github.com/articles/adding-a-new-ssh-key-to-the-ssh-agent/):

   ```shell
   $ ssh-add ~/.ssh/personaluser_private_key
   $ ssh-add ~/.ssh/workuser_private_key
   ```
   This only stays for that session. If you want to load automatically, add below code to be executed as default everytime config file (`~/.ssh/config`) is invoked
   
   ```shell
     Host *
      AddKeysToAgent yes
      # Additional common settings you can use for connecting remote servers using ssh
      # Not needed if you are using only git commands 
      # ServerAliveInterval 300
      # ServerAliveCountMax 2
      # Jump through more hosts
      # ProxyJump user@host1,user@host2,user@host3
    ```

4. Test your connection

   ```shell
   $ ssh -T git@github.com
   $ ssh -T git@github-workuser
   ```

   With each command, you may see this kind of warning, type `yes`:

   ```shell
   The authenticity of host 'github.com (ip address)' can't be established.
   RSA key fingerprint is xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:
   Are you sure you want to continue connecting (yes/no)?
   ```

   If everything is OK, you will see these messages:

   ```shell
   Hi personaluser! You've successfully authenticated, but GitHub does not provide shell access.
   ```
   
   ```shell
   Hi workuser! You've successfully authenticated, but GitHub does not provide shell access.
   ```

5. Now all are set, just clone your repositories

   ```shell
   $ git clone git@github-workuser:org/repository-name.git /path/to/repository
   $ cd /path/to/repository
   $ git config user.email "workuser@company.com"
   $ git config user.name  "Firstname Lastname"
   ```

