# Gremlin chaos engineering demo for EKS cluster

1.	Create Amazon EKS Cluster & Deploy a demo Application
    a. First we will follow our official EKS Workshop to create a Cloud9 workspace from https://www.eksworkshop.com/020_prerequisites/workspace/ 
    b. Create IAM role for your workspace.  Give it administrator priveledges and attach that role to your Cloud 9 workspace.

2. Create key pair
    aws ec2 create-key-pair --key-name gremlin-demo-key

3. Clone repo
    git clone https://github.com/gmariconda76/gremlin.git

4. Install eksctl 
    curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
    sudo mv -v /tmp/eksctl /usr/local/bin

    eksctl completion bash >> ~/.bash_completion
    . /etc/profile.d/bash_completion.sh
    . ~/.bash_completion

5. Install Kubectl
    sudo curl --silent --location -o /usr/local/bin/kubectl \
    https://amazon-eks.s3.us-west-2.amazonaws.com/1.17.11/2020-09-18/bin/linux/amd64/kubectl

    sudo chmod +x /usr/local/bin/kubectl

    kubectl completion bash >>  ~/.bash_completion
    . /etc/profile.d/bash_completion.sh
    . ~/.bash_completion

6. Install Helm
    curl -sSL https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash

    helm completion bash >> ~/.bash_completion
    . /etc/profile.d/bash_completion.sh
    . ~/.bash_completion
    source <(helm completion bash)


7. Other Installs:
    sudo yum -y install jq gettext bash-completion moreutils
    sudo pip install --upgrade awscli && hash -r


8. Create EKS cluster
    cd environment/gremlin/eks-cluster/kubernetes/
    eksctl create cluster -f gremlin-demo-cluster.yaml 

9. Deploy test application
    cd environment/gremlin/ecsdemo-crystal/kubernetes/
    kubectl apply -f deployment.yaml
    kubectl apply -f service.yaml

    cd environment/gremlin/ecsdemo-nodejs/kubernetes/
    kubectl apply -f deployment.yaml
    kubectl apply -f service.yaml

    cd environment/gremlin/ecsdemo-frontend/kubernetes/
    kubectl apply -f deployment.yaml
    kubectl apply -f service.yaml

Gremlin Setup:
    First go to www.gremlin.com and register for a free account.  
    After you login for the first time you can go to your team settings and get your Gremlin Team ID and Gremlin Team Secret.  You will need those to complete the       setup.

10. Add Helm Repo
    helm repo add gremlin https://helm.gremlin.com

11. Create Gremlin namespace in EKS
    kubectl create namespace gremlin

12.  Deploy pods into Gremlin namespace
    Set environmental variables:
    GREMLIN_TEAM_ID="get key from gremlin team settings"
    GREMLIN_CLUSTER_ID="gremlin-demo-cluster"
    GREMLIN_TEAM_SECRET="get key from gremlin team settings"

    helm install gremlin gremlin/gremlin \
        --namespace gremlin \
        --set gremlin.secret.managed=true \
        --set gremlin.secret.type=secret \
        --set gremlin.secret.teamID=$GREMLIN_TEAM_ID \
        --set gremlin.secret.clusterID=$GREMLIN_CLUSTER_ID \
        --set gremlin.secret.teamSecret=$GREMLIN_TEAM_SECRET
