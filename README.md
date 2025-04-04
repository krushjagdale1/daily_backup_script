# daily_backup_script





run this commands first

crontab -e 
and past in the file this code

0 4 * * * /root/"yourfilename.sh >> /root/cron-output.log 2>&1



#!/bin/bash

# === CONFIG ===
BUCKET_NAME="krushnajag"
DATE=$(date +%F)
LOG_FILE="/root/selected-backup.log"

# === LOG HEADER ===
echo "=== Backup started at $(date) ===" >> "$LOG_FILE"

# === FIND AND UPLOAD ONLY /root/*.txt and *.yml ===
find /root -maxdepth 5 -type f \( -iname "*.txt" -o -iname "*.yml" \) | while read file; do
    # Remove leading slash
    SAFE_FILE=$(echo "$file" | sed 's|^/||')
    S3_PATH="s3://$BUCKET_NAME/backup/$DATE/$SAFE_FILE"
   
    echo "Uploading: $file --> $S3_PATH" >> "$LOG_FILE"
    aws s3 cp "$file" "$S3_PATH" >> "$LOG_FILE" 2>&1
done

# === LOG FOOTER ===
echo "=== Backup completed at $(date) ===" >> "$LOG_FILE"
echo "----------------------------------------" >> "$LOG_FILE"
