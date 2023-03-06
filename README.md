Complete CNAPP SADA Demo using Prisma Cloud demonstrating Cloud Code Security, Cloud Workload Protection (Agent, Agentless, Web Application and API Security) , Cloud Infrastructure Entitlement Management, Cloud Data Security, Cloud Security Posture Management. Demonstrates how the platform adds value to different persona's starting from developers all the way up to executives.
Uses Github actions for CI and CD process.

> This demo only focuses on some of the capabilities of modules within Prisma Cloud. Please refer to the documentation for a full list of capabilities
## Intent:
 Demo intent is to showcase Prisma Cloud Capabilities across:
  - Multiple clouds (EKS,GKE,AKS)
  - Multiple form factors (Container, Host, Serverless, Google Run)
  - Different phases of application lifecycle
  - Multiple teams
  - Processes which are already part of organizational day to day activities

AND you have a copy-paste coder like Sandeep on your team :)    
I also want to showcase how not following best practices at dev/devops level would have an increased blast radius on your team.

## Installation:
 - Terraform templates are provided in the IaC folder. These are for reference only. Please modify as per your requirements.
 - Yaml files for K8s deployment are provided in root folder
 - Github action files can be found in .github folder and secrets configured can be found [here](https://github.com/chanduusc/prismacloud-demo/blob/schandu-tmp/IaC/README.md#secrets)
## Code Security:
### Coding phase (Persona: Developer/Devops)
1. Install Checkov plugin for VSCode (or IntelliJ) 
2. Copy copy_to_req file to requirements.txt
3. The developer/devops will be notified of misconfigurations/vulnerabilites within IDE while coding.
![Check the vulnerabilities in IDE](img/checkov_plugin.png "Check the vulnerabilities in IDE")
> Example files for terraform, yaml, and secrets can be found in fake_commits folder. Please use as per your requirement.
### Review/CI phase (Persona: Dev/Devops Leads/Managers)
1. Integrate your Github repo with Primsa Cloud Code Security module.
2. Push the code to your branch and raise a pull request.
3. The reviewers will be notified of misconfigurations/vulnerabilites in review process. This makes reviewers aware of the security issues in addition to coding issues.
4. Code-checkins are gated by security as PC scans become one of the checks during CI processes.
![Failed check in the Github Console](img/review_failed_gh_console.png "Failed check in the Github Console")
![Prisma bot comment in Github review process](img/prisma-cloud-devsecops-bot.png "Prisma bot comment in Github review process")
### Review/CI phase (Persona: Secops/Security Team)
1. Security teams can see the same failures in the Prisma Cloud console.
2. Security teams can submit fixes from the Prisma Cloud console which will open a new PR against your repo.
3. Prisma bot will mark the comments outdated once fixed.
![Check the vulnerabilities in PC Console](img/review_failed_pc_console.png "Check the vulnerabilities in PC Console")
![Submit fix from the PC Console](img/submit_pr_from_pc.png "Submit fix from the PC Console")
![Verify PR in the Github](img/pr_opened_by_prisma_cloud.png "Verify PR in the Github")
![Outdated activities after fixing the issue](img/outdated_requirements.png "Outdated activities after fixing the issue")

__**Please pay attention to docker file where apt is used against best practices**__
![APT warning](img/apt-alert.png "APT warning")
## Cloud Workload Protection:
__**Vulnerability policies are used as examples here. Can be used with compliance policies too**__
### CD phase (Persona: Developer/Devops)
1. Github actions trigger build and deploy jobs in EKS,GKE, and AKS.
2. Prisma Cloud's image scan is inserted as part of the github actions.
3. Policies for vulnerabilites (or compliance) can be set in Prisma Cloud.
4. Prisma Cloud will scan the image and fail the CD job hence the non-approved images are not pushed to registries.
> Remember Code security warned you about APT :)

![Failed build in CD process](img/gh_failed_build.png "Failed build in CD process")
### CD phase (Persona: Secops/Security Team)
1. Sets the polices related to images being built on day to day basis within the tools/processes which are already part of the organization.
2. Can view the real-time status of builds in the Prisma Cloud console.
3. Can debug/know which layer of the build introduced the vulnerabilities.
![PC policy for vulnerability severity](img/pc-vuln-policy.png "PC policy for vulnerability severity")
![PC status showing where vulnerabilities got introduced](img/pc_failed_build.png "PC status showing where vulnerabilities got introduced")
![PC showing CD status](img/pc_cd_status.png "PC showing CD status")
### Image storage in registry (Persona: Secops/Security Team)
1. Image scanning is important after image build and push to registry.
2. Vulnerabilites present in the image might be discovered after the image is built.
3. The same applies to deployed images.
![Image scan - registry](img/registry_image_scan.png "Image scan - registry")
![Image scan - deployed](img/deployed_image_scan.png "Image scan - deployed")
### Deploy phase (Persona: Devops and Production Engineering)
1. Devops teams can see the vulnerable images not getting deployed.
2. The reason of failure (policy) can be viewed in audit logs.
![Deploy fail - audit logs](img/kubectl_events.png "Deploy fail - audit logs")
![Deploy fail - pods](img/kubectl_pods.png "Deploy fail - pods")
### Deploy phase (Persona: Secops/Security Team)
1. Security team can set policies around deployment. Advanced features/exceptions can also be configured.
2. A message can be set so deployment teams can see the reason/next steps.
3. Deployment failures are logged in the PC Events tab.
![Deploy policy - PC](img/pc_deploy_policy.png "Deploy policy - PC")
![Deploy policy cont - PC](img/pc_deploy_policy_cont.png "Deploy policy cont - PC")
![Deploy audit - PC](img/pc_deploy_audits.png "Deploy audit - PC")
>Notice that the message set in Prisma Cloud console is visible in k8s audit logs
### Runtime (Persona: Secops/Security team)
1. Set runtime rules so that no malicious activities are happening during run time.
2. Set WAAS rules to protect your applications from real-time attacks.
>We have unrealistic example in wild.py but any imported module can be the bad actor. Requirements.txt scan of code security should have caught it realistically.
3. Observe the events and alerts to keep track of the production applications health.
>Use tools like nuclei to simulate attacks --> nuclei -u http://<API Endpoint>/ -t nuclei-templates/f/

![Runtime policy - PC](img/runtime_policy.png "Runtime policy - PC")
![Runtime audit - PC](img/runtime_audit_pc.png "Runtime audit - PC")
![WAAS policy - PC](img/waas_policy.png "WAAS policy - PC")
![WAAS audit - PC](img/waas_audit_log.png "WAAS audit - PC")
![WAAS attacks - PC](img/waas_attacks.png "WAAS attacks - PC")
### Runtime (Persona: Production Engineering)
1. Work closely with dev and security teams based on the production runtime results.
2. Provide continous feedback about app/api deployment pattern to security teams.
![Runtime pod error in k8s - PC](img/runtime_pod_k8s.png "Runtime pod error in k8s")
## Cloud Infrastructure Entitlement Management:
### Post Deployment (Production Engineering / Secops or Security team)
1. CIEM module detects that one of the nodes have escalated permissions.
2. The nodes has permissions to perform any action(*) on s3 service. Hence those capabilities are inherited by pods.
![Get host info - kubectl](img/kubectl_get_instance_id.png "Get host info - kubectl")
![Node asset view - PC](img/node_asset_pc.png "Node asset view - PC")
![Node alerts list - PC](img/alerts_list.png "Node alerts list - PC")
![Node graph view - PC](img/pc_graph.png "Node graph view - PC")
## Cloud Data Security:
### Post Deployment (Production Engineering / Secops or Security team)
> This is the only place where something is hardcoded in this entire project (Azure blobname and AWS s3 bucket needs to be changed)
1. The script wild.py copies malware and sensitive files to S3 buckets (and azureblob) which are public.
2. Again developers aren't educated about security and this is a consequence of their lack of security awareness.
![Data alerts list - PC](img/pc_data_sec_alerts.png "Data alerts list - PC")
## Cloud Security Posture Management:
### Post Deployment (Production Engineering / Secops or Security team and compliance teams)
1. Prisma Cloud ships with a lot of out of box policies which provides visibility (or protection) across modules.
2. Can generate reports for compliance, business units, and cloud security assesments on a regular basis.
3. Review your compliance and asset trends to check how your organization is doing against time.
4. Setup alerts to be emailed to concerned people in your organization and audit them on regular basis.
5. Make sure you integrate alerts with change management and/or incident reporting tools to track remidiation.[Integrations link](https://docs.paloaltonetworks.com/prisma/prisma-cloud/prisma-cloud-admin/configure-external-integrations-on-prisma-cloud/prisma-cloud-integrations)
![Compliance Overview- PC](img/node_asset_pc.png "Compliance Overview- PC")
![Reports - PC](img/cspm_reports.png "Report - PC")
![Asset Inventory - PC](img/asset_inventory.png "Asset Inventory - PC")


