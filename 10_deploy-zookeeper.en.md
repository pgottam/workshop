## Goal

To showcase the power of BOSH we now will take a look at a few real life example BOSH releases. Lets start with Zookeeper!

## Upload the required BOSH releases:


1. Utilize the BOSH CLI to upload the zookeeper release.

  ```bash
  bosh -e my-bosh upload-release https://bosh.io/d/github.com/cppforlife/zookeeper-release?v=0.0.6 --sha1 eea677d086161ada53dc7f6a056e94023384bba0
  ```

## Build the BOSH Manifest:

1. Using your favorite command-line text editor to create a `zookeeper-manifest.yml` text file. (We will use `nano`, but `vi` works as well!)

  ```bash
  nano zookeeper-manifest.yml
  ```

1. Paste the following into the `zookeeper-manifest.yml`.

        ---
        name: zookeeper

        releases:
        - name: zookeeper
          version: latest

        stemcells:
        - alias: default
          os: ubuntu-xenial
          version: latest

        update:
          canaries: 2
          max_in_flight: 1
          canary_watch_time: 5000-60000
          update_watch_time: 5000-60000

        instance_groups:
        - name: zookeeper
          azs: [z1, z2]
<<<<<<< HEAD
          instances: 3
=======
          instances: 10
>>>>>>> ff52c6271b391c05aff6887fcf536cdd876d4861
          jobs:
          - name: zookeeper
            release: zookeeper
            properties: {}
          vm_type: default
          stemcell: default
          persistent_disk: 10240
          networks:
          - name: default

        - name: smoke-tests
          azs: [z1]
          lifecycle: errand
          instances: 1
          jobs:
          - name: smoke-tests
            release: zookeeper
            properties: {}
          vm_type: default
          stemcell: default
          networks:
          - name: default

## Deploy the BOSH Deployment:

1. `bosh -e my-bosh -d zookeeper deploy zookeeper-manifest.yml`

## Examine the Zookeeper Deployment:

  1. Take a look at the running processes on the zookeeper instances

    - `bosh -e my-bosh -d zookeeper instances -p`
