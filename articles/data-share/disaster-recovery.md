---
title: Recuperação de desastre para o compartilhamento de dados do Azure
description: Recuperação de desastre para o compartilhamento de dados do Azure
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 07/30/2020
ms.openlocfilehash: 1d7c9935d7e0d6bb2d457aa4c08f9b2b3e5fa910
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218690"
---
# <a name="disaster-recovery-for-azure-data-share"></a>Recuperação de desastre para o compartilhamento de dados do Azure

Este artigo explica como configurar um ambiente de recuperação de desastre para o compartilhamento de dados do Azure. As interrupções do Azure data center são raras, mas podem durar em qualquer lugar, de alguns minutos a horas. As interrupções do Data Center podem causar interrupções em ambientes que dependem de dados sendo compartilhados pelo provedor de dados. Seguindo as etapas detalhadas neste artigo, os provedores de dados podem continuar a compartilhar dados com seus consumidores de dados no caso de uma interrupção data center para a região primária que hospeda seu compartilhamento de dados. 

## <a name="achieving-business-continuity-for-azure-data-share"></a>Obtendo continuidade dos negócios para o compartilhamento de dados do Azure

Para ser preparado para uma interrupção de data center, o provedor de dados pode ter um ambiente de compartilhamento de dados provisionado em uma região secundária. As medidas podem ser tomadas para garantir um failover suave caso ocorra uma interrupção data center. 

Os provedores de dados podem provisionar recursos de compartilhamento de dados secundários do Azure em uma região adicional. Esses recursos de compartilhamento de dados podem ser configurados para incluir compartilhamentos e conjuntos de dados que existem no recurso de compartilhamento primário do Azure Data. Eles podem convidar consumidores de dados para os compartilhamentos secundários ao configurar o ambiente de recuperação de desastres ou em um momento posterior (ou seja, como parte das etapas de failover manual).

Se os consumidores de dados tiverem assinaturas de compartilhamento ativas em um ambiente secundário provisionado para fins de DR, eles poderão habilitar o agendamento de instantâneo como parte do failover. Se os consumidores de dados não quiserem assinar uma região secundária para fins de DR, eles poderão ser convidados para o compartilhamento secundário em um momento posterior. 

Os consumidores de dados podem ter uma assinatura de compartilhamento ativa que está ociosa para fins de DR ou os provedores de dados podem convidá-las posteriormente como parte dos procedimentos de failover manual. 

## <a name="related-information"></a>Informações relacionadas

- [Continuidade dos negócios e recuperação de desastre](../best-practices-availability-paired-regions.md)
- [Obtenha alta disponibilidade em sua estratégia de BCDR](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)

## <a name="next-steps"></a>Próximas etapas

Para saber como iniciar o compartilhamento de dados, continue com o tutorial sobre como [compartilhar seus dados](share-your-data.md).