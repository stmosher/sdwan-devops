# VIRL

Both the control plane and the edge can be deployed in virl

>Note: The repo is designed for VIRL2/CML2.  The previous VIRL1/CML1 version can be found [here](https://github.com/CiscoDevNet/sdwan-devops/tree/virl1)

## Dependencies

Set the name of the lab, e.g.:
Using bash:
```
export VIRL_HOST=myvirlhost
export VIRL_USERNAME=myusername
export VIRL_PASSWORD=mypasword
export VIRL_LAB=myusername_sdwan
```
Using windows cli:
```
set VIRL_HOST=myvirlhost
set VIRL_USERNAME=myusername
set VIRL_PASSWORD=mypasword
set VIRL_LAB=myusername_sdwan
```
These values can be set permanently in the `virl.yml` file in the inventory by adding:
```
host: myvirlhost
username: myusername
password: mypasword
lab: myusername_sdwan
```

## Lab files

The files that define the labs are located in `{{ playbook_dir }}/files`.  The `build-virl.yml` playbooks builds the lab specified in the `virl_lab_file` fact located in `inventory/<topology_name>/group_vars/all/virl.yml`

To save changes that you make in the VIRL GUI, download the lab and override the existing file.

## Bringing up the Simulation

### Create a local CA

Using bash:
```bash
$ ./play.sh build-ca.yml
```
Using powershell:
```powershell
> powershell.exe ./play.ps1 build-ca.yml
```

### Build the topology

Run the `build-virl.yml` playbook to the build the out the control plane:

Using bash:
```bash
$ ./play.sh build-virl.yml
```
Using powershell:
```powershell
> powershell.exe ./play.ps1 build-virl.yml
```

This playbook will:

* Launch the lab in virl
* Wait until the VNFs are reachable

`--limit` can be used to build individual nodes:

Using bash:
```bash
$ ./play.sh build-virl.yml --limit=site1-cedge1
```
Using powershell:
```powershell
> powershell.exe ./play.ps1 build-virl.yml --limit=site1-cedge1
```

### Configure the SD-WAN fabric

Set the name of the organization, e.g.:
Using bash:
```
export VMANAGE_ORG=myorgname
```
Using windows cli:
```
set VMANAGE_ORG=myorgname
```

>Note: This value can be set permanently in `group_vars/all/local.yml`

Make sure that you have a serial file that matches the org name in this location: `licenses/serialFile.viptela` 

Run the playbook

Using bash:
```bash
$ ./play.sh config-virl.yml
```
Using powershell:
```powershell
> powershell.exe ./play.ps1 config-virl.yml
```

This playbook will:

* Configure setting on vmanage
* Install Enterprise CA when required
* Add vbonds and vsmarts to vmanage
* Create CSRs for vbonds and vsmarts
* Install certificates into vmanage
* Push certificates to controllers
* Import templates if present
* Import policy if present

### Deploy the SD-WAN edges

Using bash:
```bash
$ ./play.sh deploy-virl.yml
```
Using powershell:
```powershell
> powershell.exe ./play.ps1 deploy-virl.yml
```

## Clean the topology

Stops and wipes all of the nodes in the lab.

Using bash:
```bash
$ ./play.sh clean-virl.yml
```
Using powershell:
```powershell
> powershell.exe ./play.ps1 clean-virl.yml
```

`--limit` can be used to clean individual nodes:

```bash
$ ./play.sh clean-virl.yml --limit=site1-cedge1
```
Using powershell:
```powershell
> powershell.exe ./play.ps1 clean-virl.yml --limit=site1-cedge1
```

To remove the lab completely from the VIRL server:

Using bash:
```bash
$ ./play.sh clean-virl.yml --tags=destroy
```
Using powershell:
```powershell
> powershell.exe ./play.ps1 clean-virl.yml --tags=destroy
```