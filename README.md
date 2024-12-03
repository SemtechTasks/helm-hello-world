# helm-hello-world
Deploy spring-boot-hello-world using Helm charts.

# Execution steps:

1. Checkout repository:
    ```
    $ git clone https://github.com/SemtechTasks/helm-hello-world.git
    ```

2. Install Docker Deskup and enable Kubernetes from settings

3. Download Helm binary for target machine from: https://github.com/helm/helm/releases

4. Extract the helm package, ensure the helm binary has executable permissions and it's directory added to the PATH variable

5. Install Helm chart
    ```
    $ pushd spring-boot-hello-world && helm install spring-boot-hello-world . && popd
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

7. Forward port access to the application
    ```
    $ kubectl port-forward service/spring-boot-hello-world 8080:8080
    ```

    **Ref**: More about `kubectl port-forward` [here](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_port-forward/)

    **Note**: This is a blocking call so do not use this with workflows.

8. Test application deployment:

    8.1. If port-forward is running, open another terminal and execute:
    ```
    $ curl http://localhost:8080/hello
    Hello, World!
    ```

    Else

    8.2. In the same terminal execute:
    ```
    $ export NODE_PORT=$(kubectl get --namespace default -o jsonpath=\"{.spec.ports[0].nodePort}\" services spring-boot-hello-world)
    curl http://localhost:${NODE_PORT}/hello
    Hello, World!
    ```

# Settng up local self-hosted runner on Windows

In github repo, goto: Settings > Actions > Runners

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