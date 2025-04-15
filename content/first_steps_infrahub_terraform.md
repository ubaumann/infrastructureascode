Title: Using your own Infrahub Provider
Slug: first_steps_infrahub_terraform
Date: 2025-04-14 20:00
Author: mmartinez


# 🌐 How to use your Custom Infrahub Terraform Provider

Working with custom Terraform providers unlocks a whole new level of automation and flexibility. With the **Infrahub Terraform provider**, you can define, query, and manage infrastructure in a modular, declarative way—perfect for modern network and infrastructure workflows.

In this guide, you’ll learn how to:

- 🔧 Configure the Infrahub provider  
- 🔁 Chain data sources with resources for clean, reusable setups  
- 🚀 Use Terraform's workflow to deploy your infrastructure  
- 🧱 Provision both single and bulk devices  

---

## ⚠️ Disclaimer

🛠️ Haven’t generated your provider yet?

Before using the examples above, make sure you've created your custom Terraform provider with Infrahub. You can follow the step-by-step guide at [Build your own Infrahub Terraform Provider]({filename}/infrahub_terraform.md)

This blog assumes your provider is already built and ready to be used in your Terraform workflow.

---

## 🔧 Step 1: Configure the Infrahub Provider

Before you run any Terraform commands, you need to set up your provider in `main.tf`. This includes specifying where to find the provider and how to authenticate with Infrahub.

```hcl
terraform {
  required_providers {
    infrahub = {
      source  = "infrastructureascode.ch/marcom4rtinez/infrahub-main"
      version = "0.0.2"
    }
  }
}

provider "infrahub" {
  api_key         = "XXXXXXXXXXXXX"
  infrahub_server = "http://localhost:8000"
  branch          = "main"
}
```

> 🧠 **Pro Tip:** This setup allows Terraform to connect to your Infrahub instance and manage resources declaratively. Working on different branches is also possible.

---

## 📦 Step 2: Run Terraform Commands

With your provider configured, you can now use Terraform’s core workflow:

```bash
terraform init
terraform plan
terraform apply -auto-approve
```

- `terraform init` — Downloads and sets up the provider.
- `terraform plan` — Shows a preview of what will be created/changed.
- `terraform apply -auto-approve` — Applies changes without prompting.

---

## 🧩 Power of Chaining Data Sources and Resources

One of the biggest strengths of Terraform is how **data sources and resources can be chained together**. This makes your configuration **modular**, **reusable**, and **readable**.

For example:

- Pull metadata from Infrahub (like country, platform, device type)  
- Use that metadata directly when creating new infrastructure

This approach avoids hardcoding values, allows consistent references across resources, and ensures changes in upstream data automatically propagate through your configuration.


---

## 📚 Example 1: Query All Devices

Use a data source to list all devices in your Infrahub instance:

```hcl
data "infrahub_devices" "all_devices" {}

output "devices_all_devices" {
  value = data.infrahub_devices.all_devices
}
```

Great for visibility, inventory audits, or referencing existing infrastructure.

---

## 🛠 Example 2: Define a Single Device with Chained Data Sources

This example combines multiple data sources (e.g., country, topology, device type) and uses them to create a device resource.

```hcl
data "infrahub_country" "germany" {
  country_name = "Germany"
}

data "infrahub_topology" "de1-pod1" {
  topology_name = "de1-pod1"
}

data "infrahub_devicetype" "ccs" {
  device_type_name = "CCS-720DP-48S-2F"
}

data "infrahub_autonomoussystem" "AS174" {
  as_name = "AS174"
}

data "infrahub_platform" "Arista" {
  platform_name = "Arista EOS"
}

data "infrahub_ipaddress" "mgmt_address" {
  ip_address_value = "10.0.0.1/24"
}

resource "infrahub_device" "device_res" {
  name_value              = "switch27"
  asn_node_id             = data.infrahub_autonomoussystem.AS174.id
  device_type_node_id     = data.infrahub_devicetype.ccs.id
  location_node_id        = data.infrahub_country.germany.id
  platform_node_id        = data.infrahub_platform.Arista.id
  primary_address_node_id = data.infrahub_ipaddress.mgmt_address.id
  status_value            = "active"
  topology_node_id        = data.infrahub_topology.de1-pod1.id
  role_value              = "leaf"
}
```

> 🔗 **Why this matters:** All values are derived dynamically from upstream sources, making your infrastructure definition both readable and DRY.

---

## ⚙️ Example 3: Bulk Provision Devices with `for_each`

Need to spin up a fleet of routers? Use a loop to define hundreds of resources cleanly:

```hcl
data "infrahub_country" "usa" {
  country_name = "United States of America"
}

data "infrahub_topology" "fra05-pod1" {
  topology_name = "fra05-pod1"
}

data "infrahub_devicetype" "ncs" {
  device_type_name = "NCS-5501-SE"
}

data "infrahub_autonomoussystem" "AS701" {
  as_name = "AS701"
}

data "infrahub_platform" "iosxr" {
  platform_name = "Cisco IOS-XR"
}

data "infrahub_ipaddress" "mgmt_address" {
  ip_address_value = "10.0.0.2/24"
}

resource "infrahub_device" "bulk_device_modify" {
  for_each                = toset([for i in range(100) : format("router%d", i)])
  name_value              = each.key
  asn_node_id             = data.infrahub_autonomoussystem.AS701.id
  device_type_node_id     = data.infrahub_devicetype.ncs.id
  location_node_id        = data.infrahub_country.usa.id
  platform_node_id        = data.infrahub_platform.iosxr.id
  primary_address_node_id = data.infrahub_ipaddress.mgmt_address.id
  status_value            = "active"
  topology_node_id        = data.infrahub_topology.fra05-pod1.id
  role_value              = "spine"
}
```

> 💡 **Scalable Automation:** One configuration can deploy 100+ resources with consistent attributes and structure. All manageable and changeable from the same place.

---

## 📖 Documentation in Every Provider
Each generated provider includes built-in documentation under the docs/ folder. This contains:

- Usage examples
- Supported resources and data sources
- attributes of each resource

So if you're ever unsure about a field or want to explore capabilities, just open up the docs/ folder in your provider’s repo.

---

## ✅ Final Thoughts

The Infrahub Terraform provider brings the power of declarative infrastructure to your network and asset management workflows. By chaining data sources and resources, you create configurations that are not only scalable but also easy to read, maintain, and extend.

---

💬 **Have questions or feedback?** Share your use case with me — I’d love to see how you’re using Infrahub + Terraform! Catch me on [Linkedin](https://www.linkedin.com/in/marco-martinez-364461243/)