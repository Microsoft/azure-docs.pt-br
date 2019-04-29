---
title: Criar uma conta de armazenamento de arquivos do Azure premium
description: Neste artigo, você aprenderá como criar uma conta de armazenamento de arquivos do Azure premium e um compartilhamento de arquivos de premium.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 72dec14dde47580313e57bb3b8d7315604929277
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844504"
---
# <a name="how-to-create-an-azure-premium-file-share"></a>Como criar um compartilhamento de arquivos do Azure premium

O tipo de conta de armazenamento FileStorage (visualização) representa uma nova camada para arquivos do Azure, permitindo que você crie compartilhamentos de arquivos com características de desempenho premium. Esses compartilhamentos de arquivos são projetados para aplicativos em escala empresarial, fornecendo compartilhamentos de alta taxa de transferência, IOPS alta e baixa latência consistente e de alto desempenho.

Este artigo mostra como criar esse novo tipo de conta usando [portal do Azure](https://portal.azure.com/), o Azure PowerShell e CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para acessar o Armazenamento do Azure, você precisará de uma assinatura do Azure. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-premium-file-share-using-the-azure-portal"></a>Criar um compartilhamento de arquivos do premium usando o portal do Azure

### <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [Portal do Azure](https://portal.azure.com/).

### <a name="create-a-filestorage-preview-storage-account"></a>Criar uma conta de armazenamento FileStorage (visualização)

Agora você está pronto para criar sua conta de armazenamento.

Cada conta de armazenamento deve pertencer a um grupo de recursos do Azure. Um grupo de recursos é um contêiner lógico para agrupar seus serviços do Azure. Quando você cria uma conta de armazenamento, tem a opção de criar um novo grupo de recursos ou usar um grupo de recursos existente. Este artigo mostra como criar um novo grupo de recursos.

1. No portal do Azure, selecione **contas de armazenamento** no menu à esquerda.

    ![Página principal do portal do Azure selecione conta de armazenamento](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. Na janela **Contas de Armazenamento** que aparece, escolha **Adicionar**.
1. Selecione a assinatura na qual você deseja criar a conta de armazenamento.
1. No campo **Grupo de recursos**, selecione **Criar novo**. Insira um nome para seu novo grupo de recursos, conforme mostrado na imagem a seguir.

1. Em seguida, insira um nome para sua conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também deve ter entre 3 e 24 caracteres e pode incluir apenas números e letras minúsculas.
1. Selecione um local para sua conta de armazenamento ou use o local padrão.
1. Para **desempenho** selecionar **Premium**.
1. Selecione **tipo de conta** e escolha **FileStorage (visualização)**.
1. Deixe **replicação** definido como seu valor padrão de **armazenamento localmente redundante (LRS)**.

    ![Como criar uma conta de armazenamento de arquivos do premium](media/storage-how-to-create-premium-fileshare/premium-files-storage-account.png)

1. Selecione **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.
1. Selecione **Criar**.

Depois que o recurso de conta de armazenamento tiver sido criado, navegue até ele.

### <a name="create-a-premium-file-share"></a>Criar um compartilhamento de arquivo premium

1. No menu à esquerda para a conta de armazenamento, role até a **do serviço de arquivo** seção e, em seguida, selecione **arquivos (visualização)**.
1. Selecione **+ compartilhamento de arquivos** para criar um compartilhamento de arquivos de premium.
1. Insira um nome e uma cota desejada para o compartilhamento de arquivos e, em seguida, selecione **criar**.

> [!NOTE]
> Tamanhos do compartilhamento provisionado é especificado pela cota de compartilhamento, compartilhamentos de arquivos são cobrados no tamanho provisionado, consulte o [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

   ![Criar um compartilhamento de arquivo premium](media/storage-how-to-create-premium-fileshare/create-premium-file-share.png)

### <a name="clean-up-resources"></a>Limpar recursos

Se você quiser limpar todos os recursos criados neste artigo, você pode simplesmente excluir o grupo de recursos. Excluir o grupo de recursos também exclui a conta de armazenamento associado, bem como quaisquer outros recursos associados com o grupo de recursos.

## <a name="create-a-premium-file-share-using-powershell"></a>Criar um compartilhamento de arquivos do premium usando o PowerShell

### <a name="create-an-account-using-powershell"></a>Criar uma conta usando o PowerShell

Primeiro, instale a última versão do módulo [PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget) do PowerShell.

Em seguida, atualize seu módulo PowerShell, entre em sua assinatura do Azure, crie um grupo de recursos e, em seguida, crie uma conta de armazenamento.

### <a name="upgrade-your-powershell-module"></a>Atualizar seu módulo do powershell

Para interagir com arquivos de premium com o PowerShell, você precisará instalar o módulo Az.Storage mais recente.

Comece abrindo uma sessão do PowerShell com permissões elevadas.

Instale o módulo Az.Storage:

```powershell
Install-Module Az.Storage -Repository PSGallery -AllowPrerelease -AllowClobber -Force
```

### <a name="sign-in-to-your-azure-subscription"></a>Entre em sua Assinatura do Azure

Use o comando `Login-AzAccount` e siga as instruções na tela para fazer a autenticação.

```powershell
Login-AzAccount
```

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com o PowerShell, use o comando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```powershell
# put resource group in a variable so you can use the same group name going forward,
# without hardcoding it repeatedly
$resourceGroup = "storage-how-to-resource-group"
$location = "westus2"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

### <a name="create-a-filestorage-preview-storage-account"></a>Criar uma conta de armazenamento filestorage (visualização)

Para criar uma conta de armazenamento FileStorage (visualização) do PowerShell, use o [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount) comando:

```powershell
$storageAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name "fileshowto" -SkuName "Premium_LRS" -Location "westus2" -Kind "FileStorage"
```

### <a name="create-a-premium-file-share"></a>Criar um compartilhamento de arquivo premium

Agora que você tem uma conta FileStorage, você pode criar um compartilhamento de arquivos de premium. Use o [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare) cmdlet criá-lo.

> [!NOTE]
> Tamanhos do compartilhamento provisionado é especificado pela cota de compartilhamento, compartilhamentos de arquivos são cobrados no tamanho provisionado, consulte o [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

```powershell
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

### <a name="clean-up-resources"></a>Limpar recursos

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup): 

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="create-a-premium-file-share-using-azure-cli"></a>Criar um compartilhamento de arquivos do premium usando a CLI do Azure

Para iniciar o Azure Cloud Shell, entre no [portal do Azure](https://portal.azure.com).

Se desejar fazer logon na instalação local da CLI, execute o comando de logon:

```cli
az login
```

### <a name="add-the-cli-extension-for-azure-premium-files"></a>Adicionar a extensão CLI para arquivos do Azure premium

Para interagir com arquivos premium usando a CLI, você precisará adicionar uma extensão para o shell.

Para fazer isso, insira o seguinte comando usando o Cloud Shell ou um shell local: `az extension add --name storage-preview`

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Para criar um novo grupo de recursos com a CLI do Azure, use o comando [az group create](/cli/azure/group).

```azurecli-interactive
az group create `
    --name files-howto-resource-group `
    --location westus2
```

### <a name="create-a-filestorage-preview-storage-account"></a>Criar uma conta de armazenamento FileStorage (visualização)

Para criar uma conta de armazenamento FileStorage (visualização) da CLI do Azure, use o [criar conta de armazenamento az](/cli/azure/storage/account) comando.

```azurecli-interactive
az storage account create `
    --name fileshowto `
    --resource-group files-howto-resource-group `
    --location westus `
    --sku Premium_LRS `
    --kind FileStorage
```

### <a name="get-the-storage-account-key"></a>Obter a chave da conta de armazenamento

Chaves da conta de armazenamento controlam o acesso a recursos em uma conta de armazenamento, neste artigo, usamos a chave para criar um compartilhamento de arquivos de premium. As chaves são criadas automaticamente quando você cria uma conta de armazenamento. Você pode obter as chaves da conta de armazenamento usando o comando [az storage account keys list](/cli/azure/storage/account/keys):

```azurecli-interactive 
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

### <a name="create-a-premium-file-share"></a>Criar um compartilhamento de arquivo premium

Agora que você tem uma conta FileStorage, você pode criar um compartilhamento de arquivos de premium. Use o [criar compartilhamento de armazenamento az](/cli/azure/storage/share) comando para criar um.

> [!NOTE]
> Tamanhos do compartilhamento provisionado é especificado pela cota de compartilhamento, compartilhamentos de arquivos são cobrados no tamanho provisionado, consulte o [página de preços](https://azure.microsoft.com/pricing/details/storage/files/) para obter mais detalhes.

```azurecli-interactive
az storage share create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" 
```

### <a name="clean-up-resources"></a>Limpar recursos

Para remover o grupo de recursos e seus recursos associados, incluindo a nova conta de armazenamento, use o comando [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou uma conta de armazenamento de arquivos de premium. Para saber mais sobre o desempenho que oferece essa conta, continue na seção de camada de desempenho do guia de planejamento.

> [!div class="nextstepaction"]
> [Níveis de desempenho do compartilhamento de arquivos](storage-files-planning.md#file-share-performance-tiers)