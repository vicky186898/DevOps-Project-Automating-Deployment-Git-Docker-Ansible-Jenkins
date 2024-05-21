DevOps Project-1 
You have been hired as a Sr. DevOps Engineer in Abode Software. They want to implement DevOps 
Lifecycle in their company. You have been asked to implement this lifecycle as fast as possible. Abode 
Software is a product-based company, and their product is available on this GitHub link. 
https://github.com/hshar/website.git
Following are the specifications of the lifecycle:  
1. Install the necessary software on the machines using a configuration management tool  
2. Git workflow must be implemented  
3. Code Build should automatically be triggered once a commit is made to master branch or 
    develop branch.  
        a. If a commit is made to master branch, test and push to prod  
        b. If a commit is made to develop branch, just test the product, do not push to prod  
4. The code should be containerized with the help of a Docker file. The Docker file should be 
built every time there is a push to GitHub. Use the following pre-built container for your 
application: hasher/webapp the code should reside in '/var/www/html' 5. The above tasks 
should be defined in a Jenkins Pipeline with the following jobs:  
a. Job1: build  
b. Job2: test  
c. Job3: prod 

Solution :-
Steps: - 
1.  Create an ec2 instance for ansible. 
2. Install ansible on it. 
sudo apt install software-properties-common 
sudo add-apt-repository --yes --update ppa:ansible/ansible 
sudo apt install ansible 
3. Create 2 machines for testing and production. 
4. Generate private key on ansible machine by “ssh-keygen” 
 
5. Now we will go to authorized keys in slave machine and copy the content of id_rsa.pub to authorizedkey.  This process is known as password less ssh.
 
 


6. Go to ansible server and go into /etc/ansible/. 
7. Add identities of other slave EC2.  
a. For this nano hosts 
 
b.  Given the name slave and add ip address of test slave and similarly for production give the 
ip address of production. 
8. . Try to ping both the instances: - “ansible -m ping all” 
 





9. Now let's create a playbook 
 
10. Create master.sh --> Ansible(MAchine1) -----> Jenkins and Java; Docker (master.sh)
 
11. Create slave.sh -->TEST AND PROD  --------> JAva ; Docker (slave.sh)






12. Now run the playbook: - “ansible-playbook software_install.yaml” 
 
13. Check if everything is installed. 
14. Open the Jenkins using ip address of ansible machine on port 8080. 
15. Install all the plugins and setup Jenkins. 
 


16. Now we have to add nodes. 
a. Click on manage jenkins. 
b. Click on nodes. 
c. Add node. 
d. Give name, description. In remote root directory write/home/ubuntu/jenkins/ in launch 
     method select launch agents via ssh. 
e. In host enter the private DNS of the test machine. 
f. Add the credentials, change the kind to “ssh username with private key” 
g. In id write “test”. In username add ubuntu, and give the pem key content and save it. 
 

17. Clone the code i.e Github repo in ansible instance. First Fork the Repo in ur github account
18. Now create Dockerfile. 
 
 
19. Create a new branch named “develop”. 
20. Checkout to develop branch: - git checkout develop 
 
                                    Job1- 
21. Now go to jenkins and create the job1 for develop branch.   
 
 


 


Checking the Job On Test Slave
 
                             

                                        JOB2- Test 
22. In job2 add description as “when master branch is pushed this job will be triggered and since it 
is for testing so it will run on our test slave”
a. in Execute shell add following command
sudo docker build -t master-testimage .
sudo docker run -itd --name mastercontainer -p 82:80 master-testimage

Master Branch is pushed to Github JOB2 is triggered and built Successfully
Added Webhooks .
 
 
                                         
                                                 

                                       JOB3- PROD
23. Now for final Production job create a job3
      and do not select GitHub hook trigger for GITScm polling 
      just add Github URL & Branch Master 
      because Job3 which is a Production Job it will run when JOB2-Test job will be triggered
a). in Execute shell add following command
       sudo docker build -t finalimage .
       sudo docker run -itd --name prodcontainer -p 80:80 finalimage
 ---> Apply & save
                                 
                                                     Go to JOB2- Test 
24. Now go to job2 again and configure it
 a. in Execute shell add following command
sudo docker rm -f $(sudo docker ps -a -q) ---> (when we will trigger the job from master branch this command will remove any previously built containers so the error will not be there saying the container with same name is here)  (82 port no was added for testing purpose)
 
 
Now push Master branch to Github -
1)	It will trigger JOB2 which is test job and 
2)	as it will be triggered another job i.e JOB3-Prod will also be triggered as it is mentioned in Post Build Actions in Job2
 

 
Successful
