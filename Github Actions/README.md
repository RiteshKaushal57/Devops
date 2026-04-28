### What is Github Actions?
GitHub actions is the service introduced by GitHub which is typically used by DevOps engineers to automate their build, test and deployment activities.

### Explain Github workflow structure.

name:
on (trigger):
jobs:
   name of the job:
     runs-on (where should this job run): 
     steps:
     - name of the step:
     - run:

### Guthub Actions triggers
Triggers determines when a workflow should run.  
 **1. Event based triggers**     
 **2. Manual triggers**    
 **3. Scheduled triggers**   
 **4. Workflow triggers**

If we have env variable at job level and workflow level, then workflow level takes precedence.