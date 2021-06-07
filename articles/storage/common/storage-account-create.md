---
title: Criar uma conta de armazenamento
titleSuffix: Azure Storage
description: Aprenda a criar uma conta de armazenamento para armazenar blobs, arquivos, filas e tabelas. Uma conta de armazenamento do Azure fornece um namespace exclusivo em Microsoft Azure para ler e gravar seus dados.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/11/2021
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 73bef1648b08f5c1e0664ef1da15375bc44149cc
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045341"
---
# <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento do Azure contém todos os seus objetos de dados do Armazenamento do Azure: blobs, arquivos, filas, tabelas e discos. A conta de armazenamento fornece um namespace exclusivo para os dados do armazenamento do Azure que podem ser acessados de qualquer lugar do mundo por HTTP ou HTTPS. Os dados em sua conta de armazenamento do Azure são duráveis e altamente disponíveis, seguros e amplamente escalonáveis.

Neste artigo de instruções, você aprende a criar uma conta de armazenamento usando o [portal do Azure](https://portal.azure.com/), [Azure PowerShell](/powershell/azure/), [CLI do Azure](/cli/azure)ou um [modelo Azure Resource Manager](../../azure-resource-manager/management/overview.md).  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Nenhum.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para criar uma conta de armazenamento do Azure com o PowerShell, verifique se você instalou o módulo Azure PowerShell AZ versão 0,7 ou posterior. Para obter mais informações, consulte [introdução ao módulo Azure PowerShell AZ](/powershell/azure/new-azureps-module-az).

Para localizar a versão atual, execute o seguinte comando:

```powershell
Get-InstalledModule -Name "Az"
```

Para instalar ou atualizar Azure PowerShell, consulte [instalar o módulo Azure PowerShell](/powershell/azure/install-Az-ps).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode entrar no Azure e executar comandos da CLI do Azure de uma das duas formas a seguir:

- Você pode executar comandos da CLI de dentro do portal do Azure, no Azure Cloud Shell.
- Você pode instalar a CLI e executar comandos da CLI localmente.

### <a name="use-azure-cloud-shell"></a>Usar o Azure Cloud Shell

O Azure Cloud Shell é um shell Bash gratuito que pode ser executado diretamente no portal do Azure. A CLI do Azure é pré-instalada e configurada para usar com sua conta. Clique no botão **Cloud Shell** no menu na seção superior direita da portal do Azure:

[![Cloud Shell](./media/storage-quickstart-create-account/cloud-shell-menu.png)](https://portal.azure.com)

O botão inicia um shell interativo que você pode usar para executar as etapas descritas neste artigo de instruções:

[![Captura de tela mostrando a janela do Cloud Shell no portal](./media/storage-quickstart-create-account/cloud-shell.png)](https://portal.azure.com)

### <a name="install-the-cli-locally"></a>Instalar a CLI localmente

Você também pode instalar e usar a CLI do Azure localmente. Os exemplos neste artigo exigem CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

# <a name="template"></a>[Modelo](#tab/template)

Nenhum.

---

## <a name="sign-in-to-azure"></a>Entrar no Azure

# <a name="portal"></a>[Portal](#tab/azure-portal)

Entre no [portal do Azure](https://portal.azure.com).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Entre em sua assinatura do Azure com o comando `Connect-AzAccount` e siga as instruções na tela para fazer a autenticação.

```powershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para iniciar o Azure Cloud Shell, entre no [portal do Azure](https://portal.azure.com).

Para fazer logon em sua instalação local da CLI, execute o comando [AZ login](/cli/azure/reference-index#az-login) :

```azurecli-interactive
az login
```

# <a name="template"></a>[Modelo](#tab/template)

N/D

---

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Cada conta de armazenamento deve pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente. Este artigo mostra como criar um grupo de recursos.

Uma conta de armazenamento de **uso geral v2** fornece acesso a todos os serviços de armazenamento do Azure: BLOBs, arquivos, filas, tabelas e discos. As etapas descritas aqui criam uma conta de armazenamento de uso geral v2, mas as etapas para a criação de qualquer tipo de conta de armazenamento são semelhantes. Para obter mais informações sobre tipos de contas de armazenamento e outras configurações da conta de armazenamento, confira [Visão geral da conta de armazenamento do Azure](storage-account-overview.md).

# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [storage-create-account-portal-include](../../../includes/storage-create-account-portal-include.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Primeiro, crie um grupo de recursos com o PowerShell usando o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hard-coding it repeatedly
$resourceGroup = "storage-resource-group"
$location = "westus"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

Caso não tenha certeza de qual região especificar para o parâmetro `-Location`, recupere uma lista de regiões com suporte para sua assinatura com o comando [Get-AzLocation](/powershell/module/az.resources/get-azlocation):

```powershell
Get-AzLocation | select Location
```

Em seguida, crie uma conta de armazenamento de uso geral V2 com armazenamento com redundância geográfica com acesso de leitura (RA-GRS) usando o comando [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) . Lembre-se de que o nome da sua conta de armazenamento deve ser exclusivo no Azure, portanto, substitua o valor do espaço reservado entre colchetes com seu próprio valor exclusivo:

```powershell
New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name <account-name> `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2
```

> [!IMPORTANT]
> Se você planeja usar [Azure data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), inclua nesta `-EnableHierarchicalNamespace $True` lista de parâmetros.

Para criar uma conta de armazenamento de uso geral V2 com uma opção de replicação diferente, substitua o valor desejado na tabela abaixo para o parâmetro **SkuName** .

|Opção de replicação  |Parâmetro SkuName  |
|---------|---------|
|Armazenamento com redundância local (LRS)     |Standard_LRS         |
|ZRS (armazenamento com redundância de zona)     |Standard_ZRS         |
|Armazenamento com redundância geográfica (GRS)     |Standard_GRS         |
|Armazenamento com redundância geográfica com acesso de leitura (GRS)     |Standard_RAGRS         |
|Armazenamento com redundância de zona geográfica (GZRS)    |Standard_GZRS         |
|RA-GZRS (armazenamento com redundância de zona geográfica com acesso de leitura)    |Standard_RAGZRS         |

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, crie criar um novo grupo de recursos com a CLI do Azure usando o comando [az group create](/cli/azure/group#az_group_create).

```azurecli-interactive
az group create \
    --name storage-resource-group \
    --location westus
```

Se não tiver certeza de qual região especificar para o parâmetro `--location`, recupere uma lista de regiões com suporte para a assinatura com o comando [az account list-locations](/cli/azure/account#az_account_list).

```azurecli-interactive
az account list-locations \
    --query "[].{Region:name}" \
    --out table
```

Em seguida, crie uma conta de armazenamento de uso geral V2 com o armazenamento com redundância geográfica com acesso de leitura usando o comando [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) . Lembre-se de que o nome da sua conta de armazenamento deve ser exclusivo no Azure, portanto, substitua o valor do espaço reservado entre colchetes com seu próprio valor exclusivo:

```azurecli-interactive
az storage account create \
    --name <account-name> \
    --resource-group storage-resource-group \
    --location westus \
    --sku Standard_RAGRS \
    --kind StorageV2
```

> [!IMPORTANT]
> Se você planeja usar [Azure data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), inclua nesta `--enable-hierarchical-namespace true` lista de parâmetros.

Para criar uma conta de armazenamento de uso geral V2 com uma opção de replicação diferente, substitua o valor desejado na tabela abaixo para o parâmetro **SKU** .

|Opção de replicação  |Parâmetro sku  |
|---------|---------|
|Armazenamento com redundância local (LRS)     |Standard_LRS         |
|ZRS (armazenamento com redundância de zona)     |Standard_ZRS         |
|Armazenamento com redundância geográfica (GRS)     |Standard_GRS         |
|Armazenamento com redundância geográfica com acesso de leitura (GRS)     |Standard_RAGRS         |
|Armazenamento com redundância de zona geográfica (GZRS)    |Standard_GZRS         |
|RA-GZRS (armazenamento com redundância de zona geográfica com acesso de leitura)    |Standard_RAGZRS         |

# <a name="template"></a>[Modelo](#tab/template)

Você pode usar o Azure PowerShell ou CLI do Azure para implantar um modelo do Resource Manager para criar uma conta de armazenamento. O modelo usado neste artigo de instruções é de [Azure Resource Manager modelos de início rápido](https://azure.microsoft.com/resources/templates/101-storage-account-create/). Para executar os scripts, selecione **Experimente** para abrir o Azure cloud Shell. Para colar o script, clique com o botão direito do mouse no shell e, em seguida, selecione **Colar**.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

> [!NOTE]
> Este modelo serve apenas como exemplo. Há muitas configurações de conta de armazenamento que não são configuradas como parte deste modelo. Por exemplo, se você quiser usar [Azure data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), modificaria esse modelo definindo a `isHnsEnabledad` Propriedade do `StorageAccountPropertiesCreateParameters` objeto como `true` . 

Para saber como modificar esse modelo ou criar novos, consulte:

- [Azure Resource Manager documentação](../../azure-resource-manager/index.yml).
- [Referência de modelo da conta de armazenamento](/azure/templates/microsoft.storage/allversions).
- [Exemplos de modelo de conta de armazenamento adicional](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage).

---

## <a name="delete-a-storage-account"></a>Excluir uma conta de armazenamento

A exclusão de uma conta de armazenamento exclui a conta inteira, incluindo todos os dados na conta e não pode ser desfeita.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navegue até a conta de armazenamento no [portal do Azure](https://portal.azure.com).
1. Clique em **Excluir**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para excluir a conta de armazenamento, use o comando [Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -Name <storage-account> -ResourceGroupName <resource-group>
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para excluir a conta de armazenamento, use o comando [AZ Storage Account Delete](/cli/azure/storage/account#az-storage-account-delete) :

```azurecli-interactive
az storage account delete --name <storage-account> --resource-group <resource-group>
```

# <a name="template"></a>[Modelo](#tab/template)

Para excluir a conta de armazenamento, use Azure PowerShell ou CLI do Azure.

```azurepowershell-interactive
$storageResourceGroupName = Read-Host -Prompt "Enter the resource group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"
Remove-AzStorageAccount -Name $storageAccountName -ResourceGroupName $storageResourceGroupName
```

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --name storageAccountName --resource-group resourceGroupName
```

---

Como alternativa, você pode excluir o grupo de recursos, que exclui a conta de armazenamento e todos os outros recursos nesse grupo de recursos. Para obter mais informações sobre como excluir um grupo de recursos, consulte [excluir recursos e grupo](../../azure-resource-manager/management/delete-resource-group.md)de recursos.

> [!WARNING]
> Não é possível restaurar uma conta de armazenamento excluída nem recuperar nenhuma parte de seu conteúdo antes da exclusão. Não se esqueça de fazer backup de todas as informações que você deseja salvar antes de excluir a conta. Isso também é verdadeiro para todos os recursos na conta – depois que você excluir um blob, tabela, fila ou arquivo, ele será excluído permanentemente.
>
> Se você tentar excluir uma conta de armazenamento associada a uma máquina virtual do Azure, poderá receber um erro indicando que a conta de armazenamento ainda está em uso. Para obter ajuda para solucionar esse erro, consulte [solucionar erros ao excluir contas de armazenamento](/troubleshoot/azure/virtual-machines/welcome-virtual-machines).

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da conta de armazenamento](storage-account-overview.md)
- [Atualizar para uma conta de armazenamento de uso geral v2](storage-account-upgrade.md)
- [Mover uma conta de armazenamento do Azure para outra região](storage-account-move.md)
- [Recuperar uma conta de armazenamento excluída](storage-account-recover.md)