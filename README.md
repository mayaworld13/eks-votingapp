
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


## Step 5: Create Nodegroup (2 nodes of t2.medium instance type)

1. **Name your Nodegroup and Node IAM Role**

   - Navigate to the EKS Console and select your cluster.
   - Click on `Add node group`.
     
   ![Create Nodegroup](https://github.com/mayaworld13/eks-votingapp/assets/127987256/0c1129b2-0457-4453-96c6-13220ce22b59)

2. **Create Node IAM Role**

   - Choose `Create a new IAM role`.
   - Select the following permissions for your Node IAM role:

   ![Nodeiamrole](https://github.com/mayaworld13/eks-votingapp/assets/127987256/7b87ae42-7e06-4b3d-a0da-581ce3546333)

3. **Select Instance Configuration**

   - Click `Next` and select `Amazon Linux 2` as the AMI type.
   - Choose `t2.medium` as the instance type.
   - Leave the rest of the configurations as default.
   - Click `Next`, review the settings, and then click `Create`.

   ![Instance Configuration](https://github.com/mayaworld13/eks-votingapp/assets/127987256/9efa1e63-4287-4a7a-a5b9-4a5ad80f5697)

4. **Wait for Nodegroup Creation**

   - The nodegroup creation process may take a few minutes. Once complete, your EKS cluster will have a nodegroup with two `t2.medium` instances.
  

## Step 6: Set Up Project in EC2 t2.micro

Follow these instructions to set up your project on an EC2 instance:

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

## Step 7: Troubleshoot Error When Accessing EKS from Local Machine

If you encounter the "Unauthorized" error when trying to access your EKS cluster from EC2 or your local machine, follow these steps to troubleshoot and resolve the issue:

1. **Check IAM Access Entry in EKS Cluster**

   - Navigate to the EKS Console and select your cluster.
   - Go to the `Access` section and check the IAM roles and users listed and you will see that it is created by the root user

   ![Access Section](https://github.com/mayaworld13/eks-votingapp/assets/127987256/420ef560-4b40-4560-b7e5-ce31ac76694b)

2. **Add IAM Access Entry**

   - Click on `Create access entry`. 

   ![Create Access Entry](https://github.com/mayaworld13/eks-votingapp/assets/127987256/df00f9b0-20ac-49bd-863b-a826ccb73f7e)

   - Select your IAM user from the dropdown under IAM principal ARN (e.g., `mayank`) and copy the ARN of your user.

   ![IAM User ARN](https://github.com/mayaworld13/eks-votingapp/assets/127987256/adf9b79c-a867-4f14-bf63-5b7a5e61884f)

3. **Configure IAM Access**

   - Paste the ARN of your IAM user in the `Username` section.
   - Click `Next`.

4. **Add Permissions**

   - Add the `AmazonEKSClusterAdminPolicy` to your IAM user.
   
   ![Add Policy](https://github.com/mayaworld13/eks-votingapp/assets/127987256/2ac2ce1b-431d-49d5-bbfb-663fc3ded8b1)

5. **Review and Create**

   - Review the settings and click `Create` to add the IAM access entry.

Once the IAM access entry with `AmazonEKSClusterAdminPolicy` is added for your IAM user, retry accessing your EKS cluster from your local machine or EC2 instance. The "Unauthorized" error should be resolved.

6. **Verification**

   - update the context
     ```bash
      aws eks update-kubeconfig --name voting-cluster-mayank --region us-east-1
     ```
   -  Lets check it worked or not
       ```bash
        kubectl get nodes
       ```

    ![accescluster](https://github.com/mayaworld13/eks-votingapp/assets/127987256/94456159-c51a-49f5-ba24-9b4fb4e071ce)


## Step 8: Troubleshoot Access Error from EC2

If you face an "Unauthorized" error accessing your EKS cluster from EC2, follow these steps to resolve it:

![IAM Access Policy](https://github.com/mayaworld13/eks-votingapp/assets/127987256/3d5dcfbe-9b0a-4d43-8dc5-c1d857ef776f)   

1. **Review IAM Access Policy**

   - Check the IAM access policy attached to your EC2 instance in the IAM Management Console, update the `kubeconfig` file with the following role:

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
     ```

   - Edit the `configmap` with the IAM role ARN accessible from your local machine:

     ```bash
     kubectl edit configmap aws-auth --namespace kube-system
     ```

   ![Edit ConfigMap](https://github.com/mayaworld13/eks-votingapp/assets/127987256/e57091e4-7398-4bbe-8b56-8ff668a929e0)

   - Save and verify access to the cluster from EC2.

   ![Verify Access](https://github.com/mayaworld13/eks-votingapp/assets/127987256/1081bf47-dc5f-4dd1-91e9-2f5aa90ce241)

Following these steps should resolve the "Unauthorized" error and enable access to your EKS cluster from EC2 using the configured IAM access policy.


## Step 9: Deploy the Application

**clone the Repo**
```
git clonehttps://github.com/mayaworld13/eks-votingapp.git
```

**Create CloudChamp Namespace**
```
kubectl create ns mayaworld

kubectl config set-context --current --namespace mayaworld
```

**MONGO Database Setup**


To create Mongo statefulset with Persistent volumes, run the command in manifests folder:
```
kubectl apply -f mongo-statefulset.yaml
```

Mongo Service
```
kubectl apply -f mongo-service.yaml
```

Create a temporary network utils pod. Enter into a bash session within it. In the terminal run the following command:
```
kubectl run --rm utils -it --image praqma/network-multitool -- bash
```
Within the new utils pod shell, execute the following DNS queries:
```
for i in {0..2}; do nslookup mongo-$i.mongo; done
```
Note: This confirms that the DNS records have been created successfully and can be resolved within the cluster, 1 per MongoDB pod that exists behind the Headless Service - earlier created. 

Exit the utils container
```
exit
```

On the `mongo-0` pod, initialise the Mongo database Replica set. In the terminal run the following command:
```
cat << EOF | kubectl exec -it mongo-0 -- mongo
rs.initiate();
sleep(2000);
rs.add("mongo-1.mongo:27017");
sleep(2000);
rs.add("mongo-2.mongo:27017");
sleep(2000);
cfg = rs.conf();
cfg.members[0].host = "mongo-0.mongo:27017";
rs.reconfig(cfg, {force: true});
sleep(5000);
EOF
```

Note: Wait until this command completes successfully, it typically takes 10-15 seconds to finish, and completes with the message: bye


To confirm run this in the terminal:
```
kubectl exec -it mongo-0 -- mongo --eval "rs.status()" | grep "PRIMARY\|SECONDARY"
```

Load the Data in the database by running this command:
## Note: use langdb not langdb() as shown in the video
```
cat << EOF | kubectl exec -it mongo-0 -- mongo
use langdb;
db.languages.insert({"name" : "csharp", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 5, "compiled" : false, "homepage" : "https://dotnet.microsoft.com/learn/csharp", "download" : "https://dotnet.microsoft.com/download/", "votes" : 0}});
db.languages.insert({"name" : "python", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 3, "script" : false, "homepage" : "https://www.python.org/", "download" : "https://www.python.org/downloads/", "votes" : 0}});
db.languages.insert({"name" : "javascript", "codedetail" : { "usecase" : "web, client-side", "rank" : 7, "script" : false, "homepage" : "https://en.wikipedia.org/wiki/JavaScript", "download" : "n/a", "votes" : 0}});
db.languages.insert({"name" : "go", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 12, "compiled" : true, "homepage" : "https://golang.org", "download" : "https://golang.org/dl/", "votes" : 0}});
db.languages.insert({"name" : "java", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 1, "compiled" : true, "homepage" : "https://www.java.com/en/", "download" : "https://www.java.com/en/download/", "votes" : 0}});
db.languages.insert({"name" : "nodejs", "codedetail" : { "usecase" : "system, web, server-side", "rank" : 20, "script" : false, "homepage" : "https://nodejs.org/en/", "download" : "https://nodejs.org/en/download/", "votes" : 0}});

db.languages.find().pretty();
EOF
```

Create Mongo secret:
```
kubectl apply -f mongo-secret.yaml
```

**API Setup**

Create GO API deployment by running the following command:
```
kubectl apply -f api-deployment.yaml
```

Expose API deployment through service using the following command:
```
kubectl expose deploy api \
 --name=api \
 --type=LoadBalancer \
 --port=80 \
 --target-port=8080
```

Next set the environment variable:

```
{
API_ELB_PUBLIC_FQDN=$(kubectl get svc api -ojsonpath="{.status.loadBalancer.ingress[0].hostname}")
until nslookup $API_ELB_PUBLIC_FQDN >/dev/null 2>&1; do sleep 2 && echo waiting for DNS to propagate...; done
curl $API_ELB_PUBLIC_FQDN/ok
echo
}
```

Test and confirm that the API route URL /languages, and /languages/{name} endpoints can be called successfully. In the terminal run any of the following commands:
```
curl -s $API_ELB_PUBLIC_FQDN/languages | jq .
curl -s $API_ELB_PUBLIC_FQDN/languages/go | jq .
curl -s $API_ELB_PUBLIC_FQDN/languages/java | jq .
curl -s $API_ELB_PUBLIC_FQDN/languages/nodejs | jq .
```

![image](https://github.com/mayaworld13/eks-votingapp/assets/127987256/2ee23c7f-c23c-4937-b8bf-6920b21ab6ae)

![image](https://github.com/mayaworld13/eks-votingapp/assets/127987256/5790dbe1-c7da-454e-bc3d-96810f871426)


If everything works fine, go ahead with Frontend setup.
```
{
API_ELB_PUBLIC_FQDN=$(kubectl get svc api -ojsonpath="{.status.loadBalancer.ingress[0].hostname}")
echo API_ELB_PUBLIC_FQDN=$API_ELB_PUBLIC_FQDN
}
```

**Frontend setup**

Create the Frontend Deployment resource. In the terminal run the following command:
```
kubectl apply -f frontend-deployment.yaml
```

Create a new Service resource of LoadBalancer type. In the terminal run the following command:
```
kubectl expose deploy frontend \
 --name=frontend \
 --type=LoadBalancer \
 --port=80 \
 --target-port=8080
```

Confirm that the Frontend ELB is ready to recieve HTTP traffic. In the terminal run the following command:
```
{
FRONTEND_ELB_PUBLIC_FQDN=$(kubectl get svc frontend -ojsonpath="{.status.loadBalancer.ingress[0].hostname}")
until nslookup $FRONTEND_ELB_PUBLIC_FQDN >/dev/null 2>&1; do sleep 2 && echo waiting for DNS to propagate...; done
curl -I $FRONTEND_ELB_PUBLIC_FQDN
}
```

Generate the Frontend URL for browsing. In the terminal run the following command:
```
echo http://$FRONTEND_ELB_PUBLIC_FQDN
```

Test the full end-to-end cloud native application in case if your application not work then possible update the api load balancer dns in `frontend-deployment.yml` file or check logs

Using your local workstation's browser - browse to the URL created in the previous output.

![image](https://github.com/mayaworld13/eks-votingapp/assets/127987256/31b3396f-f491-4def-8fc9-12a39c06cad0)


After the voting application has loaded successfully, vote by clicking on several of the **+1** buttons, this will generate AJAX traffic which will be sent back to the API via the API's assigned ELB.


Query the MongoDB database directly to observe the updated vote data. In the terminal execute the following command:
```
kubectl exec -it mongo-0 -- mongo langdb --eval "db.languages.find().pretty()"
```
![image](https://github.com/mayaworld13/eks-votingapp/assets/127987256/2254eba0-f594-4d6c-8064-9421b5c7290b)

## **Summary**

In this Project, we have learnt how to deploy a cloud native application into EKS. Once deployed and up and running, you used your local workstation's browser to test out the application. You later confirmed that your activity within the application generated data which was captured and recorded successfully within the MongoDB ReplicaSet back end within the cluster.







