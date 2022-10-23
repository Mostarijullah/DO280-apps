Day 1 - Practice Mock
-------------------------------

Task 1 :

Perform a smoke test of the cluster to verify basic cluster functionality. Use a deployment
to create an application named hello-world-nginx. The application source code is
located in the hello-world-nginx subdirectory of the repository.

• https://github.com/Mostarijullah/DO280-apps

Create a route for the application, use hello-world.apps.ocp4.example.com as the
hostname. Verify that the application responds to external requests.


<pre>

[student@workstation DO280-apps]$ oc new-app --name hello-world-nginx \
&gt;     https://github.com/Mostarijullah/DO280-apps \
&gt;     --context-dir hello-world-nginx
--&gt; Found container image 11f9dba (3 years old) from registry.access.redhat.com for &quot;registry.access.redhat.com/ubi8:8.0&quot;

    Red Hat Universal Base Image 8 
    ------------------------------ 
    The Universal Base Image is designed and engineered to be the base layer for all of your containerized applications, middleware and utilities. This base image is freely redistributable, but Red Hat only supports Red Hat technologies through subscriptions for Red Hat products. This image is maintained by Red Hat and updated regularly.

    Tags: base rhel8

    * An image stream tag will be created as &quot;ubi8:8.0&quot; that will track the source image
    * A Docker build using source code from https://github.com/Mostarijullah/DO280-apps will be created
      * The resulting image will be pushed to image stream tag &quot;hello-world-nginx:latest&quot;
      * Every time &quot;ubi8:8.0&quot; changes a new build will be triggered

--&gt; Creating resources ...
    imagestream.image.openshift.io &quot;ubi8&quot; created
    imagestream.image.openshift.io &quot;hello-world-nginx&quot; created
    buildconfig.build.openshift.io &quot;hello-world-nginx&quot; created
    deployment.apps &quot;hello-world-nginx&quot; created
    service &quot;hello-world-nginx&quot; created
--&gt; Success
    Build scheduled, use &apos;oc logs -f buildconfig/hello-world-nginx&apos; to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     &apos;oc expose service/hello-world-nginx&apos; 
    Run &apos;oc status&apos; to view your app.
[student@workstation DO280-apps]$ oc status
In project review-troubleshoot on server https://api.ocp4.example.com:6443

svc/hello-world-nginx - 172.30.86.46:8080
  deployment/hello-world-nginx deploys istag/hello-world-nginx:latest &lt;-
    bc/hello-world-nginx docker builds https://github.com/Mostarijullah/DO280-apps on istag/ubi8:8.0 
      not built yet
    deployment #1 running for 13 seconds - 0/1 pods growing to 1


2 warnings, 1 info identified, use &apos;oc status --suggest&apos; to see details.
[student@workstation DO280-apps]$ oc get all
NAME                        TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
service/hello-world-nginx   ClusterIP   172.30.86.46   &lt;none&gt;        8080/TCP   19s

NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello-world-nginx   0/1     0            0           20s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/hello-world-nginx-874b97b6f   1         0         0       20s

NAME                                               TYPE     FROM   LATEST
buildconfig.build.openshift.io/hello-world-nginx   Docker   Git    0

NAME                                               IMAGE REPOSITORY                                                                         TAGS   UPDATED
imagestream.image.openshift.io/hello-world-nginx   image-registry.openshift-image-registry.svc:5000/review-troubleshoot/hello-world-nginx          
imagestream.image.openshift.io/ubi8                image-registry.openshift-image-registry.svc:5000/review-troubleshoot/ubi8                       
[student@workstation DO280-apps]$ oc expose service hello-world-nginx \
&gt;     --hostname hello-world.apps.ocp4.example.com
route.route.openshift.io/hello-world-nginx exposed
[student@workstation DO280-apps]$ oc get pods
NAME                        READY   STATUS    RESTARTS   AGE
hello-world-nginx-1-build   1/1     Running   0          38s
[student@workstation DO280-apps]$ oc get all
NAME                            READY   STATUS    RESTARTS   AGE
pod/hello-world-nginx-1-build   1/1     Running   0          44s

NAME                        TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
service/hello-world-nginx   ClusterIP   172.30.86.46   &lt;none&gt;        8080/TCP   65s

NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello-world-nginx   0/1     0            0           66s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/hello-world-nginx-874b97b6f   1         0         0       66s

NAME                                               TYPE     FROM   LATEST
buildconfig.build.openshift.io/hello-world-nginx   Docker   Git    1

NAME                                           TYPE     FROM          STATUS    STARTED          DURATION
build.build.openshift.io/hello-world-nginx-1   Docker   Git@d0630df   Running   44 seconds ago   

NAME                                               IMAGE REPOSITORY                                                                         TAGS   UPDATED
imagestream.image.openshift.io/hello-world-nginx   image-registry.openshift-image-registry.svc:5000/review-troubleshoot/hello-world-nginx          
imagestream.image.openshift.io/ubi8                image-registry.openshift-image-registry.svc:5000/review-troubleshoot/ubi8                8.0    45 seconds ago

NAME                                         HOST/PORT                           PATH   SERVICES            PORT       TERMINATION   WILDCARD
route.route.openshift.io/hello-world-nginx   hello-world.apps.ocp4.example.com          hello-world-nginx   8080-tcp                 None
[student@workstation DO280-apps]$ curl -s http://hello-world.apps.ocp4.example.com \
&gt;     | grep Hello
[student@workstation DO280-apps]$ curl -s http://hello-world.apps.ocp4.example.com | grep Hello
    &lt;h1&gt;<font color="#EF2929"><b>Hello</b></font>, world from nginx!&lt;/h1&gt;
[student@workstation DO280-apps]$ 
</pre>



Task 2 :

Configure the cluster to use an HTPasswd identity provider. The name of the identity provider is cluster-users. The identity provider reads htpasswd credentials stored in the local-users secret.

Ensure that four user accounts exist: admin, leader, developer, and qa-engineer. All user accounts must use redhat as the password.

Add the cluster-admin role to the admin user.


<pre> 

[student@workstation DO280-apps]$ 

[student@workstation DO280-apps]$ touch /tmp/cluster-users
[student@workstation DO280-apps]$ 
[student@workstation DO280-apps]$ for user in admin leader developer qa-engineer
&gt;     do
&gt;     htpasswd -B -b /tmp/cluster-users ${user} redhat
&gt;     done
Adding password for user admin
Adding password for user leader
Adding password for user developer
Adding password for user qa-engineer
[student@workstation DO280-apps]$ cat /tmp/cluster-users 
admin:$2y$05$1XKA79Hc2J4pRdljCQvHlOsvOUGBmv3RgCSJakAU6TZM3t/6YXWRa
leader:$2y$05$5G4kdTjd/LuyRdZl5L/WOe0wmPwgWnTHxfpkC1IEQdLimy4SLx7Vu
developer:$2y$05$9EB8zQVAO0iuEDcc2qOuBOQnUJ6IdnngGd2H1ZkCUYmTvP3bsE.1q
qa-engineer:$2y$05$jTPpG7UXzf2.b4xFBfyVoeMV/MVYV3ikWc6R7NSvkkcbjw8srsJPO
[student@workstation DO280-apps]$ oc create secret generic local-users \
&gt;     --from-file htpasswd=/tmp/cluster-users -n openshift-config
secret/local-users created
[student@workstation DO280-apps]$ oc get oauth cluster -o yaml &gt; /tmp/oauth.yaml
[student@workstation DO280-apps]$ vi /tmp/oauth.yaml 
[student@workstation DO280-apps]$ oc replace -f /tmp/oauth.yaml 
oauth.config.openshift.io/cluster replaced
[student@workstation DO280-apps]$ oc adm policy add-cluster-role-to-user \
&gt;     cluster-admin admin
Warning: User &apos;admin&apos; not found
clusterrole.rbac.authorization.k8s.io/cluster-admin added: &quot;admin&quot;
[student@workstation DO280-apps]$ oc login -u admin -p redhat
Login successful.

You have access to 60 projects, the list has been suppressed. You can list all projects with &apos; projects&apos;

Using project &quot;review-troubleshoot&quot;.
[student@workstation DO280-apps]$ oc get nd
error: the server doesn&apos;t have a resource type &quot;nd&quot;
[student@workstation DO280-apps]$ oc get ns
NAME                                               STATUS   AGE
default                                            Active   467d
kube-node-lease                                    Active   467d
kube-public                                        Active   467d
kube-system                                        Active   467d
nfs-client-provisioner                             Active   467d
openshift                                          Active   467d
openshift-apiserver                                Active   467d
openshift-apiserver-operator                       Active   467d
openshift-authentication                           Active   467d
openshift-authentication-operator                  Active   467d
openshift-cloud-credential-operator                Active   467d
openshift-cluster-csi-drivers                      Active   467d
openshift-cluster-machine-approver                 Active   467d
openshift-cluster-node-tuning-operator             Active   467d
openshift-cluster-samples-operator                 Active   467d
openshift-cluster-storage-operator                 Active   467d
openshift-cluster-version                          Active   467d
openshift-config                                   Active   467d
openshift-config-managed                           Active   467d
openshift-config-operator                          Active   467d
openshift-console                                  Active   467d
openshift-console-operator                         Active   467d
openshift-controller-manager                       Active   467d
openshift-controller-manager-operator              Active   467d
openshift-dns                                      Active   467d
openshift-dns-operator                             Active   467d
openshift-etcd                                     Active   467d
openshift-etcd-operator                            Active   467d
openshift-image-registry                           Active   467d
openshift-infra                                    Active   467d
openshift-ingress                                  Active   467d
openshift-ingress-operator                         Active   467d
openshift-insights                                 Active   467d
openshift-kni-infra                                Active   467d
openshift-kube-apiserver                           Active   467d
openshift-kube-apiserver-operator                  Active   467d
openshift-kube-controller-manager                  Active   467d
openshift-kube-controller-manager-operator         Active   467d
openshift-kube-scheduler                           Active   467d
openshift-kube-scheduler-operator                  Active   467d
openshift-kube-storage-version-migrator            Active   467d
openshift-kube-storage-version-migrator-operator   Active   467d
openshift-machine-api                              Active   467d
openshift-machine-config-operator                  Active   467d
openshift-marketplace                              Active   467d
openshift-monitoring                               Active   467d
openshift-multus                                   Active   467d
openshift-network-operator                         Active   467d
openshift-node                                     Active   467d
openshift-oauth-apiserver                          Active   467d
openshift-openstack-infra                          Active   467d
openshift-operator-lifecycle-manager               Active   467d
openshift-operators                                Active   467d
openshift-ovirt-infra                              Active   467d
openshift-sdn                                      Active   467d
openshift-service-ca                               Active   467d
openshift-service-ca-operator                      Active   467d
openshift-user-workload-monitoring                 Active   467d
openshift-vsphere-infra                            Active   467d
review-troubleshoot                                Active   25m
[student@workstation DO280-apps]$ oc get nodes
NAME       STATUS   ROLES           AGE    VERSION
master01   Ready    master,worker   467d   v1.19.0+b00ba52
master02   Ready    master,worker   467d   v1.19.0+b00ba52
master03   Ready    master,worker   467d   v1.19.0+b00ba52
[student@workstation DO280-apps]$ 
</pre>

oauth file -

spec:
  identityProviders:
  - name: cluster-users
    mappingMethod: claim
    type: HTPasswd
    htpasswd: 
      fileData: 
        name: local-users


<pre>spec:
  identityProviders:
  - name: cluster-users
    mappingMethod: claim
    type: HTPasswd
    htpasswd: 
      fileData: 
        name: local-users

</pre>



Task 3 :

As the admin user, create three user groups: leaders, developers, and qa.

Assign the leader user to the leaders group, the developer user to the developers group, and the qa-engineer user to the qa group.

Assign roles to each group:

Assign the self-provisioner role to the leaders group, which allows members to create projects. For this role to be effective, you must also remove the ability of any authenticated user to create new projects.

Assign the edit role to the developers group for the review-troubleshoot project only, which allows members to create and delete project resources.

Assign the view role to the qa group for the review-troubleshoot project only, which provides members with read access to project resources.

<pre>

[student@workstation DO280-apps]$ 
[student@workstation DO280-apps]$ 
[student@workstation DO280-apps]$ oc whoami --show-console
https://console-openshift-console.apps.ocp4.example.com
[student@workstation DO280-apps]$ oc whoami
admin
[student@workstation DO280-apps]$ oc project
Using project &quot;review-troubleshoot&quot; on server &quot;https://api.ocp4.example.com:6443&quot;.
[student@workstation DO280-apps]$ for group in leaders developers qa
&gt;  do
&gt;  oc adm groups new ${group}
&gt;  done
group.user.openshift.io/leaders created
group.user.openshift.io/developers created
group.user.openshift.io/qa created
[student@workstation DO280-apps]$ oc adm groups add-users leaders leader
group.user.openshift.io/leaders added: &quot;leader&quot;
[student@workstation DO280-apps]$ oc adm groups add-users developers developer
group.user.openshift.io/developers added: &quot;developer&quot;
[student@workstation DO280-apps]$ oc adm groups add-users qa qa-engineer
group.user.openshift.io/qa added: &quot;qa-engineer&quot;
[student@workstation DO280-apps]$ oc adm policy add-cluster-role-to-group \
&gt;     self-provisioner leaders
clusterrole.rbac.authorization.k8s.io/self-provisioner added: &quot;leaders&quot;
[student@workstation DO280-apps]$ oc adm policy remove-cluster-role-from-group \
&gt;     self-provisioner system:authenticated:oauth
Warning: Your changes may get lost whenever a master is restarted, unless you prevent reconciliation of this rolebinding using the following command: oc annotate clusterrolebinding.rbac self-provisioners &apos;rbac.authorization.kubernetes.io/autoupdate=false&apos; --overwrite
clusterrole.rbac.authorization.k8s.io/self-provisioner removed: &quot;system:authenticated:oauth&quot;
[student@workstation DO280-apps]$ oc policy add-role-to-group edit developers
clusterrole.rbac.authorization.k8s.io/edit added: &quot;developers&quot;
[student@workstation DO280-apps]$ oc policy add-role-to-group view qa
clusterrole.rbac.authorization.k8s.io/view added: &quot;qa&quot;
[student@workstation DO280-apps]$ oc get users
NAME    UID                                    FULL NAME   IDENTITIES
admin   01af9941-ce61-439a-aa68-d15061a6bdb1               cluster-users:admin
[student@workstation DO280-apps]$ oc get groups
NAME         USERS
developers   developer
leaders      leader
qa           qa-engineer
[student@workstation DO280-apps]$ oc get identities
NAME                  IDP NAME        IDP USER NAME   USER NAME   USER UID
cluster-users:admin   cluster-users   admin           admin       01af9941-ce61-439a-aa68-d15061a6bdb1
[student@workstation DO280-apps]$ oc login -u developer -p redhat
Login successful.

You have one project on this server: &quot;review-troubleshoot&quot;

Using project &quot;review-troubleshoot&quot;.
[student@workstation DO280-apps]$ oc login -u qa-engineer -p redhat
Login successful.

You have one project on this server: &quot;review-troubleshoot&quot;

Using project &quot;review-troubleshoot&quot;.
[student@workstation DO280-apps]$ oc login -u leader -p redhat
Login successful.

You don&apos;t have any projects. You can try to create a new project, by running

    oc new-project &lt;projectname&gt;

[student@workstation DO280-apps]$ oc new-project test
Now using project &quot;test&quot; on server &quot;https://api.ocp4.example.com:6443&quot;.

You can add applications to this project with the &apos;new-app&apos; command. For example, try:

    oc new-app rails-postgresql-example

to build a new example application in Ruby. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=k8s.gcr.io/serve_hostname

[student@workstation DO280-apps]$ oc delete project test 
project.project.openshift.io &quot;test&quot; deleted
[student@workstation DO280-apps]$ oc login -u qa-engineer -p redhat
Login successful.

You have one project on this server: &quot;review-troubleshoot&quot;

Using project &quot;review-troubleshoot&quot;.
[student@workstation DO280-apps]$ oc new-project test-qa
Error from server (Forbidden): You may not request a new project via this API.
[student@workstation DO280-apps]$ oc login -u developer -p redhat
Login successful.

You have one project on this server: &quot;review-troubleshoot&quot;

Using project &quot;review-troubleshoot&quot;.
[student@workstation DO280-apps]$ oc new-project test-qa
Error from server (Forbidden): You may not request a new project via this API.
[student@workstation DO280-apps]$ 
</pre>


Task 4 :


As the developer user, use a deployment to create an application named mysql in the review-troubleshoot project. Use the image available at registry.redhat.io/rhel8/mysql-80:1-139. This application provides a shared database service for other project applications.

Create a generic secret named mysql using password as the key and redhat as the value.

Set the MYSQL_ROOT_ environment variables from the values in the mysql secret.

Configure the mysql database application to mount a persistent volume claim (PVC) to the /var/lib/mysql/data directory within the pod. The PVC must be 2 GB in size and must only request the ReadWriteOnce access mode.



<pre>[student@workstation DO280-apps]$ 
[student@workstation DO280-apps]$ 
[student@workstation DO280-apps]$ 
[student@workstation DO280-apps]$ 
[student@workstation DO280-apps]$ oc whoami
developer
[student@workstation DO280-apps]$ oc new-app --name mysql \
&gt;    --docker-image registry.redhat.io/rhel8/mysql-80:1-139
--&gt; Found container image a2a7718 (16 months old) from registry.redhat.io for &quot;registry.redhat.io/rhel8/mysql-80:1-139&quot;

    MySQL 8.0 
    --------- 
    MySQL is a multi-user, multi-threaded SQL database server. The container image provides a containerized packaging of the MySQL mysqld daemon and client application. The mysqld server daemon accepts connections from clients and provides access to content from MySQL databases on behalf of the clients.

    Tags: database, mysql, mysql80, mysql-80

    * An image stream tag will be created as &quot;mysql:1-139&quot; that will track this image

--&gt; Creating resources ...
    imagestream.image.openshift.io &quot;mysql&quot; created
    deployment.apps &quot;mysql&quot; created
    service &quot;mysql&quot; created
--&gt; Success
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     &apos;oc expose service/mysql&apos; 
    Run &apos;oc status&apos; to view your app.
[student@workstation DO280-apps]$ oc status
In project review-troubleshoot on server https://api.ocp4.example.com:6443

http://hello-world.apps.ocp4.example.com to pod port 8080-tcp (svc/hello-world-nginx)
  deployment/hello-world-nginx deploys istag/hello-world-nginx:latest &lt;-
    bc/hello-world-nginx docker builds https://github.com/Mostarijullah/DO280-apps on istag/ubi8:8.0 
    deployment #2 running for 44 minutes - 1 pod
    deployment #1 deployed 46 minutes ago

svc/mysql - 172.30.236.110:3306
  deployment/mysql deploys istag/mysql:1-139 
    deployment #2 running for 10 seconds - 0/1 pods
    deployment #1 deployed 12 seconds ago - 0/1 pods growing to 1


2 infos identified, use &apos;oc status --suggest&apos; to see details.
[student@workstation DO280-apps]$ oc get pods
NAME                                READY   STATUS             RESTARTS   AGE
hello-world-nginx-1-build           0/1     Completed          0          45m
hello-world-nginx-d58b5fc8b-csv6n   1/1     Running            0          44m
mysql-64d9f4d857-4vb9p              0/1     CrashLoopBackOff   1          18s
[student@workstation DO280-apps]$  oc create secret generic mysql \
&gt; --from-literal password=redhat
secret/mysql created
[student@workstation DO280-apps]$ oc set env deployment mysql \
&gt;     --prefix MYSQL_ROOT_ --from secret/mysql
deployment.apps/mysql updated
[student@workstation DO280-apps]$ oc get pods -l deployment=mysql
NAME                    READY   STATUS    RESTARTS   AGE
mysql-fbf67ff96-q8pzj   1/1     Running   0          18s
[student@workstation DO280-apps]$ oc set volumes deployment/mysql --name mysql-storage \
&gt;    --add --type pvc --claim-size 2Gi --claim-mode rwo \
&gt;    --mount-path /var/lib/mysql/data
deployment.apps/mysql volume updated
[student@workstation DO280-apps]$ oc get pods -l deployment=mysql
NAME                     READY   STATUS    RESTARTS   AGE
mysql-66c6b4976d-dbbwk   1/1     Running   0          12s
[student@workstation DO280-apps]$ oc describe pod mysql-66c6b4976d-dbbwk 
Name:         mysql-66c6b4976d-dbbwk
Namespace:    review-troubleshoot
Priority:     0
Node:         master01/192.168.50.10
Start Time:   Sun, 23 Oct 2022 10:04:08 -0400
Labels:       deployment=mysql
              pod-template-hash=66c6b4976d
Annotations:  k8s.v1.cni.cncf.io/network-status:
                [{
                    &quot;name&quot;: &quot;&quot;,
                    &quot;interface&quot;: &quot;eth0&quot;,
                    &quot;ips&quot;: [
                        &quot;10.8.0.20&quot;
                    ],
                    &quot;default&quot;: true,
                    &quot;dns&quot;: {}
                }]
              k8s.v1.cni.cncf.io/networks-status:
                [{
                    &quot;name&quot;: &quot;&quot;,
                    &quot;interface&quot;: &quot;eth0&quot;,
                    &quot;ips&quot;: [
                        &quot;10.8.0.20&quot;
                    ],
                    &quot;default&quot;: true,
                    &quot;dns&quot;: {}
                }]
              openshift.io/generated-by: OpenShiftNewApp
              openshift.io/scc: restricted
Status:       Running
IP:           10.8.0.20
IPs:
  IP:           10.8.0.20
Controlled By:  ReplicaSet/mysql-66c6b4976d
Containers:
  mysql:
    Container ID:   cri-o://1ecc1117da5fb3d787914934ec35bfd8b2d69b0fc6c0f02d3c57face77a3fc5f
    Image:          registry.redhat.io/rhel8/mysql-80@sha256:1c65c90a5ffc40d0454d27e6c8a13b7a746a08ea151ed24edce96c61aeb28801
    Image ID:       registry.redhat.io/rhel8/mysql-80@sha256:1c65c90a5ffc40d0454d27e6c8a13b7a746a08ea151ed24edce96c61aeb28801
    Port:           3306/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sun, 23 Oct 2022 10:04:10 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      MYSQL_ROOT_PASSWORD:  &lt;set to the key &apos;password&apos; in secret &apos;mysql&apos;&gt;  Optional: false
    Mounts:
      /var/lib/mysql/data from mysql-storage (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-g99n5 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  mysql-storage:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  pvc-qwgrj
    ReadOnly:   false
  default-token-g99n5:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-g99n5
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  &lt;none&gt;
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason            Age   From               Message
  ----     ------            ----  ----               -------
  Warning  FailedScheduling  30s   default-scheduler  0/3 nodes are available: 3 pod has unbound immediate PersistentVolumeClaims.
  Warning  FailedScheduling  30s   default-scheduler  0/3 nodes are available: 3 pod has unbound immediate PersistentVolumeClaims.
  Normal   Scheduled         28s   default-scheduler  Successfully assigned review-troubleshoot/mysql-66c6b4976d-dbbwk to master01
  Normal   AddedInterface    26s   multus             Add eth0 [10.8.0.20/23]
  Normal   Pulled            26s   kubelet            Container image &quot;registry.redhat.io/rhel8/mysql-80@sha256:1c65c90a5ffc40d0454d27e6c8a13b7a746a08ea151ed24edce96c61aeb28801&quot; already present on machine
  Normal   Created           26s   kubelet            Created container mysql
  Normal   Started           26s   kubelet            Started container mysql
[student@workstation DO280-apps]$ oc get pv
Error from server (Forbidden): persistentvolumes is forbidden: User &quot;developer&quot; cannot list resource &quot;persistentvolumes&quot; in API group &quot;&quot; at the cluster scope
[student@workstation DO280-apps]$ oc get pvc
NAME        STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc-qwgrj   Bound    pvc-f1fb064f-6d75-4d8a-813a-fe9f1797b9a3   2Gi        RWO            nfs-storage    93s
[student@workstation DO280-apps]$ oc exec mysql-66c6b4976d-dbbwk -- \
&gt;    df -h /var/lib/mysql/data
Filesystem                                                                                           Size  Used Avail Use% Mounted on
192.168.50.254:/exports-ocp4/review-troubleshoot-pvc-qwgrj-pvc-f1fb064f-6d75-4d8a-813a-fe9f1797b9a3   40G  931M   40G   3% /var/lib/mysql/data
[student@workstation DO280-apps]$ 
</pre>


Task 5 :

As the developer user, use a deployment to create an application named wordpress. Create the application in the review-troubleshoot project. Use the image available at quay.io/redhattraining/wordpress:5.7-php7.4-apache.

The Wordpress application requires that you set several environment variables. The required environment variables are: WORDPRESS_DB_HOST with a value of mysql, WORDPRESS_DB_NAME to have a value of wordpress, WORDPRESS_USER with a value of wpuser, WORDPRESS_PASSWORD with a value of wppass, WORDPRESS_TITLE to have a value of review-troubleshoot, WORDPRESS_URL with a value of wordpress.${RHT_OCP4_WILDCARD_DOMAIN} and WORDPRESS_EMAIL with a value of student@redhat.com.

Set the WORDPRESS_DB_* environment variables to retrieve their values from the mysql secret.

The wordpress application requires the anyuid security context constraint. Create a service account named wordpress-sa, and then assign the anyuid security context constraint to it. Configure the wordpress deployment to use the wordpress-sa service account.

The wordpress application also requires that the database WORDPRESS_DB_NAME exists on the database server. Create an empty database named wordpress.

Create a route for the application using any available hostname in the apps.ocp4.example.com subdomain. If you correctly deploy the application, then an installation wizard displays when you access the application from a browser.


<pre>

[student@workstation DO280-apps]$ oc new-app --name wordpress \
&gt;     --docker-image quay.io/redhattraining/wordpress:5.7-php7.4-apache \
&gt;     -e WORDPRESS_DB_HOST=mysql -e WORDPRESS_DB_NAME=wordpress \
&gt;     -e WORDPRESS_DB_USER=root \
&gt;     -e WORDPRESS_USER=wpuser -e WORDPRESS_PASSWORD=wppass \
&gt;     -e WORDPRESS_TITLE=review-troubleshoot \
&gt;     -e WORDPRESS_URL=wordpress.${RHT_OCP4_WILDCARD_DOMAIN} \
&gt;     -e WORDPRESS_EMAIL=student@redhat.com
--&gt; Found container image 4d3c70b (15 months old) from quay.io for &quot;quay.io/redhattraining/wordpress:5.7-php7.4-apache&quot;

    * An image stream tag will be created as &quot;wordpress:5.7-php7.4-apache&quot; that will track this image

--&gt; Creating resources ...
    imagestream.image.openshift.io &quot;wordpress&quot; created
    deployment.apps &quot;wordpress&quot; created
    service &quot;wordpress&quot; created
--&gt; Success
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     &apos;oc expose service/wordpress&apos; 
    Run &apos;oc status&apos; to view your app.
[student@workstation DO280-apps]$ oc get all
NAME                                    READY   STATUS              RESTARTS   AGE
pod/hello-world-nginx-1-build           0/1     Completed           0          58m
pod/hello-world-nginx-d58b5fc8b-csv6n   1/1     Running             0          56m
pod/mysql-66c6b4976d-dbbwk              1/1     Running             0          9m39s
pod/wordpress-d4956dc76-cclgj           0/1     ContainerCreating   0          8s

NAME                        TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/hello-world-nginx   ClusterIP   172.30.86.46     &lt;none&gt;        8080/TCP   58m
service/mysql               ClusterIP   172.30.236.110   &lt;none&gt;        3306/TCP   12m
service/wordpress           ClusterIP   172.30.87.138    &lt;none&gt;        80/TCP     15s

NAME                                READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello-world-nginx   1/1     1            1           58m
deployment.apps/mysql               1/1     1            1           12m
deployment.apps/wordpress           0/1     1            0           15s

NAME                                          DESIRED   CURRENT   READY   AGE
replicaset.apps/hello-world-nginx-874b97b6f   0         0         0       58m
replicaset.apps/hello-world-nginx-d58b5fc8b   1         1         1       56m
replicaset.apps/mysql-64d9f4d857              0         0         0       12m
replicaset.apps/mysql-66c6b4976d              1         1         1       9m39s
replicaset.apps/mysql-77c9c667d5              0         0         0       12m
replicaset.apps/mysql-fbf67ff96               0         0         0       10m
replicaset.apps/wordpress-594cbf6c55          1         0         0       15s
replicaset.apps/wordpress-d4956dc76           1         1         0       8s

NAME                                               TYPE     FROM   LATEST
buildconfig.build.openshift.io/hello-world-nginx   Docker   Git    1

NAME                                           TYPE     FROM          STATUS     STARTED          DURATION
build.build.openshift.io/hello-world-nginx-1   Docker   Git@d0630df   Complete   58 minutes ago   1m14s

NAME                                               IMAGE REPOSITORY                                                                         TAGS                UPDATED
imagestream.image.openshift.io/hello-world-nginx   image-registry.openshift-image-registry.svc:5000/review-troubleshoot/hello-world-nginx   latest              56 minutes ago
imagestream.image.openshift.io/mysql               image-registry.openshift-image-registry.svc:5000/review-troubleshoot/mysql               1-139               12 minutes ago
imagestream.image.openshift.io/ubi8                image-registry.openshift-image-registry.svc:5000/review-troubleshoot/ubi8                8.0                 58 minutes ago
imagestream.image.openshift.io/wordpress           image-registry.openshift-image-registry.svc:5000/review-troubleshoot/wordpress           5.7-php7.4-apache   8 seconds ago

NAME                                         HOST/PORT                           PATH   SERVICES            PORT       TERMINATION   WILDCARD
route.route.openshift.io/hello-world-nginx   hello-world.apps.ocp4.example.com          hello-world-nginx   8080-tcp                 None
[student@workstation DO280-apps]$ oc get pods -l=wordpress
No resources found in review-troubleshoot namespace.
[student@workstation DO280-apps]$ oc get pods
NAME                                READY   STATUS      RESTARTS   AGE
hello-world-nginx-1-build           0/1     Completed   0          58m
hello-world-nginx-d58b5fc8b-csv6n   1/1     Running     0          57m
mysql-66c6b4976d-dbbwk              1/1     Running     0          10m
wordpress-d4956dc76-cclgj           0/1     Error       2          46s
[student@workstation DO280-apps]$ oc set env deployment/wordpress \
&gt;    --prefix WORDPRESS_DB_ --from secret/mysql
deployment.apps/wordpress updated
[student@workstation DO280-apps]$ oc create serviceaccount wordpress-sa
serviceaccount/wordpress-sa created
[student@workstation DO280-apps]$ oc whoami
developer
[student@workstation DO280-apps]$ oc login -u admin -p redhat
Login successful.

You have access to 60 projects, the list has been suppressed. You can list all projects with &apos; projects&apos;

Using project &quot;review-troubleshoot&quot;.
[student@workstation DO280-apps]$ oc adm policy add-scc-to-user anyuid -z wordpress-sa
clusterrole.rbac.authorization.k8s.io/system:openshift:scc:anyuid added: &quot;wordpress-sa&quot;
[student@workstation DO280-apps]$ oc login -u developer -p redhat
Login successful.

You have one project on this server: &quot;review-troubleshoot&quot;

Using project &quot;review-troubleshoot&quot;.
[student@workstation DO280-apps]$ oc set serviceaccount deployment/wordpress \
&gt;     wordpress-sa
deployment.apps/wordpress serviceaccount updated
[student@workstation DO280-apps]$ oc get pods
NAME                                READY   STATUS      RESTARTS   AGE
hello-world-nginx-1-build           0/1     Completed   0          61m
hello-world-nginx-d58b5fc8b-csv6n   1/1     Running     0          60m
mysql-66c6b4976d-dbbwk              1/1     Running     0          12m
wordpress-689f4f5c49-zvxb2          1/1     Running     0          10s
[student@workstation DO280-apps]$ oc get pods -l deployment=mysql
NAME                     READY   STATUS    RESTARTS   AGE
mysql-66c6b4976d-dbbwk   1/1     Running   0          13m
[student@workstation DO280-apps]$ oc exec mysql-66c6b4976d-dbbwk -- \
&gt; /usr/bin/mysql -uroot -e &quot;CREATE DATABASE wordpress&quot;
[student@workstation DO280-apps]$ oc expose service wordpress \
&gt;    --hostname wordpress.apps.ocp4.example.com
route.route.openshift.io/wordpress exposed
[student@workstation DO280-apps]$ 
</pre>

