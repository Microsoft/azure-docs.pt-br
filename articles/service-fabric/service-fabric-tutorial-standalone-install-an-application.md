---
title: Instalar um aplicativo em um cluster autônomo
description: Neste tutorial, você aprenderá a implantar um aplicativo no cluster autônomo do Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: ae946321b34f12c816a717db4a3d07f57feefe52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96485353"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Tutorial: Implantar um aplicativo no seu cluster do Service Fabric autônomo

Os clusters autônomos do Service Fabric oferecem a opção de escolher seu próprio ambiente e criar um cluster como parte da abordagem “qualquer sistema operacional, qualquer nuvem” que está sendo adotada pelo Service Fabric. Nesta série de tutoriais, você criará um cluster autônomo hospedado no AWS e implantará um aplicativo nele.

Este tutorial é a parte três de uma série.  Os clusters autônomos do Service Fabric oferecem a opção de escolher seu próprio ambiente e criar um cluster como parte de nossa abordagem "qualquer sistema operacional, qualquer nuvem" com o Service Fabric. Este tutorial mostra como criar a infraestrutura AWS necessária para hospedar este cluster autônomo.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Baixar o aplicativo de exemplo
> * Implante-o no cluster

## <a name="prerequisites"></a>Prerequisites

Antes de começar este tutorial:

* [Instale o Visual Studio 2019](https://www.visualstudio.com/) e as cargas de trabalho **Desenvolvimento do Azure** e **Desenvolvimento para a Web e ASP.NET**.
* [Instalar o SDK do Service Fabric](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Baixar o aplicativo de exemplo Votação

Em uma janela de comando, execute o comando a seguir para clonar o repositório de aplicativos de exemplo no computador local.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Implantar o aplicativo no cluster do Service Fabric

Após o aplicativo ser baixado, você poderá implantá-lo no cluster diretamente por meio do Visual Studio.

1. Abra o Visual Studio

2. Selecione **Arquivo** > **Abrir**

3. Navegue até a pasta para a qual você clonou o repositório do git e selecione Voting.sln

4. Clique com botão direito do mouse no projeto de aplicativo `Voting` no Gerenciador de Soluções e escolha **Publicar**

5. Selecione o menu suspenso para a **Ponto de extremidade de conexão** e insira o nome de DNS público de um dos nós no seu cluster.  Por exemplo, `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. No Azure, um FQDN (nome de domínio totalmente qualificado) não é fornecido automaticamente, mas pode ser facilmente [definido na página Visão Geral de VM.](../virtual-machines/create-fqdn.md)

6. Abra o seu navegador de preferência e digite o endereço do cluster (o ponto de extremidade de conexão, este aplicativo implanta na porta 8080 - por exemplo, ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![Resposta de API do Cluster](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a implantar um aplicativo em seu cluster:

> [!div class="checklist"]
> * Baixar o aplicativo de exemplo
> * Implante-o no cluster

Avance para a parte quatro da série para limpar o cluster.

> [!div class="nextstepaction"]
> [Limpar seus recursos](service-fabric-tutorial-standalone-clean-up.md)