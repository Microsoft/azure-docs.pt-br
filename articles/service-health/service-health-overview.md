---
title: "Visão geral da integridade de serviço do Azure | Microsoft Docs"
description: "Informações personalizadas sobre como seus aplicativos do Azure são afetados pela manutenção e pelos problemas de serviço atuais e futuros do Azure."
services: Resource health
documentationcenter: 
author: rboucher
manager: 
editor: 
ms.assetid: 
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/07/2017
ms.author: robb
ms.openlocfilehash: 001dc1fa2a0fd7e132101944a87be3f8552d8738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-service-health"></a>Integridade de serviço do Azure
A integridade de serviço do Azure fornece informações em tempo hábil e personalizadas a respeito de quando problemas nos serviços do Azure afetam seus serviços.  Ela também ajuda você a se preparar para a próxima manutenção planejada.

## <a name="service-health-events"></a>Eventos de Integridade do Serviço
A integridade do serviço controla os três tipos de eventos de integridade que podem afetar seus recursos:
1. **Problemas de serviço** – problemas nos serviços do Azure que lhe afetam imediatamente. 
2. **Manutenção planejada** – próxima manutenção que poderá afetar a disponibilidade de seus serviços no futuro.  
3. **Aconselhamento de integridade** – alterações nos serviços do Azure que exigem sua atenção. Exemplos incluem quando os recursos do Azure são preteridos ou se você excede uma cota de uso.

    ![Eventos de integridade do serviço](./media/service-health-overview/azure-service-health-overview-7.png)

## <a name="get-started-with-service-health"></a>Introdução à integridade de serviço
Para iniciar o painel de Integridade do Serviço, selecione o bloco de Integridade do Serviço em seu painel do portal. Se você removeu anteriormente o bloco ou está usando o painel personalizado, pesquise pelo serviço Integridade do Serviço em "Mais serviços" (parte inferior esquerda no seu painel).
![Introdução à integridade de serviço](./media/service-health-overview/azure-service-health-overview-1.png)

## <a name="see-current-issues-which-impact-your-services"></a>Ver problemas atuais que afetam seus serviços
A exibição **Problemas de serviço** mostra os problemas em andamento nos serviços do Azure que estão afetando seus recursos. Você pode entender quando o problema começou e quais serviços e regiões foram afetadas. Você também pode ler a atualização mais recente para entender o que o Azure está fazendo para resolver o problema. 
![Gerenciar problema de serviço](./media/service-health-overview/azure-service-health-overview-2.png)

Escolha a guia **Impacto potencial** para ver a lista específica de recursos que você tem e que podem ser afetados pelo problema. Você pode baixar uma lista em CSV desses recursos para compartilhar com sua equipe.
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

## <a name="configure-service-health-alerts"></a>Configurar alertas de Integridade de Serviço
A Integridade de Serviço do Azure integra-se ao Azure Monitor para alertar você por emails, mensagens de texto e notificações de webhook quando seus recursos críticos para os negócios são afetados. Configure um alerta de log de atividades para o evento de Integridade de Serviço apropriado. Direcione esse alerta para as pessoas apropriadas em sua organização usando Grupos de Ações. Para obter mais informações, consulte [Configurar alertas para a Integridade do Serviço](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md)

# <a name="next-steps"></a>Próximas etapas
Configure alertas para receber notificações de problemas de integridade. Para obter mais informações, consulte [Configurar alertas do Service Health](../monitoring-and-diagnostics/monitoring-activity-log-alerts-on-service-notifications.md). 