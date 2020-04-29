# Ansible NetApp Volume with Qtree Automation
 
Create a new netapp encrypted volume, qtree, share, set permissions on share, and NTFS on storage based on AD group. 

## Requirements:
   -  ansible <2.8
   -  DATAONTAP <9.6
    
## Required settings:
   - On NetApp Cluster:
     - Create a dedicated admin account ssh,ontapi,console
     - ***set -priv advanced; system services web modify -http-enabled true*** command set on storage

## File Descriptions:
   - AnsibleVolQtreeShare.yml    - Main ansible playbook no modifications required
   - uservars.yml           - Variables that will need updating with each execution
   - defaultvars.yml        - Default Variables for volume creation and setup
   - secret.yml               - contains host user credentials for setup of config (optional can use extra vars or setup prompt in ansible play)

## Basic setup
   - Install prerequisites
   - Configure storage for ansible
   - Clone repo
   - Review the AnsibleVolQtreeShare.yml
   - Update secret.yml, uservars.yml, defaultvars.yml


## Usage examples:
Basic play with all vars in var files

***ansible-playbook AnsibleVolQtreeShare.yml***

Remove volume and other things from the play above

***ansible-playbook AnsibleVolQtreeShare.yml --extra-vars="state=absent"***

To skip using the NTFS and share hardening use the following

***ansible-playbook AnsibleVolQtreeShare.yml --skip-tags=harden***

***currently there are these tags avalible:
    - harden            # Skips sections that create ntfs group rights 
    - no-admin-access   # Skips the removal of the Admin Access to the share only
    - cifs_share        # Does not create cifs share
    - no-quota          # Does not create hard quota limit on the qtree

##### Multiple varibles can be combined to execute a command vs updating each time the play is used.
***ansible-playbook AnsibleVolQtreeShare.yml --extra-vars="svm='svm01',volume_name='vol02',volume_share='Qtree12',aggr='agggr1',qtree_gb_size=4,vol_gb_size=50,netapp_hostname='Cluster02',netapp_username='Ansible-admin',netapp_password='Ansibe-password',useradmin_acct='AD-UserAdminGroup',usergroup_acct='ADUSERSGRP01',vol_qospolicy='extreme'"***


Note: the NVE encryption flag has been set to true by default. set encrypt="false" if this is not the desired behavior.

If you run into issues with ansible use the -vvv flag to show full output 
The top issues in ansible are:
   - syntax errors spaces not tabs!
   - module installation or configuration of storage
   - version compatibility issues older versions of ontap work with some of the features not all. 
