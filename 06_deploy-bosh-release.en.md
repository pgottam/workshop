## Goal

Before we can deploy a BOSH release we need to define how the release should be deployed. Do we deploy 3 or 1 instances? Do we give it an IP of 10.0.0.1 or 10.0.0.50? etc...
As you can see there are many different ways to deploy the release. With BOSH we call this definition a BOSH Manifest. Lets create a manifest and deploy our sample-bosh-release.

## Prerequisites

1. You are in the jumpbox and logged into BOSH
1. Uploaded stemcells and downloaded `sample-bosh-release`

## Part 1: Create the manifest

1. Using your favorite command-line text editor to create a `sample-bosh-manifest.yml` text file. (We will use `nano`, but `vi` works as well!)

  ```bash
  nano sample-bosh-manifest.yml
  ```

1. Paste the following into the `sample-bosh-manifest.yml`.

        name: sample-bosh-deployment
        
        releases:
        - {name: sample-bosh-release, version: latest}
        
        stemcells:
        - alias: xenial
          os: ubuntu-xenial
          version: latest
        
        instance_groups:
        - name: sample_vm
          instances: 1
          networks:
          - name: default
          azs: [z1]
          jobs:
          - name: sample_job
            release: sample-bosh-release
          stemcell: xenial
          vm_type: default
        update:
          canaries: 1
          max_in_flight: 10
          canary_watch_time: 1000-30000
          update_watch_time: 1000-30000

  - The goal of this deployment is to deploy a single VM that runs our sample_job from the [sample-bosh-release](https://github.com/Pivotal-Field-Engineering/bosh-release).

  - `name` - the unique name of this deployment within a BOSH director. When a BOSH director receives subsequent deployment manifests with the same name it will assume it is an upgrade of the existing deployment.

  - `releases` - lists the specific BOSH release versions that are to be used. In our case the [sample-bosh-release](https://github.com/Pivotal-Field-Engineering/bosh-release).

  - `instance_groups` - lists the sets of instances (VMs) that will run the same job templates/packages as each other. Instance groups will be deployed as long running instances by default.

  - `stemcells` - lists the stemcells (discussed earlier) we will run our instances with.

## Part 2: Deploy!

1. Here is where all the magic happens! By BOSH `deploy`ing we combine the Stemcells, Releases, and Manifest to create our software system. This can take a few minutes.

  ```bash
  bosh -e my-bosh -d sample-bosh-deployment deploy sample-bosh-manifest.yml
  ```

  - Double check the changes and type `y` followed by ENTER.

  - If successful, you should see something similar to the following:

```bash
    Task 18

    Task 18 | 19:22:12 | Preparing deployment: Preparing deployment (00:00:03)
    Task 18 | 19:22:15 | Preparing deployment: Rendering templates (00:00:01)
    Task 18 | 19:22:16 | Preparing package compilation: Finding packages to compile (00:00:00)
    Task 18 | 19:22:16 | Compiling packages: golang-1-linux/a58647561918778c52fb932074e6099497a9063bce3a1531e5c60ba643e1f4df
    Task 18 | 19:22:16 | Compiling packages: sample_app/3698d0cc632d3162a6a2fedcd36ac00364a7cd64 (00:01:06)
    Task 18 | 19:23:56 | Compiling packages: golang-1-linux/a58647561918778c52fb932074e6099497a9063bce3a1531e5c60ba643e1f4df (00:01:40)
    Task 18 | 19:23:56 | Compiling packages: bosh-dns/58c7e157ad34bbd80c0d13dc6d1e3e073c8f0daf071bba8b4ca2ce0f10a3dbac (00:00:58)
    Task 18 | 19:25:52 | Creating missing vms: sample_vm/7c68189c-ce53-48cd-ab4c-50718ed33426 (0) (00:00:40)
    Task 18 | 19:26:32 | Updating instance sample_vm: sample_vm/7c68189c-ce53-48cd-ab4c-50718ed33426 (0) (canary) (00:00:13)

    Task 18 Started  Wed Jan 15 19:22:12 UTC 2020
    Task 18 Finished Wed Jan 15 19:26:45 UTC 2020
    Task 18 Duration 00:04:33
    Task 18 done

  ```
