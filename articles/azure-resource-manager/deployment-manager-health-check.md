---
title: Introduzir a distribuição de integração de integridade para o Gerenciador de implantação do Azure
description: Descreve como implantar um serviço em muitas regiões com o Azure Deployment Manager. Ele mostra as práticas de implantação segura para verificar a estabilidade da sua implantação antes de distribuir a todas as regiões.
services: azure-resource-manager
documentationcenter: na
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: 0c6d32f06e30bd85c12967ce4b15a053bb505ab7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594318"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Introduzir a distribuição de integração de integridade para o Azure Deployment Manager (visualização pública)

[O Gerenciador de implantação do Azure](./deployment-manager-overview.md) permite que você execute as distribuições em estágios dos recursos do Azure Resource Manager. Os recursos são implantados região por região de maneira ordenada. A verificação de integridade integrados do Gerenciador de implantação do Azure pode monitorar as distribuições e automaticamente as distribuições problemático de parada, para que você possa solucionar problemas e reduzir a escala do impacto. Esse recurso pode reduzir causada por regressões em atualizações de indisponibilidade de serviço.

## <a name="health-monitoring-providers"></a>Provedores de monitoramento de integridade

Para facilitar ao máximo a integração de integridade, a Microsoft tem trabalhado com algumas das principais empresas de monitoramento de integridade de serviço para fornecer uma solução copiar/colar simples visando integrar as verificações de integridade às suas implantações. Se você ainda não estiver usando um monitor de integridade, estes são soluções excelentes para começar:

| ![implantação do Azure manager health monitor provedor datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![implantação do Azure manager health monitor provedor site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![implantação do Azure manager health monitor provedor wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, o monitoramento principal e plataforma de análise para ambientes de nuvem modernos. Ver [como o Datadog se integra com o Gerenciador de implantação do Azure](https://www.datadoghq.com/azure-deployment-manager/).|Site24x7, serviços de nuvem pública e privada em um solução de monitoramento. Ver [como Site24x7 se integra com o Gerenciador de implantação do Azure](https://www.site24x7.com/azure/adm.html).| Wavefront, a plataforma de análise e monitoramento para ambientes de nuvem com vários aplicativos. Ver [como Wavefront se integra com o Gerenciador de implantação do Azure](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>Como a integridade do serviço é determinada

[Provedores de monitoramento de integridade](#health-monitoring-providers) oferecem vários mecanismos para serviços de monitoramento e alerta você de quaisquer problemas de integridade do serviço. [O Azure Monitor](../azure-monitor/overview.md) é um exemplo de um tal oferta. O Azure Monitor pode ser usado para criar alertas quando determinados limites forem excedidos. Por exemplo, sua utilização de memória e CPU pico além dos níveis esperados quando você implanta uma nova atualização em seu serviço. Quando receber a notificação, você pode tomar ações corretivas.

Normalmente, esses provedores de integridade oferecem APIs REST para que o status dos monitores do seu serviço pode ser examinado por meio de programação. As APIs REST pode vir novamente com um sinal íntegros/não íntegros simple (determinado pelo código de resposta de HTTP) e/ou com informações detalhadas sobre os sinais que está recebendo.

O novo *healthCheck* etapa no Gerenciador de implantação do Azure permite que você declare códigos HTTP que indiquem um serviço Íntegro ou, para resultados mais complexos de REST, você pode até especificar expressões regulares que, se elas corresponderem, indicam um Íntegro resposta.

O fluxo para a conclusão da instalação com verificações de integridade do Gerenciador de implantação do Azure:

1. Crie os monitores de integridade por meio de um provedor de serviço de integridade de sua escolha.
1. Crie uma ou mais etapas de verificação de integridade como parte da sua distribuição do Gerenciador de implantação do Azure. Preencha as etapas de verificação de integridade com as seguintes informações:

    1. O URI para a API REST para os monitores de integridade (conforme definidos por seu provedor de serviço de integridade).
    1. Informações de autenticação. Atualmente, somente autenticação de estilo de chave de API tem suporte.
    1. [Códigos de status HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) ou expressões regulares que definem uma resposta íntegra. Observe que você pode fornecer expressões regulares, que devem todas as correspondência para a resposta a ser considerada íntegra, ou você pode fornecer expressões que qualquer devem corresponder para que a resposta para ser considerado íntegro. Ambos os métodos são suportados.

    O Json a seguir está um exemplo:

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

1. Invocar as etapas de verificação de integridade no momento apropriado em sua distribuição do Gerenciador de implantação do Azure. No exemplo a seguir, uma etapa de verificação de integridade é invocada no **postDeploymentSteps** dos **stepGroup2**.

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

Para percorrer um exemplo, consulte [Tutorial: Usar a verificação de integridade no Gerenciador de Implantação do Azure](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Fases de uma verificação de integridade

Neste momento o Gerenciador de implantação do Azure sabe como consultar a integridade do seu serviço e em quais fases em sua distribuição para fazer isso. No entanto, o Gerenciador de implantação do Azure também permite configuração profunda do cronograma dessas verificações. Uma etapa de verificação de integridade é executada em 3 fases sequenciais, todas com durações configuráveis: 

1. Aguarde

    1. Após uma operação de implantação, as VMs podem ser reinicializar, reconfigurar com base nos novos dados ou até mesmo sendo iniciado pela primeira vez. Ele também leva tempo para os serviços começar a emitir sinais de integridade agregados pelo provedor de monitoramento em algo útil de integridade. Durante esse processo tumultuado, talvez não faça sentido para verificar a integridade do serviço, pois a atualização ainda não atingiu um estado estável. Na verdade, o serviço pode ser oscilando para parar entre estados íntegros e não íntegros como liquidar os recursos. 
    1. Durante a fase de espera, a integridade do serviço não é monitorada. Isso é usado para permitir que os recursos implantados o tempo para colocá-lo antes de iniciar o processo de verificação de integridade. 
1. Elástico

    1. Como é impossível saber em todos os casos quanto recursos levará implantado antes que eles se tornam estáveis, permite que a fase de elástica por um período de tempo flexível entre quando os recursos estiverem possivelmente instáveis e quando eles são necessários para manter uma constante íntegra estado.
    1. Quando começa a fase de elástica, o Gerenciador de implantação do Azure começa a sondar o ponto de extremidade REST fornecido para a integridade do serviço periodicamente. O intervalo de sondagem é configurável. 
    1. Se o monitor de integridade de volta com sinais indicando que o serviço não está íntegro, esses sinais são ignoradas, continua a fase de elástica e continua de sondagem. 
    1. Assim que o monitor de integridade é fornecido com sinais que indica que o serviço esteja íntegro, elástica começa a fase termina e a fase de HealthyState. 
    1. Portanto, a duração especificada para a fase de elástica é a quantidade máxima de tempo que pode ser gasto sondando a integridade do serviço antes de uma resposta íntegra considerada obrigatória. 
1. HealthyState

    1. Durante a fase de HealthyState, integridade do serviço é continuamente sondada no mesmo intervalo como a fase de Elástico. 
    1. O serviço deve manter saudáveis sinais do provedor de monitoramento de integridade durante todo o período especificado. 
    1. Se a qualquer momento uma resposta não íntegro é detectada, o Gerenciador de implantação do Azure interromperá a distribuição de inteira e retornar a resposta REST carregar os sinais de serviço não íntegro.
    1. Depois que a duração de HealthyState tiver terminado, a verificação de integridade for concluída e implantação continuará para a próxima etapa.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre como integrar o monitoramento de integridade no Gerenciador de implantação do Azure. Vá para o próximo artigo para saber como implantar com o Gerenciador de implantação.

> [!div class="nextstepaction"]
> [Tutorial: integrar o verificação de integridade do Gerenciador de implantação do Azure](./deployment-manager-tutorial-health-check.md)