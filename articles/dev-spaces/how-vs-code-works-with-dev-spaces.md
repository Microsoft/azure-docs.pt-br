---
title: Como Visual Studio Code funciona com Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/08/2019
ms.topic: conceptual
description: Como Visual Studio Code funciona com Azure Dev Spaces
keywords: Azure Dev Spaces, espaços de desenvolvimento, Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres
ms.openlocfilehash: 0d80643b366b6d7313f24e73258056e492eb56fc
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297863"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Como Visual Studio Code funciona com Azure Dev Spaces

Você pode usar Visual Studio Code e a [extensão Azure dev Spaces][azds-extension] para preparar, executar e depurar seus serviços com o Azure dev Spaces. Com Visual Studio Code e a extensão de Azure Dev Spaces, você pode:

* Gerar ativos para executar e depurar serviços no AKS
* Executar seus serviços Java, Node. js e .NET Core em um espaço de desenvolvimento
* Depure diretamente seus serviços Java, Node. js e .NET Core em execução em um espaço de desenvolvimento

## <a name="generate-assets"></a>Gerar ativos

Visual Studio Code e a extensão Azure Dev Spaces geram os seguintes ativos para seu projeto:

* Dockerfiles para aplicativos Java usando Maven, aplicativos node. js e aplicativos .NET Core
* Gráficos Helm para quase qualquer linguagem com um Dockerfile
* Um `azds.yaml` arquivo, que é o [arquivo de configuração Azure dev Spaces][azds-yaml] para seu projeto
* Uma `.vscode` pasta com o Visual Studio Code iniciar a configuração do seu projeto para aplicativos Java usando o Maven, aplicativos node. js e aplicativos .NET Core

O Dockerfile, o gráfico do Helm `azds.yaml` e os arquivos são os mesmos ativos gerados `azds prep`durante a execução. Esses arquivos também podem ser usados fora do Visual Studio Code para executar seu projeto no AKS, como a execução `azds up`de. A `.vscode` pasta é usada apenas pelo Visual Studio Code para executar seu projeto no AKs da Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Execute seu serviço no AKS

Depois de gerar os ativos para seu projeto, você pode executar os serviços Java, Node. js e .NET Core em um espaço de desenvolvimento existente do Visual Studio Code. Na página de *depuração* do Visual Studio Code, você pode invocar a configuração de inicialização `.vscode` do diretório para executar o projeto.

Você deve criar o cluster AKS e habilitar Azure Dev Spaces em seu cluster fora do Visual Studio Code. Por exemplo, você pode usar o CLI do Azure ou o portal do Azure para fazer essa configuração. Você pode reutilizar Dockerfiles existentes, gráficos do Helm `azds.yaml` e arquivos criados fora do Visual Studio Code, como os ativos gerados pela execução `azds prep`. Se você reutilizar ativos gerados fora do Visual Studio Code, ainda precisará ter um `.vscode` diretório. Esse `.vscode` diretório pode ser regenerado pelo Visual Studio Code e pela extensão Azure dev Spaces e não substituirá os ativos existentes.

Para projetos do .NET Core, você deve ter a installed to run your .NET service from Visual Studio Code. Also for Java projects using Maven, you must have the [Java Debugger for Azure Dev Spaces extension][java-extension] [ C# extensão][csharp-extension] instalada, bem como o Maven [instalado e configurado][Maven] para executar seu serviço Java a partir de Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Depurar seu serviço no AKS

Depois de iniciar o projeto, você pode depurar os serviços Java, Node. js e .NET Core em execução em um espaço de desenvolvimento diretamente do Visual Studio Code. A configuração de inicialização no `.vscode` diretório fornece informações adicionais de depuração para executar um serviço com depuração habilitada em um espaço de desenvolvimento. O Visual Studio Code também é anexado ao processo de depuração no contêiner em execução em seus espaços de desenvolvimento, permitindo que você defina pontos de interrupção, inspecione variáveis e execute outras operações de depuração.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Usar Visual Studio Code com Azure Dev Spaces

Você pode ver Visual Studio Code e a extensão de Azure Dev Spaces trabalhando com Azure Dev Spaces nos guias de início rápido a seguir:

* [Desenvolver com Java][quickstart-java]
* [Desenvolver com o .NET][quickstart-netcore]
* [Desenvolver com node. js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
