# Gitlab Using Docker

## Step 1: Docker Installation

Recommendation: 
[Follow official Documentation](https://docs.docker.com/engine/install/ubuntu/)

Start and enable the Docker service:
```
sudo systemctl enable --now docker
```

#### To run Docker without sudo, add your user to the docker group.

##### 1. Check if docker group exists: 
```
getent group docker
```

If nothing is returned, create it:
```
sudo groupadd docker
```

##### 2. Add your user to docker group

For current user:
```
sudo usermod -aG docker $USER
```

Or explicitly:
```
sudo usermod -aG docker ubuntu
```

##### 3. Apply group changes
```
newgrp docker
```

##### 4. Verify

Run:
```
groups
```

You should see docker.

Then test:
```
docker ps
```

No sudo should be needed.

## Step 2: Set up GitLab Storage Directories

To keep repositories and data safe when the container restarts, create a dedicated folder structure:

```
mkdir -p ~/gitlab/config ~/gitlab/logs ~/gitlab/data
```

## Step 3: Run the GitLab Container

Execute the following command to download and start GitLab Community Edition (CE).

Note: Since it is running on your laptop, we will bind it to localhost on port `7777` so it doesn't conflict with any existing web servers on your machine.

```
sudo docker run --detach \
  --hostname localhost \
  --publish 8443:443 --publish 7777:80 --publish 2222:22 \
  --name gitlab \
  --restart always \
  --volume ~/gitlab/config:/etc/gitlab \
  --volume ~/gitlab/logs:/var/log/gitlab \
  --volume ~/gitlab/data:/var/opt/gitlab \
  gitlab/gitlab-ce:latest
```

## Step 4: Get Your Initial Admin Password
GitLab takes 2 to 5 minutes to fully initialize. Once it is up, retrieve the auto-generated password for the root account by running:

```
sudo docker exec -it gitlab grep 'Password:' /etc/gitlab/initial_root_password
```

(Copy this password; the file automatically deletes itself after 24 hours).

## Step 5: Access GitLab
Open your web browser and go to: http://localhost:7777

Log in with the username `root` and the password you just copied.
