---
title: "Migração entre regiões do Azure Data Lake Store | Microsoft Docs"
description: "Saiba mais sobre a migração entre regiões do Azure Data Lake Store."
services: data-lake-store
documentationcenter: 
author: swums
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/27/2017
ms.author: stewu
ms.openlocfilehash: b04cca6e551a15a31bbebc4932ea05dd39e8e916
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-data-lake-store-across-regions"></a>Migrar o Data Lake Store entre regiões

Com o Azure Data Lake Store sendo disponibilizado em novas regiões, você pode optar por executar uma migração única para aproveitar o que a nova região tem a oferecer. Saiba o que considerar ao planejar e concluir a migração.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Para obter mais informações, consulte [Criar sua conta gratuita do Azure hoje mesmo](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Data Lake Store em duas regiões diferentes**. Para obter mais informações, consulte [Introdução ao Azure Data Lake Store](data-lake-store-get-started-portal.md).
* **Azure Data Factory**. Para obter mais informações, consulte [Introdução ao Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Considerações sobre a migração

Primeiro, identifique a estratégia de migração que funciona melhor para seu aplicativo, que grava, lê ou processa dados no Data Lake Store. Ao escolher uma estratégia, considere os requisitos de disponibilidade de seu aplicativo e o tempo de inatividade que ocorre durante a migração. Por exemplo, a abordagem mais simples poderia ser o uso do modelo de migração na nuvem “lift-and-shift”. Nessa abordagem, você pausa o aplicativo em sua região existente, enquanto todos os seus dados são copiados para a nova região. Quando o processo de cópia é concluído, você retoma o aplicativo na nova região e, em seguida, exclui a conta antiga do Data Lake Store. O tempo de inatividade durante a migração é necessário.

Para reduzir o tempo de inatividade, é possível começar imediatamente a ingerir novos dados na nova região. Quando você tiver o mínimo de dados necessário, execute o aplicativo na nova região. Em segundo plano, continue copiando os dados mais antigos da conta existente do Data Lake Store para a nova conta do Data Lake Store na nova região. Ao usar essa abordagem, é possível mudar para a nova região com tempo de inatividade mínimo. Depois de copiar todos os dados mais antigos, exclua a conta antiga do Data Lake Store.

Outros detalhes importantes a considerar no planejamento da migração são:

* **Volume de dados**. O volume de dados (em gigabytes, o número de arquivos e pastas e assim por diante) afeta o tempo e os recursos necessários para a migração.

* **Nome da conta do Data Lake Store**. O novo nome da conta na nova região deve ser global exclusivo. Por exemplo, o nome da conta antiga do Data Lake Store no Leste dos EUA 2 pode ser contosoeastus2.azuredatalakestore.net. Você poderá nomear sua nova conta do Data Lake Store no Norte da Europa como contosonortheu.azuredatalakestore.net.

* **Ferramentas**. Recomendamos o uso da [Atividade de Cópia do Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) para copiar arquivos do Data Lake Store. O Data Factory dá suporte à movimentação de dados com alto desempenho e confiabilidade. Lembre-se de que o Data Factory copia somente a hierarquia de pastas e o conteúdo dos arquivos. É necessário aplicar manualmente as ACLs (listas de controle de acesso) usadas na conta antiga à nova conta. Para obter mais informações, incluindo as metas de desempenho para cenários mais favoráveis, consulte o [guia de ajuste e desempenho da Atividade de Cópia](../data-factory/copy-activity-performance.md). Se desejar que os dados sejam copiados mais rapidamente, talvez você precise usar mais Unidades de Movimentação de Dados na Nuvem. Algumas outras ferramentas, como AdlCopy, não dão suporte à cópia de dados entre regiões.  

* **Encargos de largura de banda**. Os [encargos de largura de banda](https://azure.microsoft.com/en-us/pricing/details/bandwidth/) se aplicam, pois os dados são transferidos para fora de uma região do Azure.

* **ACLs nos dados**. Proteja seus dados na nova região aplicando ACLs a arquivos e pastas. Para obter mais informações, consulte [Protegendo dados armazenados no Azure Data Lake Store](data-lake-store-secure-data.md). Recomendamos o uso da migração para atualizar e ajustar as ACLs. Talvez você deseje usar configurações semelhantes para as configurações atuais. É possível exibir as ACLs aplicadas a um arquivo usando o portal do Azure, [cmdlets do PowerShell](/powershell/module/azurerm.datalakestore/get-azurermdatalakestoreitempermission) ou SDKs.  

* **Localização dos serviços de análise**. Para obter o melhor desempenho, os serviços de análise, como o Azure Data Lake Analytics ou o Azure HDInsight, devem estar na mesma região dos dados.  

## <a name="next-steps"></a>Próximas etapas
* [Visão geral do Repositório Azure Data Lake](data-lake-store-overview.md)
