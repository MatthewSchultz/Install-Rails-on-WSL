# Rails from Scratch

A simple guide to installing rails on WSL. Not super unique, but complete....

## Step 1 - WSL

* Admin powershell, execute `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`
* Reboot
* In Microsoft Store, find Ubuntu, download that stuffs...
* Hit the Launch button in store, or find Ubuntu in start menu. Watch and wait while it unpacks itself.
* Run `sudo apt update && sudo apt upgrade` to start getting packages

## Step 2 - Install NodeJS and YARN

Rails 6 now requires the YARN package manager to function, since it likes webpack more than other stuff now. It also tends to be built heavily around Node packages. While it's theoretically possible to use Rails without these things, in modern web apps, Node and YARN should be installed.

### Setup NodeJS as a Javascript runtime

```bash
curl -sL https://deb.nodesource.com/setup_13.x | sudo -E bash -
sudo apt-get install -y nodejs
```

This sets up NodeJS by downloading a setup script from the NodeJS site and running it. This is superior to simply installing node Ubuntu's managed repos, since that version is ususally woefully out of date.

Be sure to change the version your trying to get in the URI - the above URI gets the latest version of the v13 major branch.

### Setup the YARN package manager

First, let's add the YARN server to the package repo list (since the one that is included with repo lists is nearly guaranteed to be out of date):

```bash
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update
```

Now install YARN...

```bash
sudo apt install yarn
```

...and test

```bash
yarn --version
```

Rails 6, as of this writing, requires at least YARN 1.0.

## Step 3 - Pre-Installation

### Setup Dependencies

```bash
sudo apt-get install git-core curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev software-properties-common libffi-dev
sudo apt-get install libpq-dev
sudo apt-get install imagemagick
sudo apt-get install graphviz
```

Here we install a bunch of dependencies, of which libxml2-dev is particularly important for nokogiri.

The second line can be omitted if (for some probably terrible reason, you are not using postgresql).

The third line install imagemagick, a critical image-processing library. If you know you don't need this it's ok to omit, but it's a common enough library I suggest installing it.

The fourth line installs graphviz, a graphical generation utility. Not sure how important this is, but I use it often.

### Setup QOL Tools

```bash
sudo apt-get install tmux tty-clock htop cowsay postgresql-client fortune
```

These are somewhat optional, but make living in semi-linux bearable.

## Step 4 - Ruby via rbenv

This will install Ruby via rbenv. Doing it via ruby-full seems to suck.

```bash
cd
git clone https://github.com/rbenv/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
exec $SHELL

git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
exec $SHELL

rbenv install 2.7.1
rbenv global 2.7.1
ruby -v
```

Now, write the following to `~/.gemrc` (this is optional, of course, but I mean, who really wants to wait for everything to compile it's documentation):

```bash
echo 'gem: --no-document' >> ~/.gemrc
```

Finally:

```bash
gem install bundler
rbenv rehash
```

## Step 5 - Setup GIT

```bash
git config --global color.ui true
git config --global user.name "YOUR NAME"
git config --global user.email "YOUR@EMAIL.com"
ssh-keygen -t rsa -b 4096 -C "YOUR@EMAIL.com"
```

This sets up GIT and runs keygen. Now `cat ~/.ssh/id_rsa.pub` to get the key to paste into BitBucket/GitHub. Make sure to make a new window if you're in a tmux pane so you can copy that noise.

Now do `ssh -T git@bitbucket.org` or `ssh -T git@github.com` to test it.

## Step 6 - Because we Can

```bash
gem install lolcat
```

Helps make sure gems are working anyways.......not really. I just find lolcat funny.

## Step 7 - Install the Rails

```bash
gem install rails
```

Here we are just going for the latest version, at the time of this writing, **6.0.3**.

Because we installed all that dependencies in step 2, this *should* install nokogiri correctly.

Test by `rails -v`

## Step 8 - Change the Way WSL Handles CHMOD

Do `sudo vim /etc/wsl.conf` - we need to write a wsl config to turn on metadata tracking for mounted noise. Into that file write:

```bash
[automount]
enabled = true
options = "metadata"
mountFsTab = false
```

Now, restart the entire Ubuntu instance, and Rails will suddenly behave itself. Skipping this causes Rails to fail on Rails new with some complete rubbish about permissions...

## Step 9 - Get to Project Directory

Do `cd /mnt/<windows full path>` to get to the project directory. Project directories ARE case sensitive, use linux folder seperation, do not use the colon notation for drives, and the drive letter is lowercased, so `C:\Users\You\Projects\SomeFolder\` becomes `/mnt/c/Users/You/Projects/SomeFolder`.
