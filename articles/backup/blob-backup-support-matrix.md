---
title: Matriz de suporte para backup de BLOBs do Azure
description: Fornece um resumo das configurações de suporte e limitações ao fazer backup de BLOBs do Azure (em versão prévia)
ms.topic: conceptual
ms.date: 02/16/2021
ms.custom: references_regions
ms.openlocfilehash: 12d289fdc3f84e7cbb3489a3ece283179e51772c
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561892"
---
# <a name="support-matrix-for-azure-blobs-backup-in-preview"></a>Matriz de suporte para backup de BLOBs do Azure (em versão prévia)

Este artigo resume a disponibilidade regional, os cenários com suporte e as limitações de backup operacional de BLOBs.

## <a name="supported-regions"></a>Regiões com suporte

O backup operacional para BLOBs está disponível atualmente nas seguintes regiões: Austrália Central, leste da Austrália, sul do Brasil, central do Canadá, Índia central, EUA Central, Ásia Oriental, leste dos EUA, leste dos EUA 2, Centro-oeste da Alemanha, leste do Japão, oeste do Japão, Coreia central, Coreia do Sul, Europa Setentrional, Sul EUA Central, Sul Ásia Oriental, Norte da Suíça, Norte dos EAU, Sul do Reino Unido, Oeste do Reino Unido, West EUA Central , Oeste dos EUA, oeste dos EUA 2

## <a name="limitations"></a>Limitações

O backup operacional de BLOBs usa restauração pontual de BLOB, controle de versão de BLOB, exclusão reversível para BLOBs, feed de alterações para BLOBs e exclusão de bloqueio para fornecer uma solução de backup local. Portanto, as limitações que se aplicam a esses recursos também se aplicam ao backup operacional.

**Cenários com suporte:** O backup operacional dá suporte a blobs de bloco somente nas contas de armazenamento de uso geral v2 padrão. Portanto, não há suporte para contas ADLS Gen2. Além disso, todos os blobs de páginas, blobs de acréscimo e blobs Premium em sua conta de armazenamento não serão restaurados e somente blobs de blocos serão restaurados.

**Outras limitações:**

- Se você excluiu um contêiner durante o período de retenção, esse contêiner não será restaurado com a operação de restauração pontual. Se você tentar restaurar um intervalo de BLOBs que inclui BLOBs em um contêiner excluído, a operação de restauração pontual falhará. Para obter mais informações sobre como proteger contêineres da exclusão, consulte [exclusão reversível para contêineres (versão prévia)](../storage/blobs/soft-delete-container-overview.md).
- Se um blob tiver se movido entre as camadas quente e fria no período entre o momento atual e o ponto de restauração, o blob será restaurado para sua camada anterior. Não há suporte para a restauração de blobs de blocos na camada de arquivo. Por exemplo, se um blob na camada quente foi movido para a camada de arquivamento dois dias atrás e uma operação de restauração restaura a um ponto três dias atrás, o BLOB não é restaurado para a camada quente. Para restaurar um blob arquivado, primeiro mova-o para fora da camada de arquivo morto. Para obter mais informações, consulte [dados de blob reidratar da camada de arquivo morto](../storage/blobs/storage-blob-rehydration.md).
- Um bloco que foi carregado por meio do [bloco Put](/rest/api/storageservices/put-block) ou [Put bloco da URL](/rest/api/storageservices/put-block-from-url), mas não confirmado via [lista de blocos Put](/rest/api/storageservices/put-block-list), não faz parte de um blob e, portanto, não é restaurado como parte de uma operação de restauração.
- Um blob com uma concessão ativa não pode ser restaurado. Se um blob com uma concessão ativa estiver incluído no intervalo de BLOBs a serem restaurados, a operação de restauração falhará automaticamente. Interrompa todas as concessões ativas antes de iniciar a operação de restauração.
- Os instantâneos não são criados ou excluídos como parte de uma operação de restauração. Somente o blob de base é restaurado para seu estado anterior.

## <a name="next-steps"></a>Próximas etapas

- [Visão geral do backup operacional para BLOBs do Azure (em versão prévia)](blob-backup-overview.md)