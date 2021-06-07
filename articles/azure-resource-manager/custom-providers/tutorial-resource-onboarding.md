---
title: 'Tutorial: integração de recursos'
description: A integração de recursos por meio de provedores personalizados permite que você manipule e estenda os recursos existentes do Azure.
ms.topic: tutorial
ms.author: jobreen
author: jjbfour
ms.date: 09/17/2019
ms.openlocfilehash: 22d1dcd997a4ddb94aba184c5dace4c00509054d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "75648603"
---
# <a name="tutorial-resource-onboarding-with-azure-custom-providers"></a>Tutorial: Integração de recursos com os Provedores Personalizados do Azure

Neste tutorial, você implantará no Azure um provedor de recursos personalizado que estende a API do Azure Resource Manager com o tipo de recurso Microsoft.CustomProviders/associations. O tutorial mostra como estender os recursos existentes que estão fora do grupo de recursos em que a instância do provedor personalizado está localizada. Neste tutorial, o provedor de recursos personalizado é ativado por um aplicativo lógico do Azure, mas você pode usar qualquer ponto de extremidade de API pública.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará saber:

* As funcionalidades dos [Provedores Personalizados do Azure](overview.md).
* Informações básicas sobre a [integração de recursos com provedores personalizados](concepts-resource-onboarding.md).

## <a name="get-started-with-resource-onboarding"></a>Introdução à integração de recursos

Neste tutorial, há duas partes que precisam ser implantadas: o provedor personalizado e a associação. Para facilitar o processo, opcionalmente, você pode usar um único modelo que implanta ambos.

O modelo usará estes recursos:

* Microsoft.CustomProviders/resourceProviders
* Microsoft.Logic/workflows
* Microsoft.CustomProviders/associations

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "allowedValues": [
                "australiaeast",
                "eastus",
                "westeurope"
            ],
            "metadata": {
                "description": "Location for the resources."
            }
        },
        "logicAppName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the logic app to be created."
            }
        },
        "customResourceProviderName": {
            "type": "string",
            "defaultValue": "[uniqueString(resourceGroup().id)]",
            "metadata": {
                "description": "Name of the custom provider to be created."
            }
        },
        "customResourceProviderId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The resource ID of an existing custom provider. Provide this to skip deployment of new logic app and custom provider."
            }
        },
        "associationName": {
            "type": "string",
            "defaultValue": "myAssociationResource",
            "metadata": {
                "description": "Name of the custom resource that is being created."
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "condition": "[empty(parameters('customResourceProviderId'))]",
            "name": "customProviderInfrastructureTemplate",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {
                        "logicAppName": {
                            "type": "string",
                            "defaultValue": "[parameters('logicAppName')]"
                        }
                    },
                    "resources": [
                        {
                            "type": "Microsoft.Logic/workflows",
                            "apiVersion": "2017-07-01",
                            "name": "[parameters('logicAppName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "state": "Enabled",
                                "definition": {
                                    "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
                                    "actions": {
                                        "Switch": {
                                            "cases": {
                                                "Case": {
                                                    "actions": {
                                                        "CreateCustomResource": {
                                                            "inputs": {
                                                                "body": {
                                                                    "properties": "@addProperty(triggerBody().Body['properties'], 'myDynamicProperty', 'myDynamicValue')"
                                                                },
                                                                "statusCode": 200
                                                            },
                                                            "kind": "Http",
                                                            "type": "Response"
                                                        }
                                                    },
                                                    "case": "CREATE"
                                                }
                                            },
                                            "default": {
                                                "actions": {
                                                    "DefaultHttpResponse": {
                                                        "inputs": {
                                                            "statusCode": 200
                                                        },
                                                        "kind": "Http",
                                                        "type": "Response"
                                                    }
                                                }
                                            },
                                            "expression": "@triggerBody().operationType",
                                            "type": "Switch"
                                        }
                                    },
                                    "contentVersion": "1.0.0.0",
                                    "outputs": {},
                                    "parameters": {},
                                    "triggers": {
                                        "CustomProviderWebhook": {
                                            "inputs": {
                                                "schema": {}
                                            },
                                            "kind": "Http",
                                            "type": "Request"
                                        }
                                    }
                                }
                            }
                        },
                        {
                            "type": "Microsoft.CustomProviders/resourceProviders",
                            "apiVersion": "2018-09-01-preview",
                            "name": "[parameters('customResourceProviderName')]",
                            "location": "[parameters('location')]",
                            "properties": {
                                "resourceTypes": [
                                    {
                                        "name": "associations",
                                        "mode": "Secure",
                                        "routingType": "Webhook,Cache,Extension",
                                        "endpoint": "[[listCallbackURL(concat(resourceId('Microsoft.Logic/workflows', parameters('logicAppName')), '/triggers/CustomProviderWebhook'), '2017-07-01').value]"
                                    }
                                ]
                            }
                        }
                    ],
                    "outputs": {
                        "customProviderResourceId": {
                            "type": "string",
                            "value": "[resourceId('Microsoft.CustomProviders/resourceProviders', parameters('customResourceProviderName'))]"
                        }
                    }
                }
            }
        },
        {
            "type": "Microsoft.CustomProviders/associations",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('associationName')]",
            "location": "global",
            "properties": {
                "targetResourceId": "[if(empty(parameters('customResourceProviderId')), reference('customProviderInfrastructureTemplate').outputs.customProviderResourceId.value, parameters('customResourceProviderId'))]",
                "myCustomInputProperty": "myCustomInputValue",
                "myCustomInputObject": {
                    "Property1": "Value1"
                }
            }
        }
    ],
    "outputs": {
        "associationResource": {
            "type": "object",
            "value": "[reference(parameters('associationName'), '2018-09-01-preview', 'Full')]"
        }
    }
}
```

### <a name="deploy-the-custom-provider-infrastructure"></a>Implantar a infraestrutura do provedor personalizado

A primeira parte do modelo implanta a infraestrutura do provedor personalizado. Essa infraestrutura define o efeito do recurso de associações. Caso não tenha familiaridade com provedores personalizados, confira [Conceitos básicos sobre o provedor personalizado](overview.md).

Vamos implantar a infraestrutura do provedor personalizado. Copie, salve e implante o modelo anterior ou acompanhe o tutorial e implante a infraestrutura usando o portal do Azure.

1. Vá para o [Portal do Azure](https://portal.azure.com).

2. Pesquise **modelos** em **Todos os Serviços** ou usando a caixa de pesquisa principal:

   ![Pesquisar modelos](media/tutorial-resource-onboarding/templates.png)

3. Selecione **Adicionar** no painel **Modelos**:

   ![Selecione Adicionar](media/tutorial-resource-onboarding/templatesadd.png)

4. Em **Geral**, insira um **Nome** e uma **Descrição** para o novo modelo:

   ![Nome do modelo e descrição](media/tutorial-resource-onboarding/templatesdescription.png)

5. Crie o modelo do Resource Manager copiando o modelo JSON da seção "Introdução à integração de recursos" deste artigo:

   ![Criar um modelo do Resource Manager](media/tutorial-resource-onboarding/templatesarmtemplate.png)

6. Selecione **Adicionar** para criar o modelo. Se o novo modelo não for exibido, selecione **Atualizar**.

7. Selecione o modelo recém-criado e, em seguida, selecione **Implantar**:

   ![Selecione o novo modelo e, em seguida, selecione Implantar](media/tutorial-resource-onboarding/templateselectspecific.png)

8. Insira as configurações para os campos obrigatórios e, em seguida, selecione a assinatura e o grupo de recursos. Deixe a caixa **ID do Provedor de Recursos Personalizado** vazia.

   | Nome da configuração | Necessário? | Descrição |
   | ------------ | -------- | ----------- |
   | Location | Sim | A localização dos recursos no modelo. |
   | Nome do Aplicativo Lógico | Não | O nome do aplicativo lógico. |
   | Nome do Provedor de Recursos Personalizado | Não | O nome do provedor de recursos personalizado. |
   | ID do Provedor de Recursos Personalizado | Não | Um provedor de recursos personalizado existente que dá suporte ao recurso de associação. Se você especificar um valor aqui, o aplicativo lógico e a implantação do provedor personalizado serão ignorados. |
   | Nome da Associação | Não | O nome do recurso de associação. |

   Parâmetros de exemplo:

   ![Insira os parâmetros de modelo](media/tutorial-resource-onboarding/templatescustomprovider.png)

9. Acesse a implantação e aguarde a conclusão dela. Você deverá ver algo semelhante à captura de tela a seguir. Você deverá ver o novo recurso de associação como uma saída:

   ![Implantação bem-sucedida](media/tutorial-resource-onboarding/customproviderdeployment.png)

   Este é o grupo de recursos, com a opção **Mostrar tipos ocultos** selecionada:

   ![Implantação de provedor personalizado](media/tutorial-resource-onboarding/showhidden.png)

10. Explore a guia **Histórico de execuções** do aplicativo lógico para ver as chamadas para a criação da associação:

    ![Histórico de execuções do aplicativo lógico](media/tutorial-resource-onboarding/logicapprun.png)

## <a name="deploy-additional-associations"></a>Implantar associações adicionais

Depois de configurar a infraestrutura do provedor personalizado, você poderá implantar mais associações com facilidade. O grupo de recursos para associações adicionais não precisa ser o mesmo do grupo de recursos em que você implantou a infraestrutura do provedor personalizado. Para criar uma associação, você precisa ter permissões Microsoft.CustomProviders/resourceproviders/write na ID do Provedor de Recursos Personalizado especificado.

1. Acesse o recurso **Microsoft.CustomProviders/resourceProviders** do provedor personalizado no grupo de recursos da implantação anterior. Você precisará marcar a caixa de seleção **Mostrar tipos ocultos**:

   ![Acessar o recurso](media/tutorial-resource-onboarding/showhidden.png)

2. Copie a propriedade ID do Recurso do provedor personalizado.

3. Pesquise **modelos** em **Todos os Serviços** ou usando a caixa de pesquisa principal:

   ![Pesquisar modelos](media/tutorial-resource-onboarding/templates.png)

4. Selecione o modelo criado anteriormente e, em seguida, selecione **Implantar**:

   ![Selecionar o modelo criado anteriormente e, em seguida, selecionar Implantar](media/tutorial-resource-onboarding/templateselectspecific.png)

5. Insira as configurações para os campos obrigatórios e, em seguida, selecione a assinatura e outro grupo de recursos. Na configuração da **ID do Provedor de Recursos Personalizado**, insira a ID do Recurso que você copiou do provedor personalizado implantado anteriormente.

6. Acesse a implantação e aguarde a conclusão dela. Agora, ela deverá implantar apenas o novo recurso de associações:

   ![Novo recurso de associações](media/tutorial-resource-onboarding/createdassociationresource.png)

Se desejar, volte ao **Histórico de execuções** do aplicativo lógico e veja que outra chamada foi feita ao aplicativo lógico. Atualize o aplicativo lógico para aumentar a funcionalidade adicional de cada associação criada.

## <a name="getting-help"></a>Obtendo ajuda

Caso você tenha dúvidas sobre os Provedores Personalizados do Azure, tente postar suas perguntas no [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Uma pergunta semelhante já pode ter sido respondida, portanto, verifique-a primeiro antes de postá-la. Adicione a marca `azure-custom-providers` para obter uma resposta rápida.

