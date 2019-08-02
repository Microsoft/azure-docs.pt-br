---
title: Início Rápido – Instalar o Ansible em Máquinas Virtuais do Linux no Azure | Microsoft Docs
description: Neste Início Rápido, saiba como instalar e configurar o Ansible para gerenciar recursos do Azure no Ubuntu, no CentOS e no SLES
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: gwallace
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d3302d999dd70a83be18ce610b9c3d44992c865c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671858"
---
# <a name="quickstart-install-ansible-on-linux-virtual-machines-in-azure"></a>Início Rápido: Instalar o Ansible em Máquinas Virtuais do Linux no Azure

O Ansible permite que você automatize a implantação e a configuração de recursos em seu ambiente. Este artigo mostra como configurar o Ansible para algumas das distribuições do Linux mais comuns. Para instalar o Ansible em outras distribuições, ajuste os pacotes instalados para sua plataforma específica. 

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-sp.md](../../../includes/open-source-devops-prereqs-create-service-principal.md)]
- **Acesso ao Linux ou a uma máquina virtual do Linux** - Se você não tiver acesso a um computador do Linux, crie uma [máquina virtual do Linux](/azure/virtual-network/quick-create-cli).

## <a name="install-ansible-on-an-azure-linux-virtual-machine"></a>Instalar o Ansible em uma máquina virtual do Linux do Azure

Entre no seu computador do Linux e selecione uma das distribuições a seguir para obter instruções sobre como instalar o Ansible:

- [CentOS 7.4](#centos-74)
- Ubuntu 16.04 LTS
- [SLES 12 SP2](#sles-12-sp2)

### <a name="centos-74"></a>CentOS 7.4

Nesta seção, você configurará o CentOS para usar o Ansible.

1. Abra uma janela de terminal.

1. Insira o seguinte comando para instalar os pacotes necessários para os módulos do SDK do Python do Azure:

    ```bash
    sudo yum check-update; sudo yum install -y gcc libffi-devel python-devel openssl-devel epel-release
    sudo yum install -y python-pip python-wheel
    ```

1. Insira o seguinte comando para instalar os pacotes necessários do Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Criar as credenciais do Azure](#create-azure-credentials).

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

Nesta seção, você configurará o Ubuntu para usar o Ansible.

1. Abra uma janela de terminal.

1. Insira o seguinte comando para instalar os pacotes necessários para os módulos do SDK do Python do Azure:

    ```bash
    sudo apt-get update && sudo apt-get install -y libssl-dev libffi-dev python-dev python-pip
    ```

1. Insira o seguinte comando para instalar os pacotes necessários do Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. [Criar as credenciais do Azure](#create-azure-credentials).

### <a name="sles-12-sp2"></a>SLES 12 SP2

Nesta seção, você configurará o SLES para usar o Ansible.

1. Abra uma janela de terminal.

1. Insira o seguinte comando para instalar os pacotes necessários para os módulos do SDK do Python do Azure:

    ```bash
    sudo zypper refresh && sudo zypper --non-interactive install gcc libffi-devel-gcc5 make \
        python-devel libopenssl-devel libtool python-pip python-setuptools
    ```

1. Insira o seguinte comando para instalar os pacotes necessários do Ansible:

    ```bash
    sudo pip install ansible[azure]
    ```

1. Insira o seguinte comando para remover o pacote de criptografia conflitante do Python:

    ```bash
    sudo pip uninstall -y cryptography
    ```

1. [Criar as credenciais do Azure](#create-azure-credentials).

## <a name="create-azure-credentials"></a>Criar as credenciais do Azure

Para configurar as credenciais do Ansible, você precisará ter as seguintes informações:

* ID da assinatura do Azure 
* Os valores da entidade de serviço

Se você estiver usando o Ansible Tower ou o Jenkins, declare os valores da entidade de serviço como variáveis de ambiente.

Configure as credenciais do Ansible usando uma das seguintes técnicas:

- [Criar um arquivo de credenciais do Ansible](#file-credentials)
- [Usar variáveis de ambiente Ansible](#env-credentials)

### <a name="span-idfile-credentials-create-ansible-credentials-file"></a><span id="file-credentials"/> Criar arquivo de credenciais do Ansible

Nesta seção, você criará um arquivo de credenciais local para fornecer credenciais para o Ansible. 

Para obter mais informações sobre como definir as credenciais do Ansible, confira [Como fornecer credenciais para os módulos do Azure](https://docs.ansible.com/ansible/guide_azure.html#providing-credentials-to-azure-modules).

1. Para um ambiente de desenvolvimento, crie um arquivo chamado `credentials` na máquina virtual do host:

    ```bash
    mkdir ~/.azure
    vi ~/.azure/credentials
    ```

1. Insira as linhas a seguir no arquivo. Substitua os espaços reservados pelos valores da entidade de serviço.

    ```bash
    [default]
    subscription_id=<your-subscription_id>
    client_id=<security-principal-appid>
    secret=<security-principal-password>
    tenant=<security-principal-tenant>
    ```

1. Salve e feche o arquivo.

### <a name="span-idenv-credentialsuse-ansible-environment-variables"></a><span id="env-credentials"/>Usar variáveis de ambiente Ansible

Nesta seção, você exportará os valores da entidade de serviço para configurar suas credenciais do Ansible.

1. Abra uma janela de terminal.

1. Exporte os valores da entidade de serviço:

    ```bash
    export AZURE_SUBSCRIPTION_ID=<your-subscription_id>
    export AZURE_CLIENT_ID=<security-principal-appid>
    export AZURE_SECRET=<security-principal-password>
    export AZURE_TENANT=<security-principal-tenant>
    ```

## <a name="verify-the-configuration"></a>Verificar a configuração

Para verificar a configuração bem-sucedida, use o Ansible para criar um grupo de recursos do Azure.

[!INCLUDE [create-resource-group-with-ansible.md](../../../includes/ansible-snippet-create-resource-group.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Início Rápido: Configurar uma máquina virtual do Linux no Azure usando o Ansible](./ansible-create-vm.md)