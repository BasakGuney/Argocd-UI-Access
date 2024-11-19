# Argocd UI Access
## Assumptions
> We assume that you have installed argocd on your Rocky9 VM. Since your VM doesn't have UI you want to access Argocd UI from your host PC by using web browser.

## Option 1 - Port Forwarding

Run the following command to forward argocd-server's 443 port to VMs 8080 port:
```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
If you get port already in use error you can try 8000 instead of 8080.

Don't end this process while you're accessing to UI.  
<br>

Then on your host PC, open powershell and run the following command:
```bash
ssh -L 8080:localhost:8080 user@<vm-ip>
```
The vm-ip is your master node's IP since you run the port-forward command there.  

<br>

After this you can acces your VM's 8080 port from your host PC's 8080 port. Go to the web browser on your host PC and type following:
```txt
http://localhost:8080
```

Now, you have to be able to view Argocd UI.

Default username:
```txt
admin
```
Password:  
Run the following command to get your password
```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode ; echo
```


## Option 2 - Node Port
You can open your node to an external access by using NodePort services. To enable it,

```bash
kubectl expose deployment argocd-server --name=argocd-server-np --port=80 --target-port=8080 --type=NodePort --namespace=argocd 
```
The command above exposes the argocd-server to a NodePort service named argocd-server-np.


You can check which port the argocd-server-np service uses by running the following command. (It's in >=3000 range)
```bash
kubectl get svc -n argocd
```
Keep in mind the port number, we will use it.

<br>

Run the following command the learn your master node's IP:
```bash
 kubectl get nodes -o wide
```
You can see it under INTERNAL-IP.

Then go to the browser on your host PC and type following:
```txt
http://<node-ip>:<node-port>
```

Now, you have to be able view Argocd UI.



