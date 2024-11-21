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

  ![image](https://github.com/user-attachments/assets/48c1b03b-53d9-40b0-bac8-c9b22b9ef135)

- Iteration 2

  ![image](https://github.com/user-attachments/assets/85ce9cf5-ca13-49cd-a050-184d2ae87b0f)

  ![image](https://github.com/user-attachments/assets/048917cd-31fc-4603-b770-ff63db2df1e5)

- Iteration 3

  ![image](https://github.com/user-attachments/assets/9c18b140-ad0c-4071-b095-10383e4d8e8b)

  ![image](https://github.com/user-attachments/assets/22bccf8d-545e-42d9-b89e-61ccb1d246fd)

- Iteration 4
  
  ![image](https://github.com/user-attachments/assets/15802fa1-38a0-41a0-9cd9-87b6721ff79d)

  ![image](https://github.com/user-attachments/assets/17907e6d-cf33-4297-a2df-08d3a0c83f99)

- Iteration 5

  ![image](https://github.com/user-attachments/assets/1afbd7fc-2748-4915-a828-8a5a9abc4489)

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
    data = <<-EOF
    #cloud-config
    runcmd:
        - sysctl -w net.ipv4.ip_forward=1
        - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
        - sysctl -p
        - netplan apply
        - ip link set eth0 up
        - ip link set eth1 up
        - ip link set eth2 up
        - ip route add 192.168.26.34/30 via 192.168.26.33 dev eth1
        - ip route add 192.168.26.98/30 via 192.168.26.90 dev eth2


    EOF

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
    runcmd:
        - sysctl -w net.ipv4.ip_forward=1
        - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
        - sysctl -p
        - netplan apply
        - ip link set eth0 up
        - ip link set eth1 up
        - ip route add 0.0.0.0/0 via 192.168.26.33 dev eth0
        - ip route add 192.168.26.18/30 via 192.168.26.17 dev eth1
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
    runcmd:
        - sysctl -w net.ipv4.ip_forward=1
        - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
        - sysctl -p
        - netplan apply
        - ip link set eth0 up
        - ip link set eth1 up
        - ip link set eth2 up
        - ip route add 0.0.0.0/0 via 192.168.26.17 dev eth0
        - ip route add 192.168.26.2/29 via 192.168.26.1 dev eth1
        - ip route add 192.168.26.10/30 via 192.168.26.9 dev eth2
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
    runcmd:
        - sysctl -w net.ipv4.ip_forward=1
        - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
        - sysctl -p
        - netplan apply
        - ip link set eth0 up
        - ip link set eth1 up
        - ip link set eth2 up
        - ip route add 0.0.0.0/0 via 192.168.26.33 dev eth0
        - ip route add 192.168.26.90/30 via 192.168.26.89 dev eth1
        - ip route add 192.168.26.66/29 via 192.168.26.65 dev eth2
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
    runcmd:
        - sysctl -w net.ipv4.ip_forward=1
        - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
        - sysctl -p
        - netplan apply
        - ip link set eth0 up
        - ip link set eth1 up
        - ip route add 0.0.0.0/0 via 192.168.26.89 dev eth0
        - ip route add 192.168.26.82/29 via 192.168.26.81 dev eth1
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
    runcmd:
        - sysctl -w net.ipv4.ip_forward=1
        - echo 'net.ipv4.ip_forward=1' >> /etc/sysctl.conf
        - sysctl -p
        - netplan apply
        - ip link set eth0 up
        - ip link set eth1 up
        - ip route add 0.0.0.0/0 via 192.168.26.65 dev eth0
        - ip route add 192.168.26.74/29 via 192.168.26.73 dev eth1
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
        vm_id = "5555"
    }

    agent {
        enabled = true
    }

    initialization {
      ip_config { # eth0
        ipv4 {
          address = lookup(var.ip_list, "family-mart-eth0")
        }
      }

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

      user_account {
        username = "d26"
        password = "d26"
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
        vm_id = "5555"
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

      user_account {
        username = "d26"
        password = "d26"
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
        vm_id = "5555"
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

      user_account {
        username = "d26"
        password = "d26"
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
        vm_id = "5555"
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
        vm_id = "5555"
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
        vm_id = "5555"
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
        vm_id = "5555"
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

      user_account {
        username = "d26"
        password = "d26"
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
        vm_id = "5555"
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

      user_account {
        username = "d26"
        password = "d26"
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
        vm_id = "5555"
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
        vm_id = "5555"
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
        vm_id = "5555"
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
        vm_id = "5555"
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

      user_account {
        username = "d26"
        password = "d26"
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
        vm_id = "5555"
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
        vm_id = "5555"
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


### variables.tf Configuration
```
variable "proxmox_url" {
  type    = string
  default = "https://10.21.71.4:8006" # http://10.21.71.4:8006
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
    "family-mart-eth0" = "dhcp"
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

### Testing

- Client - client

  `Put your testing screenshot in here`

- Client - Server

  `Put your testing screenshot in here`

- Client - Router

  `Put your testing screenshot in here`

- Server - Server

  `Put your testing screenshot in here`

- Server - Router

  `Put your testing screenshot in here`

- Router - Router

  `Put your testing screenshot in here`

<br>
  
## Problems
- Cloning VM yang gagal terus ketika ramai
- ga bisa login pakai kredensial user_account
- (Danny) dari hari minggu, tidak bisa mengapply script terraform. baik menggunakan ethernet atau wifi di rumah <br>
  Tambahan: Meskipun pakai myITS-WiFi, sering wifinya menghilang dan connection timed out ketika melakukan terraform apply
<br>

## Revisions (if any)
