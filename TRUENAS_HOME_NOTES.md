# TRUENAS STORE

## MACHINE

1x 32gbor64gb m.2SATA
5x 4tb HDD

login creds use root and ip to server for webportal

Added all 5 4tb drives to Pool as RaidZ1. 14~TB available of 20TB physical.

CUrrently one Dataset on it, specified case sensitivity insensitive and SMB

Added the group for specific user account group first nasuser

then added permissions to the user by giving them group access

SMB auth for all

went to sharing > SMB, share the dataset specifically. gave reasonable name

can access it by \\IPADDRESS\datasetSMBsharedName

add specific creds for user generated

### Adding network drive on windows

right click on My pc in file explorer 

map network drive

add path to share

click connect on login, choose other account details

enter user created that has access to that dataset
