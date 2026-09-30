# AWS Docker Nginx Web Server

A personal cybersecurity-themed website hosted on an AWS EC2 instance, served by Nginx running inside a Docker container.

**Live site:** http://3.12.238.207

## Architecture

```
Internet
   ↓  HTTP (port 80)
AWS Security Group
   ↓
EC2 Instance (Amazon Linux) + Elastic IP
   ↓
Docker container
   ↓
Nginx
   ↓
Static website (site/)
```

## Technologies
- AWS EC2 (Amazon Linux), Elastic IP, Route 53
- Docker
- Nginx
- HTML / CSS / JavaScript
- Git / GitHub

## What I did
- Launched an EC2 instance and connected over SSH with key-based authentication
- Troubleshot SSH login failures (the default username depends on the AMI)
- Installed Docker and ran Nginx in a container with a read-only bind mount
- Configured the EC2 Security Group to allow inbound HTTP on port 80
- Attached an Elastic IP so the server address stays fixed
- Set up DNS records in Route 53
- Resolved a port conflict between Docker containers

## Security notes
- The web root is limited to the `site/` folder so the `.git` directory is never publicly served (an exposed `.git` folder can leak source code and history)
- The content is mounted read-only (`:ro`) into the container
- Private keys (`.pem`) and secrets are excluded with `.gitignore`
- SSH uses key-based authentication only

## How to run

Option 1: build an image
```bash
git clone https://github.com/LucasJulioSanto/aws-linux-nginx-project.git
cd aws-linux-nginx-project
docker build -t my-site .
docker run -d --name my-site -p 80:80 my-site
```

Option 2: bind mount (live editing)
```bash
docker run -d --name nginx -p 80:80 \
  -v "$(pwd)/site":/usr/share/nginx/html:ro nginx
```

Then open `http://<your-server-ip>` in a browser.

## Future improvements
- HTTPS with Let's Encrypt (Certbot)
- Custom domain (juliosprofile.com)
- Restrict SSH in the Security Group to my IP only
