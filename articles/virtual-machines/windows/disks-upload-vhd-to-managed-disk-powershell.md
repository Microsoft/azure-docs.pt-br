---
title: Carregar um VHD no Azure ou copiar um disco entre regiões-Azure PowerShell
description: Saiba como carregar um VHD em um disco gerenciado do Azure e copiar um disco gerenciado entre regiões, usando Azure PowerShell, por meio do carregamento direto.
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: c766c78705a1c1e40a9385360d35ac06a3db3a5d
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99252229"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Carregar um VHD no Azure ou copiar um disco gerenciado para outra região-Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Baixe a versão mais recente [do AzCopy V10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Instale o módulo Azure PowerShell](/powershell/azure/install-Az-ps).
- Se você pretende carregar um VHD do local: um VHD de tamanho fixo que foi [preparado para o Azure](prepare-for-upload-vhd-image.md), armazenado localmente.
- Ou, um disco gerenciado no Azure, se você pretende executar uma ação de cópia.

## <a name="getting-started"></a>Introdução

Se preferir carregar discos por meio de uma GUI, você pode fazer isso usando Gerenciador de Armazenamento do Azure. Para obter detalhes, consulte: [usar Gerenciador de armazenamento do Azure para gerenciar o Azure Managed disks](../disks-use-storage-explorer-managed-disks.md)

Para carregar o VHD no Azure, você precisará criar um disco gerenciado vazio que esteja configurado para esse processo de carregamento. Antes de criar uma, há algumas informações adicionais que você deve saber sobre esses discos.

Esse tipo de disco gerenciado tem dois Estados exclusivos:

- ReadToUpload, que significa que o disco está pronto para receber um upload, mas nenhuma [assinatura de acesso seguro](../../storage/common/storage-sas-overview.md) (SAS) foi gerada.
- ActiveUpload, o que significa que o disco está pronto para receber um upload e a SAS foi gerada.

> [!NOTE]
> Em qualquer um desses Estados, o disco gerenciado será cobrado no preço de [HDD padrão](https://azure.microsoft.com/pricing/details/managed-disks/), independentemente do tipo real de disco. Por exemplo, um P10 será cobrado como um S10. Isso será verdadeiro até que `revoke-access` seja chamado no disco gerenciado, que é necessário para anexar o disco a uma VM.

## <a name="create-an-empty-managed-disk"></a>Criar um disco gerenciado vazio

Para poder criar um HDD padrão vazio para carregamento, você precisará do tamanho do arquivo do VHD que deseja carregar, em bytes. O código de exemplo o levará para você, mas, para você mesmo, você pode usar: `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length` . Esse valor é usado ao especificar o parâmetro **-UploadSizeInBytes** .

Agora, no Shell local, crie um HDD padrão vazio para carregamento, especificando a configuração de **carregamento** no parâmetro **-createoption** , bem como o parâmetro **-UploadSizeInBytes** no cmdlet [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) . Em seguida, chame [New-AzDisk](/powershell/module/az.compute/new-azdisk) para criar o disco.

Substitua `<yourdiskname>` , `<yourresourcegroupname>` e `<yourregion>` Execute os seguintes comandos:

> [!TIP]
> Se você estiver criando um disco do sistema operacional, adicione `-HyperVGeneration '<yourGeneration>'` a `New-AzDiskConfig` .

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Se você quiser carregar um SSD Premium ou um SSD padrão, substitua **Standard_LRS** por **Premium_LRS** ou **StandardSSD_LRS**. Ainda não há suporte para ultra discos.

Agora que você criou um disco gerenciado vazio que está configurado para o processo de carregamento, você pode carregar um VHD para ele. Para carregar um VHD no disco, você precisará de uma SAS gravável, para que você possa referenciá-lo como o destino do upload.

Para gerar uma SAS gravável de seu disco gerenciado vazio, substitua `<yourdiskname>` e `<yourresourcegroupname>` , em seguida, use os seguintes comandos:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Carregar um VHD

Agora que você tem uma SAS para seu disco gerenciado vazio, você pode usá-lo para definir o disco gerenciado como o destino para o comando de carregamento.

Use AzCopy V10 para carregar o arquivo VHD local em um disco gerenciado especificando o URI SAS gerado.

Esse carregamento tem a mesma taxa de transferência que o [HDD padrão](../disks-types.md#standard-hdd)equivalente. Por exemplo, se você tiver um tamanho que é igual a S4, terá uma taxa de transferência de até 60 MiB/s. Mas, se você tiver um tamanho igual a S70, terá uma taxa de transferência de até 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Depois que o upload for concluído e você não precisar mais gravar mais dados no disco, revogue a SAS. A revogação da SAS alterará o estado do disco gerenciado e permitirá que você anexe o disco a uma VM.

Substitua `<yourdiskname>` e `<yourresourcegroupname>` , em seguida, execute o seguinte comando:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Copiar um disco gerenciado

O carregamento direto também simplifica o processo de cópia de um disco gerenciado. Você pode copiar na mesma região ou copiar o disco gerenciado para outra região.

O script a seguir fará isso para você, o processo é semelhante às etapas descritas anteriormente, com algumas diferenças, já que você está trabalhando com um disco existente.

> [!IMPORTANT]
> Você precisa adicionar um deslocamento de 512 quando estiver fornecendo o tamanho do disco em bytes de um disco gerenciado do Azure. Isso ocorre porque o Azure omite o rodapé ao retornar o tamanho do disco. A cópia falhará se você não fizer isso. O script a seguir já faz isso para você.

Substitua o `<sourceResourceGroupHere>` , `<sourceDiskNameHere>` , `<targetDiskNameHere>` , `<targetResourceGroupHere>` `<yourOSTypeHere>` e `<yourTargetLocationHere>` (um exemplo de um valor de local seria uswest2) por seus valores e, em seguida, execute o script a seguir para copiar um disco gerenciado.

> [!TIP]
> Se você estiver criando um disco do sistema operacional, adicione-HyperVGeneration ' <yourGeneration> ' a `New-AzDiskConfig` .

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Próximas etapas

Agora que você carregou com êxito um VHD em um disco gerenciado, você pode anexar o disco a uma VM e começar a usá-lo.

Para saber como anexar um disco de dados a uma VM, consulte nosso artigo sobre o assunto: [anexar um disco de dados a uma VM do Windows com o PowerShell](attach-disk-ps.md). Para usar o disco como o disco do sistema operacional, consulte [criar uma VM do Windows de um disco especializado](create-vm-specialized.md#create-the-new-vm).
