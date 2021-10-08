# General

<u>List all OpenStack commands:</u>
```bash
openstack command list
```

---

### Inspecting endpoints:
<u>Show information for all endpoints:</u>

```bash
openstack endpoint list
```

<u>Show information for a specific endpoint:</u>

```bash
openstack endpoint show <Endpoint ID>
```

---

### Interacting with projects:
<u>Creation:</u>

```bash
openstack project create <project name>
```

<u>Show information for all projects:</u>

```bash
openstack project list
```

<u>Show information for a specific project:</u>

```bash
openstack project show <project name>
```

<u>Set a field on a project:</u>

```bash
openstack project set --<field name> "Field Contents" <project name>`

#Ex.
openstack project set --description "Demo project" demo_project
```

---

### Interacting with users:

<u>Create a new user:</u>
```bash
# --project: users must be associated with at least one project.
# --password-prompt: ask for the user's password upon creation.
openstack user create --project <project name> --password-prompt <username>
```

---

### Interacting with roles:

<u>List existing roles:</u>
```bash
openstack role list
```

<u>List roles assigned to a user:</u>
```bash
openstack role assignment list --project <project name> --user <username>
```

<u>Adding roles to a user:</u>

```bash
openstack role add --project <project name> --user <username> <role name>
```

--- 
# Resource-Specific Commands

## Keystone

<u>Show service catalog:</u>
```bash
openstack catalog list
```

---

## Glance

<u>List available images:</u>
```bash
openstack image list
```

<u>Show information for a specific image:</u>
```bash
openstack image show <image name or ID>
```

<u>Add a local image to Glance:</u>
```bash
# --min-disk: minimum disk size needed to boot the image; if unset, instances will not boot.
# --private: image is inaccessible to users outside of current project.
# --disk-format: qcow2 is a QEMU format; other possibilities are iso, vdi, vhd, vmdk, etc.
openstack image create --min-disk <size in GB> --private --disk-format qcow2 --file /path/to/image <name for image>
```

<u>Remove an image from Glance:</u>
```bash
openstack image delete <image name or ID>
```

---

## Neutron



---