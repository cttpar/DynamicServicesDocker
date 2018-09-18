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
git clone ssh://gituser@fortytwo.ddns.uark.edu/diffusion/S/cttp-symfony-application.git
mv cttp-symfony-application cttp
cd cttp
cp .env.dist .env
cp .arcconfig.dist .arcconfig
```

In the docker/backend folder, create an auth directory. Now create a `.arcrc` file with these contents:

```
{
  "hosts": {
    "http://gituser@fortytwo.ddns.uark.edu/api/": {
      "token": "YOURTOKENHERE"
    }
  }
}

```

Now navigate to http://fortytwo.ddns.uark.edu/conduit/login/ and replace "YOURTOKENHERE" in `.arcrc` with the token given at that url.

You must also put your ssh private key (usually id_rsa) in this docker/backend/auth folder.

```
docker-compose up -d
```

- While your containers are being built, go ahead and navigate to your .env file and update the following fields:
 - Required:
  - SMTP_ACCOUNT - with a gmail account you'd be comfortable sending automated emails from
  - SMTP_PASSWORD - the password of said gmail account
  - PRIVATEKEYNAME - the name of the ssh key file you generated (usually id_rsa)
 - Optional, but strongly encouraged - use https://randomkeygen.com CodeIgniter Encryption Keys for the following:
  - Update MYSQL_PASSWORD and PMA_PASSWORD using the same password
   - (MYSQL_PASSWORD should be the same as PMA_PASSWORD)
  - Update MYSQL_ROOT_PASSWORD
  - Update SYMFONY_SECRET
  - Update SYMFONY_API_KEY
- Now open Kitematic (right click docker icon, kitematic), click the cttp_backend, and click exec and run the following:

```
a2enmod rewrite
service apache2 restart
```

- Since the container sole purpose is to run apache, you'll notice the container has stopped.
- In your Kitematic window, click cttp_backend, click restart, and then run the following:

**You can also run ./init.sh in the exec in the CTTP_backend container if you do not want to manualy run these commands.**
```
usermod -u 1000 www-data
chown -R www-data:www-data /var/www/app/cache
chown -R www-data:www-data /var/www/app/logs
composer install
app/console c:c --no-warmup
app/console a:d
app/console d:s:u --dump-sql --force
```

***Note: If your diffs are showing up as someone who is not you, this command will fix that.***

**FYI: Once you reset your docker containers you will need to run this command again.**

```
arc install-certificate
```

- You'll be prompted with some instructions to follow a link to our server, do this and then continue.
- After following the prompt, execute the following commands:

```
docker cp cttp_backend:/root/.arcrc ~/.arcrc
```

- Lastly, in the cttp-symfony-application repo, tell git who you are

```
git config --local user.name "YOUR PHABRICATOR USER NAME"
git config --local user.email "YOUR PHABRICATOR EMAIL"
```
