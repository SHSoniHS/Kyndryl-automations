# Kyndryl-automations
Testing Kyndryl automation space.
"Project_name": "dtl_project_Sonia_Herrero_Santos_SHS-Automation"
Shared environment user manual
Shared environment is immediately available to everyone in Kyndryl for free of charge. It contains a fixed set of endpoints that are available through Ansible automation. You can use it to test your playbook or to test deployment and execution of playbook of someone else. There will be installed middlewares with test data on all endpoints in this environment - currently in progress.

Shared environment is represented by a DTL organization on testing Tower. In this organization you are allowed to:

Create/delete your Projects by running Job template dtl_project_creation_automation
Create/delete your Job templates as in production (not execute them).
But instead execute prepared Workflow dtl_job_template_executor which will execute your Job template.
No other actions are allowed in this organization! Specifically it is not allowed to run any playbook or workflow, except the prepared workflow/template (described below). It is not allowed to do any changes in the inventory or credentials. Please be considerate to others and do not modify Projects and Job templates that are not yours. If you need to do some special change (e.g. add variable to inventory or create some credentials), please contact KDTL admins.

All actions in this environment are auditable. Follow BCG. Do not use real PI/SPI data. Please be aware that ANY customer data including hostnames, IPAddresses, Customer PI or SPI is NOT allowed on any of the Lab tools or products. When audited, if any customer information found, the employee will be held responsible according to Business Conduct Guidelines.

Known problems and limitations
Ansible Tower log in page is sometimes not working properly with Firefox browser. It is recommended to use different browser e.g. Chrome.
Ansible Tower used for shared environment is a testing Tower. It does not have production level support. Outages of the Tower may occur.
There is currently no way how to pass encrypted variables to the job from Survey. Survey variables needs to be provided through EXTRA VARIABLES This limitation may be eliminated in future when workaround is found.
Currently there is no possibility to pass secret variables (like passwords) to the playbooks via Survey.
Generic steps
Connect to testing Tower (see IP for hosts file bellow).
Create your Project by running Job template dtl_project_creation_automation
Create your Job template (do not run it).
Run prepared Workflow dtl_job_template_executor
Detailed walkthrough
1. Connect to testing Tower
https://ansible-tower-web-svc-tower.apps.ocp6n.sr1.ag1.sp.ibm.local

Prerequisites
Connected through IBM GlobalProtect VPN (ocean ID)
Correct record in your hosts file:
Windows: C:\Windows\System32\drivers\etc\hosts
Linux: /etc/hosts
Mac OS X: /private/etc/hosts
158.87.50.2   ansible-tower-web-svc-tower.apps.ocp6n.sr1.ag1.sp.ibm.local
Sign in
DO NOT insert USERNAME and PASSWORD to the Tower sign in page! Instead use w3id SAML button to sign in using your ocean ID intranet credentials.

SAML button

2. Create your Project
Due to Tower limitations users are not allowed to manually create Projects. To create your Project go to the Templates and execute Job template called dtl_project_creation_automation

Create project launch

Fill in input variables:

PROJECT NAME: Your naming of the project. It will be part of the full name of the project. This value is not the full name of the generated project, just part of the full name, e.g. my_project_1
GITHUB URL: Full HTTPS URL to the GitHub repository. All repositories with code executed in the shared environment (DTL organization) must be public.
GITHUB BRANCH: Branch of the repository.
GITHUB CREDENTIALS: Do not change default value if not instructed otherwise.
Create project

Launch the automation and wait until it finishes. In the end of the job log you will see the full generated name of your project. Copy-paste it for use in next step.

Create project get name

Before going to next step verify that the project was correctly created and synced with GitHub - go to the Projects and check that you see it there and it has solid (not blinking) green circle next to it. If the circle is red, then please verify that the link and branch are correct and that the repository is set to public. If the link or branch are not correct, edit the Project, fix the values and click on SAVE to save the modified Project. It will try to resync automatically. Other option is to delete the whole Project and re-create it again with correct values.

3. Create your Job template
Create your Job template in the same way as you would do it in production. DO NOT RUN THE JOB TEMPLATE! Your Job template will be executed as part of Workflow in next step.

Create template

NAME: All Job templates should start with prefix dtl_jobtemplate_ Remember the name, it will be needed in next step.
INVENTORY: Use inventory named dtl_inventory
PROJECT: Name of the project generated in previous step. You can link multiple Job templates to one Project.
PLAYBOOK: Select playbook you want to run. If you don't see the playbooks, verify that the Project was correctly created and synced with GitHub.
CREDENTIALS: Click on icon of magnifying glass, select ALL credentials you want to use and then click on SELECT. Table of available credentials:
CREDENTIAL TYPE	NAME	Note
credtype_jumphost_1hop	dtl_cred_sshproxy_1hop_azure_dtl	Must be used everytime you want to connect to any endpoint.
Machine	dtl_cred_linux_DTLAzure	Must be used if you want to connect to nonwindows endpoints. Only one Machine credentials can be used.
Machine	dtl_cred_windows_DTLAzure	Must be used if you want to connect to Windows endpoints. Only one Machine credentials can be used.
Ansible Tower	dtl_cred_tower_rvanek	Optional. Use only if the Tower credentials are required by the playbook.
LIMIT: Leave empty and click on PROMPT ON LAUNCH to enable it.
EXTRA VARIABLES: Insert extra variables needed by the playbook or leave it empty and enable PROMPT ON LAUNCH to fill it later in next step.
Click on SAVE to save the Job template.

4. Run prepared Workflow
Endpoints are stopped when there is no job running. They must be spun up before running your Job template and spun down after the Job template ends. There is prepared Workflow that does this for you. All you need to do is to execute this Workflow and insert information about your Job template that you want to run. Do not interrupt or cancel execution of the Workflow - always let the spin down playbook finish.

Testing groups structure

Open the Templates and run the workflow dtl_job_template_executor_workflow
Launch workflow

Insert the input variables and execute the workflow.
Variable name	Details
PRE-RUN JOB TEMPLATE NAME	Optional. Name of the Job template executed before your main job template. Leave empty if not needed.
MAIN JOB TEMPLATE NAME	Mandatory. Exact name of your Job template created in the previous step.
POST-RUN JOB TEMPLATE NAME	Optional. Name of the Job template executed after your main job template. Leave empty if not needed.
JOB TEMPLATE EXTRA VARS	Optional. Use if you selected PROMPT ON LAUNCH for EXTRA VARIABLES in previous step.
ENDPOINT HOSTNAME	Mandatory. Select endpoints where you want to run your playbook. Available endpoints are displayed in multiselect menu. Keep in mind that it is not possible to connect to Windows and non-Windows OS in a single job but you can select multiple endpoints.
Workflow triggers execution of your Job template. You can either find it in the Jobs or get the link to it in the job log of dtl_job_template_executor
dtl_workflow_log

dtl_workflow_joblink[Ansible Tower instructions.pdf](https://github.com/SHSoniHS/Kyndryl-automations/files/9812267/Ansible.Tower.instructions.pdf)
