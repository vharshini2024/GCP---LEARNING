## V-net peering
VPC Peering enables you to connect two VPC networks so that resources in one network can communicate with resources in another, using internal IP addresses. This connection is established through a private, direct link.

# Terraform Configuration for Google Cloud

## VPC Network and Firewall

The following Terraform code sets up a VPC network, a subnet within that network, and a firewall rule:

```hcl
provider "google" {
  credentials = file("C:/Users/vharshini/OneDrive - Sopra Steria/GCP LANDING ZONE/creds.json")
  project     = "harshini-poc"
  region      = "us-central1"
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
