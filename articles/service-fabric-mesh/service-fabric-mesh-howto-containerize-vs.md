---
title: Colocar um aplicativo .NET existente em um contêiner da Malha do Service Fabric
description: Adicione Service Fabric suporte de orquestração de contêiner de malha a projetos de console e ASP.NET que usam o .NET Framework completo.
author: georgewallace
ms.author: gwallace
ms.date: 11/08/2018
ms.topic: conceptual
ms.openlocfilehash: b9c5053a2a49c942cc89bd50c65e13f3a2f8d9d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625865"
---
# <a name="containerize-an-existing-net-app-for-service-fabric-mesh"></a>Colocar um aplicativo .NET existente em um contêiner da Malha do Service Fabric

> [!IMPORTANT]
> A versão prévia da Malha do Azure Service Fabric foi desativada. Não serão mais permitidas novas implantações por meio da API da Malha do Service Fabric. O suporte para as implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, confira [Desativação da versão prévia da Malha do Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Este artigo mostra como adicionar suporte à orquestração do contêiner da Malha do Service Fabric para um aplicativo .NET existente.

No Visual Studio 2017, é possível adicionar suporte à geração de contêineres para projetos ASP.NET e de Console que usam a estrutura completa do .NET.

> [!NOTE]
> Atualmente, não há suporte para projetos do .NET **Core**.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Verifique se você [configurou o ambiente de desenvolvimento](service-fabric-mesh-howto-setup-developer-environment-sdk.md). Isso inclui a instalação do runtime do Service Fabric, SDK, Docker, Visual Studio 2017 e criar um cluster local.

## <a name="open-an-existing-net-app"></a>Abrir um aplicativo .NET existente

Abra o aplicativo ao qual você deseja adicionar suporte à orquestração de contêineres.

Se quiser testar um exemplo, você pode usar o exemplo de código do [eShop](https://github.com/MikkelHegn/ContainersSFLab). O restante deste artigo assumirá que estamos usando esse projeto, embora você possa aplicar essas etapas ao seu próprio projeto.

Obtenha uma cópia do projeto **eShop**:

```git
git clone https://github.com/MikkelHegn/ContainersSFLab.git
```

Após fazer o download do projeto no Visual Studio 2017, abra **ContainersSFLab\eShopLegacyWebFormsSolution\eShopLegacyWebForms.sln**.

## <a name="add-container-support"></a>Adicionar suporte ao contêiner
 
Adicione suporte à orquestração de contêineres a um projeto ASP.NET ou de Console existente usando as ferramentas de Malha do Service Fabric da seguinte maneira:

No gerenciador de soluções do Visual Studio, clique com o botão direito do mouse no nome do projeto (no exemplo **eShopLegacyWebForms**) e escolha **Adicionar** > **Suporte ao Orquestrador de Contêineres**.
A caixa de diálogo **Adicionar Suporte ao Orquestrador de Contêineres** é exibida.

![Caixa de diálogo adicionar orquestrador de contêineres do Visual Studio](./media/service-fabric-mesh-howto-containerize-vs/add-container-orchestration-support.png)

Escolha **Malha do Service Fabric** na lista suspensa e, em seguida, clique em **OK**.


>[!NOTE]
> Desde 2 de novembro de 2020, [limites de taxa de download se aplicam](https://docs.docker.com/docker-hub/download-rate-limit/) a solicitações anônimas e autenticadas para o Docker Hub de contas do plano gratuito do Docker e são impostas por endereço IP. Para obter mais detalhes, confira [Autenticar-se com o Docker Hub](../container-registry/buffer-gate-public-content.md#authenticate-with-docker-hub).
>
> Para evitar a taxa limitada, certifique-se de que o padrão `FROM microsoft/aspnet:4.7.2-windowsservercore-1803 AS base` em seu Dockerfile seja substituído por `FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-1803 AS base`

Em seguida, a ferramenta verifica se o Docker está instalado, adiciona um Dockerfile ao projeto e desce uma imagem do docker para seu projeto.  
Um projeto de aplicativo da Malha do Service Fabric é adicionado à sua solução. Ele contém os arquivos de configuração e perfis de publicação da Malha. O nome do projeto é o mesmo que o nome do seu projeto, com 'Aplicativo' concatenado ao final, por exemplo, **eShopLegacyWebFormsApplication**. 

No novo projeto de Malha, você verá duas pastas das quais você deve estar ciente:
- **Recursos do aplicativo** que contém os arquivos YAML que descrevem os recursos de Malha adicionais, como a rede.
- **Recursos do serviço** que contém um arquivo service.yaml que descreve como seu aplicativo deverá ser executado quando for implantado.

Depois que o suporte à orquestração de contêineres for adicionado ao seu aplicativo, pressione **F5** para depurar seu aplicativo .NET em seu cluster da Malha local do Service Fabric. Veja o aplicativo ASP.NET do eShop em execução em um cluster da Malha do Service Fabric: 

![Aplicativo eShop](./media/service-fabric-mesh-howto-containerize-vs/eshop-running.png)

Agora você pode publicar o aplicativo na Malha do Azure Service Fabric.

## <a name="next-steps"></a>Próximas etapas

Veja como publicar um aplicativo na Malha do Service Fabric: [Tutorial - Implantar um aplicativo de Malha do Service Fabric para a Malha do Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)