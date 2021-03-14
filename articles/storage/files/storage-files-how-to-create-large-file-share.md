---
title: Habilitar e criar compartilhamentos de arquivos grandes-arquivos do Azure
description: Neste artigo, você aprenderá a habilitar e a criar grandes compartilhamentos de arquivos.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/29/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c22b3f3164cbb7c1a7ed150d093f77777c7b1023
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102501287"
---
# <a name="enable-and-create-large-file-shares"></a>Habilitar e criar compartilhamentos de arquivos grandes

Quando você habilita grandes compartilhamentos de arquivos em sua conta de armazenamento, os compartilhamentos de arquivos podem ser escalados verticalmente até 100 TiB, ao mesmo tempo em que aumentam os limites de IOPS e taxa de transferência para os Você também pode habilitar esse dimensionamento em suas contas de armazenamento existentes para seus compartilhamentos de arquivos existentes. Consulte [compartilhamento de arquivos e destinos de escala de arquivo](storage-files-scale-targets.md#azure-files-scale-targets) para obter detalhes. 

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Se pretende usar a CLI do Azure, [instale a versão mais recente](/cli/azure/install-azure-cli).
- Se você pretende usar o módulo Azure PowerShell, [Instale a versão mais recente](/powershell/azure/install-az-ps).

## <a name="restrictions"></a>Restrições

Por enquanto, você só pode usar LRS (armazenamento com redundância local) ou ZRS (armazenamento com redundância de zona) em contas habilitadas para compartilhamento de arquivos grandes. Você não pode usar armazenamento com redundância de zona geográfica (GZRS), armazenamento com redundância geográfica (GRS), armazenamento com redundância geográfica com acesso de leitura (RA-GRS) ou armazenamento com redundância de acesso de leitura (RA-GZRS).

A habilitação de grandes compartilhamentos de arquivos em uma conta é um processo irreversível. Depois de habilitá-lo, você não conseguirá converter sua conta em GZRS, GRS, RA-GRS ou RA-GZRS.

## <a name="create-a-new-storage-account"></a>Criar uma nova conta de armazenamento

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Entre no [portal do Azure](https://portal.azure.com).
1. No portal do Azure, clique em **Todos os serviços**. 
1. Na lista de recursos, insira **Contas de Armazenamento**. A lista filtra com base em sua entrada, à medida que você digita. Selecione **Contas de Armazenamento**.
1. Na janela **contas de armazenamento** que aparece, selecione **Adicionar**.
1. Selecione a assinatura que você usará para criar a conta de armazenamento.
1. No campo **Grupo de recursos**, selecione **Criar novo**. Insira um nome para o novo grupo de recursos.

    ![Captura de tela mostrando como criar um grupo de recursos no portal](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Em seguida, insira um nome para sua conta de armazenamento. O nome deve ser exclusivo em todo o Azure. O nome também deve ter de 3 a 24 caracteres de comprimento e só pode ter números e letras minúsculas.
1. Selecione um local para sua conta de armazenamento.
1. Defina a replicação para armazenamento com redundância **local** ou **armazenamento com redundância de zona**.
1. Deixe esses campos com seus valores padrão:

   |Campo  |Valor  |
   |---------|---------|
   |Modelo de implantação     |Gerenciador de Recursos         |
   |Desempenho     |Standard         |
   |Tipo de conta     |StorageV2 (uso geral v2)         |
   |Camada de acesso     |Frequente         |

1. Selecione **avançado** e, em seguida, selecione o botão de opção **habilitado** à direita de **grandes compartilhamentos de arquivos**.
1. Selecione **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.

    ![Captura de tela com o botão de opção "habilitado" em uma nova conta de armazenamento no portal do Azure](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Selecione **Criar**.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, [Instale a versão mais recente do CLI do Azure](/cli/azure/install-azure-cli) para que você possa habilitar grandes compartilhamentos de arquivos.

Para criar uma conta de armazenamento com grandes compartilhamentos de arquivos habilitados, use o comando a seguir. Substitua `<yourStorageAccountName>` , `<yourResourceGroup>` e `<yourDesiredRegion>` pelas suas informações.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Primeiro, [Instale a versão mais recente do PowerShell](/powershell/azure/install-az-ps) para que você possa habilitar compartilhamentos de arquivos grandes.

Para criar uma conta de armazenamento com grandes compartilhamentos de arquivos habilitados, use o comando a seguir. Substitua `<yourStorageAccountName>` , `<yourResourceGroup>` e `<yourDesiredRegion>` pelas suas informações.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, RA-GRS, or RA-GZRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```
---

## <a name="enable-large-files-shares-on-an-existing-account"></a>Habilitar compartilhamentos de arquivos grandes em uma conta existente

Você também pode habilitar compartilhamentos de arquivos grandes em suas contas existentes. Se você habilitar grandes compartilhamentos de arquivos, não será possível converter em GZRS, GRS, RA-GRS ou RA-GZRS. A habilitação de grandes compartilhamentos de arquivos é irreversível nessa conta de armazenamento.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Abra o [portal do Azure](https://portal.azure.com)e vá para a conta de armazenamento onde você deseja habilitar grandes compartilhamentos de arquivos.
1. Abra a conta de armazenamento e selecione **configuração**.
1. Selecione **habilitado** em **compartilhamentos de arquivos grandes** e, em seguida, selecione **salvar**.
1. Selecione **visão geral** e selecione **Atualizar**.

![Selecionando o botão de opção habilitado em uma conta de armazenamento existente no portal do Azure](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Agora você habilitou grandes compartilhamentos de arquivos em sua conta de armazenamento. Em seguida, você deve [atualizar a cota do compartilhamento existente](#expand-existing-file-shares) para tirar proveito da maior capacidade e escala.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para habilitar grandes compartilhamentos de arquivos em sua conta existente, use o comando a seguir. Substitua `<yourStorageAccountName>` e `<yourResourceGroup>` por suas informações.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

Agora você habilitou grandes compartilhamentos de arquivos em sua conta de armazenamento. Em seguida, você deve [atualizar a cota do compartilhamento existente](#expand-existing-file-shares) para tirar proveito da maior capacidade e escala.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para habilitar grandes compartilhamentos de arquivos em sua conta existente, use o comando a seguir. Substitua `<yourStorageAccountName>` e `<yourResourceGroup>` por suas informações.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

Agora você habilitou grandes compartilhamentos de arquivos em sua conta de armazenamento. Em seguida, você deve [atualizar a cota do compartilhamento existente](#expand-existing-file-shares) para tirar proveito da maior capacidade e escala.

---

## <a name="create-a-large-file-share"></a>Criar um compartilhamento de arquivos grande

Depois de habilitar grandes compartilhamentos de arquivos em sua conta de armazenamento, você poderá criar compartilhamentos de arquivos nessa conta com cotas maiores. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

A criação de um compartilhamento de arquivos grande é quase idêntica à criação de um compartilhamento de arquivos padrão. A principal diferença é que você pode definir uma cota de até 100 TiB.

1. Em sua conta de armazenamento, selecione **compartilhamentos de arquivos**.
1. Selecione **+Compartilhamento de arquivos**.
1. Insira um nome para o compartilhamento de arquivos. Você também pode definir o tamanho da cota que deseja, até 100 TiB. Em seguida, selecione **Criar**. 

![A interface do usuário do portal do Azure mostrando as caixas nome e cota](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar um compartilhamento de arquivos grande, use o comando a seguir. Substitua `<yourStorageAccountName>` , `<yourStorageAccountKey>` e `<yourFileShareName>` pelas suas informações.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para criar um compartilhamento de arquivos grande, use o comando a seguir. Substitua `<YourStorageAccountName>` , `<YourStorageAccountKey>` e `<YourStorageAccountFileShareName>` pelas suas informações.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```
---

## <a name="expand-existing-file-shares"></a>Expandir compartilhamentos de arquivos existentes

Depois de habilitar grandes compartilhamentos de arquivos em sua conta de armazenamento, você também pode expandir os compartilhamentos de arquivos existentes nessa conta para a cota mais alta. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Em sua conta de armazenamento, selecione **compartilhamentos de arquivos**.
1. Clique com o botão direito do mouse no compartilhamento de arquivos e selecione **cota**.
1. Insira o novo tamanho desejado e selecione **OK**.

![A interface do usuário do portal do Azure com cota de compartilhamentos de arquivos existentes](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para definir a cota para o tamanho máximo, use o comando a seguir. Substitua `<yourStorageAccountName>` , `<yourStorageAccountKey>` e `<yourFileShareName>` pelas suas informações.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para definir a cota para o tamanho máximo, use o comando a seguir. Substitua `<YourStorageAccountName>` , `<YourStorageAccountKey>` e `<YourStorageAccountFileShareName>` pelas suas informações.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```
---

## <a name="next-steps"></a>Próximas etapas

* [Conectar e montar um compartilhamento de arquivos no Windows](storage-how-to-use-files-windows.md)
* [Conectar e montar um compartilhamento de arquivos no Linux](storage-how-to-use-files-linux.md)
* [Conectar e montar um compartilhamento de arquivos no macOS](storage-how-to-use-files-mac.md)