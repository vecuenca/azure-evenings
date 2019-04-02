# Azure Blob Storage Lab - working with the Azure Portal
1. Follow [this tutorial](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-portal), **but do not clean up resources** just yet.
2. Navigate to the container you created in your Azure Storage account.
3. Acquire a lease on a blob:

   - Select the check mark for a blob, then click on the blob to bring up the context menu. Click `Acquire lease`.
   - A pane appears with the lease ID for your blob. 
   - **You must supply this lease ID in order to write to the blob, or to renew, change, or break the lease. Think of it as an exclusive lock.**
4. Break the lease on the blob, by accessing the same context menu and clicking `Break lease`. Note that the `LEASE STATE` column updates.
5. Implement a data retention policy:

   - Select an existing container in your Storage account. **(Note: your storage account must be in a GPv2 or a blob storage account!)**
   - Select `Access policy` in the container settings. Then, select `Add policy` under `Immutable blob storage`. 
     - This has your blob enter into an immutable WORM (Write Once, Read Many) state. The data is non-erasable and non-modifiable for a given interval.
   - Select `Time based retention` from the drop-down menu.
   - Enter a retention value in days (1 - 146000).
   - Initially, the policy is unlocked, allowing you to make changes to the policy before you lock it.
   - **DON'T LOCK THE POLICY!!! You want to conserve your Azure subscription credits for future use :)**
6. Implement a lifecycle management policy for your storage account:

   - Navigate to your Azure Storage account.
   - Under `Blob service`, select `Lifecycle management` and add the following policy, **replacing the `prefixMatch` attribute with a prefix matching your container and/or blob:**
   
```
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```
This policy does the following: 
- Tier blob to cool tier 30 days after last modification
- Tier blob to archive tier 90 days after last modification
- Delete blob 2,555 days (seven years) after last modification
- Delete blob snapshots 90 days after snapshot creation
   

# Azure Tables Lab - working with the .NET driver
1. Follow this [tutorial](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs).
