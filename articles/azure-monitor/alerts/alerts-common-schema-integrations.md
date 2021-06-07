---
title: Como integrar o esquema de alerta comum com aplicativos lógicos
description: Saiba como criar um aplicativo lógico que aproveita o esquema de alerta comum para lidar com todos os seus alertas.
ms.topic: conceptual
ms.date: 05/27/2019
ms.openlocfilehash: 459ac43a6260e046befbe58d3531bac494903d51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038332"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Como integrar o esquema de alerta comum com aplicativos lógicos

Este artigo mostra como criar um aplicativo lógico que aproveita o esquema de alerta comum para lidar com todos os seus alertas.

## <a name="overview"></a>Visão geral

O [esquema de alerta comum](./alerts-common-schema.md) fornece um esquema JSON padronizado e extensível em todos os tipos de alerta diferentes. O esquema de alerta comum é mais útil quando aproveitado programaticamente – por meio de WebHooks, runbooks e aplicativos lógicos. Neste artigo, demonstramos como um único aplicativo lógico pode ser criado para lidar com todos os seus alertas. Os mesmos princípios podem ser aplicados a outros métodos programáticos. O aplicativo lógico descrito neste artigo cria variáveis bem definidas para os [campos ' essenciais '](alerts-common-schema-definitions.md#essentials)e também descreve como você pode manipular a lógica específica do [tipo de alerta](alerts-common-schema-definitions.md#alert-context) .


## <a name="prerequisites"></a>Pré-requisitos 

Este artigo pressupõe que o leitor esteja familiarizado com 
* Configurando regras de alerta ([métrica](../alerts/alerts-metric.md), [log](./alerts-log.md), [log de atividades](./alerts-activity-log.md))
* Configurando [grupos de ação](./action-groups.md)
* Habilitando o [esquema de alerta comum](./alerts-common-schema.md#how-do-i-enable-the-common-alert-schema) de dentro dos grupos de ação

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Criar um aplicativo lógico aproveitando o esquema de alerta comum

1. Siga as [etapas descritas para criar seu aplicativo lógico](./action-groups-logic-app.md). 

1.  Selecione o gatilho **Quando uma solicitação HTTP for recebida**.

    ![Gatilhos do aplicativo lógico](media/action-groups-logic-app/logic-app-triggers.png "Gatilhos do aplicativo lógico")

1.  Selecione **Editar** para alterar o gatilho de solicitação HTTP.

    ![Gatilhos de solicitação HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Gatilhos de solicitação HTTP")


1.  Copie e cole o esquema a seguir:

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Selecione **+** **nova etapa** e, em seguida, escolha **Adicionar uma ação**.

    ![Adicionar uma ação](media/action-groups-logic-app/add-action.png "Adicionar uma ação")

1. Neste estágio, você pode adicionar uma variedade de conectores (Microsoft Teams, margem de atraso, Salesforce, etc.) com base em seus requisitos específicos de negócios. Você pode usar os ' campos essenciais ' prontos para uso. 

    ![Campos essenciais](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Campos essenciais")
    
    Como alternativa, você pode criar a lógica condicional com base no tipo de alerta usando a opção ' Expression '.

    ![Expressão de aplicativo lógico](media/alerts-common-schema-integrations/logic-app-expressions.png "Expressão de aplicativo lógico")
    
     O [campo ' monitoringService '](alerts-common-schema-definitions.md#alert-context) permite que você identifique exclusivamente o tipo de alerta, com base no qual você pode criar a lógica condicional.

    
    Por exemplo, o trecho de código abaixo verifica se o alerta é um alerta de log baseado Application Insights e, em caso positivo, imprime os resultados da pesquisa. Caso contrário, ele imprimirá ' NA '.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Saiba mais sobre como [escrever expressões de aplicativo lógico](../../logic-apps/workflow-definition-language-functions-reference.md#logical-comparison-functions).

    


## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre grupos de ações](./action-groups.md).
* [Saiba mais sobre o esquema de alerta comum](./alerts-common-schema.md).