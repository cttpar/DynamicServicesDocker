# Complete dockerized setup:

- Install git on your host machine - https://git-scm.com/downloads
 - Use the Vim editor by default if you fancy a challenge
 - Use the Nano editor by default if you want simplicity
 - Regardless, you will use the configured editor each time you diff, choose wisely
 - Use Git from Git Bash only
 - Use the OpenSSL library
 - Checkout as-is, commit Unix-style line endings - your co-workers will thank you
 - Use MinTTY - it's prettier, honestly
 - Enable file system caching
 - Enable git credential manager though it is optional since you'll be using ssh keys
- Install docker on your host machine - https://www.docker.com/community
- Install Kitematic for docker
 - Windows:
  - Start docker
  - Once it's running, right click the docker icon and click Open Kitematic
  - Follow the prompt and extract the downloaded zip file to `C:\Program Files\Docker\Kitematic`
 - Mac:
  - Simply download the docker toolbox found here: https://kitematic.com
- Open up a new terminal on your host machine and follow the instructions listed here:
 - https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/#generating-a-new-ssh-key
 - (Noting that your_email@example.com is your phabricator email)
 - Save the key to your local .ssh directory (i.e. ~/.ssh/yourDesiredkeyNameHere)
 - I wouldn't require a passphrase unless you just like putting in your password every time you use your key
- After generating your ssh key, copy and paste the following commands:

```
mkdir ~/projects
cd ~/projects
git clone ssh://gituser@fortytwo.ddns.uark.edu/diffusion/SDW/symfony-docker-wrapper.git
cd symfony-docker-wrapper
cp .env.dist .env
git submodule update --init
```

- Go ahead and navigate to your .env file and update the following fields:
 - Required:
  - SMTP_ACCOUNT - with a gmail account you'd be comfortable sending automated emails from
  - SMTP_PASSWORD - the password of said gmail account
 - Optional, but strongly encouraged - use https://randomkeygen.com "CodeIgniter Encryption Keys" for the following:
  - Update MYSQL_PASSWORD and PMA_PASSWORD using the same password
   - (MYSQL_PASSWORD should be the same as PMA_PASSWORD)
  - Update MYSQL_ROOT_PASSWORD
  - Update SYMFONY_SECRET
  - Update SYMFONY_API_KEY

```
docker-compose up -d
```

- goto localhost:8080, import your db data, then proceed

```
docker exec -ti cttp_backend bash
./init.sh
a2enmod rewrite
service apache2 restart
```

- Since the container sole purpose is to run apache, you'll notice the container has stopped.

```
docker container start cttp_backend
```

- You must also put/copy your ssh private key (usually id_rsa - the one you just generated) in auth/.ssh/ folder.
- Lastly, exec into the cttp_backend container and tell git who you are

```
docker exec -ti cttp_backend bash
git config --local user.name "YOUR PHABRICATOR USER NAME"
git config --local user.email "YOUR PHABRICATOR EMAIL"
```
