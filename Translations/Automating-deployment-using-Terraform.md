# Automating the Deployment of Infrastructure Using Terraform

## Objectives
> Create a configuration for an auto mode network
> Create a configuration for a firewall rule
> Create a module for VM instances
> Create and deploy a configuration
> Verify the deployment of a configuration

## Task 1. Set up Terraform and Cloud Shell

### Install Terraform 
- Terraform is already integrated into Cloud Shell.

1. Activate Cloud Shell
2. Confirm that Terraform is installed by running:
   
   terraform version

### Initialise Terraform
1. Create a Terraform directory by running the following command:
   
   mkdir tfinfra

2. Change your current directory to 'tfinfra':
   
   cd tfinfra

3. Create new file named 'provider.tf' within 'tfintra' directory:
   
   nano provider.tf

4. Copy the following code into the open file:
   
   provider "google" {}

5. Run the following code to initialise Terraform:
   
   terraform init

## Task 2. Create mynetwork and its resources

> Create the auto mode network mynetwork along with its firewall rule and two VM instances (mynet_us_vm and mynet_eu_vm).

### Configure mynetwork

1. In the same directory, '/tfinfra/' create new file called 'mynetwork.tf'
   
   nano mynetwork.tf

2. Copy the following code into the 'mynetwork.tf' file:

    # Create the mynetwork network
    resource "google_compute_network" "mynetwork" {
        name = "mynetwork"
        auto_create_subnetworks = true
    }
   
    # Add a firewall rule to allow HTTP, SSH, RDP, and ICMP traffic on mynetwork
    resource "google_compute_firewall"      "mynetwork-allow-http-ssh-rdp-icmp" {
        name = "mynetwork-allow-http-ssh-rdp-icmp"
        network = google_compute_network.mynetwork.self_link
    allow {
        protocol = "tcp"
        ports    = ["22", "80", "3389"]
        }
    allow {
        protocol = "icmp"
        }
    }

3. Save file.
   
### Configure VM instance
1. Create a new folder called 'instance':
   
   mkdir instance

2. Navigate into the 'instance' directory and create new file called 'main.tf' 

   nano instance/main.tf

3.  Then copy and paste the following code:

#declare the variables
variable "instance_name" {}
variable "instance_zone" {}
variable "instance_type" {
  default = "n1-standard-1"
  }
variable "instance_network" {}

resource "google_compute_instance" "vm_instance" {
  name         = "${var.instance_name}"

  #define the zone and machine type of the instance as input variables
  zone         = "${var.instance_zone}"
  machine_type = "${var.instance_type}"

  #define the boot disk to use the Debian 9 OS image
  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-9"
      }
  }

  #defines the network interface
  network_interface {
    network = "${var.instance_network}"
    access_config {
      # Allocate a one-to-one NAT IP to the instance
    }
  }
}

4. Save file.

6. Once again open 'mynetwork.tf' and add the following code:
   
   #Create the mynet-us-vm instance
module "mynet-us-vm" {
  source           = "./instance"
  instance_name    = "mynet-us-vm"
  instance_zone    = "us-central1-a"
  instance_network = google_compute_network.mynetwork.self_link
}

#Create the mynet-eu-vm" instance
module "mynet-eu-vm" {
  source           = "./instance"
  instance_name    = "mynet-eu-vm"
  instance_zone    = "europe-west1-d"
  instance_network = google_compute_network.mynetwork.self_link
}

7. Save file.

8. Ensure you have the following files on your cloud shell:
   - 'provider.tf'
   - 'mynetwork.tf'
   - 'instances/main.tf'

## Create mynetwork and its resources

1. To rewrite the Terraform configuration files to a canonical format and style, run the following command:
   
   terraform fmt

2. To initialize Terraform, run the following command:

    terraform init

3. To create an execution plan, run the following command:

    terraform plan 

4. To apply the desired changes run the following:
   
   terraform apply

5. Type 'yes' when prompted.

The output should look like this:

...
Apply complete! Resources: 4 added, 0 changed, 0 destroyed.
