---
title: Habilitar o Gerenciamento de Atualizações usando o modelo do Azure Resource Manager
description: Este artigo diz como usar um modelo do Azure Resource Manager to habilitar o Gerenciamento de Atualizações.
services: automation
ms.subservice: update-management
ms.topic: conceptual
ms.date: 09/18/2020
ms.openlocfilehash: 95ef52acedc9171ba86110a665d08ea97c59bfbb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575822"
---
# <a name="enable-update-management-using-azure-resource-manager-template"></a>Habilitar o Gerenciamento de Atualizações usando o modelo do Azure Resource Manager

Você pode usar um [modelo do Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md) para habilitar o recurso de Gerenciamento de Atualizações da Automação do Azure em seu grupo de recursos. Este artigo fornece um modelo de exemplo que automatiza o seguinte:

* Automatiza a criação de um Azure Monitor espaço de trabalho Log Analytics.
* Automatiza a criação de uma conta de Automação do Azure.
* Vincula a conta de Automação ao workspace do Log Analytics.
* Adiciona runbooks de Automação de exemplo à conta.
* Habilita o recurso Gerenciamento de Atualizações.

O modelo não automatiza a habilitação de Gerenciamento de Atualizações em uma ou mais VMs do Azure ou não Azure.

Se você já tiver um workspace do Log Analytics e uma conta da Automação implantada em uma região com suporte em sua assinatura, eles não serão vinculados. O uso desse modelo cria com êxito o link e implanta Gerenciamento de Atualizações.

>[!NOTE]
>Não há suporte para a criação da conta Executar como de Automação quando você está usando um modelo do ARM. Para criar uma conta Executar como manualmente no portal ou com o PowerShell, confira [Criar uma conta Executar como](../create-run-as-account.md).

Depois de concluir essas etapas, você precisará [definir configurações de diagnóstico](../automation-manage-send-joblogs-log-analytics.md) para sua conta de automação para enviar fluxos de trabalho e status do trabalho de runbook para o workspace do Log Analytics vinculado.

## <a name="api-versions"></a>Versões de API

A tabela a seguir lista a versão de API para os recursos usados neste exemplo.

| Recurso | Tipo de recurso | Versão da API |
|:---|:---|:---|
| [Workspace](/azure/templates/microsoft.operationalinsights/workspaces) | workspaces | 2020-03-01-preview |
| [Conta de automação](/azure/templates/microsoft.automation/automationaccounts) | automação | 2020-01-13-preview |
| [Serviços vinculados de workspace](/azure/templates/microsoft.operationalinsights/workspaces/linkedservices) | workspaces | 2020-03-01-preview |
| [Soluções](/azure/templates/microsoft.operationsmanagement/solutions) | solutions | 2015-11-01-preview |

## <a name="before-using-the-template"></a>Antes de usar o modelo

O modelo JSON está configurado para solicitar:

* O nome do workspace.
* A região para criar o workspace.
* O nome da conta de Automação.
* A região na qual a conta de Automação será criada.

Os seguintes parâmetros no modelo são definidos com um valor padrão para o espaço de trabalho Log Analytics:

* *sku* assume por padrão o novo tipo de preço por GB lançado no modelo de preço de abril de 2018.
* *dataRetention* usa como padrão 30 dias.

>[!WARNING]
>Se você deseja criar ou configurar um espaço de trabalho do Log Analytics em uma assinatura que tiver aceitado o modelo de preços de abril de 2018, o único tipo de preço válido do Log Analytics *PerGB2018*.
>

O modelo JSON especifica um valor padrão para os outros parâmetros que provavelmente seriam usados como uma configuração padrão em seu ambiente. Você pode armazenar o modelo em uma conta de armazenamento do Azure para acesso compartilhado na sua organização. Para obter mais informações sobre como trabalhar com modelos, confira [Implantar recursos com modelos do ARM e a CLI do Azure](../../azure-resource-manager/templates/deploy-cli.md).

Se você for novo na automação do Azure e Azure Monitor, é importante entender os detalhes de configuração a seguir. Eles podem ajudá-lo a evitar erros ao tentar criar, configurar e usar um workspace do Log Analytics vinculado à sua nova conta de automação.

* Examine [detalhes adicionais](../../azure-monitor/logs/resource-manager-workspace.md#create-a-log-analytics-workspace) para entender totalmente as opções de configuração do espaço de trabalho, como modo de controle de acesso, tipo de preço, retenção e nível de reserva de capacidade.

* Examine [mapeamentos de workspace](../how-to/region-mappings.md) para especificar as regiões com suporte embutidas ou em um arquivo de parâmetro. Ao habilitar soluções, somente determinadas regiões têm suporte para vincular um workspace do Log Analytics e uma Conta de Automação na sua assinatura.

* Se você for novo nos logs do Azure Monitor e ainda não tiver implantado um espaço de trabalho, leia as [diretrizes de design do workspace](../../azure-monitor/logs/design-logs-deployment.md). Elas ajudarão você a aprender sobre o controle de acesso e compreender as estratégias de implementação de design que recomendamos para sua organização.

## <a name="deploy-template"></a>Implantar modelo

1. Copie e cole a seguinte sintaxe JSON em seu arquivo:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string",
                "metadata": {
                    "description": "Workspace name"
                }
            },
            "sku": {
                "type": "string",
                "allowedValues": [
                    "pergb2018",
                    "Free",
                    "Standalone",
                    "PerNode",
                    "Standard",
                    "Premium"
                ],
                "defaultValue": "pergb2018",
                "metadata": {
                    "description": "Pricing tier: perGB2018 or legacy tiers (Free, Standalone, PerNode, Standard or Premium), which are not available to all customers."
                }
            },
            "dataRetention": {
                "type": "int",
                "defaultValue": 30,
                "minValue": 7,
                "maxValue": 730,
                "metadata": {
                    "description": "Number of days to retain data."
                }
            },
            "location": {
                "type": "string",
                "defaultValue": "[resourceGroup().location]",
                "metadata": {
                    "description": "Specifies the location in which to create the workspace."
                }
            },
            "automationAccountName": {
                "type": "string",
                "metadata": {
                    "description": "Automation account name"
                }
            },
            "automationAccountLocation": {
                "type": "string",
                "metadata": {
                    "description": "Specifies the location in which to create the Automation account."
                }
            },
            "sampleGraphicalRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorial"
            },
            "sampleGraphicalRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePowerShellRunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialScript"
            },
            "samplePowerShellRunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "samplePython2RunbookName": {
                "type": "String",
                "defaultValue": "AzureAutomationTutorialPython2"
            },
            "samplePython2RunbookDescription": {
                "type": "String",
                "defaultValue": " An example runbook that gets all the Resource Manager resources by using the Run As account (service principal)."
            },
            "_artifactsLocation": {
                "type": "string",
                "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-automation/",
                "metadata": {
                    "description": "URI to artifacts location"
                }
            },
            "_artifactsLocationSasToken": {
                "type": "securestring",
                "defaultValue": "",
                "metadata": {
                    "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated"
                }
            }
        },
        "variables": {
        "Updates": {
            "name": "[concat('Updates', '(', parameters('workspaceName'), ')')]",
            "galleryName": "Updates"
          }
        },
        "resources": [
            {
                "type": "Microsoft.OperationalInsights/workspaces",
                "apiVersion": "2020-03-01-preview",
                "name": "[parameters('workspaceName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "sku": {
                        "name": "[parameters('sku')]"
                    },
                    "retentionInDays": "[parameters('dataRetention')]",
                    "features": {
                        "searchVersion": 1,
                        "legacy": 0
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "location": "[parameters('location')]",
                "name": "[variables('Updates').name]",
                "type": "Microsoft.OperationsManagement/solutions",
                "id": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationsManagement/solutions/', variables('Updates').name)]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'))]"
                },
                "plan": {
                    "name": "[variables('Updates').name]",
                    "publisher": "Microsoft",
                    "promotionCode": "",
                    "product": "[concat('OMSGallery/', variables('Updates').galleryName)]"
                }
            },
            {
                "type": "Microsoft.Automation/automationAccounts",
                "apiVersion": "2020-01-13-preview",
                "name": "[parameters('automationAccountName')]",
                "location": "[parameters('automationAccountLocation')]",
                "dependsOn": [
                    "[parameters('workspaceName')]"
                ],
                "properties": {
                    "sku": {
                        "name": "Basic"
                    }
                },
                "resources": [
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('sampleGraphicalRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "GraphPowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('sampleGraphicalRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.graphrunbook', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePowerShellRunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "PowerShell",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePowerShellRunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorial.ps1', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    },
                    {
                        "type": "runbooks",
                        "apiVersion": "2018-06-30",
                        "name": "[parameters('samplePython2RunbookName')]",
                        "location": "[parameters('automationAccountLocation')]",
                        "dependsOn": [
                            "[parameters('automationAccountName')]"
                        ],
                        "properties": {
                            "runbookType": "Python2",
                            "logProgress": "false",
                            "logVerbose": "false",
                            "description": "[parameters('samplePython2RunbookDescription')]",
                            "publishContentLink": {
                                "uri": "[uri(parameters('_artifactsLocation'), concat('scripts/AzureAutomationTutorialPython2.py', parameters('_artifactsLocationSasToken')))]",
                                "version": "1.0.0.0"
                            }
                        }
                    }
                ]
            },
            {
                "type": "Microsoft.OperationalInsights/workspaces/linkedServices",
                "apiVersion": "2020-03-01-preview",
                "name": "[concat(parameters('workspaceName'), '/' , 'Automation')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[parameters('workspaceName')]",
                    "[parameters('automationAccountName')]"
                ],
                "properties": {
                    "resourceId": "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccountName'))]"
                }
            }
        ]
    }
    ```

2. Edite o modelo para atender às suas necessidades. Considere a criação de um [arquivo de parâmetros do Resource Manager](../../azure-resource-manager/templates/parameter-files.md) em vez de passar parâmetros como valores embutidos.

3. Salve esse arquivo em uma pasta local como **deployUMSolutiontemplate.json**.

4. Você está pronto para implantar o modelo. Você pode usar a CLI do Azure ou o PowerShell. Quando for solicitado a informar um nome de workspace ou conta da Automação, forneça um nome que seja globalmente exclusivo em todas as assinaturas do Azure.

    **PowerShell**

    ```powershell
    New-AzResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateFile deployUMSolutiontemplate.json
    ```

    **CLI do Azure**

    ```azurecli
    az deployment group create --resource-group <my-resource-group> --name <my-deployment-name> --template-file deployUMSolutiontemplate.json
    ```

    A implantação pode levar alguns minutos para ser concluída. Quando ela for concluída, você verá uma mensagem semelhante que inclui o resultado:

    ![Resultados de exemplo, quando a implantação for concluída](media/enable-from-template/template-output.png)

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

1. Entre no [portal do Azure](https://portal.azure.com).

2. Na portal do Azure, abra a conta de automação que você criou.

3. No painel esquerdo, selecione **Runbooks**. Na página **Runbooks**, estão listados três runbooks do tutorial criados com a conta de Automação.

    ![Runbooks do tutorial criados com a conta de Automação](../media/quickstart-create-automation-account-template/automation-sample-runbooks.png)

4. No painel esquerdo, selecione **Workspace vinculado**. Na página **Workspace vinculado**, ele mostra o workspace do Log Analytics especificado anteriormente que foi vinculado à sua conta de Automação.

    ![Conta de Automação vinculada ao workspace do Log Analytics](../media/quickstart-create-automation-account-template/automation-account-linked-workspace.png)

5. No painel esquerdo, selecione gerenciamento de **atualizações**. Na página **Gerenciamento de atualizações** , ele mostra a página avaliação sem nenhuma informação como resultado da habilitação de apenas para ser habilitado e as máquinas não são configuradas para gerenciamento.

    ![Exibição de avaliação de recurso Gerenciamento de Atualizações](./media/enable-from-template/update-management-assessment-view.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você não precisar mais deles, exclua a solução **atualizações** no espaço de trabalho log Analytics, desvincule a conta de automação do espaço de trabalho e, em seguida, exclua a conta de automação e o espaço de trabalho.

## <a name="next-steps"></a>Próximas etapas

* Para usar Gerenciamento de Atualizações para VMs, consulte [gerenciar atualizações e patches para suas VMs](manage-updates-for-vm.md).

* Se você não quiser mais usar Gerenciamento de Atualizações e desejar removê-lo, consulte as instruções em [remover gerenciamento de atualizações recurso](remove-feature.md).

* Para excluir VMs do Gerenciamento de Atualizações, confira [Remover VMs do Gerenciamento de Atualizações](remove-vms.md).