PROJECT_ID=anthos-onprem-demo-314300
# Setup Connect Gateway Impersonations
gcloud services enable \
    connectgateway.googleapis.com \
    anthos.googleapis.com \
    gkeconnect.googleapis.com \
    gkehub.googleapis.com \
    cloudresourcemanager.googleapis.com \
    cloudbuild.googleapis.com \
    --project ${PROJECT_ID}

MEMBER=serviceAccount:574816103089@cloudbuild.gserviceaccount.com
gcloud projects add-iam-policy-binding ${PROJECT_ID} --member ${MEMBER} --role roles/gkehub.gatewayAdmin
gcloud projects add-iam-policy-binding ${PROJECT_ID} --member ${MEMBER} --role roles/gkehub.viewer

MEMBER=user:admin@zaynabs.altostrat.com
gcloud projects add-iam-policy-binding ${PROJECT_ID} --member ${MEMBER} --role roles/gkehub.gatewayAdmin
gcloud projects add-iam-policy-binding ${PROJECT_ID} --member ${MEMBER} --role roles/gkehub.viewer


# Apply impersonation policy to the cluster.
kubectl apply -f config/impersonate.yaml

# Apply permission policy to the cluster.
kubectl apply -f config/admin-permission.yaml

#--------------------------------------------------------------------
# The below will setup the KUBECONFIG via Connect Gateway
gcloud alpha container hub memberships get-credentials $CLUSTER_NAME --project $PROJECT_ID
