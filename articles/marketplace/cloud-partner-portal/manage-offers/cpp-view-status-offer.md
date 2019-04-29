---
title: Exibir o status das ofertas do Marketplace – Azure Marketplace | Microsoft Docs
description: Exibir o status das ofertas nos Marketplaces do Azure e do AppSource usando o Portal do Cloud Partner
services: Azure, AppSource, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: pbutlerm
ms.openlocfilehash: bdec2d699e8448c8e2303dfbabcb4d176a9ca389
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60729728"
---
# <a name="view-the-publishing-status-of-azure-marketplace-and-appsource-offers"></a>Exibir o status de publicação de ofertas do Azure Marketplace e do AppSource

Depois de criar uma oferta e especialmente durante o processo de publicação, você pode exibir o status de sua oferta no Portal do Cloud Partner.  Em geral, o status de publicação está disponível nas páginas [**Todas as Ofertas**](../portal-tour/cpp-all-offers-page.md) e [**Aprovações**](../portal-tour/cpp-approvals-page.md) do portal.  Um dos indicadores de status a seguir deve ser exibido para cada oferta.  

|            Status              |   DESCRIÇÃO                                                           |
|            ------              |   -----------                                                           |
| **-**                          | A oferta foi criada mas o processo de publicação não foi iniciado.            |
| **Publicação em andamento**        | A oferta está passando pelas etapas do processo de publicação.   |
| **Falha ao publicar**             | Um problema crítico foi descoberto durante a validação ou análise pela Microsoft. |
| **Publicação cancelada**           | O publicador cancelou a processo de publicação da oferta.  Esse estado não retira uma oferta existente da lista no Marketplace. | 
| **Aguardando saída do publicador** | A oferta foi revisada pela Microsoft e agora aguarda uma verificação final pelo publicador. |
| **Removida da lista**                   | Uma oferta publicada anteriormente no marketplace foi removida.      | 
|  |  |


## <a name="publishing-status-details"></a>Detalhes do status de publicação 

Mais detalhes sobre o status de uma oferta enquanto ela percorre o processo de publicação podem ser encontrados na guia **Status** da página **Nova Oferta**.  Esta página lista todas as etapas de publicação para esse tipo de oferta.  *Observe que o número de etapas e as etapas específicas geralmente diferem entre os tipos de oferta.*  Essa página também indica quaisquer problemas pendentes gerados pelas etapas de validação e de revisão da Microsoft, que geralmente exigem ação pelo publicador antes que o processo de publicação possa continuar.  Por exemplo, a imagem a seguir mostra a guia **Status** para uma nova oferta de máquina virtual. 

![Guia de Status para a oferta de VM](./media/vm-offer-pub-steps1.png)

O exemplo a seguir mostra a guia **Status** para um serviço de consultoria, mostrando um erro relatado nas configurações de gerenciamento de cliente potencial.  Já que o gerenciamento de cliente potencial é necessário para serviços de consultoria, esse erro deve ser corrigido antes que publicação possa continuar.

![Guia de Status para consultar o erro de exibição do serviço](./media/consulting-service-error.png)

O status final de exemplo de um aplicativo do Azure mostra um problema crítico de revisão da Microsoft.  Ele contém um hot link para o item do VSTS que contém informações detalhadas sobre esse problema de revisão.  Para obter mais informações, confira [Publicar oferta de aplicativo do Azure](cpp-publish-offer.md).

![Guia de Status para um aplicativo do Azure mostrando o problema de revisão](../azure-applications/media/status-tab-ms-review.png)


## <a name="next-steps"></a>Próximas etapas

Para corrigir os problemas pendentes ou atualizar as configurações da oferta, você precisa [atualizar uma oferta](./cpp-update-offer.md). 
