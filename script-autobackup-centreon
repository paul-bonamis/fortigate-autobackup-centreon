#!/bin/sh

USER="backup" # User used for SSH connection (can be overridden by an argument)
BACKUP_DIR="/DATA/BACKUP/FORTINET" # Path where files will be stored (can be overridden by an argument)
BACKUP_FILE="NONE" # Name of backup file (must be set with an argument)
IP_HOST="NONE" # IP of the host to connect via SSH

while [ -n "$1" ]; do # Parse script arguments
  case "$1" in
  -h | --help)
    echo "Usage:"
    echo "-i or --ip and -n or --hostname are mandatory."
    echo "-u or --user (default: \"backup\")."
    echo "-d or --directory (default: /DATA/BACKUP/FORTINET)."
    echo ""
    echo "Options:"
    echo "-i, --ip          Specify the IP address to save."
    echo "-u, --user        Specify the user to connect to Fortigate."
    echo "-d, --directory   Specify where to save the backup file."
    echo "-n, --hostname    Specify the Fortigate hostname."
    echo "-h, --help        Display this help message."
    exit 0
    ;;

  -i | --ip)
    if echo "$2" | grep -Eq '^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+$'; then
      IP_HOST="$2"
    else
      echo "Error: Invalid IP address format!"
      exit 1
    fi
    shift
    ;;

  -u | --user)
    USER="$2"
    shift
    ;;

  -n | --hostname)
    BACKUP_FILE="$2"
    shift
    ;;

  -d | --directory)
    if [ -d "$2" ]; then
      BACKUP_DIR="$2"
    else
      echo "Error: '$2' is not a valid directory!"
      exit 1
    fi
    shift
    ;;

  *)
    echo "Error: Unknown argument '$1'. Use -h or --help for usage information."
    exit 1
    ;;
  esac
  shift
done

# Check mandatory arguments
if [ "$IP_HOST" = "NONE" ] || [ "$BACKUP_FILE" = "NONE" ]; then
  echo "Error: Please specify an IP address and a hostname!"
  echo "Use -i or --ip and -n or --hostname."
  exit 1
fi

# Create backup directory if it doesn't exist
if [ ! -d "${BACKUP_DIR}/${BACKUP_FILE}" ]; then
  mkdir -p "${BACKUP_DIR}/${BACKUP_FILE}"
fi

# Define backup file path
FILE_PATH="${BACKUP_DIR}/${BACKUP_FILE}/${BACKUP_FILE}-$(date +"%d-%m-%Y-%H%M")--temp.conf"

# Try to copy configuration file via SCP
scp -o ConnectTimeout=30 -o "StrictHostKeyChecking no" "${USER}@${IP_HOST}:sys_config" "${FILE_PATH}"

if [ $? -ne 0 ]; then
  scp -o KexAlgorithms=ecdh-sha2-nistp521 -o ConnectTimeout=30 -o "StrictHostKeyChecking no" "${USER}@${IP_HOST}:sys_config" "${FILE_PATH}"

  if [ $? -ne 0 ]; then
    rm -f "${FILE_PATH}"
    echo "Error: Failed to connect to Fortigate via SSH!"
    exit 2
  fi
fi

# Check if configuration has changed
CURRENT_CONF=$(ls -1 "${BACKUP_DIR}/${BACKUP_FILE}/" | grep -- "--last.conf$" | head -n 1)

if [ -n "$CURRENT_CONF" ] && diff "${FILE_PATH}" "${BACKUP_DIR}/${BACKUP_FILE}/${CURRENT_CONF}" > /dev/null; then
  rm -f "${FILE_PATH}"
  echo "No changes detected in the Fortigate configuration."
  exit 0
else
  # Rename previous backup file
  if [ -n "$CURRENT_CONF" ]; then
    mv "${BACKUP_DIR}/${BACKUP_FILE}/${CURRENT_CONF}" "${BACKUP_DIR}/${BACKUP_FILE}/${CURRENT_CONF%--last.conf}.conf"
  fi

  # Rename new backup file
  mv "${FILE_PATH}" "${BACKUP_DIR}/${BACKUP_FILE}/${BACKUP_FILE}--last.conf"
  echo "Fortigate configuration has been updated!"
  exit 0
fi
