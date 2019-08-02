---
title: Visão geral do Azure Resource Health | Microsoft Docs
description: Visão geral do Azure Resource Health
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.date: 05/10/2019
ms.openlocfilehash: 9c2096f94f38d13288c6ce3742252bc6d576835a
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854224"
---
# <a name="resource-health-overview"></a>Visão geral de Resource Health
 
Azure Resource Health ajuda a diagnosticar e obter suporte para problemas de serviço que afetam os recursos do Azure. Ele relata a integridade atual e passada de seus recursos.

Relatórios de [status do Azure](https://status.azure.com) sobre problemas de serviço que afetam um amplo conjunto de clientes do Azure. Resource Health fornece um painel personalizado da integridade de seus recursos. Resource Health mostra todas as ocasiões em que os recursos não estão disponíveis devido a problemas de serviço do Azure. Esses dados tornam mais fácil para você ver se um SLA foi violado.

## <a name="resource-definition-and-health-assessment"></a>Definição de recurso e avaliação de integridade

Um *recurso* é uma instância específica de um serviço do Azure, como uma máquina virtual, um aplicativo Web ou um banco de dados SQL. Resource Health se baseia em sinais de diferentes serviços do Azure para avaliar se um recurso está íntegro. Se um recurso não estiver íntegro, o Resource Health analisará informações adicionais para determinar a origem do problema. Ele também relata as ações que a Microsoft está adotando para corrigir o problema e identifica as coisas que você pode fazer para solucioná-lo.

Para obter mais informações sobre como a integridade é avaliada, consulte a lista de tipos de recursos e verificações de integridade em [Azure Resource Health](resource-health-checks-resource-types.md).

## <a name="health-status"></a>Status de integridade

A integridade de um recurso é exibida como um dos estados a seguir.

### <a name="available"></a>Disponível

*Disponível* significa que não há nenhum evento detectado que afete a integridade do recurso. Nos casos em que o recurso se recuperou do tempo de inatividade não planejado durante as últimas 24 horas, você verá uma notificação "recentemente resolvido".

![Status de * disponível * para uma máquina virtual que tenha uma notificação "recentemente resolvida"](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Indisponível

*Indisponível* significa que o serviço detectou um evento de plataforma ou não plataforma em andamento que afeta a integridade do recurso.

#### <a name="platform-events"></a>Eventos de plataforma

Os eventos da plataforma são disparados por vários componentes da infraestrutura do Azure. Eles incluem ações agendadas (por exemplo, manutenção planejada) e incidentes inesperados (por exemplo, uma reinicialização do host não planejada).

Resource Health fornece detalhes adicionais sobre o evento e o processo de recuperação. Ele também permite que você entre em contato Suporte da Microsoft mesmo que não tenha um contrato de suporte ativo.

![Status de * não disponível * para uma máquina virtual devido a um evento de plataforma](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>Eventos de não plataforma

Eventos que não são de plataforma são disparados por ações do usuário. Os exemplos incluem parar uma máquina virtual ou atingir o número máximo de conexões com o cache do Azure para Redis.

![Status de "não disponível" para uma máquina virtual devido a um evento de não plataforma](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Unknown

*Desconhecido* significa que Resource Health não recebeu informações sobre o recurso por mais de 10 minutos. Embora esse status não seja uma indicação definitiva do estado do recurso, ele é um ponto de dados importante para a solução de problemas.

Se o recurso estiver em execução conforme o esperado, o status do recurso mudará para *Disponível* após alguns minutos.

Se você tiver problemas com o recurso, o status de integridade *desconhecido* poderá significar que um evento na plataforma está afetando o recurso.

![Status de * desconhecido * para uma máquina virtual](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>Degradado

*Degradado* significa que o recurso detectou uma perda no desempenho, embora ainda esteja disponível para uso.

Recursos diferentes têm seus próprios critérios para quando eles relatarem que estão degradados.

![Status de * degradado * para uma máquina virtual](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>Relatar um status incorreto

Se você considerar que o status de integridade atual está incorreto, você pode nos informar selecionando **relatar o status de integridade incorreto**. Nos casos em que um problema do Azure está afetando você, incentivamos você a entrar em contato com o suporte de Resource Health.

![Formulário para enviar informações sobre um status incorreto](./media/resource-health-overview/incorrect-status.png)

## <a name="history-information"></a>Informações de histórico

Você pode acessar 14 dias de histórico na seção **histórico de integridade** de Resource Health.

![Lista de eventos do Resource Health nas últimas duas semanas](./media/resource-health-overview/history-blade.png)

## <a name="get-started"></a>Introdução

Abrir Resource Health para um recurso:

1. Entre no Portal do Azure.
2. Procure o recurso.
3. No menu de recursos no painel esquerdo, selecione **Resource Health**.

![Abrir o Resource Health a partir da exibição de recursos](./media/resource-health-overview/from-resource-blade.png)

Também é possível acessar o Resource Health, selecionando **Todos os serviços** e digitando **resource health** na caixa de texto de filtro. No painel **Ajuda + suporte**, selecione [Resource Health](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth).

![Abrir o Resource Health a partir de "Todos os serviços"](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>Próximas etapas

Confira estas referências para saber mais sobre Resource Health:
-  [Tipos de recursos e verificações de integridade no Azure Resource Health](resource-health-checks-resource-types.md)
-  [Perguntas frequentes sobre o Azure Resource Health](resource-health-faq.md)
