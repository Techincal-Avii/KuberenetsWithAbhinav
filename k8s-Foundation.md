Kubernets - This tool help us to manage the containerized applications
---------------------------
Why k8s - It solves 5 major issues - 1. Self-healing , if conatiner crashed it automatically restarts. 2. Autoscalling - If traffic increases it auto scales the Pods. 3. Rescheduling - If machine died, it automatically reschedule it to the availble nods. 4. Rolloing Updates - If we have new version deploy it need zero downtime. 5. Declrative Config - We can manage 500 container nut modigying the YMAl file.
-----------------------------
k8s Architecture- 
Control Plane the boss (Simply who takes decisions)
Worker Nodes - these are simply workers (who apply the decisions containers are run in worker node)
COMPONENTS OF CONTROL PLANE -
1. API Server - This is the command line tool where client run the commands
2. ETCD - This is the  Database of the server all the states of the containers are store here like pods, status of the pods allocation of the pods. if this will corrupted full cluster will died.
3. SCHEDULER - It simply schedule or decides the pod will run on which availble node and checks the cpu ram.
4. CONTROLL MANAGER - It continuously spys the cluseter. It is responsible for self healing and checks that the current state of the cluster is matches with the desired state. Is state mismatches then is trigeerd reconciliation.
Worker Node -
1. KUBELET - Its the agent availble on every node. Its takes the order fron control plane and checks the helath of the pods.
2. KUBE PROXY - Its handles networking and responsible for traffic is reaching to the pods.
3. CONTAINER RUNTIME -It actually run the conatiner.
---------------------------------
THE FLOW
kubectl apply -f pod.yaml
        ↓
   API Server (request receive ki)
        ↓
   etcd (desired state save ki — "1 pod chahiye")
        ↓
   Scheduler (decide kiya — Node 2 pe jaayega)
        ↓
   API Server → Kubelet on Node 2
        ↓
   Kubelet → Container Runtime
        ↓
   Container starts running 
