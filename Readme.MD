# Trouble shooting project:
* To check Kubernetes pods connectivity to databases, you need to verify several things:
✅ 1. Check if the Pod is Running
Use:
kubectl get pods -n <namespace>
To describe a pod and see its details (including environment variables and network settings):
kubectl describe pod <pod-name> -n <namespace>

✅ 2. Check if the Pod Can Resolve the DB Hostname
Use:
kubectl exec -it <pod-name> -n <namespace> -- nslookup db.daws78s.online
Or with dig if available:
kubectl exec -it <pod-name> -n <namespace> -- dig db.daws78s.online

✅ 3. Check if Pod Can Ping DB Server (Basic Network Connectivity)
kubectl exec -it <pod-name> -n <namespace> -- ping -c3 db.daws78s.online

✅ 4. Check Port Connectivity to MySQL from the Pod
kubectl exec -it <pod-name> -n <namespace> -- telnet db.daws78s.online 3306
If telnet is not available, you can use nc (netcat):
kubectl exec -it <pod-name> -n <namespace> -- nc -zv db.daws78s.online 3306

✅ 5. Check MySQL Login from Pod
kubectl exec -it <pod-name> -n <namespace> -- \
mysql -h db.daws78s.online -u root -pExpenseApp@1
You can even run SQL commands like:
show databases;
use transactions;
show tables;

✅ 6. Check Environment Variables Used in Pod
Check if DB credentials and host are correctly set:
kubectl exec -it <pod-name> -n <namespace> -- env | grep -i db

✅ 7. Check Logs for Errors
To check for DB connection issues in application logs:
kubectl logs <pod-name> -n <namespace>
If it's a deployment:
kubectl logs deployment/<deployment-name> -n <namespace>

✅ 8. (Optional) Use a Debug Pod to Test Network/DB from Inside Cluster
You can create a debug pod with full tools:
kubectl run debug --rm -it --image=busybox -- /bin/sh
Or use nicolaka/netshoot image:
kubectl run netshoot --rm -it --image=nicolaka/netshoot -- bash
From there, test ping, DNS, telnet, curl, etc.


# How to create and delete all pods?
```
kubectl apply -f mysql/manifest.yaml
```
```
kubectl apply -f backend/manifest.yaml
```
```
kubectl apply -f frontend/manifest.yaml
```
```
kubectl apply -f debug/manifest.yaml
```
```
kubectl apply -f namespace.yaml
```

```
kubectl delete pods --all -n your-namespace
```