---
title: Entender o analisador do modelo digital gêmeos | Microsoft Docs
description: Como desenvolvedor, saiba como usar o analisador DTDL para validar modelos.
author: rido-min
ms.author: rmpablos
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d68abe8548dac3306228683e4b6ce8935a248ebc
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331780"
---
# <a name="understand-the-digital-twins-model-parser"></a>Entender o analisador do modelo dos Gêmeos Digitais

A DTDL (digital gêmeos Definition Language) é descrita na [especificação DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Os usuários podem usar o pacote NuGet do _digital gêmeos Model Parser_ para validar e consultar um modelo definido em vários arquivos.

## <a name="install-the-dtdl-model-parser"></a>Instalar o analisador de modelo DTDL

O analisador está disponível em NuGet.org com a ID: [Microsoft. Azure. DigitalTwins. Parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Para instalar o analisador, use qualquer Gerenciador de pacotes NuGet compatível, como aquele no Visual Studio ou na `dotnet` CLI.

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

> [!NOTE]
> No momento da gravação, a versão do analisador é `3.12.5` .

## <a name="use-the-parser-to-validate-a-model"></a>Usar o analisador para validar um modelo

Um modelo pode ser composto por uma ou mais interfaces descritas em arquivos JSON. Você pode usar o analisador para carregar todos os arquivos em uma determinada pasta e usar o analisador para validar todos os arquivos como um todo, incluindo quaisquer referências entre os arquivos:

1. Crie um `IEnumerable<string>` com uma lista de todo o conteúdo do modelo:

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. Instanciar a `ModelParser` chamada e `ParseAsync` :

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. Verifique se há erros de validação. Se o analisador encontrar erros, ele lançará um `ParsingException` com uma lista de erros:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (ParsingException pex)
    {
        Console.WriteLine(pex.Message);
        foreach (var err in pex.Errors)
        {
            Console.WriteLine(err.PrimaryID);
            Console.WriteLine(err.Message);
        }
    }
    ```

1. Inspecione o `Model` . Se a validação for realizada com sucesso, você poderá usar a API do analisador de modelo para inspecionar o modelo. O trecho de código a seguir mostra como iterar todos os modelos analisados e exibe as propriedades existentes:

    ```csharp
    foreach (var item in parseResult)
    {
        Console.WriteLine($"\t{item.Key}");
        Console.WriteLine($"\t{item.Value.DisplayName?.Values.FirstOrDefault()}");
    }
    ```

## <a name="next-steps"></a>Próximas etapas

A API do analisador de modelo revisada neste artigo habilita muitos cenários para automatizar ou validar tarefas que dependem de modelos de DTDL. Por exemplo, você pode criar dinamicamente uma interface do usuário com base nas informações no modelo.
