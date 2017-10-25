---
title: "Testando sua oferta de modelo de solução para o Marketplace | Microsoft Docs"
description: "Saiba como testar a oferta do modelo de solução para o Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: ef8f9b5e-b98c-49f3-913f-cdf772c14c12
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/04/2015
ms.author: hascipio; v-divte
ms.openlocfilehash: da1fc4713fd1d832c7ba91226f72cbef63b241bc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="test-your-solution-template-offer-in-staging"></a>Testar a oferta de modelo de solução em preparo
Preparo significa implantar a sua oferta em uma "área restrita" privada, em que você poderá testar e verificar sua funcionalidade antes de publicá-la para produção. A oferta aparece em preparo como apareceria para um cliente que a tivesse implantado. Sua oferta deve estar certificada para ser enviada por push para preparação.

Depois que a oferta for preparada, você poderá exibir e testar a oferta no [Portal do Azure](https://portal.azure.com/).

Siga as etapas abaixo para enviar por push sua oferta de preparo e testá-la no [Portal do Azure](https://portal.azure.com/):

1. Acesse o [Portal de Publicação](https://publish.windowsazure.com) > **guia Modelos de Solução** > sua oferta > **Publicar** > **Enviar por Push para Preparação**.
2. Forneça a lista de assinaturas do Azure que você usará para visualizar e testar sua oferta.
3. Entre no portal de visualização do Azure usando uma dos IDs de assinatura do Azure que você usou na etapa anterior.
4. Execute pelo menos uma rodada de testes no portal de visualização do Azure nos pontos mencionados a seguir:
   * Certifique-se de que o conteúdo de marketing seja mostrado corretamente no Azure Marketplace.
   * Implantação ponta a ponta da topologia.
   * Execute testes de desempenho e testes de estresse.
   * Certifique-se de que a sua topologia segue as práticas recomendadas.

## <a name="next-steps"></a>Próximas etapas
Se você estiver satisfeito com os resultados, poderá prosseguir para a fase de publicação de oferta final, **Etapa 4**: [Implantando sua oferta no Marketplace](marketplace-publishing-push-to-production.md). Caso contrário, faça alterações na oferta e solicite a certificação novamente.

> [!NOTE]
> A certificação não é necessária para mudanças de conteúdo de marketing.
> 
> 

Confira [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md) para obter um guia para todas as tarefas de editor.

