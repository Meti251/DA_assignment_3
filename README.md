# DA_assignment_3
🔹 Architecture Summary
This project deploys a Gitea Git server on an Amazon EC2 instance using Docker, with persistent storage provided by a mounted data directory. Backups are created using a shell script that archives the data directory and stores it in an Amazon S3 bucket. The system ensures data durability through EBS-backed storage and S3-based backups, enabling full recovery in case of failure.

🔹 Deployment Instructions
1. Launch an EC2 instance (Ubuntu)
2. Install Docker:
sudo apt update
sudo apt install docker.io -y
3. Create data directory:
mkdir -p ~/data
4. Run Gitea container:
docker run -d \
  --name gitea \
  -p 3000:3000 \
  -v $HOME/data:/data \
  gitea/gitea:latest
5. Open in browser:
http://<EC2-PUBLIC-IP>:3000


🔹 Backup Instructions
1. Create backup script:
chmod +x backup.sh
./backup.sh
2. Upload to S3:
BUCKET="s3://twstst/backups"
ARCHIVE="$(ls -t /tmp/gitea-backup-*.tar.gz | head -n 1)"

aws s3 cp "${ARCHIVE}" "${BUCKET}/"
aws s3 ls "${BUCKET}/"


🔹 Restore Instructions
1. Stop container:
docker stop gitea
2. Download backup:
aws s3 cp s3://twstst/backups/<backup-file>.tar.gz /tmp/
3. Restore data:
sudo rm -rf $HOME/data/*
sudo tar -xzf /tmp/<backup-file>.tar.gz -C $HOME/data
4. Restart container:
docker start gitea


