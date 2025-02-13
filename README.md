# Fortigate Auto Backup for Centreon  

## 📝 Description  
This script automates the backup of Fortigate configurations via SSH. It is designed to integrate with Centreon for monitoring and regularly archiving configurations.  

## ⚙️ Features  
✅ Automatic backup of Fortigate configuration  
✅ Compares with the last backup to avoid duplicates  
✅ Automatically creates backup directories  
✅ Handles SSH connection errors  
✅ Compatible with Centreon for monitoring  

⚠️ Requirements

A user with SSH access to the Fortigate device
scp installed on the system centreon

## 🚀 Installation  
1. **Clone the repository:**  
   ```sh
   git clone https://github.com/your-username/fortigate-autobackup-centreon.git
   cd fortigate-autobackup-centreon
   ```
2. **Grant execution permissions::**
   chmod +x fortigate_backup.sh
  ```sh
  chmod +x fortigate_backup.sh
```

🔧 Usage
```sh
  ./fortigate_backup.sh -i <Fortigate_IP> -n <Hostname> [-u <User>] [-d <Backup_Directory>]
```
🎯 Options
-i or --ip → (Required) Fortigate IP address
-n or --hostname → (Required) Hostname for the backup file
-u or --user → (Optional) SSH user (default: backup)
-d or --directory → (Optional) Backup directory (default: /DATA/BACKUP/FORTINET)
-h or --help → Display help

🔄 Example

```sh
./fortigate_backup.sh -i 192.168.1.1 -n FG-Branch1
```
This command will back up the configuration of the Fortigate at 192.168.1.1 and store it under /DATA/BACKUP/FORTINET/FG-Branch1/.
