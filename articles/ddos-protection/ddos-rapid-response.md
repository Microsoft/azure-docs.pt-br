---
title: Resposta rápida do DDoS do Azure
description: Saiba como envolver especialistas em DDoS durante um ataque ativo para o suporte especializado.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: 8de95a56f3da928997a97ff216b13d522c82afca
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814186"
---
# <a name="azure-ddos-rapid-response"></a>Resposta rápida do DDoS do Azure

Durante um acesso ativo, os clientes padrão da proteção contra DDoS do Azure têm acesso à equipe de reação rápida a DDoS (DRR), que pode ajudar na investigação de ataques durante uma análise de ataque e de ataque.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de concluir as etapas deste tutorial, você deve primeiro criar um plano de [proteção padrão do DDoS do Azure](manage-ddos-protection.md).

## <a name="when-to-engage-drr"></a>Quando envolver DRR

Você só deve envolver DRR se: 

- Durante um ataque de DDoS, se você descobrir que o desempenho do recurso protegido está degradado gravemente ou o recurso não está disponível. Examine a etapa 2 acima em Configurando monitores para detectar problemas de disponibilidade e desempenho de recursos.
- Você considera que o recurso está sob ataque de DDoS, mas o serviço de proteção contra DDoS não está mitigando o ataque com eficiência.
- Você está planejando um evento viral que aumentará significativamente o tráfego de rede.
- Para ataques que têm um impacto crítico nos negócios.

## <a name="engage-drr-during-an-active-attack"></a>Envolver o DRR durante um ataque ativo

1. Em portal do Azure ao criar uma nova solicitação de suporte, escolha **tipo de problema** como técnico.
2. Escolha **serviço** como **proteção contra DDoS**.
3. Escolha um recurso no menu suspenso de recursos. _Você deve selecionar um plano de DDoS vinculado à rede virtual que está sendo protegida pela proteção contra DDoS Standard para envolver o DRR._

    ![Escolher recurso](./media/ddos-rapid-response/choose-resource.png)

4. Na página próximo **problema** , selecione a **severidade** como impacto crítico e tipo de **problema** como ' em ataque '.

    ![PSeverity e tipo de problema](./media/ddos-rapid-response/severity-and-problem-type.png)

5. Conclua detalhes adicionais e envie a solicitação de suporte.

DRR segue o modelo de suporte Azure Rapid Response. Consulte o [escopo de suporte e a capacidade de resposta](https://azure.microsoft.com/en-us/support/plans/response/) para obter mais informações sobre resposta rápida.

Para saber mais, leia a [documentação padrão de proteção contra DDoS](./ddos-protection-overview.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [testar por meio de simulações](test-through-simulations.md).
- Saiba como [Exibir e configurar a telemetria de proteção contra DDoS](telemetry.md).
- Saiba como [Exibir e configurar o log de diagnóstico de DDoS](diagnostic-logging.md).