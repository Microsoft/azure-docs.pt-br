---
title: Executar comandos do CLI do Azure ou do PowerShell com as credenciais do Azure AD para acessar dados de BLOB ou de fila | Microsoft Docs
description: O CLI do Azure e o PowerShell dão suporte à entrada com as credenciais do Azure AD para executar comandos no blob de armazenamento do Azure e dados de filas. Um token de acesso é fornecido para a sessão e usado para autorizar operações de chamada. As permissões dependem da função RBAC atribuída à entidade de segurança do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/03/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 80ab896e1393d6c68b22a61d1b96acd507aa6994
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249900"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Executar comandos do CLI do Azure ou do PowerShell com as credenciais do Azure AD para acessar dados de BLOB ou fila

O armazenamento do Azure fornece extensões para CLI do Azure e PowerShell que permitem que você entre e execute comandos de script com as credenciais do Azure Active Directory (AD do Azure). Quando você entra no CLI do Azure ou no PowerShell com credenciais do Azure AD, um token de acesso OAuth 2,0 é retornado. Esse token é usado automaticamente pela CLI ou pelo PowerShell para autorizar operações de dados subsequentes em relação ao armazenamento de BLOB ou de fila. Para operações com suporte, você não precisa passar uma chave de conta ou token SAS com o comando.

Você pode atribuir permissões a dados de BLOB e de fila a uma entidade de segurança do Azure AD por meio do RBAC (controle de acesso baseado em função). Para obter mais informações sobre as funções RBAC no armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados do armazenamento do Azure com o RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Operações com suporte

As extensões têm suporte para operações em contêineres e filas. As operações que você pode chamar dependem das permissões concedidas à entidade de segurança do Azure AD com a qual você entra no CLI do Azure ou no PowerShell. Permissões para contêineres de Armazenamento do Microsoft Azure ou filas são atribuídas por meio do controle de acesso baseado em função (RBAC). Por exemplo, se você tiver atribuído a função de **leitor de dados de blob** , poderá executar comandos de script que lêem dados de um contêiner ou fila. Se você tiver atribuído a função **colaborador de dados de blob** , poderá executar comandos de script que lêem, gravam ou excluem um contêiner ou uma fila ou os dados que eles contêm. 

Para obter detalhes sobre as permissões necessárias para cada operação de armazenamento do Azure em um contêiner ou fila, consulte [chamar operações de armazenamento com tokens OAuth](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Chamar comandos da CLI usando credenciais do Azure AD

CLI do Azure dá suporte `--auth-mode` ao parâmetro para operações de dados de BLOB e de fila:

- Defina o `--auth-mode` parâmetro como `login` para entrar usando uma entidade de segurança do Azure AD.
- Defina o `--auth-mode` parâmetro o valor `key` herdado para tentar consultar uma chave de conta, se nenhum parâmetro de autenticação para a conta for fornecido. 

O exemplo a seguir mostra como criar um contêiner em uma nova conta de armazenamento de CLI do Azure usando suas credenciais do Azure AD. Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores: 

1. Certifique-se de ter instalado CLI do Azure versão 2.0.46 ou posterior. Execute `az --version` para verificar sua versão instalada.

1. Execute `az login` e autentique na janela do navegador: 

    ```azurecli
    az login
    ```

1. Especifique a assinatura desejada. Criar um grupo de recursos usando [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Crie uma conta de armazenamento dentro desse grupo de recursos usando [AZ Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create):

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```

1. Antes de criar o contêiner, atribua a função de [colaborador de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a si mesmo. Embora você seja o proprietário da conta, você precisa de permissões explícitas para executar operações de dados na conta de armazenamento. Para obter mais informações sobre como atribuir funções RBAC, consulte [conceder acesso ao blob do Azure e dados de fila com RBAC no portal do Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > As atribuições de função do RBAC podem levar alguns minutos para serem propagadas.

1. Chame o comando [AZ Storage container Create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) com o `--auth-mode` parâmetro definido como `login` para criar o contêiner usando suas credenciais do Azure AD:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

A variável de ambiente associada ao `--auth-mode` parâmetro é `AZURE_STORAGE_AUTH_MODE`. Você pode especificar o valor apropriado na variável de ambiente para evitar incluí-lo em cada chamada para uma operação de dados do armazenamento do Azure.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Chamar comandos do PowerShell usando credenciais do Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para usar Azure PowerShell para entrar e executar operações subsequentes no armazenamento do Azure usando as credenciais do Azure AD, crie um contexto de armazenamento para fazer referência à conta `-UseConnectedAccount` de armazenamento e incluindo o parâmetro.

O exemplo a seguir mostra como criar um contêiner em uma nova conta de armazenamento de Azure PowerShell usando suas credenciais do Azure AD. Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

1. Entre em sua assinatura do Azure com o `Connect-AzAccount` comando e siga as instruções na tela para inserir suas credenciais do Azure AD: 

    ```powershell
    Connect-AzAccount
    ```

1. Crie um grupo de recursos do Azure chamando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Crie uma conta de armazenamento chamando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Obtenha o contexto da conta de armazenamento que especifica a nova conta de armazenamento chamando [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Ao atuar em uma conta de armazenamento, você pode fazer referência ao contexto em vez de passar repetidamente as credenciais. Inclua o `-UseConnectedAccount` parâmetro para chamar quaisquer operações de dados subsequentes usando suas credenciais do Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Antes de criar o contêiner, atribua a função de [colaborador de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a si mesmo. Embora você seja o proprietário da conta, você precisa de permissões explícitas para executar operações de dados na conta de armazenamento. Para obter mais informações sobre como atribuir funções RBAC, consulte [conceder acesso ao blob do Azure e dados de fila com RBAC no portal do Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > As atribuições de função do RBAC podem levar alguns minutos para serem propagadas.

1. Crie um contêiner chamando [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Como essa chamada usa o contexto criado nas etapas anteriores, o contêiner é criado usando suas credenciais do Azure AD. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as funções RBAC para o armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados de armazenamento com o RBAC](storage-auth-aad-rbac.md).
- Para saber mais sobre como usar identidades gerenciadas para recursos do Azure com o armazenamento do Azure, confira autenticar o [acesso a BLOBs e filas com Azure Active Directory e identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md).
- Para saber como autorizar o acesso aos contêineres e filas de seus aplicativos de armazenamento, consulte [Use o Microsoft Azure Active Directory com aplicativos de armazenamento](storage-auth-aad-app.md).
