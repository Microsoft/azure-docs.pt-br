---
title: Usar as recomendações da Central de Segurança do Azure para aprimorar a segurança | Microsoft Docs
description: " Saiba como usar as políticas de segurança e as recomendações da Central de Segurança do Azure para ajudar a atenuar um ataque de segurança. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 4c6b8b0bfa78dca5ca32c8c72edcc463ebb9057a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91322131"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Usar as recomendações da Central de Segurança do Azure para aprimorar a segurança

Você pode reduzir as chances de um evento de segurança significativo configurando uma política de segurança e implementando as recomendações fornecidas pela Central de Segurança do Azure. Esse artigo mostra como usar as políticas de segurança e as recomendações da Central de Segurança para ajudar a atenuar uma violação de segurança. 

A central de segurança executa verificações contínuas automaticamente para analisar o estado de segurança de seus recursos do Azure. Quando a Central de Segurança identifica possíveis vulnerabilidades de segurança, ela cria recomendações que guiam você pelo processo de configuração dos controles de segurança necessários. A central de segurança atualiza suas recomendações dentro de 24 horas, com as seguintes exceções:

- As recomendações de configuração de segurança do sistema operacional são atualizadas dentro de 48 horas
- As recomendações de Endpoint Protection problemas são atualizadas dentro de 8 horas

## <a name="scenario"></a>Cenário
Este cenário mostra como usar a Central de Segurança para ajudar a reduzir as chances de um incidente de segurança monitorando as recomendações da Central de Segurança e executando ações. O cenário usa a empresa fictícia Contoso e funções apresentadas no [guia de planejamento e operações](security-center-planning-and-operations-guide.md#security-roles-and-access-controls) da Central de Segurança. Neste cenário, estamos nos concentrando nas funções das seguintes personas:

![Funções de cenário](./media/security-center-using-recommendations/scenario-roles.png)

A Contoso migrou recentemente alguns dos seus recursos locais para o Azure. A Contoso deseja proteger seus recursos e reduzir a vulnerabilidade de seus recursos na nuvem.

## <a name="use-azure-security-center"></a>Usar a Central de Segurança do Azure
David, da segurança de TI da Contoso, já optou por integrar a Central de Segurança nas assinaturas da Contoso à Central de Segurança do Azure para evitar e detectar vulnerabilidades de segurança. 

A Central de Segurança analisa o estado de segurança dos recursos do Azure da Contoso e aplica as políticas de segurança padrão automaticamente. Quando a central de segurança identifica possíveis vulnerabilidades de segurança, ele cria **recomendações** com base nos controles definidos na política de segurança. 

David executa a segurança do Azure com o Azure defender habilitado, em todas as suas assinaturas para obter o pacote completo de recomendações e recursos de segurança disponíveis. Jeff também integra todos os servidores locais existentes que ainda não foram migrados para a nuvem para que eles possam aproveitar o suporte híbrido da central de segurança em seus [servidores Windows e Linux](quickstart-onboard-machines.md).

Jeff é proprietário da carga de trabalho de nuvem. Jeff é responsável por aplicar controles de segurança de acordo com as políticas de segurança da Contoso. 

Jeff realiza as seguintes tarefas:

- Monitorar as recomendações de segurança fornecidas pela Central de Segurança
- Avalie as recomendações de segurança e decida se elas devem aplicar ou ignorar as recomendações.
- Aplicar as recomendações de segurança

### <a name="remediate-threats-using-recommendations"></a>Corrigir ameaças usando recomendações
Como parte de suas atividades diárias de monitoramento, Jeff entra no Azure e abre a central de segurança. 

1. Jeff seleciona as assinaturas da carga de trabalho.

2. Jeff verifica a **Pontuação segura** para obter uma visão geral de como as assinaturas são seguras e vê que a pontuação é 548.

3. Jeff precisa decidir com quais recomendações deseja lidar primeiro. Então Jeff clica em Pontuação segura e começa a lidar com recomendações com base na quantidade de ti que melhora sua [Pontuação segura](secure-score-security-controls.md).

4. Como Jeff tem muitas VMs conectadas, Jeff decide se concentrar em seus computadores no [inventário de ativos](asset-inventory.md).

5. Quando Jeff abre o inventário de ativos, uma lista de recomendações é exibida. Jeff os manipula de acordo com o impacto de Pontuação segura.

6. Jeff tem várias VMs voltadas para a Internet e, como suas portas são expostas, eles estão preocupados com o fato de um invasor poder ter controle sobre os servidores. Portanto, Jeff escolhe usar o [**acesso à VM just-in-time**](security-center-just-in-time.md).

Jeff continua a seguir as recomendações de alta e média prioridade, tomando decisões quanto à implementação. Para cada recomendação, Jeff examina as informações detalhadas fornecidas pela central de segurança para entender quais recursos são afetados, qual é o impacto da Pontuação segura, o que cada recomendação significa e as etapas de correção para saber como atenuar cada problema.

### <a name="enforce-recommendations-to-prevent-security-misconfigurations"></a>Impor recomendações para impedir configurações incorretas de segurança

Para garantir que os usuários não criem recursos que afetam negativamente a pontuação de Jeff, eles configuram as opções de aplicação e negação nas recomendações mais importantes para elas. Saiba mais em [Impedir configurações incorretas com as recomendações de Impor/Negar](prevent-misconfigurations.md).


## <a name="conclusion"></a>Conclusão
Monitorar as recomendações na Central de Segurança ajuda a eliminar vulnerabilidades de segurança antes de uma violação ocorrer. Quando você corrige recomendações, sua pontuação segura e a postura de segurança de suas cargas de trabalho melhoram. A Central de Segurança detecta automaticamente os novos recursos que você implanta, os avalia em relação à sua política de segurança e fornece novas recomendações para protegê-los.


## <a name="next-steps"></a>Próximas etapas
Certifique-se de que você tem em vigor um processo de monitoramento em que você verifica regularmente as recomendações na Central de Segurança para que possa proteger seus recursos ao longo do tempo.

Esse cenário mostrou como usar as políticas de segurança e as recomendações da Central de Segurança para ajudar a atenuar uma violação de segurança.

Saiba como responder a ameaças com [Gerenciamento e resposta a alertas de segurança](security-center-managing-and-responding-alerts.md).
