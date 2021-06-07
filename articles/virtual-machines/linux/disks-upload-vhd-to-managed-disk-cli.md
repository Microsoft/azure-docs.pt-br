---
title: Carregar um VHD no Azure ou copiar um disco entre regiões-CLI do Azure
description: Saiba como carregar um VHD em um disco gerenciado do Azure e copiar um disco gerenciado entre regiões, usando o CLI do Azure, por meio do carregamento direto.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 06/15/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 473e87904742395eca6b7eeba0875cd93789104d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91978978"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Carregar um VHD no Azure ou copiar um disco gerenciado para outra região-CLI do Azure

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Baixe a versão mais recente [do AzCopy V10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Instale a CLI do Azure](/cli/azure/install-azure-cli).
- Se você pretende carregar um VHD do local: um VHD de tamanho fixo que foi [preparado para o Azure](../windows/prepare-for-upload-vhd-image.md), armazenado localmente.
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

Para poder criar um HDD padrão vazio para carregamento, você precisará do tamanho do arquivo do VHD que deseja carregar, em bytes. Para obtê-lo, você pode usar o `wc -c <yourFileName>.vhd` ou o `ls -al <yourFileName>.vhd` . Esse valor é usado ao especificar o parâmetro **--upload-size-bytes** .

Crie um HDD padrão vazio para carregamento, especificando o parâmetro **-– para-upload** e o parâmetro **--upload-size-bytes** em um cmdlet Create de [disco](/cli/azure/disk#az-disk-create) :

Substitua `<yourdiskname>` , `<yourresourcegroupname>` , `<yourregion>` com os valores de sua escolha. O `--upload-size-bytes` parâmetro contém um valor de exemplo de `34359738880` , substitua-o por um valor apropriado para você.

> [!TIP]
> Se você estiver criando um disco do sistema operacional, adicione--Hyper-v-Generation <yourGeneration> ao `az disk create` .

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Se você quiser carregar um SSD Premium ou um SSD padrão, substitua **standard_lrs** por **premium_LRS** ou **standardssd_lrs**. Os ultra discos não têm suporte por enquanto.

Agora que você criou um disco gerenciado vazio que está configurado para o processo de carregamento, você pode carregar um VHD para ele. Para carregar um VHD no disco, você precisará de uma SAS gravável, para que você possa referenciá-lo como o destino do upload.

Para gerar uma SAS gravável de seu disco gerenciado vazio, substitua `<yourdiskname>` e `<yourresourcegroupname>` , em seguida, use o seguinte comando:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Valor retornado de exemplo:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Carregar um VHD

Agora que você tem uma SAS para seu disco gerenciado vazio, você pode usá-lo para definir o disco gerenciado como o destino para o comando de carregamento.

Use AzCopy V10 para carregar o arquivo VHD local em um disco gerenciado especificando o URI SAS gerado.

Esse carregamento tem a mesma taxa de transferência que o [HDD padrão](../disks-types.md#standard-hdd)equivalente. Por exemplo, se você tiver um tamanho que é igual a S4, terá uma taxa de transferência de até 60 MiB/s. Mas, se você tiver um tamanho igual a S70, terá uma taxa de transferência de até 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Depois que o upload for concluído e você não precisar mais gravar mais dados no disco, revogue a SAS. A revogação da SAS alterará o estado do disco gerenciado e permitirá que você anexe o disco a uma VM.

Substitua `<yourdiskname>` e `<yourresourcegroupname>` , em seguida, use o seguinte comando para tornar o disco utilizável:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Copiar um disco gerenciado

O carregamento direto também simplifica o processo de cópia de um disco gerenciado. Você pode copiar na mesma região ou em uma região cruzada (para outra região).

O script a seguir fará isso para você, o processo é semelhante às etapas descritas anteriormente, com algumas diferenças, já que você está trabalhando com um disco existente.

> [!IMPORTANT]
> Você precisa adicionar um deslocamento de 512 quando estiver fornecendo o tamanho do disco em bytes de um disco gerenciado do Azure. Isso ocorre porque o Azure omite o rodapé ao retornar o tamanho do disco. A cópia falhará se você não fizer isso. O script a seguir já faz isso para você.

Substitua o `<sourceResourceGroupHere>` , `<sourceDiskNameHere>` , `<targetDiskNameHere>` , `<targetResourceGroupHere>` e `<yourTargetLocationHere>` (um exemplo de um valor de local seria uswest2) por seus valores e, em seguida, execute o script a seguir para copiar um disco gerenciado.

> [!TIP]
> Se você estiver criando um disco do sistema operacional, adicione--Hyper-v-Generation <yourGeneration> ao `az disk create` .

```azurecli
sourceDiskName=<sourceDiskNameHere>
sourceRG=<sourceResourceGroupHere>
targetDiskName=<targetDiskNameHere>
targetRG=<targetResourceGroupHere>
targetLocation=<yourTargetLocationHere>

sourceDiskSizeBytes=$(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocation --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI=$(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Próximas etapas

Agora que você carregou com êxito um VHD em um disco gerenciado, é possível anexar o disco como um [disco de dados a uma VM existente](add-disk.md) ou [anexar o disco a uma VM como um disco do sistema operacional](upload-vhd.md#create-the-vm), para criar uma nova VM.