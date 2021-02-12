---
title: Migração de região cruzada Gen1 do armazenamento de dados do Azure | Microsoft Docs
description: Saiba o que considerar ao planejar e concluir uma migração para Azure Data Lake Storage Gen1 à medida que ela se tornar disponível em novas regiões.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: amitkul
editor: swums
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: twooley
ms.openlocfilehash: c6520036f3ddb8799025129391330268b0604886
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723799"
---
# <a name="migrate-azure-data-lake-storage-gen1-across-regions"></a>Migrar o Gen1 do Armazenamento de Dados do Azure pelas regiões

À medida que o Armazenamento de Dados do Windows Azure Gen1 se torna disponível em novas regiões, você pode optar por fazer uma migração única para aproveitar a nova região. Saiba o que considerar ao planejar e concluir a migração.

## <a name="prerequisites"></a>Pré-requisitos

* **Uma assinatura do Azure**. Para obter mais informações, consulte [Criar sua conta gratuita do Azure hoje mesmo](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Data Lake armazenamento Gen1 em duas regiões diferentes**. Para obter mais informações, consulte [Introdução ao Azure Data Lake armazenamento Gen1](data-lake-store-get-started-portal.md).
* **Azure data Factory**. Para obter mais informações, consulte [Introdução ao Azure Data Factory](../data-factory/introduction.md).


## <a name="migration-considerations"></a>Considerações sobre migração

Primeiro, identifique a estratégia de migração que funciona melhor para seu aplicativo que grava, lê ou processa dados no Data Lake armazenamento Gen1. Ao escolher uma estratégia, considere os requisitos de disponibilidade do aplicativo e o tempo de inatividade que ocorre durante uma migração. Por exemplo, a abordagem mais simples pode ser usar o modelo de migração em nuvem "lift-and-shift". Nessa abordagem, você pausa o aplicativo em sua região existente, enquanto todos os seus dados são copiados para a nova região. Quando o processo de cópia for concluído, continue executando o aplicativo na nova região e, em seguida, exclua a conta do Data Lake armazenamento Gen1 antiga. O tempo de inatividade durante a migração é necessário.

Para reduzir o tempo de inatividade, é possível começar imediatamente a ingerir novos dados na nova região. Quando você tiver o mínimo de dados necessário, execute o aplicativo na nova região. Em segundo plano, continue copiando dados antigos da conta existente do Data Lake Storage Gen1 para a nova conta do Data Lake Storage Gen1 na nova região. Ao usar essa abordagem, é possível mudar para a nova região com tempo de inatividade mínimo. Quando todos os dados mais antigos tiverem sido copiados, exclua a conta antiga do Data Lake Storage Gen1.

Outros detalhes importantes a considerar no planejamento da migração são:

* **Volume de dados**. O volume de dados (em gigabytes, o número de arquivos e pastas e assim por diante) afeta o tempo e os recursos necessários para a migração.

* **Nome da conta do Data Lake Storage Gen1**. O novo nome da conta na nova região deve ser global exclusivo. Por exemplo, o nome da sua antiga conta Data Lake Storage Gen1 no Leste dos EUA 2 pode ser contosoeastus2.azuredatalakestore.net. Você pode nomear sua nova conta Data Lake Storage Gen1 no norte da Europa contosonortheu.azuredatalakestore.net.

* **Ferramentas**. É recomendável que você use o [atividade de cópia do Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) para copiar os arquivos do Data Lake armazenamento Gen1. O Data Factory dá suporte à movimentação de dados com alto desempenho e confiabilidade. Lembre-se de que o Data Factory copia somente a hierarquia de pastas e o conteúdo dos arquivos. É necessário aplicar manualmente as ACLs (listas de controle de acesso) usadas na conta antiga à nova conta. Para obter mais informações, incluindo as metas de desempenho para cenários mais favoráveis, consulte o [guia de ajuste e desempenho da Atividade de Cópia](../data-factory/copy-activity-performance.md). Se desejar que os dados sejam copiados mais rapidamente, talvez você precise usar mais Unidades de Movimentação de Dados na Nuvem. Algumas outras ferramentas, como AdlCopy, não dão suporte à cópia de dados entre regiões.  

* **Encargos de largura de banda**. Os [encargos de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/) se aplicam, pois os dados são transferidos para fora de uma região do Azure.

* **ACLs nos dados**. Proteja seus dados na nova região aplicando ACLs a arquivos e pastas. Para obter mais informações, consulte [protegendo os dados armazenados no Azure Data Lake armazenamento Gen1](data-lake-store-secure-data.md). Recomendamos o uso da migração para atualizar e ajustar as ACLs. Talvez você deseje usar configurações semelhantes para as configurações atuais. É possível exibir as ACLs aplicadas a um arquivo usando o portal do Azure, [cmdlets do PowerShell](/powershell/module/az.datalakestore/get-azdatalakestoreitempermission) ou SDKs.  

* **Localização dos serviços de análise**. Para obter o melhor desempenho, os serviços de análise, como o Azure Data Lake Analytics ou o Azure HDInsight, devem estar na mesma região dos dados.  

## <a name="next-steps"></a>Próximas etapas
* [Visão Geral do Azure Data Lake Storage Gen1](data-lake-store-overview.md)
