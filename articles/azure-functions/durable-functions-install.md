---
title: "Instalar a extensão de Funções Duráveis e exemplos – Azure"
description: "Saiba como instalar a extensão de Funções Duráveis do Azure Functions, para desenvolvimento no portal ou desenvolvimento no Visual Studio."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: debadde78d937bcd4ec1df665aacfd1887fbcd02
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="install-the-durable-functions-extension-and-samples-azure-functions"></a>Instalar a extensão de Funções Duráveis e exemplos (Azure Functions)

A extensão [Funções Duráveis](durable-functions-overview.md) do Azure Functions é fornecida no pacote de NuGet [Microsoft.Azure.WebJobs.Extensions.DurableTask](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask). Este artigo mostra como instalar o pacote, bem como um conjunto de exemplos para os ambientes de desenvolvimento a seguir:

* Visual Studio 2017 (recomendado) 
* Portal do Azure

## <a name="visual-studio-2017"></a>Visual Studio 2017

Atualmente, o Visual Studio fornece a melhor experiência para desenvolver aplicativos que usam Funções Duráveis.  Suas funções podem ser executadas localmente e também podem ser publicadas no Azure. Você pode começar com um projeto vazio ou com um conjunto de funções de exemplo.

### <a name="prerequisites"></a>Pré-requisitos

* Instale a [versão mais recente do Visual Studio](https://www.visualstudio.com/downloads/) (versão 15.3 ou posterior). Inclua as ferramentas do Azure em suas opções de instalação.

### <a name="start-with-sample-functions"></a>Comece com as funções de exemplo

1. Baixe o [arquivo .zip do Aplicativo de Exemplo para o Visual Studio](https://azure.github.io/azure-functions-durable-extension/files/VSDFSampleApp.zip). Você não precisa adicionar a referência do NuGet, porque o projeto de exemplo já a tem.
2. Instale e execute o [Emulador de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/storage-use-emulator) 5.2 ou posterior. Como alternativa, você pode atualizar o arquivo *local.appsettings.json* com cadeias de conexão reais do Armazenamento do Azure.
3. Abra o projeto no Visual Studio 2017. 
4. Para obter instruções de como executar o exemplo, comece com [Encadeamento de funções – Sequência de exemplo Hello](durable-functions-sequence.md). O exemplo pode ser executado localmente ou publicado no Azure.

### <a name="start-with-an-empty-project"></a>Comece com um projeto vazio
 
Siga as mesmas instruções para começar com o exemplo, mas execute as etapas a seguir em vez de baixar o arquivo *.zip*:

1. Crie um projeto de Aplicativo de Funções.
2. Adicione a seguinte referência de pacote do NuGet ao seu arquivo *.csproj*:

   ```xml
   <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.DurableTask" Version="1.0.0-beta" />
   ```

## <a name="azure-portal"></a>Portal do Azure

Se preferir, você poderá usar o portal do Azure para o desenvolvimento de Funções Duráveis.

### <a name="create-an-orchestrator-function"></a>Crie uma função de orquestrador

1. Crie um novo aplicativo de funções em [functions.azure.com](https://functions.azure.com/signin).
2. Configure o aplicativo de funções para [usar a versão de tempo de execução 2.0](functions-versions.md).
3. Crie uma nova função e selecione o modelo **Orquestrador de Funções Duráveis – C#**.
4. Em **Extensões não instaladas**, clique em **Instalar** para baixar a extensão de NuGet.org.

### <a name="copy-sample-code-to-the-function-app"></a>Copie o código de exemplo para o aplicativo de funções

1. Baixe o arquivo [DFSampleApp.zip](https://github.com/Azure/azure-functions-durable-extension/raw/master/docfx/files/DFSampleApp.zip).
2. Descompacte o arquivo de exemplo em `D:\home\site\wwwroot` no aplicativo de funções usando Kudu ou FTP.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Executar o exemplo de encadeamento de funções](durable-functions-sequence.md)
