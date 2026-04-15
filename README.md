# aws-kargo-crashcourse
A hands-on Kargo crash course for AWS Container Days

It is currently a WIP and constantly evolving

Project name: kargo-aws

akuity argocd apply -f app-project.yaml --name <argocd instance name> --org-name=<org name>

envsubst < applicationset.yaml | akuity argocd apply -f - --name emily-demo --org-name=akuity

In the Argo CD dashboard, you should see three Applications:

kargo-demo-test
kargo-demo-uat
kargo-demo-prod
You will notice all three Argo CD Applications have not yet synced because they're not configured to do so automatically, and in fact, the branches referenced by their targetRevision fields do not even exist yet. In Argo CD, these Applications will show a status of Unknown until those branches exist and you begin promoting in later challenges.\

Kargo needs credentials to access your GitOps repository. This is a crucial step that enables Kargo to:

Read application manifests from different branches
Create and update stage-specific branches
Commit changes when promoting Freight between stages

envsubst < secret.yaml | akuity kargo apply -f - secret.yaml --name kargo-emily --org-name=akuity 
Kargo instance applied

Create a Warehouse to discover new versions of container images.

akuity kargo apply -f kargo-resources/warehouse.yaml --name kargo-emily --org-name=akuity 

Create the PromotionTask that defines how Freight gets promoted.

envsubst < kargo-resources/promotion-task.yaml | akuity kargo apply -f - kargo-resources/promotion-task.yaml --name kargo-emily --org-name=akuity

Create three stages (test, uat, prod) to form your deployment pipeline.

akuity kargo apply -f kargo-resources/stages.yaml --name kargo-emily --org-name=akuity  

The pipeline is now ready! Freight discovered by the Warehouse can be promoted through the stages, with each stage requiring the previous stage to be healthy before promotion is allowed.

Your first promotion
* drag and drop to test 
* dashboard UI to UAT (the truck icon)
* CLI to prod