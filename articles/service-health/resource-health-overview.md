---
title: "Visão geral do Azure Resource Health | Microsoft Docs"
description: "Visão geral do Azure Resource Health"
services: Resource health
documentationcenter: 
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/01/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 040d58a81a9b41fe660e4276d698bf884f90bb6c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-health-overview"></a>Visão geral do Azure Resource Health
 
O Resource Health ajuda a diagnosticar e obter suporte quando um problema do Azure afeta seus recursos. Ele informa sobre a integridade atual e anterior de seus recursos e ajuda a reduzir os problemas. O Resource Health fornece suporte técnico quando você precisa de ajuda com problemas de serviço do Azure.

Enquanto o [Status do Azure](https://status.azure.com) informa sobre problemas de serviço que afetam um amplo conjunto de clientes do Azure, o Resource Health fornece um painel personalizado referente à integridade de seus recursos. O Resource Health mostra todos os horários em que seus recursos não estavam disponíveis no passado devido a problemas do serviço do Azure. Isso simplifica a tarefa de compreender se um SLA foi violado. 

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-a-resource-is-healthy-or-not"></a>O que é considerado um recurso e como o Resource Health decide se um recurso está íntegro ou não?
Um recurso é uma instância criada de um tipo de recurso oferecido por um serviço do Azure por meio do Azure Resource Manager, por exemplo: uma máquina virtual, um aplicativo Web ou um banco de dados SQL.

O Resource Health se baseia em sinais emitidos pelos diferentes serviços do Azure para avaliar se um recurso está íntegro ou não. Se um recurso não está íntegro, o Resource Health analisa informações adicionais para determinar a origem do problema. Ele também identifica ações que a Microsoft está tomando para corrigir o problema ou as ações que você pode tomar para resolver a causa do problema. 

Examine a lista completa de tipos de recursos e verificações de integridade em [Azure Resource Health](resource-health-checks-resource-types.md) para obter detalhes adicionais sobre como a integridade é avaliada.

## <a name="health-status-provided-by-resource-health"></a>Status de integridade fornecida pelo Resource Health
A integridade de um recurso é um dos status a seguir:

### <a name="available"></a>Disponível
O serviço não detectou nenhum evento que afete a integridade do recurso. Nos casos em que o recurso tiver se recuperado de tempo de inatividade não planejado nas últimas 24 horas, você verá a notificação **recuperado recentemente**.

![Máquina virtual disponível do Resource Health](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Indisponível
O serviço detectou um evento de plataforma ou não plataforma em andamento que afeta a integridade do recurso.

#### <a name="platform-events"></a>Eventos de plataforma
Esses eventos são disparados por vários componentes da infraestrutura do Azure e incluem tanto ações agendadas, tais como manutenção planejada, quanto incidentes inesperados, assim como uma reinicialização não planejada do host.

O Resource Health fornece detalhes adicionais sobre o evento, o processo de recuperação e permite que você entre em contato com o suporte mesmo que você não tenha um contrato de suporte da Microsoft ativo.

![Máquina virtual do Resource Health indisponível devido a evento de plataforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventos de não plataforma
Esses eventos são disparados por ações executadas por usuários, por exemplo, parar uma máquina virtual ou alcançar o número máximo de conexões para um Cache Redis.

![Máquina virtual do Resource Health indisponível devido a evento de não plataforma](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Desconhecido
Esse status de integridade indica que o Resource Health não recebeu informações sobre este recurso há mais de 10 minutos. Embora esse status não seja uma indicação definitiva do estado do recurso, é um ponto de dados importantes no processo de solução de problemas:
* Se o recurso estiver sendo executado como esperado, o status do recurso será atualizado para disponível depois de alguns minutos.
* Se você está tendo problemas com o recurso, o status de integridade Desconhecido pode sugerir que um evento na plataforma está afetando o recurso.

![Máquina virtual desconhecida do Resource Health](./media/resource-health-overview/Unknown.png)

## <a name="report-an-incorrect-status"></a>Relatar um status incorreto
Se a qualquer momento você acredita que o status de integridade atual está incorreto, você pode nos informar a respeito clicando em **Relatar o status de integridade incorreto**. Em casos nos quais você será afetado por um problema do Azure, recomendamos que você entre em contato com o suporte por meio da folha do Resource Health. 

![Status incorreto do Relatório do Resource Health](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>Informações de histórico
Você pode acessar até 14 dias de dados de integridade históricos clicando em **Exibir Histórico** na folha do Resource Health. 

![Histórico do Relatórios do Resource Health](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>Introdução
Para abrir o Resource Health para um recurso
1.  Entre no Portal do Azure.
2.  Navegue até seu recurso.
3.  No menu do recurso localizado no lado esquerdo, clique em **Resource Health**.

![Abra o Resource Health na folha Recurso](./media/resource-health-overview/from-resource-blade.png)

Você também pode acessar o Resource Health clicando em **Mais serviços**e digitando **Resource Health** na caixa de texto de filtro para abrir a folha **Ajuda + Suporte**. Finalmente, clique em [**Resource Health**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Abra o Resource Health em Mais serviços](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Próximas etapas

Confira estes recursos para saber mais sobre o Resource Health:
-  [Tipos de recursos e verificações de integridade no Azure Resource Health](resource-health-checks-resource-types.md)
-  [Perguntas frequentes sobre o Azure Resource Health](resource-health-faq.md)




