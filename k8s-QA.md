QA 1 . Your pod is in CrashLoopBackOff. Walk me through your complete debugging process — commands, what you're looking for at each step, and 5 different root causes it could be.
Ans. CrashloopBackOff - its a status is k8s that shows the pods are crashing or restarting again and again.
DEBUGGUNG -
    Setps 1 - Check the pod status it check the current status of the pod - kubectl get pods (pod name )
              Check The events - kubectl grt events
    Steps 2 - We describe the pods it gives us a detailed information or here we can check the health probes liveness or readiness - kubectl describe pod (pod name)
    steb 3 - Check ing the logs it checks the logs of the pod like any error - kubectl logs (pod name) or kubectl logs (pod name) -- previous, it helps us to check the previous crashed container instance
    Step 3 - We can sheck the memory issue - kbectl top pods or (pod name)
    Step 4 - We can even check the memory issue with node - kubectl top nodes
    step5 - Check the env variable and cofig maps for any missing key or wrong values - kubectl get configmap or kubectl get secrets
MAJOR REASONS - 
    1. Any exception or syntax error in deployment
    2. OOMKILLED (Memory Limit Issue)- we can add or increase the memory
    3. Incorrect Liveness Probe - We can recheck the path
    4. Secrets and configmap Missmatch 
ERRORS -  0 - success, 1 - Application Error, 137 - OOMKILLED(memory limit error), 139 - Segmentation Fould, 143 - SIGTERM (shutdown didnt complete)

QA2. 