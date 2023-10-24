# Sarthak Application manifest Repository

## High Level Architecture Design
![Alt text](assets/ArgoCD.png)

## Project Folder Structure and Control Flow

- We have leveraged apps-of-apps pattern provided by ArgoCD
- ArgoCD provides a Custom Resource Definition(CRD) called as **Application** which is used to manage microservice application
- We have used helm templating to manage our application deployments

### Breaking Down Folder Structure and its purpose

1. sarthak-app (Parent app managing all Children applications)
   - It follows helm templating approach
   - Inside templates folder we have a file called `apps.yaml` which is responsible for managing all the children application
   - We have environment specific values specified in respective `env-configs` folder for e.g `sit` folder for managing `sit` environment configurations and `pre` folder for managing `pre` environment
   - Sample yaml file after deploying it to `dev` environment 
   ```yaml
    project: default
    source:
    repoURL: 'https://github.com/sarsatis/helm-charts.git'
    path: sarthak-app-of-apps
    targetRevision: main
    helm:
        valueFiles:
        - env-configs/sit/values.yaml
    destination:
    server: 'https://kubernetes.default.svc'
    namespace: mfa
    syncPolicy:
    automated:
        prune: true
        selfHeal: true
    syncOptions:
        - CreateNamespace=true
   ```
   - Once the above template is deployed it finds the repository mentioned in `repoUrl` field and deploys the application manifests under the folder path mentioned in `path` field and its respective overriding values are fed from `valueFiles`
   
2. sarthak-app-of-apps
   - Inside templates folder we have a file `app.yaml` which is responsible for deloying individual microservice applications
   - We have use range operator capability provided by helm to iterate over application list specified under respective `env-configs` folder for e.g `sit` folder for managing `sit` environment configurations and `pre` folder for managing `pre` environment
   - sample yaml for one of the Application deployed e.g `employeemanagement`
    ```yaml
    apiVersion: argoproj.io/v1alpha1
    kind: Application
    metadata:
    finalizers:
        - resources-finalizer.argocd.argoproj.io
    labels:
        app.kubernetes.io/instance: apps-sit
    name: employeemanagement
    namespace: argocd
    spec:
    destination:
        namespace: mfa
        server: 'https://kubernetes.default.svc'
    project: default
    source:
        helm:
        valueFiles:
            - /manifests/employeemanagement/sit/immutable/values.yaml
            - /manifests/employeemanagement/sit/configmap/configmap.yaml
        path: helm-charts
        repoURL: 'https://github.com/sarsatis/helm-charts.git'
        targetRevision: main
    syncPolicy:
        automated:
        prune: true
        selfHeal: true
        syncOptions:
        - CreateNamespace=true
    ```
    - Once the above template is deployed it finds the repository mentioned in `repoUrl` field and deploys the application manifests under the folder path mentioned in `path` field and its respective overriding values are fed from `valuesFiles`
    - Application which needs to be deployed are controlled by `appList` which is maintained in respective environment config folder for e.g `env-configs/sit/values.yaml`
  
        - To deploy the application to cluster add the app name under `appList` in respective env folder
        ```yaml
        appList:
        - employeemanagement
        - librarymanagementsystem
        ```
        - To delete the application from the cluster comment the app name under `appList`
        ```yaml
        appList:
        # - employeemanagement
        - librarymanagementsystem
        ```
3. helm-charts
   - This section is further divided into 4 sections to manage kubernetes resources of respective application
     - api :- Deployment manifests are managed from this folder
     - configuration :- ConfigMap objects are managed
     - istio :- Virtual service components are managed 
     - service :- service object are managed for communication between microservice applications and routing traffic from external sources

4. manifests
   - Environment specific values for microservice application are provided

5. PR Generator
   
   
   
        
   
   



