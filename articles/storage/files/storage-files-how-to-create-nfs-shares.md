---
title: Criar um compartilhamento NFS-arquivos do Azure (versão prévia)
description: Saiba como criar um compartilhamento de arquivos do Azure que pode ser montado usando o protocolo de sistema de arquivos de rede.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 323eed77d6f7a6ccfcdd0a7c7aecff3a125300dc
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602667"
---
# <a name="how-to-create-an-nfs-share"></a>Como criar um compartilhamento NFS

Os compartilhamentos de arquivos do Azure são compartilhamentos de arquivos totalmente gerenciados que residem na nuvem. Eles podem ser acessados usando o protocolo de bloqueio de mensagens do servidor ou o protocolo NFS (sistema de arquivos de rede). Este artigo aborda a criação de um compartilhamento de arquivos que usa o protocolo NFS. Para obter mais informações sobre ambos os protocolos, consulte [protocolos de compartilhamento de arquivos do Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Limitações

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Crie uma [conta de armazenamento](storage-how-to-create-premium-fileshare.md)de File.

    > [!IMPORTANT]
    > Os compartilhamentos NFS só podem ser acessados de redes confiáveis. As conexões com o compartilhamento NFS devem se originar de uma das seguintes fontes:

    - [Crie um ponto de extremidade privado](storage-files-networking-endpoints.md#create-a-private-endpoint) (recomendado) ou [restrinja o acesso ao seu ponto de extremidade público](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Configure uma VPN ponto a site (P2S) no Linux para uso com os arquivos do Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Configure uma VPN site a site para uso com os arquivos do Azure](storage-files-configure-s2s-vpn.md).
    - Configure o [ExpressRoute](../../expressroute/expressroute-introduction.md).
- Se pretende usar a CLI do Azure, [instale a versão mais recente](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="register-the-nfs-41-protocol"></a>Registrar o protocolo NFS 4,1

Se você estiver usando o módulo Azure PowerShell ou o CLI do Azure, Registre seu recurso usando os seguintes comandos:

### <a name="powershell"></a>PowerShell

```azurepowershell
Connect-AzAccount
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context
Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az login
az feature register --name AllowNfsFileShares \
                    --namespace Microsoft.Storage \
                    --subscription <yourSubscriptionIDHere>
az provider register --namespace Microsoft.Storage
```

## <a name="verify-feature-registration"></a>Verificar o registro do recurso

A aprovação de registro pode levar até uma hora. Para verificar se o registro foi concluído, use os seguintes comandos:

### <a name="powershell"></a>PowerShell

```azurepowershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNfsFileShares
```

### <a name="azure-cli"></a>CLI do Azure

```azurecli
az feature show --name AllowNfsFileShares --namespace Microsoft.Storage --subscription <yourSubscriptionIDHere>
```

## <a name="verify-storage-account-kind"></a>Verificar tipo de conta de armazenamento

Atualmente, apenas contas de armazenamento de fileseriais podem criar compartilhamentos NFS. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para verificar o tipo de conta de armazenamento que você tem, navegue até ela na portal do Azure. Em seguida, em sua conta de armazenamento, selecione **Propriedades**. Na folha Propriedades, examine o valor em **tipo de conta**, o valor deve ser **FileStorage**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para verificar se você tem uma conta de armazenamento de File, você pode usar o seguinte comando:

```azurepowershell
$accountKind=Get-AzStorageAccount -ResourceGroupName "yourResourceGroup" -Name "yourStorageAccountName"
$accountKind.Kind
```

A saída deve ser **FileStorage**, se não for, a sua conta de armazenamento é do tipo incorreto. Para criar uma conta de **armazenamento** de arquivo, consulte [como criar um compartilhamento de arquivos premium do Azure](storage-how-to-create-premium-fileshare.md).

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para verificar se você tem uma conta de armazenamento de File, você pode usar o seguinte comando:

```azurecli
az storage account show -g yourResourceGroup -n yourStorageAccountName
```

A saída deve conter **"Kind": "FileStorage"**, se não tiver, sua conta de armazenamento é do tipo incorreto. Para criar uma conta de **armazenamento** de arquivo, consulte [como criar um compartilhamento de arquivos premium do Azure](storage-how-to-create-premium-fileshare.md).

---
## <a name="create-an-nfs-share"></a>Criar um compartilhamento NFS

# <a name="portal"></a>[Portal](#tab/azure-portal)

Agora que você criou uma conta de armazenamento de arquivo e configurou a rede, é possível criar um compartilhamento de arquivos NFS. O processo é semelhante à criação de um compartilhamento SMB, você seleciona **NFS** em vez de **SMB** ao criar o compartilhamento.

1. Navegue até sua conta de armazenamento e selecione **Compartilhamentos de arquivo**.
1. Selecione **+ compartilhamento de arquivos** para criar um novo compartilhamento de arquivos.
1. Nomeie o compartilhamento de arquivos, selecione uma capacidade provisionada.
1. Para **protocolo** , selecione **NFS (versão prévia)**.
1. Para o **comprimir raiz** , faça uma seleção.

    - Comprimir raiz (padrão)-o acesso para o superusuário remoto (raiz) é mapeado para UID (65534) e GID (65534).
    - Nenhum superusuário remoto de comprimir (raiz) recebe o acesso como raiz.
    - Todos os acessos ao usuário de comprimido são mapeados para UID (65534) e GID (65534).
    
1. Selecione **Criar**.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="Captura de tela da folha de criação de compartilhamento de arquivos":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Verifique se o .NET Framework está instalado. Consulte [baixar .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Verifique se a versão do PowerShell instalada é `5.1` ou superior usando o comando a seguir.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Para atualizar sua versão do PowerShell, consulte [atualizando o Windows PowerShell existente](/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
1. Instale a versão mais recente do módulo PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Feche e reabra o console do PowerShell.

1. Instale o módulo de visualização **AZ. Storage** versão **2.5.2-Preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Para obter mais informações sobre como instalar módulos do PowerShell, consulte [instalar o Azure PowerShell Module](/powershell/azure/install-az-ps?view=azps-3.0.0)
   
1. Para criar um compartilhamento de arquivos Premium com o módulo Azure PowerShell, use o cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) .

> [!NOTE]
> Os tamanhos de compartilhamento provisionados são especificados pela cota de compartilhamento, os compartilhamentos de arquivos são cobrados no tamanho provisionado. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/storage/files/).

  ```powershell
  New-AzRmStorageShare `
   -ResourceGroupName $resourceGroupName `
   -StorageAccountName $storageAccountName `
   -Name myshare `
   -EnabledProtocol NFS `
   -RootSquash RootSquash `
   -Context $storageAcct.Context
  ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar um compartilhamento de arquivos Premium com o CLI do Azure, use o comando [AZ Storage share Create](/cli/azure/storage/share-rm) .

> [!NOTE]
> Os tamanhos de compartilhamento provisionados são especificados pela cota de compartilhamento, os compartilhamentos de arquivos são cobrados no tamanho provisionado. Para saber mais, confira a [página de preço](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share-rm create \
    --storage-account $STORAGEACCT \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --name "myshare" 
```
---

## <a name="next-steps"></a>Próximas etapas

Agora que você criou um compartilhamento NFS, para usá-lo, você precisa montá-lo em seu cliente Linux. Para obter detalhes, consulte [como montar um compartilhamento NFS](storage-files-how-to-mount-nfs-shares.md).

Se você tiver problemas, consulte [solucionar problemas de compartilhamentos de arquivos NFS do Azure](storage-troubleshooting-files-nfs.md).
