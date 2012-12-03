OS X Setup (10.8 Mountain Lion)
===============================

System Config
-------------

Modify /etc/paths so it looks like this:

    /usr/local/bin
    /usr/bin
    /bin
    /usr/local/sbin
    /usr/sbin
    /sbin

Prevent writing of `.DS_Store` files on network drives:

    defaults write com.apple.desktopservices DSDontWriteNetworkStores true

Prevent TextMate from creating extra file on network drives for storing extended attributes:

    defaults write com.macromates.textmate OakDocumentDisableFSMetaData 1

In Time Machine preferences > Options, turn off "Lock documents _ after last edit"


JungleDisk
----------

Install program, mount monstroso, mount active dir to sync.


General Software
----------------

* [Google Chrome](http://www.google.com/chrome)
* [Firefox](http://www.firefox.com/)
  * [Firebug](http://getfirebug.com)
  * [YSlow](http://developer.yahoo.com/yslow)
  * [Live HTTP Headers](https://addons.mozilla.org/en-US/firefox/addon/3829)
* [Transmission](http://www.transmissionbt.com)
* [VLC](http://www.videolan.org/vlc)
* [Spotify](http://www.spotify.com/)
* [ScanSnap S1300 software](http://www.fujitsu.com/global/support/computing/peripheral/scanners/software/s1300m-setup.html)
* [VirtualBox](http://virtualbox.org/)
  * [Windows images](https://github.com/xdissent/ievms)
  * [Ubuntu images](http://virtualboxes.org/images/ubuntu/)


Development Software
--------------------

Install manually:

* XCode (via App Store)
* [Command Line Tools for Xcode](http://connect.apple.com/)
* [iTerm2](http://www.iterm2.com/)
* [MacVim](http://code.google.com/p/macvim/)
* [Homebrew](http://mxcl.github.com/homebrew/)

Then:

    brew install ack rpl curl s3cmd imagemagick graphicsmagick \
      redis wget bash-completion meld

Install pdftk via author's OSX installer (not included in Homebrew):

    http://www.pdflabs.com/docs/install-pdftk/

Install wkhtmltopdf manually (doesn't seem to work with Homebrew):

    curl -O http://wkhtmltopdf.googlecode.com/files/wkhtmltopdf-0.9.9-OS-X.i368
    mv wkhtmltopdf-0.9.9-OS-X.i368 /usr/local/bin/wkhtmltopdf
    chmod +x /usr/local/bin/wkhtmltopdf

### Databases

Be sure to follow post-install instructions:

    brew install sqlite mysql postgresql mongodb

After installing PostgreSQL I was unable to connect through a socket (though I could connect through via localhost). To fix it, in `/usr/local/var/postgres/postgresql.conf` set:

    unix_socket_directory = '/tmp'

and restart the server. Then:

    sudo mkdir /var/pgsql_socket
    sudo chown alex /var/pgsql_socket
    ln -s /tmp/.s.PGSQL.5432 /var/pgsql_socket/

### Ruby

Install libyaml (for psych support):

    brew install libyaml

Install [rbenv](https://github.com/sstephenson/rbenv):

    git clone git://github.com/sstephenson/rbenv.git ~/.rbenv

If you haven't already, add to your .bashrc or .bash_profile (and then `exec $SHELL`):

    export PATH="$HOME/.rbenv/bin:$PATH"
    eval "$(rbenv init -)"

Install [ruby-build](https://github.com/sstephenson/ruby-build) plugin:

    git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build

Install Ruby versions:

    rbenv install 1.9.3-p327
    rbenv install 1.8.7-p370
    rbenv rehash

Set 1.9.3 to be the default Ruby:

    rbenv global 1.9.3-p327

Install common gems:

    gem install bundler autotest-rails debugger

### Chadwick

    cd /usr/local/src
    sudo wget http://downloads.sourceforge.net/project/chadwick/chadwick-0.5/0.5.2/chadwick-0.5.2.tar.gz
    sudo tar xf chadwick-0.5.2.tar.gz
    cd chadwick-0.5.2
    ./configure
    make
    sudo make install

### To-do

Still need to set up the following:

* Apache (w/ mod_xsendfile)
* PHP
* VirtualBox


Backup
------

Simple. Use Time Machine.

However, we should also dump all MySQL and Postgres databases regularly, using cron (`crontab -e` to edit):

    10 3 * * * pg_dumpall -U postgres | gzip > /Users/alex/backups/postgresql.sql.gz
    20 3 * * * mysqldump -A -u root | gzip > /Users/alex/backups/mysql.sql.gz


Restore
-------

### Databases

Restore MySQL and PostgreSQL databases from dumpfiles in `/var/backups`:

    mysql -uroot < mysql.sql
    psql -f postgresql.sql postgres

If you are installing onto a system with the exact same database versions as before you can instead restore the contents of the directories:

    /var/lib/mysql
    /var/lib/postgresql/<version>/main

Tell MySQL to use UTF-8 by adding an `/etc/my.cnf` that looks like this:

    [mysqld]
    character-set-server = utf8
    default-character-set = utf8

    [mysql]
    default-character-set = utf8

    [client]
    default-character-set=utf8

### Files

    ~/
      .bash_profile
      .credentials
      .gitconfig
      .heroku
      .s3cfg
      .ssh

      # also symlinks like .vimrc, .vim/*, and .bash* into the ~/dotfiles repo

    ~/Library/Preferences/
      com.googlecode.iterm2.plist

