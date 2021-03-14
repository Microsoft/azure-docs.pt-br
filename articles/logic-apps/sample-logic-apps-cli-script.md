---
title: Exemplo de script CLI do Azure – criar um aplicativo lógico
description: Script de exemplo para criar um aplicativo lógico por meio da extensão de aplicativos lógicos no CLI do Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: a4553ceee482fb232e9ab56deca650be93f9dc6b
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218036"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Exemplo de script CLI do Azure – criar um aplicativo lógico

Esse script cria um aplicativo lógico de exemplo por meio da [extensão de aplicativos lógicos CLI do Azure](/cli/azure/ext/logic/logic), ( `az logic` ). Para obter um guia detalhado para criar e gerenciar aplicativos lógicos por meio do CLI do Azure, consulte o guia de [início rápido dos aplicativos lógicos para o CLI do Azure](quickstart-logic-apps-azure-cli.md).

> [!WARNING]
> Atualmente, a extensão de Aplicativos Lógicos para a CLI do Azure está em fase *experimental* e não é *coberta pelo atendimento ao cliente*. Use essa extensão da CLI com cuidado, especialmente se optar por usar a extensão em ambientes de produção.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* A [CLI do Azure](/cli/azure/install-azure-cli) instalada no computador local.
* A [extensão de Aplicativos Lógicos para a CLI do Azure](/cli/azure/azure-cli-extensions-list) instalada no computador. Para instalar essa extensão, use este comando: `az extension add --name logic`
* Uma [definição de fluxo de trabalho](quickstart-logic-apps-azure-cli.md#workflow-definition) para seu aplicativo lógico. Esse arquivo JSON deve seguir o [esquema de linguagem de definição de fluxo de trabalho](logic-apps-workflow-definition-language.md).
* Uma conexão de API com uma conta de email por meio de um [conector de aplicativos lógicos](../connectors/apis-list.md) com suporte no mesmo grupo de recursos que seu aplicativo lógico. Este exemplo usa o conector do [Outlook do Office 365](../connectors/connectors-create-api-office365-outlook.md) , mas você também pode usar outros conectores como [Outlook.com](../connectors/connectors-create-api-outlook.md).

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

Valide seu ambiente antes de começar:

* Entre no portal do Azure e verifique se a sua assinatura está ativa executando `az login`.

* Verifique sua versão da CLI do Azure em uma janela Comando ou de terminal executando `az --version`. Para obter a última versão, confira as [notas sobre a versão mais recente](/cli/azure/release-notes-azure-cli).

  * Caso não tenha a última versão, atualize a instalação seguindo o [guia de instalação para seu sistema operacional ou sua plataforma](/cli/azure/install-azure-cli).

### <a name="sample-workflow-explanation"></a>Explicação do fluxo de trabalho de exemplo

Este arquivo de definição de fluxo de trabalho de exemplo cria o mesmo aplicativo lógico básico do [início rápido dos aplicativos lógicos para o portal do Azure](quickstart-create-first-logic-app-workflow.md). 

Este fluxo de trabalho de exemplo: 

1. Especifica um esquema, `$schema` , para o aplicativo lógico.

1. Define um gatilho para o aplicativo lógico na lista de gatilhos, `triggers` . O gatilho se repete ( `recurrence` ) a cada 3 horas. As ações são disparadas quando um novo item de feed é publicado ( `When_a_feed_item_is_published` ) para o RSS feed especificado () `feedUrl` .

1. Define uma ação para o aplicativo lógico na lista de ações, `actions` . A ação envia um email ( `Send_an_email_(V2)` ) por meio de Microsoft 365 com detalhes dos itens do RSS feed, conforme especificado na seção corpo ( `body` ) das entradas da ação ( `inputs` ).

## <a name="sample-workflow-definition"></a>Definição de fluxo de trabalho de exemplo

Antes de executar o script de exemplo, você deve primeiro criar uma [definição de fluxo de trabalho](#prerequisites)de exemplo.

1. Crie um arquivo JSON `testDefinition.json` em seu computador. 

1. Copie o seguinte conteúdo para o arquivo JSON: 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. Atualize os valores de espaço reservado com suas próprias informações:

    1. Substitua o endereço de email do espaço reservado ( `"To": "test@example.com"` ). Você precisa usar um endereço de email compatível com conectores de aplicativos lógicos. Para obter mais informações, consulte os [pré-requisitos](#prerequisites).

    1. Substitua os detalhes do conector adicional se você estiver usando outro conector de email que o conector do Outlook do Office 365.

    1. Substitua os valores de assinatura de espaço reservado ( `00000000-0000-0000-0000-000000000000` ) para seus identificadores de conexão ( `connectionId` e `id` ) no parâmetro de conexões ( `$connections` ) pelos seus próprios valores de assinatura.

1. Salve suas alterações.

## <a name="sample-script"></a>Exemplo de script

> [!NOTE]
> Este exemplo é escrito para o `bash` shell. Se você quiser executar esse exemplo em outro shell, como o Windows PowerShell ou o prompt de comando, talvez seja necessário fazer modificações no script.

Antes de executar este script de exemplo, execute este comando para se conectar ao Azure:

```azurecli-interactive

az login

```

Em seguida, navegue até o diretório no qual você criou sua definição de fluxo de trabalho. Por exemplo, se você criou o arquivo JSON de definição de fluxo de trabalho em seu desktop:

```azurecli

cd ~/Desktop

```

Em seguida, execute este script para criar um aplicativo lógico. 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>Limpar a implantação

Depois de terminar de usar o script de exemplo, execute o comando a seguir para remover o grupo de recursos e todos os seus recursos aninhados, incluindo o aplicativo lógico.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Explicação sobre o script

Este script de exemplo usa os seguintes comandos para criar um novo grupo de recursos e um aplicativo lógico.

| Comando | Observações |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual os recursos do aplicativo lógico são armazenados. |
| [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) | Cria um aplicativo lógico com base no fluxo de trabalho definido no parâmetro `--definition` . |
| [`az group delete`](/cli/azure/vm/extension) | Exclui um grupo de recursos e todos os seus recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, confira a [Documentação da CLI do Azure](/cli/azure/).

Veja exemplos de script adicionais da CLI dos Aplicativos Lógicos no [navegador de exemplos de código da Microsoft](/samples/browse/?products=azure-logic-apps).
