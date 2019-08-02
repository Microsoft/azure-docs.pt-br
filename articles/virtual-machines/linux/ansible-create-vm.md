---
title: Início Rápido – Configurar Máquinas Virtuais do Linux no Azure usando o Ansible | Microsoft Docs
description: Neste Início Rápido, saiba como criar uma máquina virtual do Linux no Azure usando o Ansible
keywords: ansible, azure, devops, máquina virtual
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 32d4486138f21bd99c3d75ee72ae5dd0df667e41
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67668639"
---
# <a name="quickstart-configure-linux-virtual-machines-in-azure-using-ansible"></a>Início Rápido: Configurar máquinas virtuais do Linux no Azure usando o Ansible

Usando uma linguagem declarativa, o Ansible permite automatizar a criação, configuração e implantação dos recursos do Azure por meio dos *guias estratégicos* do Ansible. Este artigo apresenta um guia estratégico de exemplo do Ansible para configurar máquinas virtuais do Linux. O [guia estratégico completo do Ansible](#complete-sample-ansible-playbook) está listado no final deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

O Ansible precisa de um grupo de recursos para implantar todos os seus recursos. A seção do guia estratégico de exemplo do Ansible a seguir cria um grupo de recursos denominado `myResourceGroup` no local `eastus`:

```yaml
- name: Create resource group
  azure_rm_resourcegroup:
    name: myResourceGroup
    location: eastus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Ao criar uma máquina virtual do Azure, você deve criar uma [rede virtual](/azure/virtual-network/virtual-networks-overview) ou usar uma rede virtual existente. Você também precisa decidir como suas máquinas virtuais devem ser acessadas na rede virtual. A seção a seguir do guia estratégico de exemplo do Ansible cria uma rede virtual chamada `myVnet` no espaço de endereço `10.0.0.0/16`:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

Todos os recursos do Azure implantados em uma rede virtual são implantados em uma [sub-rede](/azure/virtual-network/virtual-network-manage-subnet) dentro de uma rede virtual. 

A seção a seguir do guia estratégico de exemplo do Ansible cria uma sub-rede chamada `mySubnet` na rede virtual `myVnet`:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>Criar um endereço IP público





[Endereços IP públicos](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) permitem recursos de Internet para comunicar a entrada para recursos do Azure. Endereços IP públicos também habilitam recursos do Azure para comunicar a saída a serviços do Azure voltados ao público. Nos dois cenários, um endereço IP atribuído ao recurso que está sendo acessado. O endereço é dedicado ao recurso até que você cancele a atribuição. Se um endereço IP público não está atribuído a um recurso, o recurso ainda pode se comunicar externamente com a Internet. A conexão é feita pelo Azure, atribuindo dinamicamente um endereço IP disponível. O endereço atribuído dinamicamente não é dedicado ao recurso.

A seção a seguir do guia estratégico de exemplo do Ansible cria um endereço IP público chamado `myPublicIP`:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Os [Grupos de Segurança de Rede](/azure/virtual-network/security-overview) filtram o tráfego de rede entre os recursos do Azure em uma rede virtual. São definidas regras de segurança que controlam o tráfego de entrada e saída de e para os recursos do Azure. Para obter mais informações sobre recursos do Azure e grupos de segurança de rede, confira [Integração de rede virtual para serviços do Azure](/azure/virtual-network/virtual-network-for-azure-services)

O guia estratégico a seguir cria um grupo de segurança de rede chamado `myNetworkSecurityGroup`. O grupo de segurança de rede inclui uma regra que permite o tráfego na porta TCP 22.

```yaml
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
```

## <a name="create-a-virtual-network-interface-card"></a>Criar uma placa de adaptador de rede virtual

Uma placa de adaptador de rede virtual conecta-se à máquina virtual para uma determinada rede virtual, um endereço IP público e um grupo de segurança de rede. 

A seção a seguir do guia estratégico de exemplo do Ansible cria uma placa de adaptador da rede virtual denominada `myNIC` conectada aos recursos da rede virtual que você criou:

```yaml
- name: Create virtual network interface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

A etapa final é criar uma máquina virtual que usa todos os recursos que você criou nas seções anteriores deste artigo. 

A seção de guia estratégico de exemplo do Ansible apresentada nesta seção cria uma máquina virtual denominada `myVM` e anexa a placa de adaptador de rede virtual denominada `myNIC`. Substitua o espaço reservado &lt;your-key-data> pelos seus próprios dados de chave pública completos.

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
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>Guia estratégico de exemplo do Ansible completo

Esta seção lista todo o guia estratégico de exemplo do Ansible que você criou ao longo deste artigo. 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
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
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
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
  - name: Create virtual network interface card
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
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>Executar o guia estratégico de exemplo do Ansible

Esta seção orienta você na execução do guia estratégico de exemplo do Ansible apresentado neste artigo.

1. Entre no [Portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Abra o [Cloud Shell](/azure/cloud-shell/overview).

1. Crie um arquivo (para conter seu guia estratégico) chamado `azure_create_complete_vm.yml`, e abra-o no editor VI da seguinte maneira:

   ```azurecli-interactive
   vi azure_create_complete_vm.yml
   ```

1. Entre no modo de inserção selecionando a tecla **I**.

1. Cole [todo o guia estratégico de exemplo do Ansible](#complete-sample-ansible-playbook) no editor.

1. Saia do modo de inserção selecionando a tecla **Esc**.

1. Salve o arquivo e saia do editor vi, inserindo o comando a seguir:

    ```bash
    :wq
    ```

1. Execute o guia estratégico de exemplo do Ansible.

   ```bash
   ansible-playbook azure_create_complete_vm.yml
   ```

1. A saída é semelhante ao seguinte exemplo, onde você pode ver que uma máquina virtual foi criada com êxito:

   ```bash
   PLAY [Create Azure VM] ****************************************************

   TASK [Gathering Facts] ****************************************************
   ok: [localhost]

   TASK [Create resource group] *********************************************
   changed: [localhost]

   TASK [Create virtual network] *********************************************
   changed: [localhost]

   TASK [Add subnet] *********************************************************
   changed: [localhost]

   TASK [Create public IP address] *******************************************
   changed: [localhost]

   TASK [Dump public IP for VM which will be created] ********************************************************************
   ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
   }

   TASK [Create Network Security Group that allows SSH] **********************
   changed: [localhost]

   TASK [Create virtual network interface card] *******************************
   changed: [localhost]

   TASK [Create VM] **********************************************************
   changed: [localhost]

   PLAY RECAP ****************************************************************
   localhost                  : ok=8    changed=7    unreachable=0    failed=0
   ```

1. O comando SSH é usado para acessar sua VM do Linux. Substitua o espaço reservado &lt;ip-address> pelo endereço IP da etapa anterior.

    ```bash
    ssh azureuser@<ip-address>
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Início Rápido: Configurar uma máquina virtual do Linux no Azure usando o Ansible](./ansible-manage-linux-vm.md)
