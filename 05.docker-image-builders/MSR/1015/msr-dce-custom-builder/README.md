# Custom Builder for MSR dce

This custom builder relies on a configuration that is placed at this location: https://github.com/staillansag/sag-unattended-installations/blob/main/02.templates/01.setup/MSR/1015/dce/template.wmscript

This is essentially a SAG Installer script file that is reorganized a bit and includes some variables.

## How to use

The Dockerfile requires that the installer, update manager and the product and fix images are built or prepared beforehand.
Use the SAG Installer to download the products.zip image, using the template.wmscript file.
Use the SAG Update Manager to download the fixes.zip file, using the template.wmscript file.
Ensure the files have correct names, otherwise the process won't work.

- copy Dockerfile and install.sh in a folder of your choice
- copy the following upfront prepared files in the same build context folder
  - ./installer.bin
    - linux installer binary
  - ./sum-bootstrap.bin
    - linux Update Manager Bootstrapper binary
  - ./products.zip
    - Installer image containing MSR/1015/dce component
  - ./fixes.zip
    - Update Manage fixes image containing MSR/1015/dce fixes
  - ./msr-license.xml
    - Microservies runtime license file

- build against the build context folder
```docker build -t yourimagename .```
The build process will take several minutes.


