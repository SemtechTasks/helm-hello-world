# helm-hello-world
Deploy spring-boot-hello-world using Helm charts.

# Execution steps:
1. Checkout repository:
    ```
    $ git clone https://github.com/SemtechTasks/helm-hello-world.git
    ```

2. Download Helm binary for target machine from: https://github.com/helm/helm/releases

3. Extract the helm package, ensure the helm binary has executable permissions and it's directory added to the PATH variable

4. Set the required context. [We consider Docker-Desktop as default context here.]
    4.1. List available contexts:
    ```
    $ kubectl config get-contexts
    CURRENT NAME             CLUSTER          AUTHINFO         NAMESPACE
            docker-desktop   docker-desktop   docker-desktop
    ```
    4.2. Set `docker-desktop` as current context
    ```
    $ kubectl config use-context docker-desktop
    Switched to context "docker-desktop".
    ```
    4.3. Verify the current context
    ```
    $ kubectl config current-context
    docker-desktop
    ```
    **Note**: The current can also be set for particular `helm` commands using `--kube-context` command flag.

5. Install Helm chart
    ```
    $ pushd spring-boot-hello-world
    $ helm install spring-boot-hello-world .
    $ popd
    ```

6. Verify the deployment with commands:
    ```
    $ kubectl get pods
    NAME                                       READY   STATUS    RESTARTS   AGE
    spring-boot-hello-world-7f8f4d6fcc-t5znd   1/1     Running   0          9m37s

    $ kubectl get services
    NAME                      TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
    kubernetes                ClusterIP   10.96.0.1       <none>        443/TCP          5h27m
    spring-boot-hello-world   NodePort    10.107.248.41   <none>        8080:32504/TCP   9m37s
    ```

7. Test application deployment:

    ```
    $ export NODE_PORT=$(kubectl get -o jsonpath={.spec.ports[0].nodePort} services spring-boot-hello-world)

    $ curl http://localhost:${NODE_PORT}/hello
    Hello, World!
    ```
    **Note**: You could also use the node IP in place of `localhost` in curl command.


# Using Docker Desktop
1. Install Docker Desktop: https://docs.docker.com/desktop/setup/install/windows-install/
2. Enable Kubernetes from settings: https://docs.docker.com/desktop/features/kubernetes/


# Using EKS cluster
## Install tool - `eksctl`
Follow instructions here: https://eksctl.io/installation/#for-windows

## Setup cluster
Run command:
```
$ eksctl create cluster --name springboot-example --nodegroup-name springboot-example-group1 --region us-east-1 --node-type t2.micro --nodes 2 --nodes-min 1 --nodes-max 2
2025-01-01 12:42:39 [ℹ]  eksctl version 0.199.0
2025-01-01 12:42:39 [ℹ]  using region us-east-1
...
2025-01-01 12:56:57 [ℹ]  kubectl command should work with "C:\\Users\\Yashodhan\\.kube\\config", try 'kubectl get nodes'
2025-01-01 12:56:57 [✔]  EKS cluster "springboot-example" in "us-east-1" region is ready
```
This command creates the required resources using the AWS CloudFormation stack, e.g. as stack name `eksctl-springboot-example-cluster`.

**Note**: The command takes around 15mins. Have patience and ensure the network connectivity is stable.

Once setup is complete,

## Delete cluster
Run command:
```
$ eksctl delete cluster --name springboot-example --region us-east-1
```
**Note**: The command takes around 15mins.


# Settng up local self-hosted runner on Windows
In github repo, go to: **_Settings > Actions > Runners_**

Click "**New self-hosted runner**" and follow the instructions.


# Windows Troubleshooting
## Action script cannot be loaded because running scripts is disabled on this system
```
. : File C:\Users\Yashodhan\actions-runner\_work\_temp\d5eb247f-662b-49b5-92fe-487994e3682e.ps1 cannot be loaded
because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
At line:1 char:3
+ . 'C:\Users\Yashodhan\actions-runner\_work\_temp\d5eb247f-662b-49b5-9 ...
+   ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : SecurityError: (:) [], PSSecurityException
    + FullyQualifiedErrorId : UnauthorizedAccess
Error: Process completed with exit code 1.
```
Windows restricts execution of scripts as security mesure.

*  To allow execution of scripts:
    ```
    PS> Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser
    PS> Get-ExecutionPolicy -Scope CurrentUser
    Unrestricted
    ```

*  To restore:
    ```
    PS> Set-ExecutionPolicy -ExecutionPolicy Undefined -Scope CurrentUser
    PS> Get-ExecutionPolicy -Scope CurrentUser
    Undefined
    ```
