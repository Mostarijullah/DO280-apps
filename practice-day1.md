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

