---
title: Enviar dados para a camada de BLOBs quente, frio e arquivo por meio de Azure Data Box/Azure Data Box Heavy
description: Descreve como usar Azure Data Box ou Azure Data Box Heavy para enviar dados a uma camada de armazenamento de blobs de blocos apropriada, como quente, frio ou arquivo morto
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 80c4d8a70454c007ac45f588e59c03ef45f10933
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92125057"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Usar Azure Data Box ou Azure Data Box Heavy para enviar dados para a camada de blob de armazenamento do Azure apropriada

O Azure Data Box move grandes quantidades de dados para o Azure enviando a você um dispositivo de armazenamento proprietário. Você preenche o dispositivo com os dados e retorna-o. Os dados do Data Box são carregados para uma camada padrão associada à conta de armazenamento. Então você pode mover os dados para outra camada de armazenamento.

Este artigo descreve como os dados são carregados pelo Data Box podem ser movidos para uma camada de blob quente, fria ou de arquivos. Este artigo se aplica a Azure Data Box e Azure Data Box Heavy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Escolher a camada de armazenamento correta para seus dados

O armazenamento do Azure permite três níveis diferentes para armazenar os dados da maneira mais econômica – quente, frio ou arquivos. A camada de armazenamento quente é otimizada para armazenar dados acessados com frequência. O armazenamento a quente tem custos de armazenamento mais altos do que o armazenamento Cool e Archive, mas os custos de acesso mais baixos.

A camada de armazenamento frio é para dados acessados com pouca frequência que precisam ser armazenados por pelo menos 30 dias. O custo de armazenamento para a camada fria é inferior ao da camada de armazenamento quente, mas os encargos de acesso a dados são altos em comparação aos da camada Quente.

A camada de Arquivos do Azure fica offline e oferece os custos de armazenamento mais baixos, mas também os custos de acesso mais altos. Essa camada destina-se a dados que permanecem no armazenamento de arquivos por pelo menos 180 dias. Para obter detalhes sobre cada uma dessas camadas e o modelo de preços, acesse [Comparação das camadas de armazenamento](../storage/blobs/storage-blob-storage-tiers.md).

Os dados do Data Box ou Data Box Heavy são carregados em uma camada de armazenamento associada à conta de armazenamento. Quando você cria uma conta de armazenamento, pode especificar a camada de acesso como quente ou fria. Dependendo do padrão de acesso de sua carga de trabalho e do custo, você poderá mover esses dados da camada padrão para outra camada de armazenamento.

Você só pode ordenar os dados de armazenamento de objetos nas contas de Armazenamento de Blobs ou GPv2 (Uso Geral v2). Contas de Uso geral v1 (GPv1) não dão suporte a camadas. Para escolher a camada de armazenamento correta para seus dados, examine as considerações detalhadas no [armazenamento de BLOBs do Azure: camadas de armazenamento Premium, quentes, frias e de arquivo](../storage/blobs/storage-blob-storage-tiers.md).

## <a name="set-a-default-blob-tier"></a>Defina uma camada de blob padrão

A camada de blob padrão é especificada quando a conta de armazenamento é criada no portal do Azure. Depois que um tipo de armazenamento é selecionado como o Armazenamento de Blobs ou GPv2, o atributo da camada de acesso pode ser especificado. Por padrão, a camada Quente é selecionada.

As camadas não poderão ser especificadas se você estiver tentando criar uma nova conta ao ordenar uma Data Box ou Data Box Heavy. Depois que a conta for criada, você poderá modificar a conta no portal para definir a camada de acesso padrão.

Como alternativa, você cria uma conta de armazenamento primeiro com o atributo da camada de acesso especificado. Ao criar o Data Box ou Data Box Heavy ordem, selecione a conta de armazenamento existente. Para obter mais informações sobre como definir a camada de blob padrão durante a criação da conta de armazenamento, confira [Criar uma conta de armazenamento no portal do Azure](../storage/common/storage-account-create.md?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Mover dados para uma camada não padrão

Depois que os dados do dispositivo Data Box forem carregados para a camada padrão, talvez você queira mover os dados para uma camada não padrão. Há duas maneiras de mover esses dados para uma camada não padrão.

- **Gerenciamento de ciclo de vida do Armazenamento de Blobs do Azure** – você pode usar uma abordagem baseada em política para automaticamente colocar dados em camadas ou expirá-los no final do ciclo de vida. Para obter mais informações, confira [Gerenciando o ciclo de vida do Armazenamento de Blobs do Azure](../storage/blobs/storage-lifecycle-management-concepts.md).
- **Script** – você pode usar uma abordagem com script por meio do Azure PowerShell para habilitar a camada no nível do blob. Você pode chamar a operação `SetBlobTier` para definir a camada no blob.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Usar o Azure PowerShell para definir a camada de blob

As etapas a seguir descrevem como você pode definir a camada de blob como Arquivos usando um script do Azure PowerShell.

1. Abra uma sessão elevada do Windows PowerShell. Verifique se você está executando o PowerShell 5.0 ou posterior. Tipo:

   `$PSVersionTable.PSVersion`     

2. Entre no Azure PowerShell. 

   `Login-AzAccount`  

3. Defina as variáveis para a conta de armazenamento, a chave de acesso, o contêiner e o contexto de armazenamento.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Obtenha todos os blobs no contêiner.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Defina a camada de todos os blobs no contêiner como Arquivos.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Um exemplo de saída é mostrado abaixo:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Se você desejar que os dados sejam arquivados na ingestão, defina a camada de conta padrão como quente. Se a camada padrão for Fria, haverá uma penalidade de exclusão antecipada de 30 dias se os dados forem movidos para Arquivos imediatamente.

## <a name="next-steps"></a>Próximas etapas

-  Aprenda a lidar com [cenários de camadas de dados comuns com as regras de política de ciclo de vida](../storage/blobs/storage-lifecycle-management-concepts.md#examples)