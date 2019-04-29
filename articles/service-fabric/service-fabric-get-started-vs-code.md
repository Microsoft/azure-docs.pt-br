---
title: Azure Service Fabric com a Introdução do Visual Studio Code | Microsoft Docs
description: Este artigo é uma visão geral da criação de aplicativos do Microsoft Azure Service Fabric usando o Visual Studio Code.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2018
ms.author: pepogors
ms.openlocfilehash: f977a48338f784562ec84355aabb212e5a3dade4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60946531"
---
# <a name="service-fabric-for-visual-studio-code"></a>Microsoft Azure Service Fabric para Visual Studio Code

A [extensão de serviços confiáveis do Service Fabric para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-service-fabric-reliable-services) fornece as ferramentas necessárias para criar, construir e depurar os aplicativos do Microsoft Azure Service Fabric nos sistemas operacionais Windows, Linux e macOS.

Este artigo fornece uma visão geral dos requisitos e instalação da extensão, bem como o uso dos vários comandos que são fornecidos pela extensão. 

> [!IMPORTANT]
> Aplicativos do Java do Microsoft Service Fabric podem ser desenvolvidos em máquinas Windows, mas podem ser implantados nos clusters do Linux do Azure apenas. Não há suporte para a depuração de aplicativos Java no Windows.

## <a name="prerequisites"></a>Pré-requisitos

Os pré-requisitos a seguir devem ser instalados em todos os ambientes.

* [Visual Studio Code](https://code.visualstudio.com/)
* [Node.js](https://nodejs.org/)
* [Git](https://git-scm.com/)
* [SDK do Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)
* Geradores do yeoman – instale os geradores apropriados para seu aplicativo

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Os pré-requisitos a seguir devem ser instalados em todos os ambientes:

* [SDK Java](https://aka.ms/azure-jdks) para o Lote versão 1.8
* [Gradle](https://gradle.org/install/)
* [Depurador para a extensão do Visual Studio Code do Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) necessário para depurar serviços Java. A depuração dos serviços Java tem suporte somente no Linux. Você pode instalar clicando no ícone de extensões na **Barra de Atividade** no Visual Studio Code e pesquisa para a extensão ou do Marketplace do Visual Studio Code.

Os pré-requisitos a seguir devem ser instalados para o desenvolvimento .NET Core/C#:

* [.NET Core](https://www.microsoft.com/net/learn/get-started) versão 2.0.0 ou posterior
* [C# para extensão do Visual Studio Code (fornecido por OmniSharp) VS Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) necessários para depurar serviços de linguagem C#. Você pode instalar clicando no ícone de extensões na **Barra de Atividade** no Visual Studio Code e pesquisa para a extensão ou do Marketplace do Visual Studio Code.

## <a name="setup"></a>Configuração

1. Abra o VS Code.
2. Clique no ícone Extensões na **Barra de Atividades** no lado do Visual Studio Code. Pesquisar “Service Fabric”. Clique em **Instalar** para a extensão do Service Fabric Reliable Services.

## <a name="commands"></a>Comandos
A extensão de serviços confiáveis do Service Fabric para VS Code fornece muitos comandos para ajudar os desenvolvedores a criar e implantar projetos do Service Fabric. Você pode chamar comandos da **paleta de comandos** pressionando `(Ctrl + Shift + p)`, digitando o nome do comando na barra de entrada e selecionando o comando desejado da lista de avisos. 

* O Service Fabric: Criar um aplicativo 
* O Service Fabric: Publicar aplicativo 
* O Service Fabric: Implantar aplicativo 
* O Service Fabric: Remover Aplicativo  
* O Service Fabric: Criar aplicativo 
* O Service Fabric: Limpar aplicativo 

### <a name="service-fabric-create-application"></a>O Service Fabric: Criar um aplicativo

O **do Service Fabric: Criar aplicativo** comando cria um novo aplicativo do Service Fabric no espaço de trabalho atual. Dependendo de quais geradores yeoman estão instalados no computador de desenvolvimento, você pode criar vários tipos de aplicativo do Microsoft Azure Service Fabric, incluindo projetos Java, C#, contêiner e convidado. 

1.  Selecione o **do Service Fabric: Adicionar serviço** comando
2.  Selecione o tipo para o novo aplicativo do Microsoft Azure Service Fabric. 
3.  Insira o nome do aplicativo que você deseja criar
3.  Selecione o tipo de serviço que você deseja adicionar ao seu aplicativo do Microsoft Azure Service Fabric. 
4.  Siga os prompts para nomear o serviço. 
5.  O novo aplicativo do Microsoft Azure Service Fabric aparece no workspace.
6.  Abra a nova pasta de aplicativo para que ele se torne a pasta raiz no workspace. Você pode continuar a execução de comandos aqui.

### <a name="service-fabric-add-service"></a>O Service Fabric: Adicionar Serviço
O **do Service Fabric: Adicionar serviço** comando adiciona um novo serviço para um aplicativo do Service Fabric existente. O aplicativo que o serviço será adicionado ao deve ser o diretório raiz do workspace. 

1.  Selecione o **do Service Fabric: Adicionar serviço** comando.
2.  Selecione o tipo para o aplicativo do Microsoft Azure Service Fabric atual. 
3.  Selecione o tipo de serviço que você deseja adicionar ao seu aplicativo do Microsoft Azure Service Fabric. 
4.  Siga os prompts para nomear o serviço. 
5.  O novo serviço aparece no diretório do projeto. 

### <a name="service-fabric-publish-application"></a>O Service Fabric: Publicar aplicativo
O **do Service Fabric: Publicar aplicativo** comando implanta o aplicativo do Service Fabric em um cluster remoto. O cluster de destino pode ser um site seguro ou um cluster não seguro. Se os parâmetros não são definidos no Cloud.json, o aplicativo é implantado no cluster local.

1.  Na primeira vez que o aplicativo é compilado, um arquivo de Cloud.json é gerado no diretório do projeto.
2.  Insira os valores para o cluster que você gostaria de se conectar no arquivo Cloud.json.
3.  Selecione o **do Service Fabric: Publicar aplicativo** comando.
4.  Exiba o cluster de destino com o Service Fabric Explorer para confirmar que o aplicativo foi instalado. 

### <a name="service-fabric-deploy-application-localhost"></a>O Service Fabric: Implantar o aplicativo (Localhost)
O **do Service Fabric: Implantar aplicativo** comando implanta o aplicativo do Service Fabric no cluster local. Verifique se que o cluster local está em execução antes de usar o comando. 

1. Selecione o **do Service Fabric: Implantar aplicativo** comando
2. Exibir o cluster local com o Service Fabric Explorer (http:\//localhost:19080 / Explorer) para confirmar que o aplicativo foi instalado. Isso pode levar algum tempo, portanto seja paciente.
3. Você também pode usar **do Service Fabric: Publicar aplicativo** comando sem parâmetros definido no arquivo de Cloud para implantar um cluster local.

> [!NOTE]
> Não há suporte para a implantação de aplicativos Java para o cluster local nos computadores do Windows.

### <a name="service-fabric-remove-application"></a>O Service Fabric: Remover Aplicativo
O **do Service Fabric: Remover aplicativo** comando remove um aplicativo do Service Fabric do cluster que ele foi implantado anteriormente para usar a extensão do VS Code. 

1.  Selecione o **do Service Fabric: Remover aplicativo** comando.
2.  Exiba o cluster local com o Service Fabric Explorer para confirmar que o aplicativo foi removido. Isso pode levar algum tempo, portanto seja paciente.

### <a name="service-fabric-build-application"></a>O Service Fabric: Criar aplicativo
O **do Service Fabric: Criar aplicativo** comando pode compilar o Java ou C# aplicativos do Service Fabric. 

1.  Verifique se que você está na pasta raiz do aplicativo antes de executar esse comando. O comando identifica o tipo de aplicativo (C# ou Java) e compila seu aplicativo adequadamente.
2.  Selecione o **do Service Fabric: Compilar Aplicativo**.
3.  A saída do processo de compilação é enviada para o terminal integrado.

### <a name="service-fabric-clean-application"></a>O Service Fabric: Limpar aplicativo
O **do Service Fabric: Limpar aplicativo** comando exclui todos os arquivos jar e bibliotecas nativas que foram geradas pela compilação. Válido para apenas aplicativos Java. 

1.  Verifique se que você está na pasta raiz do aplicativo antes de executar esse comando. 
2.  Selecione o **do Service Fabric: Limpar aplicativo** comando.
3.  A saída do processo de compilação é enviada para o terminal integrado.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [Desenvolver aplicativos Service Fabric do C# com Visual Studio Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Saiba como [desenvolver aplicativos Microsoft Azure Service Fabric Java com Visual Studio Code](./service-fabric-develop-java-applications-with-vs-code.md).
