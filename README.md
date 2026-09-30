# Домашнее задание к занятию "`Отказоустойчивость в облаке`" - `Павлова А.В`




---

### Задание 1

1. ###`Terraform Playbook.`


========== provider.tf ==========
terraform {
  required_providers {
    yandex = {
      source = "yandex-cloud/yandex"
    }
  }
}

provider "yandex" {
  service_account_key_file = "key.json"
  cloud_id                 = "b1go2f99t4stvp2pl4u1"
  folder_id                = "b1goaqpvj6fkemm5q7kf"
  zone                     = "ru-central1-a"
}

========== network.tf ==========
resource "yandex_vpc_network" "net" {
  name = "hw-net"
}

resource "yandex_vpc_subnet" "subnet1" {
  name           = "hw-subnet"
  zone           = "ru-central1-a"
  network_id     = yandex_vpc_network.net.id
  v4_cidr_blocks = ["192.168.10.0/24"]
}

resource "yandex_vpc_security_group" "sg" {
  name       = "hw-sg"
  network_id = yandex_vpc_network.net.id

  ingress {
    protocol       = "TCP"
    description    = "SSH"
    port           = 22
    v4_cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    protocol       = "TCP"
    description    = "HTTP"
    port           = 80
    v4_cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    protocol       = "ANY"
    description    = "All outbound"
    from_port      = 0
    to_port        = 65535
    v4_cidr_blocks = ["0.0.0.0/0"]
  }
}

========== vm.tf ==========
resource "yandex_compute_instance" "vm" {
  count = 2

  name        = "vm${count.index}"
  platform_id = "standard-v1"

  boot_disk {
    initialize_params {
      image_id = "fd83ergat2e815oohe7o" # Ubuntu 24.04
      size     = 10
    }
  }

  network_interface {
    subnet_id = yandex_vpc_subnet.subnet1.id
    nat       = true
  }

  resources {
    cores  = 2
    memory = 2
    core_fraction = 20
  }

  metadata = {
    ssh-keys  = "ubuntu:${file("~/.ssh/id_ed25519.pub")}"
    user-data = <<-EOF
      #cloud-config
      package_update: true
      packages:
        - nginx
      runcmd:
        - systemctl enable nginx
        - systemctl start nginx
        - echo "Hello from VM ${count.index}" > /var/www/html/index.html
    EOF
  }
}

========== balancer.tf ==========
# -- Целевая группа --
resource "yandex_lb_target_group" "tg" {
  name = "hw-target-group"

  target {
    subnet_id = yandex_vpc_subnet.subnet1.id
    address   = yandex_compute_instance.vm[0].network_interface.0.ip_address
  }

  target {
    subnet_id = yandex_vpc_subnet.subnet1.id
    address   = yandex_compute_instance.vm[1].network_interface.0.ip_address
  }
}

# --- Сетевой балансировщик ---
resource "yandex_lb_network_load_balancer" "lb" {
  name = "hw-balancer"

  listener {
    name = "http-listener"
    port = 80
    external_address_spec {
      ip_version = "ipv4"
    }
  }

  attached_target_group {
    target_group_id = yandex_lb_target_group.tg.id

    healthcheck {
      name = "http-healthcheck"
      http_options {
        port = 80
        path = "/"
      }
    }
  }
}




###Скриншоты


![Network.tf](https://github.com/Alena97dv/homework/blob/main/network.tf.png)
![provider.tf](https://github.com/Alena97dv/homework/blob/main/provider.tf.png)
![vm.tf](https://github.com/Alena97dv/homework/blob/main/vm.tf.png)
![balancer.tf](https://github.com/Alena97dv/homework/blob/main/balancer.tf.png)



2. 
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. `Заполните здесь этапы выполнения, если требуется ....`
7. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота 1](ссылка на скриншот 1)`


---

### Задание 2

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота 2](ссылка на скриншот 2)`


---

### Задание 3

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`

### Задание 4

`Приведите ответ в свободной форме........`

1. `Заполните здесь этапы выполнения, если требуется ....`
2. `Заполните здесь этапы выполнения, если требуется ....`
3. `Заполните здесь этапы выполнения, если требуется ....`
4. `Заполните здесь этапы выполнения, если требуется ....`
5. `Заполните здесь этапы выполнения, если требуется ....`
6. 

```
Поле для вставки кода...
....
....
....
....
```

`При необходимости прикрепитe сюда скриншоты
![Название скриншота](ссылка на скриншот)`
