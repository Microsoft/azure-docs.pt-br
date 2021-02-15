---
title: Verificação da propriedade Horário da Última Sincronização de uma conta de armazenamento
titleSuffix: Azure Storage
description: Saiba como verificar a propriedade Horário da Última Sincronização de uma conta de armazenamento de replicação geográfica. A propriedade Hora da última sincronização indica a última vez em que as gravações da região primária foram gravadas com êxito na região secundária.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 242700c05053aa9d07e3a561a21986c8451a46c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91612437"
---
# <a name="check-the-last-sync-time-property-for-a-storage-account"></a>Verificação da propriedade Horário da Última Sincronização de uma conta de armazenamento

Quando configurar uma conta de armazenamento, especifique que seus dados sejam copiados para uma região secundária que esteja a centenas de quilômetros da região primária. A replicação geográfica oferece durabilidade para seus dados em caso de uma interrupção significativa na região primária, como um desastre natural. Se habilitar também o acesso de leitura para a região secundária, seus dados ficam disponíveis para operações de leitura se a região primária ficar indisponível. Projete seu aplicativo para alternar diretamente para a leitura da região secundária se a região primária não estiver respondendo.

O armazenamento com redundância geográfica (GRS) e o armazenamento com redundância de zona geográfica (GZRS) replicam seus dados de forma assíncrona para uma região secundária. Para obter acesso de leitura para o local secundário, habilite o armazenamento com redundância geográfica com acesso de leitura (RA-GRS) ou o armazenamento com redundância de zona com acesso de leitura (RA-GZRS). Para obter mais informações sobre as várias opções de redundância oferecidas pelo Armazenamento do Azure, veja [Redundância no Armazenamento do Azure](storage-redundancy.md).

Este artigo descreve como verificar a propriedade **Hora da Última Sincronização** da sua conta de armazenamento para que você possa avaliar qualquer discrepância entre as regiões primárias e secundárias.

## <a name="about-the-last-sync-time-property"></a>Sobre a propriedade Hora da Última Sincronização

Como a replicação geográfica é assíncrona, é possível que os dados gravados na região primária ainda não tenham sido gravados na região secundária no momento em que ocorre uma interrupção. A propriedade **Hora da Última Sincronização** indica a última vez em que os dados da região primária foram gravados com êxito na região secundária. Todas as gravações feitas na região primária antes da hora da última sincronização estão disponíveis para leitura no local secundário. As gravações feitas na região primária após a hora da última sincronização podem ou não estarem disponíveis para leituras.

A propriedade **Hora da Última Sincronização** é um valor de data/hora em GMT.

## <a name="get-the-last-sync-time-property"></a>Obtenção da propriedade Hora da Última Sincronização

Você pode usar o PowerShell ou a CLI do Azure para recuperar o valor da propriedade **Hora da Última Sincronização**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para obter a hora da última sincronização para a conta de armazenamento com o PowerShell, instale a versão 1.11.0 ou posterior do módulo [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) . Em seguida, verifique a propriedade **GeoReplicationStats.LastSyncTime** da conta de armazenamento. Lembre-se de substituir os valores dos espaços reservados por seus próprios valores:

```powershell
$lastSyncTime = $(Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account> `
    -IncludeGeoReplicationStats).GeoReplicationStats.LastSyncTime
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para obter a hora da última sincronização para a conta de armazenamento com a CLI do Azure, verifique a propriedade **geoReplicationStats.lastSyncTime** da conta de armazenamento. Use o parâmetro `--expand` para retornar valores para as propriedades aninhadas em **geoReplicationStats**. Lembre-se de substituir os valores dos espaços reservados por seus próprios valores:

```azurecli-interactive
$lastSyncTime=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --expand geoReplicationStats \
    --query geoReplicationStats.lastSyncTime \
    --output tsv)
```

---

## <a name="see-also"></a>Confira também

- [Redundância do Armazenamento do Azure](storage-redundancy.md)
- [Alteração da opção de redundância de uma conta de armazenamento](redundancy-migration.md)
- [Uso da redundância geográfica para criar aplicativos altamente disponíveis](geo-redundant-design.md)
