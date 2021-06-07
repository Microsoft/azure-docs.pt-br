---
title: Criar um espaço de trabalho do Log Analytics usando a CLI do Azure | Microsoft Docs
description: Saiba como criar um espaço de trabalho do Log Analytics para habilitar as soluções de gerenciamento e a coleta de dados da sua nuvem e dos ambientes locais com a CLI do Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: 8e1fc816e32e563161e1604bdcd7a7006353e4ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047376"
---
# <a name="create-a-log-analytics-workspace-with-azure-cli-20"></a>Criar um espaço de trabalho do Log Analytics com a CLI do Azure 2.0

A CLI do Azure 2.0 é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este início rápido mostra como usar a CLI do Azure 2.0 para implantar um espaço de trabalho do Log Analytics no Azure Monitor. Um espaço de trabalho do Log Analytics é um ambiente exclusivo para dados de log do Azure Monitor. Cada workspace tem seu próprio repositório de dados e configuração. As fontes de dados e as soluções são configuradas para armazenar seus dados em um determinado workspace. Você precisa de um espaço de trabalho do Log Analytics se pretender coletar dados das seguintes fontes:

* Recursos do Azure em sua assinatura  
* Computadores locais monitorados pelo System Center Operations Manager  
* Coleções de dispositivos do Configuration Manager  
* Dados de diagnóstico ou de log do armazenamento do Azure  

Para outras fontes, como as VMs do Azure e as VMs do Windows ou do Linux em seu ambiente, veja os tópicos a seguir:

* [Coletar dados de máquinas virtuais do Azure](../vm/quick-collect-azurevm.md)
* [Coletar dados de um computador Linux híbrido](../vm/quick-collect-linux-computer.md)
* [Coletar dados de um computador Windows híbrido](../vm/quick-collect-windows-computer.md)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo exige a versão 2.0.30 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-workspace"></a>Criar um workspace
Crie um espaço de trabalho com [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create). O exemplo a seguir cria um espaço de trabalho no local *eastus* usando um modelo do Resource Manager do computador local. O modelo JSON está configurado para solicitar apenas o nome do workspace e especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão em seu ambiente. Ou ainda armazenar o modelo em uma conta de armazenamento do Azure para acesso compartilhado na sua organização. Para obter mais informações sobre como trabalhar com modelos, veja [Implantar recursos com modelos do Resource Manager e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md)

Para saber mais sobre regiões com suporte, confira [regiões em que o Log Analytics está disponível](https://azure.microsoft.com/regions/services/) e pesquise Azure Monitor no campo **Pesquisar por um produto**.

Os parâmetros a seguir definem um valor padrão:

* local – assume por padrão o valor Leste dos EUA
* SKU – assume por padrão o novo tipo de preço por GB lançado no modelo de preço de abril de 2018

>[!WARNING]
>Se criar ou configurar um espaço de trabalho do Log Analytics em uma assinatura que tiver aceitado o novo modelo de preços de abril de 2018, o único tipo de preço válido do Log Analytics **PerGB2018**.
>

### <a name="create-and-deploy-template"></a>Criar e implantar modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "type": "String",
            "metadata": {
              "description": "Specifies the name of the workspace."
            }
        },
        "location": {
            "type": "String",
            "allowedValues": [
              "eastus",
              "westus"
            ],
            "defaultValue": "eastus",
            "metadata": {
              "description": "Specifies the location in which to create the workspace."
            }
        },
        "sku": {
            "type": "String",
            "allowedValues": [
              "Standalone",
              "PerNode",
              "PerGB2018"
            ],
            "defaultValue": "PerGB2018",
            "metadata": {
            "description": "Specifies the service tier of the workspace: Standalone, PerNode, Per-GB"
        }
          }
    },
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "name": "[parameters('workspaceName')]",
            "apiVersion": "2015-11-01-preview",
            "location": "[parameters('location')]",
            "properties": {
                "sku": {
                    "Name": "[parameters('sku')]"
                },
                "features": {
                    "searchVersion": 1
                }
            }
          }
       ]
    }
    ```

2. Edite o modelo para atender às suas necessidades. Revisão de referência[Microsoft.OperationalInsights/workspaces](/azure/templates/microsoft.operationalinsights/2015-11-01-preview/workspaces) para saber quais propriedades e os valores são suportados.
3. Salve esse arquivo como **deploylaworkspacetemplate.json** para uma pasta local.   
4. Você está pronto para implantar o modelo. Use os seguintes comandos na pasta que contém o modelo. Quando for solicitado a informar um nome de espaço de trabalho, forneça um nome que seja globalmente exclusivo em todas as assinaturas do Azure.

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deploylaworkspacetemplate.json
    ```

A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem semelhante que inclui o resultado:

![Resultados de exemplo, quando a implantação for concluída](media/quick-create-workspace-cli/template-output-01.png)

## <a name="troubleshooting"></a>Solução de problemas
Quando você cria um workspace que foi excluído nos últimos 14 dias e está no [estado de exclusão reversível](../logs/delete-workspace.md#soft-delete-behavior), a operação pode ter um resultado diferente, dependendo da configuração do seu workspace:
1. Se você fornecer o mesmo nome do workspace, grupo de recursos, assinatura e região como no workspace excluído, seu workspace será recuperado, incluindo os dados, a configuração e os agentes conectados.
2. Se você usar o mesmo nome de workspace, mas outro grupo de recursos, assinatura ou região, receberá o erro *O nome do workspace 'workspace-name' não é exclusivo* ou erro de *conflito*. Para substituir a exclusão reversível, excluir permanentemente o workspace e criar um workspace com o mesmo nome, siga estas etapas para recuperar o workspace primeiro e depois executar a exclusão permanente:
   * [Recuperar](../logs/delete-workspace.md#recover-workspace) seu workspace
   * [Excluir permanentemente](../logs/delete-workspace.md#permanent-workspace-delete) seu espaço de trabalho
   * Criar um workspace usando o mesmo nome

## <a name="next-steps"></a>Próximas etapas
Agora que já tem um workspace disponível, você pode configurar a coleta de monitoramento de telemetria, executar pesquisas de logs para analisar os dados e adicionar uma solução de gerenciamento para fornecer dados adicionais e informações analíticas.  

* Para habilitar a coleta de dados de recursos do Azure com o Diagnóstico do Azure ou com o armazenamento do Azure, consulte [Coletar logs e as métricas do serviço do Azure para uso no Log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace).  
* Adicione o [System Center Operations Manager como uma fonte de dados](../agents/om-agents.md) para coletar dados de agentes que se reportam ao seu grupo de gerenciamento do Operations Manager e armazene-o em seu repositório de espaço de trabalho do Log Analytics.  
* Conecte-se ao [Configuration Manager](../logs/collect-sccm.md) para importar computadores que são membros de coleções na hierarquia.  
* Examine as [soluções de monitoramento](../insights/solutions.md) disponíveis e como adicionar ou remover uma solução do workspace.

