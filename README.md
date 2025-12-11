**Updated Architecture Diagram**

![alt text](<full stack.png>)

**Brief Application Explanation**

This application is a cloud-native, microservice-based retail system inspired by Best Buy. It is designed to demonstrate modern application architecture using multiple technologies, containerization, Kubernetes orchestration, and an automated CI/CD delivery pipeline.

The system is composed of five independent microservices:

  * **store-front (Vue.js)**: A customer-facing web application where users can browse products, view details, add items to the cart, and place orders.

  * **store-admin (Vue.js)**: An internal admin dashboard used by employees to review product details, track orders, and manage store operations.

  * **product-service (Rust)**: Provides the product catalog by exposing product details such as name, description, price, and image paths.

  * **order-service (Node.js)**: Handles order creation and publishes order messages into a RabbitMQ queue.

  * **makeline-service (Go)**: Consumes order messages from RabbitMQ and processes them, eventually storing the order data in MongoDB.

All services are containerized and deployed onto Azure Kubernetes Service (AKS).
StatefulSets are used for MongoDB to ensure data persistence, while ConfigMaps and Secrets are used for configuration management across environments.

A full CI/CD pipeline is implemented using GitHub Actions for each microservice. Every push triggers automated building, testing, container image publishing, and deployment to AKS, ensuring consistent and reliable delivery.

Overall, this project demonstrates how a real-world retail system can be built using microservices, asynchronous messaging, modern frontend frameworks, containerization, and DevOps practices.

**Deployment Instructions**

This section explains how to deploy the full microservices application to Azure Kubernetes Service (AKS). Each service is containerized, stored on Docker Hub, and deployed using Kubernetes manifests.

**1.Prerequisites**

 Before deploying, ensure the following tools are installed:

 * Azure CLI

 * kubectl

 * Docker

 * Git

 * Node.js / Rust / Go (only for local builds)

 * An Azure Subscription

 * A Docker Hub account for pushing service images

 **2. Create Azure Resources**

  * 2.1 Create a Resource Group

  * 2.2 Create an AKS Cluster

  * 2.3 Connect kubectl to the cluster

 **3. Build & Push Docker Images**

 Navigate to each service folder and build/push the Docker images.

 Example:

   * docker build -t arpitk3110/product-service-l8:v1 .
   * docker push arpitk3110/product-service-l8:v1

### Repeat for:

 * store-front

 * store-admin

 * product-service

 * order-service

 * makeline-service

Ensure the image names in your Kubernetes YAML match the pushed images.


**4. Apply Kubernetes Manifests**

 All YAML files are located inside: 

* FinalProject_Deployment/

Deploy everything using:

 * kubectl apply -f bestbuy-all-in-one.yaml

**This will create:**

 * Deployments for all 5 microservices

 * Services for internal/external access

 * StatefulSet for MongoDB

 * ConfigMap and Secret resources

 * RabbitMQ queue

 **5. Verify Deployments**

 Check running pods:
  
 * kubectl get pods

Check created services: 

 * kubectl get svc

You should see two services exposed via LoadBalancer:

 * store-front → Customer UI

 * store-admin → Admin UI

Copy the External IP to open the apps in your browser.


**6. Test the Application**

 * 6.1 Test the Front Store 

 Verify:

  * Products load from product-service

  * Product images load

  * Orders can be placed 

6.2 Test the Admin Dashboard


Verify:

 * Product list loads

 * Order list updates

 * Product details are visible
   

**7. CI/CD Pipeline Execution**

Each microservice repository includes a GitHub Actions workflow (ci_cd.yaml) that performs:

 * Automatic build

 * Container image push to Docker Hub

 * Deployment to AKS

To trigger the pipeline:

  * Push any commit to the repo
  * GitHub Actions will run automatically
  * Changes will deploy to your AKS cluster

**8. Updating a Service**

To update a microservice (example: product-service):

 * Edit code

 * Build a new image

 * Push to Docker Hub

 * Update image tag in Kubernetes YAML

 * Apply YAML again