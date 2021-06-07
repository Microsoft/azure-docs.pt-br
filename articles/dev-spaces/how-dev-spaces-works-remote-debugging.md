---
title: Como a depuração remota de seu código com Azure Dev Spaces funciona
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Descreve os processos de depuração remota no serviço kubernetes do Azure com Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres
ms.openlocfilehash: 0487b80d23974a66bafe93ee1fbdf9b796d0ab53
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91975035"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Como a depuração remota de seu código com Azure Dev Spaces funciona

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

O Azure Dev Spaces fornece várias maneiras de iterar e depurar rapidamente aplicativos Kubernetes e colaborar com sua equipe em um cluster do AKS (Serviço de Kubernetes do Azure). Depois que o projeto estiver sendo executado em um espaço de desenvolvimento, Azure Dev Spaces fornecerá uma maneira de anexar e depurar um aplicativo em execução no AKS.

Este artigo descreve como a depuração remota com espaços de desenvolvimento funciona.

## <a name="debug-your-code"></a>Depurar seu código

Para aplicativos Java, .NET Core e Node.js, você pode depurar seu aplicativo em execução diretamente em seu espaço de desenvolvimento usando o Visual Studio Code ou o Visual Studio. Visual Studio Code e o Visual Studio fornecem ferramentas para se conectar ao seu espaço de desenvolvimento, iniciar o aplicativo e anexar um depurador. Após a execução `azds prep` , você pode abrir seu projeto no Visual Studio Code ou no Visual Studio. Visual Studio Code ou o Visual Studio gerará seus próprios arquivos de configuração para conexão, que é separado da execução `azds prep` . De dentro do Visual Studio Code ou do Visual Studio, você pode definir pontos de interrupção e iniciar seu aplicativo para o espaço de desenvolvimento.

![Depurando seu código](media/get-started-node/debug-configuration-nodejs2.png)

Quando você inicia seu aplicativo usando Visual Studio Code ou o Visual Studio para depuração, eles lidam com a inicialização e a conexão ao seu espaço de desenvolvimento da mesma maneira que a execução `azds up` . Além disso, as ferramentas do lado do cliente no Visual Studio Code e no Visual Studio fornecem um parâmetro adicional com informações específicas para depuração. O parâmetro contém o nome da imagem do depurador, o local do depurador dentro da imagem do depurador e o local de destino dentro do contêiner do aplicativo para montar a pasta do depurador.

A imagem do depurador é determinada automaticamente pelas ferramentas do lado do cliente. Ele usa um método semelhante ao usado durante a geração do gráfico Dockerfile e Helm durante a execução `azds prep` . Depois que o depurador é montado na imagem do aplicativo, ele é executado usando `azds exec` .

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como funciona o Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como o Azure Dev Spaces funciona](how-dev-spaces-works.md)
