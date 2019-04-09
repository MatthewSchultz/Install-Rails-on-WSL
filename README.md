# Rails from Scratch

A simple guide to installing rails on WSL. Not super unique, but complete....

---

## Step 1 - WSL

* Admin powershell, execute `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`
* Reboot
* In Microsoft Store, find Ubuntu, download that stuffs...
* Hit the Launch button in store, or find Ubuntu in start menu. Watch and wait while it unpacks itself.
* Run `sudo apt update && sudo apt upgrade` to start getting packages

---

## Step 2 - Pre-Install

### Setup NodeJS as a Javascript runtime

```
curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
sudo apt-get install -y nodejs
```

This sets up NodeJS. Some guuides say to do this later, but can do it here no problem.


### Setup Dependencies

```
sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev
sudo apt-get install libpq-dev
```

Here we install a bunch of dependencies, of which libxml2-dev is particularly important for nokogiri.

The second line can be omitted if (for some probably terrible reason, you are not using postgresql).

### Setup QOL Tools

```
sudo apt-get install tmux tty-clock htop cowsay postgresql-client fortune
```

These are somewhat optional, but make living in semi-linux bearable.

---

## Step 3 - Ruby via rbenv

This will install Ruby via rbenv. Doing it via ruby-full seems to suck.

```
cd
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
exec $SHELL

git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
exec $SHELL

rbenv install 2.6.2
rbenv global 2.6.2
ruby -v

```

Now, write the following to `~/.gemrc`:

```
gem: --no-document
```

Finally:

```
gem install bundler
rbenv rehash
```

---

## Step 4 - Setup GIT

```
git config --global color.ui true
git config --global user.name "YOUR NAME"
git config --global user.email "YOUR@EMAIL.com"
ssh-keygen -t rsa -b 4096 -C "YOUR@EMAIL.com"
```

This sets up GIT and runs keygen. Now `cat ~/.ssh/id_rsa.pub` to get the key to paste into BitBucket/GitHub. Make sure to make a new window if you're in a tmux pane so you can copy that noise.

Now do `ssh -T git@bitbucket.org` or `ssh -T git@github.com` to test it.

---

## Step 5 - Because we Can

```
gem install lolcat
```

Helps make sure gems are working anyways.......not really. I just find lolcat funny.

---

## Step 6 - Install the Rails:

```
gem install rails
```

Here we are just going for the latest version, at the time of this writing, **5.2.3**.

Because we installed all that dependencies in step 2, this *should* install nokogiri correctly.

Test by `rails -v`

---

## Step 7 - Change the Way WSL Handles CHMOD

Do `sudo vim /etc/wsl.conf` - we need to write a wsl config to turn on metadata tracking for mounted noise. Into that file write:

```
[automount]
enabled = true
options = "metadata"
mountFsTab = false
```

Now, restart the entire Ubuntu instance, and Rails will suddenly behave itself. Skipping this causes Rails to fail on Rails new with some complete rubbish about permissions...

---

## Step 8 - Get to Project Directory

Do `cd mnt/<windows full path>` to get to the project dir
