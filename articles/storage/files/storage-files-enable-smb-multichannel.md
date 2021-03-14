---
title: Habilitar o SMB multicanal
description: Saiba como habilitar o SMB multicanal em compartilhamentos de arquivos premium do Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2f867fa6d4b7e1d864a85106b5d957a53d38eb76
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732524"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>Habilitar o SMB multicanal em uma conta de armazenamento de File(versão prévia) 

As contas do Azure FileStorage dão suporte ao SMB multicanal (versão prévia), o que aumenta o desempenho de um cliente SMB 3. x estabelecendo várias conexões de rede com seus compartilhamentos de arquivos premium. Este artigo fornece orientações passo a passo para habilitar o SMB multicanal em uma conta de armazenamento existente. Para obter informações detalhadas sobre o Multichannel do SMB de arquivos do Azure, consulte desempenho de Multichannel do SMB.

## <a name="limitations"></a>Limitações

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Crie uma conta de armazenamento de](./storage-how-to-create-file-share.md)File.
- Se você pretende usar o módulo Azure PowerShell, [Instale a versão 3.0.1-preview do módulo](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview).

## <a name="getting-started"></a>Introdução

Abra uma janela do PowerShell e entre na sua assinatura do Azure. A partir daí, você pode se registrar para a versão prévia do SMB multicanal com os comandos a seguir.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> O registro pode levar até uma hora.

### <a name="verify-that-feature-registration-is-complete"></a>Verifique se o registro do recurso está concluído

Como pode levar até uma hora para habilitar o recurso em sua conta de armazenamento, talvez você queira usar o seguinte comando para validar se ele está registrado para sua assinatura:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>Habilitar o SMB Multichannel 
Depois de criar uma conta de armazenamento de File, você pode seguir as instruções para atualizar as configurações de Fibre Channel do SMB para sua conta de armazenamento.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Entre no portal do Azure e navegue até a conta de armazenamento de armazenamento de repositório na qual você deseja configurar o SMB multicanal.
1. Selecione **compartilhamentos de arquivos** em **serviço de arquivo** e, em seguida, selecione configurações de **compartilhamento de arquivos**.
1. Alterne o **SMB Multichannel** para **ativado** (ou **desativado** para desabilitar) e selecione **salvar**.

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Captura de tela da conta de armazenamento, o SMB multicanal é alternado.":::

Se a opção SMB multicanal não estiver visível em **configurações de compartilhamento de arquivos** ou você receber uma falha ao atualizar o erro de configuração ao atualizar a configuração, verifique se sua assinatura está registrada e se sua conta está em uma das [regiões](#regional-availability) com suporte com o tipo de conta e a replicação com suporte.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para habilitar o SMB multicanal usando o módulo Azure PowerShell, você deve [instalar a versão 3.0.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) do módulo.

Defina as variáveis `$resourceGroupName` e `$storageAccountName` para o grupo de recursos e a conta de armazenamento antes de executar esses comandos do PowerShell.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
O CLI do Azure ainda não dá suporte à configuração do SMB multicanal. Consulte as instruções do portal para configurar o SMB multicanal na conta de armazenamento.

---

> [!NOTE]
> Quaisquer alterações nas definições de configuração de Multichannel do SMB serão aplicadas a todos os compartilhamentos de arquivos na conta de armazenamento. No entanto, será necessário remontar o compartilhamento no seu cliente para que as alterações entrem em vigor.


## <a name="next-steps"></a>Próximas etapas 

- [Monte novamente o compartilhamento de arquivos](storage-how-to-use-files-windows.md) para tirar proveito do SMB multicanal.
- [Solucione quaisquer problemas relacionados ao SMB multicanal](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings).
- Para saber mais sobre os aprimoramentos, consulte [desempenho de Multichannel do SMB](storage-files-smb-multichannel-performance.md)
 - Para saber mais sobre o recurso de multicanal do Windows SMB, consulte [gerenciar SMB Mulitchannel](/azure-stack/hci/manage/manage-smb-multichannel).