# Configuring Kubernetes with Docker Desktop on windows

This is basic configuration used for local kubernetes setup. Please check official documents for production best practise 

> **Note:** please make sure that you run Powershell as administrator

 1. Go to docker setting --> kubernetes --> select check box **_Enable Kubernetes_** --> And click _**Apply**_
 2. Once installed you will get success message at bottom of the setting windows
 	* _Docker is running_
	* _Kubernetes is running_
 
 3. Verify you installation       
	> `kubectl version`
	---
	    Client Version: version.Info{Major:"1", Minor:"14", GitVersion:"v1.14.0", GitCommit:"641856db18352033a0d96dbc99153fa3b27298e5", GitTreeState:"clean", BuildDate:"2019-03-25T15:53:57Z", GoVersion:"go1.12.1", Compiler:"gc", Platform:"windows/amd64"}
        Server Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.11", GitCommit:"637c7e288581ee40ab4ca210618a89a555b6e7e9", GitTreeState:"clean", BuildDate:"2018-11-26T14:25:46Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"linux/amd64"}`
	
	> `kubectl config current-context`
	---
	    docker-for-desktop
	> `kubectl cluster-info` 
	---
        Kubernetes master is running at https://localhost:6445
        KubeDNS is running at https://localhost:6445/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
	
	> `kubectl get nodes`
	---
	    NAME                 STATUS   ROLES    AGE   VERSION
        docker-for-desktop   Ready    master   18h   v1.10.11
	
 4. Kubernetes does not come by default with a dashboard and you need to enable it with the following command:
	> `kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml`
	---
        secret "kubernetes-dashboard-certs" created  
        serviceaccount "kubernetes-dashboard" created 
        role "kubernetes-dashboard-minimal" created 
        rolebinding "kubernetes-dashboard-minimal" created 
        deployment "kubernetes-dashboard" created 
        service "kubernetes-dashboard" created

 5. To access Dashboard from your local machine you must create a secure channel to your Kubernetes cluster. Create an _admin-user_ service account 
    and cluster role binding that you can use to securely connect to the dashboard with admin-level permissions.
    (If you want to skip then follow step 10)
 
		apiVersion: v1
		kind: ServiceAccount
		metadata:
		  name: admin-user
		  namespace: kube-system
		---
		apiVersion: rbac.authorization.k8s.io/v1
		kind: ClusterRoleBinding
		metadata:
		  name: admin-user
		roleRef:
		  apiGroup: rbac.authorization.k8s.io
		  kind: ClusterRole
		  name: cluster-admin
		subjects:
		- kind: ServiceAccount
		  name: admin-user
		  namespace: kube-system

	Copy above snippets to dashboard-admin-service-account.yaml and apply the service account and cluster role binding to your cluster 
	> `kubectl apply -f dashboard-admin-service-account.yaml` 
	---
	    serviceaccount "admin-user" created
        clusterrolebinding.rbac.authorization.k8s.io "admin-user" created
 
 6. Now to get token to login to dashboard, execute following command and look for :
	> `kubectl -n kube-system describe secret (kubectl -n kube-system get secret | findstr "admin-user" | %{ $_.Split(' ')[0];})`
	---
	    Name:         admin-user-token-rq5fz
        Namespace:    kube-system
        Labels:       <none>
        Annotations:  kubernetes.io/service-account.name: admin-user
                      kubernetes.io/service-account.uid: 74b95ef5-86ac-11e9-a4f0-00155d0cda01
        
        Type:  kubernetes.io/service-account-token
        
        Data
        ====
        ca.crt:     1025 bytes
        namespace:  11 bytes
	    token:      <authentication_token>
	
 7. Start the **kubectl proxy**:
	> `kubectl proxy`
 
 8. Now you can access Dashboard at:
    >`http://localhost:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy/`
  	 	
 9. Copy Token, paste the _`<authentication_token>`_ output from the previous command _(step 6)_ into the Token field, and click SIGN IN.

 10. To enable skip button on the dashboard login page for dashboard version v1.10.1 or later
	 > `kubectl edit deployment/kubernetes-dashboard --namespace=kube-system`
	 
	 This will open kubernetes dashboard config file in notepad add `--enable-skip-login` to this file in args section as below:
	
         containers:
          - args:
            - --auto-generate-certificates
            - --enable-skip-login            #<-- add this line
            image: k8s.gcr.io/kubernetes-dashboard-amd64:v1.10.1
            
            
            