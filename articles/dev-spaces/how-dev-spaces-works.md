---
title: Como o Azure Dev Spaces funciona
services: azure-dev-spaces
ms.date: 06/02/2020
ms.topic: conceptual
description: Descreve os processos que o Power Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres
ms.openlocfilehash: 8de2c27ce03c871e60b6437656ad630fc8de8408
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91963696"
---
# <a name="how-azure-dev-spaces-works"></a>Como o Azure Dev Spaces funciona

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

O desenvolvimento de um aplicativo kubernetes pode ser desafiador. Você precisa de arquivos de configuração do Docker e do kubernetes. Você precisa descobrir como testar seu aplicativo localmente e interagir com outros serviços dependentes. Talvez seja necessário lidar com o desenvolvimento e o teste de vários serviços de uma só vez e com uma equipe de desenvolvedores.

Azure Dev Spaces fornece várias maneiras de iterar e depurar rapidamente aplicativos kubernetes e colaborar com sua equipe. Este artigo descreve o que Azure Dev Spaces pode fazer e como ele funciona.

## <a name="rapidly-iterate-and-debug-your-kubernetes-application"></a>Iterar e depurar rapidamente seu aplicativo kubernetes

Azure Dev Spaces reduz o esforço para desenvolver, testar e iterar seu aplicativo kubernetes no contexto do cluster AKS. Essa redução no esforço permite que os desenvolvedores se concentrem na lógica de negócios de seus aplicativos e não Configurando seus serviços para serem executados no kubernetes.

### <a name="bridge-to-kubernetes"></a>Ponte para Kubernetes

Com o Bridge para kubernetes, você pode conectar seu computador de desenvolvimento ao cluster kubernetes, permitindo que você execute e depure o código no seu computador de desenvolvimento como se ele estivesse em execução no cluster. A ponte para o kubernetes redireciona o tráfego entre o cluster conectado executando um pod no cluster que atua como um agente remoto para redirecionar o tráfego entre o computador de desenvolvimento e o cluster. Esse redirecionamento de tráfego permite que o código em seu computador de desenvolvimento e serviços em execução no cluster se comuniquem como se estivessem no mesmo cluster. Para obter mais informações sobre como conectar seu computador de desenvolvimento a um cluster kubernetes, consulte [como a ponte para o kubernetes funciona][how-it-works-bridge-to-kubernetes].

### <a name="run-your-code-in-aks"></a>Execute seu código no AKS

Além de redirecionar o tráfego entre o computador de desenvolvimento e o cluster AKS, com Azure Dev Spaces você pode configurar e executar rapidamente seu código diretamente no AKS. Com o Visual Studio, Visual Studio Code, ou a CLI do Azure Dev Spaces, os espaços de desenvolvimento do Azure carregarão seu código no cluster, criarão e executarão o mesmo. Os espaços de desenvolvimento do Azure também podem sincronizar as alterações de código de forma inteligente e reiniciar o serviço para refletir as alterações conforme necessário. Durante a execução do código, os logs de compilação e os rastreamentos HTTP são transmitidos de volta ao cliente para que você possa monitorar o progresso e diagnosticar quaisquer problemas. Você também pode usar Azure Dev Spaces, para anexar o depurador no Visual Studio e Visual Studio Code aos serviços Java, Node.js e .NET Core. Para obter mais informações, consulte [como a preparação de um projeto para Azure dev Spaces funciona][how-it-works-prep], [como executar seu código com o Azure dev Spaces funciona][how-it-works-up]e [como a depuração remota de seu código com o Azure dev Spaces funciona][how-it-works-remote-debugging].

## <a name="team-development"></a>Desenvolvimento da equipe

Azure Dev Spaces ajuda as equipes a trabalhar de forma produtiva em seus aplicativos no mesmo cluster AKS sem interrupções.

### <a name="intelligent-routing-between-dev-spaces"></a>Roteamento inteligente entre espaços de desenvolvimento

Com o Azure Dev Spaces, uma equipe pode compartilhar um único cluster AKS executando um aplicativo nativo de nuvem e criar espaços de desenvolvimento isolados em que a equipe pode desenvolver, testar e depurar sem interferir nos outros espaços de desenvolvimento. Uma versão de linha de base do aplicativo é executada em um espaço de desenvolvimento raiz. Os membros da equipe criam espaços de desenvolvimento secundário independentes com base no espaço raiz para desenvolvimento, teste e alterações de depuração no aplicativo. Por meio dos recursos de roteamento em espaços de desenvolvimento, os espaços de desenvolvimento filho podem rotear solicitações entre os serviços em execução no espaço de desenvolvimento filho e o espaço de desenvolvimento pai. Esse roteamento permite que os desenvolvedores executem sua própria versão de um serviço ao reutilizar serviços dependentes do espaço pai. Cada espaço filho tem sua própria URL exclusiva, que pode ser compartilhada e acessada por outras pessoas para colaboração. Para obter mais informações sobre como o roteamento funciona no Azure Dev Spaces, consulte [como o roteamento funciona com Azure dev Spaces][how-it-works-routing].

### <a name="live-testing-an-open-pull-request"></a>Teste ao vivo uma solicitação de pull aberto

Você também pode usar ações do GitHub com Azure Dev Spaces para testar alterações em seu aplicativo em uma solicitação pull diretamente no cluster antes de Mesclar. Azure Dev Spaces pode implantar automaticamente uma versão de revisão do aplicativo em seu cluster, permitindo que o autor e outros membros da equipe examinem as alterações no contexto de todo o aplicativo. Usando os recursos de roteamento do Azure Dev Spaces, essa versão de revisão do aplicativo também é implantada no cluster sem afetar outros espaços de desenvolvimento. Todos esses recursos permitem que você aprove e mescle com segurança as solicitações pull. Para ver um exemplo de ações e Azure Dev Spaces do GitHub, consulte [ações do github & serviço kubernetes do Azure][pr-flow].

## <a name="next-steps"></a>Próximas etapas

Para começar a conectar seu computador de desenvolvimento local ao cluster AKS, consulte [conectar seu computador de desenvolvimento a um cluster AKs][connect].


[connect]: https://code.visualstudio.com/docs/containers/bridge-to-kubernetes
[how-it-works-bridge-to-kubernetes]: /visualstudio/containers/overview-bridge-to-kubernetes
[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[pr-flow]: how-to/github-actions.md
[routing]: #team-development