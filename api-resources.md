# api-resources


### Rook Ceph Resources

| NAME                          | SHORTNAMES                | APIVERSION      | NAMESPACED | KIND                         |
| ----------------------------- | ------------------------- | --------------- | ---------- | ---------------------------- |
| cephblockpoolradosnamespaces  | cephbprns, cephrns        | ceph.rook.io/v1 | true       | CephBlockPoolRadosNamespace  |
| cephblockpools                | cephbp                    | ceph.rook.io/v1 | true       | CephBlockPool                |
| cephbucketnotifications       | cephbn                    | ceph.rook.io/v1 | true       | CephBucketNotification       |
| cephbuckettopics              | cephbt                    | ceph.rook.io/v1 | true       | CephBucketTopic              |
| cephclients                   | cephcl                    | ceph.rook.io/v1 | true       | CephClient                   |
| cephclusters                  | ceph                      | ceph.rook.io/v1 | true       | CephCluster                  |
| cephcosidrivers               | cephcosi                  | ceph.rook.io/v1 | true       | CephCOSIDriver               |
| cephfilesystemmirrors         | cephfsm                   | ceph.rook.io/v1 | true       | CephFilesystemMirror         |
| cephfilesystems               | cephfs                    | ceph.rook.io/v1 | true       | CephFilesystem               |
| cephfilesystemsubvolumegroups | cephfssvg, cephsvg        | ceph.rook.io/v1 | true       | CephFilesystemSubVolumeGroup |
| cephnfses                     | nfs                       | ceph.rook.io/v1 | true       | CephNFS                      |
| cephnvmeofgateways            | nvmeof                    | ceph.rook.io/v1 | true       | CephNVMeOFGateway            |
| cephobjectrealms              | cephor                    | ceph.rook.io/v1 | true       | CephObjectRealm              |
| cephobjectstoreaccounts       | —                         | ceph.rook.io/v1 | true       | CephObjectStoreAccount       |
| cephobjectstores              | cephos                    | ceph.rook.io/v1 | true       | CephObjectStore              |
| cephobjectstoreusers          | rcou, objectuser, cephosu | ceph.rook.io/v1 | true       | CephObjectStoreUser          |
| cephobjectzonegroups          | cephozg                   | ceph.rook.io/v1 | true       | CephObjectZoneGroup          |
| cephobjectzones               | cephoz                    | ceph.rook.io/v1 | true       | CephObjectZone               |
| cephrbdmirrors                | cephrbdm                  | ceph.rook.io/v1 | true       | CephRBDMirror                |



### CSI Ceph Resources

| NAME                  | SHORTNAMES | APIVERSION     | NAMESPACED | KIND                 |
| --------------------- | ---------- | -------------- | ---------- | -------------------- |
| cephconnections       | —          | csi.ceph.io/v1 | true       | CephConnection       |
| clientprofilemappings | —          | csi.ceph.io/v1 | true       | ClientProfileMapping |
| clientprofiles        | —          | csi.ceph.io/v1 | true       | ClientProfile        |
| drivers               | —          | csi.ceph.io/v1 | true       | Driver               |
| operatorconfigs       | —          | csi.ceph.io/v1 | true       | OperatorConfig       |

