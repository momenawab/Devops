
Part1:

1️⃣️ create pod nginx with name my nginx direct from command don't use yaml file 

    🟢️momen@pop-os:~/Devops$ kubectl run mypod1 --image=nginx:latest
        pod/mypod1 created
==========================================================================================================================================
2️⃣️ create pod nginx with name my nginx command and use Image nginx123  direct from command don't use yaml file
        🟢️momen@pop-os:~/Devops$ kubectl run mypod2 --image=nginx123
        pod/mypod2 created
==========================================================================================================================================
3️⃣️ check the status and why it doesn't work 
    🟢️momen@pop-os:~/Devops$ kubectl describe po mypod2
      Warning  Failed     5s (x3 over 46s)   kubelet            Failed to pull image "nginx123": Error response from daemon: pull access
       denied for nginx123, repository does not exist or may require 'docker login': denied: requested access to the resource is denied
      🟢️Explain : beause nginx123 not real image 
==========================================================================================================================================
4️⃣️ I need to know node name - IP - Image Of the POD
    🟢️momen@pop-os:~/Devops$ kubectl describe po mypod2
        IP:               10.244.0.70
        Containers:
          mypod2:
            Container ID:   
            Image:          nginx123
            Image ID:       
            Port:           <none>
            Host Port:      <none>
            State:          Waiting
              Reason:       ImagePullBackOff
==========================================================================================================================================
5️⃣️ delete the pod 
   🟢️ momen@pop-os:~/Devops$ kubectl delete pod mypod2
       pod "mypod2" deleted
==========================================================================================================================================
6️⃣️ create another one with yaml file and use label

    🟢️ apiVersion: v1
        kind: Pod
        metadata:
          name: mypod
          labels:
            tier: nginx
        spec:
          containers:
          - name: nginx
            image: nginx123

            ports:
            - containerPort: 80

 Terminl :  momen@pop-os:~/Devops$ kubectl apply -f pod.yaml
            pod/mypod created
            
==========================================================================================================================================
7️⃣️ create Riplicaset with 3 replicas using nginx Image 

🟢️apiVersion: apps/v1
    kind: ReplicaSet
    metadata:
      name: nginx
      labels:
        tier: nginx
    spec:
      # modify replicas according to your case
      replicas: 3
      selector:
        matchLabels:
          tier: nginx
      template:
        metadata:
          labels:
            tier: nginx
        spec:
          containers:
          - name: backend
            image: nginx
            
        
8️⃣️ scale the replicas to 5 without edit in the Yaml file

    🟢️  momen@pop-os:~/Devops/DockerTask/Kubernates$ kubectl scale replicaset nginx --replicas=5
        replicaset.apps/nginx scaled
        9-Delete any one of the 5 pods and check what happen and explain 
        momen@pop-os:~/Devops/DockerTask/Kubernates$ kubectl delete pod nginx-c6nm2
        pod "nginx-c6nm2" deleted

    **explain : the replicaset auto created another on instead of the deleted pod**
==========================================================================================================================================    
1️⃣️0️⃣️ Scale down the pods aging to 2 without scale command use terminal  

🟢️ momen@pop-os:~/Devops/DockerTask/Kubernates$ kubectl scale replicaset nginx --replicas=2
   replicaset.apps/nginx scaled
==========================================================================================================================================
1️⃣️1️⃣️ find out the issue in the below Yaml (don't use AI)

apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-2
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        
        
       momen@pop-os:~/Devops/DockerTask/Kubernates$ kubectl apply -f test.yaml 
        The ReplicaSet "replicaset-2" is invalid: spec.template.metadata.labels: Invalid value: map[string]string{"tier":"nginx"}: 
         `selector` does not match template `labels`
         
       **explain : the matchlabels set as frontend and it try  to named in pods nginx it must be matching with the same matchlabels var**
==========================================================================================================================================
1️⃣️2️⃣️ find out the issue in the below Yaml (don't use AI)

apiVersion: apps/v1
kind: deployment
metadata:
  name: deployment-1
spec:
  replicas: 2
  selector:
    matchLabels:
      name: busybox-pod
  template:
    metadata:
      labels:
        name: busybox-pod
    spec:
      containers:
      - name: busybox-container
        image: busybox
        command:
        - sh
        - "-c"
        - echo Hello Kubernetes! && sleep 3600
            🟢️momen@pop-os:~/Devops/DockerTask/Kubernates$ kubectl apply -f test.yaml 
            Error from server (BadRequest): error when creating "test.yaml": deployment in version "v1" cannot be handled as a Deployment:
             no kind "deployment" is registered for version "apps/v1" in scheme "pkg/api/legacyscheme/scheme.go:30"
       **explain: Deployment must be "D" capital and it being small**
==========================================================================================================================================
1️⃣️3️⃣️ find out the issue in the below Yaml (don't use AI)

apiVersion: v1
kind: Deployment
metadata:
  name: deployment-1
spec:
  replicas: 2
  selector:
    matchLabels:
      name: busybox-pod
  template:
    metadata:
      labels:
        name: busybox-pod
    spec:
      containers:
      - name: busybox-container
        image: busybox
        command:
        - sh
        - "-c"
        - echo Hello Kubernetes! && sleep 3600
            🟢️momen@pop-os:~/Devops/DockerTask/Kubernates$ kubectl apply -f test.yaml 
            error: resource mapping not found for name: "deployment-1" namespace: "" from "test.yaml": no matches for kind "Deployment" in
             version "v1"
            ensure CRDs are installed first
       **expalin : we should use apps/v1 to make deployment is valid**
==========================================================================================================================================
1️⃣️4️⃣️ what's command you use to know what Image name that running the deployment 
       🟢️ momen@pop-os:~/Devops/DockerTask/Kubernates$ kubectl describe deployment nginx | grep -i image
            Image:         nginx:latest
==========================================================================================================================================
1️⃣️5️⃣️ create deployment using following data :

🟢️🟢️
Name: httpd-frontend;
Replicas: 3;
Image: httpd:2.4-alpine

apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-frontend
spec:
  replicas: 3
  selector:
    matchLabels:
      app: httpd-frontend
  template:
    metadata:
      labels:
        app: httpd-frontend
    spec:
      containers:
      - name: httpd
        image: httpd:2.4-alpine
        ports:
        - containerPort: 80
==========================================================================================================================================
1️⃣️6️⃣️ replace the image to nginx777 with command directly 

    🟢️ momen@pop-os:~/Devops/DockerTask/Kubernates$ kubectl set image deployment/httpd-frontend httpd=nginx777
        deployment.apps/httpd-frontend image updated
==========================================================================================================================================
1️⃣️7️⃣️ rollback to pervious version
       🟢️  momen@pop-os:~/Devops/DockerTask/Kubernates$kubectl rollout undo deployment/httpd-frontend
            deployment.apps/httpd-frontend rolled back
==========================================================================================================================================
1️⃣️8️⃣️ Create a Simple Web Application:
* Use a Dockerfile to create a simple web application (e.g., an Nginx server serving an HTML page).
* Build the Docker image and push it to DockerHub your private Account.
https://hub.docker.com/r/believeer/webapp
Html and docker file in Github and Files of task 
==========================================================================================================================================
1️⃣️9️⃣️ Create a Deployment Using This Image:
* Deploy the Docker image from DockerHub to Kubernetes with a Deployment that has 3 replicas.

apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp
        image: believeer/webapp:latest
        ports:
        - containerPort: 80
==========================================================================================================================================
