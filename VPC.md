# VPC- VIRTUAL PRIVATE CLOUD

VPC is a virtualized network environment within GCP that allows you to define and control network resources such as IP addresses, subnets, firewalls, and routes. It provides a private network for your GCP resources, similar to an on-premises network.

## Subnets 
Segments of the VPC network. They are regional resources and can span multiple zones within a region.

## V-net peering
VPC Peering enables you to connect two VPC networks so that resources in one network can communicate with resources in another, using internal IP addresses. This connection is established through a private, direct link.

# Terraform Configuration for GCP

## VPC Network, Subnet, and Firewall Rule

The following Terraform code sets up a Google Cloud VPC network, a subnet within that network, and a firewall rule:

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



# Terraform Configuration for VPC Network Peering

The following Terraform code sets up the Google Cloud provider and configures VPC network peering between two VPC networks:

```hcl
# Define the Google Cloud provider
provider "google" {
  credentials = file("C:/Users/vharshini/OneDrive - Sopra Steria/GCP LANDING ZONE/creds.json")
  project     = "harshini-poc"
  region      = "us-central1"  # Replace with your desired region
}

# Define the network peering between the existing VPC networks
resource "google_compute_network_peering" "vpc_peering" {
  name                                = "vpc-network-1-to-vpc-network-2"
  network                             = "projects/harshini-poc/global/networks/harshinivpc"  # First VPC network
  peer_network                        = "projects/harshini-poc/global/networks/defaults"      # Second VPC network
  export_custom_routes                = true
  export_subnet_routes_with_public_ip = true
  import_custom_routes                = true
  stack_type                          = "IPV4_ONLY"
}

resource "google_compute_network_peering" "vpc_peering_2" {
  name                                = "vpc-network-2-to-vpc-network-1"
  network                             = "projects/harshini-poc/global/networks/defaults"      # Second VPC network
  peer_network                        = "projects/harshini-poc/global/networks/harshinivpc"  # First VPC network
  export_custom_routes                = true
  export_subnet_routes_with_public_ip = true
  import_custom_routes                = true
  stack_type                          = "IPV4_ONLY"
}

