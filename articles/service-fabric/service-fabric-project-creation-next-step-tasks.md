---
title: "Próximas etapas da criação do projeto no Service Fabric | Microsoft Docs"
description: "Este artigo contém links para um conjunto de tarefas básicas de desenvolvimento para o Service Fabric"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 299d1f97-1ca9-440d-9f81-d1d0dd2bf4df
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/28/2017
ms.author: rwike77
ms.openlocfilehash: 4523c63493bc9cb3f96713c4abbc1dd1da9130d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="your-service-fabric-application-and-next-steps"></a>O seu aplicativo do Service Fabric e as próximas etapas
O seu aplicativo do Service Fabric do Azure foi criado. Este artigo descreve a composição do seu projeto e algumas das próximas etapas possíveis.

## <a name="your-application"></a>Seu aplicativo
Cada novo aplicativo inclui um projeto de aplicativo. Pode haver um ou dois projetos adicionais dependendo do tipo de serviço escolhido.

### <a name="the-application-project"></a>O projeto de aplicativo
O projeto de aplicativo consiste em:

* Um conjunto de referências para os serviços que compõem o aplicativo.
* Três perfis de publicação, (Local de 1 Nó, Local de 5 Nós e Nuvem) que você pode usar para atualizar as preferências para trabalhar com ambientes diferentes, como preferências relacionadas a um ponto de extremidade do cluster e se realizará ou não implantações de atualização por padrão.
* Três arquivos de parâmetro de aplicativos (os mesmos que acima) que você pode usar para atualizar as configurações do aplicativo específicas do ambiente, como o número de partições a criar para um serviço.
* Um script de implantação que você pode usar para implantar o aplicativo na linha de comando ou como parte de uma pipeline de implantação e integração contínua automatizada.
* O manifesto do aplicativo, que o descreve. Você pode encontrar o manifesto na pasta ApplicationPackageRoot.

### <a name="stateless-service"></a>Serviço sem estado
Ao adicionar um novo serviço sem estado, o Visual Studio adiciona à sua solução um projeto de serviço que inclua um tipo descendente de `StatelessService`. O serviço incrementa uma variável local em um contador.

### <a name="stateful-service"></a>Serviço com estado
Ao adicionar um novo serviço com estado, o Visual Studio adiciona à sua solução um projeto de serviço que inclua um tipo descendente de `StatefulService`. O serviço incrementa um contador em seu método `RunAsync` e armazena o resultado em um `ReliableDictionary`.

### <a name="actor-service"></a>Serviço de ator
Ao adicionar um novo reliable actor, o Visual Studio adiciona dois projetos à sua solução: um projeto de ator e um projeto de interface.

O projeto de ator fornece métodos para a configuração e a obtenção do valor de um contador mantido de forma confiável no estado do ator. O projeto de interface fornece uma interface que outros serviços podem usar para invocar o ator.

### <a name="stateless-web-api"></a>API Web sem monitoração de estado
O projeto de API da Web sem monitoração de estado fornece um serviço Web básico que você pode usar para abrir o aplicativo para clientes externos. Para ter mais informações sobre como o projeto é estruturado, consulte [Serviços da API Web do Service Fabric com a auto-hospedagem OWIN](service-fabric-reliable-services-communication-webapi.md).


### <a name="aspnet-core"></a>Núcleo do ASP.NET
O SDK do Service Fabric fornece o mesmo conjunto de modelos do ASP.NET Core que está disponível para projetos do ASP.NET Core autônomos: vazio, [API Web][aspnet-webapi] e [Aplicativo Web][aspnet-webapp].

### <a name="guest-executables-and-guest-containers"></a>Executáveis de convidado e contêineres de convidado

Um 'convidado' do Service Fabric é um serviço que não é compilado com os modelos de programação da plataforma. Você pode empacotar os binários de um convidado [diretamente no pacote de aplicativos](service-fabric-deploy-existing-app.md) ou então [por meio de uma imagem de contêiner](service-fabric-deploy-container.md). Em ambos os casos, o Visual Studio cria os artefatos necessários na pasta **ApplicationPackageRoot** do projeto do aplicativo. O Visual Studio não criará um novo projeto de serviço porque o código já existe em outro lugar. Se você deseja gerenciar seus projetos de convidado junto com o projeto de aplicativo do Service Fabric, você pode adicioná-los à mesma solução do Visual Studio.

## <a name="next-steps"></a>Próximas etapas
### <a name="create-an-azure-cluster"></a>Criar um cluster do Azure
O SDK do Service Fabric fornece um cluster local para desenvolvimento e teste. Para criar um cluster no Azure, veja [Configurar um cluster do Service Fabric do Portal do Azure][create-cluster-in-portal].

### <a name="publish-your-application-to-azure"></a>Publicar seu aplicativo no Azure
Você pode publicar o seu aplicativo diretamente do Visual Studio para um cluster do Azure. Para saber como, consulte [Publicar seu aplicativo no Azure][publish-app-to-azure].

### <a name="use-service-fabric-explorer-to-visualize-your-cluster"></a>Usar o Explorador do Service Fabric para visualizar o seu cluster
O Explorador do Service Fabric oferece uma maneira fácil de visualizar o seu cluster, incluindo aplicativos implantados e layout físico. Para saber mais, veja [Visualizar o cluster com o Service Fabric Explorer][visualize-with-sfx].

### <a name="version-and-upgrade-your-services"></a>Versão e atualização de serviços
O Service Fabric permite controle de versão independente e atualização de serviços independentes em um aplicativo. Para saber mais, consulte [Controlar a versão e atualizar seus serviços][app-upgrade-tutorial].

### <a name="configure-continuous-integration-with-visual-studio-team-services"></a>Configurar a integração contínua com o Visual Studio Team Services
Para saber como configurar um processo de integração contínua para seu aplicativo do Service Fabric, confira [Configurar a integração contínua com o Visual Studio Team Services][ci-with-vso].

<!-- Links -->
[add-web-frontend]: service-fabric-add-a-web-frontend.md
[create-cluster-in-portal]: service-fabric-cluster-creation-via-portal.md
[publish-app-to-azure]: service-fabric-publish-app-remote-cluster.md
[visualize-with-sfx]: service-fabric-visualizing-your-cluster.md
[ci-with-vso]: service-fabric-set-up-continuous-integration.md
[reliable-services-webapi]: service-fabric-reliable-services-communication-webapi.md
[app-upgrade-tutorial]: service-fabric-application-upgrade-tutorial.md
[aspnet-webapi]: https://docs.asp.net/en/latest/tutorials/first-web-api.html
[aspnet-webapp]: https://docs.asp.net/en/latest/tutorials/first-mvc-app/index.html
