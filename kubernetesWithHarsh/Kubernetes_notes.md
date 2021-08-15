# Kubernetes the hard way
### Thoughts and reflections 
I was motivated by the idea of supercomputers and also for developing production applications in a machine learning environment (using kubeflow) which prompted me to begin my journey to learn kubernetes. This markdown serves as a platform for me to record down what has been learnt and hopefully this can be useful to others who are also interested in pursuing kubernetes

#### Authentication
There are a few types of users who may access a cluster: 
- Admins 
- Developers 
- End Users 
- Bots 

We can secure access to the kubernetes cluster using different authentication mechanisms

Kubernetes cannot access a list of user accounts, but it is able to access the service accounts in the cluster 

> A service account is a user account that is created explicitly to provide a security context for services running on operating systems. The security context determines the service's ability to access local and network resources

All user access is managed by the **kube-apiserver**

There are different authentication mechanisms: 
1. Static Password Files: 
Create a list of users and password and userid in a csv file and pass it as an argument to the kube-apiserver.service file as --basic-auth-file=name-of-csv-file. If you set up your cluster using the kubeadm tool then you must edit the pod definition file in /etc/kubernetes/manifests/kube-apiserver.yaml 

2. Static Token Files:  
Similar to the static password file, you can specify a csv and pass it as an argument to kube-apiserver.service file as --token-auth-file=name-of-csv-file

3. Certificates:   
Related to TLS certificates \
**TLS Basics** 
If there is no secure connectivity, password and username is sent in plaintext which can be intercepted by hackers \
To tackle this, we encrypt the data with a key, and the server also needs the key. In the case where the key is sent with the data, it called symmetric encryption. However, hackers are still able to access the data  
**Assymetric Encryption** 
Public and private key 
```
creating keys for ssh purposes: 
ssh-keygen: generate a private and public key pair 
cat ~/.ssh/authorized_keys file to add the public lock 
```
```
creating keys: 
openssl genrsa -out my-bank.key 1024
openssl rsa -in my-bank.key -pubout > mybank.pem
```

How assymetric encryption works: 
1. server sends you a the public key with a certificate (validated through a certificate signing request)
2. Browser verifies the certificate against a certificate authority against the domain name of your site using the set of private and public key pairs of the certificate authorities  
3. If certificate is valid you will send over your symmetric key to the server (locked by the server's public key) 
4. server uses their private key to unlock and retrieve your symmetric key
5. connection is now established and you are now able to perform secure communication  
6. Sometimes server needs to verify that the client is who they say they are, and in that case, the client needs to generate its own certificates to be signed by the CA authority 
```
create certificate signing requests: 
openssl req -new -key my-bank.key -out my-bank.csr
-subj "/C=US/ST=CA/O=MyOrg, Inc./CN=my-bank.com"
```
usually certificates with public keys are named *.crt or *pem and private key are usually named *.key *-key.pem 

Server certificates:\
**Kube api-server** \
(name can be different) \
apiserver.crt, apiserver.key 

**etcd server** \
etcdserver.crt, etcdserver.key

**kubelet server** \
kubelet.crt, kubelet.key 

**Client certificates (Us/Admins)** \
We access the kube-api server through rest requests 

**admin** \
admin.crt admin.key 

**kube-scheduler** \
scheduler.crt scheduler.key 

4. Identity Services 