## Goal

Delete the sample-release deployment.

## Delete the Deployment:


1. Utilize the BOSH CLI to delete the sample deployment:

  - `bosh -e my-bosh -d sample-bosh-deployment delete-deployment`


            Using environment '192.168.50.6' as client 'admin'

            Using deployment 'sample-bosh-deployment'

            Continue? [yN]: y

            Task 9

            Task 9 | 23:39:32 | Deleting instances: sample_vm/fd87a629-10a9-47b2-bdff-cab797d7ec41 (0) (00:00:04)
            Task 9 | 23:39:36 | Removing deployment artifacts: Detaching stemcells (00:00:00)
            Task 9 | 23:39:36 | Removing deployment artifacts: Detaching releases (00:00:00)
            Task 9 | 23:39:36 | Deleting properties: Destroying deployment (00:00:00)

            Task 9 Started  Tue Jan 22 23:39:32 UTC 2019
            Task 9 Finished Tue Jan 22 23:39:36 UTC 2019
            Task 9 Duration 00:00:04
            Task 9 done

            Succeeded
