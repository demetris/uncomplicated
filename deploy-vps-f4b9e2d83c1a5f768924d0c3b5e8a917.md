
Uncomplicated VPS deployment setup
================================================================================


Overview
----------------------------------------

The uncomplicated.cc site is deployed on a Linode VPS running Apache.
The files are served directly from a git repository.


Workflow
----------------------------------------

1. Make changes locally
2. Test and commit
3. Deploy: `git push vps`
4. Changes are immediately live (no build process needed)


VPS setup
----------------------------------------

To enable git push deployment, configure the VPS repository to accept pushes:

```bash
ssh username@server
cd /home/hope/projects/uncomplicated
git config receive.denyCurrentBranch updateInstead
```

This allows pushing directly to the checked-out branch and automatically
updates the working directory that Apache serves.


Deployment methods
----------------------------------------

### Method 1: Git push (recommended)

From local machine:

```bash
#
# Add VPS as remote (one time setup)
#
git remote add vps username@server:/home/hope/projects/uncomplicated

#
# Deploy changes
#
git push vps main
```

### Method 2: SSH and pull

```bash
ssh username@server
cd /home/hope/projects/uncomplicated
git pull origin main
```


Apache configuration
----------------------------------------

The site is served directly from `/home/hope/projects/uncomplicated/`
with a virtual host configuration that is something like this:

```apache
<VirtualHost *:443>

  ServerName uncomplicated.cc
  ServerAlias www.uncomplicated.cc
  Protocols h2 http/1.1
  DocumentRoot /home/hope/projects/uncomplicated/

  <Directory /home/hope/projects/uncomplicated/>
  
    <FilesMatch "^(deploy-vps-)[a-f0-9]{32}\.md$">
      Require all denied
    </FilesMatch>
  
  </Directory>

  <DirectoryMatch "\.git">
    Require all denied
  </DirectoryMatch>
  
  #
  # Security headers
  #
  ...
  
  #
  # SSL configuration
  #
  ...

</VirtualHost>
```
