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
