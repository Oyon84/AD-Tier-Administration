# Powershell scripts for AD Tiering model deployment

This collection of scripts allows for quick deployment of a tiering model for Active Directory. These scripts can be used for existing environments and creates a new Top Level OU and deploys the tiering model. You can also use it in a brand new AD environment to get started with a tiering model in AD.

Its reconmended to first deploy inside a test environment to get a full understanding of the scripts and files used.

## Requirements
- AD domain
- Domain admin level permissions
- Text editor to edit the input files (csv / json / log)
- Powershell client, can be visual studio code or Powershell ISE or other preference

# 1 Deploy OU / Container Structure (Create-Structure.ps1)
First step is to create a new OU structure. This script will deploy a new Top Level OU called **corp** and deploys a new OU structure in the corp OU. This will contain OU's for all tiers and assets and this forms the basis of the tiering model. This script also deploys a set of containers for the following assets:
- Roles Tier 0, 1, 2
- Access Control Tier 0, 1, 2
- Security Groups

The script will perform a check the object after its created, if something failed it will be shown on the console with Red text displaying the error.

## Prerequisites 
- Rename user locations in **Create-Structure.ps1** to something sensible for your organization. Currently marked as Location 1, 2, 3, 4 and 5.
- Rename path to your domain. Current value is "DC=test,DC=local" standing for test.local as domain name. Use your text editor to find DC=test,DC=local and replace with DC=yourdomain,DC=local
- Create any extra OU's 

## Run script
Next step is to run the script. Output should look similair to the following: 

```
PS C:\Users\Administrator> C:\Temp\Create-Structure.ps1
Creating OU Corp in DC=test,DC=local
Creating OU Users in OU=Corp,DC=test,DC=local
Creating OU Service in OU=Corp,DC=test,DC=local
Creating OU Administration in OU=Corp,DC=test,DC=local
Creating OU Computers in OU=Corp,DC=test,DC=local
Creating OU Tier 2 in OU=Computers,OU=Corp,DC=test,DC=local
Creating OU Tier 1 in OU=Computers,OU=Corp,DC=test,DC=local
Creating OU Tier 0 in OU=Computers,OU=Corp,DC=test,DC=local
Creating OU Terminal servers in OU=Computers,OU=Corp,DC=test,DC=local
Creating OU PKI in OU=Tier 0,OU=Computers,OU=Corp,DC=test,DC=local
Creating OU Shared in OU=Tier 2,OU=Computers,OU=Corp,DC=test,DC=local
Creating OU Desktops in OU=Tier 2,OU=Computers,OU=Corp,DC=test,DC=local
Creating OU Laptops in OU=Tier 2,OU=Computers,OU=Corp,DC=test,DC=local
Creating OU Network in OU=Tier 1,OU=Computers,OU=Corp,DC=test,DC=local
Creating OU Application in OU=Tier 1,OU=Computers,OU=Corp,DC=test,DC=local
Creating OU File in OU=Tier 1,OU=Computers,OU=Corp,DC=test,DC=local
Creating OU Update in OU=Tier 1,OU=Computers,OU=Corp,DC=test,DC=local
Creating OU Delft in OU=Users,OU=Corp,DC=test,DC=local
Creating OU Location2 in OU=Users,OU=Corp,DC=test,DC=local
Creating OU Location3 in OU=Users,OU=Corp,DC=test,DC=local
Creating OU Location4 in OU=Users,OU=Corp,DC=test,DC=local
Creating OU Location5 in OU=Users,OU=Corp,DC=test,DC=local
Creating OU Offsite in OU=Users,OU=Corp,DC=test,DC=local
Creating OU Contractors in OU=Users,OU=Corp,DC=test,DC=local
Creating OU Tier 0 in OU=Service,OU=Corp,DC=test,DC=local
Creating OU Tier 1 in OU=Service,OU=Corp,DC=test,DC=local
Creating OU Users in OU=Tier 0,OU=Service,OU=Corp,DC=test,DC=local
Creating OU Users in OU=Tier 1,OU=Service,OU=Corp,DC=test,DC=local
Creating OU Tier 0 in OU=Administration,OU=Corp,DC=test,DC=local
Creating OU Tier 1 in OU=Administration,OU=Corp,DC=test,DC=local
Creating OU Tier 2 in OU=Administration,OU=Corp,DC=test,DC=local
Creating OU Users in OU=Tier 0,OU=Administration,OU=Corp,DC=test,DC=local
Creating OU Users in OU=Tier 1,OU=Administration,OU=Corp,DC=test,DC=local
Creating OU Users in OU=Tier 2,OU=Administration,OU=Corp,DC=test,DC=local
Creating Container Security groups in OU=Corp,DC=test,DC=local
Creating Container Access Control in CN=Security groups,OU=Corp,DC=test,DC=local
Creating Container Roles in CN=Security groups,OU=Corp,DC=test,DC=local
Creating Container Access Control in OU=Tier 0,OU=Administration,OU=Corp,DC=test,DC=local
Creating Container Roles in OU=Tier 0,OU=Administration,OU=Corp,DC=test,DC=local
Creating Container Access Control in OU=Tier 1,OU=Administration,OU=Corp,DC=test,DC=local
Creating Container Roles in OU=Tier 1,OU=Administration,OU=Corp,DC=test,DC=local
Creating Container Access Control in OU=Tier 2,OU=Administration,OU=Corp,DC=test,DC=local
Creating Container Roles in OU=Tier 2,OU=Administration,OU=Corp,DC=test,DC=local
Creating Container Access Control in OU=Tier 1,OU=Service,OU=Corp,DC=test,DC=local
Creating Container Access Control in OU=Tier 0,OU=Service,OU=Corp,DC=test,DC=local

PS C:\Users\Administrator> 
```

Fix any error you come accross. And the Structure deployment is done.

# 2 Creating groups (Create-Groups.ps1)
Next step is to create groups that will be used for different purposes. The "**group-creation.csv**" file contains all default groups needed for a basic tiering model. You can add extra groups as you desire in the same format.

## Prerequisites 
- Again you will have to correct the domain name, but this time in the CSV file. Open the CSV file in a text editor and replace the same values as in step 1 to match you domain.
- Store the script file (Create-Groups.ps1) and the CSV file (group-creation.csv) in the same folder. The script looks for the CSV file in the root where the script is executed. If you use ISE, go to the location of the script, for example: ```cd c:\temp```
- Add any custom groups you want to deploy as well to the CSV file, see example:
```
Name,Path,GroupScope,GroupCategory
COMPTR_Default Container_CREATE,"CN=Access Control,OU=Tier 2,OU=Administration,OU=Corp,DC=test,DC=local",DomainLocal,Security
```

## Run Script
Now you can run the script, the output should give you information about the groups successfully created and als the ones that failed to be created. Check for errors in the output, correct them as needed and run the script again. Groups that already exist will be skipped, the ones that failed will be created if the issue is resolved.

```
If the script gives an error that it can't find the CSV file check if the script and csv are in the same folder and your console location is that folder.
```
