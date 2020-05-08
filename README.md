## Setup

To start the DSL lab we have provided a [DevWorkStation.json](https://raw.githubusercontent.com/bmp-ntnx/QuickStartCalmDSL/master/DevWorkstation.json) blueprint to quickly get you started. The included DevWorkstation.json builds a CentOS VM with all the necessary tools.  This blueprint can be launched directly from Calm, but we recommend publishing it to the Calm Marketpkace for Self Service.  Also, included is [software-developer.png](https://github.com/bmp-ntnx/QuickStartCalmDSL/blob/master/software-developer.png) which can be used as an icon

## Launch DevWorkstation from Calm Marketplace

![Alt text](images/MPDevWorkstation.png)

-   **Important: Select the Credentials tab and enter desired User/Pass**

![Alt text](images/Creds.png)

-   Enter the name of Application "DevWorkstation-\<INITIALS\> and fill out the form with the requested information

![Alt text](images/DevLaunch.png)

-   Press Create

-   While waiting review the audit log to see packages being deployed.  The blueprint automatically installs several utilities along with Calm DSL

## Once the application is "running" SSH to the DevWorkstation

-   The IP address of the DevWorkstation is listed under the application overview.  The SSH user/pass is what was set under the credentials tab


![Alt text](images/IPaddress.png)

## Start the virtual environment and connect to Prism Central

-   cd to the "calm-dsl" directory from home

-   Run ```source venv/bin/activate``` to switch to the virtual environment. This will enable the virtual environment for Calm DSL

-   **Optional:** This has already been done automatically through the blueprint launch. Once you SSH into the DevWorkstation we can setup the connection to Prism Central by running ```calm init dsl```

-   Verify the current config settings by running ```calm show config``` 


![Alt text](images/Config.png)

## List the current blueprints in Calm

-   Run ```calm get bps``` and we see all the blueprints in Calm with their UUID, description, application count, project, and state


![Alt text](images/getbps.png)

-   Run ```calm get bps -q``` to display quiet output with only the BP names


![Alt text](images/calmgetbpsq.png)

## Review python based blueprint and make a modification

-   Change to the "HelloBlueprint" directory and run ```ls```

    -   This directory and it's contents were automatically created during the blueprint launch.  As part of the DevWorkstation blueprint launch we ran ```calm init bp``` which creates a sample blueprint configured to the connected Calm instance

-   There is a file called "blueprint.py" which is a python version of a blueprint

-   There is a "scripts" directory. This is where the bash/powershell/python scripts are stored that are referenced within the blueprint


![Alt text](images/hellols.png)

-   Run ```vi blueprint.py```

-   Review the blueprint for familiar constructs.  To skip directly to a line enter ```:<linenumber>```

    -   Credentials (line 54-60)

    -   OS Image (line 62-66)

    -   Under class HelloPackage(Package) you will see references to the pkg\_install\_task.sh script in the scripts directory (line 139)

    -   Basic VM spec information (vCPU/memory/disks/nics) (line 153-159)

    -   Guest Customization contains cloud-init (line 161-171)

-   In the blueprint.py modify the number of vCPU

    -   Change the vCPU from 2 to 4 (line 154)


![Alt text](images/vcpu.png)

-   Add a unique VM name using a macro (line 185)

    -   ```provider_spec.name = "<Initials>-@@{calm_unique}@@"```


![Alt text](images/vmname.png)

-   Write/quit ```:wq``` the .py blueprint file to save and close

## Modify pkg\_install\_task.sh

-   Change to the scripts directory and run ```ls```. We will see 2 scripts that are being referenced inside blueprint.py

-   Run ```cat pkg_install_task.sh``` to view the current contents of the install script.  What does the script do?


![Alt text](images/more1.png)

-   Run ```curl -Sks https://raw.githubusercontent.com/bmp-ntnx/prep/master/nginx > pkg_install_task.sh``` to replace the existing install script

-   Run ```cat pkg_install_task.sh``` to view the changed script.  What does the script do now?


![Alt text](images/more2.png)

## Push the modified blueprint.py to Calm

-   Return to the "HelloBlueprint" directory

-   Run ```calm create bp --file blueprint.py --name FromDSL-<Initials>```

    -   This converts the .py file to json and pushes it to Calm


![Alt text](images/syncbp.png)

-   **Optional:** Run ```calm compile bp -f blueprint.py``` to view the python blueprint in json format from DSL

-   Verify your new blueprint by running ```calm get bps -q | grep FromDSL-<Initials>```


![Alt text](images/verifygrep.png)

## Launch the blueprint into an application

-   Run ```calm get apps``` to verify all the current applications before launching your new app

    -   We can also run ```calm get apps -q``` to quiet the details like we did with blueprints earlier

-   Launch your newly uploaded blueprint into an application

-   Run ```calm launch bp FromDSL-<Initials> --app_name AppFromDSL-<Initials> -i```


![Alt text](images/launchbp.png)

-   Run ```calm describe app AppFromDSL-<Initials>``` to see the application summary

-   Once the app status changes to "running" we will have a nginx server deployed from Calm DSL!


![Alt text](images/describe.png)

-   Run ```calm describe app AppFromDSL-<Initials> --out json | grep -F '[{\"ip\":\"'``` to search the json output for the VM IP


![Alt text](images/getip.png)

-   Enter the IP in a web browser and this will take you to the nginx **"Welcome to DSL"** web page

![Alt text](images/welcome.png)

## Log into Prism Central to verify

-   We can see the blueprint created from DSL

-   We can see the application launched from DSL
