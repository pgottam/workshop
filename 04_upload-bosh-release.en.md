## Goal

Deploying software systems with BOSH is done with BOSH Releases. Lets upload a release to our BOSH director.

## Prerequisites

You are in the jumpbox and logged into BOSH.

## Upload the BOSH Release:

1. Download the [sample-bosh-release](https://github.com/Pivotal-Field-Engineering/bosh-release) using `wget`

  ```bash
  wget https://github.com/Pivotal-Field-Engineering/bosh-release/releases/download/1.2/sample_release.tgz
  ```

1. Upload the BOSH release tarball to the BOSH director.

  ```bash
  bosh -e my-bosh upload-release ~/sample_release.tgz
  ```

1. Ensure the release was uploaded and is ready for use!

  ```bash
  bosh -e my-bosh releases
  ```

  - You should see something similar to the following:
    ```bash
      Using environment 'https://10.0.0.6:25555' as user 'admin'

      Name                 Version   Commit Hash
      bosh-dns             1.12.0*   5d607ed
      sample-bosh-release  0+dev.1*  a2b19b8

      (*) Currently deployed
      (+) Uncommitted changes

      2 releases

      Succeeded

    ```
