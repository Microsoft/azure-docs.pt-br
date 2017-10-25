---
title: "Usar o Ansible para criar uma VM do Linux básica no Azure | Microsoft Docs"
description: "Saber como usar o Ansible para criar e gerenciar uma máquina virtual básica do Linux no Azure"
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
ms.openlocfilehash: 07cb786e3053cb933fb87b2991cebb979a91ace0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-basic-virtual-machine-in-azure-with-ansible"></a>Criar uma máquina virtual básica no Azure com o Ansible
O Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente. Você pode usar o Ansible para gerenciar suas máquinas virtuais (VMs) no Azure, da mesma forma que faria com qualquer outro recurso. Este artigo mostra como criar uma VM básica com o Ansible. Você também pode aprender a [Criar um ambiente completo de VM com o Ansible](ansible-create-complete-vm.md).


## <a name="prerequisites"></a>Pré-requisitos
Para gerenciar recursos do Azure com o Ansible, você precisará do seguinte:

- Ansible e os módulos do SDK do Python do Azure instalados no sistema host.
    - Instalar o Ansible no [Ubuntu 16.04 LTS](ansible-install-configure.md#ubuntu-1604-lts), [CentOS 7.3](ansible-install-configure.md#centos-73) e [SLES 12.2 SP2](ansible-install-configure.md#sles-122-sp2)
- Credenciais do Azure e o Ansible configurados para usá-las.
    - [Criar credenciais do Azure e configurar o Ansible](ansible-install-configure.md#create-azure-credentials)
- CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. 
    - Se você precisar atualizar, confira [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). Você também pode usar o [Cloud Shell](/azure/cloud-shell/quickstart) no seu navegador.


## <a name="create-supporting-azure-resources"></a>Criar recursos de suporte do Azure
Neste exemplo, criamos um runbook que implanta uma VM em uma infraestrutura existente. Primeiro, crie um grupo de recursos com [az group create](/cli/azure/vm#create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie a rede virtual para sua VM com [az network vnet create](/cli/azure/network/vnet#create). O exemplo a seguir cria uma rede virtual chamada *myVnet* e uma sub-rede chamada *mySubnet*:

```azurecli
az network vnet create \
  --resource-group myResourceGroup \
  --name myVnet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name mySubnet \
  --subnet-prefix 10.0.1.0/24
```


## <a name="create-and-run-ansible-playbook"></a>Criar e executar o guia estratégico do Ansible
Crie um guia estratégico do Ansible chamado **azure_create_vm.yml** e cole o seguinte conteúdo. Este exemplo cria uma única VM e configura as credenciais de SSH. Insira seus próprios dados de chave pública no par *key_data* da seguinte maneira:

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
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
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.3'
        version: latest
```

Para criar a VM com o Ansible, execute o guia estratégico da seguinte maneira:

```bash
ansible-playbook azure_create_vm.yml
```

A saída é semelhante ao seguinte exemplo que mostra que a máquina virtual foi criada com êxito:

```bash
PLAY [Create Azure VM] ****************************************************

TASK [Gathering Facts] ****************************************************
ok: [localhost]

TASK [Create VM] **********************************************************
changed: [localhost]

PLAY RECAP ****************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```


## <a name="next-steps"></a>Próximas etapas
Este exemplo cria uma máquina virtual em um grupo de recursos existente e com uma rede virtual já implantada. Para obter um exemplo mais detalhado sobre como usar o Ansible para criar recursos de suporte, como uma rede virtual e regras do grupo de segurança de rede, veja [Criar um ambiente completo de VM com o Ansible](ansible-create-complete-vm.md).