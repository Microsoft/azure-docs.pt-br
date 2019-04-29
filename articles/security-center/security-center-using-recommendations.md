---
title: Usar as recomendações da Central de Segurança do Azure para aprimorar a segurança | Microsoft Docs
description: " Saiba como usar as políticas de segurança e as recomendações da Central de Segurança do Azure para ajudar a atenuar um ataque de segurança. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: monhaber
ms.openlocfilehash: 82a46ae9523c4c2778f86c445e35d0bec961307f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703432"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Usar as recomendações da Central de Segurança do Azure para aprimorar a segurança
Você pode reduzir as chances de um evento de segurança significativo configurando uma política de segurança e implementando as recomendações fornecidas pela Central de Segurança do Azure. Esse artigo mostra como usar as políticas de segurança e as recomendações da Central de Segurança para ajudar a atenuar uma violação de segurança. 

A Central de segurança é executada automaticamente verificações contínuas para analisar o estado de segurança dos recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, ela cria recomendações que guiam você pelo processo de configuração dos controles de segurança necessários. A Central de segurança atualiza suas recomendações dentro de 24 horas, com as seguintes exceções:

- Recomendações de configuração de segurança do sistema operacional são atualizadas dentro de 48 horas
- Recomendações de problemas de proteção de ponto de extremidade são atualizadas dentro de 8 horas

## <a name="scenario"></a>Cenário
Este cenário mostra como usar a Central de Segurança para ajudar a reduzir as chances de um incidente de segurança monitorando as recomendações da Central de Segurança e executando ações. O cenário usa a empresa fictícia Contoso e funções apresentadas no [guia de planejamento e operações](security-center-planning-and-operations-guide.md#security-roles-and-access-controls) da Central de Segurança. Neste cenário, estamos nos concentrando nas funções das seguintes personas:

![Funções de cenário](./media/security-center-using-recommendations/scenario-roles.png)

A Contoso migrou recentemente alguns dos seus recursos locais para o Azure. A Contoso deseja proteger seus recursos e reduzir a vulnerabilidade de seus recursos na nuvem.

## <a name="use-azure-security-center"></a>Usar a Central de Segurança do Azure
David, da segurança de TI da Contoso, já optou por integrar a Central de Segurança nas assinaturas da Contoso à Central de Segurança do Azure para evitar e detectar vulnerabilidades de segurança. 

A Central de Segurança analisa o estado de segurança dos recursos do Azure da Contoso e aplica as políticas de segurança padrão automaticamente. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, ela cria **recomendações** com base nos controles definidos na política de segurança. 

David executa a camada padrão de segurança do Azure em todas as suas assinaturas para obter o conjunto completo de recomendações e recursos de segurança disponíveis. Jeff também integra todos os seus servidores locais existentes que ainda não foram migrados para a nuvem para que possa tirar proveito do suporte híbrido da Central de Segurança em seus servidores [Windows](quick-onboard-windows-computer.md) e [Linux](quick-onboard-linux-computer.md).

Jeff é proprietário da carga de trabalho de nuvem. Jeff é responsável por aplicar controles de segurança de acordo com as políticas de segurança da Contoso. 

Jeff realiza as seguintes tarefas:

- Monitorar as recomendações de segurança fornecidas pela Central de Segurança
- Avaliar as recomendações de segurança e decidir quais ele deve aplicar ou ignorar
- Aplicar as recomendações de segurança

### <a name="remediate-threats-using-recommendations"></a>Corrigir ameaças usando recomendações
Como parte de suas atividades diárias de monitoramento, Jeff entra no Azure e abre a Central de Segurança. 

1. Jeff seleciona assinaturas da sua carga de trabalho.

2. Jeff verifica sua **classificação de segurança** para ter uma visão geral do quanto as assinaturas são seguras e vê que a sua classificação é de 548.

3. Jeff precisa decidir com quais recomendações deseja lidar primeiro. Sendo assim, Jeff clica na classificação de segurança e começa a lidar com as recomendações com base no quanto elas melhoram o [impacto da classificação de segurança](security-center-secure-score.md).

4. Como Jeff tem muitos servidores e VMs conectadas, ele decide se concentrar em **Computação e aplicativos**.

5. Quando clica em **Computação e aplicativos**, Jeff vê uma lista de recomendações e trata delas de acordo com o impacto sobre a classificação de segurança.

6. Jeff tem inúmeras VMs voltadas para a Internet e, como suas portas são expostas, ele tem medo de que um invasor possa obter controle dos servidores. Portanto, Jeff opta por usar o (**acesso a VM Just-In-Time**) [security-center-just-in-time.md].

Jeff continua a seguir as recomendações de alta e média prioridade, tomando decisões quanto à implementação. Para cada recomendação, ele analisa as informações detalhadas fornecidas pela Central de Segurança para entender quais recursos são afetados, qual é o impacto sobre a classificação de segurança, o que cada recomendação significa e quais são as etapas de correção para atenuar cada problema.

## <a name="conclusion"></a>Conclusão
Monitorar as recomendações na Central de Segurança ajuda a eliminar vulnerabilidades de segurança antes de uma violação ocorrer. Quando você corrige as recomendações, sua classificação de segurança e a postura de segurança de suas cargas de trabalho melhoram. A Central de Segurança detecta automaticamente os novos recursos que você implanta, os avalia em relação à sua política de segurança e fornece novas recomendações para protegê-los.


## <a name="next-steps"></a>Próximas etapas
Certifique-se de que você tem em vigor um processo de monitoramento em que você verifica regularmente as recomendações na Central de Segurança para que possa proteger seus recursos ao longo do tempo.

Esse cenário mostrou como usar as políticas de segurança e as recomendações da Central de Segurança para ajudar a atenuar uma violação de segurança. Consulte o [cenário de resposta a incidente](security-center-incident-response.md) para saber como ter um plano de resposta a incidente em vigor antes que ocorra um ataque.

Saiba como responder a ameaças com a [resposta a incidentes](security-center-incident-response.md).
