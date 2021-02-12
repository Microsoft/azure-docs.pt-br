---
title: Executar uma restauração pontual em dados de blob de blocos
titleSuffix: Azure Storage
description: Saiba como usar a restauração pontual para restaurar um conjunto de blobs de blocos para seu estado anterior em um determinado momento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/29/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: b62e341d35a4ff7fd5a7ddd6d9f19b138aaf0aa9
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071640"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Executar uma restauração pontual em dados de blob de blocos

Você pode usar a restauração pontual para restaurar um ou mais conjuntos de blobs de blocos para um estado anterior. Este artigo descreve como habilitar a restauração pontual para uma conta de armazenamento e como executar uma operação de restauração.

Para saber mais sobre a restauração pontual, consulte [restauração pontual para BLOBs de blocos](point-in-time-restore-overview.md).

> [!CAUTION]
> A restauração pontual dá suporte a operações de restauração somente em blobs de blocos. Não é possível restaurar operações em contêineres. Se você excluir um contêiner da conta de armazenamento chamando a operação [excluir contêiner](/rest/api/storageservices/delete-container) , esse contêiner não poderá ser restaurado com uma operação de restauração. Em vez de excluir um contêiner inteiro, exclua BLOBs individuais se você quiser restaurá-los mais tarde. Além disso, a Microsoft recomenda habilitar a exclusão reversível para contêineres e BLOBs para proteger contra a exclusão acidental. Para obter mais informações, consulte [exclusão reversível para contêineres (visualização)](soft-delete-container-overview.md) e [exclusão reversível para BLOBs](soft-delete-blob-overview.md).

## <a name="enable-and-configure-point-in-time-restore"></a>Habilitar e configurar a restauração pontual

Antes de habilitar e configurar a restauração pontual, habilite seus pré-requisitos para a conta de armazenamento: exclusão reversível, feed de alteração e controle de versão de BLOB. Para obter mais informações sobre esses recursos, confira estes artigos:

- [Habilitar exclusão reversível para blobs](./soft-delete-blob-enable.md)
- [Habilitar e desabilitar o feed de alterações](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Habilitar e gerenciar o controle de versão de blob](versioning-enable.md)

> [!IMPORTANT]
> Habilitar a exclusão reversível, o feed de alterações e o controle de versão de blob pode resultar em encargos adicionais. Para obter mais informações, consulte [exclusão reversível para BLOBs](soft-delete-blob-overview.md), [suporte ao feed de alterações no armazenamento de BLOBs do Azure](storage-blob-change-feed.md)e [controle de versão de blob](versioning-overview.md).

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para configurar a restauração pontual com o portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure.
1. Em **configurações**, escolha **proteção de dados**.
1. Selecione **ativar a restauração pontual** . Quando você seleciona essa opção, a exclusão reversível para BLOBs, controle de versão e feed de alteração também são habilitadas.
1. Defina o ponto de restauração máximo para a restauração pontual, em dias. Esse número deve ser pelo menos um dia menor que o período de retenção especificado para exclusão reversível do blob.
1. Salve suas alterações.

A imagem a seguir mostra uma conta de armazenamento configurada para a restauração pontual com um ponto de restauração de sete dias atrás e um período de retenção para exclusão reversível de blob de 14 dias.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Captura de tela mostrando como configurar a restauração pontual no portal do Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para configurar a restauração pontual com o PowerShell, primeiro instale o módulo [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) versão 2.6.0 ou posterior. Em seguida, chame o comando [Enable-AzStorageBlobRestorePolicy](/powershell/module/az.storage/enable-azstorageblobrestorepolicy) para habilitar a restauração pontual para a conta de armazenamento.

O exemplo a seguir habilita a exclusão reversível e define o período de retenção de exclusão reversível, habilita o feed de alterações e o controle de versão e, em seguida, habilita a restauração pontual. Lembre-se de substituir os valores entre colchetes angulares pelos seus próprios valores quando executar o exemplo:

```powershell
# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable blob soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para configurar a restauração pontual com o CLI do Azure, primeiro instale o CLI do Azure versão 2.2.0 ou posterior. Em seguida, chame o comando [AZ Storage Account blob-Service-Properties Update](/cli/azure/ext/storage-blob-preview/storage/account/blob-service-properties#ext_storage_blob_preview_az_storage_account_blob_service_properties_update) para habilitar a restauração pontual e as outras configurações de proteção de dados necessárias para a conta de armazenamento.

O exemplo a seguir habilita a exclusão reversível e define o período de retenção de exclusão reversível para 14 dias, habilita o feed de alterações e o controle de versão e habilita a restauração pontual com um período de restauração de 7 dias. Lembre-se de substituir os valores entre colchetes angulares pelos seus próprios valores quando executar o exemplo:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --enable-delete-retention true \
    --delete-retention-days 14 \
    --enable-versioning true \
    --enable-change-feed true \
    --enable-restore-policy true \
    --restore-days 7
```

---

## <a name="choose-a-restore-point"></a>Escolher um ponto de restauração

O ponto de restauração é a data e a hora em que os dados são restaurados. O armazenamento do Azure sempre usa um valor de data/hora UTC como o ponto de restauração. No entanto, o portal do Azure permite que você especifique o ponto de restauração na hora local e, em seguida, converte esse valor de data/hora em um valor de data/hora UTC para executar a operação de restauração.

Ao executar uma operação de restauração com o PowerShell ou CLI do Azure, você deve especificar o ponto de restauração como um valor de data/hora UTC. Se o ponto de restauração for especificado com um valor de hora local em vez de um valor de hora UTC, a operação de restauração ainda poderá se comportar conforme o esperado em alguns casos. Por exemplo, se a hora local for UTC menos cinco horas, a especificação de um valor de hora local resultará em um ponto de restauração que é de cinco horas antes do valor que você forneceu. Se nenhuma alteração foi feita nos dados no intervalo a ser restaurado durante esse período de cinco horas, a operação de restauração produzirá os mesmos resultados, independentemente de qual valor de tempo foi fornecido. É recomendável especificar uma hora UTC para o ponto de restauração para evitar resultados inesperados.

## <a name="perform-a-restore-operation"></a>Realizar uma operação de restauração

Você pode restaurar todos os contêineres na conta de armazenamento ou pode restaurar um intervalo de BLOBs em um ou mais contêineres. Um intervalo de BLOBs é definido modo lexicográfico, ou seja, na ordem de dicionário. Há suporte para até dez intervalos de lexicográfica por operação de restauração. O início do intervalo é inclusivo e o final do intervalo é exclusivo.

O padrão de contêiner especificado para o intervalo inicial e o intervalo final deve incluir um mínimo de três caracteres. A barra (/) que é usada para separar um nome de contêiner de um nome de blob não conta para esse mínimo.

Não há suporte para caracteres curinga em um intervalo de lexicográfica. Quaisquer caracteres curinga são tratados como caracteres padrão.

Você pode restaurar blobs nos contêineres `$root` e `$web` especificando-os explicitamente em um intervalo passado para uma operação de restauração. Os contêineres `$root` e `$web` são restaurados apenas se forem especificados explicitamente. Outros contêineres do sistema não podem ser restaurados.

Somente os blobs de blocos são restaurados. Blobs de páginas e blobs de acréscimo não são incluídos em uma operação de restauração. Para obter mais informações sobre as limitações relacionadas a blobs de acréscimo, consulte [restauração pontual para BLOBs de blocos](point-in-time-restore-overview.md).

> [!IMPORTANT]
> Ao executar uma operação de restauração, o armazenamento do Azure bloqueia operações de dados nos BLOBs nos intervalos que estão sendo restaurados durante a operação. As operações de leitura, gravação e exclusão são bloqueadas no local principal. Por esse motivo, as operações como os contêineres de listagem no portal do Azure podem não ser executadas conforme o esperado enquanto a operação de restauração está em andamento.
>
> As operações de leitura do local secundário podem continuar durante a operação de restauração se a conta de armazenamento for replicada geograficamente.
>
> O tempo necessário para restaurar um conjunto de dados é baseado no número de operações de gravação e exclusão feitas durante o período de restauração. Por exemplo, uma conta com 1 milhão objetos com 3.000 objetos adicionados por dia e 1.000 objetos excluídos por dia exigirá aproximadamente duas horas para restaurar para um ponto de 30 dias no passado. Um período de retenção e uma restauração mais de 90 dias no passado não seriam recomendados para uma conta com essa taxa de alteração.

### <a name="restore-all-containers-in-the-account"></a>Restaurar todos os contêineres na conta

Você pode restaurar todos os contêineres na conta de armazenamento para retorná-los ao estado anterior em um determinado momento.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para restaurar todos os contêineres e blobs na conta de armazenamento com o portal do Azure, siga estas etapas:

1. Navegue até a lista de contêineres para sua conta de armazenamento.
1. Na barra de ferramentas, escolha **restaurar contêineres** e **restaurar tudo**.
1. No painel **restaurar todos os contêineres** , especifique o ponto de restauração fornecendo uma data e hora.
1. Confirme que você deseja continuar marcando a caixa.
1. Selecione **restaurar** para iniciar a operação de restauração.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Captura de tela mostrando como restaurar todos os contêineres para um ponto de restauração especificado":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para restaurar todos os contêineres e blobs na conta de armazenamento com o PowerShell, chame o comando **Restore-AzStorageBlobRange** e forneça o ponto de restauração como um valor de data/hora UTC. Por padrão, o comando **Restore-AzStorageBlobRange** é executado de forma assíncrona e retorna um objeto do tipo **PSBlobRestoreStatus** que você pode usar para verificar o status da operação de restauração.

O exemplo a seguir restaura de forma assíncrona os contêineres na conta de armazenamento para seu estado 12 horas antes do momento atual e verifica algumas das propriedades da operação de restauração:

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).ToUniversalTime().AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

Para executar a operação de restauração de forma síncrona, inclua o parâmetro **-WaitForComplete** no comando. Quando o parâmetro **-WaitForComplete** estiver presente, o PowerShell exibirá uma mensagem que inclui a ID de restauração da operação e, em seguida, bloqueará a execução até que a operação de restauração seja concluída. Tenha em mente que o período de tempo exigido por uma operação de restauração depende da quantidade de dados a serem restaurados e uma grande operação de restauração pode levar até uma hora para ser concluída.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para restaurar todos os contêineres e blobs na conta de armazenamento com CLI do Azure, chame o comando [AZ Storage blob Restore](/cli/azure/storage/blob#az_storage_blob_restore) e forneça o ponto de restauração como um valor de data/hora UTC.

O exemplo a seguir restaura de forma assíncrona todos os contêineres na conta de armazenamento para seu estado 12 horas antes de uma data e hora especificadas. Para verificar o status da operação de restauração, chame [AZ Storage Account show](/cli/azure/storage/account#az_storage_account_show):

```azurecli
az storage blob restore \
    --resource-group <resource_group> \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --no-wait
```

Para verificar as propriedades de uma operação de restauração, chame [AZ Storage Account show](/cli/azure/storage/account#az_storage_account_show) e expanda a propriedade **blobRestoreStatus** . O exemplo a seguir mostra como verificar a propriedade **status** .

```azurecli
az storage account show \
    --name <storage-account> \
    --resource-group <resource_group> \ 
    --expand blobRestoreStatus \
    --query blobRestoreStatus.status \
    --output tsv
```

Para executar o comando **AZ Storage blob Restore** de forma síncrona e bloquear a execução até que a operação de restauração seja concluída, omita o `--no-wait` parâmetro.

---

### <a name="restore-ranges-of-block-blobs"></a>Restaurar intervalos de blobs de blocos

Você pode restaurar um ou mais intervalos de lexicográfica de BLOBs dentro de um único contêiner ou em vários contêineres para retornar esses BLOBs para seu estado anterior em um determinado momento.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para restaurar um intervalo de BLOBs em um ou mais contêineres com o portal do Azure, siga estas etapas:

1. Navegue até a lista de contêineres para sua conta de armazenamento.
1. Selecione o contêiner ou contêineres a serem restaurados.
1. Na barra de ferramentas, escolha **restaurar contêineres** e **restaurar selecionado**.
1. No painel **restaurar contêineres selecionados** , especifique o ponto de restauração fornecendo uma data e hora.
1. Especifique os intervalos a serem restaurados. Use uma barra (/) para delinear o nome do contêiner do prefixo do blob.
1. Por padrão, o painel **restaurar contêineres selecionados** especifica um intervalo que inclui todos os BLOBs no contêiner. Exclua esse intervalo se você não quiser restaurar todo o contêiner. O intervalo padrão é mostrado na imagem a seguir.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Captura de tela mostrando o intervalo de BLOBs padrão a ser excluído antes de especificar o intervalo personalizado":::

1. Confirme que você deseja continuar marcando a caixa.
1. Selecione **restaurar** para iniciar a operação de restauração.

A imagem a seguir mostra uma operação de restauração em um conjunto de intervalos.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Captura de tela mostrando como restaurar intervalos de BLOBs em um ou mais contêineres":::

A operação de restauração mostrada na imagem executa as seguintes ações:

- Restaura o conteúdo completo de *Container1*.
- Restaura os BLOBs no intervalo de lexicográfica *blob1* por meio de *blob5* no *container2*. Esse intervalo restaura os BLOBs com nomes como *blob1*, *blob11*, *blob100*, *blob2* e assim por diante. Como o final do intervalo é exclusivo, ele restaura os BLOBs cujos nomes começam com *blob4*, mas não restaura os BLOBs cujos nomes começam com *blob5*.
- Restaura todos os BLOBs em *container3* e *Container4*. Como o final do intervalo é exclusivo, esse intervalo não restaura *container5*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para restaurar um único intervalo de BLOBs, chame o comando **Restore-AzStorageBlobRange** e especifique um intervalo lexicográfica de nomes de contêiner e BLOB para o `-BlobRestoreRange` parâmetro. Por exemplo, para restaurar os BLOBs em um único contêiner chamado *Container1*, você pode especificar um intervalo que comece com *Container1* e termine com *container2*. Não há nenhum requisito para os contêineres nomeados nos intervalos de início e de término existirem. Como o final do intervalo é exclusivo, mesmo que a conta de armazenamento inclua um contêiner chamado *container2*, somente o contêiner chamado *Container1* será restaurado:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Para especificar um subconjunto de BLOBs em um contêiner a ser restaurado, use uma barra (/) para separar o nome do contêiner do padrão de prefixo de BLOB. Por exemplo, o intervalo a seguir seleciona blobs em um único contêiner cujos nomes começam com as letras *d* até *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Em seguida, forneça o intervalo para o comando **Restore-AzStorageBlobRange** . Especifique o ponto de restauração fornecendo um valor UTC **DateTime** para o parâmetro `-TimeToRestore`. O exemplo a seguir restaura os blobs no intervalo especificado para seu estado 3 dias antes de agora:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Por padrão, o comando **Restore-AzStorageBlobRange** é executado de forma assíncrona. Quando você inicia uma operação de restauração de forma assíncrona, o PowerShell exibe imediatamente uma tabela de propriedades para a operação:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Para restaurar vários intervalos de blobs de blocos, especifique uma matriz de intervalos para o parâmetro `-BlobRestoreRange`. O exemplo a seguir especifica dois intervalos para restaurar o conteúdo completo de *Container1* e *Container4* para seu estado 24 horas atrás e salva o resultado em uma variável:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

Para executar a operação de restauração de forma síncrona e bloquear a execução até que ela seja concluída, inclua o parâmetro **-WaitForComplete** no comando.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para restaurar um intervalo de BLOBs, chame o comando [AZ Storage blob Restore](/cli/azure/storage/blob#az_storage_blob_restore) e especifique um intervalo lexicográfica de contêineres e nomes de BLOB para o `--blob-range` parâmetro. Para especificar vários intervalos, forneça o `--blob-range` parâmetro para cada intervalo distinto.

Por exemplo, para restaurar os BLOBs em um único contêiner chamado *Container1*, você pode especificar um intervalo que comece com *Container1* e termine com *container2*. Não há nenhum requisito para os contêineres nomeados nos intervalos de início e de término existirem. Como o final do intervalo é exclusivo, mesmo que a conta de armazenamento inclua um contêiner chamado *container2*, somente o contêiner chamado *Container1* será restaurado.

Para especificar um subconjunto de BLOBs em um contêiner a ser restaurado, use uma barra (/) para separar o nome do contêiner do padrão de prefixo de BLOB. O exemplo mostrado abaixo restaura de forma assíncrona um intervalo de BLOBs em um contêiner cujos nomes começam com as `d` letras `f` .

```azurecli
az storage blob restore \
    --account-name <storage-account> \
    --time-to-restore 2021-01-14T06:31:22Z \
    --blob-range container1 container2
    --blob-range container3/d container3/g
    --no-wait
```

Para executar o comando **AZ Storage blob Restore** de forma síncrona e bloquear a execução até que a operação de restauração seja concluída, omita o `--no-wait` parâmetro.

---

## <a name="next-steps"></a>Próximas etapas

- [Restauração pontual para BLOBs de blocos](point-in-time-restore-overview.md)
- [Exclusão reversível](./soft-delete-blob-overview.md)
- [Feed de alterações](storage-blob-change-feed.md)
- [Controle de versão de BLOB](versioning-overview.md)