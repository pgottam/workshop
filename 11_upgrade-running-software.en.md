## Goal

BOSH makes day 2 operations easy! Lets upgrade our zookeeper cluster to the latest version.

## Prerequisites

1. Completed Zoo keeper deploy from previous step

## Ensure our zookeeper is deployed:

1. Utilize the BOSH CLI to check the status of the deployment.

  ```bash
  bosh -e my-bosh -d zookeeper instances -p
  ```

  - You should see something similar to the following:

```
Using environment 'https://10.0.0.6:25555' as user 'admin'

Task 23. Done

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

Succeeded
```

## Upload the latest zookeeper BOSH Release

1. Utilize the BOSH CLI to upload the latest zookeeper release.

```bash
bosh -e my-bosh upload-release https://bosh.io/d/github.com/cppforlife/zookeeper-release?v=0.0.7 --sha1 a863ef216ea75d22950703a3c924959cb20e59ba`
```
1. Ensure the release was uploaded and is ready for use!

```bash
bosh -e my-bosh releases

Using environment 'https://10.0.0.6:25555' as user 'admin'

Name                 Version  Commit Hash
bosh-dns             1.12.0*  5d607ed
sample-bosh-release  0+dev.1  a2b19b8
zookeeper            0.0.7    e7ead22
~                    0.0.6*   2f8926f

(*) Currently deployed
(+) Uncommitted changes

4 releases

Succeeded
```

## Update our zookeeper deployment

1. Update our zookeeper deployment.

  ```bash
  bosh -e my-bosh -d zookeeper deploy ~/zookeeper-manifest.yml`
  ```

  - Notice that we did not change any values in the `zookeeper-manifest.yml`! This is because we specified `latest` under the releases block in the `zookeeper-manifest.yml`.
