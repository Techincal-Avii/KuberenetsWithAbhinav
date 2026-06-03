POD - Its is the smalles unit of k8s and its kind of wrappen around the containers.
┌─────────────────────────────┐
│           POD               │
│  ┌─────────┐  ┌─────────┐   │
│  │Container│  │Container│   │
│  └─────────┘  └─────────┘   │
│                             │
│  - Shared Network (IP)      │
│  - Shared Storage           │
│  - ON Same Node             │
└─────────────────────────────┘

In pod we have multiple conatiners but when ?
99% case - pod hase one container
1% cases - pod have multiple container

Pattern 1 - SIDECAR CONTAINER
A Sidecar Container runs alongside the main application container throughout the pod's lifecycle.
Pattren 2 - INIT CONTAINER
An Init Container is used for initialization tasks that must complete before the main application starts, such as waiting for a database, downloading configuration files, or running migrations. It runs once and exits successfully before the application container starts.

Main Container = Node.js App
                 (logs likh raha hai files mein)

Sidecar Container = Fluentd
                    (woh files read karta hai
                     aur Elasticsearch ko bhejta hai)

Dono saath chalte hain — 
App logs likhti hai → 
Fluentd collect karta hai → 
Dashboard mein dikhta hai ✅
----------------------------------------------
REPLICA SET - It makes ensure that the current state of application is maches the desigred state.
How replica set knows which pods are maintaned - it uses LABELS AND SELECTORS - Each pod have lablen and replica set have selector if lables are matched then replica set knows these aye my pods.
SLEF-HEALING - 
ReplicaSet watch kar raha hai:

Normal:     [Pod1] [Pod2] [Pod3] ✅

Pod1 crash: [DEAD] [Pod2] [Pod3] 
            ↓
ReplicaSet: "Sirf 2 hain! 3 chahiye!"
            ↓
            [Pod4] [Pod2] [Pod3] ✅
            (naya pod auto create)

Node dead:  [Pod4] [Pod2] [Pod3] 
            sab ek hi node pe the
            ↓
ReplicaSet: Doosre node pe 3 naye pods ✅
----------------------------------------------
DEPLOYMENT - It manages the replicaset, rolling updates and rollback capability. In production deployment is alwys used.

Naya version deploy kiya →
Kuch gadbad ho gayi →
Ek command se wapas jaao!

kubectl rollout undo deployment/my-app

Deployment automatically 
purane ReplicaSet pe wapas jaata hai ✅

If there is bug or any problen in the new update we use stetegy 

kubectl rollout status deployment/my-app
→ Stuck dikhega ✅

kubectl get pods
→ CrashLoopBackOff dikhega ✅

kubectl logs <pod-name> --previous
→ Error logs dikhenge ✅
---------------------------------------------------------------------
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  strategy:
    type: RollingUpdate        # default strategy
    rollingUpdate:
      maxSurge: 1              # ek extra pod create kar sakta hai
      maxUnavailable: 1        # ek pod unavailable ho sakta hai
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: nginx:1.21      # 1.21 → 1.22 karna hai
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"

--------------------------------------------------
SERVICE - 
