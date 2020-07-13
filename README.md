# task6

Perform third task with the help of Jenkins coding file
( called as jenkinsfile approach ) and perform the with
following phases:

1. Create container image that’s has Jenkins installed
   using dockerfile  Or You can use the Jenkins Server 
   on RHEL 8/7
2.  When we launch this image, it should automatically 
    starts Jenkins service in the container.
3.  Create a job chain of job1, job2, job3 and  job4 using
    build pipeline plugin in Jenkins 
4.  Job2 ( Seed Job ) : Pull  the Github repo automatically 
    when some developers push repo to Github.
5. Further on jobs should be pipeline using written code  
   using Groovy language by the developer
6. Job1 :  
    1. By looking at the code or program file, Jenkins should
       automatically start the respective language interpreter 
       installed image container to deploy code on top of 
       Kubernetes ( eg. If code is of  PHP, then Jenkins should 
       start the container that has PHP already installed )
    2.  Expose your pod so that testing team could perform the
        testing on the pod
    3. Make the data to remain persistent using PVC ( If server 
       collects some data like logs, other user information )
7.  Job3 : Test your app if it  is working or not.
8.  Job4 : if app is not working , then send email to developer 
           with error messages and redeploy the application after 
           code is being edited by the developer
           
           
 # Step 1:
 First step of the task is to create a dockerfile which has jenkins 
 installed with its yum repo configured with some required components 
 to run commands inside our centos container.In jenkins we have to 
 provide root or admin power which is not bydefault provided.With this 
 we have to expose jenkins with its port 8080. 
 After creation building the dockerfile with the command 
 
    docker build -t task6:v6
    
 Running the container image which is created after building
      
      docker run -it -p 2401 --name OS6 task6:v6
      
 # Step 2:
 Creating deployment for the server
 
 For this we have to create PVC for storing permanent data
 
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: pvc-storage
    spec:
    storageClassName: manual
    accessModes:
      - ReadWriteOnce
    resources:
      requests:
        storage: 3Gi
        
   Creating PVC with command:
     
         kubectl create -f pvc-storage.yml
         
  
  After creating pvc creating the deployment file:   
  
         apiVersion: apps/v1
         kind: Deployment
         metadata:
           name: web-deployment
             labels:
               app: web
         spec:
           replicas: 3
             selector:
               matchLabels:
                 app: server
             template:
               metadata:
                 name: web-deployment
                 labels:
                   app: server
               spec:
                 containers:
                 - name: deployment-server
                   image: yashu-web:v2
                   imagePullPolicy: IfNotPresent
            
                 volumeMounts:
                 - mountPath: "/var/log/httpd"
                   name: pvc-storage
               volumes:
               - name: pvc-storage
                 persistentVolumeClaim:
                   claimname: pvc-html    

 
 
