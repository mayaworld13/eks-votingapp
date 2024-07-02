
# Cloud-Native Web Voting Application with Kubernetes

This cloud-native web application is built using a mix of technologies. It's designed to be accessible to users via the internet, allowing them to vote for their preferred programming language out of six choices: C#, Python, JavaScript, Go, Java, and NodeJS.

## Technical Stack

- **Frontend**: The frontend of this application is built using React and JavaScript. It provides a responsive and user-friendly interface for casting votes.

- **Backend and API**: The backend of this application is powered by Go (Golang). It serves as the API handling user voting requests. MongoDB is used as the database backend, configured with a replica set for data redundancy and high availability.

## Kubernetes Resources

To deploy and manage this application effectively, we leverage Kubernetes and a variety of its resources:

- **Namespace**: Kubernetes namespaces are utilized to create isolated environments for different components of the application, ensuring separation and organization.

- **Secret**: Kubernetes secrets store sensitive information, such as API keys or credentials, required by the application securely.

- **Deployment**: Kubernetes deployments define how many instances of the application should run and provide instructions for updates and scaling.

- **Service**: Kubernetes services ensure that users can access the application by directing incoming traffic to the appropriate instances.

- **StatefulSet**: For components requiring statefulness, such as the MongoDB replica set, Kubernetes StatefulSets are employed to maintain order and unique identities.

- **PersistentVolume and PersistentVolumeClaim**: These Kubernetes resources manage the storage required for the application, ensuring data persistence and scalability.

---
# Get Started

## Step 1: Configure Cluster

1. **Create Cluster Service Role and Policy**

   - Navigate to the IAM Management Console.
   - Create a new role with `AmazonEKSClusterPolicy` attached.
   - Name your role appropriately, for example, `EKSClusterServiceRole`.

2. **Create Cluster**

   - Go to the EKS Console and click on `Create cluster`.

   ![Create Cluster](https://github.com/mayaworld13/eks-votingapp/assets/127987256/fafccbbb-30f5-458f-a2ad-83e16a43e2d7)

3. **Cluster Configuration**

   - Enter your desired cluster name and Kubernetes version.
   - Attach the service role you created earlier.

   ![Configure Cluster](https://github.com/mayaworld13/eks-votingapp/assets/127987256/2c347ba3-82cf-4e7a-9379-7f10af6ddfca)

4. **Cluster Authentication**

   - Set the authentication method to `ConfigMap` and `EKS API`.
   - Click `Next`.

   ![Cluster Authentication](https://github.com/mayaworld13/eks-votingapp/assets/127987256/6f3aeb54-795e-4400-a367-0411c15ff6ec)

## Step 2: Specify Networking

1. **Networking Configuration**

   - Select the VPC, subnets, and security groups for your cluster.
   - Set the cluster endpoint access to `Public`.
   - Click `Next`.

   ![Networking](https://github.com/mayaworld13/eks-votingapp/assets/127987256/acca569b-94b8-4273-9c7e-3d5f619c0ed5)

## Step 3: Finalize and Create

 - Review your configurations and make sure everything is correct.
 - Leave the remaining settings as default.
 - Click on `Create Cluster`.

## Step 4: Addons EBS

1. **Add EBS CSI Driver**

   - Once your cluster is created, go to the `Addons` section.
   - Click on `Get more addons` and select the `EBS CSI driver`.

   ![EBS CSI Driver](https://github.com/mayaworld13/eks-votingapp/assets/127987256/d34bc953-97a6-4b13-8dfd-c726a758349c)

2. **Install EBS CSI Driver**

   - Click `Next`, then `Next` again, and finally `Create`.

And that's it! Your EKS cluster now has the EBS CSI driver installed, enabling persistent volumes and PVCs.



1. **Create IAM Role for EC2**

   - Use the following policy for the EC2 role:
 
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [{
           "Effect": "Allow",
           "Action": [
               "eks:DescribeCluster",
               "eks:ListClusters",
               "eks:DescribeNodegroup",
               "eks:ListNodegroups",
               "eks:ListUpdates",
               "eks:AccessKubernetesApi"
           ],
           "Resource": "*"
       }]
     }

2. **Install Kubectl and AWS CLI on EC2**:
   - install kubectl
   
     ```bash
     curl -O https://s3.us-west-2.amazonaws.com/amazon-eks/1.24.11/2023-03-17/bin/linux/amd64/kubectl
     chmod +x ./kubectl
     sudo cp ./kubectl /usr/local/bin
     export PATH=/usr/local/bin:$PATH
     ```
   - Verify kubectl installation
     ```bash
     kubectl version --client
     ```
   - Install AWScli:
     ```bash
     curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
     unzip awscliv2.zip
     sudo ./aws/install
     ```
   - Verify AWS CLI installation
     ```bash
     aws --version
     ```
3. **Configure EKS Cluster Context**
   - Once the Cluster is ready run the command to set context:

     ```bash
     aws eks update-kubeconfig --name EKS_CLUSTER_NAME --region us-east-1
     ```
   - To check the nodes in your cluster run

     ```bash
     kubectl get nodes
     ```






