# Setup Kubernetes (K8s) Cluster on AWS


1. Create Ubuntu EC2 instance
2. Install AWSCLI
   ```sh
    sudo apt-get update
    curl https://s3.amazonaws.com/aws-cli/awscli-bundle.zip -o awscli-bundle.zip
    apt install unzip python
    unzip awscli-bundle.zip
    #sudo apt-get install unzip - if you dont have unzip in your system
    ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
   ```

3. Install Kubectl on Ubuntu Instance
   ```sh
    curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
    chmod +x ./kubectl
    sudo mv ./kubectl /usr/local/bin/kubectl
   ```

4. Install kops on Ubuntu Instance
   ```sh
    curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
    chmod +x kops-linux-amd64
    sudo mv kops-linux-amd64 /usr/local/bin/kops
   ```

5. Create an IAM role with Route53, EC2, IAM and S3 full access

6. Attach IAM Role to Ubuntu Instance
   ```sh
    # Note: If you create IAM user with programmatic access then provide Access keys. Otherwise region information is enough
    aws configure
   ```

7. Create a Route53 private Hosted Zone (you can create Public hosted zone if you have a domain)
   ```sh
    Routeh53 --> hosted zones --> created hosted zone  
    Domain Name: ameintu.net
    Type: Private hosted zone for Amzon VPC
   ```

8. Create an S3 Bucket
   ```sh
    aws s3 mb s3://demo.k8s.ameintu.net
   ```
   
9. Expose Environment Variable
   ```sh
    export KOPS_STATE_STORE=s3://demo.k8s.ameintu.net
   ```

10. Create sshkeys before Creating Cluster
   ```sh
    ssh-keygen
   ```

11. Create Kubernetes Cluster Definitions on S3 bucket
   ```sh
    kops create cluster --cloud=aws --zones=ap-south-1b --name=demo.k8s.ameintu.net --dns-zone=ameintu.net --dns private 
   ```

12. Create Kubernetes Cluser
   ```sh
    kops update cluster demo.k8s.ameintu.net --yes
   ```

13. Validate Kubernetes Cluster
   ```sh
    kops validate cluster
   ```

14. List Kubernetes Nodes
   ```sh
    kubectl get nodes
   ```

15. Delete Kubernetes Cluster
    ```sh
     kops delete cluster demo.k8s.ameintu.net --yes
    ```
