---
title: Acesso de vários protocolos em Azure Data Lake Storage | Microsoft Docs
description: Use APIs de BLOB e aplicativos que usam APIs de blob com Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e3997fc215637175165402a926bffc6ac8d02771
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77914851"
---
# <a name="multi-protocol-access-on-azure-data-lake-storage"></a>Acesso de vários protocolos no Azure Data Lake Storage

As APIs de blob agora funcionam com contas que têm um namespace hierárquico. Isso desbloqueia o ecossistema de ferramentas, aplicativos e serviços, bem como vários recursos de armazenamento de BLOBs para contas que têm um namespace hierárquico.

Até recentemente, talvez você precise manter soluções de armazenamento separadas para armazenamento de objetos e armazenamento de análise. Isso ocorre porque Azure Data Lake Storage Gen2 tinha suporte limitado a ecossistema. Ele também tinha acesso limitado aos recursos do serviço BLOB, como o log de diagnóstico. Uma solução de armazenamento fragmentada é difícil de manter, pois você precisa mover dados entre contas para realizar vários cenários. Você não precisa mais fazer isso.

Com o acesso de vários protocolos no Data Lake Storage, você pode trabalhar com seus dados usando o ecossistema de ferramentas, aplicativos e serviços. Isso também inclui ferramentas e aplicativos de terceiros. Você pode apontar para as contas que têm um namespace hierárquico sem precisar modificá-las. Esses aplicativos funcionam *como estão* , mesmo se chamarem APIs de BLOB, porque as APIs de blob podem agora operar em dados em contas que têm um namespace hierárquico.

Os recursos de armazenamento de BLOBs, como [log de diagnóstico](../common/storage-analytics-logging.md), [camadas de acesso](storage-blob-storage-tiers.md)e [políticas de gerenciamento do ciclo de vida do armazenamento de BLOBs](storage-lifecycle-management-concepts.md) , agora funcionam com contas que têm um namespace hierárquico. Portanto, você pode habilitar namespaces hierárquicos em suas contas de armazenamento de BLOBs sem perder o acesso a esses recursos importantes. 

> [!NOTE]
> O acesso de vários protocolos no Data Lake Storage está geralmente disponível e está disponível em todas as regiões. Alguns serviços do Azure ou recursos de armazenamento de BLOBs habilitados pelo acesso de vários protocolos permanecem em versão prévia.  Esses artigos resumem o suporte atual para recursos de armazenamento de BLOBs e integrações de serviço do Azure. 
>
> [Recursos de armazenamento de BLOBs disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
>
>[Serviços do Azure compatíveis com o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)

## <a name="how-multi-protocol-access-on-data-lake-storage-works"></a>Como funciona o acesso de vários protocolos no armazenamento do data Lake

APIs de BLOB e APIs de Data Lake Storage Gen2 podem operar nos mesmos dados em contas de armazenamento que têm um namespace hierárquico. Data Lake Storage Gen2 roteia as APIs de blob por meio do namespace hierárquico para que você possa obter os benefícios das operações de diretório de primeira classe e das ACLs (listas de controle de acesso) compatíveis com POSIX. 

![Acesso de vários protocolos em Data Lake Storage conceitual](./media/data-lake-storage-interop/interop-concept.png) 

As ferramentas e os aplicativos existentes que usam a API de blob obterão esses benefícios automaticamente. Os desenvolvedores não precisarão modificá-los. Data Lake Storage Gen2 aplica de forma consistente ACLs de nível de arquivo e de diretório, independentemente do protocolo que as ferramentas e os aplicativos usam para acessar os dados. 

## <a name="see-also"></a>Confira também

- [Recursos de armazenamento de BLOBs disponíveis no Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md)
- [Serviços do Azure compatíveis com o Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md)
- [Plataformas de software livre compatíveis com o Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md)
- [Problemas conhecidos com o Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)




