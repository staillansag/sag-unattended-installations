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
```
docker build -t yourimagename .
```
The build process will take several minutes.

## How to update the template

If you need to add or change the image contents (to add adapters, connectors, etc.) then follow this process:

### Fork of this Github repository

### Modify the template.wmscript file

The list of packages is specified in this script file: https://github.com/staillansag/sag-unattended-installations/blob/main/02.templates/01.setup/MSR/1015/dce/template.wmscript

First, run the SAG Installer manually to create a new script file, where you select all the packages you want to include in the new image.
In the new script file, copy the line starting with InstallProducts, it contains the a list of product.
In the previously existing template.wmscript, replace the InstallProducts line with the new one.

Once your new template.wmscript file is ready, commit it and puch it to your forked github repository.

### Modify the Dockerfile

You need to copy the packages the image.

This is done in a section of the Dockerfile that looks like this:
```
COPY --from=installer ${SAG_HOME}/IntegrationServer/packages/WmCloud/ ${SAG_HOME}/IntegrationServer/packages/WmCloud/
COPY --from=installer ${SAG_HOME}/IntegrationServer/packages/WmConsul/ ${SAG_HOME}/IntegrationServer/packages/WmConsul/
COPY --from=installer ${SAG_HOME}/IntegrationServer/packages/WmFlatFile/ ${SAG_HOME}/IntegrationServer/packages/WmFlatFile/
COPY --from=installer ${SAG_HOME}/IntegrationServer/packages/WmISExtDC/ ${SAG_HOME}/IntegrationServer/packages/WmISExtDC/
COPY --from=installer ${SAG_HOME}/IntegrationServer/packages/WmJDBCAdapter/ ${SAG_HOME}/IntegrationServer/packages/WmJDBCAdapter/
COPY --from=installer ${SAG_HOME}/IntegrationServer/packages/WmJSONAPI/ ${SAG_HOME}/IntegrationServer/packages/WmJSONAPI/
COPY --from=installer ${SAG_HOME}/IntegrationServer/packages/WmPublic/ ${SAG_HOME}/IntegrationServer/packages/WmPublic/
COPY --from=installer ${SAG_HOME}/IntegrationServer/packages/WmRoot/ ${SAG_HOME}/IntegrationServer/packages/WmRoot/
COPY --from=installer ${SAG_HOME}/IntegrationServer/packages/WmXSLT/ ${SAG_HOME}/IntegrationServer/packages/WmXSLT/
COPY --from=installer ${SAG_HOME}/IntegrationServer/packages/WmCloudStreams/ ${SAG_HOME}/IntegrationServer/packages/WmCloudStreams/
COPY --from=installer ${SAG_HOME}/IntegrationServer/packages/WmHDFSAdapter/ ${SAG_HOME}/IntegrationServer/packages/WmHDFSAdapter/
COPY --from=installer ${SAG_HOME}/IntegrationServer/packages/WmMonitor/ ${SAG_HOME}/IntegrationServer/packages/WmMonitor/
```
Add or remove your packages there, commit the Dockerfile and push it to Github.

### Modify the install.sh file

Since you've forked and changed the Github repository, you need to make the build process point to your forked Github repository.
In the install.sh file, located this section:
```
git clone -b "${SUIF_TAG}" --single-branch \
  https://github.com/staillansag/sag-unattended-installations.git \
  "${SUIF_HOME}"
```
Replace the https://github.com/staillansag/sag-unattended-installations.git url with the one corresponding to your new Github repo.

