# Setup Kubernetes (K8s) Cluster on GCP


1. Create Ubuntu GCP Instance

2. Install gcloud
   ```sh
    sudo su -
    apt-get update
    snap search google-cloud-sdk
    sudo snap install google-cloud-sdk --classic
    # check gcloud
    which gcloud
    which gsutil
    gcloud --version
    ```

3. Install Kubectl on Ubuntu Instance
   ```sh
    curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
    chmod +x ./kubectl
    sudo mv ./kubectl /usr/local/bin/kubectl
    kubectl version
   ```

4. Install kops on Uuntu Instance
   ```sh
    curl -Lo kops https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
    chmod +x ./kops
    sudo mv ./kops /usr/local/bin/
    kops version
    ```

5.  Configure Default Credentials for Creating Bucket
    ```sh
    gcloud auth login
    gcloud auth application-default login
    ```

6. Check Zones, Instance and Group Instance List
    ```sh
      gcloud compute zones list
      gcloud compute instances list
      gcloud compute instance-groups list
    ```

7. Check Network list and Firewall Rules
    ```sh
      gcloud compute networks list
      gcloud compute networks subnets list
      gcloud compute firewall-rules list
    ```

8. List of Bucket and Create Bucket with unique name “ameintu”
   ```sh
    gsutil list
    sutil mb gs://ameintu
    export KOPS_STATE_STORE=gs://ameintu
    ```

9. Expose Environment Variable
   ```sh
    export KOPS_STATE_STORE=gs://ameintu    
   ```

10. Create ssh-keys before creating cluster – sudo su
   ```sh
     ssh-keygen
   ```

11. Check gcloud Info Details
   ```sh
    gcloud info
    echo $PROJECT
   ```

12. Create kubernetes Cluster
   ```sh
    PROJECT=`gcloud config get-value project`
    # to unlock the GCE features
    export KOPS_FEATURE_FLAGS=AlphaAllowGCE
    kops create cluster simple.k8s.local --zones us-central1-a --state ${KOPS_STATE_STORE}/ --project=${PROJECT}
   ```

13. Edit kubernetes cluser
    ```sh
    kops edit cluster simple.k8s.local
    ```

14. Edit Kubernetes Node Instance Group
    ```sh
    kops edit ig --name=simple.k8s.local nodes
    # Change machine type – n1-standard-2 to any other instance if required
    ```
    
15. Edit Kubernetes Master Instance Group
    ```sh
    kops edit ig --name=simple.k8s.local master-us-central1-a
    # Change machine type – n1-standard-1to any other instance if required
    ```

16. List Kubernetes Cluster
    ```sh
    kops get cluster
    ```
    
17. Finally Configure Cluster
    ```sh
    kops update cluster --name simple.k8s.local --yes
    # it will create below new resources. 
      GCP Instances and Instance Group
      Folders and Files in Bucket
      Create new Roles
      Create Load Balancer
      Create Firewall Rules
      Create Network and Subnet
    ```
    
18. Validate Kubernetes Cluster
    ```sh
    kops validate cluster
    ```
    
19. Connect to the Kubernetes Master Cluster
    ```sh
    ssh -i ~/.ssh/id_rsa admin@<Host-Name-Kubernetes-Master-Server>
    ssh -i ~/.ssh/id_rsa admin@api.simple.k8s.local
    exit
    ```
    
20. List Kubernetes Nodes
     ```sh
    kubectl get nodes
    ```

21. Delete Kubernetes Cluster
   ```sh
   kops delete cluster simple.k8s.local –yes
   kops get cluster
   ```

22. Delete Bucket
    ```sh
    gsutil list
    gsutil ls gs://ameintu/
    ```

23. Check Resources using gcloud Command
     ```sh
    gcloud compute zones list
    gcloud compute instances list
    gcloud compute instance-groups list
    gcloud compute networks list
    gcloud compute networks subnets list
    gcloud compute firewall-rules list
    ```
