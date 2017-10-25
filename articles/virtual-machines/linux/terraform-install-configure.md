---
title: Instalar e configurar o Terraform para provisionar VMs e outra infraestrutura no Azure | Microsoft Docs
description: Saiba como instalar e configurar o Terraform para criar recursos do Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/14/2017
ms.author: echuvyrov
ms.openlocfilehash: da567097be38ac649c6bf1de1508de24d21cb877
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Instalar e configurar o Terraform para provisionar VMs e outra infraestrutura no Azure 
Este artigo descreve as etapas necessárias para instalar e configurar o Terraform para provisionar recursos como máquinas virtuais no Azure. Você aprenderá a criar e usar as credenciais do Azure para permitir que o Terraform provisione recursos da nuvem de forma segura.

O HashiCorp Terraform fornece uma maneira fácil de definir e implantar a infraestrutura de nuvem usando uma linguagem de modelagem personalizada chamada HCL (linguagem de configuração HashiCorp). Essa linguagem personalizada é [fácil de escrever e de entender](terraform-create-complete-vm.md). Além disso, usando o comando `terraform plan`, você pode visualizar as alterações à sua infraestrutura antes de confirmá-las. Siga estas etapas para começar a usar o Terraform com o Azure.

## <a name="install-terraform"></a>Instalar o Terraform
Para instalar o Terraform, [baixe](https://www.terraform.io/downloads.html) o pacote apropriado para seu sistema operacional para um diretório de instalação separado. O download contém um único arquivo executável, para o qual também deve definir um caminho global. Para obter instruções sobre como definir o caminho no Linux e no Mac, acesse [esta página da Web](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Para obter instruções sobre como definir o caminho no Windows, acesse [esta página da Web](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). Para verificar sua instalação, execute o comando `terraform`. Você deve ver uma lista das opções do Terraform disponíveis como saída.

Em seguida, você precisa permitir o acesso do Terraform à sua assinatura do Azure para executar o provisionamento de infraestrutura.

## <a name="set-up-terraform-access-to-azure"></a>Configurar o acesso do Terraform ao Azure
Para habilitar o Terraform a provisionar recursos para o Azure, você precisa criar duas entidades no Azure AD (Azure Active Directory): aplicativo Azure AD e entidade de serviço do Azure AD. Em seguida, use os identificadores dessas entidades nos scripts do Terraform. A entidade de serviço é uma instância local de um aplicativo Azure AD global. Uma entidade de serviço permite um controle de acesso local granular aos recursos globais.

Há várias maneiras de criar um aplicativo Azure AD e uma entidade de serviço do Azure AD. A maneira mais fácil e rápida hoje é usar a CLI 2.0 do Azure, que [pode ser baixada e instalada no Windows/Linux/Mac](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli). Você também pode usar o PowerShell ou a CLI 1.0 do Azure para criar a infraestrutura de segurança necessária. As instruções a seguir mostram como configurar o Terraform para Azure usando todas essas abordagens.

### <a name="use-azure-cli-20-for-windows-linux-or-mac-users"></a>Usar a CLI 2.0 do Azure (para usuários do Windows, Linux ou Mac) 
Depois de baixar e instalar a [CLI 2.0 do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli), entre como administrar na sua assinatura do Azure emitindo o seguinte comando:

```
az login
```

>[!NOTE]
>Se você usar as nuvens da China, do Azure Alemanha ou do Azure Governamental, precisará primeiro configurar a CLI do Azure para funcionar com essa nuvem. Você pode fazer isso executando o seguinte:

```
az cloud set --name AzureChinaCloud|AzureGermanCloud|AzureUSGovernment
```

Se você tiver várias assinaturas do Azure, seus detalhes serão retornados pelo comando `az login`. Defina a variável de ambiente `SUBSCRIPTION_ID` para manter o valor do campo `id` retornado da assinatura que você deseja usar. 

Defina a assinatura que você deseja usar para essa sessão.

```
az account set --subscription="${SUBSCRIPTION_ID}"
```

Consulte a conta para obter os valores de ID da assinatura e ID do locatário.

```
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Em seguida, crie credenciais separadas para o Terraform.

```
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

A appId, a senha, o sp_name e o locatário são retornados. Anote a appId e a senha.

Para confirmar suas credenciais (entidade de serviço), abra um novo shell e execute os comandos a seguir. Substitua os valores retornados para sp_name, senha e locatário:

```
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

### <a name="use-powershell-for-windows-users"></a>Use o PowerShell (para usuários do Windows) 
Para usar um computador Windows para escrever e executar seus scripts do Terraform e usar o PowerShell para tarefas de configuração, configure o computador com as ferramentas certas do PowerShell. 

1. Instale as ferramentas do PowerShell seguindo as etapas em [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps). 

2. Baixe e execute o [script azure-setup.ps1](https://github.com/echuvyrov/terraform101/blob/master/azureSetup.ps1) no console do PowerShell.

3. Para executar o script azure-setup.ps1, baixe-o e execute o comando `./azure-setup.ps1 setup` no console do PowerShell. Em seguida, entre na sua assinatura do Azure com privilégios administrativos.

4. Forneça um nome de aplicativo (cadeia de caracteres arbitrária, obrigatória) quando solicitado. Opcionalmente, forneça uma senha forte quando solicitado. Caso você não forneça a senha, uma senha forte será gerada para você usando as bibliotecas de segurança do .NET.

### <a name="use-azure-cli-10-for-linux-or-mac-users"></a>Usar a CLI 1.0 do Azure (para usuários do Linux ou do Mac)
Para começar a usar o Terraform em computadores Linux ou Macs com a CLI 1.0 do Azure, instale as bibliotecas adequadas no seu computador.  

1. Instale as ferramentas da CLI xPlat do Azure seguindo as etapas em [Instalar CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

2. Baixe e instale um processador JSON seguindo as instruções em [Baixar jq](https://stedolan.github.io/jq/download/).

3. Baixe e execute o script bash [azure-setup.sh script](https://github.com/mitchellh/packer/blob/master/contrib/azure-setup.sh) do console.

4. Para executar o script azure-setup.sh, baixe-o e execute o comando `./azure-setup setup` do console. Em seguida, entre na sua assinatura do Azure com privilégios administrativos.
 
5. Forneça um nome de aplicativo (cadeia de caracteres arbitrária, obrigatória) quando solicitado. Opcionalmente, forneça uma senha forte quando solicitado. Caso você não forneça a senha, uma senha forte será gerada para você usando as bibliotecas de segurança do .NET.

Todos os scripts anteriores criam um aplicativo Azure AD e a entidade de serviço. A entidade de serviço obtém um acesso em nível do proprietário ou colaborador na assinatura. Devido ao alto nível de acesso concedido, você sempre deve proteger as informações de segurança geradas por esses scripts. Anote as quatro partes das informações de segurança fornecidas por estes scripts: appId, senha, subscription_id e tenant_id.

## <a name="set-environment-variables"></a>Configurar variáveis de ambiente
Depois de criar e configurar a entidade de serviço do Azure AD, você precisa informar o Terraform sobre a ID do locatário, a ID da assinatura, a ID do cliente e o segredo do cliente a serem usados. Você pode fazer isso inserindo os valores em seus scripts do Terraform, conforme descrito em [Criar infraestrutura básica usando o Terraform](terraform-create-complete-vm.md). Como alternativa, você pode definir as seguintes variáveis de ambiente (e, assim, evitar o check-in ou o compartilhamento acidental de suas credenciais):

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

Você pode usar esse exemplo de script shell para definir essas variáveis:

```
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

Além disso, se estiver usando o Terraform com o Azure na China ou o Azure Governamental ou o Azure Alemanha, você precisará definir a variável de AMBIENTE de modo adequado.

## <a name="next-steps"></a>Próximas etapas
Agora você instalou o Terraform e configurou as credenciais do Azure, de modo que pode iniciar a implantação da infraestrutura em sua assinatura do Azure. Em seguida, saiba como [criar a infraestrutura com o Terraform](terraform-create-complete-vm.md).
