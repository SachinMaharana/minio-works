# Setup MinIo Cluster ( Cluster ) : 30 mins 


```
sudo lsblk

nvme0n1

mkdir /export

sudo -Es

mkdir /export
mkfs.xfs /dev/nvme0n1
echo "/dev/nvme0n1 /export xfs defaults,noatime,nofail 0 0" >> /etc/fstab
mount -a

df -h

cat > /etc/hosts << EOF
10.240.0.20    minio-1
10.240.0.21    minio-2
10.240.0.22    minio-3
10.240.0.23    minio-4
EOF

apt update && apt install wget -y

wget -O /usr/local/bin/minio https://dl.minio.io/server/minio/release/linux-amd64/minio

chmod +x /usr/local/bin/minio

cat > /lib/systemd/system/minio.service << EOF
[Unit]
Description=minio
Documentation=https://docs.min.io
Wants=network-online.target
After=network-online.target
AssertFileIsExecutable=/usr/local/bin/minio

[Service]
WorkingDirectory=/usr/local/
User=root
Group=root
EnvironmentFile=/etc/default/minio
ExecStart=/usr/local/bin/minio server \$MINIO_OPTS
Restart=always
LimitNOFILE=65536
TimeoutStopSec=infinity
SendSIGKILL=no

[Install]
WantedBy=multi-user.target
EOF


cat > /etc/default/minio << EOF
MINIO_OPTS="--address :80 http://minio-1:80/export/data http://minio-2:80/export/data http://minio-3:80/export/data http://minio-4:80/export/data"
MINIO_ACCESS_KEY="AKaHEgQ4II0S7BjT6DjAUDA4BX"
MINIO_SECRET_KEY="SKFzHq5iDoQgF7gyPYRFhzNMYSvY6ZFMpH"
EOF

systemctl daemon-reload
systemctl enable minio
systemctl start minio.service



wget https://dl.min.io/client/mc/release/linux-amd64/mc

chmod +x ./mc

cp ./mc /usr/local/bin


mc alias set minio http://10.240.0.20:80 AKaHEgQ4II0S7BjT6DjAUDA4BX SKFzHq5iDoQgF7gyPYRFhzNMYSvY6ZFMpH
```
