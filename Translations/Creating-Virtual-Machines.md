# Creating Virtual Machines

## OBjectives
> Create several standard VMs
> Create advanced VMs

## Task 1: Create a utility virtual machine

### Create a VM

1. Open Cloud Shell or GCloud SDK bash terminal
   
2. Enter the following command to create a VM in US-CENTRAL1-C zone, and no external address:

gcloud beta compute --project=[project-name] instances create instance-1 --zone=us-central1-c --machine-type=n1-standard-1 --subnet=default --no-address

### Replace [project-name] with your own project name. If the other flags are left unchecked, they  will be populated with default values.

## Task 2: Create a Windows virtual machine

1. Create a Windows VM for Windows Server 2016 Datacenter Core. Use the following command and replace [project-name] and [instance-name] with your own names:

gcloud beta compute --project=[project-name]instances create [instance-name] --zone=us-central1-a --machine-type=n1-standard-2 --subnet=default --network-tier=PREMIUM --maintenance-policy=MIGRATE
--tags=http-server,https-server --image=windows-server-2016-dc-core-v20200908 --image-project=windows-cloud --boot-disk-size=100GB --boot-disk-type=pd-ssd --boot-disk-device-name=instance-1 --no-shielded-secure-boot --shielded-vtpm --shielded-integrity-monitoring --reservation-affinity=an

### Other attributes will be left as defaults.

2. Create firewall rules for http using the following command:
   
   gcloud compute --project=qwiklabs-gcp-03-6dc36d2321f3 firewall-rules create default-allow-http --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:80 --source-ranges=0.0.0.0/0 --target-tags=http-server

3. Create firewall rules for http using the following command:
   
   gcloud compute --project=[project-name] firewall-rules create default-allow-https --direction=INGRESS --priority=1000 --network=default --action=ALLOW --rules=tcp:443 --source-ranges=0.0.0.0/0 --target-tags=http-server

## Task3 Create custom VM

1. Use the following command to create a custom VM with 6 vCPU and 32GB of memory:

    gcloud beta compute --project=[project-name] instances create [instance-name] --zone=us-west1-b --machine-type=e2-custom-6-32768

### Other attributes are left as defaults

2. Connect to the custom VM instance using SSH:

    gcloud compute ssh --project [PROJECT_NAME] --zone us-west1-b [INSTANCE_NAME]

3. To see information about unused and used memory and swap space on your custom VM, run the following command:

    free

4. To see details about the RAM installed on your VM, run the following command:

    sudo dmidecode -t 17

5. To verify the number of processors, run the following command:

    nproc

6. To see details about the CPUs installed on your VM, run the following command:

    lscpu

7. To exit the SSH terminal using 'exit' command.

