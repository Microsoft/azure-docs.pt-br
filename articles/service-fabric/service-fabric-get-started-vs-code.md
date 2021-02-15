---
title: Service Fabric do Azure com VS Code Introdução
description: Este artigo é uma visão geral da criação de aplicativos do Microsoft Azure Service Fabric usando o Visual Studio Code.
author: peterpogorski
ms.topic: article
ms.date: 06/29/2018
ms.author: pepogors
ms.custom: devx-track-js
ms.openlocfilehash: a655199d854462285ca98a2d8f454c483c061f99
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316666"
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
* [SDK do Service Fabric](./service-fabric-get-started.md)
* Geradores do yeoman – instale os geradores apropriados para seu aplicativo

   ```sh
   npm install -g yo
   npm install -g generator-azuresfjava
   npm install -g generator-azuresfcsharp
   npm install -g generator-azuresfcontainer
   npm install -g generator-azuresfguest
   ```

Os pré-requisitos a seguir devem ser instalados em todos os ambientes:

* [SDK Java](/azure/developer/java/fundamentals/java-jdk-long-term-support) para o Lote versão 1.8
* [Gradle](https://gradle.org/install/)
* [Depurador para a extensão do Visual Studio Code do Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug) necessário para depurar serviços Java. A depuração dos serviços Java tem suporte somente no Linux. Você pode instalar clicando no ícone de extensões na **Barra de Atividade** no Visual Studio Code e pesquisa para a extensão ou do Marketplace do Visual Studio Code.

Os pré-requisitos a seguir devem ser instalados para o desenvolvimento .NET Core/C#:

* [.NET Core](https://www.microsoft.com/net/learn/get-started) versão 2.0.0 ou posterior
* [C# para extensão do Visual Studio Code (fornecido por OmniSharp) VS Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) necessários para depurar serviços de linguagem C#. Você pode instalar clicando no ícone de extensões na **Barra de Atividade** no Visual Studio Code e pesquisa para a extensão ou do Marketplace do Visual Studio Code.

## <a name="setup"></a>Instalação

1. Abra o VS Code.
2. Clique no ícone Extensões na **Barra de Atividades** no lado do Visual Studio Code. Pesquisar “Service Fabric”. Clique em **Instalar** para a extensão do Service Fabric Reliable Services.

## <a name="commands"></a>Comandos
A extensão de serviços confiáveis do Service Fabric para VS Code fornece muitos comandos para ajudar os desenvolvedores a criar e implantar projetos do Service Fabric. Você pode chamar comandos da **paleta de comandos** pressionando `(Ctrl + Shift + p)`, digitando o nome do comando na barra de entrada e selecionando o comando desejado da lista de avisos. 

* Microsoft Azure Service Fabric: Criar aplicativo 
* Microsoft Azure Service Fabric: Publicar aplicativo 
* Microsoft Azure Service Fabric: Implantar aplicativo 
* Microsoft Azure Service Fabric: Remover aplicativo  
* Microsoft Azure Service Fabric: Compilar aplicativo 
* Microsoft Azure Service Fabric: Limpar aplicativo 

### <a name="service-fabric-create-application"></a>Microsoft Azure Service Fabric: Criar aplicativo

O comando **Microsoft Azure Service Fabric: criar aplicativo** cria um novo aplicativo do Microsoft Azure Service Fabric no workspace atual. Dependendo de quais geradores yeoman estão instalados no computador de desenvolvimento, você pode criar vários tipos de aplicativo do Microsoft Azure Service Fabric, incluindo projetos Java, C#, contêiner e convidado. 

1.  Selecione o comando **Service Fabric: criar aplicativo**
2.  Selecione o tipo para o novo aplicativo do Microsoft Azure Service Fabric. 
3.  Insira o nome do aplicativo que você deseja criar
3.  Selecione o tipo de serviço que você deseja adicionar ao seu aplicativo do Microsoft Azure Service Fabric. 
4.  Siga os prompts para nomear o serviço. 
5.  O novo aplicativo do Microsoft Azure Service Fabric aparece no workspace.
6.  Abra a nova pasta de aplicativo para que ele se torne a pasta raiz no workspace. Você pode continuar a execução de comandos aqui.

### <a name="service-fabric-add-service"></a>Microsoft Azure Service Fabric: Adicionar Serviço
O comando do **Microsoft Service Fabric: Adicionar Serviço** adiciona um novo serviço a um aplicativo do Microsoft Azure Service Fabric existente. O aplicativo que o serviço será adicionado ao deve ser o diretório raiz do workspace. 

1.  Selecione o comando **Service Fabric: Adicionar serviço** .
2.  Selecione o tipo para o aplicativo do Microsoft Azure Service Fabric atual. 
3.  Selecione o tipo de serviço que você deseja adicionar ao seu aplicativo do Microsoft Azure Service Fabric. 
4.  Siga os prompts para nomear o serviço. 
5.  O novo serviço aparece no diretório do projeto. 

### <a name="service-fabric-publish-application"></a>Microsoft Azure Service Fabric: Publicar aplicativo
O comando do **Microsoft Azure Service Fabric: publicar aplicativo** comando o seu aplicativo Microsoft Azure Service Fabric em um cluster remoto. O cluster de destino pode ser um site seguro ou um cluster não seguro. Se os parâmetros não são definidos no Cloud.json, o aplicativo é implantado no cluster local.

1.  Na primeira vez que o aplicativo é compilado, um arquivo de Cloud.json é gerado no diretório do projeto.
2.  Insira os valores para o cluster que você gostaria de se conectar no arquivo Cloud.json.
3.  Selecione o comando **Microsoft Azure Service Fabric: Publicar aplicativo**.
4.  Exiba o cluster de destino com o Service Fabric Explorer para confirmar que o aplicativo foi instalado. 

### <a name="service-fabric-deploy-application-localhost"></a>Microsoft Azure Service Fabric: Implantar aplicativo (Localhost)
O comando do **Microsoft Azure Service Fabric: publicar aplicativo** implanta o seu aplicativo no Microsoft Azure Service Fabric em um cluster remoto. Verifique se que o cluster local está em execução antes de usar o comando. 

1. Selecione o comando **Microsoft Azure Service Fabric: Implantar aplicativo**
2. Exiba o cluster local com Service Fabric Explorer (http: \/ /localhost: 19080/Explorer) para confirmar que o aplicativo foi instalado. Isso pode levar algum tempo, portanto seja paciente.
3. Você também pode usar o comando **Service Fabric: publicar aplicativo** sem parâmetros definidos no arquivo de Cloud.json para implantar um cluster local.

> [!NOTE]
> Não há suporte para a implantação de aplicativos Java para o cluster local nos computadores do Windows.

### <a name="service-fabric-remove-application"></a>Microsoft Azure Service Fabric: Remover aplicativo
O comando **Microsoft Azure Service Fabric: remover aplicativo** remove um aplicativo do Microsoft Azure Service Fabric do cluster que ele foi implantado anteriormente para usar a extensão do Visual Studio Code. 

1.  Selecione o comando **Microsoft Azure Service Fabric: Remover aplicativo**.
2.  Exiba o cluster local com o Service Fabric Explorer para confirmar que o aplicativo foi removido. Isso pode levar algum tempo, portanto seja paciente.

### <a name="service-fabric-build-application"></a>Microsoft Azure Service Fabric: Compilar aplicativo
O comando **Service Fabric: Compilar aplicativo** pode criar aplicativos Java ou C# Service Fabric. 

1.  Verifique se que você está na pasta raiz do aplicativo antes de executar esse comando. O comando identifica o tipo de aplicativo (C# ou Java) e compila seu aplicativo adequadamente.
2.  Selecione o comando **Microsoft Azure Service Fabric: Compilar aplicativo**.
3.  A saída do processo de compilação é enviada para o terminal integrado.

### <a name="service-fabric-clean-application"></a>Microsoft Azure Service Fabric: Limpar aplicativo
O comando **Microsoft Azure Service Fabric: limpar aplicativo** exclui todos os arquivos jar e bibliotecas nativas que foram geradas pela compilação. Válido para apenas aplicativos Java. 

1.  Verifique se que você está na pasta raiz do aplicativo antes de executar esse comando. 
2.  Selecione o comando **Microsoft Azure Service Fabric: Limpar aplicativo**.
3.  A saída do processo de compilação é enviada para o terminal integrado.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [Desenvolver aplicativos Service Fabric do C# com Visual Studio Code](./service-fabric-develop-csharp-applications-with-vs-code.md).
* Saiba como [desenvolver aplicativos Microsoft Azure Service Fabric Java com Visual Studio Code](./service-fabric-develop-java-applications-with-vs-code.md).