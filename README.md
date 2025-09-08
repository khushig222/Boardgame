# BoardgameListingWebApp

## Description

**Board Game Database Full-Stack Web Application.**
This web application displays lists of board games and their reviews. While anyone can view the board game lists and reviews, they are required to log in to add/ edit the board games and their reviews. The 'users' have the authority to add board games to the list and add reviews, and the 'managers' have the authority to edit/ delete the reviews on top of the authorities of users.  

## Technologies

- Java
- Spring Boot
- Amazon Web Services(AWS) EC2
- Thymeleaf
- Thymeleaf Fragments
- HTML5
- CSS
- JavaScript
- Spring MVC
- JDBC
- H2 Database Engine (In-memory)
- JUnit test framework
- Spring Security
- Twitter Bootstrap
- Maven

## What this project does

This is a complete DevOps setup that takes a Java application from source code to production deployment on AWS EKS. The pipeline handles everything - code analysis, building, testing, security scanning, and deployment.

## Components we're using

- EKS Cluster - Where our Java app runs
- Jenkins - Handles our CI/CD pipeline 
- ECR - Stores our Docker images
- Nexus - Stores our JAR files (runs on port 8081)
- SonarQube - Checks code quality (runs on port 9000)
- Trivy - Scans Docker images for vulnerabilities
- Prometheus & Grafana - Monitoring (ports 9090 and 3000)

## How the pipeline works

### Jenkins CI Pipeline

1. Clone the repo - Gets the latest code
2. SonarQube analysis - Scans code for vulnerabilities and duplicate lines. You can see results on the SonarQube UI at port 9000
3. Quality Gate - Only proceeds if code quality checks pass
4. Build package - Runs `mvn clean package` to build the JAR
5. Deploy to Nexus - Stores artifacts in one of two repos:
   - If version in pom.xml is `7.0.0-SNAPSHOT` → goes to maven-snapshot repo
   - If version in pom.xml is `7.0.0` → goes to maven-release repo
   - Nexus runs on port 8081
6. Install Docker - Sets up Docker on the build agent
7. Build Docker image - Creates container with our app
8. Trivy scan - Scans the image for security vulnerabilities
9. Push to ECR - Uploads image to AWS container registry

### EKS Deployment

What we set up first:
- Created EKS Cluster with node groups
- Connected EC2 instance to the cluster so we can access it
- Set up Cluster IAM Role and Node IAM Role
- Added access point so EC2 can talk to the cluster

**Deployment steps:**
1. Check connection works: `kubectl get nodes`
2. Clone the repo on the instance
3. Run `kubectl apply -f deployment-service.yml`
4. This creates pods, deployment, and service
5. Service type is LoadBalancer so you can access the app through the AWS load balancer

## Monitoring setup

We use Prometheus and Grafana to monitor both Jenkins and the EKS cluster:
- Prometheus runs on port 9090
- Grafana runs on port 3000

## Network configuration

**Ports we need open:**
- Jenkins:  Allow HTTP/HTTPS (8080/443)
- SonarQube: Allow HTTP (9000) 
- Nexus:  Allow HTTP/HTTPS (8081/443)
- Prometheus: Allow Prometheus UI (9090)
- Grafana: Allow Grafana UI (3000)
- Application: Allow inbound traffic from Load Balancer (80/443)

## IAM stuff

Jenkins EC2 needs:
- ECR permissions to push/pull images
- EKS access to deploy stuff

EKS cluster and nodes need:
- Basic EKS permissions
- ECR access to pull images

## Repository logic

The Nexus setup is pretty straightforward:
- Snapshot versions (like 7.0.0-SNAPSHOT) go to the snapshot repo
- Release versions (like 7.0.0) go to the release repo
- Just change the version in your pom.xml and it automatically knows where to put it

## Quality checks

SonarQube runs analysis on every build and the quality gate stops bad code from getting deployed. Trivy does the same thing but for container security - it'll catch vulnerabilities in your Docker image before it gets to production.

## Monitoring what's happening

Prometheus collects metrics from Jenkins and the EKS cluster. Grafana gives you dashboards to see:
- How Jenkins is performing
- EKS cluster health
- Application metrics
- System resource usage

## If something breaks

Can't connect to EKS:
```bash
kubectl get nodes
aws eks update-kubeconfig --region your-region --name your-cluster
```

Pipeline failing:
- Check if SonarQube is running (port 9000)
- Make sure Nexus credentials are right
- Verify ECR authentication

Monitoring not working:
- Check if Prometheus can reach its targets
- Make sure security groups allow the monitoring ports

## Quick verification

After deployment:
1. Check the app loads through the load balancer
2. Look at Grafana dashboards for metrics
3. Make sure no critical security issues in the latest scan
4. Verify your JAR ended up in the right Nexus repo

## Features

- Full-Stack Application
- UI components created with Thymeleaf and styled with Twitter Bootstrap
- Authentication and authorization using Spring Security
  - Authentication by allowing the users to authenticate with a username and password
  - Authorization by granting different permissions based on the roles (non-members, users, and managers)
- Different roles (non-members, users, and managers) with varying levels of permissions
  - Non-members only can see the boardgame lists and reviews
  - Users can add board games and write reviews
  - Managers can edit and delete the reviews
- Deployed the application on AWS EC2
- JUnit test framework for unit testing
- Spring MVC best practices to segregate views, controllers, and database packages
- JDBC for database connectivity and interaction
- CRUD (Create, Read, Update, Delete) operations for managing data in the database
- Schema.sql file to customize the schema and input initial data
- Thymeleaf Fragments to reduce redundancy of repeating HTML elements (head, footer, navigation)

## How to Run

1. Clone the repository
2. Open the project in your IDE of choice
3. Run the application
4. To use initial user data, use the following credentials.
  - username: bugs    |     password: bunny (user role)
  - username: daffy   |     password: duck  (manager role)
5. You can also sign-up as a new user and customize your role to play with the application! 😊
