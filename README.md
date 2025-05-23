# POC of CD on Terraform Module
![image](https://github.com/user-attachments/assets/b8d4409f-ff38-4700-be8c-aa534e1ebd04)


| **Author** | **Created on** | **Version** | **Last updated by**|**Last Edited On**|**Level** |**Reviewer** |
|------------|---------------------------|-------------|----------------|-----|-------------|-------------| 
| Nikita Joshi|   29-03-2025          | v1          | Nikita Joshi   |29-03-2025    |  Internal Reviewer | Komal Jaiswal |

## Table Of Contents
- [Introduction](#introduction)
- [Pre-requisites](#pre-requisites)
- [Implementation of CD on Terraform Module](#implementation-of-cd-on-terraform-module)
- [Terraform Module Structure](#terraform-module-structure)
- [Shared Library Jenkins File](#shared-library-jenkins-file)
- [Terraform groovy template file](#terraform-groovy-template-file)
- [Shared Library Src file](#shared-library-src-file)
- [Contact](#contact-information)
- [References](#references)

## Introduction
Terraform module CD (Continuous Deployment) automates infrastructure deployment using Terraform, ensuring changes are applied in a controlled manner. Jenkins provides a streamlined workflow for managing infrastructure code changes.

## Pre-requisites
- **Tools Required:**
  - Terraform
  - Jenkins

## Implementation of CD on Terraform Module

![image](https://github.com/user-attachments/assets/324551c5-c446-45da-b685-28c39dcff6c4)


### Tools Used

| Tool              | Purpose                                                                 |
|-------------------|-------------------------------------------------------------------------|
| Terraform plan    | Generates an execution plan outlining the changes Terraform will apply to the infrastructure|
| Terraform Apply   | Applies changes to reach desired state                                  |
| Terraform Destroy | Destroys Terraform-managed resources                                   |



### step 1. **Create a New Pipeline Job**
   - Navigate to the Jenkins dashboard and click on New Item.
   - Enter a name for your job (e.g., **Terraform-CD-Pipeline**)
   - Select `Pipeline` and click `OK`

### step 2. **Configure Pipeline Script**
   - In Pipeline section, select `Pipeline script from SCM`
   - Provide repository URL and credentials
     
     ![image](https://github.com/user-attachments/assets/f1c525a8-d496-4f9a-9e45-df2896537d2c)


### step 3.  **Save the Configuration**
  - Click on Save to save the job configuration.

### step 4. **Build the Pipeline**
  - Once the pipeline configuration is saved, you can manually trigger the build by clicking on Build Now.

### step 5.  **View Build Console Output**

- After triggering the build, you can view the progress and console output of the build by clicking on the build number in the Jenkins dashboard.
- The console output will display the steps executed by the pipeline script, including code checkout and compilation.
- Verify Successful Compilation.
    
![image](https://github.com/user-attachments/assets/696abf93-4421-41ce-bdb8-6bd15af15cac)
___
![image](https://github.com/user-attachments/assets/b31b1cda-a86b-4cad-8267-4c17774e7dd2)

___
![image](https://github.com/user-attachments/assets/7dc006a6-5123-434c-92ec-3e71968e78b5)




    
## Terraform Module Structure

![image](https://github.com/user-attachments/assets/e712ad44-1af9-48a1-bbd9-a3c0f492f555)
___

## [Shared Library Jenkins File]()
```groovy
@Library('terraform@Nikita-187') _

def cipipeline = new org.template.terraformCD()

node {
    def repoUrl = 'https://github.com/nikita647/terraform_infra.git'
    def branch = 'main'
    def gitPassword = 'git-cred'
    def terraformPath = "/var/lib/jenkins/workspace/job6"
    def terraformHome = tool 'terraform'
    env.PATH = "${terraformHome}/bin:${env.PATH}"
    
    properties([
        parameters([
            choice(name: 'action', choices: ['apply', 'destroy'], description: 'Choose any one option')
        ])
    ])
    
  def message = (params.action == 'apply') ? 'Approval for infrastructure apply' : 'Approval for infrastructure destroy'


    cipipeline.call(repoUrl, branch, gitPassword, terraformPath, message, params.action)
}
```

___
## [Terraform groovy template file](https://github.com/snaatak-Zero-Downtime-Crew/shared-library/blob/Nikita-SCRUM-337/src/org/template/terrraformCD.groovy)

``` groovy
package org.template

import org.common.*
import org.terraform.*

def call(String repoUrl, String branch, String credentialsId, String terraform_path, String message, String action) {

    clean = new cleanWs()
    clone = new gitClone()
    terraformInit = new terraform_init()
    terraformPlan = new terraform_plan()
    terraformApply = new terraform_apply()
    terraformDestroy = new terraform_destroy()
    manualApproval = new manualapproval()
    
    clean.call()
    clone.call(repoUrl, branch, credentialsId)
    terraformInit.call(terraform_path)
    terraformPlan.call(terraform_path)

    if (action == 'apply') {
       manualApproval.call(message ?: 'Approval for infrastructure apply')
        terraformApply.call(terraform_path)
    } else if (action == 'destroy') {
        manualApproval.call(message ?: 'Approval for infrastructure destroy')
        terraformDestroy.call(terraform_path)
    } else {
        error("Invalid action: ${action}. Please specify 'apply' or 'destroy'.")
    }
}
```

___
## [Shared Library Src file](https://github.com/snaatak-Zero-Downtime-Crew/shared-library/tree/Nikita-SCRUM-337/src/org)

![image](https://github.com/user-attachments/assets/7a4fa22b-3888-49bb-a70b-2f1c84a8fda5)

___
### **Contact Information**

| **Name** | **Email address**            | **Github ID**
|----------|-------------------------------|-------------------|
| Nikita joshi    |  Nikita.Joshi@mygurukulam.co   | https://github.com/jnikita19  |

---
### **References**

| **Link** | **Description**            |
|----------|-------------------------------|
| [Terraform module ](https://spacelift.io/blog/what-are-terraform-modules-and-how-do-they-work/)     | Overview of terraform module          |
| [Terraform module CI/CD](https://spacelift.io/blog/terraform-in-ci-cd)   | Comprehensive guideCI/CD. |

