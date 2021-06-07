---
title: Criar e gerenciar aplicativos lógicos com a CLI do Azure
description: Use a CLI do Azure para criar um aplicativo lógico e gerencie seu aplicativo lógico usando operações como listar, mostrar (get), atualizar e excluir.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli, contperf-fy21q2
ms.date: 11/23/2020
ms.openlocfilehash: bc172fd1702addf8f4e34094452a779b09320a4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033367"
---
# <a name="quickstart-create-and-manage-logic-apps-using-the-azure-cli"></a>Início Rápido: Criar e gerenciar aplicativos lógicos usando a CLI do Azure

Este guia de início rápido mostra como criar e gerenciar aplicativos lógicos usando a [extensão de Aplicativos Lógicos para a CLI do Azure](/cli/azure/ext/logic/logic) (`az logic`). Na linha de comando, você pode criar um aplicativo lógico usando o arquivo JSON para uma definição de fluxo de trabalho do aplicativo lógico. Em seguida, você pode gerenciar seu aplicativo lógico executando operações como `list`, `show` (`get`), `update` e `delete` na linha de comando.

> [!WARNING]
> Atualmente, a extensão de Aplicativos Lógicos para a CLI do Azure está em fase *experimental* e não é *coberta pelo atendimento ao cliente*. Use essa extensão da CLI com cuidado, especialmente se optar por usar a extensão em ambientes de produção.

Se você não estiver familiarizado com os Aplicativos Lógicos, também poderá aprender a criar seus primeiros aplicativos lógicos [por meio do portal do Azure](quickstart-create-first-logic-app-workflow.md), [no Visual Studio](quickstart-create-logic-apps-with-visual-studio.md) e [no Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa. Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* A [CLI do Azure](/cli/azure/install-azure-cli) instalada no computador local.
* A [extensão de Aplicativos Lógicos para a CLI do Azure](/cli/azure/azure-cli-extensions-list) instalada no computador. Para instalar essa extensão, use este comando: `az extension add --name logic`
* Um [grupo de recursos do Azure](#example---create-resource-group) no qual o aplicativo lógico será criado.

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

Valide seu ambiente antes de começar:

* Entre no portal do Azure e verifique se a sua assinatura está ativa executando `az login`.
* Verifique sua versão da CLI do Azure em uma janela Comando ou de terminal executando `az --version`. Para obter a última versão, confira as [notas sobre a versão mais recente](/cli/azure/release-notes-azure-cli?tabs=azure-cli).
  * Caso não tenha a última versão, atualize a instalação seguindo o [guia de instalação para seu sistema operacional ou sua plataforma](/cli/azure/install-azure-cli).

### <a name="example---create-resource-group"></a>Exemplo – criar grupo de recursos

Caso ainda não tenha um grupo de recursos para seu aplicativo lógico, crie o grupo com o comando `az group create`. Por exemplo, o comando a seguir cria um grupo de recursos chamado `testResourceGroup` na localização `westus`.

```azurecli-interactive

az group create --name testResourceGroup --location westus

```

A saída mostra o `provisioningState` como `Succeeded` quando o grupo de recursos é criado com êxito:

```output

<...>
  "name": "testResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
<...>

```

## <a name="workflow-definition"></a>Definição de fluxo de trabalho

Para [criar um aplicativo lógico](#create-logic-apps-from-cli) ou [atualizar um aplicativo lógico existente](#update-logic-apps-from-cli) usando a CLI do Azure, você precisará ter uma definição de fluxo de trabalho para o aplicativo lógico. No portal do Azure, veja a definição de fluxo de trabalho subjacente do aplicativo lógico no formato JSON alternando a exibição de **Designer** para a **exibição de Código**.

Quando você executa os comandos necessários para criar ou atualizar o aplicativo lógico, a definição de fluxo de trabalho é carregada como um parâmetro obrigatório (`--definition`). Você precisa criar a definição de fluxo de trabalho como um arquivo JSON que segue o [esquema da Linguagem de Definição de Fluxo de Trabalho](./logic-apps-workflow-definition-language.md).

## <a name="create-logic-apps-from-cli"></a>Criar aplicativos lógicos por meio da CLI

Crie um fluxo de trabalho de aplicativo lógico por meio da CLI do Azure usando o comando [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create) com um arquivo JSON para a definição.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

O comando precisa incluir os seguintes [parâmetros obrigatórios](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters):

| Parâmetro | Valor | Descrição |
| --------- | ----- | ----------- |
| Definição de fluxo de trabalho | `--definition` | Um arquivo JSON com a [definição de fluxo de trabalho](#workflow-definition) do aplicativo lógico. |
| Location | `--location -l` | A região do Azure na qual o aplicativo lógico está localizado. |
| Nome | `--name -n` | O nome do aplicativo lógico. O nome só pode conter letras, números, hifens (`-`), sublinhados (`_`), parênteses (`()`) e pontos (`.`). O nome também precisa ser exclusivo entre as regiões. |
| Nome do grupo de recursos | `--resource-group -g` | O [grupo de recursos do Azure](../azure-resource-manager/management/overview.md) no qual você deseja criar o aplicativo lógico. [Crie um grupo de recursos](#example---create-resource-group) antes de começar, caso ainda não tenha um para seu aplicativo lógico. |

Inclua também [parâmetros opcionais](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) adicionais para configurar os controles de acesso, os pontos de extremidade, a conta de integração, o ambiente de serviço de integração, o estado e as marcas de recurso do aplicativo lógico.

### <a name="example---create-logic-app"></a>Exemplo – criar aplicativo lógico

Neste exemplo, um fluxo de trabalho chamado `testLogicApp` é criado no grupo de recursos `testResourceGroup` na localização `westus`. O arquivo JSON `testDefinition.json` contém a definição de fluxo de trabalho.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

Quando o fluxo de trabalho é criado com êxito, a CLI mostra o código JSON da nova definição de fluxo de trabalho. Se a criação do fluxo de trabalho falhar, confira a [lista de possíveis erros](#errors).

## <a name="update-logic-apps-from-cli"></a>Atualizar aplicativos lógicos por meio da CLI

Atualize também o fluxo de trabalho de um aplicativo lógico por meio da CLI do Azure usando o comando [`az logic workflow create`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create).

O comando precisa incluir os mesmos [parâmetros obrigatórios](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-required-parameters) usados quando você [cria um aplicativo lógico](#create-logic-apps-from-cli). Adicione também os mesmos [parâmetros opcionais](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-create-optional-parameters) usados durante a criação de um aplicativo lógico.

```azurecli

az logic workflow create --definition
                         --location
                         --name
                         --resource-group
                         [--access-control]
                         [--endpoints-configuration]
                         [--integration-account]
                         [--integration-service-environment]
                         [--state {Completed, Deleted, Disabled, Enabled, NotSpecified, Suspended}]
                         [--tags]

```

### <a name="example---update-logic-app"></a>Exemplo – atualizar aplicativo lógico

Neste exemplo, o [fluxo de trabalho de exemplo criado na seção anterior](#example---create-logic-app) é atualizado para usar outro arquivo de definição JSON, `newTestDefinition.json` e adicionar duas marcas de recurso, `testTag1` e `testTag2`, com valores de descrição.

```azurecli-interactive

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "newTestDefinition.json" --tags "testTag1=testTagValue1" "testTag2=testTagValue"

```

Quando o fluxo de trabalho é atualizado com êxito, a CLI mostra a definição de fluxo de trabalho atualizada do aplicativo lógico. Se a atualização falhar, confira a [lista de possíveis erros](#errors).

## <a name="delete-logic-apps-from-cli"></a>Excluir aplicativos lógicos por meio da CLI

Exclua o fluxo de trabalho de um aplicativo lógico por meio da CLI do Azure usando o comando [`az logic workflow delete`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete).

O comando precisa incluir os seguintes [parâmetros obrigatórios](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-required-parameters):

| Parâmetro | Valor | Descrição |
| --------- | ----- | ----------- |
| Nome | `--name -n` | O nome do aplicativo lógico. |
| Nome do grupo de recursos | `-resource-group -g` | O grupo de recursos no qual o aplicativo lógico está localizado. |

Inclua também um [parâmetro opcional](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-delete-optional-parameters) para ignorar os avisos de confirmação, `--yes -y`.

```azurecli

az logic workflow delete --name
                         --resource-group
                         [--yes]

```

Em seguida, a CLI solicita a confirmação da exclusão do aplicativo lógico. Ignore o aviso de confirmação usando o parâmetro opcional `--yes -y` com o comando.

```azurecli

Are you sure you want to perform this operation? (y/n):

```

Confirme a exclusão de um aplicativo lógico [listando seus aplicativos lógicos na CLI](#list-logic-apps-in-cli) ou vendo seus aplicativos lógicos no portal do Azure.

### <a name="example---delete-logic-app"></a>Exemplo – excluir aplicativo lógico

Neste exemplo, o [fluxo de trabalho de exemplo criado em uma seção anterior](#example---create-logic-app) é excluído.

```azurecli-interactive

az logic workflow delete --resource-group "testResourceGroup" --name "testLogicApp"

```

Depois que você responde ao aviso de confirmação com `y`, o aplicativo lógico é excluído.

## <a name="show-logic-apps-in-cli"></a>Mostrar aplicativos lógicos na CLI

Obtenha um fluxo de trabalho de aplicativo lógico específico usando o comando [`az logic workflow show`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show).

```azurecli

az logic workflow show --name
                       --resource-group

```

O comando precisa incluir os [parâmetros obrigatórios](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-show-required-parameters) a seguir

| Parâmetro | Valor | Descrição |
| --------- | ----- | ----------- |
| Nome | `--name -n` | O nome do aplicativo lógico. |
| Nome do grupo de recursos | `--resource-group -g` | O nome do grupo de recursos no qual o aplicativo lógico está localizado. |

### <a name="example---get-logic-app"></a>Exemplo – obter aplicativo lógico

Neste exemplo, o aplicativo lógico `testLogicApp` no grupo de recursos `testResourceGroup` é retornado com logs completos para depuração.

```azurecli-interactive

az logic workflow show --resource-group "testResourceGroup" --name "testLogicApp" --debug

```

## <a name="list-logic-apps-in-cli"></a>Listar aplicativos lógicos na CLI

Liste seus aplicativos lógicos por assinatura usando o comando [`az logic workflow list`](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list). Esse comando retorna o código JSON para os fluxos de trabalho dos aplicativos lógicos.

Filtre os resultados pelos seguintes [parâmetros opcionais](/cli/azure/ext/logic/logic/workflow#ext-logic-az-logic-workflow-list-optional-parameters):

| Parâmetro | Valor | Descrição |
| --------- | ----- | ----------- |
| Nome do grupo de recursos | `--resource-group -g` | O nome do grupo de recursos pelo qual você deseja filtrar os resultados. |
| Número de itens | `--top` | O número de itens incluídos nos resultados. |
| Filtrar | `--filter` | O tipo de filtro que você está usando na sua lista. Filtre a lista por estado (`State`), por gatilho (`Trigger`) e pelo identificador do recurso referenciado (`ReferencedResourceId`). |

```azurecli

az logic workflow list [--filter]
                       [--resource-group]
                       [--top]

```

### <a name="example---list-logic-apps"></a>Exemplo – listar aplicativos lógicos

Neste exemplo, todos os fluxos de trabalho habilitados no grupo de recursos `testResourceGroup` são retornados em um formato de tabela ASCII.

```azurecli-interactive

az logic workflow list --resource-group "testResourceGroup" --filter "(State eq 'Enabled')" --output "table"

```

## <a name="errors"></a>Errors

O erro a seguir indica que a extensão de Aplicativos Lógicos do Azure para a CLI não está instalada. Siga as etapas em pré-requisitos para [instalar a extensão dos Aplicativos Lógicos](#prerequisites) no computador.

```output

az: 'logic' is not in the 'az' command group. See 'az --help'. If the command is from an extension, please make sure the corresponding extension is installed. To learn more about extensions, please visit https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview

```

O erro a seguir pode indicar que o caminho do arquivo para carregar a definição de fluxo de trabalho está incorreto.

```output

Expecting value: line 1 column 1 (char 0)

```

## <a name="global-parameters"></a>Parâmetros globais

Use os seguintes parâmetros opcionais globais da CLI do Azure com os comandos `az logic`:

| Parâmetro | Valor | Descrição |
| --------- | ----- | ----------- |
| Formato da saída | `--output -o` | Altere o [formato de saída](/cli/azure/format-output-azure-cli) do JSON padrão. |
| Mostrar somente erros | `--only-show-errors` | Suprime os avisos e mostra somente os erros. |
| Detalhado | `--verbose` | Mostra logs detalhados. |
| Depurar | `--debug` | Mostra todos os logs de depuração. |
| Mensagem de ajuda | `--help -h` | Mostra a caixa de diálogo de ajuda. |
| Consulta | `--query` | Define uma cadeia de consulta JMESPath para a saída JSON. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, confira a [Documentação da CLI do Azure](/cli/azure/).

Veja exemplos de script adicionais da CLI dos Aplicativos Lógicos no [navegador de exemplos de código da Microsoft](/samples/browse/?products=azure-logic-apps).

Em seguida, você poderá criar uma lógica de aplicativo de exemplo por meio da CLI do Azure usando uma definição de fluxo de trabalho e script de exemplo.

> [!div class="nextstepaction"]
> [Criar aplicativo lógico usando script de exemplo](sample-logic-apps-cli-script.md).
