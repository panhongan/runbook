**[Rotate Docker Container log]**  
1. Create file: `/etc/logrotate.d/docker-container`
2. Fill content:
`
/var/lib/docker/containers/*/*.log {  
    daily  
    rotate 7  
    copytruncate  
    missingok  
    compress  
    delaycompress  
    maxsize 2G  
    minsize 1024k  
}  
`
