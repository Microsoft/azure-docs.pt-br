---
title: Perguntas frequentes sobre o Azure Resource Health | Microsoft Docs
description: "Visão geral do Azure Resource Health"
services: Resource health
documentationcenter: dev-center-name
author: BernardoAMunoz
manager: 
editor: 
ms.assetid: 85cc88a4-80fd-4b9b-a30a-34ff3782855f
ms.service: service-health
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Supportability
ms.date: 07/05/2017
ms.author: BernardoAMunoz
ms.openlocfilehash: 794117b6f383bdd1851681864e99b3c1ef077f86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-resource-health-faq"></a>Perguntas frequentes sobre o Azure Resource Health
Conheça as respostas para as perguntas comuns sobre o Azure Resource Health.

## <a name="what-is-azure-resource-health"></a>O que é o Azure Resource Health?
O Resource Health ajuda a diagnosticar e obter suporte quando um problema do Azure afeta seus recursos. Ele informa sobre a integridade atual e anterior de seus recursos e ajuda a reduzir os problemas. O Resource Health fornece suporte técnico quando você precisa de ajuda com problemas de serviço do Azure.  

## <a name="what-is-the-resource-health-intended-for"></a>Qual é o objetivo do Resource Health?
Após a detecção de um problema com um recurso, o Resource Health pode ajudar você a diagnosticar a causa raiz. Ele fornece ajuda para atenuar o problema e o suporte técnico quando você precisar de mais ajuda com problemas de serviço do Azure.

## <a name="what-health-checks-are-performed-by-resource-health"></a>Quais verificações de integridade são realizadas pelo Resource Health?
O Resource Health executa várias verificações com base no [tipo de recurso](resource-health-checks-resource-types.md). Essas verificações foram projetadas para implementar três tipos de problemas: 
- Eventos não planejados, por exemplo, uma reinicialização inesperada do host
- Eventos planejados, como atualizações agendadas do sistema operacional host
- Eventos disparados por ações do usuário, por exemplo, um usuário que reinicializa uma máquina virtual

## <a name="what-does-each-of-the-health-status-mean"></a>O que significa cada status de integridade?
Há três status de integridade diferentes:
- Disponível: não há problemas conhecidos na plataforma do Azure que poderiam afetar esse recurso
- Não disponível: o Resource Health detectou problemas que estão afetando o recurso
- Desconhecido: o Resource Health não consegue determinar a integridade de um recurso porque parou de receber informações sobre ele. 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>O que significa o status desconhecido? Há algo errado com meu recurso?
O status de integridade é definido como desconhecido quando o Resource Health para de receber informações sobre um recurso específico. Embora esse status não seja uma indicação definitiva do estado do recurso, nos casos em que você estiver enfrentando problemas, isso pode indicar um problema com o Azure.

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>Como posso obter ajuda para um recurso que não está disponível?
Você pode enviar uma solicitação de suporte na folha do Resource Health. Você não precisa de um contrato de suporte com a Microsoft para abrir uma solicitação quando o recurso não estiver disponível devido a eventos da plataforma.

## <a name="does-resource-health-differentiate-between-unavailability-cased-by-platform-problems-versus-something-i-did"></a>O Resource Health diferencia a indisponibilidade causada por problemas de plataforma de uma ação executada por mim?
Sim. Quando um recurso não está disponível, o Resource Health identifica a causa raiz dentro de uma destas categorias: 
-   Ação iniciada pelo usuário
-   Evento planejado 
-   Evento não planejado

No portal, as ações iniciadas pelo usuário aparecem usando um ícone de notificação azul, enquanto eventos planejados e não planejados são mostrados com um ícone de aviso vermelho. Mais detalhes são fornecidos na [Visão geral do Resource Health](Resource-health-overview.md).  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Posso integrar o Resource Health com minhas ferramentas de monitoramento?
O Resource Health é um serviço projetado para ajudar você a diagnosticar e atenuar problemas de serviço do Azure que afetam seus recursos. Embora seja possível usar a API do Resource Health para obter de forma programática o status de integridade, recomendamos o uso de métricas para monitorar os recursos. Quando um problema é detectado, o Resource Health ajuda a determinar a causa raiz e orienta você por meio de ações para resolvê-la. Visite [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) para saber mais sobre como você pode usar métricas para verificar seus recursos.

## <a name="where-do-i-find-resource-health"></a>Onde encontro o Resource Health?
Depois de fazer logon no portal do Azure, há várias maneiras de acessar o Resource Health:
- Navegue até seu recurso. No painel de navegação à esquerda, selecione **Resource Health**
- Acesse a folha do Azure Monitor.  No painel de navegação à esquerda, selecione **Resource Health**.
- Abra a folha **Ajuda + Suporte** selecionando o ponto de interrogação no canto superior direito do portal e, depois, selecionando **Ajuda + Suporte**. Quando a folha abrir, selecione **Resource Health**

Você também pode usar a API do Resource Health para obter informações sobre a integridade de seus recursos.

## <a name="is-resource-health-available-for-all-resource-types"></a>O Resource Health está disponível para todos os tipos de recursos?
A lista de verificações de integridade e os tipos de recursos com suporte por meio do Resource Health podem ser encontrados [aqui](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>O que devo fazer se meu recurso estiver aparecendo disponível, mas eu acreditar que não esteja?
Ao verificar a integridade de um recurso, imediatamente abaixo do status de integridade você pode clicar em **Relatar o status de integridade incorreto**. Antes de enviar o relatório, você tem a opção de fornecer detalhes adicionais sobre o motivo de você achar que o status de integridade atual está incorreto.

## <a name="is-resource-health-available-for-all-azure-regions"></a>O Resource Health está disponível para todas as regiões do Azure? 
O Resource Health está disponível em todas as geografias do Azure, exceto nas seguintes regiões:
- Gov. dos EUA – Virgínia
- Gov do Iowa nos EUA
- DoD do Leste dos EUA
- DoD Central dos EUA
- Alemanha Central
- Nordeste da Alemanha
- Leste da China
- Norte da China

## <a name="how-is-resource-health-different-from-the-service-health-dashboard-or-the-azure-portal-service-notifications"></a>De que forma o Resource Health é diferente do Painel do Service Health ou das notificações de serviço do portal do Azure?
As informações fornecidas pelo Resource Health são mais específicas do que aquelas fornecidas pelo Painel do Azure Service Health.

Enquanto o [Status do Azure](https://status.azure.com) e as notificações de serviço do portal informam sobre problemas de serviço que afetam um amplo conjunto de clientes (por exemplo, uma região do Azure), o Resource Health expõe eventos mais granulares que são relevantes apenas ao recurso específico. Por exemplo, se um host for reinicializado inesperadamente, o Resource Health alertará somente os clientes cujas máquinas virtuais estavam em execução nesse host.

É importante observar que, para fornecer visibilidade completa sobre eventos que afetam os recursos, o Resource Health também exibe eventos publicados nas notificações do Service e no Painel do Service Health.

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>Preciso ativar o Resource Health em cada recurso?
Não. As informações de integridade estão disponíveis para todos os tipos de recursos disponíveis por meio do Resource Health. 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>Precisamos habilitar o Resource Health para minha organização?
Não.  O Azure Resource Health é acessível no portal do Azure sem nenhum requisito de instalação.

## <a name="is-resource-health-available-free-of-charge"></a>O Resource Health está disponível gratuitamente?
Sim.  O Azure Resource Health está disponível gratuitamente.

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Quais são as recomendações fornecidas pelo Resource Health?
Com base no status de integridade, o Resource Health fornece recomendações com a meta de reduzir o tempo gasto com a solução de problemas. Para os recursos disponíveis, as recomendações se concentram em como resolver os problemas mais comuns que os clientes encontram. Se o recurso não estiver disponível devido a um evento não planejado do Azure, o foco estará no auxílio durante e após o processo de recuperação. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o Resource Health:
-  [Visão geral do Azure Resource Health](Resource-health-overview.md)
-  [Tipos de recurso e verificações de integridade disponíveis por meio do Azure Resource Health](resource-health-checks-resource-types.md)
