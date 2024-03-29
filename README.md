# Open Knowledge Data Packager

## Installing Data Packager

To install Data Packager:

1. Get an Ubuntu 12.04, 64-bit server or virtual machine, ssh into it and
   follow the steps below.

2. Make sure Ubuntu's package index is up to date:

        sudo apt-get update

3. Install the Ubuntu packages that Data Packager requires:

        sudo apt-get install -y nginx apache2 libapache2-mod-wsgi libpq5 postgresql solr-jetty

4. Download the latest version of the Data Packager package from the
   [releases page](https://github.com/okfn/datapackager/releases), for example
   (replacing the URL with the URL of the deb file for the latest release):

        wget 'https://github.com/okfn/datapackager/releases/download/vX.Y.Z/datapackager_X.Y.Z-I_amd64.deb'

  (If you get `wget: command not found` then do `sudo apt-get install wget`
  and try again.)

5. Install the package, for example (replacing the filename with the name of
   the file you downloaded):

        sudo dpkg -i datapackager_X.Y.Z-I_amd64.deb

   If you get this error:

        Syntax error on line 1 of /etc/apache2/sites-enabled/ckan_default:
        Invalid command 'WSGISocketPrefix', perhaps misspelled or defined by a module not included in the server configuration
        Action 'configtest' failed.
        The Apache error log may have more information.
          ...fail!

   It means that for some reason the Apache WSGI module was not enabled.
   Enable it by running these commands:

        sudo a2enmod wsgi
        sudo service apache2 restart

   Then try again.

6. Follow these instructions to setup Solr for CKAN:
   <http://docs.ckan.org/en/ckan-2.2/install-from-source.html#setting-up-solr>

7. Follow these instructions to setup PostgreSQL for CKAN:
   <http://docs.ckan.org/en/ckan-2.2/install-from-source.html#postgres-setup>,
   then edit the `sqlalchemy.url` setting in your
   `/etc/ckan/default/production.ini` file and set the correct password,
   database and database user.

8. Initialize your CKAN database:

        sudo ckan db init

9. Follow these instructions to enable file uploads:
    <http://docs.ckan.org/en/ckan-2.2/filestore.html#setup-file-uploads>

10. Restart Apache and Nginx:

        sudo service apache2 restart
        sudo service nginx restart

11. You're done! Data Packager should be running on port 80 on your server.


## Updating Data Packager

If you've already installed Data Packager and now you want to upgrade to a new
release, follow these steps:

1. Download the new release from the
   [releases page](https://github.com/okfn/datapackager/releases), for example
   (replacing the URL with the URL of the deb file for the latest release):

        wget 'https://github.com/okfn/datapackager/releases/download/vX.Y.Z/datapackager_X.Y.Z-I_amd64.deb'

2. Install the new release, for example (replacing the filename with the name
   of the file you downloaded):

        sudo dpkg -i datapackager_X.Y.Z-I_amd64.deb

3. Restart Apache:

        sudo service apache2 restart


## Building the Data Packager Ubuntu package

Note: You'll need a build machine capable of 64-bit virtualization to do this.

To build a new version of the Data Packager Ubuntu package containing the latest
code:

1. Install [Vagrant 1.4](http://www.vagrantup.com/),
   [Virtualbox 4.3](https://www.virtualbox.org), [Git](http://git-scm.com/)
   and [Ansible](http://www.ansible.com/).

2. Clone the `ckan-packaging` git repo, checking out the `datapackager` branch:

        git clone -b datapackager https://github.com/ckan/ckan-packaging.git

3. Change to the directory that you cloned `ckan-packaging` into:

        cd ckan-packaging

4. Update the version number in `package.yml` (the `--version` argument to
   the `fpm` command). We use [Semantic Versioning](http://semver.org/) to
   decide the version numbers.

4. Create and boot the virtual machine:

        vagrant up

   This will create a virtual machine and run the Vagrantfile in the
   `ckan-packaging` directory, which in turn runs an Ansible playbook which
   builds the Data Packager package.

   You'll be prompted for a build 'iteration'. This should normally be `0`,
   but if for some reason you have to publish a new version of the same package
   (i.e. nothing in the code changed, but something went wrong in the packaging
   so the package has to be rebuilt), then you should increase the iteration
   number. When building the first package for a new version of Data Packager,
   the iteration number should be reset to `0` again.

   When typing the iteration number, your input will not be output to the
   console as there is a current bug with Ansible and Vagrant.

   The build process may take some time. Once it has completed, there will be a
   file called `datapackager_X.Y.Z-I_amd64.deb` in the `ckan-packaging`
   directory.

Once you've followed the above process once, you can re-use the same virtual
machine to rebuild the package (using the latest code from each git repo).
Just update the version number in `ckan-packaging/package.yml`, then do:

    vagrant provision
