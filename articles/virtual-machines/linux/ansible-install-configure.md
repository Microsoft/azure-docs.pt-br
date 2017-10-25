---
title: "Instalar e configurar Ansible para uso com máquinas virtuais do Azure | Microsoft Docs"
description: Saber como instalar e configurar o Ansible para gerenciar recursos do Azure no Ubuntu, no CentOS e no SLES
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
ms.openlocfilehash: 3fabfee169c3ebedc7bca81a6a6d9a44171daf20
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-ansible-to-manage-virtual-machines-in-azure"></a>Instalar e configurar o Ansible para gerenciar máquinas virtuais no Azure
Este artigo fornece detalhes sobre como instalar o Ansible e os módulos necessários do SDK do Python no Azure para algumas das distribuições de Linux mais comuns. Você pode instalar o Ansible em outras distribuições ajustando os pacotes instalados para se adaptar a sua plataforma específica. Para criar recursos do Azure de maneira segura, você também aprenderá a criar e definir credenciais para o Ansible usar. 

Para obter mais opções de instalação e etapas para outras plataformas, veja o [Guia de instalação do Ansible](https://docs.ansible.com/ansible/intro_installation.html).


## <a name="install-ansible"></a>Instalar o Ansible
Primeiro, crie um grupo de recursos com [az group create](/cli/azure/group#create). O seguinte exemplo cria um grupo de recursos chamado *myResourceGroupAnsible* na localização *eastus*:

```azurecli
az group create --name myResourceGroupAnsible --location eastus
```

Agora crie uma máquina virtual e instale o Ansible para uma das seguintes distribuições:

- [Ubuntu 16.04 LTS](#ubuntu1604-lts)
- [CentOS 7.3](#centos-73)
- [SLES 12.2 SP2](#sles-122-sp2)

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS
Crie uma VM com [az vm create](/cli/azure/vm#create). O exemplo a seguir cria uma VM chamada *myVMAnsible*:

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para a sua VM usando o `publicIpAddress` observado na saída da operação de criação da VM:

```bash
ssh azureuser@<publicIpAddress>
```

Na sua VM, instale os pacotes necessários para os módulos de SDK de Python no Azure e no Ansible da seguinte maneira:

```bash
## Install pre-requisite packages
sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip

## Install Ansible and Azure SDKs via pip
pip install ansible[azure]
```

Agora vá para [Criar credenciais do Azure](#create-azure-credentials).


### <a name="centos-73"></a>CentOS 7.3
Crie uma VM com [az vm create](/cli/azure/vm#create). O exemplo a seguir cria uma VM chamada *myVMAnsible*:

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para a sua VM usando o `publicIpAddress` observado na saída da operação de criação da VM:

```bash
ssh azureuser@<publicIpAddress>
```

Na sua VM, instale os pacotes necessários para os módulos de SDK de Python no Azure e no Ansible da seguinte maneira:

```bash
## Install pre-requisite packages
sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
sudo yum install -y python-pip python-wheel

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Agora vá para [Criar credenciais do Azure](#create-azure-credentials).


### <a name="sles-122-sp2"></a>SLES 12.2 SP2
Crie uma VM com [az vm create](/cli/azure/vm#create). O exemplo a seguir cria uma VM chamada *myVMAnsible*:

```bash
az vm create \
    --name myVMAnsible \
    --resource-group myResourceGroupAnsible \
    --image SLES \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH para a sua VM usando o `publicIpAddress` observado na saída da operação de criação da VM:

```bash
ssh azureuser@<publicIpAddress>
```

Na sua VM, instale os pacotes necessários para os módulos de SDK de Python no Azure e no Ansible da seguinte maneira:

```bash
## Install pre-requisite packages
sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 python-devel \
    libopenssl-devel libtool python-pip python-setuptools

## Install Ansible and Azure SDKs via pip
sudo pip install ansible[azure]
```

Agora vá para [Criar credenciais do Azure](#create-azure-credentials).


## <a name="create-azure-credentials"></a>Criar as credenciais do Azure
O Ansible comunica-se com o Azure usando um nome de usuário e uma senha ou uma entidade de serviço. Uma entidade de serviço do Azure é uma identidade de segurança que você pode usar com aplicativos, serviços e ferramentas de automação como o Ansible. Você controla e define as permissões referentes a quais operações a entidade de serviço pode executar no Azure. Para melhorar a segurança em apenas fornecer um nome de usuário e uma senha, este exemplo cria uma entidade de serviço básica.

Crie uma entidade de serviço com [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) e gere as credenciais necessárias para o Ansible:

```azurecli
az ad sp create-for-rbac --query [appId,password,tenant]
```

Um exemplo da saída dos comandos anteriores é o seguinte:

```json
[
  "eec5624a-90f8-4386-8a87-02730b5410d5",
  "531dcffa-3aff-4488-99bb-4816c395ea3f",
  "72f988bf-86f1-41af-91ab-2d7cd011db47"
]
```

Para se autenticar no Azure, você também precisa obter sua ID de assinatura do Azure com [az account show](/cli/azure/account#show):

```azurecli
az account show --query [id] --output tsv
```

Você pode usar a saída desses dois comandos na próxima etapa.


## <a name="create-ansible-credentials-file"></a>Criar arquivo de credenciais do Ansible
Para fornecer credenciais para o Ansible, você poderá definir variáveis de ambiente ou criar um arquivo de credenciais locais. Para saber mais sobre como definir credenciais do Ansible, veja [Fornecendo credenciais para os módulos do Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules). 

Para um ambiente de desenvolvimento, crie um arquivo de *credenciais* para o Ansible em sua VM host da seguinte maneira:

```bash
mkdir ~/.azure
vi ~/.azure/credentials
```

O próprio arquivo de *credenciais* combina a ID da assinatura com a saída da criação de uma entidade de serviço. A saída do comando anterior [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) é a mesma ordem conforme o necessário para *client_id*, *secret* e *tenant*. O arquivo de *credenciais* de exemplo a seguir mostra esses valores correspondentes à saída anterior. Insira seus próprios valores da seguinte maneira:

```bash
[default]
subscription_id=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
client_id=eec5624a-90f8-4386-8a87-02730b5410d5
secret=531dcffa-3aff-4488-99bb-4816c395ea3f
tenant=72f988bf-86f1-41af-91ab-2d7cd011db47
```


## <a name="use-ansible-environment-variables"></a>Usar variáveis de ambiente Ansible
Se você pretende usar ferramentas como o Ansible Tower ou Jenkins, poderá definir variáveis de ambiente da seguinte maneira. Essas variáveis combinam a ID da assinatura com a saída da criação de uma entidade de serviço. A saída do comando anterior [az ad sp create-for-rbac](/cli/azure/ad/sp#create-for-rbac) é a mesma ordem conforme o necessário para *AZURE_CLIENT_ID*, *AZURE_SECRET* e *AZURE_TENANT*. 

```bash
export AZURE_SUBSCRIPTION_ID=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
export AZURE_CLIENT_ID=eec5624a-90f8-4386-8a87-02730b5410d5
export AZURE_SECRET=531dcffa-3aff-4488-99bb-4816c395ea3f
export AZURE_TENANT=72f988bf-86f1-41af-91ab-2d7cd011db47
```

## <a name="next-steps"></a>Próximas etapas
Agora você tem o Ansible e os módulos necessários do SDK do Python do Azure instalados e as credenciais definidas para o Ansible usar. Saiba como [criar uma VM com o Ansible](ansible-create-vm.md). Você também pode aprender a [criar uma VM do Azure completa e os recursos de apoio com o Ansible](ansible-create-complete-vm.md).