---
title: Alterar, treinar aplicativo, C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: Neste início rápido de C#, adicione exemplos de enunciados a um aplicativo Automação de Página Inicial e treine o aplicativo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: d44877e35687745ddba51d4d015729e62106c9ca
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348442"
---
# <a name="quickstart-change-model-using-c"></a>Início Rápido: Alterar o modelo usando C#

[!INCLUDE [Quickstart introduction for change model](../../../includes/cognitive-services-luis-qs-change-model-intro-para.md)]

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Quickstart prerequisites for changing model](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [**Edição mais recente do Visual Studio Community**](https://www.visualstudio.com/downloads/).
* Linguagem de programação C# instalada.
* Pacotes do NuGet [JsonFormatterPlus](https://www.nuget.org/packages/JsonFormatterPlus) e [CommandLine](https://www.nuget.org/packages/CommandLineParser/)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>Exemplo de arquivo JSON de enunciados

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Criar código de início rápido 

No Visual Studio, crie um novo aplicativo de **Console de área de trabalho clássica do Windows** usando o .Net Framework. Dê ao projeto o nome de `ConsoleApp1`.

![Tipo de projeto do Visual Studio](./media/luis-quickstart-cs-add-utterance/vs-project-type.png)

### <a name="add-the-systemweb-dependency"></a>Adicionar a dependência do System.Web

O projeto do Visual Studio precisa de **System.Web**. No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e selecione **Adicionar Referência** da seção Assemblies.

![Adicione a referência System.web](./media/luis-quickstart-cs-add-utterance/system.web.png)

### <a name="add-other-dependencies"></a>Adicionar outras dependências

O projeto do Visual Studio precisa de **JsonFormatterPlus** e **CommandLineParser**. No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** e selecione **Gerenciar Pacotes NuGet…** . Procure e adicione cada um dos dois pacotes. 

![Adicionar dependências de terceiros](./media/luis-quickstart-cs-add-utterance/add-dependencies.png)


### <a name="write-the-c-code"></a>Escreva o código C#
O arquivo **Program.cs** deve seguir a seguinte estrutura:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Atualize as dependências para que sejam:

   [!code-csharp[Add the dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=1-11 "Add the dependencies")]


Adicione as IDs de LUIS e cadeias de caracteres para a classe do **Programa**.

   [!code-csharp[Add the LUIS IDs and strings](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=19-30&dedent=8 "Add the LUIS IDs and strings")]

Adicionar classe para gerenciar os parâmetros de linha de comando para a classe **Programa**.

   [!code-csharp[Add class to manage command line parameters.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=32-46 "Add class to manage command-line parameters.")]

Adicione o método de solicitação GET à classe **Programa**.

   [!code-csharp[Add the GET request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=49-59 "Add the GET request.")]


Adicione o método de solicitação POST à classe **Programa**. 

   [!code-csharp[Add the POST request.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=60-76 "Add the POST request.")]

Adicionar enunciados de exemplo do método file à classe **Programa**.

   [!code-csharp[Add example utterances from file.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=77-86 "Add example utterances from file.")]

Depois que as alterações forem aplicadas ao modelo, treine o modelo. Adicione o método à classe **Programa**.

   [!code-csharp[After the changes are applied to the model, train the model.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=87-96 "After the changes are applied to the model, train the model.")]

O treinamento não pode ser concluído imediatamente, verifique o status para verificar se o treinamento está concluído. Adicione o método à classe **Programa**.

   [!code-csharp[Training may not complete immediately, check status to verify training is complete.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=97-103 "Training may not complete immediately, check status to verify training is complete.")]

Para gerenciar argumentos de linha de comando, adicione o código principal. Adicione o método à classe **Programa**.

   [!code-csharp[To manage command line arguments, add the main code.](~/samples-luis/documentation-samples/quickstarts/change-model/csharp/ConsoleApp1/Program.cs?range=104-137 "To manage command-line arguments, add the main code.")]

### <a name="copy-utterancesjson-to-output-directory"></a>Copiar utterances.json para o diretório de saída

No Gerenciador de Soluções, adicione `utterances.json` clicando com o botão direito do mouse no nome do projeto do Gerenciador de Soluções e então selecionando **Adicionar** e **Item Existente**. Selecione o arquivo `utterances.json`. Isso adiciona o arquivo ao projeto. Em seguida, ele precisa ser adicionado para a direção de saída. Clique com o botão direito do mouse em `utterances.json` e selecione **Propriedades**. Nas janelas de propriedades, marque a **Ação de compilação** de `Content`e **Copiar para diretório de saída** de `Copy Always`.  

![Marcar o arquivo JSON como conteúdo](./media/luis-quickstart-cs-add-utterance/content-properties.png)

## <a name="build-code"></a>Compilar o código

Compile o código em Visual Studio. 

## <a name="run-code"></a>Executar código

No diretório /bin/Debug do projeto, execute o aplicativo de uma linha de comando. 

```console
ConsoleApp1.exe --add utterances.json --train --status
```

A linha de comando exibe os resultados da chamada da API para adicionar enunciados. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Limpar recursos
Quando você terminar com o início rápido, remova todos os arquivos criados nesse início rápido. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"] 
> [Criar um aplicativo LUIS programaticamente](luis-tutorial-node-import-utterances-csv.md) 
