---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/27/2018
ms.author: glenga
ms.openlocfilehash: f9b853f0e86fc298ab35421928492ba813d85827
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444620"
---
## <a name="create-an-azure-functions-project"></a>Criar seu projeto do Functions com uma função 

O modelo de projeto do Azure Functions no Visual Studio Code cria um projeto que pode ser publicado em um aplicativo de funções no Azure. Um aplicativo de funções permite a você agrupar funções como uma unidade lógica para o gerenciamento, implantação e compartilhamento de recursos.

1. No Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comandos, pesquise e selecione `Azure Functions: Create new project...`.

1. Escolha um local de diretório para o espaço de trabalho do projeto e escolha **Selecionar**.

    > [!NOTE]
    > Estas etapas foram projetadas para serem concluídas fora de um espaço de trabalho. Nesse caso, não selecione uma pasta de projeto que faz parte de um workspace.

1. Após os prompts, forneça as seguintes informações:

    | Prompt | Valor | DESCRIÇÃO |
    | ------ | ----- | ----------- |
    | Selecione uma linguagem de programação para o seu projeto de aplicativo de função. | C# ou JavaScript | Este artigo é compatível com C# e JavaScript. Para Python, consulte [neste artigo do Python](https://code.visualstudio.com/docs/python/tutorial-azure-functions) e para PowerShell, consulte [este artigo do PowerShell](../articles/azure-functions/functions-create-first-function-powershell.md).  |
    | Selecione um modelo para a primeira função do projeto | Gatilho HTTP | Crie uma função disparada por HTTP no novo aplicativo de funções. |
    | Forneça um nome de função | HttpTrigger | Pressione Enter para usar o nome padrão. |
    | Forneça um namespace | My.Functions | (Somente C#) As bibliotecas de classe C# devem ter um namespace.  |
    | Nível de autorização | Função | Requer uma [tecla de função](../articles/azure-functions/functions-bindings-http-webhook.md#authorization-keys) para chamar o ponto de extremidade HTTP da função. |
    | Selecione como você gostaria de abrir seu projeto | Adicionar ao espaço de trabalho | Cria o aplicativo de funções no espaço de trabalho atual. |

O Visual Studio Code cria o projeto de aplicativo de função em um novo workspace. Este projeto contém os arquivos de configuração [host.json](../articles/azure-functions/functions-host-json.md) e [local.settings.json](../articles/azure-functions/functions-run-local.md#local-settings-file), além de quaisquer arquivos de projeto específicos a uma linguagem. 

Uma nova função disparada por HTTP também é criada na pasta HttpTrigger do projeto do aplicativo de função.