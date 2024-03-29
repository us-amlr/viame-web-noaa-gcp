# Default Deployment

NOTE: Be sure to first read the [General](deployment-general.md) deployment instructions.

These instructions are for a single VM with at least one GPU, meaning all operations (annotation, training, etc.) will happen on this VM. For hybrid options, e.g., a low-cost compute VM paired with a worker VM with GPUs, see [Split Services](deployment-split.md).

## Create GCP Resources

To create a single VM for an instance of VIAME-Web, create a VM with at least one GPU using the 'viame-web-noaa-gcp' module. The [source path](https://www.terraform.io/language/modules/sources) to this module can either be relative (e.g., '../viame-web-noaa-gcp') or an unprefixed `github.com` URL (e.g., 'github.com/us-amlr/viame-web-noaa-gcp'). Be sure to provide a non-zero value for gpu_count. 

[Click here](https://drive.google.com/u/0/uc?id=1aD1sjUx3M4AMGAi-o57V--xu1HfKxEy5&export=download) to download the Terraform code template for a VM for a default deployment. Copy this code into your Terraform config file (e.g., main.tf) and update project-specific values as needed. In the Cloud Shell Terminal, run `terraform init`, and then `terraform apply` to create the resources.

## Provision GCP VM

Once you have created the VM, set variables in your Cloud Shell session that will be used throughout. Then, run a command to download the install script to the VM, make it executable, and then run the install script on the VM. Running this install script may take 10-15 minutes.

``` bash
ZONE=us-east4-c
INSTANCE_NAME=viame-web
REPO_URL=https://raw.githubusercontent.com/us-amlr/viame-web-noaa-gcp/main/scripts

gcloud compute ssh $INSTANCE_NAME --zone=$ZONE \
  --command="curl -L $REPO_URL/dive_install.sh -o ~/dive_install.sh \
  && chmod +x ~/dive_install.sh \
  && ./dive_install.sh"
```

Because of permissions changes and installing the NVIDIA drivers, the VM must now be restarted. While you can restart the VM from the console, it is generally easiest to run the following from Cloud Shell to 1) restart the VM and 2) run the startup script to pull updated files and spin up the VIAME-Web stack:

``` bash
gcloud compute instances stop $INSTANCE_NAME --zone=$ZONE \
  && gcloud compute instances start $INSTANCE_NAME --zone=$ZONE
```
``` bash
gcloud compute ssh $INSTANCE_NAME --zone=$ZONE --command="/opt/noaa/dive_startup_full.sh"
```

## Access VIAME-Web deployment

See [Access VIAME-Web](deployment-access.md)
