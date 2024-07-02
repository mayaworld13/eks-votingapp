
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








