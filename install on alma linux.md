To install Oracle 11g XE on AlmaLinux, follow these steps. AlmaLinux is a Red Hat Enterprise Linux (RHEL) compatible operating system, and these instructions are applicable to similar RHEL-based systems.

### Prerequisites:
1. **AlmaLinux (64-bit)**: Ensure you have AlmaLinux installed.
2. **Oracle 11g XE**: Download the Oracle 11g XE installation package from Oracle's website.
3. **Memory & Disk Space**: Minimum of 1 GB RAM and 2 GB of disk space.
4. **User Privileges**: Root or sudo privileges on the system.

### Step-by-Step Installation Process:

#### Step 1: Update AlmaLinux

Before starting, ensure your system is up to date:
```bash
sudo dnf update -y
```

#### Step 2: Download Oracle 11g XE

Visit the official Oracle website, register or log in, and download the Oracle 11g XE RPM package. You may need to transfer the downloaded file to your server if you downloaded it on a different machine.

#### Step 3: Install Required Dependencies

Oracle 11g XE has some dependencies. Install them by running the following command:
```bash
sudo dnf install -y bc flex bison binutils elfutils-libelf-devel gcc gcc-c++ glibc glibc-devel glibc-headers libaio libaio-devel libgcc libstdc++ libstdc++-devel make sysstat unixODBC unixODBC-devel pcre-devel
```

#### Step 4: Disable Transparent HugePages

Oracle recommends disabling Transparent HugePages. Edit the Grub configuration:
```bash
sudo vi /etc/default/grub
```
Add `transparent_hugepage=never` at the end of the `GRUB_CMDLINE_LINUX` line, like so:
```bash
GRUB_CMDLINE_LINUX="... transparent_hugepage=never"
```
After that, update Grub:
```bash
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

#### Step 5: Set Kernel Parameters

Oracle 11g XE requires specific kernel parameters to be set. Open the `/etc/sysctl.conf` file for editing:
```bash
sudo vi /etc/sysctl.conf
```
Add the following lines at the end of the file:
```bash
fs.file-max = 6815744
kernel.sem = 250 32000 100 128
kernel.shmall = 2097152
kernel.shmmax = 536870912
kernel.shmmni = 4096
net.ipv4.ip_local_port_range = 9000 65500
net.core.rmem_default = 262144
net.core.rmem_max = 4194304
net.core.wmem_default = 262144
net.core.wmem_max = 1048576
```
Apply the new settings with:
```bash
sudo sysctl -p
```

#### Step 6: Configure Oracle User Limits

Oracle 11g XE requires setting user limits. Open the `/etc/security/limits.conf` file for editing:
```bash
sudo vi /etc/security/limits.conf
```
Add the following lines:
```bash
oracle soft nproc 2047
oracle hard nproc 16384
oracle soft nofile 1024
oracle hard nofile 65536
oracle soft stack 10240
```

#### Step 7: Install Oracle 11g XE

Navigate to the directory where the Oracle 11g XE RPM file was downloaded. Install the Oracle 11g XE RPM package with the following command:
```bash
sudo dnf localinstall oracle-xe-11.2.0-1.0.x86_64.rpm
```

#### Step 8: Configure Oracle 11g XE

After installation, configure Oracle 11g XE using the following command:
```bash
sudo /etc/init.d/oracle-xe configure
```

You will be prompted to provide the following details:
- **Specify a listener port** (default is 1521).
- **Specify an HTTP port** for the Oracle Application Express (default is 8080).
- **Specify a password** for the SYS and SYSTEM administrative accounts.

After providing this information, Oracle will automatically configure the installation.

#### Step 9: Start Oracle Database

Start the Oracle service:
```bash
sudo service oracle-xe start
```

To make Oracle 11g XE start automatically on boot:
```bash
sudo chkconfig oracle-xe on
```

#### Step 10: Set Oracle Environment Variables

To access Oracle utilities like `sqlplus`, you need to set Oracle environment variables. Open the `.bash_profile` for the oracle user:
```bash
sudo vi /home/oracle/.bash_profile
```
Add the following lines at the end:
```bash
export ORACLE_HOME=/u01/app/oracle/product/11.2.0/xe
export ORACLE_SID=XE
export PATH=$ORACLE_HOME/bin:$PATH
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$LD_LIBRARY_PATH
```
Save and apply the changes:
```bash
source /home/oracle/.bash_profile
```

#### Step 11: Access Oracle 11g XE

You can now access Oracle 11g XE via `sqlplus` as the `oracle` user:
```bash
sqlplus / as sysdba
```

#### Step 12: Configure Firewall (Optional)

If you have a firewall enabled, allow access to Oracle ports:
```bash
sudo firewall-cmd --permanent --add-port=1521/tcp
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
```

---

### Conclusion

You have successfully installed Oracle 11g XE on AlmaLinux. You can now manage your Oracle Database, use SQL commands, and build your database applications.