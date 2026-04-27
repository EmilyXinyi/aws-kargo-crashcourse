# aws-kargo-crashcourse
A hands-on Kargo crash course for AWS Container Days

# Prerequisites 
* Akuity account (https://training.akuity.cloud) 
* EKS cluster 
* Argo CD Instance 
* Kargo Instance
* Access to both Argo CD and Kargo Instance control planes 
* A fork of this repo, inside of your own repo 
* A personal access token (PAT) at grants read and write access to this repo 

# Step-by-Step Instructions
* create your argo cd agent (ie. register this cluster to the control plane)
* create your kargo agent

`akuity login`

Project name: kargo-aws
```
akuity argocd apply -f app-project.yaml --name <argocd instance name> --org-name=<org name>

export GITOPS_REPO_URL=<your repo URL>
export GITHUB_USERNAME=<your github username>
export GITHUB_PAT=<your PAT>
```

Creating some Argo CD applications 

`envsubst < apps/applicationset.yaml | akuity argocd apply -f - --name <argocd instance name> --org-name=<org name>`

In the Argo CD dashboard, you should see three Applications:

`kargo-aws-test`, `kargo-aws-uat`, `kargo-aws-prod`

You will notice all three Argo CD Applications have not yet synced because they're not configured to do so automatically, and in fact, the branches referenced by their targetRevision fields do not even exist yet. In Argo CD, these Applications will show a status of Unknown until those branches exist and you begin promoting in later challenges.

Create Kargo project
`akuity kargo apply -f kargo-resources/project.yaml --name <kargo instance name> --org-name=<org name> `

Kargo needs credentials to access your GitOps repository. This is a crucial step that enables Kargo to:
* Read application manifests from different branches
* Create and update stage-specific branches
* Commit changes when promoting Freight between stages

`envsubst < secret.yaml | akuity kargo apply -f - secret.yaml --name <kargo instance name> --org-name=<org name> `

Create a Warehouse to discover new versions of container images.

`akuity kargo apply -f kargo-resources/warehouse.yaml --name <kargo instance name> --org-name=<org name> `

Create the PromotionTask that defines how Freight gets promoted.

`envsubst < kargo-resources/promotion-task.yaml | akuity kargo apply -f - kargo-resources/promotion-task.yaml --name <kargo instance name> --org-name=<org name>`

Create three stages (test, uat, prod) to form your deployment pipeline.

`akuity kargo apply -f kargo-resources/stages.yaml --name <kargo instance name> --org-name=<org name> ` 

The pipeline is now ready! Freight discovered by the Warehouse can be promoted through the stages, with each stage requiring the previous stage to be healthy before promotion is allowed.

Your first promotion
* drag and drop to test 
* dashboard UI to UAT (the truck icon)
* CLI to prod

`kargo login https://kargo.example.com (--sso --admin --kubeconfig) `

`kargo promote --project <kargo project name> --stage prod --freight-alias <alias-name>`

# Bonus tasks

* Create a new freight and promote it 
* Perform a roll back
* Turn on auto-promotion
* Change colours of the stages
* Add another step to promotion steps 
    - Make Kargo say "hello" with custom steps! 
* Use Argo CD to orchestrate Kargo resources (hint: App-of-Apps pattern)
