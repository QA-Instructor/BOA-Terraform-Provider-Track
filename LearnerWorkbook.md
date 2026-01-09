## TF00

# Lab TF00 - Environment Setup

## Lab Objectives
Configure access to a cloud environment in which to complete this course's labs

## High-Level Steps

## Detailed Steps
1.  Ensure you have created a qa.qwiklabs account via https://qa.qwiklabs.com, and notified the instructor of your username

2.  Access the classroom for the course and launch the available lab by selecting the link and then clicking on Start Lab.

3. Note your credential information is available here (you may need to scroll down) and can be copied onto your clipboard using the copy icon. Make a note of your project ID

4. Once initialized, access your GCP project via the Console link. Ensure that you open the console in a new, inprivate/incognito window which is NOT signed into ANY other google account

5.  A new session window will open. Log in using the Username and password provided to access your Google cloud project Welcome screen. Acknowledge any license agreement prompts

6.  Open the cloud shell

7. Whilst you have a cloud shell open, run the following gcloud command:
```bash
gcloud compute project-info add-metadata --metadata=enable-oslogin=false
```
This will enable SSH access via custom SSH keys, for later labs

8. Expand the cloudshell panel into its own window, and close cloudshell in the existing window, so that you have one tab for your GCP console, and one for cloudshell

9. Switch to Cloud Shell tab. This will be refered to as your IDE.

10. Open the editor

11. Close the terminal

12. Use Setting to configure a theme of your choice

13. Open a terminal session within Cloud Shell Editor using the hamburger menu > Terminal > New Terminal, or by pressing ctrl+shift+c

14. Upload the provided Lab files to your cloudshell instance home directory, in a folder called 'Labs'

15. From the extensions menu in the editor, add the HashiCorp Terraform extension

16. You can close any qa.qwiklabs tabs in your browser, leaving you with an IDE tab and a Dashboard/Console tab

## TF01

# Lab TF01 - A Sample Terraform Deployment

## Lab Objectives
Deploy, modify and delete a container resource using Terraform

## Learning Outcomes
By the end of this lab, you will have:
* Been introduced to the Terraform workflow: **init > plan > apply**
* Modified a Terraform configuration and applied that new configuration
* Been introduced to the concept of 'providers', 'resources' and 'state'

## High-Level Steps
* Review an existing Terraform file

## Detailed Steps

Before you begin

1.  Ensure you have completed TF00 before attempting this lab.
2.  In the IDE terminal window, enter the following command

```bash
cd ~/Labs/TF01
```

3.  This shifts your current working directory to Labs/lab01. Ensure all commands are executed in this directory

4.  Close any existing files and use the Explorer pane to open main.tf in the Labs/TF01 folder

### Review the sample terraform configuration file

1.  In the IDE, open Labs/TF01/main.tf

#### Breaking It Down

##### Defining the Terraform Block

The first block is the terraform code block. It specifies the provider(s) required for the configuration, here it is Docker, indicating that you want to use Terraform to manage Docker resources. The source section on line 4 identifies the Docker provider component to be downloaded during initialization, and line 5 indicates which version is required.

Edit the version number to be "3.6.2"

##### Initializing the Provider

The provider block details parameters to be used when initializing the Docker provider. The configuration needed here is dependent upon the platform on which Docker is running. You are using the GCP Cloud Shell Editor which runs on Linux which is a Unix derived operating system.

##### Pulling the Docker Image

This is one of two blocks of code that indicate the resources you want Terraform to instruct Docker to create. Here you are defining a docker image that needs to be pulled from Docker hub. Line 16 identifies the image you want, the latest version of "httpd", an Apache webserver.

##### Creating the Docker Container

This is the second block of code that indicates the resources you want Terraform to instruct Docker to create. Here you are defining that you want docker to create a container called "web_server" (line 22) and you want it to
be accessible using ports 80 and 88 (lines 24-25). A docker container is
created from a docker image. Line 21 indicates that the image to be used
to create this container is the one you ask Docker to pull from Docker hub
in lines 15-17. This is an example of one resource code block referencing
another and it also indicates to Terraform that it must wait for Docker to
download the image before it can ask it to create the container using it.
This is known as an implicit dependency; the container depends upon the
image existing.

### Run Terraform & Test

1.  Run `terraform init` ensuring you are in the correct working directory

2.  Run `terraform plan` and review what will be created

3.  Run `terraform apply`  typing **yes** when prompted. Review output in the CLI

4.  Run `docker images` in CLI. This should show the downloaded **httpd** image

5.  Run `docker ps` to list your running containers

6.  Note the last few characters of your container ID

7.  Type `curl http://127.0.0.1:88`. View the "It Works" response that is standard with an Apache web server (index.html)

8.  You will now modify the deployment by changing the external port number from *88* to *8088*.  

Some resource changes can be applied to the existing instance of the resource, whilst others require the destruction of the original resource and the creation of a replacement. Let's see whether a port change modifies or recreates this resource.

9.  Return to the **main.tf** and change the external port to 8088.

10. Save the file

11. Run `terraform plan` and review the changes

12. Run `terraform apply`, typing **yes** when prompted

13. Type `curl http://127.0.0.1:8088`. This should return a success "it works"

14. Type `curl http://127.0.0.1:88`. An error message is returned.

15. Run `docker ps`

16. Note that the name of the container is the same, but the ID has changed

Terraform destroyed the original container and deployed a replacement. This is a crucial point to be aware of as you progress through the course. Whether you 'update' or 'replace' a resource depends upon the resource itself and the changes you make.

### Examine the state file
1.  When `terraform apply` is run for the first time, a persistent record of resources Terraform deploys and manages is created in the form of a state file: *terraform.tfstate*. This file is updated whenever you ask Terraform to create, change, or destroy resources thereafter. By default this file will be in your working directory. Click on it now to examine its contents.

### Lab Clean-up

1.  Run `terraform destroy` - review the output and type **yes**

2.  Run `docker ps` to confirm your container has been deleted

3.  Run `docker images` and confirm your image has been deleted.

4.  Revisit the state file and view its now reduced content

## TF02
# Lab TF02 - Create a GCE instance with Terraform

## Lab Objectives
In this lab, you will:

*  Review the Terraform documentation for the GCP Provider
*  Build a simple main.tf configuration file based on example code
*  Deploy a GCE instance on Google Cloud
*  Test and modify the deployment
*  Destroy the deployment

Teaching Points
This lab takes you through the writing of code to create a GCE instance in Google
Cloud.

Before you begin
1.  Ensure you have completed TF00 before attempting this lab.

2.  In the IDE terminal pane, enter the following commands

```bash
cd ~/Labs/TF02
```

3.  This shifts your current working directory to Labs/TF02. Ensure all commands are executed in this directory

4.  Close any open files and use the Explorer pane to navigate to and open the
empty main.tf file in Labs/TF02

### Solution
The solution to this lab can be found in Labs/solutions/TF02. Try to use
this only as a last resort if you are struggling to complete the step-by-step
processes

### Review the documentation and create a configuration file to deploy a GCE instance
1. Review the Terraform Google Provider documentation:
https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_instance

2.  Click `Use Provider`

3.  Copy the code block into the empty main.tf file in the Labs/TF02 folder. For convenience, the code is listed below:

```terraform
terraform {
  required_providers {
    google = {
      source = "hashicorp/google"
      version = "6.17.0"
    }
  }
}

provider "google" {
  _configuration options_
}
```

4.  Click on the 'Copy' icon within the google_compute_instance example usage
and paste this into main.tf beneath your current entries.

5.  For this simple lab you will not being using a service account, so *remove* the
**google_service_account** block

6.  Within the **google_compute_instance** resource block, *remove* the **service_account** sub-block. Also *remove* the **scratch_disk** sub-block, the **metadata** and the **metadata_startup_script** parameters

7.  You should now be left with:
```terraform
resource "google_compute_instance" "default" {
  name         = "my-instance"
  machine_type = "n2-standard-2"
  zone         = "us-central1-a"

  tags = ["foo", "bar"]

  boot_disk {
    initialize_params {
      image = "debian-cloud/debian-11"
      labels = {
        my_label = "value"
      }
    }
  }

  network_interface {
    network = "default"

    access_config {
      // Ephemeral public IP
    }
  }
}
```

### Run Terraform & Test

1.  Run `terraform init` ensuring you are in the correct working directory.

2.  Run `terraform plan`

3.  An error will be returned indicating that you have not specified the project into
which you want to provision the resource. This can be entered either as part of
the provider block in which case it will apply to all resources, or on a per-resource basis as part of the resource block itself.

4.  Update your provider block to include your project.

```terraform
provider "google" {
  project = "<your project id here>"
  _configuration options_
}
```

5.  Run `terraform plan` and review what will be created

6.  Run `terraform apply`, typing **yes** when prompted. Review the output in the CLI.
Notice that a persistent **terraform.tfstate** file now exists in your directory.

7.  Select the **terraform.tfstate** file to view its contents

8.  Scroll down around **line 63** to view the unique **id** of the compute instance

9.  Switch to the Console and search for my-instance in GCE. Click on the DETAILS
tab to view the Instance ID, verifying that this is the instance terraform
deployed

10. Switch back to the IDE and in **main.tf** within the instance resource block, change the size
from "ns-standard-2" to  "e2-small" to ascertain if this change invokes an in-place modification or a deletion/re-creation.

11. Run `terraform plan` Review the output and note that this is an 'update in-place'

12. Run `terraform apply` typing `yes` when prompted.

13. An error is shown regarding the updating of a running instance.

14. Modify your **main.tf** and include the following line under the zone setting
**allow_stopping_for_update = true**

15. Run `terraform apply` typing `yes` when prompted.

16. As the modification is made, use the console to verify that the id of the
instance is unchanged. The update may take several minutes. Refresh the
display periodically. Scroll down the DETAILS of the instance to verify its
machine type has changed to e2-small

17. Switch back to the IDE and within the boot_disk sub-block, change the image
from "debian-cloud/debian-11" to **"ubuntu-minimal-2404-noble-amd64-v20250725"**

18. Run `terraform plan` Review the output and note that this is a destroy and
replace.

19. Run `terraform apply` typing `yes` when prompted.

20. Switch to the console and watch (periodically refresh) as the old instance is
first destroyed, and a new one is created with a new id

### Destroy your deployment

1.  Switch back to the IDE and run `terraform destroy`. Review the output and type `yes`

2.  Switch to the Console and verify the deletion of your instance

3.  In the IDE, note how the content of the **terraform.tfstate** file is reduced.

## TF03
# Lab TF03 - VPC deployment using Terraform

## Overview

1.  This lab will take you through coding a multi-component cloud deployment
into Google Cloud using Terraform. You will deploy a relatively simple VPC, but
even this has several components that need either to exist already or be
created as part of the deployment.

2.  Elements required in this lab are the VPC itself, Subnets, Routing, NAT Service
and Firewall Rules. This lab breaks the deployment into phases, each a
separate task, to demonstrate component interdependencies

## Lab Objectives
In this lab, you will:

*  Deploy a custom VPC
*  In the us-central1 region, create a Public subnet using CIDR 10.0.1.0/24 and
a Private subnet using CIDR 10.0.2.0/24

*  Create a Router with a NAT service
*  Create a GCE instance on the public subnet with an internal and external IP
and one on the private subnet with just an internal IP

*  Restrict inbound traffic to the Public Subnet to only SSH traffic from any
other source

*  Restrict inbound traffic to the Private subnet, allowing any traffic from the
Public subnet only

*  Allow unrestricted outbound traffic
*  Test the configuration by establishing an SSH session to the instance on the
public subnet and then from it, test communication to the instance on the
private subnet.

## Before you begin

1.  Ensure you have completed TF00 before attempting this lab.

2.  In the IDE terminal pane, enter the following commands:

```bash
cd ~/Labs/TF03
```

3.  This shifts your current working directory to Labs/TF03. Ensure all
commands are executed in this directory

4.  Close any open files and use the Explorer pane to navigate to and open the
*empty* **main.tf** file in Labs/TF03

## Solution
The solution to this lab can be found in Labs/solutions/lab03. Try to use
this only as a last resort if you are struggling to complete the step-by-step
processes

### Create the basic VPC
References:
* https://registry.terraform.io/providers/hashicorp/google/6.17.0
* https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_network
* https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_subnetwork

Task 1: Provision a network and 2 subnets

Try it yourself...

The aim of this task is to:

1.  Create a VPC resource `google_compute_network.lab_vpc` named **lab-vpc**

2.  On this VPC, create a subnet resource `google_compute_subnetwork.public_subnet` in **us-central1** named **public-subnet** using CIDR 10.0.1.0\24

3.  Create a second subnet resource, `google_compute_subnetwork.private_subnet`, named **private-subnet** using CIDR 10.0.2.0\24

4.  If you feel comfortable doing so, then attempt to complete this task without referencing the step-by-step instructions below. You can verify your attempt by comparing your code with the Task 1 section of the solution code.

5.  Note: Provider version 6.17.0 should be used throughout this lab.

Step by Step

1.  Review Terraform Google Provider documentation:

https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs

2.  Click `Use Provider`

3.  Copy the code block into main.tf. For convenience, the code is listed below…

```terraform
terraform {
  required_providers {
    google = {
      source = "hashicorp/google"

      version = "6.17.0"
    }
  }
}

provider "google" {
  _configuration options_
}
```

4.  From within the documentation, you can see that the provider configuration options typically include the project and region. Copy the sample code and overwrite the empty provider sub-block in main.tf:

```terraform
provider "google" {
  project     = "<your project id here>"
  region      = "us-central1"
}
```

5.  Update the project value to reflect your lab project id

6.  Review the documentation for creating a VPC;

https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_network

7.  Copy the Example Usage - Network Basic sample code into main.tf

```terraform
resource "google_compute_network" "vpc_network" {
  name = "vpc-network"
}
```

8.  Change the resource block identifier from vpc_network to lab_vpc and the resource name from vpc-network to lab-vpc

9.  You will only be creating selective subnets on the VPC and therefore need to toggle off the default behaviour which is to create a subnet in every region. Add the argument auto_create_subnetworks and set its value to *false*

10. Your modified resource block should now be:

```terraform
resource "google_compute_network" "lab_vpc" {
    name = "lab-vpc"
    auto_create_subnetworks = false
}
```

11. You now need to add 2 subnets to this VPC. Review the documentation for sample code on the google_compute_subnetwork resource

https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_subnetwork

12. You need 2 subnets, so the simplest option is to create 2 copies of this resource
block. Add the first example code block into **main.tf**.

```terraform
resource "google_compute_subnetwork" "network-with-private-secondary-
ip-ranges" {
  name = "test-subnetwork"
  ip_cidr_range = "10.2.0.0/16"
  region        = "us-central1"
  network       = google_compute_network.custom-test.id
  secondary_ip_range {
    range_name    = "tf-test-secondary-range-update1"
    ip_cidr_range = "192.168.10.0/24"
  }
}
```

13. Change the resource block identifier from network-with-private-secondary-ip-ranges to public_subnet

14. Change the resource name from test-subnetwork to public-subnet

15. Change the cidr block from 10.2.0.0/16 to 10.0.1.0/24

16. Change the network argument value from google_compute_network.custom-test.id to google_compute_network.lab_vpc.id

This references the vpc you defined earlier and is an example of an implicit
dependency. Terraform will know to create the vpc before attempting to create
this subnet on it.

17. Finally, delete the secondary_ip_range sub-block.

18. The modified block should now be as follows:

```terraform
resource "google_compute_subnetwork" "public_subnet" {
  name = "public-subnet"
  ip_cidr_range = "10.0.1.0/24"
  region        = "us-central1"
  network = google_compute_network.lab_vpc.id
}
```

19. Duplicate this resource block

20. Change the resource block identifier from public_subnet to private_subnet

21. Change the resource name from public-subnet to private-subnet

22. Change the cidr block from 10.0.1.0/24 to 10.0.2.0/24

23. The modified block should now be as follows:

```terraform
resource "google_compute_subnetwork" "private_subnet" {
  name = "private-subnet"
  ip_cidr_range = "10.0.2.0/24"
  region        = "us-central1"
  network = google_compute_network.lab_vpc.id
}
```

24. Save main.tf and run `terraform init`

25. Run `terraform plan` Note any errors and fix if appropriate. Refer to the Task 1 block in the solution code, if necessary

26. Run `terraform apply`, entering **yes** when prompted. 3 resources should be added.

27. Run `terraform state list` and verify the resources exist

28. Switch to the Console

29. Use the Search bar to search for VPC networks

30. Verify you see lab-vpc, the VPC you have just created

31. Select lab-vpc and verify you see the 2 subnets


Task 2: Provision a Cloud Router and NAT Service
References

https://registry.terraform.io/providers/hashicorp/google/latest/docs/resources/compute_router_nat

Try it yourself...

1.  If you feel comfortable doing so, attempt to complete this task without
referencing the step-by-step instructions below. You can verify your attempt by
comparing your code with the Task 2 block in the solution code

2.  Important. If you attempted the previous task yourself, then your code, whilst
hopefully achieving the objectives specified, may vary slightly from the solution
provided at the end of this lab. You are encouraged to continue attempting
each task without precise guidance if you feel comfortable doing so.  An alternative approach is to 'reset' your code at the start of each task to align it with the solution code prior to moving forward.

3.  **Optional** reset:

    a.  Destroy any currently deployed resources  
    b.  Clear the contents of your current main.tf file  
    c.  Copy the Task 1 block from the solution main.tf into your main.tf  
    d.  Update the project id with your project id and deploy the resources  

4.  The aim of this task is to:

    a.  Create a Cloud Router resource `google_compute_router.lab_router`
    named **lab-router** and associate it with
    `google_compute_network.lab_vpc`  
    b.  Create a router nat resource `google_compute_router_nat.lab_nat`
    named **lab-nat**, associate it with `google_compute_network.lab_vpc` and
    `google_compute_subnetwork.private_subnet`  

### Step by Step

1.  Review the Terraform Registry documentation regarding the creation of a
Google Cloud resources google_compute_router and
google_compute_router_nat at
https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_router_nat

2.  Copy the code examples relating to google_compute_router and
google_compute_router_nat from Example Usage - Router NAT Basic, into
main.tf

3.  Change the identifier of the resource block google_compute_router from
router to lab_router

4.  Change the name of the google_compute_router resource from my-router to
lab-router

5.  Change the google_compute_router region from
google_compute_subnetwork.subnet.region to
google_compute_subnetwork.private_subnet.region
This references the private subnet you defined earlier and is another example
of an implicit dependency. Terraform will know the subnet has to exist before
attempting to create this router in the same region

6.  Change the google_compute_router network from
google_compute_network.net.id to google_compute_network.lab_vpc.id

7.  Delete the bgp sub-block

8.  Change the name of the resource block google_compute_router_nat from nat
to lab-nat

9.  Change the google_compute_router_nat name from my-router-nat to lab-nat

10. Change the google_compute_router_nat router from
google_compute_router.router.name to
google_compute_router.lab_router.name

11. Change the google_compute_router_nat region from
google_compute_router.router.region to
google_compute_router.lab_router.region

12. Change the source_subnetwork_ip_ranges_to_nat from
ALL_SUBNETWORKS_ALL_IP_RANGES to LIST_OF_SUBNETWORKS

13. Delete the log_config sub-block and replace it with the code below to apply
use of the nat gateway to the private subnet
```terraform
subnetwork {
    name = google_compute_subnetwork.private_subnet.id
    source_ip_ranges_to_nat = ["ALL_IP_RANGES"]
}
```

14. Confirm the modified blocks are as shown below
```terraform
resource "google_compute_router" "lab_router" {
   name = "lab-router"
  network = google_compute_network.lab_vpc.id
}

resource "google_compute_router_nat" "lab_nat" {
  name = "lab-nat"
  router = google_compute_router.lab_router.name
  region = google_compute_router.lab_router.region
  nat_ip_allocate_option = "AUTO_ONLY"
  source_subnetwork_ip_ranges_to_nat = "LIST_OF_SUBNETWORKS"

  subnetwork {
    name = google_compute_subnetwork.private_subnet.id
    source_ip_ranges_to_nat = ["ALL_IP_RANGES"]
  }
}
```

15. Run `terraform plan`. Note any errors and fix if appropriate. Refer to the Task 2
section of the solution code, if necessary.

16. Run `terraform apply`, entering **yes** when prompted. 2 additional
resources are created.

17. Run `terraform state list` and verify the following 5 resources exist: lab_vpc, lab_router, lab_nat, private_subnet, public_subnet

18. Switch to the Console

19. Search for Cloud NAT and verify the existence of the new NAT Gateway and Cloud Router. Drill into the nat gateway to verify that the gateway will provide
Cloud NAT mapping to the private-subnet only.

20. As well as network connectivity, you must also consider firewall rules, needed
to permit inbound and outbound traffic flows. Every VPC has default rules that
deny all inbound traffic and allow all outbound traffic.

21. In Task 3 you will enhance your code to include appropriate firewall rules to enable ingress to your VPC.

Task 3. Provision Routing tables and Firewall Rules

References:
https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_firewall

Try it yourself...

1.  If you feel comfortable doing so, attempt to complete this task without
referencing the step-by-step instructions below. You can verify your attempt by
comparing your code with the Task 3 section of the solution code.

2.  Important:  If you attempted a previous task yourself, then your code, whilst
hopefully achieving the objectives specified, may vary slightly from the solution
provided. You are encouraged to continue attempting
each task without precise guidance if you feel comfortable doing so.  An
alternative approach is to 'reset' your code at the start of each task to align it
with the solution code, prior to moving forward.

3.  **Optional** Reset:

      a.  Destroy any currently deployed resources  
      b.  Clear the contents of your current main.tf file  
      c.  Copy the Task 1 and Task 2 blocks from the solution main.tf into your
      main.tf  
      d.  Update the project id with your project id and deploy the resources  

4.  The aim of this task is to:

a.  Create a Firewall Rule **lab-private-fw-rule** that will be applied to
instances on the private subnet, with a rule that allows unrestricted
inbound traffic from public-subnet instances. Note: In production you
would only open selective ports between the public and private subnets.  

b.  Use `source` tags **pub-subnet-vm** and `target` tags **priv-subnet-vm** as these
will be used by instances on the public and private subnets, respectively.  

c.  Create a second Firewall Rule **lab-public-fw-rule** that will be applied to
instances on the public subnet, with a rule that allows TCP port 22
inbound traffic from any source IP. Note: In production you would be
more selective regarding source ranges.  

d.  Use `target` tag **pub-subnet-vm** as this will be used by instances  on the
public subnets.

### Step by Step

1.  Review the Terraform Registry documentation regarding the creation of a

Google Cloud Firewall Rule resource google_compute_firewall
https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_firewall

2.  Copy the Example Usage - Firewall Basic code block into main.tf (do not copy
the google_compute_network resource block as you have already defined your
network)

```terraform
resource "google_compute_firewall" "default" {
  name    = "test-firewall"
  network = google_compute_network.default.name

  allow {
    protocol = "icmp"
  }
  allow {
    protocol = "tcp"
    ports    = ["80", "8080", "1000-2000"]
  }
  source_tags = ["web"]
}
```

3.  Change the resource block identifier to **lab_private_fw_rule**

4.  Change the resource name to **lab-private-firewall**

5.  Change the network value to **google_compute_network.lab_vpc.name**

6.  Remove the allow icmp sub-block

7.  Change the remaining allow sub-block to allow "all" protocols across all ports
(delete the port argument)

8.  Add a `source_tags` argument with a list including **pub-subnet-vm**

9.  Add a `target_tags` argument with a list including **priv_subnet_vm**

10. The modified block should now be as shown below…

```terraform
resource "google_compute_firewall" "lab_private_fw_rule" {
  name    = "lab-private-firewall"
  network = google_compute_network.lab_vpc.name

  allow {
    protocol = "all"
  }
  source_tags = ["pub-subnet-vm"]
  target_tags = ["priv-subnet-vm"]
}
```
11. Duplicate the entire google_compute_firewall resource block and paste a copy
into the end of main.tf

12. Change the new resource block identifier to **lab_public_fw_rule**

13. Change the resource name to **lab_public_firewall**

14. Change the protocol argument value from "all" to "tcp" and add a `ports` argument with list value of "22"

15. Replace the source_tags argument with a `source_ranges` argument containing ["0.0.0.0/0"]

16. Change the `target_tags` from priv-subnet-vm to **pub-subnet-vm**

17. The modified block should now be as shown:

```terraform
resource "google_compute_firewall" "lab_public_fw_rule" {
  name    = "lab-public-firewall"
  network = google_compute_network.lab_vpc.name

 allow {
    protocol = "tcp"

    ports    = ["22"]
  }
  source_ranges =["0.0.0.0/0"]
  target_tags = ["pub-subnet-vm"]
}
```

18. Save main.tf

19. Run `terraform plan` Note any errors and fix if appropriate. Refer to Task 3 block in the solution code, if necessary.

20. Run `terraform apply`, entering **yes** when prompted. 2 new resources are
created.

21. Run `terraform state list` and verify there are now 7 resources including **lab-private-fw-rule** and **lab-public-fw-rule**

22. Switch to the Console

23. Navigate to `VPC Network -> Firewall`, and verify the existence of
the new firewall rules

24. In Task 4 you will create GCE instances on each subnet to test the VPC network
connectivity and Firewall rules.

Task 4. Create GCE Instances

1.  Important If you attempted a previous task yourself, then your code, whilst
hopefully achieving the objectives specified, may vary slightly from the solution
provided at the end of this lab. You are encouraged to continue attempting
each task without precise guidance if you feel comfortable doing so.  An
alternative approach is to 'reset' your code at the start of each task to align it
with the solution code, prior to moving forward.

2.  **Optional** Reset:

    a.  Ensure you have destroyed any resources

    b.  Clear the contents of your current main.tf file

    c.  Scroll down to the Solution section at the end of this document and copy
    the Task1, 2 and 3 code blocks  into main.tf

    d.  Update the project id in the provider block and save main.tf

3.  During this task you are required to:

* Create 2 EC2 Instances of size `e2-small` based on `debian-cloud/debian-11` public image

*  Both instances should be in availability zone `us-central1-a`

* Name the first instance `pub-vm`

* Place  `pub-vm` on the Public Subnet

* Allocate PubVM a dynamic public IP address

* Name the second instance `priv-vm`

* Place `priv-vm` on the Private Subnet

* Do not allocate a public IP address to  `priv-vm`
* Add `tags` to each machine so it has the appropriate firewall rule applied

---


4.  Attempt this task without step-by-step guidance. Refer to the Task 4 code block
in the solution code, if needed.

5.  Save and `terraform apply` the now complete main.tf

6.  Do not destroy the deployment as you will move onto testing next.

7.  Use the following boilerplate code to achieve these objectives:

```terraform
resource "google_compute_instance" "" {
  name = ""
  machine_type = ""
  zone = ""
  allow_stopping_for_update = true
  tags = [""]
  boot_disk {
    initialize_params {
      image = ""
    }
  }
  network_interface {
    subnetwork =
    access_config {

    }
  }
}
```

Task 5. Testing

1.  Using the GCP Dashboard, note the internal IP address of `priv-vm`

2.  Click on SSH against `pub-vm` to connect using SSH-in-browser

3.  Ping `priv-vm` from within the PubVM ssh session:
```shell
ping -c 3 {private IP of PrivVM}
```

4.  Pinging `priv-vm` from `pub-vm` should succeed because there is a route from the
public subnet to the private subnet and the firewall rules allow traffic from the
public subnet to the private subnet.

5.  Modify the `lab-private-fw-rule` firewall rule with an erroneous source tag
entry, changing it from `pub-subnet-vm` to `pub-subnet-vm1`

6.  Apply the change with `terraform apply`

7.  Switch back to the ssh session and repeat the ping command.

8.  This ping attempt should fail

9.  Undo the change made to main.tf, resetting the tag to `pub-subnet-vm`

10. `Apply` the change

11. Repeat the ping attempt

12. The pings should succeed.

13. Type exit to close the SSH session to `pub-vm`

Task 6. Destroy your deployment

1.  Switch back to the IDE and run `terraform destroy` review the output and type
`yes`

2.  Switch to the Console and verify the deletion of your resources

## TF04
# Lab TF04 - Variables and Workspaces

# Lab Objectives
In this lab you will:

*  Deploy resources using a hard-coded terraform file
*  Utilize variables to make your code reuseable
*  Implement overrides using the command line and tfvars files
*  Utilize terraform workspaces to allow multiple simultaneous deployments

Teaching Points
When writing terraform code, it is instinctive to supply explicit values when they
are required, for example name = "michael". This can make the code very static
though, requiring manual changes of these values each time a modification is
needed.  A better practice is to use 'placeholders', variables, to which values can
be passed at runtime, for example `name = var.username`. 

Passing values to these 
variables can be achieved using variable file default values, tfvars files, command
line supplied or by being prompted. 

By default, all changes made to your
configuration will be tracked by a single state file. Workspaces allow multiple state
files to exist simultaneously so that the same base code, provided with unique
variable values, can be deployed. This makes your code flexible and reuseable.

Before you begin

1.  Ensure you have completed TF00 before attempting this lab.

2.  In the IDE terminal pane, enter the following command

```bash
cd ~/Labs/TF04
```

3.  This shifts your current working directory to Labs/TF04. Ensure all
commands are executed in this directory

4.  Close any open files and use the Explorer pane to navigate to and open the
pre-configured main.tf file in TF04.

Solution
There is no solution code for this lab as it involves multiple deployment phases.
Reach out to your instructor if you encounter issues.

Task 1 - Review provisioned terraform files
1.  Review the provisioned files in TF04:

* **main.tf** This is a hardcoded deployment of an `e2-small` GCE instance running `debian-11` in `us-central1-a`. This represents the type of resource you will
create in this lab. In production there would likely be many other resources
defined here. There is also an output block (lines 32-41) which displays
information about the deployment, including the workspace.

* **variables.tf** All content is currently commented out

* **terraform.tfvars** All content is currently commented out

* **dev.tfvars** Variable values that will be used in a new workspace

* **prod.tfvars** Variable values that will be used in a new workspace

Task 2 - Provision resources using these hardcoded parameter values
1.  Ensure you have navigated to the `Labs\TF04` folder

2.  Update line 11 of **main.tf** with *your* project id

3.  Run `terraform init`

4.  Run `terraform plan`

5.  Review the plan output

6.  Run `terraform apply` followed by **yes**

7.  Switch to the Console and verify the creation of the GCE instance in us-central1-a

8.  Note the creation of a state file in the root folder

9.  Takeaway: Hardcoded parameter values will always be used if they exist. This
can make the code inflexible and static

Task 3 - Substitute hardcoded parameter values with variables
1.  In **main.tf** replace the hardcoded zone `"us-central1-a"` with `var.vm_location` (no quotes)

2.  Replace the hardcoded image `"debian-cloud/debian-11"` with `var.vm_image`

3.  Replace the hardcoded instance machine_type `"e2_small"` with `var.vm_size`

4.  Save the changes.

5.  Run `terraform plan` and review the 3 errors

6.  Takeaway: If variables are referenced in your code, then they must be declared.

7.  In **variables.tf** uncomment all lines except line 10 which provides a default values for the vm_size variable.
8.  Switch to **main.tf**, uncomment line 39

9.  Run `terraform plan`

10. When prompted, enter `e2-small` as the instance size

11. The planning completes using the variable default values if they exist and prompted for when there is no default value. Given that these values are the same as the old static values, the planning phase shows that there are no changes needed

12. In **variables.tf**, uncomment line 10 and save the file

13. Run `terraform plan`

14. The planning completes using all variable values drawn from the variables file.
Given that these values are the same as the old static values, the planning
phase shows that there are no changes needed.

15. Destroy your deployment using `terraform destroy` followed by **yes**

16. Switch to the console and confirm the deletion of the instance in us-central1-a

17. Takeaway: If a variable is declared but no value has been assigned, then you
are prompted for a value. If a variable is declared with a default value then this
value will be used unless overridden.

Task 4 - Overriding variable values at the command prompt
1.  Enter the following command
```bash
terraform plan -var="vm_size=e2.micro"
```
2.  Takeaway: Supplying variable values at the command prompt using -var="  "
has the highest priority and overrides values supplied anywhere else. In this
case e2.micro is used as the machine_type value. The zone and image values
are drawn from the variables file default values. Do not apply this deployment.

Task 5 - Override variable values using terraform.tfvars

1.  Uncomment *lines 1 and 2* in **terraform.tfvars**. This file now supplies values for 2
variables, values that conflict with those in **variables.tf**

2.  Run `terraform plan`
Notice that the size value e2.micro and the location value us-central1-b are
drawn from terraform.tfvars, overriding the default size in the  variables file.
The image value is still drawn from variables.tf

3.  Uncomment *line 3* in **terraform.tfvars**. This file now supplies values for all
variables, values that conflict with, and therefore override, those in variables.tf.

4.  Run `terraform plan`

5.  Run `terraform apply` followed by **yes**
6.  Switch to the console and verify the creation of the e2.micro instance in us-central1-b

7.  Takeaway: If terraform.tfvars exists and supplies all variable values, there is no
rationale for specifying default values in the variables file as these will always
be overridden. It is not uncommon therefore to define the variables using a
variables file without declaring any default values.

Task 6 - Implement Terraform Workspaces

1.  Changing deployment parameters will affect the current deployment.
Workspaces allow multiple deployments, using the same base code but with
different parameters, to exist simultaneously, each with its own state file. If no
new workspaces are created then your deployment is in the default workspace
which always exists and cannot be deleted.

2.  Create two workspaces:  "development" and "production"…

```bash
terraform workspace new development
terraform workspace new production
terraform workspace list
```

3.  The * indicates your current workspace is now production. The default
workspace always exists, and this is where your current deployment of an e2-
micro GCE instance, running ubuntu in us-central1-b is tracked by the state file
terraform.tfstate in the root folder.

4.  Note the creation of a new folder **"terraform.tfstate.d"** with an empty
subfolder for each of the new workspaces

5.  Uncomment all lines in **prod.tfvars** and **dev.tfvars**

6.  Run `terraform plan`

7.  The name given to the vm has been crafted to reflect the namespace, but all
other variable values are still drawn from terraform.tfvars

8.  Run `terraform plan --var-file=prod.tfvars`

9.  tfvar files other than terraform.tfvars and <name>.auto.tfvars are not
referenced unless specified at the command line. Here you have specified
inclusion of **prod.tfvars** which contains location value "europe-west2-b" and
size value "n2-standard-4". The image values is not defined in prod.tfvars and is therefore drawn from terraform.tfvars.

10. Notice that the plan will not destroy any resources. Recall that you currently
have a GCE instance deployed in us-central1-b. This is in the default
workspace and will not be affected as you are now in the production
workspace.

11. Run `terraform apply --var-file=prod.tfvars` followed by **yes**

12. Switch to the console and verify the creation of the n2-standard-4 instance in
europe-west2-b. Note that the e2-micro instance created in the default
workspace still exists.

NOTE: If you receive an error "No default subnetwork..." change the vm_location to "europe-west3-b" or another european location that does contain a default network.

13. In the IDE, expand the terraform.tfstate.d folder and verify the creation of a
new state file for the production workspace.

14. Move to the development work space: `terraform workspace select development`

15. Run `terraform plan --var-file=dev.tfvars`

16. Here you have specified inclusion of **dev.tfvars** which contains location value
`us-east1-d` and size value `e2-medium`. If there are conflicts, values from
**dev.tfvars** override default variable values and values in the terraform.tfvars
file. The plan shows that the location and size values are therefore drawn from
the dev.tfvars file. The image values is not defined in dev.tfvars and is
therefore drawn from **terraform.tfvars**

17. Again, notice that the plan will not destroy any resources. Recall that you now
have GCE instances deployed in us-central1-b and europe-west2-b. These are
in the default and production workspaces respectively and will not be affected
as you are now in the development workspace.
 
18. Run `terraform apply --var-file=dev.tfvars` followed by **yes**

19. Switch to the console and verify the creation of the e2-medium development
instances in us-east1-d alongside the default and production instances.

20. In the IDE, expand the **terraform.tfstate.d** folder and verify the creation of a
new state file for the development workspace.

Task 7 - Lab Clean-up

1.  When deleting resources in workspaces, always pay close attention to the
workspace you are currently working in. Use `terraform workspace show` to
determine your current workspace

2.  Destroy the resources in the current workspace (dev) using
```bash
terraform destroy --var-file=dev.tfvars
```
Note that the same tfvars file must be specified when performing the destroy action that was supploed during the apply stage.

3.  Enter **yes** when prompted

4.  Switch to the Console to confirm the deletion of the  development instance in
us-east1-d

5.  The current workspace cannot be deleted. Move to the production workspace
and delete the development workspace

```bash
terraform workspace select production
terraform workspace delete development
```

6.  Destroy the resources in the current workspace (production) using
```bash
terraform destroy --var-file=prod.tfvars
```

7.  Enter **yes** when prompted

8.  Switch to the console to confirm the deletion of the production instance in europe-west2-b

9.  The current workspace cannot be deleted. Move to the default workspace and
delete the production workspace:

```bash
terraform workspace select default
terraform workspace delete production
```
10. Verify the deletion of the workspaces, leaving just the default workspace. This
cannot be deleted..

`terraform workspace list`

11. Note that the workspace directories are deleted along with the workspaces
themselves

12. Destroy the resources in the current workspace (default) using `terraform
destroy`

13. Enter **yes** when prompted

14. Switch to the console to confirm the deletion of the default instance in us-central1-b

## TF05
# Lab TF05 - Managing and Migrating State

# Lab Objectives
1.  In this lab you will:

a. Deploy resources using the provided code, creating a statefile on your local device.

b. Reconfigure the code to migrate the statefile to a central GCS bucket.

c. Verify the state migration

Key Learning Points

This lab will use terraform to deploy a network stack into Google Cloud. This will
create a local backend (Statefile) on the client device used for the deployment.
This Statefile will then be migrated to a centrally controlled secure backend, a Google Cloud Storage (GCS) Bucket, as is more typical in a production environment.

Before you begin
1.  Ensure you have completed TF00 before attempting this lab.

2. In the IDE terminal pane, enter the following command

```shell
cd ~/Labs/TF05
```

3.  This shifts your current working directory to TF05. Ensure all commands are executed in this directory

4.  Close any open files and use the Explorer pane to navigate to and open the TF05 folder.

Solution

The solution to this lab can be found in Labs/solutions/TF05. Try to use this only as a last resort if you are struggling to complete the step-by-step processes.

Task 1. Deploy resources using pre-provisioned code

1.  Update *line 2* in **variables.tf** with your lab project id

2.  Run `terraform init` and then `terraform plan` commands

3.  Verify there are no errors and then run `terraform apply` followed by **yes**

4.  Once the deployment completes, run `terraform state list` to view the deployed resources.

5.  Select and review the newly created **terraform.tfstate** file in the TF05 folder

6.  Unless otherwise specified, the state file for your deployments will reside in
the local root directory. In all but trivial deployments the state file will be
stored in a remote, shared location.

Task 2. Create a GCS Buckets for Remote State

In this task you will create a GCS bucket for use as a terraform remote backend.

1. Ensure your gcloud config project attribute is set to your lab project
```bash
gcloud config set project <your lab project id>
```

2. Enter the following command, replacing <your-name> with your name.
```bash
gcloud storage buckets create gs://tf-remote-state-<your-name> --location=europe-west2
```
3. Add digits to your name and try again should you receive a message regarding bucket name uniqueness requirements.

4. Switch to the console and verify that the bucket now exists, `Cloud Storage -> Buckets`

Task 3. Update the Terraform Configuration

1.  In **main.tf** uncomment *lines 18 to 23*

2.  Update line 20 to reflect *your* bucket name

3.  Run `terraform init` to re-initialize Terraform

4.  During the re-initialization, Terraform will detect the local state-file and prompt to confirm its migration to GCS. Type **yes** when prompted.

5.  Confirm the state has been migrated by checking the bucket for the **default.tfstate** file. Make a note of its size. In TF05b you will deploy additional resources, and this will be reflected by a growth in the state file size. Also
notice that your local state file is now empty.

6.  Do not delete any resources as they will be used in the next phase of the lab

## TF05b

# Lab Objectives
This lab will deploy a Global external application load balancer to distribute
web traffic across an auto-scaling group of GCE instances.

Solution

There is a Solution section at the very end of these instructions. Try to use this
only as a last resort if you are struggling to complete the step-by-step tasks.

Before you begin
1.  Ensure you have completed TF00 and TF05 before attempting this lab.

2.  In the IDE terminal pane, enter the following commands:
```shell
cd ~/Labs/TF05
```

3.  This shifts your current working directory to TF05. Ensure all commands are executed in this directory

4.  Close any open files and use the Explorer pane to navigate to and open the Labs/TF05 folder.

5.  Take note of the contents of this folder. There are 2 configuration files at the
root level; maint.tf and variables.tf. There is a subfolder named network with 2 files: **main.tf** and **outputs.tf**.

6. In Lab TF05 you reconfigured this terraform configuration to use a remote backend, and you migrated your statefile to GCS.

7. Run `terraform state list` to review the currently deployed resources.

The Challenge

Having been provided with a module that deploys all the necessary networking components, you are required to deploy the front-facing layer of an n-tier solution into this network stack. This will be an http web service hosted on linux gce instances. 

Multiple web instances are to be deployed to a single zone, and there should be a minimum of 2 instances running at any time, automatically increasing to a maximum of 5 should demand dictate. An autoscaling managed instance group should be used to achieve this. Traffic should be distributed across the running instances using a global external application load balancer. 

Successful completion of the challenge will be the generation of an http url which, when accessed, will display a message indicating from which instance the page is being served from.  You may add to but are not to modify any supplied code and the provider version to be used is
6.17.0.

Understanding the traffic flow between these components in Google Cloud
Load Balancing is crucial for designing and managing your load balancer setup.
A high-level explanation of the traffic flow:

**Forwarding Rule**: The Global Forwarding Rule is the entry point for external traffic. It listens for incoming requests from users or clients. It defines the IP protocol, port range etc and maps incoming traffic to the appropriate target, which is a Target HTTP Proxy or Target HTTPS Proxy.

**Target Proxy**: The Target Proxy receives incoming traffic from the Global Forwarding Rule. It determines how the incoming requests are mapped to backend services using URL Maps and forwards the requests to the appropriate URL Map.

**URL Map**: The URL Map defines how incoming requests are matched to backend services based on rules and conditions. It can route requests based on URL path, host, header, query parameters, etc and determines which backend service will handle the request.

**Backend Service**: The Backend Service defines a group of instances that can handle incoming requests. It may also include health checks to ensure the instances are healthy. The Backend Service handles distributing requests to the instances based on its configuration (e.g., round-robin, session affinity, etc.).

**Instance Group / Serverless NEG**: The backend instances are organized into an Instance Group or a Serverless Network Endpoint Group (NEG). An Instance Group can contain VM instances, while a Serverless NEG can contain VMs or other resources. These groups are responsible for managing the instances and distributing traffic according to the load balancing algorithm configured in the Backend Service.

**Instance / Backend Instance**: Instances are the actual virtual machines or resources that handle incoming requests. They run applications and services that users interact with. The load balancer sends traffic to instances based on the rules defined in the Backend Service and the load balancing algorithm.
After processing the incoming request, the instance sends its response back to the load balancer.

References
https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_instance_template.html

https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_instance_group_manager

https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_autoscaler

https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_health_check.html

https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_global_forwarding_rule.html

Try it yourself

You have the option of attempting this lab yourself with minimal guidance. Use the step-by-step guides and solution folder if you need assistance.

The high-level lab goals are:

1.  Create **template.tf**, and use it to create a
`google_compute_instance_template.lab_instance_template` resource with the following settings:

```terraform
resource block identifier: lab_instance_template
name: lab-launch-template
size: e2-small
instance: latest version of google cloud Debian linux image
health checks: permit health checks to instances created from this template
service account: use the default service account but with narrow scope
instance metadata: apply the following startup script..

"#! /bin/bash\n sudo apt-get update\n sudo apt-get install apache2 -y\n     sudo a2ensite
default-ssl\n  sudo a2enmod ssl\n  vm_hostname=\"$(curl -H \"Metadata-Flavor:Google\"
\\\n http://169.254.169.254/computeMetadata/v1/instance/name)\"\n   sudo echo
\"Page served from: $vm_hostname\" | \\\n   tee /var/www/html/index.html\n   sudo
systemctl restart apache2"
```

2.  Create **managed-instance-group.tf**, and use it to create the following
resources:

`google_compute_health_check.http_health_check`
`google_compute_instance_group_manager.lb_backend_group`
`google_compute_autoscaler.lab_autoscaler`

3.  The managed instance group should use your template, following the challenge requirements, and reside in us-east1-b. The `google_compute_health_check.http-health-check` should check for port **80** responses.

4.  In **loadbalancer.tf** create the following resources:

`google_compute_backend_service.backend_service`
`google_compute_url_map.http_url_map`
`google_compute_target_http_proxy.http_proxy`
`google_compute_global_forwarding_rule.http_forwarding`

5.  The `google_compute_backend_service.backend_service` backend should be the managed instance group from step 2.

6.  The `google_compute_global_forwarding_rule` should receive http traffic on the **lab-lb-ip** IP address created in the network module.

7.  Using **outputs.tf**, output the IP address of the load balancer to your screen for testing.

## Step-by-step instructions
Task 1. Create Image Template

https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_instance_template.html

1.  Open the reference link and familiarize yourself with the resource

2.  Your template will be used in conjunction with a managed instance group so scroll down to the `Using with Instance Group Manager` section.

3.  Create a new file named **template.tf** and copy the example code for resource `google_compute_instance_template` into it. For convenience the
code is shown below..
```terraform
resource "google_compute_instance_template" "instance_template" {
  name_prefix  = "instance-template-"
  machine_type = "e2-medium"
  region       = "us-central1"

  // boot disk
  disk {
     ...
  }

  // networking
  network_interface {
     ...
  }

  lifecycle {
    create_before_destroy = true
  }
}
```
4.  Change the resource block identifier from "instance-template" to
"lab_instance_template"

5.  Replace the name_prefix "instance-template-" with `var.instance-prefix`
Add this variable to **variables.tf** with a default value of `"lab-template-"`

6.  Replace the machine_type "e2-medium" with `var.machine-type`. Add this variable to **variables.tf** with a default value of `"e2-small"`

7.  Delete the `region` argument, because, reading the argument reference documentation regarding region, you will see that if not specified, then the Provider region is used.

8.  You need to add disk parameters for your template. Refer to the documentation for understanding and then paste the following code within
the `disk` braces { }
```
boot = true
source_image = "projects/debian-cloud/global/images/family/debian-11"
mode  = "READ_WRITE"
type  = "PERSISTENT"
```

9.  You need to add network interface parameters for your template. Refer to the documentation for understanding and then paste the following code
within the `network_interface` braces { } 
Note the absence of an access_config sub-block, indicating you do not want the template to be given a public IP address.

```
network    = module.vpc.vpc-id
subnetwork = module.vpc.lab-subnet
```

Note. These values are derived from the outputs of the networking module. They are found in **TF05\network\outputs.tf**
Attempting to refer to the vpc directly using
google_compute_network.lab_vpc.id as you have previously, would fail here as the root module is not aware of the details of the
child module resources. The same applies to the subnetwork.

10. A startup script has been provided that will install updates and Apache, configuring index.html to display a message that includes the vm name. Add the following code to the end of your resource block, inside the closing brace:
```
metadata = {

  startup-script = "#! /bin/bash\n     sudo apt-get update\n     sudo apt-get install
apache2 -y\n     sudo a2ensite default-ssl\n     sudo a2enmod ssl\n
vm_hostname=\"$(curl -H \"Metadata-Flavor:Google\" \\\n
http://169.254.169.254/computeMetadata/v1/instance/name)\"\n   sudo echo
\"Page served from: $vm_hostname\" | \\\n   tee /var/www/html/index.html\n
sudo systemctl restart apache2"
}
```

11. This template will be used to create a managed instance group which will have health checks performed against them. Firewall rules must be applied to allow this and therefore you must use the tag already defined in the VPC firewall rules. Add the following block to the end of your resource block,
inside the closing brace:
```terraform
tags = ["allow-health-check"]
```

12. Save changes to **template.tf**
13. Run `terraform plan` Address any issues found, referring to the solution section if necessary.

14. Run `terraform apply` and use the Console to verify the deployment of the template.

Task 2. Create Managed Instance Group

1.  Create a new file named **managed-instance-group.tf** in the TF05 root directory

2.  Open the following help references to familiarize yourself with the architecture surrounding managed instance groups:

https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_instance_group_manager

https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_autoscaler

https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_health_check.html

3.  Focussing on`compute_instance_group_manager`:

Copy the `google_compute_health_check` example resource block into **managed-instance-group.tf**

For convenience the code is shown below:
```terraform
resource "google_compute_health_check" "autohealing" {
  name                = "autohealing-health-check"
  check_interval_sec  = 5
  timeout_sec         = 5

  healthy_threshold   = 2
  unhealthy_threshold = 10 # 50 seconds

  http_health_check {
    request_path = "/healthz"
    port         = "8080"
  }
}
```
4.  Change the resource block identifier from "autohealing" to
`"lab_health_check"`

5.  Change the health check name from autohealing-health-check" to `"lab-http-check"`

6.  Reading the Argument reference, you will find that the next 4 lines are optional parameters which, if unspecified, adopt default values. For code brevity, delete these lines.

7.  Change the http health check request path from "/healthz" to `"/"`

8.  Change the http health check port from "8080" to `"80"`

9.  The modified health check block should now read:
```terraform
resource "google_compute_health_check" "lab_health_check" {
  name               = "lab-http-check"
  http_health_check {
    port               = 80
    request_path       = "/"
  }
}
```
10. Copy the `google_compute_instance_group_manager` resource block example code into the bottom of **managed-instance-group.tf**

11. For convenience the code is shown below:
```terraform
resource "google_compute_instance_group_manager" "appserver" {
  name = "appserver-igm"

  base_instance_name = "app"
  zone               = "us-central1-a"

  version {

    instance_template  =
google_compute_instance_template.appserver.self_link_unique
  }

  all_instances_config {
    metadata = {
      metadata_key = "metadata_value"
    }
    labels = {
      label_key = "label_value"
    }
  }

  target_pools = [google_compute_target_pool.appserver.id]
  target_size  = 2

  named_port {
    name = "customhttp"
    port = 8888
  }

  auto_healing_policies {
    health_check      = google_compute_health_check.autohealing.id
    initial_delay_sec = 300
  }
}
```
12. Change the resource block identifier from "appserver" to `"lab_mig"`

13. Change the name from "appserver-igm" to `"lab-mig"`

14. Change the base_instance_name from "app" to `"vm"`

15. Replace the zone value with variable `var.lab-zone`, declare this in **variables.tf** with a default value of `"us-east1-b"`

16. Change the instance template resource reference from
google_compute_instance_template.appserver.self_link_unique to your template
`google_compute_instance_template.lab_instance_template.self_link_unique`

17. Remove the all_instances_config block

18. Remove the target_pools argument (do not remove target_size argument)

19. Change the named_port name from "customhttp" to `"http"`

20. Change to port from 8888 to `80`

21. Change the health_check reference from
google_compute_health_check.autohealing.id to your health check
`google_compute_health_check.lab_health_check.id`

22. Change the initial delay to `120` seconds

23. The updated code should now be as follows:
```terraform
resource "google_compute_instance_group_manager" "lab_mig" {
  name = "lab-mig"

  base_instance_name = "vm"
  zone               = var.lab-zone

  version {
    instance_template  = google_compute_instance_template.lab_instance_template.self_link_unique
  }

  target_size  = 2

  named_port {
    name = "http"
    port = 80
  }

  auto_healing_policies {
    health_check      = google_compute_health_check.lab_health_check.id
    initial_delay_sec = 120
  }
}
```
24. From the autoscaler help reference:

https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_autoscaler

Copy the `google_compute_autoscaler` resource block example from the Example Usage - Autoscaler Basic section and paste
into the bottom of **managed-instance-group.tf** 

For convenience the code is shown below:

```terraform
resource "google_compute_autoscaler" "example_autoscaler" {
  name              = "example-autoscaler"
  target            = google_compute_instance_group_manager.lb-backend-group.instance_group
  zone = "us-east1-b"

  autoscaling_policy {
    min_replicas = 2
    max_replicas = 5

    load_balancing_utilization {
      target = 0.5
    }
  }
}
```
25. Change to resource block identifier from "example_autoscaler" to `"lab_autoscaler"`

26. Change the name from "example-autoscaler" to `"lab-autoscaler"`

27. Change the target reference from
google_compute_instance_group_manager.lb-backend-
group.instance_group to your instance group
`google_compute_instance_group_manager.lab_mig.instance_group`

28. Change the zone to variable `var.lab-zone`

29. The updated code should be as shown below:

```terraform
resource "google_compute_autoscaler" "lab_autoscaler" {
  name              = "lab-autoscaler"
  target            = google_compute_instance_group_manager.lab_mig.instance_group
  zone              = var.lab-zone

  autoscaling_policy {

    min_replicas = 2
    max_replicas = 5

    load_balancing_utilization {
      target = 0.5
    }
  }
}
```
30. Save changes to files and then apply them with `terraform apply`

31. Navigate to the Instance groups section of Compute Engine in the console.
Select the `lab-mig instance group` to view the newly created instances. Wait for a minute or so and then refresh your browser to verify that the instances show as Healthy, indicating they have passed the health checks. Note the
warning that there is no backend service attached to the instance group. This will be your next task.

Task 3. Create Load Balancer

1.  Create a new file named **loadbalancer.tf** in the TF05 root directory

2.  https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_global_forwarding_rule.html

3.  Navigate to Example Usage - Global Forwarding Rule External Managed

4.  Copy the `google_compute_global_forwarding_rule` resource example into **loadbalancer.tf**

5.  For convenience the code is shown below:
```terraform
resource "google_compute_global_forwarding_rule" "default" {
  name                  = "global-rule"
  target                = google_compute_target_http_proxy.default.id
  port_range            = "80"
  load_balancing_scheme = "EXTERNAL_MANAGED"
}
```
6.  Change resource block identifier from "default" to `"http_forwarding"`

7.  Change the target reference from
google_compute_target_http_proxy.default.id to
`google_compute_target_http_proxy.http_proxy.id` (You will create this next)

8.  Add argument `ip_protocol` with value of `"TCP"`

9.  Add argument `ip_address` with value of `module.vpc.lb-ip-id`
```
Note: Here you are referencing the public ip address defined in the networking module and exposed in the **network/outputs.tf** file
```

10. Your modified code should be as shown:

```terraform
resource "google_compute_global_forwarding_rule" "http_forwarding" {
  name                  = "global-rule"
  target                = google_compute_target_http_proxy.http_proxy.id
  port_range            = "80"
  load_balancing_scheme = "EXTERNAL_MANAGED"
  ip_protocol           = "TCP"
  ip_address            = module.vpc.lb-ip-id
}
```

https://registry.terraform.io/providers/hashicorp/google/6.17.0/docs/resources/compute_global_forwarding_rule.html

11. Copy the `google_compute_target_http_proxy` resource example into loadbalancer.tf

12. For convenience the code is shown below:
```terraform
resource "google_compute_target_http_proxy" "default" {

  name        = "target-proxy"
  description = "a description"
  url_map     = google_compute_url_map.default.id
}
```

13. Change the resource block identifier from "default" to `"http_proxy"`

14. Change the name from "target-proxy" to `"http-lb-proxy"`

15. Change the url_map reference from google_compute_url_map.default.id to `google_compute_url_map.http_url_map.id` (You will create this next)

16. Delete the optional description argument. Your code should now be as shown below:

```terraform
resource "google_compute_target_http_proxy" "http_proxy" {
  name    = "http-lb-proxy"
  url_map = google_compute_url_map.http_url_map.id
}
```
17. Copy the `google_compute_url_map` resource example into **loadbalancer.tf**

For convenience the code is shown here:

```terraform
resource "google_compute_url_map" "default" {
  name            = "url-map-target-proxy"
  description     = "a description"
  default_service = google_compute_backend_service.default.id

  host_rule {
    hosts        = ["mysite.com"]
    path_matcher = "allpaths"
  }

  path_matcher {
    name            = "allpaths"
    default_service = google_compute_backend_service.default.id

    path_rule {
      paths   = ["/*"]
      service = google_compute_backend_service.default.id
    }
  }
}
```
18. Delete all arguments *except* `name` and `default_service`.

19. Change the resource block identifier from "default" to `"http_url_map"`

20. Change the default service from
google_compute_backend_service.default.id to
`google_compute_backend_service.backend_service.id` (You will create this next)

Your modified code should be as follows:

```terraform
resource "google_compute_url_map" "http_url_map" {
  name            = "url-map-target-proxy"
  default_service = google_compute_backend_service.backend_service.id

}
```
21. Copy the `google_compute_backend_service` resource example into **loadbalancer.tf**

22. For convenience the code is shown below:

```terraform
resource "google_compute_backend_service" "default" {
  name                  = "backend"
  port_name             = "http"
  protocol              = "HTTP"
  timeout_sec           = 10
  load_balancing_scheme = "EXTERNAL_MANAGED"

}
```

23. Change the resource block identifier from "default" to `"backend_service"`

24. Add the following lines:

```terraform
 health_checks = [google_compute_health_check.lab_health_check.id]
  backend {
    group = google_compute_instance_group_manager.lab_mig.instance_group
    balancing_mode  = "UTILIZATION"
    capacity_scaler = 1.0
  }
```

25. Your modified code should be as follows:

```terraform
resource "google_compute_backend_service" "backend_service" {
  name = "backend"
  port_name  = "http"
  protocol = "HTTP"
  timeout_sec = 10
  load_balancing_scheme = "EXTERNAL_MANAGED"
  health_checks = [google_compute_health_check.lab_health_check.id]
  backend {
    group = google_compute_instance_group_manager.lab_mig.instance_group
    balancing_mode  = "UTILIZATION"
    capacity_scaler = 1.0

  }
}
```

26. Create a new file in the TF05 root directory named **outputs.tf**

27. Add the following code to TF05\outputs.tf to output the load balance IP address to screen:

```terraform
output "lb-ip" {
  description = "ip of lab load balancer"
  value =   module.vpc.lb-ip
}
```

28. Use `terraform apply` to deploy the 4 new resources.

Task 4. Test the Load Balancer

1.  Note the IP address of the load balancer. Open a local browser tab and navigate to this address. You will receive error messages for a while whilst the load balancer is deployed globally. Refresh periodically until you are shown the name of the backend vm you are being connected to. This may take 3-4 minutes. Refresh your browser to verify load balancing across the 2 backend instances.

Task 5. Lab Clean Up

1.  Run `terraform destroy` to destroy your resources. This may take 5 minutes or more.

## GO01
# Lab GO01 - A Simple Go App

## Lab Objectives
Create and run a simple application using golang

## Learning Outcomes
* Gain exposure to core language constructs in go
* Build and run go apps

## High-Level Steps
* Create a simple Golang app
* Execute an app with go run
* Compile an app with go build

## Detailed Steps

### Ensure Go is installed
You will begin by ensuring that Go is installed. Run the following command:
```bash
go version
```
Go 1.25 should already be installed in the lab environment

### Hello World
Change directory into the GO01 folder:
```bash
cd Labs/GO01
```
Open **main.go** in the editor

Add the following code to main.go:
```go
package main

import "fmt"

func main() {
  fmt.Println("Hello World!")
}
```
In your terminal, execute the file using _go run_:
```bash
go run main.go
```
You should see the message 'Hello World!' displayed in the terminal.

### Handle User Input
Edit **main.go** and amend the contents as follows:
```go
package main

import "fmt"

func main() {
  name := ""
  fmt.Print("Enter your name: ")
  fmt.Scanf("%s\n", &name)
  fmt.Printf("Hello, %s", name)
}
```
The extra lines of code added here initialise an empty string variable, before displaying a prompt to the user and reading input until a newline is reached, storing the input into the name variable. A message which includes the name is then printed. 

Execute the file again:
```bash
go run main.go
```

### Compile the Application
Instead of using the _go run_ command to interpret the code each time you run the app, you can instead compile it for faster execution. You will use _go build_ for this. First, you need to create a minimal _go.mod_ file, as go build will not work without one:
```bash
echo "module greeter" > go.mod
```
A module name is the minimum configuration required for go build to be able to build the app. To compile the code, run the following:
```bash
go build -o greeter
```
This will produce an executable called _greeter_. Execute the file from the terminal:
```bash
./greeter
```


## GO02
# Lab GO02 - Create a Web App in Go

## Lab Objectives
Create and test a simple web application using Golang

## Learning Outcomes
* Use the go std library's http module to create a web app
* Write unit tests using the testing module

## High-Level Steps
* Write web app code
* Manually test the web app using curl
* Implement unit tests for the app in golang

## Detailed Steps
### Implement the Web App
In the terminal, change directory into the Labs/GO02 folder. Open the **main.go** file in the editor:
```go
package main

import (
    "fmt"
)

func main() {
    fmt.Println("TODO: Implement web app");
}
```
You will need to make some changes to this file, starting with the imports. Since you are creating a web app you will need to be able to handle HTTP requests. You can use the standard library `net/http` module for this. You will also need a few other utility modules. The imports should look like:
```go
import (
    "encoding/json"
    "net/http"
    "fmt"
    "log"
    "errors"
    "io"
    "github.com/google/uuid"
)
```
Next, add the following between the import block and the main function:
```go
type task struct {
    Completed bool `json:"completed"`
    ID string `json:"id"`
    Title string `json:"title"`
    Description string `json:"description"`
}

var tasks map[string]task = make(map[string]task)

func taskHandler(w http.ResponseWriter, req *http.Request) {
    decoder := json.NewDecoder(req.Body)
    var re, result task
    err := decoder.Decode(&re)

    if err != nil && !errors.Is(err, io.EOF) {
        panic(err)
    }

    w.Header().Set("Content-Type", "application/json")
    w.Header().Set("Access-Control-Allow-Origin", "*")
    w.Header().Set("Access-Control-Allow-Headers", "Content-Type")

    if req.Method == "GET" {
        encoder := json.NewEncoder(w)
        encoder.Encode(tasks)
    }
    if req.Method == "POST" || req.Method == "PUT" {
        i := re.ID
        if i == "" {
                i = fmt.Sprintf("%s", uuid.New())
                re.ID = i
        }
        tasks[i] = re
        result = tasks[i]
        encoder := json.NewEncoder(w)
        encoder.Encode(&result)
    }
    if req.Method == "DELETE" {
        delete(tasks, re.ID)
    }
}
```
This is the core logic of your web app. Listening for http requests, it will parse JSON from the request body according to the schema represented by the struct type definition. Now you need to edit the main function to actually serve the application:
```go
func main() {
    http.HandleFunc("/tasks", taskHandler)
    log.Fatal(http.ListenAndServe(":8080", nil))
}
```
This registers the function containing your application logic as a handler for requests to the _/tasks_ endpoint, before starting the server with logging enabled.

### Build and Run the Application
Before compiling, you will need to install the uuid library being used to programmatically generate IDs:
```bash
go get -u github.com/google/uuid
```
Then compile the code using `go build`:
```bash
go build -o bin/webapp
```
Run the binary:
```bash
./bin/webapp
```
Once the app is running, in a new terminal session use curl to test the functionality of the app:
```bash
cd ~/Labs/GO02 # ensure you're in the right place
curl -XPOST -d@request.json http://localhost:8080/tasks
curl -XGET http://localhost:8080/tasks
# edit request.json, setting 'Completed' to true
curl -XPUT -d@request.json http://localhost:8080/tasks
curl -XGET http://localhost:8080/tasks
curl -XDELETE -d@request.json http://localhost:8080/tasks
curl -XGET http://localhost:8080/tasks
```
You need to pass the task object within the request body for PUT or DELETE to function correctly.

Create a new JSON file called **request_put.json**

Add the following JSON, replacing the generated id with the web app generated uuid:
```JSON
{
  "title": "Task1",
  "description": "A sample task",
  "completed": true,
  "id": "<value copied from terminal output of GET>"
}

```
Use curl and point to the **request_put.json** file
```bash
curl -XPUT -d@request_put.json http://localhost:8080/tasks
```


In the original terminal, stop the app with `CTRL+c`

### Add a Basic Unit Test
You will now add a basic unit test for your app. For this, you will need to use another external library, `gock`, to _mock_ the response from the app:
```bash
go get -u github.com/h2non/gock
```
Once the gock package has been installed, create a new file in the same folder called **main_test.go**, with the following contents:
```go
package main

import (
    "net/http"
    "testing"
    "github.com/h2non/gock"
)

func TestTaskHandler(t *testing.T) {
    defer gock.Off()

    p := task{ID: "abcd", Title: "Foo", Description: "Bar", Completed: false}
    gock.New("http://localhost:8080").
        Get("/tasks").
        Reply(200).
        JSON(p)

    res, err := http.Get("http://localhost:8080/tasks")
    if err != nil || res.StatusCode != 200 {
        t.Errorf("Get Request Failed")
    }
}
```
Execute the test with `go test`:
```bash
go test
```
You should observe a result similar to:
```text
PASS
ok      app     0.004s
```

## GO03
# Lab GO03 - Build a Custom Terraform Provider

## Lab Objectives
Build and use a custom terraform provider to interact with an API

## Learning Outcomes
* Understand the structure of a terraform provider
* Build a provider using golang tooling
* Use a custom provider in a tf file

## High-Level Steps
* Review the codebase
* Build and relocate a binary
* Create a terraform configuration which references the new provider
* Create, update and delete objects using the custom provider

## Detailed Steps
### Examine the Provisioned Files
Change directory into the GO03 folder:
```bash
cd ~/Labs/GO03
```
In the editor, review the following files:
#### File: main.go
Purpose: This is the entry point for the Terraform provider binary. When Terraform initializes your provider, it invokes this function, which starts the plugin server.
#### File: provider/provider.go
Purpose: Registers your provider with Terraform (TypeName), sets its version (Version) and declares available resources and data sources (You currently only declare NewTaskResource())
#### File: provider/task_resource.go
Purpose: This defines the resource that the provider exposes, in this case, a simple task resource interacting with an API

Note: There is a Deep-Dive section at the end of these lab instructions that details these files.
### Initialize Golang
To set up a go.mod and go.sum file for the project, run the following: 
```bash
go mod init mynewprovider
```
Then, run: 
```bash
go mod tidy
```
This will download any missing dependencies (like the Terraform Plugin Framework) and clean up unused imports and updates the go.sum file
### Build the Provider Binary
Now you're ready to compile your provider into an executable that Terraform can use. Run:
```bash
go build -o terraform-provider-mynewprovider -buildvcs=false
```

This tells Go:

- `go build`: compile the code in this module

- `-o terraform-provider-mynewprovider`: name the output binary with Terraform's expected naming convention

- `-buildvcs=false`: don't use git commit info to automatically version the binary

### Move the provider binary
Move the new binary into terraform's default search path:
```bash
mkdir -p ~/.terraform.d/plugins/registry.terraform.io/custom/mynewprovider/0.1.0/linux_amd64

cp terraform-provider-mynewprovider ~/.terraform.d/plugins/registry.terraform.io/custom/mynewprovider/0.1.0/linux_amd64/
```
### Test Terraform Configuration
In the editor, navigate to the testing folder. Open **main.tf** and populate with:
```terraform
terraform {
  required_providers {
    mynewprovider = {
      source  = "custom/mynewprovider"
      version = "0.1.0"
    }
  }
}

provider "mynewprovider" {}

resource "mynewprovider_task" "example" {
  title       = "From custom provider"
  description = "Created using mynewprovider"
  completed   = false
}
```
In the terminal, navigate to the testing directory and run:
```bash
terraform init
terraform plan
terraform apply
```
Init and Plan will succeed but apply will fail as there is no target (mock) api running

In a new terminal (keeping the existing session open), navigate back to the GO02 directory and start the app from earlier:
```bash
go run main.go
```
Leave the app running. Back in the original terminal, re-run the `terraform apply`. It should now succeed. Verify the task creation:
```bash
curl http://localhost:8080/tasks
```
Update the Terraform configuration in **main.tf**, changing any of the values

Re-run `terraform apply`. To verify the update:
```bash
curl http://localhost:8080/tasks
```
The task attributes should have been successfully updated.

Use `terraform destroy` followed by **yes** to remove the resources you created with the custom provider.

```terraform
terraform destroy
```

## The Sample Provider - a Deeper Dive
Understanding Core Files in a Custom Terraform Provider
### main.go — Entry Point for the Terraform Plugin
Purpose:
This is the first file that runs when Terraform loads the provider binary. It registers the provider with Terraform by calling `providerserver.Serve`. 

Key Functionality:
```go
func main() {
  providerserver.Serve(
    context.Background(),
  provider.New,
  providerserver.ServeOpts{
    Address: "registry.terraform.io/custom/mynewprovider",
  },
  )
}
```
Explanation:
- provider.New: Returns an instance of your provider (defined in provider.go).
- ServeOpts{ Address: ... }: Identifies the provider namespace/type.
- providerserver.Serve: Boots the plugin server so Terraform can communicate with it.

### provider.go — Declares the Provider and Its Resources
Purpose:
This file defines what the provider exposes to Terraform — metadata, available resources, and configuration.  
Key Functionality:
```go
func (p *myNewProvider) Metadata(...) {
  resp.TypeName = "mynewprovider"
  resp.Version = "0.1.0"
}

func (p *myNewProvider) Resources(...) []func() resource.Resource {
  return []func() resource.Resource{
    NewTaskResource,
  }
}
```
Explanation:
- Metadata: Declares provider name and version.
- Resources: Registers resource constructors (e.g. task resource).
- Configure: (Optional) Injects configuration like API endpoints.
### task_resource.go — Implements the Custom Resource Logic
Purpose:
Defines the Create, Read, Update, Delete (CRUD) behavior of your custom resource.  
Key Structure:
```go
type taskResource struct{}
type taskModel struct {
  ID          types.String `tfsdk:"id"`
  Title        types.String `tfsdk:"title"`
  Description  types.String `tfsdk:"description"`
  Completed  types.Bool  `tfsdk:"completed"`
}

func (r *taskResource) Create(...) { ... }
func (r *taskResource) Read(...)   { ... }
func (r *taskResource) Update(...) { ... }
func (r *taskResource) Delete(...) { ... }
```
Explanation:
- taskModel: Maps to Terraform config.
- Create: Sends POST to API.
- Read: Sends GET to API.
- Update: Sends PUT to API.
- Delete: Sends DELETE to API.

How They Work Together
| File | Role |	Who Calls It | Interacts With |
| :--- | :--- | :--- | :--- |
| main.go | Starts the provider plugin | Terraform CLI | Calls provider.New |
| provider.go | Registers provider & resources | main.go | Returns taskResource |
| task_resource.go | Implements mynewprovider_task | provider.go |Talks to API |

Terraform executes the binary → runs main.go → which returns provider.go → which loads task_resource.go


## GO04

# Lab GO04 - Create a Terraform Provider Using the Scaffolding Template

## Lab Objectives
Use the Terraform provider scaffolding template to streamline provider creation

## Learning Outcomes
* Create a Terraform provider using a template as a starting point

## High-Level Steps
* Create a new repo based on the scaffolding template
* Edit the files to implement custom provider logic
* Build and test the provider

## Detailed Steps
### Create a New Repository
If you do not have a github account, you will need to create one to complete this lab. Log into your github account, then find the scaffolding template repository: https://github.com/hashicorp/terraform-provider-scaffolding-framework  
Click the green "Use this template" button (top right).
Select "Create a new repository".
Enter your github user and password if prompted.
Name your new repo _terraform-provider-simple1_.
Click "Create repository from template."  
In your lab environment terminal, run:
```bash
git clone https://github.com/<your-github-username>/terraform-provider-simple1.git ~/Labs/GO04
cd ~/Labs/GO04
```
### Edit the Template
Focus the new GO04 folder in the editor. In **go.mod** replace:
```
module github.com/hashicorp/terraform-provider-scaffolding-framework
```
with 
```
module github.com/<your-github-username>/terraform-provider-simple1
```
Making sure to fill in your github username in the module path

Open **main.go** and edit the contents like so:
```go
package main

import (
	"context"
	"flag"
	"log"

	"github.com/hashicorp/terraform-plugin-framework/providerserver"
	"github.com/<your_github_username>/terraform-provider-simple1/simple1"
)

var version = "dev"

func main() {
	var debug bool
	flag.BoolVar(&debug, "debug", false, "set to true to run the provider with support for debuggers like delve")
	flag.Parse()

	err := providerserver.Serve(context.Background(), simple1.New(version), providerserver.ServeOpts{
		Address: "registry.terraform.io/custom/simple1",
		Debug:   debug,
	})

	if err != nil {
		log.Fatal(err.Error())
	}
}
```
Making sure to substitute your github username and repo information where indicated

Create a new folder, "simple1", in the repository. This will house your clean provider logic.  
```bash
mkdir simple1
```

Copy provider.go from internal/provider to simple1:
```bash
cp internal/provider/provider.go simple1/
```
Update **provider.go** in the *simple1* directory with the following content:
```go
// Copyright (c) HashiCorp, Inc.
// SPDX-License-Identifier: MPL-2.0

package simple1

import (
	"context"
	"net/http"

	"github.com/hashicorp/terraform-plugin-framework/datasource"
	"github.com/hashicorp/terraform-plugin-framework/ephemeral"
	"github.com/hashicorp/terraform-plugin-framework/function"
	"github.com/hashicorp/terraform-plugin-framework/provider"
	"github.com/hashicorp/terraform-plugin-framework/provider/schema"
	"github.com/hashicorp/terraform-plugin-framework/resource"
	"github.com/hashicorp/terraform-plugin-framework/types"
)

// Ensure ScaffoldingProvider satisfies various provider interfaces.
var _ provider.Provider = &ScaffoldingProvider{}
var _ provider.ProviderWithFunctions = &ScaffoldingProvider{}
var _ provider.ProviderWithEphemeralResources = &ScaffoldingProvider{}

// ScaffoldingProvider defines the provider implementation.
type ScaffoldingProvider struct {
	// version is set to the provider version on release, "dev" when the
	// provider is built and ran locally, and "test" when running acceptance
	// testing.
	version string
}

// ScaffoldingProviderModel describes the provider data model.
type ScaffoldingProviderModel struct {
	Endpoint types.String `tfsdk:"endpoint"`
}

func (p *ScaffoldingProvider) Metadata(ctx context.Context, req provider.MetadataRequest, resp *provider.MetadataResponse) {
	resp.TypeName = "scaffolding"
	resp.Version = p.version
}

func (p *ScaffoldingProvider) Schema(ctx context.Context, req provider.SchemaRequest, resp *provider.SchemaResponse) {
	resp.Schema = schema.Schema{
		Attributes: map[string]schema.Attribute{
			"endpoint": schema.StringAttribute{
				MarkdownDescription: "Example provider attribute",
				Optional:            true,
			},
		},
	}
}

func (p *ScaffoldingProvider) Configure(ctx context.Context, req provider.ConfigureRequest, resp *provider.ConfigureResponse) {
	var data ScaffoldingProviderModel

	resp.Diagnostics.Append(req.Config.Get(ctx, &data)...)

	if resp.Diagnostics.HasError() {
		return
	}

	// Configuration values are now available.
	// if data.Endpoint.IsNull() { /* ... */ }

	// Example client configuration for data sources and resources
	client := http.DefaultClient
	resp.DataSourceData = client
	resp.ResourceData = client
}

func (p *ScaffoldingProvider) Resources(ctx context.Context) []func() resource.Resource {
	return []func() resource.Resource{
		NewTaskResource,
	}
}

func (p *ScaffoldingProvider) EphemeralResources(ctx context.Context) []func() ephemeral.EphemeralResource {
	return []func() ephemeral.EphemeralResource{}
}

func (p *ScaffoldingProvider) DataSources(ctx context.Context) []func() datasource.DataSource {
	return []func() datasource.DataSource{}
}

func (p *ScaffoldingProvider) Functions(ctx context.Context) []func() function.Function {
	return []func() function.Function{}
}

func New(version string) func() provider.Provider {
	return func() provider.Provider {
		return &ScaffoldingProvider{
			version: version,
		}
	}
}
```
Summary of changes:
* package name: provider -> simple1
* Return value for resource function: { NewExampleResource } -> { NewTaskResource }
* Return values for EphemeralResource, DataSource and Function functions: { original value } -> {}
### Create TaskResource Type
Add a new file **task_resource.go** in the simple1 directory

Browse to https://github.com/qatip/provider-lab-files.git

Copy the contents of **task_resource.go** and paste into your new file.
### Build the Provider
Run:
```bash
cd ~/Labs/GO04 # ensure you're in the right place
go mod tidy
go build -o terraform-provider-simple1 -buildvcs=false
```
### Test the Provider
Create a new directory, provider_test:
```bash
mkdir ~/provider_test
```
Copy the binary into that new directory:
```bash
cp terraform-provider-simple1 ~/provider_test/
cd ~/provider_test
```
Focus the new directory in the editor. Create a new _terraform.rc_ file:
```bash
cat - >> terraform.rc <<EOF
provider_installation {
    dev_overrides {
    "simple1" = "$(pwd)"
  }
  direct {
    exclude = ["registry.terraform.io/*/*"]
  }
}
EOF
```
Then create a simple **main.tf** config:
```terraform
provider "simple1" {}

resource "simple1_task" "example" {
  title       = "Test from Registry"
  description = "Created using the local provider"
  completed   = false
}
```
To test the configuration, start the web app from lab GO02 in another terminal session, then run:
```bash
export TF_CLI_CONFIG_FILE="$(pwd)/terraform.rc"
terraform plan
terraform apply
```
Verify the creation of the resource:
```bash
curl http://localhost:8080/tasks
```

#### Tidy Up

Use `terrafom destroy` to remove the resource you provisioned with the local provider.

Stop the web API running with CTRL+C.


## ANS01
# Lab ANS01 - Ansible Introduction and Ad-Hoc Commands

## Lab Objectives
Use ansible to deploy a webserver resource locally

## Learning Outcomes
By the end of this lab, you will have:
* Installed Ansible
* Used ad-hoc commands to configure a local webserver

## High-Level Steps
* Use apt to install ansible
* Execute an ad-hoc command to install NGINX
* Demonstrate idempotence
* Execute another command to uninstall NGINX

## Detailed Steps

### Installation
Ansible is not preinstalled in the lab environment, so your first step will be to install it. Since the cloudshell VM is debian-based, you will use apt:
```bash
sudo apt update 
sudo apt install software-properties-common
sudo apt-add-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```
Confirm the installation:
```bash
ansible --version
```

### Use Ansible to Install a Web Server
In subsequent labs you will explore many of the powerful features that Ansible offers. For now though, you will keep things simple and run an _ad-hoc_ command to configure a web server:
```bash
ansible 127.0.0.1 -m apt -a "name=nginx state=present update_cache=true" --become
```
This command instructs ansible to:
* target the local machine via the 127.0.0.1 loopback address
* execute the built-in _apt_ module
* pass the arguments defined by the string following the -a flag to the module
* use privilege escalation via sudo to gain the permissions necessary to manage system packages

You should see that Ansible returns a JSON object, showing you that it has completed the task. 

### Check NGINX has been Installed Correctly
Verify that the nginx software has been installed:
```bash
which nginx
```
Due to a quirk of cloudshell, nginx is not actually running yet. Typically you would start and stop services using the _service_ module, but this will not work on cloudshell either. To start nginx using ansible, run the following:
```bash
cd ~/Labs/ANS01
ansible 127.0.0.1 -m shell -a "cmd=$(cat ensure_nginx_started.sh)" --become
```
The `curl` command can then be used to check that your web server is running correctly:
```bash
curl http://localhost
```
You should get a response back similar to this:
```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

### Demonstrate Idempotence
One of the benefits of using a declarative configuration tool like Ansible is _idempotence_. If the target resources are already in their desired state, then reapplying the configuration should do nothing. You can observe this by re-running the same ansible command again:
```bash
ansible 127.0.0.1 -m apt -a "name=nginx state=present update_cache=true" --become
```
You should see that the execution is a lot quicker and that you get a different output, like this:
```
localhost | SUCCESS => {
    "cache_update_time": 1612268706, 
    "cache_updated": true, 
    "changed": false
}
```
You should see that the changed value is false; this means Ansible noticed that NGINX was already installed and didn't make any changes. You can run this command as many times as you like and you will get a success message.

### Uninstall NGINX
You will want to be able to install NGINX again in a subsequent lab, so for now you will uninstall it. First, stop the nginx server:
```bash
ansible 127.0.0.1 -m shell -a "cmd='killall nginx'" --become
```

NOTE:

If this does not terminate all nginx processes, manually kill the processes with:

```bash
sudo killall nginx
```
Verify that NGINX is no longer running:
```bash
curl http://localhost # should fail to connect
```
Then to remove the nginx package from the system, run the following ansible command:
```bash
ansible 127.0.0.1 -m apt -a "name=nginx state=absent update_cache=true" --become
```
Note the difference from the previous command: in the module arguments, state is set to absent, as opposed to present, meaning ensure that NGINX is not installed. Verify that NGINX is no longer installed:
```bash
which nginx
echo $? # should display 1
```
NOTE:
`echo $?` returns the rturn value (exit status) of the last executed command. Zero (0) is usually success.

## ANS02
# Lab ANS02 - Ansible Playbooks

## Lab Objectives
Configure a local webserver using an Ansible _playbook_ 

## Learning Outcomes
By the end of this lab, you will have:
* Executed a playbook using Ansible
* Modified a playbook

## High-Level Steps
* Run a basic Ansible playbook
* Add tasks to a playbook to edit server configuration

## Detailed Steps

### Setup
In the cloud IDE, focus the ANS02 directory in the explorer pane, and change directory into ANS02 in the terminal:
```bash
cd ~/Labs/ANS02
```

### Review Existing Configuration
Whilst ad-hoc commands such as those used in the previous lab are fine for a quick demonstration, _declarative_ configurations are a much better way to store and share configuration information. Ansible uses _playbooks_ to define declarative configurations. A base playbook called _playbook.yml_ has been provided for this lab:
```yaml
---
- hosts: localhost
  name: Basic Play
  connection: local
  become: true
  tasks:
  - name: Install nginx
    apt:
      name: nginx
      state: present
      update_cache: true
```
This defines a single _play_, called 'Basic Play', which in turn consists of a single _task_ named 'Install nginx'. Note how this configuration maps to the elements of the ad-hoc command from the previous lab. This playbook does the exact same thing, but in a declarative fashion.  
Execute the playbook:
```bash
ansible-playbook playbook.yml
```
Wait for the play to finish, then verify that NGINX was installed:
```bash
which nginx
```
As before, re-run the playbook and confirm that nothing changes:
```bash
ansible-playbook playbook.yml
```
The output should show 0 changed.

### Update the Webserver Configuration
You will now edit the playbook to reconfigure NGINX to return a different response. Review the provided _nginx.conf_ file:
```conf
events {}
http {
    server {
        listen 80;
        location / {
          return 200 "Hello from nginx\n";
        }
    } 
}
```
To get nginx to use this new configuration, you need to:
* place this configuration file in the location from which NGINX reads its configuration
* restart the nginx server

Ansible can do both of these things. Edit **playbook.yml** so that it contains the following:
```yaml
---
- hosts: localhost
  name: Basic Play
  connection: local
  become: true
  tasks:
  - name: Install nginx
    apt:
      name: nginx
      state: present
      update_cache: true
  
  - name: Copy nginx.conf
    copy:
      src: nginx.conf
      dest: /etc/nginx/nginx.conf
    register: nginx_config

  - name: Restart nginx if needed
    shell:
      cmd: "killall nginx || sleep 1; nginx -g 'daemon off;' &"
```
You have added 2 additional tasks, one to copy your `nginx.conf` file to the relevant place and another to restart NGINX to take in the new changes.

Now execute the playbook:
```bash
ansible-playbook playbook.yml
```
Verify that NGINX is using the new configuration:
```bash
curl localhost
```
You should see:
```
Hello from nginx
```
There is one more improvement you can make to this configuration. Re-run the playbook again:
```bash
ansible-playbook playbook.yml
```
Note that, even though the configuration is the same, the output still shows 1 change: the restarting of NGINX is not idempotent, and will happen every time you execute this playbook. To stop this, and restore the idempotency of your playbook, you can make the 'restart nginx' task conditional on the result of the 'copy nginx.conf' task. Edit **playbook.yml** and ensure that it has the following contents:
```yaml
---
- hosts: localhost
  name: Basic Play
  connection: local
  become: true
  tasks:
  - name: Install nginx
    apt:
      name: nginx
      state: present
      update_cache: true
  
  - name: Copy nginx.conf
    copy:
      src: nginx.conf
      dest: /etc/nginx/nginx.conf
    register: nginx_config

  - name: Restart nginx if needed
    shell:
      cmd: "killall nginx || sleep 1; nginx -g 'daemon off;' &"
    when: nginx_config.changed == true # uses the result registered as nginx_config, from the task above
```
Now NGINX will _only_ be restarted if a change to nginx.conf necessitated the copy to be re-executed. To observe this, re-run the playbook again:
```bash
ansible-playbook playbook.yml
```
Now the section for the restart nginx task in the output should say something like:
```
TASK [Restart nginx if needed] **************************************************************************************************
skipping: [localhost]
```
Because the nginx.conf file is unchanged, there is no need to restart NGINX.

## ANS03
# Lab ANS03 - Ansible Inventories

## Lab Objectives
Use Ansible inventories to configure the hosts available during playbook execution

## Learning Outcomes
By the end of this lab, you will have:
* Created an inventory file to configure Ansible access to remote hosts
* Executed a playbook against a set of remote hosts
* Used a _dynamic inventory_ to automatically register new hosts

## High-Level Steps
* Configure SSH keys
* Use the provided Terraform files to provision some VMs
* Create and use a static inventory of VM IPs
* Create and use a dynamic inventory

## Detailed Steps

### Configure SSH Keys
When connecting to remote linux hosts, Ansible uses SSH. This means you will need an SSH key that Ansible can use to connect to the instances you will be working with. Generate a new SSH key pair
```bash
cd ~/Labs/ANS03 # ensure you are in the ANS03 folder
ssh-keygen -q -t ed25519 -f $(pwd)/ansible_key # hit enter on both passphrase prompts to create the key without a passphrase
```

### Provision Instances
Now that you have a key pair, wyoue can provision some instances. The ANS03 folder already contains the necessary Terraform files to deploy a set of VMs onto a network with access to required ports. Provision the infrastructure by following the usual terraform workflow:
```bash
terraform init
terraform plan -var gcp_project=<gcp project from qwiklabs> -var pubkey_path=$(pwd)/ansible_key.pub

terraform apply -var gcp_project=<gcp project from qwiklabs> -var pubkey_path=$(pwd)/ansible_key.pub
```
Wait for the apply to finish. Once the apply is complete, make a note of the _external_ IPs of your VMs as displayed by the Terraform outputs.

### Connectivity Check
Before continuing, it would be a good idea to check that everything is configured correctly for SSH. For each of the IP addresses output by terraform, run the following:
```bash
ssh -i ./ansible_key ansible@<ip_address>
```
When prompted, enter 'yes' to trust the host keys from the VMs.  
Note: the username 'ansible' is important, as this is the username for which the public SSH key has been added to the VMs.

### Creating the inventory
So far you have used Ansible to execute tasks against the cloudshell machine on which you are running Ansible. In reality, you would like to be able to configure hosts remotely, and for this Ansible needs information about the target hosts, which you store in an _inventory_ file. Create a new file in ANS03, called **inventory.yml**, and add the following:
```yaml
all:
  children:
    test:
      hosts:
        IP_OF_HOST_1: # replace 
        IP_OF_HOST_2: # replace
        IP_OF_HOST_3: # replace
      vars:
        ansible_user: ansible
        ansible_ssh_private_key_file: '~/Labs/ANS03/ansible_key'
```
This defines a single group of hosts, called 'all', with one subgroup called 'test'. You have also defined the ansible user and SSH key file to use to make the SSH connection.

### Playbook
Still in the ANS03 folder, create a file called **playbook.yml**, with the following contents:
```yaml
---
- hosts: all
  name: Ping Hosts
  tasks:
  - name: "Ping {{ inventory_hostname }}"
    ping:
    register: ping_info
  
  - name: "Show ping_info in console"
    debug:
      msg: "{{ ping_info }}"
```
NOTE: `inventory_hostname` is a magic variable in Ansible that represents the name of the current host as defined in your Ansible inventory file.

This playbook tells Ansible to connect to all hosts defined in the inventory file, and run the `ping` module. This playbook will confirm that you can successfully connect to all of the hosts and execute tasks on them:
```bash
ansible-playbook -v -i inventory.yml playbook.yml
```
You should see output similar to the following, indicating that Ansible was able to connect successfully to the hosts configured in the inventory file:

```text
<output omitted>
TASK [Show ping_info in console] ************************************************************************************
ok: [IP_OF_HOST_1] => {
    "msg": {
        "changed": false, 
        "failed": false, 
        "ping": "pong"
    }
}
ok: [IP_OF_HOST_2] => {
    "msg": {
        "changed": false, 
        "failed": false, 
        "ping": "pong"
    }
}
ok: [IP_OF_HOST_3] => {
    "msg": {
        "changed": false, 
        "failed": false, 
        "ping": "pong"
    }
}

PLAY RECAP **********************************************************************************************************
IP_OF_HOST_1                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
IP_OF_HOST_2                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
IP_OF_HOST_3                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```

### Using a Dynamic Inventory
The inventory file you have just created, with hardcoded host IPs, is called a _static inventory_. This is not particularly useful when configuring environments that have ephemeral infrastructure, with instances being constantly provisioned and deprovisioned. For such environments, you can instead use _dynamic inventories_ to automatically detect and group hosts within a cloud environment.  

Destroy the existing instances, and create new ones:
```bash
terraform destroy -var gcp_project=<project_id_from_qwiklabs> -var pubkey_path=$(pwd)/ansible_key.pub

terraform apply -var gcp_project=<project_id_from_qwiklabs> -var pubkey_path=$(pwd)/ansible_key.pub
```
This will create new instances with new IP addresses. 

Create a new file, `inventory.gcp_compute.yml`, with the following contents:
```yaml
plugin: google.cloud.gcp_compute
zones:
  - europe-west1-b
projects:
  - qwiklabs-gcp-XX-XXXXXXXXXXXX # replace with your qwiklabs project ID
filters:
  - status = RUNNING
  - scheduling.automaticRestart = true AND status = RUNNING
auth_kind: application
scopes:
  - 'https://www.googleapis.com/auth/cloud-platform'
  - 'https://www.googleapis.com/auth/compute.readonly'
keyed_groups:
  - prefix: gcp
    key: labels
name_suffix: .qa.com
hostnames:
  - name
compose:
  ansible_host: networkInterfaces[0].accessConfigs[0].natIP
```
This uses a dynamic inventory plugin to construct an inventory consisting of all VMs found in the specified zones for the specified projects. This configuration will also automatically group the detected hosts based on their labels. Verify that the new inventory can detect the new hosts:
```bash
ansible-inventory -i inventory.gcp_compute.yml --list
```
In order to execute playbooks against these dynamically detected hosts, there is one thing missing - the SSH configuration. Since this will not be included in the generated inventory, you will need to define this information somewhere else. One way to do this is via a config file, _ansible.cfg_, in the same directory as your inventory and playbook:
```ini
[defaults]
  remote_user=ansible
  private_key_file=~/Labs/ANS03/ansible_key

[ssh_connection]
  ssh_args="-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null"
```
TIP: use the below if there are errors:

```ini

[defaults]
  remote_user=ansible
  private_key_file=~/Labs/ANS03/ansible_key
  host_key_checking=False
  connection=ssh
  UserKnownHostsFile=/dev/null

[ssh_connection]
  ssh_args="-o StrictHostKeyChecking=no"

```
This defines the user and keyfile that were previously set in inventory.yml, and also disables host key checking. You can now use the dynamic inventory to run the same playbook as before against the new hosts:
```bash
ansible-playbook -i inventory.gcp_compute.yml playbook.yml
```

### Clean Up
Destroy the resources you have created:
```bash
terraform destroy -var gcp_project=<gcp project from qwiklabs> -var pubkey_path=$(pwd)/ansible_key.pub
```

## ANS04
# Lab ANS04 - Reusable Configuration Best Practices

## Lab Objectives
Parameterise Ansible configuration files and enhance the reusability of Ansible config.

## Learning Outcomes
By the end of this lab, you will have:
* Used variables and facts to parameterise an Ansible playbook
* Used Ansible's template module to dynamically alter the contents of a file
* Used handlers to make individual tasks repeatable on-demand
* Used a role to streamline the reuse of Ansible configuration 

## High-Level Steps

## Detailed Steps

### Deploy the Infrastructure
You can use the same terrform files from the previous lab to provision some target infrastructure for this lab. Change directory into ANS03 and apply the configuration:
```bash
cd ~/Labs/ANS03
terraform plan -var gcp_project=<gcp project from qwiklabs> -var pubkey_path=$(pwd)/ansible_key.pub

terraform apply -var gcp_project=<gcp project from qwiklabs> -var pubkey_path=$(pwd)/ansible_key.pub
```
Make a note of the server IPs and proxy IP outputs, as you will use these later

### Starting Point - Playbook, Inventory and NGINX Configs
Change into the ANS04 directory, and review the initial playbook state:
```yaml
---
- hosts: all
  become: true
  tasks:
  - name: Install NGINX
    apt:
      pkg: 
      - nginx
      - git
      state: latest
      update_cache: true
  - name: Start NGINX Service
    service:
      name: nginx
      state: started
- hosts: gcp_role_appserver
  become: true
  tasks:
  - name: 'update website from the git repository'
    git:
      repo: "https://gitlab.com/qacdevops/static-website-example"
      dest: "/opt/static-website-example"
  - name: 'install the nginx.conf file on to the remote machine'
    copy:
      src: nginx-server.conf
      dest: /etc/nginx/nginx.conf
  - name: Restart NGINX Service
    service:
      name: nginx
      state: restarted
- hosts: gcp_role_proxy
  become: true
  tasks:
  - name: transfer_nginx_conf
    copy:
      src: nginx-proxy.conf
      dest: /etc/nginx/nginx.conf
  - name: Restart NGINX Service
    service:
      name: nginx
      state: restarted
```
The logic here is:
* Install NGINX and git on all hosts
* Setup a static website on the appserver hosts, and supply a custom nginx.conf to serve it
* For the proxy, supply a custom NGINX config which will load balance between the appservers.

Edit **inventory.gcp_compute.yml** and fill in your project ID from qwiklabs:
```yaml
projects:
  - qwiklabs-gcp-XX-XXXXXXXXXXXX # <- edit this line
```
Edit lines 5 and 6 in the **nginx-proxy.conf** file and add the server IP addresses you noted earlier (Note: be careful to use the server IPs, NOT the proxy IP):
```conf
    upstream appservers {
        server SERVER_1_IP:8080; # <- edit this line
        server SERVER_2_IP:8080; # <- and this one
    }
```
Confirm the location of the private key file is the ANS03 folder in **ansible.cfg**:

```ini
  private_key_file=~/Labs/ANS03/ansible_key
``

Execute the playbook:
```bash
ansible-playbook -i inventory.gcp_compute.yml playbook.yml
```
Once the execution is complete, navigate to the proxy IP in a browser. You should be presented with the static website.  
Before moving on, destroy and recreate the infrastructure, so that you have a clean slate for the next part of the lab:
```bash
cd ~/Labs/ANS03
terraform destroy -var gcp_project=<gcp project from qwiklabs> -var pubkey_path=$(pwd)/ansible_key.pub

terraform apply -var gcp_project=<gcp project from qwiklabs> -var pubkey_path=$(pwd)/ansible_key.pub
```
When the apply is complete, note the new proxy IP.

### Improvements
So far, whilst this is a longer playbook than those you have used previously, everything you have done should be fairly familiar from previous activities. You will now improve upon the basic playbook using _variables_, _handlers_, _roles_ and _templates_:
* _Variables_ allow for parameterised execution of playbooks. The same playbook could be executed against the same set of hosts, with different parameters leading to possibly very different results. This ensures greater reusability of playbooks.
* _Handlers_ are tasks within a playbook that can be triggered on-demand by a notification from another task.
* _Roles_ are directories containing a collection of tasks and other resources which can then be referenced within playbooks, in order to streamline the re-use of complex configurations.
* _Templates_ are used to dynamically alter the contents of a file before copying to a remote host, allowing for reuse of config files.  

You will start by using some variables to parameterise the playbook. Open **playbook.yml** and edit lines 21 and 22 and replace the hardcoded information with variables:
```yaml
...
  - name: 'update website from the git repository'
    git:
      repo: "{{ repository_url }}" # <- edit this line
      dest: "{{ install_dir }}"    # <- and this one
...
```
Now the repository and the install directory are parameterised, you could potentially re-use this playbook to install any repository into any location on the target hosts.  
Next, you will reconfigure the nginx config files to act as templates. Starting with **nginx-server.conf**, edit the file contents to be as follows:
```conf
events {}
http {
    server {
        listen 8080;
        root {{ install_dir }}; # <- changed this line
        index index.html;
        include /etc/nginx/mime.types; 
        proxy_read_timeout  90;
        proxy_set_header X-Forwarded-Host $host:$server_port;
        proxy_set_header X-Forwarded-Server $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Real-IP $remote_addr;
    
        location / {
            try_files $uri /index.html;
        }
    }
}
```
Ansible templates will be rendered by the _Jinja2_ templating engine prior to transfer to the host, allowing for injection of dynamic parameters. The server config is a fairly simple template, referencing the same *install_dir* variable as in the playbook. You can also use templates to improve the proxy config. Replace the contents of **nginx-proxy.conf** with the following:
```conf
events {}

http {
    upstream appservers {
        {% for host in groups['gcp_role_appserver'] %}
        server {{ hostvars[host]['ansible_facts']['default_ipv4']['address'] }}:8080;
        {% endfor %}
    }
    server {
        listen 80;
        location / {
            proxy_pass http://appservers;
        }
    }
}
```
This is a slightly more complex template which uses a for-loop over the hosts in the gcp_role_appserver group to dynamically construct the upstream, using _facts_ about the hosts to retrieve the IP addresses.  
To use these templates effectively, you must also update the playbook replacing `copy` with `template` on lines 24 and 35.

The full playbook should then look like:
```yaml
---
- hosts: all
  become: true
  tasks:
  - name: Install NGINX
    apt:
      pkg: 
      - nginx
      - git
      state: latest
      update_cache: true
  - name: Start NGINX Service
    service:
      name: nginx
      state: started
- hosts: gcp_role_appserver
  become: true
  tasks:
  - name: 'update website from the git repository'
    git:
      repo: "{{ repository_url }}"
      dest: "{{ install_dir }}"
  - name: 'install the nginx.conf file on to the remote machine'
    template:
      src: nginx-server.conf
      dest: /etc/nginx/nginx.conf
  - name: Restart NGINX Service
    service:
      name: nginx
      state: restarted
- hosts: gcp_role_proxy
  become: true
  tasks:
  - name: transfer_nginx_conf
    template:
      src: nginx-proxy.conf
      dest: /etc/nginx/nginx.conf
  - name: Restart NGINX Service
    service:
      name: nginx
      state: restarted
```
Execute the playbook, passing in values for the variables:
```bash
cd ~/Labs/ANS04
ansible-playbook -i inventory.gcp_compute.yml playbook.yml -e "repository_url=https://gitlab.com/qacdevops/static-website-example install_dir=/opt/static-website-example"
```
Once execution is complete you should again be able to access the website by navigating to the proxy IP in a browser. 

Destroy and recreate the infrastructure once again, to give yourself a clean slate:
```bash
cd ~/Labs/ANS03
terraform destroy -var gcp_project=<gcp project from qwiklabs> -var pubkey_path=$(pwd)/ansible_key.pub

terraform apply -var gcp_project=<gcp project from qwiklabs> -var pubkey_path=$(pwd)/ansible_key.pub
```

### Handlers and Roles
You can now make a few more changes to your configuration to reduce the repetition and improve reusability. You will start by initialising 3 _roles_:
```bash
cd ~/Labs/ANS04
ansible-galaxy init common
ansible-galaxy init appserver
ansible-galaxy init proxy
```
A role defines a collection of tasks, templates, variables and other data which can then by used within a playbook without having to duplicate the config. You will configure the three roles to hold most of your configuration. Begin with the `common` role:  
Add the following to **common/tasks/main.yml**
```yaml
- name: Install NGINX
  apt:
    pkg: 
    - nginx
    - git
    state: latest
    update_cache: true
- name: Start NGINX Service
  service:
    name: nginx
    state: started
```
And add the following to **common/handlers/main.yml**
```yaml
- name: restart nginx
  service:
    name: nginx
    state: restarted
```
Next, you will edit the `appserver` role. Add the following to **appserver/tasks/main.yml**
```yaml
- name: 'update website from the git repository'
  git:
    repo: "{{ repository_url }}"
    dest: "{{ install_dir }}"
- name: 'install the nginx.conf file on to the remote machine'
  template:
    src: nginx-server.conf
    dest: /etc/nginx/nginx.conf
  notify: restart nginx
```
NOTE: The second task uses `notify` to instruct ansible to run the handler called 'restart nginx'.

When using roles, any templates referenced need to be located within the role directory as well, so *copy* the **nginx-server.conf** into the right location:
```bash
cp nginx-server.conf appserver/templates/nginx-server.conf
```
Next, add the following to **proxy/tasks/main.yml**
```yaml
- name: transfer_nginx_conf
  template:
    src: nginx-proxy.conf
    dest: /etc/nginx/nginx.conf
  notify: restart nginx
```
NOTE: This second task also uses `notify` to instruct ansible to run the handler called 'restart nginx'.

Copy the nginx-proxy.conf file to the correct location for ansible to find it:
```bash
cp nginx-proxy.conf proxy/templates/nginx-proxy.conf
```
Now with most of your configuration separated out into roles, the playbook can become much simpler. Back up your existing playbook first, for comparison:
```bash
cp playbook.yml playbook-old.yml
```
Then replace the contents of **playbook.yml** with the following:
```yaml
---
- hosts: gcp_role_appserver
  become: true
  vars:
    repository_url: "https://gitlab.com/qacdevops/static-website-example"
    install_dir: "/opt/static-website-example"
  roles:
  - common
  - appserver
- hosts: gcp_role_proxy
  become: true
  roles:
  - common
  - proxy
```
The variable values have been defined in a `vars` attribute so they will not need to be passed in at the command line when running the playbook.

Execute the playbook: 
```bash
ansible-playbook -i inventory.gcp_compute.yml playbook.yml
```
Navigate to the proxy IP in a browser and confirm the website is displayed.

Destroy the infrastructure:
```bash
cd ~/Labs/ANS03
terraform destroy -var gcp_project=<gcp project from qwiklabs> -var pubkey_path=$(pwd)/ansible_key.pub
```

## ANS05
# Lab ANS05 - Secret Management With Ansible-Vault

## Lab Objectives
Securely store and retrieve sensitive data using ansible-vault

## Learning Outcomes
By the end of this lab, you will have:
* Created a vault file to securely store variables
* Retrieved data from a vault during playbook execution

## High-Level Steps
* Deploy a sample app that requires authentication
* Use ansible to make an authenticated request to the app
* Store the credentials in a vault file
* Reconfigure the playbook to retrieve the credentials from the vault file

## Detailed Steps

### Deploy a Sample App
For this lab, you will be deploying a sample API which Ansible will make requests to. Begin by cloning the API:
```bash
cd ~
git clone https://github.com/qa-tech-training/example_python_flask_apiserver.git api
```
Next, install dependencies and start the API:
```bash
cd api
python3 -m venv venv
venv/bin/pip3 install -r requirements.txt
venv/bin/python3 app.py
```
Leave the API running in this terminal and open a new terminal tab. 

Change directory into ANS05 and create a new playbook:
```bash
cd Labs/ANS05
touch playbook.yml
```
Open **playbook.yml** in the editor, and add the following:
```yaml
---
- hosts: localhost
  connection: local
  name: Use Credentials
  tasks:
  - name: Make API Call
    uri:
      url: "http://localhost:5000/auth/tokens"
      method: "POST"
      url_username: "learner"
      url_password: "p@ssword"
      return_content: true
    register: result
  
  - name: print info
    debug:
      msg: "{{ result.content }}"
```
Execute the playbook:
```bash
ansible-playbook playbook.yml
```
You should see in the output a generated token, something like:
```
97506f8a1816434b5291a349f0dd5bd4574962ebf82f66505bccc87baf257ac3
```

### Secure the Credential
Having a hardcoded password in the playbook like this is a problem, especially if you want to share that playbook with others. Simply passing the value as a variable on the command line is not an ideal solution, as this leaves the sensitive credential potentially exposable via command history. Instead, a better approach would be to use _ansible-vault_ to encrypt the data at rest, and retrieve it during playbook execution.  
Create a new vault file:
```bash
ansible-vault create vault.yml
```
Once you have set a password on the vault, you will be presented with an editor. Add the following content:
```yaml
password: "p@ssword"
```
Then save and quit the editor. You now have a new vault file. Attempt to cat the contents:
```bash
cat vault.yml
```
You will see output similar to:
```
$ANSIBLE_VAULT;1.1;AES256
33313334323633626365616266313161636134343635313038396162666533376665666562323164
6361303938643739383338663631623538303933356630360a366666366661653866616537643761
66623737316632366435613435393666306661303536333236643335333062633063323531623533
3462653266643330370a656531326535616439633637666164376630646531366138623335663437
39333034343132326634376363363934323762316633393430383237363832626639
```
Demonstrating that the vault file has been encrypted.

### Update the Playbook
Edit **playbook.yml** so that the contents are as follows:
```yaml
---
- hosts: localhost
  connection: local
  name: Use Credentials
  vars_files: # added this line
  - vault.yml # and this line
  tasks:
  - name: Make API Call
    uri:
      url: "http://localhost:5000/auth/tokens"
      method: "POST"
      url_username: "learner"
      url_password: "{{ password }}" # edited this line to reference the password variable
      return_content: true
    register: result
  
  - name: print info
    debug:
      msg: "{{ result.content }}"
```
Re-run the playbook, but this time add the `-J` flag, which instructs ansible to prompt for the vault password:
```bash
ansible-playbook playbook.yml -J
```
You should again expect to see a token returned, if the request was successful.

### Stretch Task
By consulting the [documentation](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/uri_module.html) for the uri module, and reviewing the source code of the API, attempt to add another task to the playbook which uses the token received from the initial request to create a new book object through the API. See the solution below if needed.  

<details>
<summary>Show Stretch Task Solution</summary>

### Stretch Task Solution

```yaml
---
- hosts: localhost
  connection: local
  name: Use Credentials
  vars_files: 
  - vault.yml
  tasks:
  - name: Make API Call
    uri:
      url: "http://localhost:5000/auth/tokens"
      method: "POST"
      url_username: "learner"
      url_password: "{{ password }}" 
      return_content: true
    register: result
  - name: Add book
    uri:
      url: "http://localhost:5000/api/books"
      method: "POST"
      headers:
        Authorization: "Bearer {{ result.content }}"
      body_format: json
      body:
        title: "Example"
        author: "John Smith"
        genre: "scifi"
        id: "0000012345"
      return_content: true
    register: result2
  - name: print info
    debug:
      msg: "{{ result2 }}"
```
Run the playbook and make a curl request to http://localhost:5000/api/books to confirm your new book was created.

```shell
curl http://localhost:5000/api/books
```
Stop the API using CTRL+C

</details>

## CICD00
# Lab CICD00 - Environment Setup

## Lab Objectives
Launch the lab environment and configure required resources

## Steps
### Ensure Ansible is Installed
You will need access to ansible on the cloudshell instance in order to complete subsequent setup steps. A script to do this has been provided for the sake of convenience. Change directory into the CICD00 folder, and run the script:
```bash
cd ~/Labs/CICD00
chmod +x install_ansible.sh
./install_ansible.sh
```

### Setup Jenkins Server
You will need a Jenkins server to complete these labs. You will deploy and configure this using Terraform and Ansible. Still in the CICD00 directory, run the following:
```bash
export TF_VAR_gcp_project=<qwiklabs project id>
echo !! | tee -a ~/.bashrc

export TF_VAR_bucket=tf-remote-state-$USER-$RANDOM-$RANDOM
echo "export TF_VAR_bucket=$TF_VAR_bucket" | tee -a ~/.bashrc

gcloud storage buckets create gs://$TF_VAR_bucket --location=europe-west1

ansible 127.0.0.1 -m template -a "src=$(pwd)/main.tftemp dest=$(pwd)/main.tf" -e "bucket=$TF_VAR_bucket"

ansible 127.0.0.1 -m template -a "src=$(pwd)/inventory_template.yml dest=$(pwd)/inventory.gcp_compute.yml" -e "project_id=$TF_VAR_gcp_project"

ssh-keygen -t ed25519 -f ./ansible_key -q

terraform init
terraform apply -auto-approve

ansible-playbook -i inventory.gcp_compute.yml playbook.yml

cat outputs.txt
```

#### Setup Process Explained

```bash
export TF_VAR_gcp_project=<qwiklabs project id>
```
This step exports the qwiklabs project id as an environment variable. By prefixing the variable name with `TF_VAR_`, you also allow Terraform to automatically resolve this variable later on
```bash
echo !! | tee -a ~/.bashrc
```
This step echoes the last command (the variable export), and pipes the echo output to `tee`, which writes it to your .bashrc file. This means the variable will be set every time you start a terminal session.
```bash
export TF_VAR_bucket=tf-remote-state-$USER-$RANDOM-$RANDOM
echo "export TF_VAR_bucket=$TF_VAR_bucket" | tee -a ~/.bashrc

gcloud storage buckets create gs://$TF_VAR_bucket --location=europe-west1
```
This step creates another environment variable storing a randomly generated bucket ID, and again adds this export to your bashrc, before creating the cloud storage bucket which terraform will use as its state backend.
```bash
ansible 127.0.0.1 -m template -a "src=$(pwd)/main.tftemp dest=$(pwd)/main.tf" -e "bucket=$TF_VAR_bucket"

ansible 127.0.0.1 -m template -a "src=$(pwd)/inventory_template.yml dest=$(pwd)/inventory.gcp_compute.yml" -e "project_id=$TF_VAR_gcp_project"
```
This step uses ansible to template the project id and bucket id into the dynamic inventory file and the terraform configuration.
```bash
ssh-keygen -t ed25519 -f ./ansible_key -q
```
This step generates a new SSH key pair, which Terraform will inject into instance metadata, and which ansible can then use to connect to the VMs.
```bash
terraform init
terraform apply -auto-approve
```
Install required Terraform providers, and apply the configuration defined by `main.tf`: one VM instance, on the default network, with a firewall allowing access on ports 22 (for SSH) and 8080 (for Jenkins).
```bash
ansible-playbook -i inventory.gcp_compute.yml playbook.yml
```
Run the provided ansible playbook against the hosts identified by the dynamic inventory you templated earlier. The provided ansible playbook does the following things:
* installs some key dependencies for the rest of the installation
* installs Terraform on the Jenkins server
* adds the ansible repository and installs ansible on the Jenkins server
* adds the Jenkins package repository as a new package source, with corresponding public key for signature verification
* installs the Jenkins software and starts the service
* retrieves and writes out the automatically generated initial admin password, as well as the Jenkins server IP, to a file in the working directory
```bash
cat outputs.txt
```
This step displays the information you need to continue with the configuration.

### Complete Initial Jenkins Configuration
1. In a new browser tab, navigate to the URL displayed in the output of the last command. You should see a screen which says 'Unlock Jenkins'. 
2. In the input field, enter the initial admin password.  
3. On the next screen, choose `Install Suggested Plugins` and wait for the plugins to install.  
4. On the next screen, create the first admin user with the following details:
    * username: `jenkinsadmin`
    * password: `JenkinsP@ssw0rd!!`
    * full name: `Jenkins Admin`
    * email: admin@jenkins.org
5. Finally, on the URL configuration screen, click `Save and Finish`, then `Start using Jenkins`. You will be redirected to the Jenkins dashboard. 

## CICD01
# Lab CICD01 - Automating Terraform Activities

## Lab Objectives
Configure a Jenkins Pipeline to provision resources using Terraform

## Learning Outcomes
By the end of this lab, you will have:
* Created a Jenkins pipeline definition
* Configured a Jenkins job to respond to webhooks
* Configured Jenkins credentials

## High-Level Steps
* Set up a new repository with a Jenkinsfile and terraform config
* Configure Jenkins with access to google credentials
* Configure and build a Jenkins pipeline project
* Configure a webhook from GitHub to Jenkins

## Detailed Steps
### Create a Github repository 
On GitHub, create a new repository. Name it however you wish, leave it as public and check the option to add a README. Then click on `Create repository`.

Once the repository is created, click on `Add > Create new file`. In the editor that opens, paste in the contents of the Jenkinsfile from the CICD01 directory:
```groovy
pipeline {
    agent any
    environment {
        TF_VAR_gcp_project = "qwiklabs-gcp-XX-XXXXXXXXXXXX" // replace with your project ID ...
    }
    stages {
        stage('Terraform Init') {
            steps {
                withCredentials([file(credentialsId: 'gcp-svc-acct', variable: 'GOOGLE_CLOUD_KEYFILE_JSON')]) {
                    sh '''
                    export GOOGLE_APPLICATION_CREDENTIALS=$GOOGLE_CLOUD_KEYFILE_JSON
                    terraform init
                    '''
                }
            }
        }
        stage('Terraform Plan') {
            steps {
                withCredentials([file(credentialsId: 'gcp-svc-acct', variable: 'GOOGLE_CLOUD_KEYFILE_JSON')]) {
                    sh '''
                    export GOOGLE_APPLICATION_CREDENTIALS=$GOOGLE_CLOUD_KEYFILE_JSON
                    terraform plan
                    '''
                }
            }
        }
        stage('Terraform Apply') {
            steps {
                withCredentials([file(credentialsId: 'gcp-svc-acct', variable: 'GOOGLE_CLOUD_KEYFILE_JSON')]) {
                    sh '''
                    export GOOGLE_APPLICATION_CREDENTIALS=$GOOGLE_CLOUD_KEYFILE_JSON
                    terraform apply -auto-approve
                    '''
                }
            }
        }
    }
}
```
Change the `TF_VAR_gcp_project` variable to match your qwiklabs project ID. 

Name the new file `Jenkinsfile`, and click on `Commit changes`.  

Once you have committed the Jenkinsfile, click `Add > Create new file` again.  

Add the contents of **CICD01/main.tf** to this new file. Edit line 10 with your bucket ID from CICD00. 

Name the file `main.tf` and `Commit changes`. You now have a repository containing a simple terraform configuration and a Jenkins pipeline definition.
 
### Add Google Cloud Credentials to Jenkins 
In order to use Terraform on Jenkins to interact with Google Cloud, you must supply it with credentials to use, typically in the form of a service account json key file.  
1. In your Google Cloud console, navigate to `IAM & Admin > Service Accounts`. 
2. Select the Actions menu against your Qwiklabs service account and click `Manage Keys`
3. Select `Add key`, and `Create new key`, of type JSON. Click `Create` and save the key file tp a suitable location.
4. Return to the **Jenkins** dashboard and navigate to `Manage Jenkins` (the cog wheel in the top right)
5. Select `Credentials` under the Security section. 
6. Click `System`
7. Click on `Global credentials (unrestricted)`
8. Click on `Add Credentials` 
9. For Kind, choose `Secret File`
10. Select `Choose file`, browse to the json file you previously downloaded
11. For the ID, enter `gcp-svc-acct` (This is the ID being referenced in the Jenkinsfile)
12. Finally, click `Create`
13. Click the Jenkins logo (top keft) to return to the main Dashboard.

### Configure Pipeline Job 
1. Select  ` + New Item`  from the Jenkins dashboard 
2. Enter `Terraform Pipeline` as the item name 
3. Select `Pipeline` as the item type 
4. Click `OK` 
5. On the General page, scroll down to the Pipeline section. Use the dropdown list to change the Definition from "Pipeline script" to `Pipeline script from SCM`
6. Select `Git` from the SCM dropdown list 
7. In the Repository URL: Enter your Github repository URL
8. In "Branches to build," "Branch Specifier;" change from */master to `*/main` 
9. Click `Save`
10. Click `Build Now`

### Verify pipeline run and explore Jenkins 
1. In Jenkins, return to the Dashboard. A record of the pipeline will be displayed showing run success / failure. Refresh the page until a result of the pipeline run is displayed 
2. Select the drop-down menu against `#1` and choose `Pipeline Overview`.
3. The stages of the pipeline are shown, with ticks (or crosses) indicating success or failure at that stage 
4. Spend a little time exploring the Jenkins interface, using the bread-crumb menu to navigate around, and finally return to the main Dashboard
5. In Google Cloud, navigate to the `VPC Network` and verify the existence of your new network: `example-vpc`.

### Updating the transformation pipeline 
Now that you have the pipeline set up, in principle a team of people could then collaborate on this codebase, and allow Jenkins to manage the use of Terraform without having to manually run terraform init/plan/apply.  
This would, however, necessitate a different approach to triggering the pipeline. Nobody wants to be the person whose full-time job it is to watch GitHub for changes and click 'build now' every time they see one. Instead, you will set up a _webhook_ - a notification that GitHub can send to Jenkins to inform Jenkins of the latest changes, which Jenkins can then automatically checkout and build.
1. Return to your Github repository overview
2. Click on the `Settings` tab for the repository, and find the `Webhooks` option in the side menu
3. Click on `Add Webhook`, and reauthenticate if prompted
4. Configure the webhook as follows:
    * payload URL: http://<Jenkins_IP>:8080/github-webhook/ 
    
    (note: the trailing slash `/` on this URL is significant - don't miss it out)
    * content type: application/json
    * secret: leave blank
5. Leave other settings as their defaults, click `Add webhook`

So far you have configured GitHub to notify Jenkins every time there is a change to the repo. You still have not, however, indicated that Jenkins should care about these notifications.

6. In Jenkins, configure the pipeline by first selecting it on the main dashboard and then choosing the `Configure` option
7.  Scroll down to the `Build Triggers` section, select `GitHub hook trigger for GITScm polling` and click on **Save**
8. Return to the GitHub repository
9. Open **main.tf** for editing
10. Change the name of the VPC to be created. This will cause the original VPC to be deleted and a new one to be created. Commit this change.
11. Switch to Jenkins and observe that a build should have started automatically
12. The build Executor Status will show the progress of the run.
13. Click to the right of the run number and from the drop-down, select `Console Output `
14. Scroll down to confirm the delete/recreate operation is successful. 
15. Switch to your cloud console and verify the creation of the new VPC.

### Configure Destroy Pipeline Job 
1. Select  ` + New Item`  from the Jenkins dashboard 
2. Enter `Terraform Pipeline Destroy` as the item name 
3. Select `Pipeline` as the item type 
4. Click `OK` 
5. On the configuration page, scroll down to the Pipeline section. This time, leave the setting as `Pipeline script`. In the editor in Jenkins, add the contents from **CICD01/destroy/Jenkinsfile**, changing the 3 environment variables appropriately (project / bucket /repo)
6. Save the configuration and `Build Now` to run the pipeline 
7. This Jenkinsfile mandates that approval must be granted for the deletion to proceed.  
8. Click on the pipeline and under Builds, select the running Terraform Pipeline Destroy job and select `Console Output`.
9.  The run is waiting for approval to continue. Click on `Yes, Destroy` to confirm the deletion.
10. The destruction should now proceed. Switch to the console to verify the deletion of your VPC.

#### Rationale for the destroy job
There are some differences in the way you have configured this second pipeline. They are not accidental. The use of 'pipeline script' instead of 'pipeline script from SCM' means that this job will NOT be automatically triggered by the existing webhook. This means that you can safely make changes to the source repo and have terraform create and update resources, and only tear everything down when you manually instruct Jenkins to do so.  

This Jenkins pipeline also includes an extra stage to checkout the repository. This was not necessary in the previous Jenkinsfile as Jenkins will check the repository out anyway in order to read the Jenkinsfile. But as this job is not reading the Jenkins pipeline from source control, you need to explicitly checkout the repository.  

This pipeline also uses an input gate to pause execution and wait for approval. This allows you to review the actions that terraform intends to take (as output by the `terraform plan -destroy` step) and either confirm that you definitely intend to destroy everything, or bail out and abort the pipeline.

## CICD02
# Lab CICD02 - Continuous Delivery With Ansible and Terraform

## Lab Objectives
To implement a CD pipeline which will automate the deployment of a sample website

## Learning Outcomes
By the end of this lab, you will have:
* Created a Jenkins pipeline which uses Terraform and Ansible to automate a deployment
* Configured a Webhook to automatically trigger builds on code changes

## High-Level Steps
* Fork the starter repo
* Add a Jenkinsfile which defines a continuous delivery pipeline
* Configure Jenkins to execute the pipeline in response to a webhook trigger

## Detailed Steps

### Fork the Starter Repo
1. Sign into your [github](https://github.com) account if you are not already signed in. 
2. Once you are logged in, navigate to the [starter repo](https://github.com/qa-tech-training/sample-awx-project), and click the 'fork' button in the top right corner.

```
NOTE: when forking the repository, make sure to uncheck 'copy the main branch only', as you will need both branches for this activity.
```

3. Wait a few seconds until the fork is complete.

#### Understanding the Repo Layout
This starter repo is derived from the same sample website codebase you have already worked with, however it is structured slightly differently. The repo has two branches: a `main` branch containing the website source plus ansible and terraform files, and a `deploy` branch containing just the website source. This is one commonly-used git branching strategy, handy for keeping release-ready content independent of development/build activities. 

### Add a Jenkinsfile
In GitHub, add a new file to your repository. Name it `Jenkinsfile` (note the capitalisation - this is important). Copy the contents from **CICD02/Jenkinsfile** into it. Make the following changes to the environment variables:
* TF_VAR_gcp_project: replace the placeholder value with your qwiklabs project ID
* TF_VAR_bucket: replace the placeholder value with your bucket ID
* REPOSITORY: fill in your GitHub username in the URL and the repo name sample-awx-project

Once you have made these changes, commit the file.

### Set Jenkins Credentials
This pipeline can re-use the service account key file from the previous lab, so this credential is fine. As you are using Ansible in this pipeline you will also need an SSH key:
1. In cloudshell, generate a new key pair:
```bash
cd ~
ssh-keygen -t ed25519 -f ./jenkinskey -q
```
2. In the explorer, right-click the new `jenkinskey` file, and download it. Open the downloaded file in Notepad
3. In Jenkins, navigate to `Manage Jenkins > Credentials > System > Global credentials`
4. Create a new credential, of type 'SSH Username with private key':
    * for the username, enter 'ansible'
    * for the key material click `enter directly` > 'Add`, then copy and paste the key data from Notepad
    * set the credential ID to be 'SSH_KEY'
5. Click `Create`

### Configure A Project
1. Return to the Jenkins dashboard and click 'New Item'
2. Name the project `jenkins-tf-ansible`, and select a type of 'Pipeline'
3. In the build triggers section of the configuration, check 'GitHub Hook Trigger for GitSCM Polling'
4. In the Pipeline section of the configuration, and change 'pipeline script' to `Pipeline script from SCM`. Configure the following settings:
    * SCM: git
    * repository: your repo URL for the sample-awx-project
    * branch specifier: */main
    * script path: Jenkinsfile
5. Save the configuration

### Build the Project
1. On the project overview, click 'Build Now' to trigger a manual build. The job will take several minutes to complete. While you are waiting, review the pipeline [explanation](#pipeline-explanation) below for a better understanding of what is happening.
2. Once the execution is complete, navigate to the compute engine overview and confirm the creation of three instances.
3. In a new browser tab, navigate to the External IP of the proxy server. You should see the same sample website now deployed automatically through a Jenkins pipeline.

### Setup a Webhook
1. Set up a webhook on your GitHub repo. Refer to the relevant step of the previous lab if you need guidance on how to do so.
2. Once the webhook is configured, experiment with changing the number of appservers via the `count` parameter in **main.tf**. Any change should automatically trigger a new pipeline build which will deploy and configure any additional servers required.


#### Pipeline Explanation
The pipeline begins, after checking out the repository, by using the SSH private key you saved as a credential to reconstruct the corresponding public key. Terraform will need this to be available so that it can be injected into the instance metadata. The pipeline then performs a terraform init, plan and apply to build the infrastructure required. 

Once the infrastructure is provisioned, the pipeline switches into the ansible directory. It uses the ansible template module to inject the correct project ID into the dynamic inventory, then executes the playbook against this inventory. The playbook checks out the 'deploy' branch of the repository onto the appserver instances. Recall that the deploy branch contains _only_ the release-ready website source, so this avoids unnecessarily cloning the ansible/terraform code onto the servers.

## CICD03
# Lab CICD03 - Scheduling Ansible Jobs With AWX

## Lab Objectives
Create a job in AWX to manage scheduled ansible executions

## Learning Outcomes
By the end of this lab, you will have:
* Deployed an AWX cluster
* Configured credentials in AWX
* Configured an AWX job to run scheduled maintenance activities

## High-Level Steps
* Deploy AWX
* Add an SSH key as a credential to AWX
* Create and run an AWX job

## Detailed Steps

### Deploy AWX
1. In your cloudshell, navigate to the **CICD03** directory
2. Initialise Terraform and apply the configuration:
```bash
cd ~/Labs/CICD03

ansible 127.0.0.1 -m template -a "src=$(pwd)/main.tftemp dest=$(pwd)/main.tf" -e "bucket=$TF_VAR_bucket"

terraform init
terraform apply -auto-approve
``` 
3. The init script takes several minutes to run, after which AWX will need another several minutes to fully initialise. Take the time to review the [explanation](#the-awx-init-script-explained) of what the script is actually doing
4. After waiting for several minutes, navigate to `http://<awx_ip>:30080` in a new browser tab. If AWX is still configuring, wait longer.
5. Once AWX is finally ready, log in with the following credentials:
    * username: admin
    * password: ChangeMe123!

You should now see the AWX dashboard.

### Create an AWX Job
1. In the AWX dashboard, create a new organisation:
    * Click on the Organizations under Access from the left side panel
    * Click Add to Create New Organization
    * When prompted to enter an Organization Name, enter `BOAAWX`
    * Click on Save
2. Return to the AWX dashboard and select Resources > Projects > Add
3. Configure the project as follows:
    * name: `website-updates` 
    * Organization: `BOAAWX` 
    * Source Control Type: `Git`
    * Source Control Type: `https://github.com/<your-github-username>/sample-awx-project.git`
4. Save.

### Add SSH Credentials
To be able to connect to your machines, AWX will need access to the private SSH key that corresponds to the public key used to build the infrastructure.
1. Navigate to Resources > Credentials, click 'Add'
2. Configure the new credential as follows:
    * name: `ansible_ssh_key`
    * organization: `BOAAWX`
    * credential type: `machine`
    * username: `ansible`
    * SSH Private Key: paste in the same key material you gave to Jenkins earlier
    * privilege escalation method: `sudo`
    * privilege escalation password: leave blank
3. Save the credential

### Add an Inventory
1. Navigate to `Resources > Inventory` and click `Add` > `Add Inventory`
2. Name the inventory **webservers**, and associate it to the BOAAWX organization, and Save

You will keep things simple with a static inventory for now, but this could be dynamic via an inventory source.

3. Click on the `Hosts` tab
4. Add a host by clicking `Add`. Enter the IP of one of your servers (NOT the Jenkins or AWX VMs, only the servers created by your pipeline). Add the VM name in the Description box e.g. app-server-0.
5. Repeat for the other app servers and the proxy server
6. Once you have added all your hosts, you need to group them, following the pattern gcp_role_<role>. From the inventory overview, click on `Groups > Add`. Name the new group **gcp_role_appserver** and Save. 
7. Click on Hosts > Add, and select `Add existing host`. Then choose all of the IPs that correspond to your appserver instances
8. Create another new group called **gcp_role_proxy**. Repeat the process to add existing hosts, this time selecting the IP of your proxy server.

### Create a Job Template
1. Navigate to `Resources > Templates`, and click `Add` > `Add job template`.
2. Configure the following:
    * NAME: deploy site
    * DSECRIPTION : Ensure site is deployed at latest version
    * JOB TYPE: Run 
    * INVENTORY: webservers
    * PROJECT: website-updates
    * PLAYBOOK: ansible/playbook.yml
    * CREDENTIALS: ansible_ssh_key
    * VARIABLES: repository_url: <your repo url>
    e.g. `https://github.com/<your-github-username>/sample-awx-project.git`
3. Save the template configuration, then Launch the job manually to test connectivity

### Create a Schedule
As with Jenkins, manually triggering job executions is not the preferred way to run AWX jobs. AWX is excellent for scheduling jobs, to be run at specific times.
1. Select your job template, edit it and select `schedules`
2. Configure the schedule so that the job runs every day at midnight. 


#### The AWX Init Script Explained
The primary distribution mechanism for AWX is as a Kubernetes operator. Detailed understanding of Kubernetes is beyond the scope of this course, but in short it is the de-facto standard orchestration platform for containerised workloads. Running AWX through Kubernetes allows for highly available, scalable deployments of the workloads needed to execute AWX jobs. To set up AWX, the init script does the following:
* installs and configures _docker_, a common container management tool
* installs *K*ubernetes-*in*-*D*ocker (KinD), a tool for running a Kubernetes cluster as a set of containers
* Creates a KinD cluster with appropriate ports mapped
* Deploys the AWX operator and associated resources into the KinD cluster
[back to instructions](#deploy-awx)

## CICD04
# Lab CICD04 - Credential Management With Vault

## Lab Objectives
Deploy Hashicorp Vault and use it to store credentials

## Learning Outcomes
By the end of this lab, you will have:
* Deployed HashiCorp Vault in development mode
* Created a Vault secret
* Configured AWX to retrieve a secret from the vault

## High-Level Steps
* Deploy Hashicorp vault
* Move an SSH key into the vault
* Reconfigure AWX jobs to read credentials from the vault

## Detailed Steps

### Deploy Vault
1. In cloudshell, switch into the **CICD04** directory, and run the following to deploy a vault server:
```bash
cd ~/Labs/CICD04

ansible 127.0.0.1 -m template -a "src=$(pwd)/main.tftemp dest=$(pwd)/main.tf" -e "bucket=$TF_VAR_bucket"

terraform init
terraform apply -auto-approve
```
2. Make a note of the output `vault_ip` value, and also the root token that this vault installation has been deployed with: `example-vault-token-1234`. You will use these in the next step.

### Store a Credential
You will start by storing the private SSH key that Jenkins and AWX have been using for ansible jobs. Run the following in cloudshell:
```bash
export VAULT=<your vault IP>
export VAULT_TOKEN=example-vault-token-1234

cd ~/Labs/CICD04

chmod +x generate_post_data.py

./generate_post_data.py $HOME/jenkinskey

curl \
    -H "X-Vault-Token: $VAULT_TOKEN" \
    -H "Content-Type: application/json" \
    -XPOST \
    -d@data.json \
    http://$VAULT:8200/v1/secret/data/ansible-ssh-key
```
Verify the credential creation:
```bash
curl \
    -H "X-Vault-Token: $VAULT_TOKEN" \
    http://$VAULT:8200/v1/secret/data/ansible-ssh-key > secrets.json

cat secrets.json
```

### Configure AWX to Retrieve Credentials from Vault
1. Return to your AWX dashboard and navigate to the credentials overview.
2. Create a new credential with the following configuration:
    * name: `vault_credential`
    * type: `HashiCorp Vault Secret Lookup`
    * URL: `http://<your vault server IP>:8200`
    * Token: `example-vault-token-1234`
    * API version: `v1`
3. Save this credential and return to the credentials overview
4. Edit the configuration for the credential you created earlier `ansible_ssh_key`:
    * Replace the existing SSH private key data, and click the key icon to use an external source
    * Use the credential you just created as the secret lookup credential `vault_credential`
    * path to secret: `/secret/data/ansible-ssh-key`
    * key name: `sshkey`
5. Return to the job template you defined earlier and trigger a new execution to test the connectivity with the key pulled from vault.

## VAL01
# Lab VAL01 - API Validation and OpenAPI

## Lab Objectives
The objective of this lab is to generate an OpenAPI spec for an API and execute acceptance tests against an API

## Learning Outcomes
By the end of this lab, you will have:
* Used swaggo to generate OpenAPI specs for an API
* Created a test suite based on that spec
* Executed acceptance tests

## High-Level Steps
* Obtain the source code
* Generate an OpenAPI spec
* Create a bruno collection from the OpenAPI spec
* Implement and execute tests

## Detailed Steps

### Generate OpenAPI Specs
1. Change directory into the **VAL01** directory:
```bash
cd ~/Labs/VAL01
```
2. Review the **main.go** file. Notice the comments. This is a specific doc comment format used by the swaggo/swag tool, which you will use shortly.
3. Run `go mod tidy` to install current dependencies and initialise go.sum
4. Install swaggo/swag:
```bash
go install github.com/swaggo/swag/cmd/swag@latest
```
5. Once the tool is installed, run `swag init` to generate the OpenAPI specs
6. Review the generated documentation @ docs/swagger.json

### Setup Bruno Collection
Bruno is an API test runner, similar in capabilities to Postman. You will use Bruno over postman as it does not require a postman cloud account, and works entirely locally.
1. Bruno is developed in JS, and can be installed easily via npm:
```bash
 npm i -g @usebruno/cli
 ```

2. Wait for the installation to complete, then use bruno to generate a 'collection' from the openapi spec you generated:
```bash
bru import openapi --source docs/swagger.json -o bruno -n "Example Collection"
```
This will create a new directory, bruno, which contains several .bru files. You will need to edit these to make them do anything interesting.

3. Open **`Manage_Tasks (POST).bru`** and amend the contents like so:
```
meta {
  name: Manage_Tasks (POST)
  type: http
  seq: 1
}

post {
  url: {{baseUrl}}/tasks
  auth: inherit
}

body {
  {
    "id": "ae3f1c",
    "title": "Task1",
    "description": "A sample task",
    "completed": false
  }
}

tests {
  test("should have created the task", function() {
    expect(res.body.id).to.equal("ae3f1c");
  });
}
```
4. Amend the other files similarly.

**`Manage_Tasks.bru`**:
```
meta {
  name: Manage_Tasks
  type: http
  seq: 2
}

get {
  url: {{baseUrl}}/tasks
  body: none
  auth: inherit
}

tests {
  test("should return items", function() {
    expect(res.body).to.be.an('object');
  });
}
```
**`Manage_Tasks (PUT).bru`**
```
meta {
  name: Manage_Tasks (PUT)
  type: http
  seq: 3
}

put {
  url: {{baseUrl}}/tasks
  auth: inherit
}

body {
  {
    "id": "ae3f1c",
    "title": "Task1",
    "description": "A sample task",
    "completed": true
  }
}

tests {
  test("should have updated the task", function() {
    expect(res.body.completed).to.equal(true);
  });
}
```
**`Manage_Tasks (DELETE).bru`**
```
meta {
  name: Manage_Tasks (DELETE)
  type: http
  seq: 4
}

delete {
  url: {{baseUrl}}/tasks
  auth: inherit
}

body {
  {
    "id": "ae3f1c",
    "title": "Task1",
    "description": "A sample task",
    "completed": true
  }
}

tests {
  test("should have deleted the task", function() {
    expect(res.body).to.not.have.property("ae3f1c");
  });
}
```
5. Once you have made the changes, do a go mod tidy and then start the app and use bruno to execute the defined tests:
```bash
go mody tidy 
go run main.go  &
cd bruno

bru run --env-var baseUrl=http://localhost:8080 --reporter-json results.json
```

6. All 4 tests should PASS.

## VAL02
# Lab VAL02 - Terraform Validation

## Lab Objectives
The aim of this lab is to apply validations to terraform configurations using policy-as-code

## Learning Outcomes
By the end of this lab, you will have:
* Used terrascan to validate that terraform configs adhere to common standards of security
* Created a custom terrascan policy and validated it
* Used Sentinel to implement a custom policy in HCL

## High-Level Steps
* Install terrascan
* Run terrascan with default policies
* Create and validate a custom policy
* Install HashiCorp Sentinel
* Define and validate a sentinel policy

## Detailed Steps

### Install terrascan
1. Terrascan can be installed via the following commands:
```bash
curl -L "$(curl -s https://api.github.com/repos/tenable/terrascan/releases/latest | grep -o -E "https://.+?_Linux_x86_64.tar.gz")" > terrascan.tar.gz

tar -xf terrascan.tar.gz terrascan && rm terrascan.tar.gz

sudo install terrascan /usr/local/bin && rm terrascan
```
2. Confirm the installation:
```bash
terrascan
```
3. Change directory into the VAL02 directory: `cd ~/Labs/VAL02`

4. Review the provided terraform files. They should be familiar to you as you have worked with them in an earlier lab (TF05)

5. To validate the files against terrascan's default 
policies, run:

```bash
 terrascan scan
 ```

6. Notice that the scan is successful, but there are several warnings about files not being present. Terrascan is actually a portable policy enforcement tool which supports a wide range of configurations, not just terraform.

7. Re-run the scan command, but this time add `-i terraform` to specify that only terraform validations should be attempted:
```bash
terrascan scan -i terraform
```
8. You can further filter the validations that are applied by using the `-t` flag to specify that you want the validations relevant to a specific provider:
```bash
terrascan scan -i terraform -t gcp
```
9. You can also define your own custom policies for terrascan. To create a terrascan policy you need two things: a _rule file_ and the _validation logic_. The validation logic is defined using a syntax called 'rego'.

10. Create a new file called **`ssh_rule.rego`** and add the following:
```rego
package accurics

tcp_port_22_open[api.id] {
    api := input.google_compute_firewall[_]
    rule := api.config.allow[_]
    port := rule.ports[_]
    contains(lower(port), "22")
    api.config.direction == "INGRESS"
    api.config.source_ranges[_] == "0.0.0.0/0"
}
```
This rego file checks for any firewall rules that expose port 22 (SSH) to all IP addresses. This is something that in the real world you would likely want to restrict.

11. A rule file is a JSON file that defines metadata about the rule. Create a file called **`AC_GCP_1000.json`** and add the contents below, which reference the rego validation file you created:
```json
{
	"name": "nopublicssh",
	"file": "ssh_rule.rego",
	"policy_type": "gcp",
	"resource_type": "google_compute_firewall",
	"severity": "HIGH",
	"description": "It is recommended that no security group allows unrestricted SSH access",
	"category": "NETWORK_SECURITY",
	"version": 1,
	"id": "AC_GCP_1000"
}
```
12. Run terrascan using the _-p_ flag to specify the path to the directory that contains the rules:
```bash
terrascan scan -i terraform -t gcp -p .
```
Note that the output shows only one validation performed this time.

### Policy Enforcement With Sentinel
HashiCorp, the organisation behind terraform, offer their own policy-as-code solution in the form of _Sentinel_. You will compare and contrast with Terrascan.
1. Install Sentinel on the cloud shell instance:
```bash
wget https://releases.hashicorp.com/sentinel/0.40.0/sentinel_0.40.0_linux_amd64.zip

unzip sentinel_0.40.0_linux_amd64.zip

sudo cp sentinel /usr/local/bin

sentinel version
``` 
2. Create a new sentinel file called **`enforce_cidr_restrictions.sentinel`**, adding the following contents:
```sentinel
import "tfplan/v2" as tfplan
import "strings"
import "types" 

allowed_source_ranges = [
  "10.0.1.0/24",      # own subnet
  "130.211.0.0/22",   # Specific external network
  "35.191.0.0/16",     # Google Cloud Health Check ranges (important for ingress!)
]

all_firewall_rules_valid = rule {
  allowed_source_ranges contains "10.0.1.0/24"
}

# Enforce the rule
main = rule {
  all_firewall_rules_valid
}
```
3. Sentinel works on terraform plan data, so in order to see this policy in action you have to do a little more work. Generate a plan file by running:
```bash
pip3 install sentinel-mock-plan

terraform init

terraform plan -out mock.tfplan

terraform show -json -no-color mock.tfplan > mock.json

python3 -m sentinel_mock_plan --infile mock.json --outfile mock-tfplan.sentinel
```
4. Now define sentinel configuration settings, in a file called **`sentinel.hcl`**:
```
mock "tfplan/v2" {
  module {
    source = "mock-tfplan.sentinel"
  }
}
```
Now when sentinel runs, the generated plan will be picked up as a mock of the data structure that HashiCorp Cloud feeds into sentinel automatically when using terraform cloud

5. Invoke the policy with sentinel apply:
```bash
sentinel apply enforce_cidr_restrictions.sentinel
```
The policy should pass.

6. Edit `enforce_cidr_restrictions.sentinel`, and change "10.0.1.0/24" to "0.0.0.0/0" to the allowed_source_ranges.

7. Run `sentinel apply enforce_cidr_restrictions.sentinel` again. Sentinel should pick up the 'misconfiguration' and fail the validation.

#### Comparison of Sentinel and Terrascan
Considering the differences between Sentinel and Terrascan, Terrascan has the benefits of being usable with other forms of configuration and being easier to get started with out of the box thanks to the default library of policies. Sentinel, however, benefits from strong integration with Terraform Cloud/Enterprise, and the use of HCL means a more familiar syntax for those familiar with terraform already.

----------
## TIDY UP
Manually remove any existing infrastructure created in previous labs.

Delete your app-servers and your proxy-server.

Delete the lab4-vpc network.

--------

 
## VAL03
# Lab VAL03 - Monitoring With Zabbix

## Lab Objectives
Deploy a set of resources with Zabbix deployed for monitoring

## Learning Outcomes
By the end of this lab, you will have:
* Deployed infrastructure with Terraform and configured Zabbix on the infrastructure using Ansible

## High-Level Steps
* Provision infrastructure
* Configure with ansible
* Configure Host in Zabbix UI

## Detailed Steps

### Provision the Infrastructure
1. Change directory into the lab folder:
```bash
cd ~/Labs/VAL03
```
2. Begin by reviewing the Ansible configuration files. Much of it should be familiar by now, but there is some new configuration, namely the use of `docker` to deploy the Zabbix components.

3. Switch into the terraform directory, which contains the config needed to build the infrastucture.

4. Generate an SSH key pair:
```bash
ssh-keygen -t ed25519 -f ./ansible_key -q
```
5. Export the information that Terraform needs as environment variables:

```bash
gcloud config set project <your project id>

export TF_VAR_gcp_project=$(gcloud config get project)
export TF_VAR_pubkey_path=$(pwd)/ansible_key.pub
```
6. Run a terraform init and apply:
```bash
terraform init
terraform apply
```
7. Wait for the resources to be provisioned

### Configure the Servers
1. Now you need to configure your servers. Confirm ansible is installed:

```bash
ansible --version
```
If _not_, run the following commands:

```bash
sudo apt-get install -y software-properties-common
sudo apt-add-repository ppa:ansible/ansible
sudo apt-get install -y ansible
```
2. Navigate into the `ansible` directory and update the project ID in the inventory template:
```bash
cd ansible

ansible 127.0.0.1 -m template -a "src=$(pwd)/inventory_template.yml dest=$(pwd)/inventory.gcp_compute.yml" -e "project_id=${TF_VAR_gcp_project}"
```

3. Set the **private_key_file** value in `ansible.cfg` to `../terraform/ansible_key`:
```ini
[defaults]
  remote_user=ansible
  private_key_file=../terraform/ansible_key

[ssh_connection]
  ssh_args="-o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null"

```
4. Execute the playbook:
```bash
ansible-playbook -i inventory.gcp_compute.yml playbook.yml
```
5. It will take a few minutes to fully configure all resources.

### Log into Zabbix UI and Configure Hosts
1. Although the Zabbix agent is running on the servers being monitored, Zabbix will not register them automatically, so you will need to configure host entries in Zabbix itself. 

2. Navigate to the external IP of your Zabbix server, and you should be confronted by a login screen (note: as with AWX, some errors during the initialisation process are normal, so wait a few minutes until the server is ready)

3. The default superuser credentials for zabbix are username: `Admin`, password: `zabbix`. Log in with these credentials

4. Once you are logged in, from the side menu navigate to `Data Collection > Host groups`. Use the `Create host group` button (top-right) to create a new group called **appservers**

5. Repeat to create a group called **proxies**

6. Navigate to `Data Collection > Hosts` and use the `Create host` button (top right) to add a new host. 
    * Host name: `app-server-0`
    * Templates: `Templates/Operating systems > Linux by Zabbix agent`
    * Host groups: select `appservers`
    * `Add` an interface: type `Agent`, IP Address: `VM Internal IP value`

7. Repeat for `app-server-1`

8. Repeat for `proxy-server`:
    * Host name: `proxy-server`
    * Templates: `Templates/Operating systems > Linux by Zabbix agent`
    * Host groups: select `proxies`
    * `Add` an interface: type `Agent`, IP Address: `VM Internal IP value`

9. There should now be communication between the zabbix server and agents. Navigate to `Dashboards`. It may take some time for the first data to start being populated.

10. Try increasing the load on the servers by spamming the proxy with curl requests from your cloudshell:
```bash
while true; do wget -O- http://<proxy-ip>; done
``` 
See if you notice any evidence of the work the servers are doing in the Zabbix dashboard.

11. When you are finished exploring the Zabbix dashboards, stop the curl spam. In the Cloud Shell terminal: 
```bash
CTRL + c
```

-------
