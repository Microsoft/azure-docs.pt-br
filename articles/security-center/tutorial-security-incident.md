---
title: Tutorial de resposta a alertas – Central de Segurança do Azure
description: Neste tutorial, você aprenderá a fazer a triagem de alertas de segurança e a determinar a causa raiz e o escopo de um alerta.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 181e3695-cbb8-4b4e-96e9-c4396754862f
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 41f96202bffc0169be9705cd7e67c1eedbecbc05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102453437"
---
# <a name="tutorial-triage-investigate-and-respond-to-security-alerts"></a>Tutorial: Fazer a triagem, investigar e responder a alertas de segurança
A Central de Segurança analisa continuamente as cargas de trabalho de nuvem híbrida usando a análise avançada e a inteligência contra ameaças para alertar você quanto à presença de atividades potencialmente mal-intencionadas em seus recursos de rede. Você também pode integrar alertas de outros produtos e serviços de segurança à Central de Segurança. Após um alerta ser gerado, é necessária uma ação rápida para investigação e correção do possível problema de segurança. 

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Classificação de alertas de segurança
> * Investigar um alerta de segurança a fim de determinar a causa raiz
> * Responder a um alerta de segurança e atenuar a causa raiz

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Para acompanhar os recursos abordados neste tutorial, você deve ter o Azure Defender habilitado. Você pode experimentar o Azure Defender sem custo. Para saber mais, consulte a [página de preços](https://azure.microsoft.com/pricing/details/security-center/). O início rápido [Introdução à Central de Segurança](security-center-get-started.md) explica passo a passo como atualizar.


## <a name="triage-security-alerts"></a>Classificação de alertas de segurança
A Central de Segurança fornece uma visualização unificada de todos os alertas de segurança. Os alertas de segurança são classificados com base na severidade da atividade detectada. 

Faça a triagem dos alertas na página **Alertas de segurança**:

:::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lista de alertas de segurança da Central de Segurança do Azure":::

Use esta página para examinar os alertas de segurança ativos em seu ambiente a fim de decidir qual alerta investigar primeiro.

Ao fazer a triagem dos alertas de segurança, priorize os alertas com base na severidade, tratando primeiro daqueles com severidade mais alta. Saiba mais sobre a severidade do alerta em [Como os alertas são classificados?](security-center-alerts-overview.md#how-are-alerts-classified).

> [!TIP]
> Você pode conectar a Central de Segurança do Azure às soluções de SIEM mais populares, incluindo o Azure Sentinel, e consumir os alertas da ferramenta de sua escolha. Saiba mais em [Transmitir alertas para uma solução SIEM, SOAR ou Gerenciamento de Serviços de TI](export-to-siem.md).


## <a name="investigate-a-security-alert"></a>Investigar um alerta de segurança

Quando você decidir qual alerta investigar primeiro:

1. Selecionar o alerta desejado.
1. Na página de visão geral do alerta, selecione o recurso a investigar primeiro.
1. Comece a investigação no painel esquerdo, que mostra as informações de alto nível sobre o alerta de segurança.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-left-pane.png" alt-text="O painel esquerdo da página de detalhes do alerta, destacando as informações de alto nível":::

    Este painel mostra:
    - A severidade, o status e a hora da atividade do alerta
    - Descrição que explica a atividade precisa que foi detectada
    - Recursos afetados
    - Encerrar a intenção da cadeia da atividade na matriz MITRE ATT&CK

1. Para obter informações mais detalhadas que possam ajudar você a investigar a atividade suspeita, examine a guia **Detalhes do alerta**.

1. Quando tiver analisado as informações nessa página, talvez você tenha o suficiente para prosseguir com uma resposta. Se precisar de mais detalhes:

    - Entre em contato com o proprietário do recurso para verificar se a atividade detectada é um falso positivo.
    - Investigue os logs brutos gerados pelo recurso atacado

## <a name="respond-to-a-security-alert"></a>Responder a um alerta de segurança
Após investigar um alerta e compreender seu escopo, você pode responder a um alerta de segurança na Central de Segurança do Azure:

1.  Abra a guia **Executar ação** para ver as respostas recomendadas.

    :::image type="content" source="./media/tutorial-security-incident/alert-details-take-action.png" alt-text="Guia Executar ação dos alertas de segurança" lightbox="./media/tutorial-security-incident/alert-details-take-action.png":::

1.  Examine na seção **Atenuar a ameaça** as etapas de investigação manual necessárias para atenuar o problema.
1.  Para proteger seus recursos e evitar ataques futuros desse tipo, corrija as recomendações de segurança na seção **Evitar ataques futuros**.
1.  Para disparar um aplicativo lógico com etapas de resposta automatizadas, use a seção **Disparar resposta automatizada**.
1.  Se a atividade detectada *não* for mal intencionada, suprima alertas futuros desse tipo usando a seção **Suprimir alertas semelhantes**.

1.  Quando tiver concluído a investigação do alerta e respondido da maneira apropriada, altere o status para **Ignorado**.

    :::image type="content" source="./media/tutorial-security-incident/set-status-dismissed.png" alt-text="Definindo o status de um alerta":::

    Isso remove o alerta da lista de alertas principais. Use o filtro na página de lista de alertas para exibir todos os alertas com status de **Ignorado**.

1.  Incentivamos você a fornecer comentários sobre o alerta à Microsoft:
    1. Marque o alerta como **Útil** ou **Não útil**.
    1. Selecione um motivo e adicione um comentário.

        :::image type="content" source="./media/tutorial-security-incident/alert-feedback.png" alt-text="Fornecer comentários à Microsoft sobre a utilidade de um alerta":::

    > [!TIP]
    > Analisamos seus comentários para aprimorar nossos algoritmos e fornecer alertas de segurança melhores.

## <a name="end-the-tutorial"></a>Encerrar o tutorial

Outros guias de início rápido e tutoriais da coleção aproveitam esse guia de início rápido. Se pretender continuar trabalhando com os próximos tutoriais e inícios rápidos, mantenha o provisionamento automático e o Azure Defender habilitados. 

Se você não planeja continuar ou se deseja desabilitar um desses recursos:

1. retorne ao menu principal da Central de Segurança e selecione **Preços e configurações**.
1. Selecione a assinatura relevante.
1. Para fazer downgrade, selecione **Azure Defender desativado**.
1. Para desabilitar o provisionamento automático, abra a página **Coleção de Dados** e defina **Provisionamento automático** como **Desativado**.
1. Clique em **Salvar**.

>[!NOTE]
> Desabilitar o provisionamento automático não remove o agente do Log Analytics das VMs do Azure que já têm o agente. Desabilitar o provisionamento automático limita o monitoramento de segurança dos seus recursos.
>

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu sobre os recursos da Central de Segurança a serem usados ao responder a um alerta de segurança. Para obter materiais relacionados, confira:

- [Responder a alertas do Azure Defender para Key Vault](defender-for-key-vault-usage.md)
- [Alertas de segurança – um guia de referência](alerts-reference.md)
- [Introdução ao Azure Defender](azure-defender.md)
