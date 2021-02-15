---
title: Criar um compartilhamento NFS-arquivos do Azure (versão prévia)
description: Saiba como criar um compartilhamento de arquivos do Azure que pode ser montado usando o protocolo de sistema de arquivos de rede.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/22/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: dc23dec8a8d59a7762e93cdfaa2a39d824506e7b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382116"
---
# <a name="how-to-create-an-nfs-share"></a>Como criar um compartilhamento NFS
Os compartilhamentos de arquivos do Azure são compartilhamentos de arquivos totalmente gerenciados que residem na nuvem. Este artigo aborda a criação de um compartilhamento de arquivos que usa o protocolo NFS. Para obter mais informações sobre ambos os protocolos, consulte [protocolos de compartilhamento de arquivos do Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Limitações
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Pré-requisitos
- Os compartilhamentos NFS só podem ser acessados de redes confiáveis. As conexões com o compartilhamento NFS devem se originar de uma das seguintes fontes:
    - [Crie um ponto de extremidade privado](storage-files-networking-endpoints.md#create-a-private-endpoint) (recomendado) ou [restrinja o acesso ao seu ponto de extremidade público](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Configure uma VPN ponto a site (P2S) no Linux para uso com os arquivos do Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Configure uma VPN site a site para uso com os arquivos do Azure](storage-files-configure-s2s-vpn.md).
    - Configure o [ExpressRoute](../../expressroute/expressroute-introduction.md).

- Se pretende usar a CLI do Azure, [instale a versão mais recente](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

## <a name="register-the-nfs-41-protocol"></a>Registrar o protocolo NFS 4,1
Se você estiver usando o módulo Azure PowerShell ou o CLI do Azure, Registre seu recurso usando os seguintes comandos:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Use Azure PowerShell ou CLI do Azure para registrar o recurso de NFS 4,1 para arquivos do Azure.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
# Connect your PowerShell session to your Azure account, if you have not already done so.
Connect-AzAccount

# Set the actively selected subscription, if you have not already done so.
$subscriptionId = "<yourSubscriptionIDHere>"
$context = Get-AzSubscription -SubscriptionId $subscriptionId
Set-AzContext $context

# Register the NFS 4.1 feature with Azure Files to enable the preview.
Register-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares 
    
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
# Connect your Azure CLI to your Azure account, if you have not already done so.
az login

# Provide the subscription ID for the subscription where you would like to 
# register the feature
subscriptionId="<yourSubscriptionIDHere>"

az feature register \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId

az provider register \
    --namespace Microsoft.Storage
```

---

A aprovação de registro pode levar até uma hora. Para verificar se o registro foi concluído, use os seguintes comandos:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Use Azure PowerShell ou CLI do Azure para verificar o registro do recurso NFS 4,1 para arquivos do Azure. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Get-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
```azurecli
az feature show \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
```

---

## <a name="create-a-filestorage-storage-account"></a>Criar uma conta de armazenamento do FileStorage
Atualmente, os compartilhamentos de NFS 4,1 estão disponíveis apenas como compartilhamentos de arquivos premium. Para implantar um compartilhamento de arquivos Premium com suporte a protocolo NFS 4,1, você deve primeiro criar uma conta de armazenamento de armazenamento de arquivo. Uma conta de armazenamento é um objeto de nível superior no Azure que representa um pool de armazenamento compartilhado que pode ser usado para implantar vários compartilhamentos de arquivos do Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar uma conta de armazenamento do FileStorage, navegue até a portal do Azure.

1. No portal do Azure, selecione **contas de armazenamento** no menu à esquerda.

    ![portal do Azure página principal selecionar conta de armazenamento](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

2. Na janela **Contas de Armazenamento** que aparece, escolha **Adicionar**.
3. Selecione a assinatura na qual você deseja criar a conta de armazenamento.
4. Selecione o grupo de recursos no qual criar a conta de armazenamento

5. Em seguida, insira um nome para sua conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também deve ter entre 3 e 24 caracteres e pode incluir apenas números e letras minúsculas.
6. Selecione um local para sua conta de armazenamento ou use o local padrão.
7. Para **desempenho** , selecione **Premium**.

    Você deve selecionar **Premium** para **armazenamento** de filepara ser uma opção disponível na lista suspensa **tipo de conta** .

8. Selecione **tipo de conta** e escolha **FileStorage**.
9. Deixe a **replicação** definida com seu valor padrão de **armazenamento com REDUNDÂNCIA local (LRS)**.

    ![Como criar uma conta de armazenamento para um compartilhamento de arquivos Premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

10. Selecione **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.
11. Selecione **Criar**.

Depois que o recurso da conta de armazenamento tiver sido criado, navegue até ele.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para criar uma conta de armazenamento do FileStorage, abra um prompt do PowerShell e execute os comandos a seguir, lembre-se de substituir `<resource-group>` e `<storage-account>` pelos valores apropriados para o seu ambiente.

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para criar uma conta de armazenamento de armazenamento, abra seu terminal e execute os comandos a seguir, lembrando-os de substituir `<resource-group>` e `<storage-account>` pelos valores apropriados para o seu ambiente.

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
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
    
   Para atualizar sua versão do PowerShell, consulte [atualizando o Windows PowerShell existente](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell)
    
1. Instale a versão mais recente do módulo PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Feche e reabra o console do PowerShell.

1. Instale o módulo de visualização **AZ. Storage** versão **2.5.2-Preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Para obter mais informações sobre como instalar módulos do PowerShell, consulte [instalar o Azure PowerShell Module](/powershell/azure/install-az-ps?view=azps-3.0.0&preserve-view=true)
   
1. Para criar um compartilhamento de arquivos Premium com o módulo Azure PowerShell, use o cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) .

    > [!NOTE]
    > Os compartilhamentos de arquivos Premium são cobrados usando um modelo provisionado. O tamanho provisionado do compartilhamento é especificado `QuotaGiB` abaixo. Para obter mais informações, consulte [noções básicas sobre o modelo provisionado](understanding-billing.md#provisioned-model) e a [página de preços dos arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/).

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para criar um compartilhamento de arquivos Premium com o CLI do Azure, use o comando [AZ Storage share Create](/cli/azure/storage/share-rm) .

> [!NOTE]
> Os compartilhamentos de arquivos Premium são cobrados usando um modelo provisionado. O tamanho provisionado do compartilhamento é especificado `quota` abaixo. Para obter mais informações, consulte [noções básicas sobre o modelo provisionado](understanding-billing.md#provisioned-model) e a [página de preços dos arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>Próximas etapas
Agora que você criou um compartilhamento NFS, para usá-lo, você precisa montá-lo em seu cliente Linux. Para obter detalhes, consulte [como montar um compartilhamento NFS](storage-files-how-to-mount-nfs-shares.md).

Se você tiver problemas, consulte [solucionar problemas de compartilhamentos de arquivos NFS do Azure](storage-troubleshooting-files-nfs.md).