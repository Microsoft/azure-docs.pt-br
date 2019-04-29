---
title: Perguntas frequentes sobre o Azure Resource Health | Microsoft Docs
description: Visão geral do Azure Resource Health
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 01/29/2019
ms.workload: Supportability
ms.openlocfilehash: b4062b3f0bc389de4403ac81b56688508f5ea50e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620820"
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
- Disponível: Não há problemas conhecidos na plataforma do Azure que poderiam afetar esse recurso
- Não disponível: A integridade do recurso detectou problemas que afetam o recurso
- Desconhecido: A integridade do recurso não consegue determinar a integridade porque parou de receber informações sobre ele. 

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
Integridade de recursos tem [suporte de visualização](resource-health-alert-arm-template-guide.md) para Log de atividades com base em alertas. Uso de alertas do Log de atividades [grupos de ação](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/action-groups) para notificar os usuários que um alerta foi disparado. Grupos de ação oferecem suporte a uma variedade de canais de notificação, como email, SMS, webhook e ações de ITSM.

## <a name="where-do-i-find-resource-health"></a>Onde encontro o Resource Health?
Depois de fazer logon no portal do Azure, há várias maneiras de acessar o Resource Health:
- Navegue até seu recurso. No painel de navegação à esquerda, selecione **Resource Health**
- Vá à folha Integridade do Serviço do Azure.  No painel de navegação à esquerda, selecione **Resource Health**.
- Abra a folha **Ajuda + Suporte** selecionando o ponto de interrogação no canto superior direito do portal e, depois, selecionando **Ajuda + Suporte**. Quando a folha abrir, selecione **Resource Health**

Você também pode usar a API do Resource Health para obter informações sobre a integridade de seus recursos.

## <a name="is-resource-health-available-for-all-resource-types"></a>O Resource Health está disponível para todos os tipos de recursos?
A lista de verificações de integridade e os tipos de recursos com suporte por meio do Resource Health podem ser encontrados [aqui](resource-health-checks-resource-types.md).

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>O que devo fazer se meu recurso estiver aparecendo disponível, mas eu acreditar que não esteja?
Ao verificar a integridade de um recurso, imediatamente abaixo do status de integridade você pode clicar em **Relatar o status de integridade incorreto**. Antes de enviar o relatório, você tem a opção de fornecer detalhes adicionais sobre o motivo de você achar que o status de integridade atual está incorreto.

## <a name="is-resource-health-available-for-all-azure-regions"></a>O Resource Health está disponível para todas as regiões do Azure? 
O Resource health está disponível em todas as geografias do Azure.

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Como o Resource Health é diferente do status do Azure ou painel de Integridade de Serviço?
As informações fornecidas pelo Resource Health são mais específicas do que aquelas fornecidas pelo status do Azure ou painel de Integridade do Serviço.

Enquanto o [status do Azure](https://status.azure.com) e o painel de Integridade do Serviço informam sobre problemas de serviço que afetam um amplo conjunto de clientes (por exemplo, uma região do Azure), o Resource Health expõe eventos mais granulares que são relevantes apenas ao recurso específico. Por exemplo, se um host for reinicializado inesperadamente, o Resource Health alertará somente os clientes cujas máquinas virtuais estavam em execução nesse host.

É importante observar que, para fornecer visibilidade completa sobre eventos que afetam os recursos, o Resource Health também exibe eventos publicados no painel da Integridade do Serviço.

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
