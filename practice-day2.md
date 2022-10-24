Day 2 Practice Mock Tasks
-----------------------------------------

Task 7 :

As the admin user, update the OpenShift cluster to use a new project template. The project
template must automatically create the network policy, limit range, and add quota resources
for new projects. New projects must automatically have a label matching the name of the
project. For example, a project named test has the name=test label.

Project => Includes a label with the name of the project.

NetworkPolicy =>
Policy 1:
• Routes are accessible to external traffic; this means that traffic is allowed
from pods in namespaces with the network.openshift.io/policy-
group=ingress label.
Policy 2:
• Pods in the same namespace can communicate with each other.
• Pods do not respond to pods that exist in a different namespace,
except namespaces with the network.openshift.io/policy-
group=ingress label.

LimitRange => 

Each container requests 30 millicores of CPU.
Each container requests 30 MiB of memory.
Each container is limited to 100 millicores of CPU.
Each container is limited to 100 MiB of memory.

ResourceQuota =>

Projects are limited to 10 pods.
Projects can request a maximum of 1 GiB of memory.
Projects can request a maximum of 2 CPUs.
Projects can use a maximum of 4 GiB of memory.
Projects can use a maximum of 4 CPUs.


<pre>[student@workstation DO280-apps]$ 
[student@workstation DO280-apps]$ 
[student@workstation DO280-apps]$ oc whoami
admin
[student@workstation DO280-apps]$ oc whoami --show-console
https://console-openshift-console.apps.ocp4.example.com
[student@workstation DO280-apps]$ oc adm create-bootstrap-project-template \
&gt; -o yaml &gt; ~/DO280/labs/review-template/project-template.yaml
[student@workstation DO280-apps]$ cd ~/DO280/labs/review-template
[student@workstation review-template]$ ls
hello-secure-combined.pem  hello-secure-key.pem  project-template.yaml  <font color="#0087FF">sample-files</font>
[student@workstation review-template]$ vi project-template.yaml 
[student@workstation review-template]$ cd sample-files/
[student@workstation sample-files]$ ls
allow-from-openshift-ingress.yaml  allow-same-namespace.yaml  limitrange.yaml  resourcequota.yaml
[student@workstation sample-files]$ vi allow-from-openshift-ingress.yaml 
[student@workstation sample-files]$ vi allow-same-namespace.yaml 
[student@workstation sample-files]$ la
bash: la: command not found...
[student@workstation sample-files]$ ls
allow-from-openshift-ingress.yaml  allow-same-namespace.yaml  limitrange.yaml  resourcequota.yaml
[student@workstation sample-files]$ vi limitrange.yaml 
[student@workstation sample-files]$ ls
allow-from-openshift-ingress.yaml  allow-same-namespace.yaml  limitrange.yaml  resourcequota.yaml
[student@workstation sample-files]$ vi resourcequota.yaml 
[student@workstation sample-files]$ cd ..
[student@workstation review-template]$ oc create -f project-template.yaml \
&gt; -n openshift-config
The Template &quot;project-request&quot; is invalid: metadata.labels: Invalid value: &quot;${PROJECT_NAME}&quot;: a valid label must be an empty string or consist of alphanumeric characters, &apos;-&apos;, &apos;_&apos; or &apos;.&apos;, and must start and end with an alphanumeric character (e.g. &apos;MyValue&apos;,  or &apos;my_value&apos;,  or &apos;12345&apos;, regex used for validation is &apos;(([A-Za-z0-9][-A-Za-z0-9_.]*)?[A-Za-z0-9])?&apos;)
[student@workstation review-template]$ vi project-template.yaml 
[student@workstation review-template]$ oc create -f project-template.yaml -n openshift-config
template.template.openshift.io/project-request created
[student@workstation review-template]$ oc get templates -n openshift-config
NAME              DESCRIPTION   PARAMETERS    OBJECTS
project-request                 5 (5 blank)   6
[student@workstation review-template]$ oc edit \
&gt; projects.config.openshift.io/cluster
project.config.openshift.io/cluster edited
[student@workstation review-template]$ watch oc get pods -n openshift-apiserver
</pre>


Task 8 :


As the developer user, create a project named review-template. Ensure that the review-template project inherits the settings specified in the new project template. In the review-template project, create a deployment named hello-secure using the container image located at quay.io/redhattraining/hello-world-secure:v1.0.


<pre>

[student@workstation review-template]$ oc login -u developer -p developer
Login successful.

You have one project on this server: &quot;review-troubleshoot&quot;

Using project &quot;review-troubleshoot&quot;.
[student@workstation review-template]$ oc new-project review-template
Now using project &quot;review-template&quot; on server &quot;https://api.ocp4.example.com:6443&quot;.

You can add applications to this project with the &apos;new-app&apos; command. For example, try:

    oc new-app rails-postgresql-example

to build a new example application in Ruby. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=k8s.gcr.io/serve_hostname

[student@workstation review-template]$ oc get networkpolicies
NAME                           POD-SELECTOR   AGE
allow-from-openshift-ingress   &lt;none&gt;         27s
allow-same-namespace           &lt;none&gt;         27s
[student@workstation review-template]$ oc get limitranges
NAME                 CREATED AT
project-limitrange   2022-10-24T04:01:33Z
[student@workstation review-template]$ oc get resourcequotas
NAME            AGE   REQUEST                                                 LIMIT
project-quota   55s   pods: 0/10, requests.cpu: 0/2, requests.memory: 0/1Gi   limits.cpu: 0/4, limits.memory: 0/4Gi
[student@workstation review-template]$ oc get project review-template \
&gt; --show-labels
NAME              DISPLAY NAME   STATUS   LABELS
review-template                  Active   name=review-template
[student@workstation review-template]$ 
</pre>

<pre>
[student@workstation review-template]$ 
[student@workstation review-template]$ 
[student@workstation review-template]$ 
[student@workstation review-template]$ oc whoami
developer
[student@workstation review-template]$ oc project
Using project &quot;review-template&quot; on server &quot;https://api.ocp4.example.com:6443&quot;.
[student@workstation review-template]$ oc new-app --name hello-secure \
&gt; --image quay.io/redhattraining/hello-world-secure:v1.0
Error: unknown flag: --image
See &apos;oc new-app --help&apos; for usage.
[student@workstation review-template]$ oc new-app --name hello-secure --docker-image quay.io/redhattraining/hello-world-secure:v1.0
--&gt; Found container image 2a1b082 (2 years old) from quay.io for &quot;quay.io/redhattraining/hello-world-secure:v1.0&quot;

    Red Hat Universal Base Image 8 
    ------------------------------ 
    The Universal Base Image is designed and engineered to be the base layer for all of your containerized applications, middleware and utilities. This base image is freely redistributable, but Red Hat only supports Red Hat technologies through subscriptions for Red Hat products. This image is maintained by Red Hat and updated regularly.

    Tags: base rhel8

    * An image stream tag will be created as &quot;hello-secure:v1.0&quot; that will track this image

--&gt; Creating resources ...
    imagestream.image.openshift.io &quot;hello-secure&quot; created
    deployment.apps &quot;hello-secure&quot; created
    service &quot;hello-secure&quot; created
--&gt; Success
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     &apos;oc expose service/hello-secure&apos; 
    Run &apos;oc status&apos; to view your app.
[student@workstation review-template]$ oc status
In project review-template on server https://api.ocp4.example.com:6443

svc/hello-secure - 172.30.44.192:8443
  deployment/hello-secure deploys istag/hello-secure:v1.0 
    deployment #2 running for 10 seconds - 0/1 pods
    deployment #1 deployed 11 seconds ago - 0/1 pods growing to 1


1 info identified, use &apos;oc status --suggest&apos; to see details.
[student@workstation review-template]$ watch oc get pods

[1]+  Stopped                 watch oc get pods
[student@workstation review-template]$ oc get pods
NAME                           READY   STATUS   RESTARTS   AGE
hello-secure-9bb4d6cb8-mg7rj   0/1     Error    3          74s

</pre>


Task 9 :

As the developer user, create a TLS secret using the hello-secure-combined.pem certificate and the hello-secure-key.pem key located in the ~/DO280/labs/review-template/ directory. Use the logs from the failed hello-secure pod to determine the expected mount point for the certificate. Mount the TLS secret as a volume in the pod using the identified directory. Verify that the hello-secure pod successfully redeploys.



<pre>

[student@workstation review-template]$ ls
hello-secure-combined.pem  hello-secure-key.pem  project-template.yaml  <font color="#0087FF">sample-files</font>
[student@workstation review-template]$ oc create secret tls hello-tls \
&gt; --cert hello-secure-combined.pem --key hello-secure-key.pem
secret/hello-tls created
[student@workstation review-template]$ oc logs hello-secure-9bb4d6cb8-mg7rj 
nginx: [warn] the &quot;user&quot; directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:5
nginx: [emerg] BIO_new_file(&quot;/run/secrets/nginx/tls.crt&quot;) failed (SSL: error:02001002:system library:fopen:No such file or directory:fopen(&apos;/run/secrets/nginx/tls.crt&apos;,&apos;r&apos;) error:2006D080:BIO routines:BIO_new_file:no such file)
[student@workstation review-template]$ oc set volumes deployment/hello-secure \
&gt; --add --type secret --secret-name hello-tls --mount-path /run/secrets/nginx
info: Generated volume name: volume-fmmsp
deployment.apps/hello-secure volume updated
[student@workstation review-template]$ oc get pods
NAME                            READY   STATUS    RESTARTS   AGE
hello-secure-5c57bbf467-m6lng   1/1     Running   0          26s
[student@workstation review-template]$ oc get all
NAME                                READY   STATUS    RESTARTS   AGE
pod/hello-secure-5c57bbf467-m6lng   1/1     Running   0          33s

NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
service/hello-secure   ClusterIP   172.30.44.192   &lt;none&gt;        8443/TCP   3m46s

NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/hello-secure   1/1     1            1           3m46s

NAME                                      DESIRED   CURRENT   READY   AGE
replicaset.apps/hello-secure-5c57bbf467   1         1         1       33s
replicaset.apps/hello-secure-64975fbb78   0         0         0       3m46s
replicaset.apps/hello-secure-9bb4d6cb8    0         0         0       3m45s

NAME                                          IMAGE REPOSITORY                                                                TAGS   UPDATED
imagestream.image.openshift.io/hello-secure   image-registry.openshift-image-registry.svc:5000/review-template/hello-secure   v1.0   3 minutes ago


</pre>


Task 10 :

The hello-secure-combined.pem certificate is valid for a single host name. Use the openssl x509 command with the -noout and -ext 'subjectAltName' options to read the hello-secure-combined.pem certificate and identify the host name. As the developer user, create a passthrough route to the hello-secure service using the identified host name. Verify that the route responds to external requests.


<pre>


[student@workstation review-template]$ openssl x509 \
&gt; -in hello-secure-combined.pem -noout -ext &apos;subjectAltName&apos;
X509v3 Subject Alternative Name: 
    DNS:hello-secure.apps.ocp4.example.com
[student@workstation review-template]$ oc create route passthrough \
&gt; --service hello-secure --hostname hello-secure.apps.ocp4.example.com
route.route.openshift.io/hello-secure created
[student@workstation review-template]$ curl -s https://hello-secure.apps.ocp4.example.com \
&gt; | grep -i Hello
    &lt;h1&gt;<font color="#EF2929"><b>Hello</b></font>, world from nginx!&lt;/h1&gt;
[student@workstation review-template]$ 
</pre>


Task 11 :

As the developer user, configure the hello-secure deployment to scale automatically. The deployment must have at least one pod running. If the average CPU utilization exceeds 80%, then the deployment scales to a maximum of five pods.


<pre>            HPA_READY=&quot;true&quot;
            echo &quot;READY&quot;
            echo &quot;============================================================&quot;
            echo
            break
          else
            sleep 5
            ((HPA_LOOP_COUNT=HPA_LOOP_COUNT+1))
          fi
        done

        if [ ${HPA_LOOP_COUNT} -eq ${HPA_LOOP_LIMIT} ]
        then
          echo &quot;NOT READY&quot;
          echo &quot;============================================================&quot;
        fi
      else
        HPA_READY=&quot;true&quot;
      fi

      if [ &quot;${HPA_READY}&quot; == &quot;true&quot; ]
      then
        echo
        ab -c200 -k -dS -n100000 https://${HOSTNAME}/index.html
      fi
    else
      echo
      echo &quot;ERROR: &apos;${POD}&apos; did not make a CPU request.&quot; 1&gt;&amp;2
      echo
    fi
  fi
fi

[student@workstation review-template]$ clear

[student@workstation review-template]$ oc get pods
NAME                            READY   STATUS    RESTARTS   AGE
hello-secure-5c57bbf467-m6lng   1/1     Running   0          22m
[student@workstation review-template]$  oc autoscale deployment/hello-secure \
&gt;     --min 1 --max 5 --cpu-percent 80
horizontalpodautoscaler.autoscaling/hello-secure autoscaled
[student@workstation review-template]$ ./test-hpa.sh 

This is ApacheBench, Version 2.3 &lt;$Revision: 1843412 $&gt;
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking hello-secure.apps.ocp4.example.com (be patient)
Completed 10000 requests
Completed 20000 requests
Completed 30000 requests
Completed 40000 requests
Completed 50000 requests
Completed 60000 requests
Completed 70000 requests
Completed 80000 requests
Completed 90000 requests
Completed 100000 requests
Finished 100000 requests


Server Software:        nginx/1.14.1
Server Hostname:        hello-secure.apps.ocp4.example.com
Server Port:            443
SSL/TLS Protocol:       TLSv1.2,ECDHE-RSA-AES256-GCM-SHA384,4096,256
Server Temp Key:        X25519 253 bits
TLS Server Name:        hello-secure.apps.ocp4.example.com

Document Path:          /index.html
Document Length:        72 bytes

Concurrency Level:      200
Time taken for tests:   73.827 seconds
Complete requests:      100000
Failed requests:        0
Keep-Alive requests:    99080
Total transferred:      30795400 bytes
HTML transferred:       7200000 bytes
Requests per second:    1354.51 [#/sec] (mean)
Time per request:       147.655 [ms] (mean)
Time per request:       0.738 [ms] (mean, across all concurrent requests)
Transfer rate:          407.35 [Kbytes/sec] received

Connection Times (ms)
              min   avg   max
Connect:        0    2811018
Processing:     0   120 1499
Waiting:        0   120 1499
Total:          0   14711321
[student@workstation review-template]$ oc get pods
NAME                            READY   STATUS    RESTARTS   AGE
hello-secure-5c57bbf467-2svwf   1/1     Running   0          42s
hello-secure-5c57bbf467-fnbhs   1/1     Running   0          42s
hello-secure-5c57bbf467-g48gr   1/1     Running   0          42s
hello-secure-5c57bbf467-m6lng   1/1     Running   0          24m
hello-secure-5c57bbf467-t9nm9   1/1     Running   0          27s
[student@workstation review-template]$ 
</pre>
