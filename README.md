| Name           | NRP        | Class     |
| ---            | ---        | ----------|
| Danny Rachmadian Yusuf Satryatama | 5025231240 | Jaringan Komputer D |
| Indhira Ayu Puspita Ningrum | 5025231316 | Jaringan Komputer D |

<br>

<b> Letakkan link excel hasil perhitungan di sini </b>
<br>
<b> _Place the excel calculation results link here_ </b>
```
https://docs.google.com/spreadsheets/d/1riwyAUTSPqZzU2tHLGdjimiFmKcyY7wibPbxmj-a4Qs/edit?gid=699820178#gid=699820178
```

# Laporan Praktikum Modul 4 Terraform

## Prefix IP
Letakkan prefix IP yang digunakan di bawah:
```
192.168.26.x
```

## Routing

- Routing table
<br>![image](https://github.com/user-attachments/assets/0215284c-0a41-417c-b680-d630e960e5c6)
<br>

## VLSM

### Tree

- Tree image

  ![image](https://github.com/user-attachments/assets/c832f225-2c22-4a0e-b499-5103cc0bc226)


- IP distribution table

  ![image](https://github.com/user-attachments/assets/26227c99-d5a2-42ff-bbcf-27b800bf9999)

## CIDR

### Tree

- Tree image

 ![image](https://github.com/user-attachments/assets/4a5ac6d1-5593-414b-8d24-d7b3a42952d2)


- IP distribution table

  ![image](https://github.com/user-attachments/assets/aba53115-621d-4f42-b997-fafc44e8c435)

### Subnet Merging Iteration

- Iteration 1

  ![image](https://github.com/user-attachments/assets/b19e3719-3a9f-4579-856a-937992d436ec)
  
- Iteration 2

  ![image](https://github.com/user-attachments/assets/0ad615e4-8ae0-42ce-a9ea-9520bfe46f10)

  ![image](https://github.com/user-attachments/assets/048917cd-31fc-4603-b770-ff63db2df1e5)

- Iteration 3

  ![image](https://github.com/user-attachments/assets/88d00246-9661-4ece-8019-94ace948c89c)

  ![image](https://github.com/user-attachments/assets/22bccf8d-545e-42d9-b89e-61ccb1d246fd)

- Iteration 4
  
  ![image](https://github.com/user-attachments/assets/d2cd8a4c-7647-4877-a3c8-a5bcac2672b6)

  ![image](https://github.com/user-attachments/assets/17907e6d-cf33-4297-a2df-08d3a0c83f99)

- Iteration 5

  ![image](https://github.com/user-attachments/assets/15572e0d-c741-4104-bd12-dfa39146b981)

  ![image](https://github.com/user-attachments/assets/b7b07b12-216d-4d80-b985-c3599f4e5200)

## Terraform

### main.tf Configuration
```
terraform {
  required_providers {
    proxmox = {
      source  = "bpg/proxmox"
      version = "0.66.1"
    }
  }
}

provider "proxmox" {
  endpoint = var.proxmox_url
  username = var.proxmox_user
  password = var.proxmox_password
  insecure = true
}

resource "proxmox_virtual_environment_file" "script-family-mart" {
  content_type = "snippets"
  datastore_id = "local"
  node_name    = "its"
  file_mode    = "0700"

  source_raw {
    data = <<-EOT
    #cloud-config
    users:
        - name: "d26"
          password: "d26"
    
    runcmd:
        - sysctl -w net.ipv4.ip_forward=1
        - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
        - sysctl -p
        - netplan apply
        - ip link set eth0 up
        - ip link set eth1 up
        - ip link set eth2 up
        - ip route add 192.168.26.16/30 via 192.168.26.34 dev eth1
        - ip route add 192.168.26.0/29 via 192.168.26.34 dev eth1
        - ip route add 192.168.26.8/30 via 192.168.26.34 dev eth1
        - ip route add 192.168.26.88/30 via 192.168.26.98 dev eth2
        - ip route add 192.168.26.80/29 via 192.168.26.98 dev eth2
        - ip route add 192.168.26.64/29 via 192.168.26.98 dev eth2
        - ip route add 192.168.26.72/29 via 192.168.26.98 dev eth2

    EOT

    file_name = "script-family-mart-d26.yaml"
  }
}

resource "proxmox_virtual_environment_file" "script-indomaret" {
  content_type = "snippets"
  datastore_id = "local"
  node_name    = "its"
  file_mode    = "0700"

  source_raw {
    data = <<-EOF
    #cloud-config
    users:
        - name: "d26"
          password: "d26"

    runcmd:
        - sysctl -w net.ipv4.ip_forward=1
        - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
        - sysctl -p
        - netplan apply
        - ip link set eth0 up
        - ip link set eth1 up
        - ip route add 0.0.0.0/0 via 192.168.26.33 dev eth0
        - ip route add 192.168.26.0/29 via 192.168.26.18 dev eth1
        - ip route add 192.168.26.8/30 via 192.168.26.18 dev eth1

    EOF

    file_name = "script-indomaret-d26.yaml"
  }
}

resource "proxmox_virtual_environment_file" "script-alfamart" {
  content_type = "snippets"
  datastore_id = "local"
  node_name    = "its"
  file_mode    = "0700"

  source_raw {
    data = <<-EOF
    #cloud-config
    users:
        - name: "d26"
          password: "d26"

    runcmd:
        - sysctl -w net.ipv4.ip_forward=1
        - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
        - sysctl -p
        - netplan apply
        - ip link set eth0 up
        - ip link set eth1 up
        - ip link set eth2 up
        - ip route add 0.0.0.0/0 via 192.168.26.17 dev eth0
    EOF

    file_name = "script-alfamart-d26.yaml"
  }
}

resource "proxmox_virtual_environment_file" "script-superindo" {
  content_type = "snippets"
  datastore_id = "local"
  node_name    = "its"
  file_mode    = "0700"

  source_raw {
    data = <<-EOF
    #cloud-config
    users:
        - name: "d26"
          password: "d26"

    runcmd:
        - sysctl -w net.ipv4.ip_forward=1
        - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
        - sysctl -p
        - netplan apply
        - ip link set eth0 up
        - ip link set eth1 up
        - ip link set eth2 up
        - ip route add 0.0.0.0/0 via 192.168.26.97 dev eth0
        - ip route add 192.168.26.80/29 via 192.168.26.90 dev eth1
        - ip route add 192.168.26.72/29 via 192.168.26.66 dev eth2
    EOF

    file_name = "script-superindo-d26.yaml"
  }
}

resource "proxmox_virtual_environment_file" "script-sakinah" {
  content_type = "snippets"
  datastore_id = "local"
  node_name    = "its"
  file_mode    = "0700"

  source_raw {
    data = <<-EOF
    #cloud-config
    users:
        - name: "d26"
          password: "d26"

    runcmd:
        - sysctl -w net.ipv4.ip_forward=1
        - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
        - sysctl -p
        - netplan apply
        - ip link set eth0 up
        - ip link set eth1 up
        - ip route add 0.0.0.0/0 via 192.168.26.89 dev eth0
    EOF

    file_name = "script-sakinah-d26.yaml"
  }
}

resource "proxmox_virtual_environment_file" "script-its-mart" {
  content_type = "snippets"
  datastore_id = "local"
  node_name    = "its"
  file_mode    = "0700"

  source_raw {
    data = <<-EOF
    #cloud-config
    users:
        - name: "d26"
          password: "d26"
          
    runcmd:
        - sysctl -w net.ipv4.ip_forward=1
        - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
        - sysctl -p
        - netplan apply
        - ip link set eth0 up
        - ip link set eth1 up
        - ip route add 0.0.0.0/0 via 192.168.26.65 dev eth0
    EOF

    file_name = "script-its-mart-d26.yaml"
  }
}

resource "proxmox_virtual_environment_vm" "family-mart-d26" {
    name = "family-mart-d26"
    node_name = "its"
    on_boot = true
    stop_on_destroy = true
    scsi_hardware = "virtio-scsi-single"
    vm_id = lookup(var.vm_id_list, "family-mart-d26")

    clone {
        datastore_id = "local-lvm"
        node_name = "its"
        vm_id = 5555
    }

    agent {
        enabled = true
    }

    initialization {
      ip_config { #eth1
        ipv4 {
          address = lookup(var.ip_list, "family-mart-eth1")
        }
      }

      ip_config { #eth2
        ipv4 {
          address = lookup(var.ip_list, "family-mart-eth2")
        }
      }

      user_data_file_id = proxmox_virtual_environment_file.script-family-mart.id
    }

    cpu {
        cores = 1
        type = "x86-64-v2-AES"
    }

    memory {
        dedicated = 1024
        floating = 1024
    }
    
    disk {
        datastore_id = "local-lvm"
        file_id = "local:iso/focal-server-cloudimg-amd64.img"
        file_format = "raw"
        interface = "virtio0"
        iothread = true
        size = 3 # GB
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
}

resource "proxmox_virtual_environment_vm" "indomaret-d26" {
    name = "indomaret-d26"
    node_name = "its"
    on_boot = true
    stop_on_destroy = true
    scsi_hardware = "virtio-scsi-single"
    vm_id = lookup(var.vm_id_list, "indomaret-d26")

    clone {
        datastore_id = "local-lvm"
        node_name = "its"
        vm_id = 5555
    }

    agent {
        enabled = true
    }

    initialization {
      ip_config { # eth0
        ipv4 {
          address = lookup(var.ip_list, "indomaret-eth0")
        }
      }

      ip_config { #eth1
        ipv4 {
          address = lookup(var.ip_list, "indomaret-eth1")
        }
      }

      user_data_file_id = proxmox_virtual_environment_file.script-indomaret.id
    }

    cpu {
        cores = 1
        type = "x86-64-v2-AES"
    }

    memory {
        dedicated = 1024
        floating = 1024
    }
    
    disk {
        datastore_id = "local-lvm"
        file_id = "local:iso/focal-server-cloudimg-amd64.img"
        file_format = "raw"
        interface = "virtio0"
        iothread = true
        size = 3 # GB
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
}

resource "proxmox_virtual_environment_vm" "alfamart-d26" {
    name = "alfamart-d26"
    node_name = "its"
    on_boot = true
    stop_on_destroy = true
    scsi_hardware = "virtio-scsi-single"
    vm_id = lookup(var.vm_id_list, "alfamart-d26")

    clone {
        datastore_id = "local-lvm"
        node_name = "its"
        vm_id = 5555
    }

    agent {
        enabled = true
    }

    initialization {
      ip_config { # eth0
        ipv4 {
          address = lookup(var.ip_list, "alfamart-eth0")
        }
      }

      ip_config { #eth1
        ipv4 {
          address = lookup(var.ip_list, "alfamart-eth1")
        }
      }

      ip_config { #eth2
        ipv4 {
          address = lookup(var.ip_list, "alfamart-eth2")
        }
      }

      user_data_file_id = proxmox_virtual_environment_file.script-alfamart.id
    }

    cpu {
        cores = 1
        type = "x86-64-v2-AES"
    }

    memory {
        dedicated = 1024
        floating = 1024
    }
    
    disk {
        datastore_id = "local-lvm"
        file_id = "local:iso/focal-server-cloudimg-amd64.img"
        file_format = "raw"
        interface = "virtio0"
        iothread = true
        size = 3 # GB
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
    
    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
}

resource "proxmox_virtual_environment_vm" "nasi-uduk-d26" {
    name = "nasi-uduk-d26"
    node_name = "its"
    on_boot = true
    stop_on_destroy = true
    scsi_hardware = "virtio-scsi-single"
    vm_id = lookup(var.vm_id_list, "nasi-uduk-d26")

    clone {
        datastore_id = "local-lvm"
        node_name = "its"
        vm_id = 5555
    }

    agent {
        enabled = false
    }

    initialization {
      ip_config { # eth0
        ipv4 {
          address = lookup(var.ip_list, "nasi-uduk")
          gateway = lookup(var.gateaway_list, "nasi-uduk-geprek")
        }
      }

      user_account {
        username = "d26"
        password = "d26"
      }
    }

    cpu {
        cores = 1
        type = "x86-64-v2-AES"
    }

    memory {
        dedicated = 1024
        floating = 1024
    }
    
    disk {
        datastore_id = "local-lvm"
        file_id = "local:iso/focal-server-cloudimg-amd64.img"
        file_format = "raw"
        interface = "virtio0"
        iothread = true
        size = 3 # GB
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
}

resource "proxmox_virtual_environment_vm" "geprek-d26" {
    name = "geprek-d26"
    node_name = "its"
    on_boot = true
    stop_on_destroy = true
    scsi_hardware = "virtio-scsi-single"
    vm_id = lookup(var.vm_id_list, "geprek-d26")

    clone {
        datastore_id = "local-lvm"
        node_name = "its"
        vm_id = 5555
    }

    agent {
        enabled = false
    }

    initialization {
      ip_config { # eth0
        ipv4 {
          address = lookup(var.ip_list, "geprek")
          gateway = lookup(var.gateaway_list, "nasi-uduk-geprek")
        }
      }

      user_account {
        username = "d26"
        password = "d26"
      }
    }

    cpu {
        cores = 1
        type = "x86-64-v2-AES"
    }

    memory {
        dedicated = 1024
        floating = 1024
    }
    
    disk {
        datastore_id = "local-lvm"
        file_id = "local:iso/focal-server-cloudimg-amd64.img"
        file_format = "raw"
        interface = "virtio0"
        iothread = true
        size = 3 # GB
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
}

resource "proxmox_virtual_environment_vm" "kwetiaw-d26" {
    name = "kwetiaw-d26"
    node_name = "its"
    on_boot = true
    stop_on_destroy = true
    scsi_hardware = "virtio-scsi-single"
    vm_id = lookup(var.vm_id_list, "kwetiaw-d26")

    clone {
        datastore_id = "local-lvm"
        node_name = "its"
        vm_id = 5555
    }

    agent {
        enabled = false
    }

    initialization {
      ip_config { # eth0
        ipv4 {
          address = lookup(var.ip_list, "kwetiaw")
          gateway = lookup(var.gateaway_list, "kwetiaw")
        }
      }

      user_account {
        username = "d26"
        password = "d26"
      }
    }

    cpu {
        cores = 1
        type = "x86-64-v2-AES"
    }

    memory {
        dedicated = 1024
        floating = 1024
    }
    
    disk {
        datastore_id = "local-lvm"
        file_id = "local:iso/focal-server-cloudimg-amd64.img"
        file_format = "raw"
        interface = "virtio0"
        iothread = true
        size = 3 # GB
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
}

resource "proxmox_virtual_environment_vm" "superindo-d26" {
    name = "superindo-d26"
    node_name = "its"
    on_boot = true
    stop_on_destroy = true
    scsi_hardware = "virtio-scsi-single"
    vm_id = lookup(var.vm_id_list, "superindo-d26")

    clone {
        datastore_id = "local-lvm"
        node_name = "its"
        vm_id = 5555
    }

    agent {
        enabled = true
    }

    initialization {
      ip_config { # eth0
        ipv4 {
          address = lookup(var.ip_list, "superindo-eth0")
        }
      }

      ip_config { #eth1
        ipv4 {
          address = lookup(var.ip_list, "superindo-eth1")
        }
      }

      ip_config { #eth2
        ipv4 {
          address = lookup(var.ip_list, "superindo-eth2")
        }
      }

      user_data_file_id = proxmox_virtual_environment_file.script-superindo.id
    }

    cpu {
        cores = 1
        type = "x86-64-v2-AES"
    }

    memory {
        dedicated = 1024
        floating = 1024
    }
    
    disk {
        datastore_id = "local-lvm"
        file_id = "local:iso/focal-server-cloudimg-amd64.img"
        file_format = "raw"
        interface = "virtio0"
        iothread = true
        size = 3 # GB
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
}

resource "proxmox_virtual_environment_vm" "sakinah-d26" {
    name = "sakinah-d26"
    node_name = "its"
    on_boot = true
    stop_on_destroy = true
    scsi_hardware = "virtio-scsi-single"
    vm_id = lookup(var.vm_id_list, "sakinah-d26")

    clone {
        datastore_id = "local-lvm"
        node_name = "its"
        vm_id = 5555
    }

    agent {
        enabled = true
    }

    initialization {
      ip_config { # eth0
        ipv4 {
          address = lookup(var.ip_list, "sakinah-eth0")
        }
      }

      ip_config { #eth1
        ipv4 {
          address = lookup(var.ip_list, "sakinah-eth1")
        }
      }

      user_data_file_id = proxmox_virtual_environment_file.script-sakinah.id
    }

    cpu {
        cores = 1
        type = "x86-64-v2-AES"
    }

    memory {
        dedicated = 1024
        floating = 1024
    }
    
    disk {
        datastore_id = "local-lvm"
        file_id = "local:iso/focal-server-cloudimg-amd64.img"
        file_format = "raw"
        interface = "virtio0"
        iothread = true
        size = 3 # GB
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
}

resource "proxmox_virtual_environment_vm" "pangsit-d26" {
    name = "pangsit-d26"
    node_name = "its"
    on_boot = true
    stop_on_destroy = true
    scsi_hardware = "virtio-scsi-single"
    vm_id = lookup(var.vm_id_list, "pangsit-d26")

    clone {
        datastore_id = "local-lvm"
        node_name = "its"
        vm_id = 5555
    }

    agent {
        enabled = false
    }

    initialization {
      ip_config { # eth0
        ipv4 {
          address = lookup(var.ip_list, "pangsit")
          gateway = lookup(var.gateaway_list, "pangsit-naspad")
        }
      }

      user_account {
        username = "d26"
        password = "d26"
      }
    }

    cpu {
        cores = 1
        type = "x86-64-v2-AES"
    }

    memory {
        dedicated = 1024
        floating = 1024
    }
    
    disk {
        datastore_id = "local-lvm"
        file_id = "local:iso/focal-server-cloudimg-amd64.img"
        file_format = "raw"
        interface = "virtio0"
        iothread = true
        size = 3 # GB
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
}

resource "proxmox_virtual_environment_vm" "naspad-d26" {
    name = "naspad-d26"
    node_name = "its"
    on_boot = true
    stop_on_destroy = true
    scsi_hardware = "virtio-scsi-single"
    vm_id = lookup(var.vm_id_list, "naspad-d26")

    clone {
        datastore_id = "local-lvm"
        node_name = "its"
        vm_id = 5555
    }

    agent {
        enabled = false
    }

    initialization {
      ip_config { # eth0
        ipv4 {
          address = lookup(var.ip_list, "naspad")
          gateway = lookup(var.gateaway_list, "pangsit-naspad")
        }
      }

      user_account {
        username = "d26"
        password = "d26"
      }
    }

    cpu {
        cores = 1
        type = "x86-64-v2-AES"
    }

    memory {
        dedicated = 1024
        floating = 1024
    }
    
    disk {
        datastore_id = "local-lvm"
        file_id = "local:iso/focal-server-cloudimg-amd64.img"
        file_format = "raw"
        interface = "virtio0"
        iothread = true
        size = 3 # GB
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
}

resource "proxmox_virtual_environment_vm" "ikan-fillet-d26" {
    name = "ikan-fillet-d26"
    node_name = "its"
    on_boot = true
    stop_on_destroy = true
    scsi_hardware = "virtio-scsi-single"
    vm_id = lookup(var.vm_id_list, "ikan-fillet-d26")

    clone {
        datastore_id = "local-lvm"
        node_name = "its"
        vm_id = 5555
    }

    agent {
        enabled = false
    }

    initialization {
      ip_config { # eth0
        ipv4 {
          address = lookup(var.ip_list, "ikan-fillet")
          gateway = lookup(var.gateaway_list, "ikan-fillet")
        }
      }

      user_account {
        username = "d26"
        password = "d26"
      }
    }

    cpu {
        cores = 1
        type = "x86-64-v2-AES"
    }

    memory {
        dedicated = 1024
        floating = 1024
    }
    
    disk {
        datastore_id = "local-lvm"
        file_id = "local:iso/focal-server-cloudimg-amd64.img"
        file_format = "raw"
        interface = "virtio0"
        iothread = true
        size = 3 # GB
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
}

resource "proxmox_virtual_environment_vm" "its-mart-d26" {
    name = "its-mart-d26"
    node_name = "its"
    on_boot = true
    stop_on_destroy = true
    scsi_hardware = "virtio-scsi-single"
    vm_id = lookup(var.vm_id_list, "its-mart-d26")

    clone {
        datastore_id = "local-lvm"
        node_name = "its"
        vm_id = 5555
    }

    agent {
        enabled = true
    }

    initialization {
      ip_config { # eth0
        ipv4 {
          address = lookup(var.ip_list, "its-mart-eth0")
        }
      }

      ip_config { #eth1
        ipv4 {
          address = lookup(var.ip_list, "its-mart-eth1")
        }
      }

      user_data_file_id = proxmox_virtual_environment_file.script-its-mart.id
    }

    cpu {
        cores = 1
        type = "x86-64-v2-AES"
    }

    memory {
        dedicated = 1024
        floating = 1024
    }
    
    disk {
        datastore_id = "local-lvm"
        file_id = "local:iso/focal-server-cloudimg-amd64.img"
        file_format = "raw"
        interface = "virtio0"
        iothread = true
        size = 3 # GB
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
}

resource "proxmox_virtual_environment_vm" "tahu-tek-d26" {
    name = "tahu-tek-d26"
    node_name = "its"
    on_boot = true
    stop_on_destroy = true
    scsi_hardware = "virtio-scsi-single"
    vm_id = lookup(var.vm_id_list, "tahu-tek-d26")

    clone {
        datastore_id = "local-lvm"
        node_name = "its"
        vm_id = 5555
    }

    agent {
        enabled = false
    }

    initialization {
      ip_config { # eth0
        ipv4 {
          address = lookup(var.ip_list, "tahu-tek")
          gateway = lookup(var.gateaway_list, "tahu-tek-sego-jamur")
        }
      }

      user_account {
        username = "d26"
        password = "d26"
      }
    }

    cpu {
        cores = 1
        type = "x86-64-v2-AES"
    }

    memory {
        dedicated = 1024
        floating = 1024
    }
    
    disk {
        datastore_id = "local-lvm"
        file_id = "local:iso/focal-server-cloudimg-amd64.img"
        file_format = "raw"
        interface = "virtio0"
        iothread = true
        size = 3 # GB
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
}

resource "proxmox_virtual_environment_vm" "sego-jamur-d26" {
    name = "sego-jamur-d26"
    node_name = "its"
    on_boot = true
    stop_on_destroy = true
    scsi_hardware = "virtio-scsi-single"
    vm_id = lookup(var.vm_id_list, "sego-jamur-d26")

    clone {
        datastore_id = "local-lvm"
        node_name = "its"
        vm_id = 5555
    }

    agent {
        enabled = false
    }

    initialization {
      ip_config { # eth0
        ipv4 {
          address = lookup(var.ip_list, "sego-jamur")
          gateway = lookup(var.gateaway_list, "tahu-tek-sego-jamur")
        }
      }

      user_account {
        username = "d26"
        password = "d26"
      }
    }

    cpu {
        cores = 1
        type = "x86-64-v2-AES"
    }

    memory {
        dedicated = 1024
        floating = 1024
    }
    
    disk {
        datastore_id = "local-lvm"
        file_id = "local:iso/focal-server-cloudimg-amd64.img"
        file_format = "raw"
        interface = "virtio0"
        iothread = true
        size = 3 # GB
    }

    network_device {
        enabled = true
        firewall = false
        bridge = "vmbr0"
    }
}
```
Explanation:
- ```
  terraform {
  required_providers {
    proxmox = {
      source  = "bpg/proxmox"
      version = "0.66.1"
      }
    }
  }
  ```
  Penjelasan: Digunakan untuk mendeklarasikan provider yang akan dipakai, yaitu Proxmox
- ```
  provider "proxmox" {
  endpoint = var.proxmox_url
  username = var.proxmox_user
  password = var.proxmox_password
  insecure = true
  }
  ```
  Penjelasan: Digunakan untuk _login_ ke dalam URL Proxmox yang didefinisikan di `variables.tf`. `insecure` digunakan untuk masuk ke dalam website yang _Not Secure_.
- ```
    resource "proxmox_virtual_environment_file" "script-family-mart" {
    content_type = "snippets"
    datastore_id = "local"
    node_name    = "its"
    file_mode    = "0700"
  
    source_raw {
      data = <<-EOT
      #cloud-config
      users:
          - name: "d26"
            password: "d26"
      
      runcmd:
          - sysctl -w net.ipv4.ip_forward=1
          - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
          - sysctl -p
          - netplan apply
          - ip link set eth0 up
          - ip link set eth1 up
          - ip link set eth2 up
          - ip route add 192.168.26.16/30 via 192.168.26.34 dev eth1
          - ip route add 192.168.26.0/29 via 192.168.26.34 dev eth1
          - ip route add 192.168.26.8/30 via 192.168.26.34 dev eth1
          - ip route add 192.168.26.88/30 via 192.168.26.98 dev eth2
          - ip route add 192.168.26.80/29 via 192.168.26.98 dev eth2
          - ip route add 192.168.26.64/29 via 192.168.26.98 dev eth2
          - ip route add 192.168.26.72/29 via 192.168.26.98 dev eth2
  
      EOT
  
      file_name = "script-family-mart-d26.yaml"
    }
  }
  
  resource "proxmox_virtual_environment_file" "script-indomaret" {
    content_type = "snippets"
    datastore_id = "local"
    node_name    = "its"
    file_mode    = "0700"
  
    source_raw {
      data = <<-EOF
      #cloud-config
      users:
          - name: "d26"
            password: "d26"
  
      runcmd:
          - sysctl -w net.ipv4.ip_forward=1
          - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
          - sysctl -p
          - netplan apply
          - ip link set eth0 up
          - ip link set eth1 up
          - ip route add 0.0.0.0/0 via 192.168.26.33 dev eth0
          - ip route add 192.168.26.0/29 via 192.168.26.18 dev eth1
          - ip route add 192.168.26.8/30 via 192.168.26.18 dev eth1
  
      EOF
  
      file_name = "script-indomaret-d26.yaml"
    }
  }
  
  resource "proxmox_virtual_environment_file" "script-alfamart" {
    content_type = "snippets"
    datastore_id = "local"
    node_name    = "its"
    file_mode    = "0700"
  
    source_raw {
      data = <<-EOF
      #cloud-config
      users:
          - name: "d26"
            password: "d26"
  
      runcmd:
          - sysctl -w net.ipv4.ip_forward=1
          - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
          - sysctl -p
          - netplan apply
          - ip link set eth0 up
          - ip link set eth1 up
          - ip link set eth2 up
          - ip route add 0.0.0.0/0 via 192.168.26.17 dev eth0
      EOF
  
      file_name = "script-alfamart-d26.yaml"
    }
  }
  
  resource "proxmox_virtual_environment_file" "script-superindo" {
    content_type = "snippets"
    datastore_id = "local"
    node_name    = "its"
    file_mode    = "0700"
  
    source_raw {
      data = <<-EOF
      #cloud-config
      users:
          - name: "d26"
            password: "d26"
  
      runcmd:
          - sysctl -w net.ipv4.ip_forward=1
          - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
          - sysctl -p
          - netplan apply
          - ip link set eth0 up
          - ip link set eth1 up
          - ip link set eth2 up
          - ip route add 0.0.0.0/0 via 192.168.26.97 dev eth0
          - ip route add 192.168.26.80/29 via 192.168.26.90 dev eth1
          - ip route add 192.168.26.72/29 via 192.168.26.66 dev eth2
      EOF
  
      file_name = "script-superindo-d26.yaml"
    }
  }
  
  resource "proxmox_virtual_environment_file" "script-sakinah" {
    content_type = "snippets"
    datastore_id = "local"
    node_name    = "its"
    file_mode    = "0700"
  
    source_raw {
      data = <<-EOF
      #cloud-config
      users:
          - name: "d26"
            password: "d26"
  
      runcmd:
          - sysctl -w net.ipv4.ip_forward=1
          - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
          - sysctl -p
          - netplan apply
          - ip link set eth0 up
          - ip link set eth1 up
          - ip route add 0.0.0.0/0 via 192.168.26.89 dev eth0
      EOF
  
      file_name = "script-sakinah-d26.yaml"
    }
  }
  
  resource "proxmox_virtual_environment_file" "script-its-mart" {
    content_type = "snippets"
    datastore_id = "local"
    node_name    = "its"
    file_mode    = "0700"
  
    source_raw {
      data = <<-EOF
      #cloud-config
      users:
          - name: "d26"
            password: "d26"
            
      runcmd:
          - sysctl -w net.ipv4.ip_forward=1
          - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
          - sysctl -p
          - netplan apply
          - ip link set eth0 up
          - ip link set eth1 up
          - ip route add 0.0.0.0/0 via 192.168.26.65 dev eth0
      EOF
  
      file_name = "script-its-mart-d26.yaml"
    }
  }
  ```
  Penjelasan: Script-script `cloud-init` diatas digunakan untuk men-_setup_ router-router yang akan digunakan sebagi "jalan"
- ```
  resource "proxmox_virtual_environment_vm" "family-mart-d26" {
      name = "family-mart-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "family-mart-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = true
      }
  
      initialization {
        ip_config { #eth1
          ipv4 {
            address = lookup(var.ip_list, "family-mart-eth1")
          }
        }
  
        ip_config { #eth2
          ipv4 {
            address = lookup(var.ip_list, "family-mart-eth2")
          }
        }
  
        user_data_file_id = proxmox_virtual_environment_file.script-family-mart.id
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }
  
  resource "proxmox_virtual_environment_vm" "indomaret-d26" {
      name = "indomaret-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "indomaret-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = true
      }
  
      initialization {
        ip_config { # eth0
          ipv4 {
            address = lookup(var.ip_list, "indomaret-eth0")
          }
        }
  
        ip_config { #eth1
          ipv4 {
            address = lookup(var.ip_list, "indomaret-eth1")
          }
        }
  
        user_data_file_id = proxmox_virtual_environment_file.script-indomaret.id
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }
  
  resource "proxmox_virtual_environment_vm" "alfamart-d26" {
      name = "alfamart-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "alfamart-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = true
      }
  
      initialization {
        ip_config { # eth0
          ipv4 {
            address = lookup(var.ip_list, "alfamart-eth0")
          }
        }
  
        ip_config { #eth1
          ipv4 {
            address = lookup(var.ip_list, "alfamart-eth1")
          }
        }
  
        ip_config { #eth2
          ipv4 {
            address = lookup(var.ip_list, "alfamart-eth2")
          }
        }
  
        user_data_file_id = proxmox_virtual_environment_file.script-alfamart.id
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
      
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }

  resource "proxmox_virtual_environment_vm" "superindo-d26" {
      name = "superindo-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "superindo-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = true
      }
  
      initialization {
        ip_config { # eth0
          ipv4 {
            address = lookup(var.ip_list, "superindo-eth0")
          }
        }
  
        ip_config { #eth1
          ipv4 {
            address = lookup(var.ip_list, "superindo-eth1")
          }
        }
  
        ip_config { #eth2
          ipv4 {
            address = lookup(var.ip_list, "superindo-eth2")
          }
        }
  
        user_data_file_id = proxmox_virtual_environment_file.script-superindo.id
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }
  
  resource "proxmox_virtual_environment_vm" "sakinah-d26" {
      name = "sakinah-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "sakinah-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = true
      }
  
      initialization {
        ip_config { # eth0
          ipv4 {
            address = lookup(var.ip_list, "sakinah-eth0")
          }
        }
  
        ip_config { #eth1
          ipv4 {
            address = lookup(var.ip_list, "sakinah-eth1")
          }
        }
  
        user_data_file_id = proxmox_virtual_environment_file.script-sakinah.id
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }
  
  resource "proxmox_virtual_environment_vm" "its-mart-d26" {
      name = "its-mart-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "its-mart-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = true
      }
  
      initialization {
        ip_config { # eth0
          ipv4 {
            address = lookup(var.ip_list, "its-mart-eth0")
          }
        }
  
        ip_config { #eth1
          ipv4 {
            address = lookup(var.ip_list, "its-mart-eth1")
          }
        }
  
        user_data_file_id = proxmox_virtual_environment_file.script-its-mart.id
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }
  ```
  Penjelasan: Potongan kode tersebut digunakan untuk meng-_spawn_ router-router yang akan digunakan. Jumlah `ip_config` dan `network_device` tergantung banyaknya _interface_ yang akan digunakan. `user_data_file_id` digunakan untuk memanggil script ke dalam routernya masing-masing dan `agent` digunakan untuk menjalankan script tersebut.
- ```
  resource "proxmox_virtual_environment_vm" "nasi-uduk-d26" {
      name = "nasi-uduk-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "nasi-uduk-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = false
      }
  
      initialization {
        ip_config { # eth0
          ipv4 {
            address = lookup(var.ip_list, "nasi-uduk")
            gateway = lookup(var.gateaway_list, "nasi-uduk-geprek")
          }
        }
  
        user_account {
          username = "d26"
          password = "d26"
        }
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }
  
  resource "proxmox_virtual_environment_vm" "geprek-d26" {
      name = "geprek-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "geprek-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = false
      }
  
      initialization {
        ip_config { # eth0
          ipv4 {
            address = lookup(var.ip_list, "geprek")
            gateway = lookup(var.gateaway_list, "nasi-uduk-geprek")
          }
        }
  
        user_account {
          username = "d26"
          password = "d26"
        }
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }
  
  resource "proxmox_virtual_environment_vm" "kwetiaw-d26" {
      name = "kwetiaw-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "kwetiaw-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = false
      }
  
      initialization {
        ip_config { # eth0
          ipv4 {
            address = lookup(var.ip_list, "kwetiaw")
            gateway = lookup(var.gateaway_list, "kwetiaw")
          }
        }
  
        user_account {
          username = "d26"
          password = "d26"
        }
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }
  resource "proxmox_virtual_environment_vm" "pangsit-d26" {
      name = "pangsit-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "pangsit-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = false
      }
  
      initialization {
        ip_config { # eth0
          ipv4 {
            address = lookup(var.ip_list, "pangsit")
            gateway = lookup(var.gateaway_list, "pangsit-naspad")
          }
        }
  
        user_account {
          username = "d26"
          password = "d26"
        }
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }
  
  resource "proxmox_virtual_environment_vm" "naspad-d26" {
      name = "naspad-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "naspad-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = false
      }
  
      initialization {
        ip_config { # eth0
          ipv4 {
            address = lookup(var.ip_list, "naspad")
            gateway = lookup(var.gateaway_list, "pangsit-naspad")
          }
        }
  
        user_account {
          username = "d26"
          password = "d26"
        }
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }
  
  resource "proxmox_virtual_environment_vm" "ikan-fillet-d26" {
      name = "ikan-fillet-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "ikan-fillet-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = false
      }
  
      initialization {
        ip_config { # eth0
          ipv4 {
            address = lookup(var.ip_list, "ikan-fillet")
            gateway = lookup(var.gateaway_list, "ikan-fillet")
          }
        }
  
        user_account {
          username = "d26"
          password = "d26"
        }
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }
  resource "proxmox_virtual_environment_vm" "nasi-uduk-d26" {
      name = "nasi-uduk-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "nasi-uduk-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = false
      }
  
      initialization {
        ip_config { # eth0
          ipv4 {
            address = lookup(var.ip_list, "nasi-uduk")
            gateway = lookup(var.gateaway_list, "nasi-uduk-geprek")
          }
        }
  
        user_account {
          username = "d26"
          password = "d26"
        }
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }
  
  resource "proxmox_virtual_environment_vm" "geprek-d26" {
      name = "geprek-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "geprek-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = false
      }
  
      initialization {
        ip_config { # eth0
          ipv4 {
            address = lookup(var.ip_list, "geprek")
            gateway = lookup(var.gateaway_list, "nasi-uduk-geprek")
          }
        }
  
        user_account {
          username = "d26"
          password = "d26"
        }
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }
  
  resource "proxmox_virtual_environment_vm" "kwetiaw-d26" {
      name = "kwetiaw-d26"
      node_name = "its"
      on_boot = true
      stop_on_destroy = true
      scsi_hardware = "virtio-scsi-single"
      vm_id = lookup(var.vm_id_list, "kwetiaw-d26")
  
      clone {
          datastore_id = "local-lvm"
          node_name = "its"
          vm_id = 5555
      }
  
      agent {
          enabled = false
      }
  
      initialization {
        ip_config { # eth0
          ipv4 {
            address = lookup(var.ip_list, "kwetiaw")
            gateway = lookup(var.gateaway_list, "kwetiaw")
          }
        }
  
        user_account {
          username = "d26"
          password = "d26"
        }
      }
  
      cpu {
          cores = 1
          type = "x86-64-v2-AES"
      }
  
      memory {
          dedicated = 1024
          floating = 1024
      }
      
      disk {
          datastore_id = "local-lvm"
          file_id = "local:iso/focal-server-cloudimg-amd64.img"
          file_format = "raw"
          interface = "virtio0"
          iothread = true
          size = 3 # GB
      }
  
      network_device {
          enabled = true
          firewall = false
          bridge = "vmbr0"
      }
  }
  ```
  Penjelasan: Potongan kode diatas digunakan untuk meng-_spawn_ client dan server yang akan disambungkan. Bedanya dengan kode yang digunakan untuk meng-_spawn_ router adalah banyaknya jumlah _interface_ (hanya 1 untuk client), `user_account` untuk login ke client, dan `gateway` (sebagai "perkenalan" dengan router yang satu subnet dengan client tersebut)
  
### variables.tf Configuration
```
variable "proxmox_url" {
  type    = string
  default = "https://10.21.71.4:8006"
}

variable "proxmox_user" {
  type    = string
  default = "D26@pam"
}

variable "proxmox_password" {
  type    = string
  default = "7MC4#"
}

variable "vm_id_list" {
  type        = map(number)
  default = {
    "family-mart-d26" = 550 
    "indomaret-d26" = 551
    "alfamart-d26" = 552
    "superindo-d26" = 553
    "sakinah-d26" = 554
    "its-mart-d26" = 555
    "nasi-uduk-d26" = 556
    "geprek-d26" = 557
    "kwetiaw-d26" = 558
    "pangsit-d26" = 559
    "naspad-d26" = 560
    "ikan-fillet-d26" = 561
    "tahu-tek-d26" = 562
    "sego-jamur-d26" = 563
  }
}

variable "ip_list" {
  type        = map(string)
  default = {
    "family-mart-eth1" = "192.168.26.33/30"
    "family-mart-eth2" = "192.168.26.97/30"
    "indomaret-eth0" = "192.168.26.34/30"
    "indomaret-eth1" = "192.168.26.17/30"
    "alfamart-eth0" = "192.168.26.18/30"
    "alfamart-eth1" = "192.168.26.1/29"
    "alfamart-eth2" = "192.168.26.9/30"
    "superindo-eth0" = "192.168.26.98/30"
    "superindo-eth1" = "192.168.26.89/30"
    "superindo-eth2" = "192.168.26.65/29"
    "sakinah-eth0" = "192.168.26.90/30"
    "sakinah-eth1" = "192.168.26.81/29"
    "its-mart-eth0" = "192.168.26.66/29"
    "its-mart-eth1" = "192.168.26.73/29"
    "nasi-uduk" = "192.168.26.2/29"
    "geprek" = "192.168.26.3/29"
    "kwetiaw" = "192.168.26.10/30"
    "tahu-tek" = "192.168.26.74/29"
    "sego-jamur" = "192.168.26.75/29"
    "ikan-fillet" = "192.168.26.67/29"
    "pangsit" = "192.168.26.82/29"
    "naspad" = "192.168.26.83/29"
  }
}

variable "gateaway_list" {
  type        = map(string)
  default = {
    "nasi-uduk-geprek" = "192.168.26.1"
    "kwetiaw" = "192.168.26.9"
    "tahu-tek-sego-jamur" = "192.168.26.73"
    "ikan-fillet" = "192.168.26.65"
    "pangsit-naspad" = "192.168.26.81"
  }
}
```
Explanation
- ```
  variable "proxmox_url" {
    type    = string
    default = "https://10.21.71.4:8006"
  }
  ```
  Penjelasan: Potongan kode tersebut adalah site proxmox yang akan digunakan
- ```
  variable "proxmox_user" {
    type    = string
    default = "D26@pam"
  }
  
  variable "proxmox_password" {
    type    = string
    default = "7MC4#"
  }
  ```
  Penjelasan: Potongan kode tersebut digunakan untuk _login_ ke dalam proxmox
- ```
  variable "vm_id_list" {
    type        = map(number)
    default = {
      "family-mart-d26" = 550 
      "indomaret-d26" = 551
      "alfamart-d26" = 552
      "superindo-d26" = 553
      "sakinah-d26" = 554
      "its-mart-d26" = 555
      "nasi-uduk-d26" = 556
      "geprek-d26" = 557
      "kwetiaw-d26" = 558
      "pangsit-d26" = 559
      "naspad-d26" = 560
      "ikan-fillet-d26" = 561
      "tahu-tek-d26" = 562
      "sego-jamur-d26" = 563
    }
  }
  ```
  Penjelasan: Potongan kode diatas digunakan untuk mendeklarasikan ID-ID milik `VM` / router, client, dan server yang akan digunakan
- ```
  variable "ip_list" {
    type        = map(string)
    default = {
      "family-mart-eth1" = "192.168.26.33/30"
      "family-mart-eth2" = "192.168.26.97/30"
      "indomaret-eth0" = "192.168.26.34/30"
      "indomaret-eth1" = "192.168.26.17/30"
      "alfamart-eth0" = "192.168.26.18/30"
      "alfamart-eth1" = "192.168.26.1/29"
      "alfamart-eth2" = "192.168.26.9/30"
      "superindo-eth0" = "192.168.26.98/30"
      "superindo-eth1" = "192.168.26.89/30"
      "superindo-eth2" = "192.168.26.65/29"
      "sakinah-eth0" = "192.168.26.90/30"
      "sakinah-eth1" = "192.168.26.81/29"
      "its-mart-eth0" = "192.168.26.66/29"
      "its-mart-eth1" = "192.168.26.73/29"
      "nasi-uduk" = "192.168.26.2/29"
      "geprek" = "192.168.26.3/29"
      "kwetiaw" = "192.168.26.10/30"
      "tahu-tek" = "192.168.26.74/29"
      "sego-jamur" = "192.168.26.75/29"
      "ikan-fillet" = "192.168.26.67/29"
      "pangsit" = "192.168.26.82/29"
      "naspad" = "192.168.26.83/29"
    }
  }
  ```
  Penjelasan: Potongan kode diatas digunakan untuk mendeklarasikan IP-IP hasil perhitungan milik router, client, dan server
- ```
  variable "gateaway_list" {
    type        = map(string)
    default = {
      "nasi-uduk-geprek" = "192.168.26.1"
      "kwetiaw" = "192.168.26.9"
      "tahu-tek-sego-jamur" = "192.168.26.73"
      "ikan-fillet" = "192.168.26.65"
      "pangsit-naspad" = "192.168.26.81"
    }
  }
  ```
  Penjelasan: Potongan kode diatas digunakan untuk mendeklarasikan gateway-gateway milik client dan server ke router
### Testing

- Client - client
  - Client di 1 subnet yang sama
    - nasi-uduk - geprek
      <br>![image](https://github.com/user-attachments/assets/f429351b-7fa2-4b4e-85eb-db21e85f834c)<br>
    - pangsit - naspad
      <br>![image](https://github.com/user-attachments/assets/b3c31343-2c28-45d9-a16e-de188eccfabf)<br>
    - tahu-tek - sego-jamur
      <br>![image](https://github.com/user-attachments/assets/4afe2021-ba38-4168-a919-4cad6aad5aee)
  - Client di subnet yang berbeda
    - nasi-uduk - pangsit
      <br>![image](https://github.com/user-attachments/assets/3a205a06-6e22-46f3-834c-2ea0f8066b27)<br>
    - naspad - sego-jamur
      <br>![image](https://github.com/user-attachments/assets/6c076843-332e-46a7-a166-851f2cd42dde)<br>
    - geprek - tahu-tek
      <br>![image](https://github.com/user-attachments/assets/3c7744b2-53f9-4adb-ae67-e8b64d8920d7)
      
- Client - Server
  - Client dan Server di 1 subnet yang sama
    - geprek - kwetiaw
      <br>![image](https://github.com/user-attachments/assets/d72570e5-3fa1-4268-827c-b2687b01da8c)
  - Client dan Server di subnet yang berbeda
    - nasi-uduk - ikan-fillet
      <br>![image](https://github.com/user-attachments/assets/33ff27d8-0d22-4476-8981-3a4f68493a6c)<br>
    - naspad - ikan-fillet
      <br>![image](https://github.com/user-attachments/assets/e36e24f8-3ad2-4aae-97dc-3baabd59e0e9)<br>
    - tahu-tek - ikan-fillet
      <br>![image](https://github.com/user-attachments/assets/3037a765-af58-457f-a5da-7edce908b0d9)<br>
    - sego-jamur - kwetiaw
      <br>![image](https://github.com/user-attachments/assets/ab4a17d7-5430-471f-8a4d-c881a103c608)<br>
    - pangsit - kwetiaw
      <br>![image](https://github.com/user-attachments/assets/90ef29ce-718a-4195-9f5e-e6c154ba7893)

- Client - Router
  - Client dan Router di 1 subnet yang sama
      - geprek - alfamart-eth1
        <br>![image](https://github.com/user-attachments/assets/330d0ad9-97e0-48fe-8ea8-8e8df68fb09a)<br>
      - sego-jamur - its-mart-eth1
        <br>![image](https://github.com/user-attachments/assets/8a623da0-6d49-4dd2-ae93-96718b1b4244)<br>
      - pangsit - sakinah-eth1
        <br>![image](https://github.com/user-attachments/assets/af1dff66-19e2-4458-9577-770632a40b12)
    - Client dan Router di subnet yang berbeda
      - nasi-uduk - family-mart-eth0
        <br>![image](https://github.com/user-attachments/assets/6b0a1b8b-8903-4127-85af-90b26ff1e702)<br>
      - naspad - superindo-eth0
        <br>![image](https://github.com/user-attachments/assets/49994e73-8623-4283-8cd4-6fc54833dcf7)<br>
      - tahu-tek - indomaret-eth0
        <br>![image](https://github.com/user-attachments/assets/9382a71d-35e9-4220-8b1e-67ee15dbb12e)
        
- Server - Server
  - kwetiaw - ikan-fillet
    <br>![image](https://github.com/user-attachments/assets/90ca449e-7b81-48a0-9aa2-a5e79d2d799b)<br>
  - ikan-fillet - kwetiaw
    <br>![image](https://github.com/user-attachments/assets/489b8a46-1f33-452e-9ee3-be094de17d45)

- Server - Router
  - Server dan Router di 1 subnet yang sama
    - ikan-fillet - superindo-eth2
      <br>![image](https://github.com/user-attachments/assets/68e420e9-3b19-4165-9950-d726b806f4fe)<br>
    - ikan-fillet - its-mart-eth0
      <br>![image](https://github.com/user-attachments/assets/25ac3485-7940-481e-bf53-810617804730)<br>
    - kwetiaw - alfamart-eth2
      <br>![image](https://github.com/user-attachments/assets/c55b4541-ebad-4eda-b743-c85a8d02a8cd)
  - Server dan Router di subnet yang berbeda
    - ikan-fillet - family-mart-eth1
      <br>![image](https://github.com/user-attachments/assets/b5e4f928-730d-4827-94a2-d58013e2c7b7)<br>
    - kwetiaw - indomaret-eth1
      <br>![image](https://github.com/user-attachments/assets/83629fae-f41f-4665-ae44-b2de6fd43334)<br>
    - ikan-fillet - alfamart-eth1
      <br>![image](https://github.com/user-attachments/assets/e5b6efa4-ecb1-45e4-af56-49b18a99b3ce)<br>
    - kwetiaw - its-mart-eth0
      <br>![image](https://github.com/user-attachments/assets/5df2a9d6-9c4f-427d-820e-fa42a701339d)<br>
    - ikan-fillet - sakinah-eth1
      <br>![image](https://github.com/user-attachments/assets/f476b70d-23d5-4083-ad50-2719acb8e6d4)<br>
    - kwetiaw - superindo-eth0
      <br>![image](https://github.com/user-attachments/assets/641a69b0-5afe-4fd3-b260-bed7b42079e7)

- Router - Router
  - Router di 1 subnet yang sama
    - family-mart - indomaret-eth0
      <br>![image](https://github.com/user-attachments/assets/040537f2-94e3-4b3c-b3ae-bb6ceef50287)<br>
    - indomaret - alfamart-eth0
      <br>![image](https://github.com/user-attachments/assets/2d07669f-2301-46d6-b049-a9f313ed01fb)<br>
    - superindo - family-mart-eth1
      <br>![image](https://github.com/user-attachments/assets/eb43f3e7-9da4-4e17-8552-108404ec0024)<br>
    - sakinah - superindo-eth1
      <br>![image](https://github.com/user-attachments/assets/c816fa1f-be97-485e-93ac-6a4a3cdab463)<br>
    - its-mart - superindo-eth2
      <br>![image](https://github.com/user-attachments/assets/458b7382-d7c4-4443-af48-a7963c6b1511)
  - Router di subnet yang berbeda
    - family-mart - sakinah-eth1
      <br>![image](https://github.com/user-attachments/assets/1b5b0e19-84d4-4c51-b8f5-94a9a326582d)<br>
    - its-mart - family-mart-eth2
      <br>![image](https://github.com/user-attachments/assets/053102e1-dbae-417e-bd11-d650999fb54e)<br>
    - indomaret - superindo-eth2
      <br>![image](https://github.com/user-attachments/assets/0c1bca37-84f3-4ff6-aeb6-e77393cfb3cf)<br>
    - alfamart - its-mart-eth1
      <br>![image](https://github.com/user-attachments/assets/72fefbd2-73e2-407d-adc6-5e36172b2c0d)<br>
    - sakinah - indomaret-eth0
      <br>![image](https://github.com/user-attachments/assets/f0375d7a-2869-451f-b9f2-cfebe9b72cbd)

<br>
  
## Problems
- Cloning VM yang gagal terus ketika ramai
- ga bisa login pakai kredensial user_account
  <br>![Screenshot 2024-11-17 000617](https://github.com/user-attachments/assets/8f8af6a2-7415-407b-b15d-710bfb312291)
- (Danny) dari hari minggu, tidak bisa mengapply script terraform. baik menggunakan ethernet atau wifi di rumah
  <br>Tambahan: Meskipun pakai myITS-WiFi, sering wifinya menghilang dan connection timed out ketika melakukan terraform apply<br>
- (Danny) 25/11/2024, terraform apply hanya bisa sekali / dua kali saja, sisanya error semua

## Revisions (if any)
