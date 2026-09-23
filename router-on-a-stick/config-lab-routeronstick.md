# Tabela

|Dispositivo | interface  | vlan   | ip - mascara    | default gateway|
|-           |-           |-       |-                |-               |
|Switch 1    |fa 0/1      |   .99  | 172.17.99.60/24 |  172.17.99.1   |
|-           |-           |   .20  |-                |                |
|-           |fa 0/6      |   .10  |-                |-               | 
|-           |fa 0/5      |-       |-                |-               |
|-|-|-|-|-|
|Switch 2    |fa 0/1      |   .99  | 172.17.99.12/24 |  172.17.99.1   |
|-           |fa 0/6      |   .20  |                 |                |
|-           |-           |   .10  |                 |                |
|-|-|-|-|-
|Roteador    |-           |        |                 |                |
|-           |-           |        |                 |                |
|-           |-           |        |                 |                |
|-|-|-|-|-
|pc1         | nic        |   N/A  | 192.168.10.10   |  192.168.10.1  |
|pc2         | nic        |   N/A  | 192.168.20.10   |  192.168.20.1  |

## Switch 1

Configuração Switch 1

```
!-Config S1

!- Conf geral
en
conf t
hostname S1
enable secret cisco
line console 0
password cisco
login
exit
service password-encryption

!- Conf gateway
ip default-gateway 172.17.99.1

!- Conf dominios
no ip domain-lookup
ip domain-name ifsuldeminas

!- Gerar chave ssh e ativar ssh
crypto key generate rsa
1024
username cisco password cisco	
ip ssh version 2

!- Conf linhas
line vty 0 15
login local 
transport input ssh
exit

!- Criar vlan 10
vlan 10
name LAN10
end

!- Criar vlan 20
conf t
vlan 20
name LAN20
end

!- Criar e configurar vlan 99

conf t
interface vlan 99
name gerencia
ip address 172.17.99.11 255.255.255.0
no shut
end

!- Atribuir interface para vlan

conf t
interface fa0/1
switchport access vlan 99
switchport mode access
end

!- Atribuir interface conectada ao pc1 para vlan 10

conf t
interface fa0/6
switchport mode access
switchport access vlan 10
no shut
exit

!- Trunk entre switchs

interface fa0/1
switchport mode trunk
no shut
exit

!- Trunk switch e roteador

interface fa0/5
switchport mode trunk
no shut
end

```

## Switch 2

Configuração Switch 2

```
!-Config S1

!- Conf geral
en
conf t
hostname S1
enable secret cisco
line console 0
password cisco
login
exit
service password-encryption

!- Conf gateway
ip default-gateway 172.17.99.1

!- Conf dominios
no ip domain-lookup
ip domain-name ifsuldeminas

!- Gerar chave ssh e ativar ssh
crypto key generate rsa
1024
username cisco password cisco	
ip ssh version 2

!- Conf linhas
line vty 0 15
login local 
transport input ssh
exit

!- Criar vlan 10
vlan 10
name LAN10
end

!- Criar vlan 20
conf t
vlan 20
name LAN20
end

!- Criar e configurar vlan 99

conf t
interface vlan 99
name gerencia
ip address 172.17.99.60 255.255.255.0
no shut
end

!- Atribuir interface para vlan

conf t
interface fa0/1
switchport access vlan 99
switchport mode access
end

!- Atribuir interface conectada ao pc2 para vlan 20

conf t
interface fa0/6
switchport mode access
switchport access vlan 20
no shut
exit


!- Trunk entre switchs

interface fa0/1
switchport mode trunk
no shut
exit

```

## Roteador
Configuração do roteador

```
!- Interface - Vlan 10
en
conf t
interface g0/0.10
encapsulation dot1Q 10
ip add 192.168.10.1 255.255.255.0
exit

!- Interface - Vlan 20
interface g0/0.20
encapsulation dot1Q 20
ip add 192.168.20.1 255.255.255.0
exit

!- Interface - Vlan 99
interface g0/0.99
encapsulation dot1Q 99
ip add 172.17.99.1 255.255.255.0
exit

!- Interface Trunk 

no shut
end

```