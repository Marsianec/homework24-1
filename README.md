# Домашнее задание к занятию «Организация сети» - Тертерян Вячеслав

---

### Задание 1  

1. Создать пустую VPC. Выбрать зону.  

```
resource "yandex_vpc_network" "my-network" {
  name = var.vpc_name
}
```  

2.  Создать в VPC subnet с названием public, сетью 192.168.10.0/24.  

```
resource "yandex_vpc_subnet" "public" {
  name           = "public"
  zone           = var.default_zone
  network_id     = yandex_vpc_network.my-network.id
  v4_cidr_blocks = ["192.168.10.0/24"]
}
```  

3. Создать в этой подсети NAT-инстанс, присвоив ему адрес 192.168.10.254 и создать в этой публичной подсети виртуалку с публичным IP, подключиться к ней и убедиться, что есть доступ к интернету.  

![alt text](https://github.com/Marsianec/homework24-1/blob/main/img/1.png)   

![alt text](https://github.com/Marsianec/homework24-1/blob/main/img/2.png)   

4. Создать в VPC subnet с названием private, сетью 192.168.20.0/24.  

```
resource "yandex_vpc_subnet" "private" {
  name           = "private"
  zone           = var.default_zone
  network_id     = yandex_vpc_network.my-network.id
  v4_cidr_blocks = ["192.168.20.0/24"]
  route_table_id = yandex_vpc_route_table.nat-instance-route.id
}
```  

5. Создать route table. Добавить статический маршрут, направляющий весь исходящий трафик private сети в NAT-инстанс.  

```
resource "yandex_vpc_route_table" "nat-instance-route" {
  name       = "nat-instance-route"
  network_id = yandex_vpc_network.my-network.id
  static_route {
    destination_prefix = "0.0.0.0/0"
    next_hop_address   = yandex_compute_instance.nat-instance.network_interface.0.ip_address
  }
}
```  

6. Создать в этой приватной подсети виртуалку с внутренним IP, подключиться к ней через виртуалку, созданную ранее, и убедиться, что есть доступ к интернету.  

![alt text](https://github.com/Marsianec/homework24-1/blob/main/img/3.png) 