#Building Datapackager Ubuntu Package

Install [vagrant](http://www.vagrantup.com/)

Checkout the datapackager build repository
`git clone -b tsb-b2 https://github.com/ckan/ckan-packaging.git`

Change to packaging directory
`cd ckan-packaging`

Run Vagrant
`vagrant up`

This will set off the build process, you will be prompted for a build
'iteration' which you should set higher than the latest build, your
input will not be output to the console as there is a current bug with ansible
and vagrant. The build process may take some time. Once it has completed, there
will be a file called `datapackage_1-{iteration}.deb` in package directory.

#Installing the Datapackager Package
Follow the install instructions 

http://docs.ckan.org/en/latest/maintaining/installing/install-from-package.html

replace step 4 with
`sudo dpkg -i datapackager_1-1_amd64.deb`
