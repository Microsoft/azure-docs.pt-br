---
title: Distribuição de integração de integridade-Gerenciador de Implantação do Azure
description: Descreve como implantar um serviço em muitas regiões com o Azure Deployment Manager. Ele mostra as práticas de implantação segura para verificar a estabilidade da sua implantação antes de distribuir a todas as regiões.
author: mumian
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: jgao
ms.openlocfilehash: ae95269dbac3ef1561e19d4b7ea5dd383c1eed73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99536960"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Introduzir a distribuição de integração de integridade para o Azure Gerenciador de Implantação (visualização pública)

O [Azure Gerenciador de implantação](./deployment-manager-overview.md) permite que você execute distribuições em etapas de recursos de Azure Resource Manager. Os recursos são implantados na região por região de maneira ordenada. A verificação de integridade integrada do Azure Gerenciador de Implantação pode monitorar as distribuições e parar automaticamente as distribuições problemáticas, para que você possa solucionar problemas e reduzir a escala do impacto. Esse recurso pode reduzir a indisponibilidade de serviço causada por regressões em atualizações.

## <a name="health-monitoring-providers"></a>Provedores de monitoramento de integridade

Para facilitar ao máximo a integração de integridade, a Microsoft tem trabalhado com algumas das principais empresas de monitoramento de integridade de serviço para fornecer uma solução copiar/colar simples visando integrar as verificações de integridade às suas implantações. Se você ainda não estiver usando um monitor de integridade, estas são excelentes soluções para começar:

| ![provedor de monitor de integridade do Azure monitor do Azure](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-azure-monitor.svg)| ![provedor de monitoramento de integridade do Gerenciador de implantação do Azure datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![provedor de monitoramento de integridade do Gerenciador de implantação do Azure site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![provedor de monitoramento de integridade do Gerenciador de implantação do Azure Wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|-----|------|------|
|Azure Monitor, a plataforma de observação de pilha completa da Microsoft para nuvem nativa & monitoramento e análise híbridas. |Datadog, a principal plataforma de monitoramento e análise para ambientes de nuvem modernos. Veja [como o Datadog se integra ao Azure Gerenciador de implantação](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, a solução de monitoramento de serviços de nuvem pública e privado tudo em um. Veja [como o Site24x7 se integra ao Azure Gerenciador de implantação](https://www.site24x7.com/azure/adm.html).| Wavefront, a plataforma de monitoramento e análise para ambientes de aplicativos de várias nuvens. Veja [como o Wavefront se integra ao Azure Gerenciador de implantação](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Como a integridade do serviço é determinada

Os [provedores de monitoramento de integridade](#health-monitoring-providers) oferecem vários mecanismos para monitorar serviços e alertá-lo sobre qualquer problema de integridade do serviço. [Azure monitor](../../azure-monitor/overview.md) é um exemplo de uma dessas ofertas. Azure Monitor pode ser usado para criar alertas quando determinados limites são excedidos. Por exemplo, o pico de utilização da memória e da CPU ultrapassa os níveis esperados quando você implanta uma nova atualização para seu serviço. Quando notificado, você pode tomar medidas corretivas.

Esses provedores de integridade normalmente oferecem APIs REST para que o status dos monitores de seu serviço possa ser examinado programaticamente. As APIs REST podem voltar com um simples sinal íntegro/não íntegro (determinado pelo código de resposta HTTP) e/ou com informações detalhadas sobre os sinais que ele está recebendo.

A nova `healthCheck` etapa no Azure Gerenciador de implantação permite que você declare códigos http que indicam um serviço íntegro. Para resultados de REST complexos, você pode especificar expressões regulares que, quando combinadas, indicam uma resposta íntegra.

O fluxo para configurar as verificações de integridade do Azure Gerenciador de Implantação:

1. Crie seus monitores de integridade por meio de um provedor de serviços de integridade de sua escolha.
1. Crie uma ou mais `healthCheck` etapas como parte de sua distribuição de Gerenciador de implantação do Azure. Preencha as `healthCheck` etapas com as seguintes informações:

    1. O URI para a API REST para seus monitores de integridade (conforme definido pelo seu provedor de serviços de integridade).
    1. Informações de autenticação. Atualmente, somente a autenticação de estilo de chave de API tem suporte. Por Azure Monitor, o tipo de autenticação deve ser definido como `RolloutIdentity` a identidade gerenciada atribuída pelo usuário usada para a distribuição de Gerenciador de implantação do Azure se estende para o Azure monitor.
    1. [Códigos de status http](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) ou expressões regulares que definem uma resposta íntegra. Você pode fornecer expressões regulares, as quais todas devem corresponder para que a resposta seja considerada íntegra, ou você pode fornecer expressões das quais qualquer um deve corresponder para que a resposta seja considerada íntegra. Os dois métodos têm suporte.

    O JSON a seguir é um exemplo para integrar Azure Monitor com o Gerenciador de Implantação do Azure. O exemplo usa `RolloutIdentity` e estabelece uma verificação de integridade em que uma distribuição prossegue se não houver nenhum alerta. A única API Azure Monitor com suporte: [alertas – obter tudo](/rest/api/monitor/alertsmanagement/alerts/getall).

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT1M",
          "maxElasticDuration": "PT1M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "RolloutIdentity"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "\"value\":\\[\\]"
                    ],
                    "matchQuantifier": "All"
                  }
                }
              }
            ]
          }
        }
      }
    }
    ```

    O JSON a seguir é um exemplo para todos os outros provedores de monitoramento de integridade:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Invoque as `healthCheck` etapas no momento apropriado em sua distribuição de Gerenciador de implantação do Azure. No exemplo a seguir, uma `healthCheck` etapa é invocada em `postDeploymentSteps` de `stepGroup2` .

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Para percorrer um exemplo, consulte [tutorial: usar a verificação de integridade no Azure Gerenciador de implantação](./deployment-manager-tutorial-health-check.md).

## <a name="phases-of-a-health-check"></a>Fases de uma verificação de integridade

Neste ponto, o Azure Gerenciador de Implantação sabe como consultar a integridade do seu serviço e em quais fases em sua distribuição para fazer isso. No entanto, o Azure Gerenciador de Implantação também permite uma configuração profunda do tempo dessas verificações. Uma `healthCheck` etapa é executada em três fases sequenciais, todas com durações configuráveis:

1. Aguarde

    1. Após a conclusão de uma operação de implantação, as VMs podem ser reinicializadas, reconfiguradas com base em novos dados ou até serem iniciadas pela primeira vez. Também leva tempo para que os serviços comecem a emitir sinais de integridade para serem agregados pelo provedor de monitoramento de integridade em algo útil. Durante esse processo de tumultuada, talvez não faça sentido verificar a integridade do serviço, pois a atualização ainda não atingiu um estado estável. Na verdade, o serviço pode ser oscillating entre Estados íntegros e não íntegros à medida que os recursos são liquidados.
    1. Durante a fase de espera, a integridade do serviço não é monitorada. Isso é usado para permitir que os recursos implantados detortam antes de iniciar o processo de verificação de integridade.

1. Elástico

    1. Como é impossível saber em todos os casos, quanto tempo levará antes que os recursos sejam estáveis, a fase elástica permite um período de tempo flexível entre o momento em que os recursos são potencialmente instáveis e quando eles são necessários para manter um estado íntegro estável.
    1. Quando a fase elástica começa, o Azure Gerenciador de Implantação começa a sondar o ponto de extremidade REST fornecido para integridade do serviço periodicamente. O intervalo de sondagem é configurável.
    1. Se o Health Monitor voltar com sinais indicando que o serviço não está íntegro, esses sinais serão ignorados, a fase elástica continuará e a sondagem continuará.
    1. Quando o monitor de integridade retorna sinais indicando que o serviço está íntegro, a fase elástica termina e a fase Íntegrostate começa.
    1. Assim, a duração especificada para a fase elástica é a quantidade máxima de tempo que pode ser gasta sondando a integridade do serviço antes que uma resposta íntegra seja considerada obrigatória.

1. HealthyState

    1. Durante a fase Íntegrostate, a integridade do serviço é sondada continuamente no mesmo intervalo da fase elástica.
    1. Espera-se que o serviço mantenha sinais íntegros do provedor de monitoramento de integridade para toda a duração especificada.
    1. Se a qualquer momento uma resposta não íntegra for detectada, o Azure Gerenciador de Implantação interromperá toda a distribuição e retornará a resposta REST que está realizando os sinais de serviço não íntegros.
    1. Após o término da duração de íntegro, o `healthCheck` está concluído e a implantação continua na próxima etapa.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre como integrar o monitoramento de integridade no Azure Gerenciador de Implantação. Vá para o próximo artigo para saber como implantar com o Gerenciador de implantação.

> [!div class="nextstepaction"]
> [Tutorial: usar a verificação de integridade no Azure Gerenciador de Implantação](./deployment-manager-tutorial-health-check.md)
