# VPC- VIRTUAL PRIVATE CLOUD

VPC is a virtualized network environment within GCP that allows you to define and control network resources such as IP addresses, subnets, firewalls, and routes. It provides a private network for your GCP resources, similar to an on-premises network.

## Subnets 
Segments of the VPC network. They are regional resources and can span multiple zones within a region.

## VPC Network and Firewall

The following Terraform code sets up a VPC network, a subnet within that network, and a firewall rule:

```hcl
provider "google" {
  credentials = file("C:/Users/vharshini/OneDrive - Sopra Steria/GCP LANDING ZONE/creds.json")
  project     = "harshini-poc"
  region      = "us-central1"
}

# Define the VPC network
resource "google_compute_network" "my_vpc_network" {
  name                    = "harshinivpc"
  auto_create_subnetworks = false
}

# Define the subnet within the VPC network
resource "google_compute_subnetwork" "my_subnet" {
  name          = "har-subnet"
  ip_cidr_range = "10.0.0.0/24"
  network       = google_compute_network.my_vpc_network.id
  region        = "us-central1"
}

# Define a firewall rule to allow traffic (e.g., allow all traffic from within the VPC)
resource "google_compute_firewall" "allow_all" {
  name    = "allow-all"
  network = google_compute_network.my_vpc_network.id

  allow {
    protocol = "tcp"
    ports    = ["0-65535"]
  }

  allow {
    protocol = "udp"
    ports    = ["0-65535"]
  }

  allow {
    protocol = "icmp"
  }

  source_ranges = ["10.0.0.0/24"]
}



