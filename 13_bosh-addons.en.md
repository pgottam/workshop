## Goal

Adding specific software agents, monitors, and policies to specific BOSH dmakes day 2 operations easy! Lets upgrade our zookeeper cluster to the latest version.

## Prerequisites

1. Completed Deploy Zookeeper Demo

## Ensure our zookeeper is deployed:

1. Utilize the BOSH CLI to check the status of the deployment.

  ```bash
  bosh -e my-bosh -d zookeeper instances -p
  ```

  - You should see something similar to the following:

```
Using environment 'https://10.0.0.6:25555' as user 'admin'

Task 25. Done

Deployment 'zookeeper'

Instance                                          Process               Process State  AZ  IPs        Deployment
smoke-tests/f7a0d36b-70f5-4af6-b3c7-1bbe4d74d10d  -                     -              z1  -          zookeeper
zookeeper/06c46a15-6a46-4ee2-ac75-81babbb2ca3d    -                     running        z2  10.0.0.13  zookeeper
~                                                 bosh-dns              running        -   -          -
~                                                 bosh-dns-healthcheck  running        -   -          -
~                                                 bosh-dns-resolvconf   running        -   -          -
~                                                 zookeeper             running        -   -          -
zookeeper/39c6b3ba-6fce-4e1b-badc-6d6462fda9ab    -                     running        z1  10.0.0.11  zookeeper
~                                                 bosh-dns              running        -   -          -
~                                                 bosh-dns-healthcheck  running        -   -          -
~                                                 bosh-dns-resolvconf   running        -   -          -
~                                                 zookeeper             running        -   -          -
zookeeper/a098afdc-16e9-45d8-b1d6-41b3bd2ccf28    -                     running        z1  10.0.0.12  zookeeper
~                                                 bosh-dns              running        -   -          -
~                                                 bosh-dns-healthcheck  running        -   -          -
~                                                 bosh-dns-resolvconf   running        -   -          -
~                                                 zookeeper             running        -   -          -

4 instances
```

## Upload the OS-Conf BOSH Release

1. Utilize the BOSH CLI to upload the latest os-conf release that we can use to add an SSH banner to our BOSH zookeeper instances.

  ```bash
  bosh -e my-bosh upload-release https://bosh.io/d/github.com/cloudfoundry/os-conf-release?v=19 --sha1 f515406949ee0bba0329d1ce4a7eb1679521eabd
  ```

## Create a BOSH Runtime Config

1. BOSH has a way to specify global configuration for all VMs in all deployments, it is called the Runtime-Config.
1. To update our zookeeper deployment by adding an SSH banner, we first have to create a runtime-config.

  ```bash
  nano runtime-config.yml
  ```

1. Copy the following into the `runtime-config.yml` file we just created.

        releases:
        - name: os-conf
          version: 19

        addons:
        - name: misc
          jobs:
          - name: login_banner
            release: os-conf
            properties:
              login_banner:
                text: |
                  Welcome to Pivotals Workshop!
                  BBBB   OOO   SSS  H  H    III  OOO
                  B   B O   O S     H  H     I  O   O
                  BBBB  O   O  SSS  HHHH     I  O   O
                  B   B O   O     S H  H ..  I  O   O
                  BBBB   OOO  SSSS  H  H .. III  OOO
                  This computer system is for authorized use only. All activity is logged and
                  regularly checked by system administrators. Individuals attempting to connect to,
                  port-scan, deface, hack, or otherwise interfere with any services on this system
                  will be reported.

## Upload the BOSH Runtime Config

1. Now lets use the BOSH CLI to upload the `runtime-config.yml`.

  ```bash
  bosh -e my-bosh update-runtime-config runtime-config.yml
  ```

## Apply the BOSH Runtime Config

1. BOSH only applies runtime-config changes to the deployment during a `bosh deploy`. We must redeploy the zookeeper deployment as a last step to get our SSH banner.

  ```bash
  bosh -e my-bosh -d zookeeper deploy ~/zookeeper-manifest.yml
  ```

## Test the SSH Banner

1. To test the SSH Banner lets SSH to a BOSH instance. Review [Releases]("../concepts/releases") for understanding access to BOSH instances.

  ```bash
  bosh -e my-bosh -d zookeeper instances
  bosh -e my-bosh -d zookeeper ssh zookeeper/185f9261-e51f-46b5-87f4-38e3e56f61a9
  ```
  - You should see something similar to the following:

```
Using environment '10.0.0.6' as user 'admin' (openid, bosh.admin)

Using deployment 'zookeeper'

Task 51. Done
Welcome to Pivotals Workshop!

BBBB   OOO   SSS  H  H    III  OOO
B   B O   O S     H  H     I  O   O
BBBB  O   O  SSS  HHHH     I  O   O
B   B O   O     S H  H ..  I  O   O
BBBB   OOO  SSSS  H  H .. III  OOO

This computer system is for authorized use only. All activity is logged and
regularly checked by system administrators. Individuals attempting to connect to,
port-scan, deface, hack, or otherwise interfere with any services on this system
will be reported.

Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 4.4.0-97-generic x86_64)
```
