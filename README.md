# eks-blue-green-terraform

To set up an EKS cluster using Terraform and run an NGINX deployment with blue-green traffic switching, follow these steps. This includes creating the EKS cluster, defining the necessary subnets, security groups, and node groups, and deploying NGINX with a blue-green deployment strategy.

1. Prerequisites for Provision an EKS cluster using terraform. 

    1. Terraform v1.3+ installed locally.
    2. An AWS account
    3. The AWS CLI v2.7.0/v1.24.0 or newer, installed and configured
    4. AWS IAM Authenticator
    5. kubectl v1.24.0 or newer


2. Set Up the Directory Structure: Organize your files for Terraform.
  
   mkdir terraform-eks-bluegreen
   cd terraform-eks-bluegreen

3. Create Terraform Configuration Files:

   create variables.tf file.
   create outputs.tf file.
   create main.tf file. 

4. Initialize and Apply Terraform Configuration:

   terraform init
   terraform plan
   terraform apply

5. Configure kubectl:
   
   aws eks --region us-east-2 update-kubeconfig --name bluegreen-eks-cluster (name of eks cluster)

6. Deploy NGINX with Blue-Green Deployment:

   Create a nginx-deployment.yaml for blue deployment.
   Create a nginx-deployment-green.yaml for green deployment.
   Create a ConfigMap for the blue deployment to serve custom HTML content.
   Create a ConfigMap for the green deployment to serve custom HTML content.

7. Apply the Configurations.
   
   Apply the ConfigMap and deployment configurations using below commands.

   kubectl apply -f nginx-blue-configmap.yaml
   kubectl apply -f nginx-deployment-blue.yaml
   kubectl apply -f nginx-green-configmap.yaml
   kubectl apply -f nginx-deployment-green.yaml
   kubectl apply -f nginx-service.yaml


8. Check the External IP Address
   
   Retrieve the external IP address for your NGINX service:
    
    kubectl get services

Look for the EXTERNAL-IP of the nginx-service.


9. Switch Traffic Between Blue and Green Deployments.
 
    we can switch traffic between blue and green in different ways.

    1. Use the kubectl patch commands to switch traffic between the blue and green deployments.

       To switch traffic to the blue deployment:

          kubectl patch service nginx-service -p '{"spec":{"selector":{"version":"blue"}}}'
      
      To switch traffic to the green deployment:

          kubectl patch service nginx-service -p '{"spec":{"selector":{"version":"green"}}}'

    2.  Initially, configure the service to point to the blue deployment. means we can create single service yaml file and use labels of deployment to switch between blue and green. 

        example: 

         in blue and green deployment yaml file we will add a labels as below under spec: selector:

             version: blue
             version: green
        
         simillarly we can update selector using same labels in service yaml file and apply configuration.

           kubectl apply -f service-blue.yaml
          
         To switch traffic to the green deployment, update the service selector.
           
           kubectl apply -f service-green.yaml


By following these steps, we can set up an EKS cluster using Terraform and deploy an Nginx application with a blue-green deployment strategy, switching traffic between the blue and green deployments as needed.
