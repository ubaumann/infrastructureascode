Title: Creating your own Infrahub Terraform Provider
Slug: infrahub_terraform
Date: 2025-04-14 20:00
Tags: Terraform, Infrahub
Category: DevOps
Author: mmartinez


# 🚀 How to Build and Deploy Your Own Terraform Provider with Infrahub

If you're venturing into the world of custom Terraform providers and want to leverage the power of Infrahub, this guide is your fast track. We'll walk through everything from cloning the template to deploying your provider to a private registry. Whether you're automating infrastructure workflows or integrating a new API, this setup will get you running in no time.

---

## 🧭 Choosing Your Deployment Path

If you're planning to deploy your provider to the private registry available at [marcom4rtinez/terraform-registry](https://github.com/marcom4rtinez/terraform-registry), be sure to follow **all the steps** outlined in this guide—especially Steps 3 and 4 which cover packaging and deployment.

> 💡 **Tip:** If you're not using this private registry and just want to run the provider locally or publish it elsewhere, you can **skip Steps 3 and 4**.

---

## 🔧 Step 1: Clone the Terraform Provider Template

Start by using the [Terraform Provider Infrahub Template](https://github.com/marcom4rtinez/terraform-provider-infrahub). Hit the **"Use this template"** button on GitHub and create a new repository.

> ⚠️ **Important:** Name your repo using the `terraform-provider-<your-name>` convention (e.g. `terraform-provider-infrahub`). This is required by Terraform.

Once your repo is created, go ahead and clone it locally:

```bash
git clone https://github.com/your-username/terraform-provider-infrahub.git
cd terraform-provider-infrahub
```

---

## ⚙️ Step 2: Generate the Provider

Put your GraphQL queries inside the `gql/` directory — these are the definitions your provider will use to interact with Infrahub.

An example could look like the following:

```gql
query Devicequery($device_name: String!) {
    InfraDevice(name__value: $device_name) {
        edges {
          node {
            id
            name {
              value
            }
            role {
              value
              color
              description
              id
            }
            platform {
              node {
                id
              }
            }
            primary_address {
              node {
                id
              }
            }
            status {
              id
            }
            topology {
              node {
                id
              }
            }
            device_type {
              node {
                id
              }
            }
            asn {
              node {
                asn {
                  id
                }
              }
            }
            description {
              value
            }
          }
        }
    }
}
```

Repeat this step with as many queries as desired. Based on the type of the query it will change the terraform type. GQL queries will generate Terraform `data sources`, GQL mutations will generate `resources`. For further information consult `gql/README.md` in the template repository.


> ⚠️ **Important:** Make sure your Infrahub instance is accessible. The next step will pull the schema from your infrahub instance to do type checking of the gql files.

Set the environment variable for your Infrahub instance:

```bash
export INFRAHUB_SERVER="http://localhost:8000"
```

Now generate the SDK, provider logic, and documentation:

```bash
make all
```

That’s it! You now have a working provider. 🎉 From here, you can test it locally or proceed to deployment.

---

## 🚀 Step 3: Prepare for Deployment

Publishing the provider requires a **GPG key** for signing and a **GitHub token** with access to the original repository. These are used by **GoReleaser** to publish the release artifacts.

> 💡 **Tip:** [Create GPG Key Pairs](https://docs.github.com/en/authentication/managing-commit-signature-verification/generating-a-new-gpg-key)

Before you can publish your provider, set the following environment variables:

```bash
export GPG_FINGERPRINT=9A52F2BE41E9C446A902C723B53E44105C84C057
export GPG_PUBLIC_KEY=$(gpg --armor --export $GPG_FINGERPRINT)
export GITHUB_TOKEN=your_github_token
export TERRAFORM_REGISTRY_ENDPOINT="http://localhost:8080/v1/providers/marcom4rtinez/infrahub-main/upload"
export RELEASE_URL="https://github.com/marcom4rtinez/terraform-provider-infrahub/releases/download"
```

---

## 📦 Step 4: Deploy to a Private Registry

If you're using [this Terraform Registry](https://github.com/marcom4rtinez/terraform-registry), you're ready to go. Otherwise, you’ll need to tweak the `makefile` accordingly.

Tag your release before deployment:

```bash
git tag -a v1.4 -m "My awesome Infrahub Terraform Provider"
git push --tags
```

Then, deploy:

```bash
make generate_deploy
```

Your provider will now be published in GitHub Releases and available in your registry.

---

## 🛠 Local Development Setup

To work with your provider locally, update your Terraform config:

Edit `~/.terraformrc`:

```hcl
provider_installation {
  dev_overrides {
    "registry.terraform.io/marcom4rtinez/infrahub" = "/Users/youruser/go/bin"
  }
  direct {}
}
```

Build and install locally:

```bash
make all    # To build and generate the provider
make        # To rebuild and install the provider locally
```

Then use it in your Terraform config:

```hcl
terraform {
  required_providers {
    infrahub = {
      source  = "registry.marcomartinez.ch/marcom4rtinez/infrahub"
      version = "1.0"
    }
  }
}
```

---

## 📝 Generating Documentation & Examples

Docs are auto-generated as part of the build process, but you can also run it manually:

```bash
make generate
```

To enhance the documentation with examples, drop your `.tf` files in the `example/` folder and check out `example/README.md` for guidance.

---

## 🎯 Wrapping Up

With this setup, you're now fully equipped to develop, test, and deploy your own Terraform provider powered by Infrahub. Whether you're automating infrastructure or building integrations, this template gives you a rock-solid foundation.

Got questions or building something cool with Infrahub? Let me know — I’d love to see what you're creating!

Feel free to reach out to me on [Linkedin](https://www.linkedin.com/in/marco-martinez-364461243/), I'm happy to have a conversation!

