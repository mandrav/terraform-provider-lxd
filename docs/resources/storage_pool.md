# lxd_storage_pool

Manages an LXD storage pool.

## Example Usage

### Basic Example

```hcl
resource "lxd_storage_pool" "pool1" {
  name   = "mypool"
  driver = "dir"
  config = {
    source = "/var/lib/lxd/storage-pools/mypool"
  }
}
```

### Cluster Example

In order to create a storage pool in a cluster, you first have to define
the storage pool on each node in the cluster. Then you can create the
actual pool

```hcl
resource "lxd_storage_pool" "mypool_node1" {
  target = "node1"

  name   = "mypool"
  driver = "dir"
  config = {
    source = "/var/lib/lxd/storage-pools/mypool"
  }
}

resource "lxd_storage_pool" "mypool_node2" {
  target = "node2"

  name   = "mypool"
  driver = "dir"
  config = {
    source = "/var/lib/lxd/storage-pools/mypool"
  }
}

resource "lxd_storage_pool" "mypool" {
  depends_on = [
    lxd_storage_pool.mypool_node1,
    lxd_storage_pool.mypool_node2,
  ]

  name   = "mypool"
  driver = "dir"
}
```

Please see the [LXD Clustering documentation](https://documentation.ubuntu.com/lxd/en/latest/howto/cluster_config_storage/)
for more details on how to create a storage pool in clustered mode.

## Argument Reference

* `remote` - *Optional* - The remote in which the resource will be created. If
	it is not provided, the default provider remote is used.

* `target` - *Optional* - Specify a target node in a cluster.

* `name`   - *Required* - Name of the storage pool.

* `driver` - _Required_ - Storage Pool driver. Must be one of `ceph`, `cephfs`, or `cephobject`
  `btrfs`, `dir`, `lvm`, or `zfs`.

* `config` - *Optional* - Map of key/value pairs of
	[storage pool config settings](https://documentation.ubuntu.com/lxd/en/latest/reference/storage_drivers/).
	Config settings vary from driver to driver.

* `project` - *Optional* - Name of the project where the storage pool will be stored.

## Importing

Storage pools can be imported by doing:

```shell
$ terraform import lxd_storage_pool.my_pool <name of pool>
```
