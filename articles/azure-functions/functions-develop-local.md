---
title: Desenvolver e executar funções do Azure localmente | Microsoft Docs
description: Saiba como codificar e testar o Azure Functions no computador local antes de executá-las no Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 6486b38040fc92c2ece839801293817c4afc7cf5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60614003"
---
# <a name="code-and-test-azure-functions-locally"></a>Codificar e testar o Azure Functions localmente

Enquanto você é capaz de desenvolver e testar o Azure Functions no [Portal do Azure], muitos desenvolvedores preferem uma experiência de desenvolvimento local. O Functions facilita a utilização do seu editor de códigos favorito e das ferramentas de desenvolvimento para criar e testar as funções em seu computador local. Suas funções locais podem se conectar a serviços do Azure em tempo real e você pode depurá-las em seu computador local usando o tempo de execução total do Functions.

## <a name="local-development-environments"></a>Ambientes de desenvolvimento locais

A maneira como você desenvolve funções em seu computador local depende de seu [idioma](supported-languages.md) e preferências de ferramentas. Os ambientes na tabela a seguir dão suporte a desenvolvimento local:

|Ambiente                              |Languages         |DESCRIÇÃO|
|-----------------------------------------|------------|---|
| [Prompt de comando ou terminal](functions-run-local.md) | [C# (biblioteca de classes)](functions-dotnet-class-library.md), [script do C# (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | [Ferramentas básicas do Azure Functions] fornece os principais tempos de execução e modelos para a criação de funções, o que permite o desenvolvimento local. A versão 2.x oferece suporte a desenvolvimento em Linux, MacOS e Windows. Todos os ambientes contam com ferramentas essenciais para o tempo de execução local do Functions. |
|[Visual Studio Code](functions-create-first-function-vs-code.md)| [C# (biblioteca de classes)](functions-dotnet-class-library.md), [script do C# (.csx)](functions-reference-csharp.md), [JavaScript](functions-reference-node.md) | A [extensão do Azure Functions para VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) adiciona suporte ao Functions para o VS Code. Requer as Ferramentas Essenciais. Oferece suporte ao desenvolvimento em Linux, MacOS e Windows ao usar a versão 2.x das Ferramentas Essenciais. Para saber mais, confira [Criar sua primeira função usando o Visual Studio Code](functions-create-first-function-vs-code.md). |
| [Visual Studio 2017](functions-develop-vs.md) | [C# (biblioteca de classes)](functions-dotnet-class-library.md) | As ferramentas do Azure Functions estão incluídas na carga de trabalho de **desenvolvimento do Azure** do [Visual Studio 2017 versão 15.5](https://www.visualstudio.com/vs/) e posterior. Permite que você compile funções em uma biblioteca de classes e publique o arquivo .dll no Azure. Inclui as Ferramentas Essenciais para teste local. Para saber mais, consulte [Desenvolver Azure Functions usando o Visual Studio](functions-develop-vs.md). |
| [Maven](functions-create-first-java-maven.md) (diversos) | [Java](functions-reference-java.md) | Integra-se com as Ferramentas Essenciais para habilitar o desenvolvimento de funções Java. A versão 2.x oferece suporte a desenvolvimento em Linux, MacOS e Windows. Para saber mais, consulte [Criar sua primeira função com Java e Maven](functions-create-first-java-maven.md). Também dá suporte ao uso de desenvolvimento [Eclipse](functions-create-maven-eclipse.md) e [IntelliJ IDEA](functions-create-maven-intellij.md) |

[!INCLUDE [Don't mix development environments](../../includes/functions-mixed-dev-environments.md)]

Cada um desses ambientes de desenvolvimento local permite criar projetos de aplicativo de funções e usar modelos predefinidos do Functions para criar novas funções. Cada um usa as Ferramentas Essenciais para que você possa testar e depurar suas funções com relação ao tempo de execução do Functions em seu próprio computador, assim como faria com qualquer outro aplicativo. Você também pode publicar seu projeto de aplicativo de funções de qualquer um desses ambientes no Azure.  

## <a name="next-steps"></a>Próximas etapas

+ Para saber mais sobre o desenvolvimento local de funções de C# compiladas usando o Visual Studio 2017, consulte [Desenvolver o Azure Functions usando o Visual Studio](functions-develop-vs.md).
+ Para saber mais sobre o desenvolvimento local de funções usando o VS Code em um computador Mac, Linux ou Windows, consulte a [Documentação do VS Code para o Azure Functions](https://code.visualstudio.com/tutorials/functions-extension/getting-started).
+ Para saber mais sobre como desenvolver funções do prompt de comando ou do terminal, consulte [Trabalhar com as Ferramentas Essenciais do Azure Functions](functions-run-local.md).

<!-- LINKS -->

[Ferramentas básicas do Azure Functions]: https://www.npmjs.com/package/azure-functions-core-tools
[Portal do Azure]: https://portal.azure.com 
[Node.js]: https://docs.npmjs.com/getting-started/installing-node#osx-or-windows
