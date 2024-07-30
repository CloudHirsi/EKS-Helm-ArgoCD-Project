# **Transforming a Go Web App through DevOps**
## Project Overview:
This project aimed to enhance a Go web application from scratch by implementing advanced DevOps practices using tools like Terraform, Docker, Kubernetes, GitHub Actions, Helm, ArgoCD, Prometheus, and Grafana. The project was managed using Agile methodologies within a Jira Agile Scrum framework to ensure efficient delivery and iteration.

Key Objectives:
- Containerize the Application: Securely containerize the Go web app using a multi-stage distroless Dockerfile for better security and performance.
- Automate Infrastructure Provisioning: Use Terraform to set up an EKS cluster and associated AWS resources.
- Streamline Kubernetes Deployment: Create Kubernetes YAML files for deployment, service, and ingress configurations, and set up an NGINX ingress controller.
- Implement CI/CD Pipeline: Develop a comprehensive CI/CD pipeline using GitHub Actions for automated build, test, and deployment processes.
- Leverage Helm for Kubernetes Management: Utilize Helm charts to efficiently manage Kubernetes resources.
- Continuous Delivery with ArgoCD: Implement ArgoCD for automated deployment of Kubernetes resources based on GitOps principles.
- Enable Monitoring and Logging: Integrate Prometheus and Grafana for robust monitoring and visualization.

## Project Structure:
```
.
├── .github
│   └── workflows
│       └── ci-cd.yml
├── go-web-app/
│   ├── main.go
│   └── ...
├── helm/
│   └── go-web-app/
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
├── kubernetes/
│   ├── deployment.yaml
│   └── service.yaml
├── terraform/
│   ├── main.tf
│   ├── variables.tf
│   └── outputs.tf
├── Dockerfile
└── README.md
```

# More Detail...

## Project Management with Agile and Jira
Created a Jira Agile Scrum project to manage tasks and sprints.
Utilized Kanban boards to track progress and ensure transparency.
![image](https://github.com/user-attachments/assets/186e9f25-89cd-4633-9a38-23d3dbf2650d)
![image](https://github.com/user-attachments/assets/1bb7d538-5696-4cc0-8048-7217565a9553)



## Containerization with Docker
- Developed a multi-stage distroless Dockerfile to build the Go web application, ensuring a secure and lightweight container image.
![image](https://github.com/user-attachments/assets/f0fb7695-a9bd-4073-a13a-b286215f48ae)


## Infrastructure Provisioning with Terraform
- Defined and provisioned an EKS cluster along with necessary AWS resources using Terraform, enabling infrastructure as code (IaC) for consistent and repeatable deployments.
![image](https://github.com/user-attachments/assets/0221e3a0-3808-4e68-b1ec-dc2dd9c99d60)


## Kubernetes Deployment
- Crafted Kubernetes YAML files for deploying the Go web application, setting up services, and managing ingress.
- Implemented an NGINX ingress controller and mapped DNS to the load balancer for seamless traffic management.
![image](https://github.com/user-attachments/assets/8caf4f04-aa72-4373-a4ee-86d813b2931c)
![image](https://github.com/user-attachments/assets/24e2bc50-b901-4258-8840-f53d8038e0eb)


## Helm Chart Creation
- Created a Helm chart to template the Kubernetes YAML files, simplifying the management and deployment of Kubernetes resources.
- Using Helm charts facilitated easier updates and version control of Kubernetes configurations.
![image](https://github.com/user-attachments/assets/275474ab-3c74-4600-be5f-e0e41dd9040e)
![image](https://github.com/user-attachments/assets/d7613d0c-af57-4023-bab7-ded0234d6947)


## CI/CD Pipeline with GitHub Actions
- Set up a CI pipeline in GitHub Actions to build and test the application on every push to the main branch, excluding certain paths to optimize performance.
- Configured Docker build and push actions to automate the creation and deployment of Docker images to DockerHub.
- Implemented a task to update Helm chart versions automatically in the repository.
![image](https://github.com/user-attachments/assets/0b8d9021-3059-44af-a9cf-93c1a88568c1)
```
name: CI/CD

on:
  push:
    branches:
      - main
    paths-ignore:
      - 'helm/**'
      - 'kubernetes/**'
      - 'terraform/**'
      - 'README.md'

jobs:

  build:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Set up Go 1.22.5
      uses: actions/setup-go@v2
      with:
        go-version: 1.22.5

    - name: Build
      run: go build -o go-web-app

    - name: Test
      run: go test ./...
  
  code-quality:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Run golangci-lint
      uses: golangci/golangci-lint-action@v6
      with:
        version: v1.56.2
  
  push:
    runs-on: ubuntu-latest

    needs: build

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v1

    - name: Login to DockerHub
      uses: docker/login-action@v3
      with:
        username: ${{ secrets.DOCKERHUB_USERNAME }}
        password: ${{ secrets.DOCKERHUB_TOKEN }}

    - name: Build and Push action
      uses: docker/build-push-action@v6
      with:
        context: .
        file: ./Dockerfile
        push: true
        tags: ${{ secrets.DOCKERHUB_USERNAME }}/go-web-app:${{github.run_id}}

  update-newtag-in-helm-chart:
    runs-on: ubuntu-latest

    needs: push

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4
      with:
        token: ${{ secrets.TOKEN }}

    - name: Update tag in Helm chart
      run: |
        sed -i 's/tag: .*/tag: "${{github.run_id}}"/' helm/go-web-app/values.yaml

    - name: Commit and push changes
      run: |
        git config --global user.email "Cloudhirsi@outlook.com"
        git config --global user.name "CloudHirsi"
        git add helm/go-web-app/values.yaml
        git commit -m "Updated tag in Helm chart"
        git push
```

## Continuous Delivery with ArgoCD
- Configured ArgoCD to monitor the repository and automatically apply changes to the Kubernetes cluster, ensuring consistent and reliable deployments through GitOps practices.
![image](https://github.com/user-attachments/assets/e6e6bd79-2ca7-4a58-a6a1-10a383c256d7)


## Monitoring with Prometheus and Grafana
- Set up Prometheus for collecting metrics from the Kubernetes cluster and application pods.
- Integrated Grafana for real-time visualization and monitoring of application performance and health.
![image](https://github.com/user-attachments/assets/76321fb9-fe67-4fea-b95c-1ff9f33b37cf)
![image](https://github.com/user-attachments/assets/1a1cff41-7a3d-4d1d-a9df-462d6329837f)


## Testing and Validation
- Tested the entire setup by making changes to the Go web application and pushing updates to GitHub.
- Verified that the CI pipeline triggered correctly, the new Docker image was built and pushed to DockerHub, and ArgoCD deployed the updated resources.
- Confirmed that the changes were reflected in the live environment, demonstrating the effectiveness of the CI/CD pipeline and the robustness of the deployment strategy.
![image](https://github.com/user-attachments/assets/684c2a0a-1f65-4be5-898e-89c6ccb8a396)
![image](https://github.com/user-attachments/assets/12dd9bfa-6061-4cfc-bf08-0dd7dac6ad64)



# __Results__
The project successfully demonstrated the implementation of a complete DevOps lifecycle for a Go web application. By leveraging Terraform, Docker, Kubernetes, GitHub Actions, Helm, ArgoCD, Prometheus, and Grafana. 
I was able to achieve:

- Automated and secure infrastructure provisioning with _**Terraform**_.
- Streamlined application deployment and management with _**EKS**_ and _**Helm**_.
- Enhanced monitoring and visualization capabilities with _**Prometheus**_ and _**Grafana**_.
- Consistent and reliable CI/CD processes adhering to Agile methodologies with _**Github Actions**_ and _**ArgoCD**_.

This comprehensive DevOps Transformation project serves as a showcase for my advanced skills and knowledge in managing cloud-native applications and infrastructure, through DevOps best practices, in an Agile environment.





