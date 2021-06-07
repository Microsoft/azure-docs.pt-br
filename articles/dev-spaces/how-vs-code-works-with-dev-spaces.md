---
title: Como Visual Studio Code funciona com Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Saiba como Visual Studio Code e Azure Dev Spaces ajudá-lo a depurar e iterar rapidamente seus aplicativos kubernetes
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres
ms.openlocfilehash: edfcb8107280bb86144b798da2d5b1c16371528e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91960738"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Como Visual Studio Code funciona com Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Você pode usar Visual Studio Code e a [extensão Azure dev Spaces][azds-extension] para preparar, executar e depurar seus serviços com o Azure dev Spaces. Com Visual Studio Code e a extensão de Azure Dev Spaces, você pode:

* Gerar ativos para executar e depurar serviços no AKS
* Executar seus serviços Java, Node.js e .NET Core em um espaço de desenvolvimento
* Depure diretamente seus serviços Java, Node.js e .NET Core em execução em um espaço de desenvolvimento

## <a name="generate-assets"></a>Gerar ativos

Visual Studio Code e a extensão Azure Dev Spaces geram os seguintes ativos para seu projeto:

* Dockerfiles para aplicativos Java usando Maven, aplicativos Node.js e aplicativos .NET Core
* Gráficos Helm para quase qualquer linguagem com um Dockerfile
* Um `azds.yaml` arquivo, que é o [arquivo de configuração Azure dev Spaces][azds-yaml] para seu projeto
* Uma `.vscode` pasta com o Visual Studio Code iniciar a configuração do seu projeto para aplicativos Java usando Maven, aplicativos Node.js e aplicativos .NET Core

O Dockerfile, o gráfico do Helm e os `azds.yaml` arquivos são os mesmos ativos gerados durante a execução `azds prep` . Esses arquivos também podem ser usados fora do Visual Studio Code para executar seu projeto no AKS, como a execução de `azds up` . A `.vscode` pasta é usada apenas pelo Visual Studio Code para executar seu projeto no AKs da Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Execute seu serviço no AKS

Depois de gerar os ativos para seu projeto, você pode executar os serviços Java, Node.js e .NET Core em um espaço de desenvolvimento existente do Visual Studio Code. Na página de *depuração* do Visual Studio Code, você pode invocar a configuração de inicialização do `.vscode` diretório para executar o projeto.

Você deve criar o cluster AKS e habilitar Azure Dev Spaces em seu cluster fora do Visual Studio Code. Você pode reutilizar Dockerfiles existentes, gráficos do Helm e `azds.yaml` arquivos criados fora do Visual Studio Code, como os ativos gerados pela execução `azds prep` . Se você reutilizar ativos gerados fora do Visual Studio Code, ainda precisará ter um `.vscode` diretório. Esse `.vscode` diretório pode ser regenerado pelo Visual Studio Code e pela extensão Azure dev Spaces e não substituirá os ativos existentes.

Para projetos do .NET Core, você deve ter a [extensão C#][csharp-extension] instalada para executar o serviço .net de Visual Studio Code. Além disso, para projetos Java usando o Maven, você deve ter o [depurador Java para Azure dev Spaces extensão][java-extension] instalada, bem como o [Maven instalado e configurado][maven] para executar o serviço Java do Visual Studio Code.

## <a name="debug-your-service-in-aks"></a>Depurar seu serviço no AKS

Depois de iniciar o projeto, você pode depurar os serviços Java, Node.js e .NET Core em execução em um espaço de desenvolvimento diretamente do Visual Studio Code. A configuração de inicialização no `.vscode` diretório fornece informações adicionais de depuração para executar um serviço com depuração habilitada em um espaço de desenvolvimento. O Visual Studio Code também é anexado ao processo de depuração no contêiner em execução em seus espaços de desenvolvimento, permitindo que você defina pontos de interrupção, inspecione variáveis e execute outras operações de depuração.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como funciona o Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como o Azure Dev Spaces funciona](how-dev-spaces-works.md)

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
