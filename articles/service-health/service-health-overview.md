---
title: Visão geral da Integridade de Serviço | Microsoft Docs
description: Informações personalizadas sobre como seus aplicativos do Azure são afetados pela manutenção e pelos problemas de serviço atuais e futuros do Azure.
services: service-health
author: stephbaron
ms.author: stbaron
documentationcenter: service-health
ms.service: service-health
ms.topic: article
ms.workload: Supportability
ms.date: 03/27/2018
ms.openlocfilehash: 465e8751d02692648234a7a90b84b68f41522cb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620707"
---
# <a name="service-health"></a>Integridade do Serviço
A Integridade do Serviço oferece um painel personalizável que controla a integridade de seus serviços do Azure nas regiões onde você os usa. Neste painel, você pode rastrear eventos ativos, como problemas de serviço em andamento, manutenção planejada futura ou avisos de saúde relevantes. Quando os eventos se tornam inativos, eles são inseridos em seu histórico de integridade por até 90 dias. Por fim, você pode usar o painel de Integridade do Serviço para criar e gerenciar alertas de integridade do serviço que notificam proativamente quando problemas de serviço estão afetando você.

## <a name="service-health-events"></a>Eventos de Integridade do Serviço
A integridade do serviço controla os três tipos de eventos de integridade que podem afetar seus recursos:
1. **Problemas de serviço** – problemas nos serviços do Azure que lhe afetam imediatamente. 
2. **Manutenção planejada** – próxima manutenção que poderá afetar a disponibilidade de seus serviços no futuro.  
3. **Aconselhamento de integridade** – alterações nos serviços do Azure que exigem sua atenção. Exemplos incluem quando os recursos do Azure são preteridos ou se você excede uma cota de uso.

> [!NOTE]
> Para exibir eventos de Integridade do Serviço, os usuários devem ter a função de Leitor em uma assinatura.

## <a name="get-started-with-service-health"></a>Introdução à integridade de serviço
Para iniciar o painel de Integridade do Serviço, selecione o bloco de Integridade do Serviço em seu painel do portal. Se você removeu anteriormente o bloco ou está usando o painel personalizado, pesquise pelo serviço Integridade do Serviço em "Mais serviços" (parte inferior esquerda no seu painel).

![Introdução à integridade de serviço](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Ver problemas atuais que afetam seus serviços
A exibição **Problemas de serviço** mostra os problemas em andamento nos serviços do Azure que estão afetando seus recursos. Você pode entender quando o problema começou e quais serviços e regiões foram afetadas. Você também pode ler a atualização mais recente para entender o que o Azure está fazendo para resolver o problema. 

![Gerenciar problema de serviço](./media/service-health-overview/azure-service-health-overview-2.png)

Escolha a guia **Impacto potencial** para ver a lista específica de recursos que você tem e que podem ser afetados pelo problema. Você pode fazer o download de uma lista em CSV desses recursos para compartilhar com sua equipe.

![Gerenciar o problema de serviço – impacto](./media/service-health-overview/azure-service-health-overview-4.png)

## <a name="get-links-and-downloadable-explanations"></a>Obter links e explicações para download 
Você pode obter um link para o problema a fim de usá-lo em seu sistema de gerenciamento de problemas. Você pode baixar um PDF e, algumas vezes, arquivos CSV para compartilhar com pessoas que não tenham acesso ao Portal do Azure.   

![Gerenciar problema de serviço – gerenciamento de problemas](./media/service-health-overview/azure-service-health-overview-3.png)

## <a name="get-support-from-microsoft"></a>Obter suporte da Microsoft
Contate o suporte se seu recurso foi deixado em um estado incorreto, mesmo depois que o problema tenha sido resolvido.  Use os links de suporte à direita da página.  

## <a name="pin-a-personalized-health-map-to-your-dashboard"></a>Fixar um mapa de integridade personalizado ao seu painel
Filtre a Integridade do Serviço para mostrar suas assinaturas, regiões e tipos de recursos essenciais aos negócios. Salve o filtro e fixe um mapa-múndi de integridade personalizado em seu painel do portal. 

![Filtrar mapa de integridade personalizado](./media/service-health-overview/azure-service-health-overview-6a.png)

![Fixar um mapa de integridade personalizado](./media/service-health-overview/azure-service-health-overview-6b.png)

## <a name="configure-service-health-alerts"></a>Configurar alertas de integridade de serviço
A Integridade de Serviço integra-se ao Azure Monitor para alertar você por emails, mensagens de texto e notificações de webhook quando seus recursos críticos para os negócios são afetados. Configure um alerta de log de atividades para o evento de integridade de serviço apropriado. Direcione esse alerta para as pessoas apropriadas em sua organização usando Grupos de Ações. Para obter mais informações, consulte [Configurar alertas para a Integridade do Serviço](../azure-monitor/platform/alerts-activity-log-service-notifications.md)

# <a name="next-steps"></a>Próximas etapas
Configure alertas para receber notificações de problemas de integridade. Para obter mais informações, consulte [Configurar alertas do Service Health](../azure-monitor/platform/alerts-activity-log-service-notifications.md). 
