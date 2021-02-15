---
title: Visão geral da Integridade de Serviço | Microsoft Docs
description: Saiba como a integridade do serviço fornece um painel personalizável que controla a integridade dos serviços do Azure nas regiões em que você os utiliza.
ms.topic: conceptual
ms.date: 05/10/2019
ms.openlocfilehash: 8246b0ab93b95c13858e4ff96d0f24b255d05e55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967786"
---
# <a name="service-health-overview"></a>Visão geral da integridade do serviço

A Integridade do Serviço oferece um painel personalizável que controla a integridade de seus serviços do Azure nas regiões onde você os usa. Neste painel, você pode rastrear eventos ativos, como problemas de serviço em andamento, manutenção planejada futura ou avisos de saúde relevantes. Quando os eventos se tornam inativos, eles são inseridos em seu histórico de integridade por até 90 dias. Por fim, você pode usar o painel de Integridade do Serviço para criar e gerenciar alertas de integridade do serviço que notificam proativamente quando problemas de serviço estão afetando você.

## <a name="service-health-events"></a>Eventos de Integridade do Serviço

A integridade do serviço rastreia quatro tipos de eventos de integridade que podem afetar seus recursos:

1. **Problemas de serviço** – problemas nos serviços do Azure que lhe afetam imediatamente. 
2. **Manutenção planejada** – próxima manutenção que poderá afetar a disponibilidade de seus serviços no futuro.  
3. **Aconselhamento de integridade** – alterações nos serviços do Azure que exigem sua atenção. Os exemplos incluem a substituição de recursos do Azure ou requisitos de atualização (por exemplo, atualizar para uma estrutura PHP com suporte).
4. **Consultorias de segurança** – notificações ou violações relacionadas à segurança que podem afetar a disponibilidade dos serviços do Azure.

> [!NOTE]
> Para exibir eventos de integridade do serviço, os usuários devem receber [a função leitor](../role-based-access-control/role-assignments-portal.md) em uma assinatura.

## <a name="get-started-with-service-health"></a>Introdução à integridade de serviço

Para iniciar o painel de Integridade do Serviço, selecione o bloco de Integridade do Serviço em seu painel do portal. Se você removeu anteriormente o bloco ou está usando o painel personalizado, pesquise pelo serviço Integridade do Serviço em "Mais serviços" (parte inferior esquerda no seu painel).

![Introdução à integridade de serviço](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Ver problemas atuais que afetam seus serviços

A exibição **Problemas de serviço** mostra os problemas em andamento nos serviços do Azure que estão afetando seus recursos. Você pode entender quando o problema começou e quais serviços e regiões foram afetadas. Você também pode ler a atualização mais recente para entender o que o Azure está fazendo para resolver o problema. 

[![Gerenciar problema de serviço](./media/service-health-overview/azure-service-health-overview-2.png)](./media/service-health-overview/azure-service-health-overview-2.png#lightbox)

Escolha a guia **Impacto potencial** para ver a lista específica de recursos que você tem e que podem ser afetados pelo problema. Você pode fazer o download de uma lista em CSV desses recursos para compartilhar com sua equipe.

[![Gerenciar o problema de serviço – impacto](./media/service-health-overview/azure-service-health-overview-4.png)](./media/service-health-overview/azure-service-health-overview-4.png#lightbox)

## <a name="see-emerging-issues-which-may-impact-your-services"></a>Veja problemas emergentes que podem afetar seus serviços

Há situações em que problemas de serviço generalizados podem ser postados na [página de status do Azure](https://status.azure.com) antes que as comunicações de destino possam ser enviadas aos clientes afetados. Para garantir que a integridade do serviço do Azure forneça uma visão abrangente dos problemas que podem afetar você, os problemas ativos da página de status do Azure são exibidos na integridade do serviço como *problemas emergentes*. Quando um evento estiver ativo na página de status do Azure, uma faixa de problemas emergentes estará presente na integridade do serviço. Clique na faixa para ver os detalhes completos do problema.

![Problema de serviço emergente](./media/service-health-overview/azure-service-health-emerging-issue.png)

## <a name="get-links-and-downloadable-explanations"></a>Obter links e explicações para download 

Você pode obter um link para o problema a fim de usá-lo em seu sistema de gerenciamento de problemas. Você pode baixar PDF e, às vezes, arquivos CSV para compartilhar com pessoas que não têm acesso ao portal do Azure.   

[![Gerenciar problema de serviço – gerenciamento de problemas](./media/service-health-overview/azure-service-health-overview-3.png)](./media/service-health-overview/azure-service-health-overview-3.png#lightbox)

## <a name="get-support-from-microsoft"></a>Obter suporte da Microsoft

Contate o suporte se seu recurso foi deixado em um estado incorreto, mesmo depois que o problema tenha sido resolvido.  Use os links de suporte à direita da página.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Fixar um mapa de integridade personalizado ao seu painel

Filtre a Integridade do Serviço para mostrar suas assinaturas, regiões e tipos de recursos essenciais aos negócios. Salve o filtro e fixe um mapa-múndi de integridade personalizado em seu painel do portal. 

[![Filtrar mapa de integridade personalizado](./media/service-health-overview/azure-service-health-overview-6a.png)](./media/service-health-overview/azure-service-health-overview-6a.png#lightbox)

![Fixar um mapa de integridade personalizado](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Configurar alertas de integridade de serviço

A Integridade de Serviço integra-se ao Azure Monitor para alertar você por emails, mensagens de texto e notificações de webhook quando seus recursos críticos para os negócios são afetados. Configure um alerta de log de atividades para o evento de integridade de serviço apropriado. Direcione esse alerta para as pessoas apropriadas em sua organização usando Grupos de Ações. Para obter mais informações, consulte [configurar alertas para integridade do serviço](./alerts-activity-log-service-notifications-portal.md)

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="next-steps"></a>Próximas etapas

Configure alertas para receber notificações de problemas de integridade. Para obter mais informações, consulte [práticas recomendadas para configurar alertas de integridade do serviço do Azure](https://www.youtube.com/watch?v=k5d5ca8K6tc&list=PLLasX02E8BPBBSqygdRvlTnHfp1POwE8K&index=6&t=0s). 
