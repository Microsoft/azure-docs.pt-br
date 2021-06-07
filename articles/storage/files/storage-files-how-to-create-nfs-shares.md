---
title: Criar um compartilhamento NFS – Arquivos do Azure (versão prévia)
description: Saiba como criar um compartilhamento de arquivo do Azure que pode ser montado usando o protocolo Network File System.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/22/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b085b9991175d8cd43e2dac0db80c5af4e703c34
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102521230"
---
# <a name="how-to-create-an-nfs-share"></a>Como criar um compartilhamento NFS
Os compartilhamentos de arquivos do Azure são totalmente gerenciados e residem na nuvem. Este artigo aborda a criação de um compartilhamento de arquivo que usa o protocolo NFS. Para obter mais informações sobre ambos os protocolos, confira [Protocolos de compartilhamento de arquivo do Azure](storage-files-compare-protocols.md).

## <a name="limitations"></a>Limitações
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Pré-requisitos
- Os compartilhamentos NFS só podem ser acessados de redes confiáveis. As conexões com o compartilhamento NFS devem se originar de uma das seguintes fontes:
    - [Crie um ponto de extremidade privado](storage-files-networking-endpoints.md#create-a-private-endpoint) (recomendado) ou [restrinja o acesso ao seu ponto de extremidade público](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Configure uma VPN P2S (Ponto a Site) no Linux para usar com os Arquivos do Azure](storage-files-configure-p2s-vpn-linux.md).
    - [Configurar uma VPN Site a Site para uso com os Arquivos do Azure](storage-files-configure-s2s-vpn.md).
    - Configurar o [ExpressRoute](../../expressroute/expressroute-introduction.md).

- Se pretende usar a CLI do Azure, [instale a versão mais recente](/cli/azure/install-azure-cli).

## <a name="register-the-nfs-41-protocol"></a>Registrar o protocolo NFS 4.1
Se você estiver usando o módulo Azure PowerShell ou a CLI do Azure, registre o seu recurso usando os seguintes comandos:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Use o Azure PowerShell ou a CLI do Azure para registrar o recurso de NFS 4.1 para os Arquivos do Azure.

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

A aprovação do registro pode levar até uma hora. Para verificar se o registro foi concluído, use os seguintes comandos:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Use o Azure PowerShell ou a CLI do Azure para verificar o registro do recurso NFS 4.1 para Arquivos do Azure. 

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
Atualmente, compartilhamentos do NFS 4.1 estão disponíveis apenas como compartilhamentos de arquivos Premium. Para implantar um compartilhamento de arquivo Premium com suporte ao protocolo NFS 4.1, você deve primeiro criar uma conta de armazenamento do FileStorage. Uma conta de armazenamento é um objeto de nível superior no Azure que representa um pool de armazenamento compartilhado que pode ser usado para implantar vários compartilhamentos de arquivos do Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar uma conta de armazenamento do FileStorage, navegue até o portal do Azure.

1. No portal do Azure, selecione **Contas de Armazenamento** no menu à esquerda.

    ![Portal do Azure, página principal, selecionar conta de armazenamento](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

2. Na janela **Contas de Armazenamento** que aparece, escolha **Adicionar**.
3. Selecione a assinatura na qual você deseja criar a conta de armazenamento.
4. Selecione o grupo de recursos no qual criar a conta de armazenamento

5. Em seguida, insira um nome para sua conta de armazenamento. O nome escolhido deve ser exclusivo no Azure. O nome também deve ter entre 3 e 24 caracteres e pode incluir apenas números e letras minúsculas.
6. Selecione um local para sua conta de armazenamento ou use o local padrão.
7. Para **Desempenho**, selecione **Premium**.

    Você deve selecionar **Premium** para **FileStorage** para ser uma opção disponível na lista suspensa **Tipo de conta**.

8. Selecione **Tipo de conta** e escolha **FileStorage**.
9. Deixe a **Replicação** definida com seu valor padrão igual a **LRS (armazenamento com redundância local)** .

    ![Como criar uma conta de armazenamento para um compartilhamento de arquivo Premium](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

10. Selecione **Revisar + Criar** para examinar as configurações da conta de armazenamento e criar a conta.
11. Selecione **Criar**.

Quando o seu recurso de conta de armazenamento for criado, navegue até ele.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para criar uma conta de armazenamento do FileStorage, abra um prompt do PowerShell e execute os comandos a seguir – não se esqueça de substituir `<resource-group>` e `<storage-account>` pelos valores apropriados para o seu ambiente.

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
Para criar uma conta de armazenamento do FileStorage, abra seu terminal e execute os comandos a seguir – não se esqueça de substituir `<resource-group>` e `<storage-account>` pelos valores apropriados para o seu ambiente.

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

Agora que você criou uma conta de armazenamento do FileStorage e configurou a rede, é possível criar um compartilhamento de arquivo NFS. O processo é semelhante à criação de um compartilhamento SMB, mas você seleciona **NFS** em vez de **SMB** ao criar o compartilhamento.

1. Navegue até sua conta de armazenamento e selecione **Compartilhamentos de arquivo**.
1. Selecione **+ Compartilhamento de arquivo** para criar um compartilhamento de arquivo.
1. Dê um nome ao compartilhamento de arquivo e selecione uma capacidade provisionada.
1. Em **protocolo**, selecione **NFS (versão prévia)** .
1. Em **Raiz Squash**, faça uma seleção.

    - Raiz Squash (padrão) – O acesso do superusuário remoto (raiz) é mapeado para a UID (65534) e a GID (65534).
    - Sem raiz squash – O superusuário remoto (raiz) recebe o acesso como raiz.
    - Todos os Squash – Todos os acessos de usuário são mapeados para a UID (65534) e a GID (65534).
    
1. Selecione **Criar**.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="Captura de tela da folha de criação de compartilhamento de arquivo":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Verifique se o .NET Framework está instalado. Confira [Baixar o .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Verifique se a versão do PowerShell instalada é `5.1` ou superior usando o comando a seguir.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Para atualizar sua versão do PowerShell, confira [Atualizando o Windows PowerShell existente](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)
    
1. Instale a última versão do módulo PowershellGet.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Feche e abra novamente o console do PowerShell.

1. Instale o módulo de versão prévia **AZ.Storage** versão **2.5.2-versão prévia**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Para obter mais informações sobre como instalar módulos do PowerShell, confira [Instalar o módulo Azure PowerShell](/powershell/azure/install-az-ps)
   
1. Para criar um compartilhamento de arquivo Premium com o módulo do Azure PowerShell, use o cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare).

    > [!NOTE]
    > Os compartilhamentos de arquivos Premium são cobrados usando um modelo provisionado. O tamanho provisionado do compartilhamento é especificado por `QuotaGiB`, abaixo. Para obter mais informações, confira [Entendendo o modelo provisionado](understanding-billing.md#provisioned-model) e a [página de preços dos Arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/).

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
Para criar um compartilhamento de arquivo Premium com a CLI do Azure, use o comando [az storage share create](/cli/azure/storage/share-rm).

> [!NOTE]
> Os compartilhamentos de arquivos Premium são cobrados usando um modelo provisionado. O tamanho provisionado do compartilhamento é especificado por `quota`, abaixo. Para obter mais informações, confira [Entendendo o modelo provisionado](understanding-billing.md#provisioned-model) e a [página de preços dos Arquivos do Azure](https://azure.microsoft.com/pricing/details/storage/files/).

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
Agora que você criou um compartilhamento NFS, para usá-lo, você precisa montá-lo em seu cliente Linux. Para obter detalhes, confira [Como montar um compartilhamento NFS](storage-files-how-to-mount-nfs-shares.md).

Se você tiver problemas, confira [Solucionar problemas de compartilhamentos de arquivos NFS do Azure](storage-troubleshooting-files-nfs.md).