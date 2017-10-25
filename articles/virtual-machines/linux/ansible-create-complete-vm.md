---
title: Usar o Ansible para criar uma VM do Linux completa no Azure | Microsoft Docs
description: "Saber como usar o Ansible para criar e gerenciar um ambiente completo de máquina virtual do Linux no Azure"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: iainfou
ms.openlocfilehash: f5dfecd1f5aafc1b7117433ef1bdb1805be2c57b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-complete-linux-virtual-machine-environment-in-azure-with-ansible"></a>Criar um ambiente completo de máquina virtual do Linux no Azure com o Ansible
O Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente. Você pode usar o Ansible para gerenciar suas máquinas virtuais (VMs) no Azure, da mesma forma que faria com qualquer outro recurso. Este artigo mostra como criar um ambiente Linux completo e os recursos de apoio com o Ansible. Você também pode aprender a [Criar uma VM básica com o Ansible](ansible-create-vm.md).


## <a name="prerequisites"></a>Pré-requisitos
Para gerenciar recursos do Azure com o Ansible, você precisará do seguinte:

- Ansible e os módulos do SDK do Python do Azure instalados no sistema host.
    - Instalar o Ansible no [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), [CentOS 7.3](ansible-install-configure.md#centos-73) e [SLES 12.2 SP2](ansible-install-configure.md#sles-122-sp2)
- Credenciais do Azure e o Ansible configurados para usá-las.
    - [Criar credenciais do Azure e configurar o Ansible](ansible-install-configure.md#create-azure-credentials)
- CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. 
    - Se você precisar atualizar, confira [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). Você também pode usar o [Cloud Shell](/azure/cloud-shell/quickstart) no seu navegador.


## <a name="create-virtual-network"></a>Criar rede virtual
A seção a seguir em um guia estratégico do Ansible cria uma rede virtual chamada *myVnet* no espaço de endereço *10.0.0.0/16*:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.10.0.0/16"
```

Para adicionar uma sub-rede, a seção a seguir cria uma sub-rede denominada *mySubnet* na rede virtual *myVnet*:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```


## <a name="create-public-ip-address"></a>Criar um endereço IP público
Para acessar os recursos na Internet, crie e atribua um endereço IP público para a sua VM. A seção a seguir em um guia estratégico do Ansible cria um endereço IP público chamado *myPublicIP*:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```


## <a name="create-network-security-group"></a>Criar um grupo de segurança de rede
Os grupos de segurança de rede controlam o fluxo de entrada e saída de tráfego de rede de sua VM. A seção a seguir em um guia estratégico do Ansible cria um grupo de segurança de rede denominado *myNetworkSecurityGroup* e define uma regra para permitir o tráfego de SSH na porta TCP 22:

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: TCP
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```


## <a name="create-virtual-network-interface-card"></a>Criar placa de adaptador de rede virtual
Uma placa de adaptador de rede virtual (NIC) conecta-se à VM para uma determinada rede virtual, um endereço IP público e um grupo de segurança de rede. A seção a seguir em um guia estratégico do Ansible cria uma NIC virtual denominada *myNIC* conectada aos recursos da rede virtual que você criou:

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```


## <a name="create-virtual-machine"></a>Criar máquina virtual
A etapa final é criar uma máquina virtual e usar todos os recursos criados. A seção a seguir em um guia estratégico do Ansible cria uma VM denominada *myVM* e anexa a NIC virtual denominada *myNIC*. Insira seus próprios dados de chave pública no par *key_data* da seguinte maneira:

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys: 
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.3'
      version: latest
```

## <a name="complete-ansible-playbook"></a>Guia estratégico completo do Ansible
Para reunir todas essas seções, crie um guia estratégico do Ansible chamado *azure_create_complete_vm.yml* e cole o seguinte conteúdo:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys: 
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: "ssh-rsa AAAAB3Nz{snip}hwhqT9h"
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

O Ansible precisa de um grupo de recursos para implantar todos os seus recursos. Crie um grupo de recursos com [az group create](/cli/azure/vm#create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Para criar o ambiente completo de VM com o Ansible, execute o guia estratégico da seguinte maneira:

```bash
ansible-playbook azure_create_complete_vm.yml
```

A saída é semelhante ao seguinte exemplo que mostra que a máquina virtual foi criada com êxito:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create virtual network] *********************************************
changed: [localhost]

TASK [Add subnet] *********************************************************
changed: [localhost]

TASK [Create public IP address] *******************************************
changed: [localhost]

TASK [Create Network Security Group that allows SSH] **********************
changed: [localhost]

TASK [Create virtual network inteface card] *******************************
changed: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=7    changed=6    unreachable=0    failed=0
```

## <a name="next-steps"></a>Próximas etapas
Este exemplo cria um ambiente completo de VM, incluindo os recursos necessários de rede virtual. Para obter um exemplo mais direto para criar uma máquina virtual em recursos de rede existentes com as opções padrão, veja [Criar uma máquina virtual](ansible-create-vm.md).