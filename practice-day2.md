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


Task 8 :


As the developer user, create a project named review-template. Ensure that the review-template project inherits the settings specified in the new project template. In the review-template project, create a deployment named hello-secure using the container image located at quay.io/redhattraining/hello-world-secure:v1.0.


