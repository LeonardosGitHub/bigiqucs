# bigiqucs


* Pre-Reqs:
    * Add device(s) to BIG-IQ
    * Rest-Proxy enabled for each device
        * https://support.f5.com/csp/article/K79939355#discover_enable
    * Create scheduled back-up

* Provide to playbook:
    * var: user
    * var: password
    * var: existingBackupName
    * var: management port of BIG-IP

* Steps completed during re-pave event:
    * Retrieve all scheduled backups from BIG-IQ
        * parse returned backups using variable 'existingBackupName'
        * pull relevant information and create a dictionary of necessary items to create backup task
    * Send request to BIG-IQ to start backup task
        * Verify above task finished successfully
    * Parse backup task to create dictionary of devices to be backed up
        * infomation will include name, IP, machineID, and management port
        * write this information to a file
    * Gather pool member status from each BIG-IP being backed up, example: up|down|unchecked
        * this uses the rest-proxy functionality of BIG-IQ
        * write this information to a file
    * Transfer UCS files from BIG-IQ to ansible host
        * this is necessary as once devices are offline they will no longer be able to communicate with BIG-IQ
    * Pause playbook for re-pave activities
        * this playbook will pause for up to 4 hours
    * targeting BIG-IPs directly
        * update hostname of BIG-IPs, using bigip_hostname module
        * save sys config, using bigip_config module
        * upload ucs to re-pave'd "factory reset" device, using bigip_ucs module
    * Pause for 5 min to allow for UCS restore and device(s) to settle
    * Check BIG-IPs /var/log/ltm file for "UCS installation success"
        * this serves as two functions 1)verify connectivity between BIG-IQ and BIG-IP has been restored 2) that UCS was successfull 
        * this uses the rest-proxy functionality of BIG-IQ
    * Gather pool member status from each BIG-IP, post UCS restore
        * this uses the rest-proxy functionality of BIG-IQ
        * write this information to a file
    * compare the pre & post status files using diff, if fails print out message to check devices
