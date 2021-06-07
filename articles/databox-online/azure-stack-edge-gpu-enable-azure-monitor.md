---
title: Habilitar Azure Monitor no dispositivo Azure Stack GPU pro Edge
description: Descreve como habilitar Azure Monitor em um dispositivo de GPU pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: c2f66895fccd14dcffd8c5570f1d5f46933090aa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439180"
---
# <a name="enable-azure-monitor-on-your-azure-stack-edge-pro-gpu-device"></a>Habilitar Azure Monitor em seu dispositivo de GPU pro Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Os contêineres de monitoramento em seu dispositivo de GPU pro Azure Stack Edge são críticos, especialmente quando você executa vários aplicativos de computação. Azure Monitor permite que você colete logs de contêiner e a memória e as métricas do processador do cluster kubernetes em execução em seu dispositivo.

Este artigo descreve as etapas necessárias para habilitar Azure Monitor em seu dispositivo e reunir logs de contêiner no espaço de trabalho Log Analytics. O repositório de métricas de Azure Monitor não tem suporte no momento com seu dispositivo de GPU pro Azure Stack Edge. 


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você precisará de:

- Um dispositivo Azure Stack Edge pro. Verifique se o dispositivo está ativado de acordo com as etapas no [tutorial: ativar seu dispositivo](azure-stack-edge-gpu-deploy-activate.md).
- Você concluiu a etapa **Configurar computação** de acordo com o [tutorial: configurar a computação em seu dispositivo Azure Stack Edge pro](azure-stack-edge-gpu-deploy-configure-compute.md) em seu dispositivo. Seu dispositivo deve ter um recurso de Hub IoT associado, um dispositivo IoT e um dispositivo IoT Edge.


## <a name="create-log-analytics-workspace"></a>Criar espaço de trabalho do Log Analytics

Execute as etapas a seguir para criar um espaço de trabalho do log Analytics. Um espaço de trabalho do log Analytics é uma unidade de armazenamento lógico em que os dados de log são coletados e armazenados.

1. Na portal do Azure, selecione **+ criar um recurso** e pesquise **log Analytics espaço de trabalho** e, em seguida, selecione **criar**. 
1. No **espaço de trabalho criar log Analytics**, defina as seguintes configurações. Aceite o restante como padrão.

    1. Na guia **noções básicas** , forneça a assinatura, o grupo de recursos, o nome e a região do espaço de trabalho. 

        ![Guia básico para Log Analytics espaço de trabalho](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-basics-1.png)  

    1. Na guia **tipo de preço** , aceite o **plano pago conforme o uso** padrão.

        ![Guia de preços do espaço de trabalho do Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-pricing-1.png) 

    1. Na guia **revisar + criar** , examine as informações do seu espaço de trabalho e selecione **criar**.

        ![Examinar + criar para Log Analytics espaço de trabalho](media/azure-stack-edge-gpu-enable-azure-monitor/create-log-analytics-workspace-review-create-1.png)

Para obter mais informações, consulte as etapas detalhadas em [criar um log Analytics espaço de trabalho via portal do Azure](../azure-monitor/logs/quick-create-workspace.md).



## <a name="enable-container-insights"></a>Habilitar informações de contêiner

Execute as etapas a seguir para habilitar o insights de contêiner em seu espaço de trabalho. 

1. Siga as etapas detalhadas na [solução como adicionar os contêineres de Azure monitor](../azure-monitor/containers/container-insights-hybrid-setup.md#how-to-add-the-azure-monitor-containers-solution). Use o seguinte arquivo de modelo `containerSolution.json` :

    ```yml
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
            }
        ]
    }
    ```

1. Obtenha a ID do recurso e o local. Ir para `Your Log Analytics workspace > General > Properties`. Copie as seguintes informações:

    - **ID de recurso**, que é a ID de recurso do Azure totalmente qualificada do espaço de trabalho do Azure log Analytics. 
    - **local**, que é a região do Azure.

    ![Propriedades do espaço de trabalho Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-properties-1.png) 

1. Use o arquivo de parâmetros a seguir `containerSolutionParams.json` . Substitua `workspaceResourceId` pela ID do recurso e `workspaceRegion` pelo local copiado na etapa anterior.

    ```yaml
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "workspaceResourceId": {
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
        },
        "workspaceRegion": {
        "value": "westus"
        }
        }
    }
    ```
    
    Aqui está um exemplo de saída de um espaço de trabalho Log Analytics com o contêiner insights habilitado:
    
    ```powershell
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...
    MOTD: Switch to Bash from PowerShell: bash
    VERBOSE: Authenticating to Azure ...
    VERBOSE: Building your Azure drive ...
    
    PS /home/alpa> az account set -s fa68082f-8ff7-4a25-95c7-ce9da541242f
    PS /home/alpa> ls
    clouddrive  containerSolution.json
    PS /home/alpa> ls
    clouddrive  containerSolution.json  containerSolutionParams.json
    PS /home/alpa> az deployment group create --resource-group myaserg --name Testdeployment1 --template-file containerSolution.json --parameters containerSolutionParams.json
    {- Finished ..
        "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.Resources/deployments/Testdeployment1",
        "location": null,
        "name": "Testdeployment1",
        "properties": {
        "correlationId": "3a9045fe-2de0-428c-b17b-057508a8c575",
        "debugSetting": null,
        "dependencies": [],
        "duration": "PT11.1588316S",
        "error": null,
        "mode": "Incremental",
        "onErrorDeployment": null,
        "outputResources": [
            {
            "id": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourceGroups/myaserg/providers/Microsoft.OperationsManagement/solutions/ContainerInsights(myaseloganalyticsws)",
            "resourceGroup": "myaserg"
            }
        ],
        "outputs": null,
        "parameters": {
            "workspaceRegion": {
            "type": "String",
            "value": "westus"
            },
            "workspaceResourceId": {
            "type": "String",
            "value": "/subscriptions/fa68082f-8ff7-4a25-95c7-ce9da541242f/resourcegroups/myaserg/providers/microsoft.operationalinsights/workspaces/myaseloganalyticsws"
            }
        },
        "parametersLink": null,
        "providers": [
            {
            "id": null,
            "namespace": "Microsoft.Resources",
            "registrationPolicy": null,
            "registrationState": null,
            "resourceTypes": [
                {
                "aliases": null,
                "apiProfiles": null,
                "apiVersions": null,
                "capabilities": null,
                "defaultApiVersion": null,
                "locations": [
                    null
                ],
                "properties": null,
                "resourceType": "deployments"
                }
            ]
            }
        ],
        "provisioningState": "Succeeded",
        "templateHash": "10500027184662969395",
        "templateLink": null,
        "timestamp": "2020-11-06T22:09:56.908983+00:00",
        "validatedResources": null
        },
        "resourceGroup": "myaserg",
        "tags": null,
        "type": "Microsoft.Resources/deployments"
    }
    PS /home/alpa>
    ```

## <a name="configure-azure-monitor-on-your-device"></a>Configurar Azure Monitor em seu dispositivo

1. Vá para o recurso de Log Analytics recém-criado e copie a **ID do espaço de trabalho** e a **chave primária** (chave do espaço de trabalho).

    ![Gerenciamento de agentes no espaço de trabalho Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-agents-management-1.png)

    Salve essas informações, pois você irá usá-las em uma etapa posterior.

1. [Conecte-se à interface do PowerShell do dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 
    
1. Use a ID e a chave do espaço de trabalho do log Analytics com o seguinte cmdlet:

    `Set-HcsKubernetesAzureMonitorConfiguration -WorkspaceId <> -WorkspaceKey <>`

1. Depois que o Azure Monitor estiver habilitado, você deverá ver os logs no espaço de trabalho Log Analytics. Para exibir o status do cluster kubernetes implantado em seu dispositivo, vá para **Azure Monitor > > contêineres de informações**. Para a opção de ambiente, selecione **tudo**. 

    ![Métricas no espaço de trabalho Log Analytics](media/azure-stack-edge-gpu-enable-azure-monitor/log-analytics-workspace-metrics-1.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba como [monitorar cargas de trabalho do kubernetes por meio do painel do kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md).
- Saiba como [gerenciar notificações de alerta de eventos de dispositivo](azure-stack-edge-gpu-manage-device-event-alert-notifications.md). 