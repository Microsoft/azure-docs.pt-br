---
title: Criar sua primeira função usando a CLI do Azure
description: Aprenda a criar sua primeira função do Azure para execução sem servidor usando a CLI do Azure e o Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 11/13/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: 625fcda4b612fdeda940f9219238ac79354dd056
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444661"
---
# <a name="create-your-first-function-from-the-command-line"></a>Criar sua primeira função na linha de comando

Este tópico de início rápido explica como criar sua primeira função na linha de comando ou no terminal. É possível usar a CLI do Azure para criar um aplicativo de funções, que é a infraestrutura [sem servidor](https://azure.microsoft.com/solutions/serverless/) que hospeda sua função. O projeto de código de função é gerado de um modelo usando o [Azure Functions Core Tools](functions-run-local.md), que também é usado para implantar o projeto de aplicativo de funções no Azure.

Você pode seguir as etapas abaixo usando um computador Mac, Windows ou Linux.

## <a name="prerequisites"></a>Pré-requisitos

Antes de executar este exemplo, você deve ter o seguinte:

+ Instale o [Azure Functions Core Tools](./functions-run-local.md#v2) versão 2.6.666 ou posterior.

+ Instale a [CLI do Azure]( /cli/azure/install-azure-cli). Este artigo requer a CLI do Azure versão 2.0 ou posterior. Execute `az --version` descobrir a versão que você tem. Você também pode usar o [Azure Cloud Shell](https://shell.azure.com/bash).

+ Uma assinatura ativa do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Criar o projeto de aplicativo de funções local

Execute o comando a seguir na linha de comando para criar um projeto de aplicativo de funções na pasta `MyFunctionProj` do diretório local atual. Um repositório GitHub também é criado em `MyFunctionProj`.

```bash
func init MyFunctionProj
```

Quando solicitado, selecione um tempo de execução de trabalho entre as seguintes opções de linguagem:

+ `dotnet`: cria um projeto de biblioteca de classes do .NET (.csproj).
+ `node`: cria um projeto de JavaScript.

Quando o comando for executado, você verá algo parecido com a seguinte saída:

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

Use o comando a seguir para navegar até a nova pasta do projeto `MyFunctionProj`.

```bash
cd MyFunctionProj
```

## <a name="enable-extension-bundles"></a>Habilitar pacotes de extensão

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Criar um aplicativo de funções

Você deve ter um aplicativo de funções para hospedar a execução de suas funções. O aplicativo de funções fornece um ambiente para execução sem servidor do seu código de função. Ele permite que você agrupe funções como uma unidade lógica para facilitar o gerenciamento, a implantação e o compartilhamento de recursos. Crie um aplicativo de funções ao usar o comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

No comando a seguir, substitua um nome de aplicativo de funções exclusivo quando você vir o espaço reservado `<APP_NAME>` e o nome da conta de armazenamento por `<STORAGE_NAME>`. O `<APP_NAME>` é usado como domínio DNS padrão para o aplicativo de funções, portanto, o nome deve ser exclusivo entre todos os aplicativos no Azure. É possível definir o tempo de execução de `<language>` para seu aplicativo de funções, do `dotnet` (C#) ou `node` (JavaScript).

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <APP_NAME> --storage-account  <STORAGE_NAME> --runtime <language>
```

Definir o parâmetro _local do plano de consumo_ significa que o aplicativo da função é hospedado em um plano de hospedagem de consumo. Nesse plano sem servidor, os recursos são adicionados dinamicamente conforme exigido pelas funções, e você paga apenas quando as funções estão em execução. Para obter mais informações, consulte [Escolher o plano de hospedagem correto](functions-scale.md).

Depois que o aplicativo de funções for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

