---
title: Próximas etapas de criação do projeto Service Fabric
description: Saiba mais sobre o projeto de aplicativo que você acabou de criar no Visual Studio.  Saiba como criar serviços usando tutoriais e mais sobre como desenvolver serviços para o Service Fabric.
ms.topic: conceptual
ms.date: 12/21/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4d162918644727d4c79ad606f1ed34816f543d81
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044372"
---
# <a name="your-service-fabric-application-and-next-steps"></a>O seu aplicativo do Service Fabric e as próximas etapas
O seu aplicativo do Service Fabric do Azure foi criado. Este artigo inclui vários recursos, algumas informações sobre as quais você pode estar interessado e [as próximas etapas](#next-steps)possíveis.

Novos usuários podem encontrar [tutoriais, passo a passos e exemplos](#get-started-with-tutorials-walk-throughs-and-samples) úteis. Ele também pode ser útil para examinar a [estrutura do projeto de aplicativo criado](#the-application-project). Também estão incluídas descrições dos modelos de [programação](#learn-more-about-the-programming-models)de Service Fabric [, comunicação de serviço](#learn-about-service-communication), segurança de [aplicativo](#learn-about-configuring-application-security)e ciclo de [vida do aplicativo](#learn-about-the-application-lifecycle).

Usuários mais experientes podem encontrar a seção de [práticas recomendadas](#learn-about-best-practices) Service Fabric úteis para aprender a aproveitar a plataforma e os aplicativos de estrutura com máxima eficácia.

Para aqueles com perguntas ou comentários, ou quem está procurando relatar um problema, consulte a [seção correspondente](#have-questions-or-feedback--need-to-report-an-issue).

## <a name="get-started-with-tutorials-walk-throughs-and-samples"></a>Início com tutoriais, passo a passo e exemplos
Pronto para começar?  

Percorra o tutorial do aplicativo .NET. Saiba como [compilar um aplicativo](service-fabric-tutorial-create-dotnet-app.md) com um front-end do ASP.NET Core e um back-end com estado, [implantar o aplicativo](service-fabric-tutorial-deploy-app-to-party-cluster.md) em um cluster, [configurar CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) e [configurar monitoramento e diagnóstico](service-fabric-tutorial-monitoring-aspnet.md).

Ou tente um dos seguintes a passo a passo e crie o primeiro...
- [Serviço C# Reliable Services no Windows](service-fabric-reliable-services-quick-start.md) 
- [Serviço C# Reliable Actors no Windows](service-fabric-reliable-actors-get-started.md) 
- [Serviço executável de convidado no Windows](quickstart-guest-app.md) 
- [Aplicativo de contêiner do Windows](service-fabric-get-started-containers.md) 

Você também pode ter interesse em experimentar os [aplicativos de exemplo](/samples/browse/?products=azure).

## <a name="the-application-project"></a>O projeto de aplicativo
Cada novo aplicativo inclui um projeto de aplicativo. Pode haver um ou dois projetos adicionais dependendo do tipo de serviço escolhido.

O projeto de aplicativo consiste em:

* Um conjunto de referências para os serviços que compõem o aplicativo.
* Três perfis de publicação, (Local de 1 Nó, Local de 5 Nós e Nuvem) que você pode usar para atualizar as preferências para trabalhar com ambientes diferentes, como preferências relacionadas a um ponto de extremidade do cluster e se realizará ou não implantações de atualização por padrão.
* Três arquivos de parâmetro de aplicativos (os mesmos que acima) que você pode usar para atualizar as configurações do aplicativo específicas do ambiente, como o número de partições a criar para um serviço. Saiba como [configurar o aplicativo para vários ambientes](service-fabric-manage-multiple-environment-app-configuration.md).
* Um script de implantação que você pode usar para implantar o aplicativo na linha de comando ou como parte de uma pipeline de implantação e integração contínua automatizada. Saiba mais sobre como [implantar aplicativos usando o PowerShell](service-fabric-deploy-remove-applications.md).
* O manifesto do aplicativo, que o descreve. Você pode encontrar o manifesto na pasta ApplicationPackageRoot. Saiba mais sobre [manifestos de aplicativo e serviço](service-fabric-application-model.md).

## <a name="learn-more-about-the-programming-models"></a>Saiba mais sobre os modelos de programação
O Service Fabric oferece várias maneiras de escrever e gerenciar seus serviços.  Aqui estão a visão geral e as informações conceituais sobre [serviços confiáveis com e sem estado](service-fabric-reliable-services-introduction.md), [Reliable Actors](service-fabric-reliable-actors-introduction.md), [contêineres](service-fabric-containers-overview.md), [executáveis de convidado ](service-fabric-guest-executables-introduction.md) e [serviços do ASP.NET Core com e sem estado](service-fabric-reliable-services-communication-aspnetcore.md).

## <a name="learn-about-service-communication"></a>Saiba mais sobre a comunicação de serviço
Um aplicativo do Service Fabric é composto de vários serviços diferentes, em que cada serviço realiza uma tarefa especializada. Esses serviços podem se comunicar entre si e talvez haja aplicativos cliente fora do cluster que se conectam e se comunicam com os serviços. Saiba como [configurar a comunicação com e entre os serviços](service-fabric-connect-and-communicate-with-services.md) no Service Fabric. 

## <a name="learn-about-configuring-application-security"></a>Saiba mais sobre como configurar a segurança do aplicativo
Você pode usar aplicativos seguros em execução no cluster em contas de usuário diferentes. O Service Fabric também protege os recursos usados pelos aplicativos no momento da implantação nas contas de usuário, por exemplo, arquivos, diretórios e certificados. Isso torna os aplicativos em execução, mesmo em um ambiente hospedado compartilhado, mais protegidos uns dos outros.  Saiba como [configurar políticas de segurança para o aplicativo](service-fabric-application-runas-security.md).

O aplicativo pode conter informações confidenciais, como cadeias de conexão de armazenamento, senhas ou outros valores que não devem ser tratados em texto sem formatação. Saiba como [gerenciar segredos no aplicativo](service-fabric-application-secret-management.md).

## <a name="learn-about-the-application-lifecycle"></a>Saiba mais sobre o ciclo de vida do aplicativo.
Assim como acontece com outras plataformas, um aplicativo do Service Fabric geralmente passa pelas seguintes fases: design, desenvolvimento, teste, implantação, upgrade, manutenção e remoção. [Este artigo](service-fabric-application-lifecycle.md) fornece uma visão geral das APIs e como elas são usadas pelas diferentes funções em todas as fases do ciclo de vida de Service Fabric aplicativo.

## <a name="learn-about-best-practices"></a>Saiba mais sobre as práticas recomendadas
Service Fabric tem vários artigos que descrevem [as práticas recomendadas](./service-fabric-best-practices-security.md). Aproveite essas informações para ajudar a garantir que o cluster e o aplicativo sejam executados assim como possível.
Os tópicos abordados incluem:
* [Segurança](./service-fabric-best-practices-security.md)
* [Rede](./service-fabric-best-practices-networking.md)
* [Planejamento e escala de computação](./service-fabric-best-practices-capacity-scaling.md)
* [Infraestrutura como código](./service-fabric-best-practices-infrastructure-as-code.md)
* [Monitoramento e diagnósticos](./service-fabric-best-practices-monitoring.md)
* [Design do aplicativo](./service-fabric-best-practices-applications.md)

Também está incluída uma [lista de verificação de preparação de produção](./service-fabric-production-readiness-checklist.md) que integra todas as informações de práticas recomendadas em um formato fácil de consumir.

## <a name="have-questions-or-feedback--need-to-report-an-issue"></a>Dúvidas ou comentários?  Precisa relatar um problema?
Leia [perguntas comuns](service-fabric-common-questions.md) e encontre respostas sobre o que o Service Fabric pode fazer e como ele deve ser usado.

Os [guias de solução de problemas](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides) podem ser úteis para ajudar a diagnosticar e resolver problemas comuns em clusters Service Fabric.

[Opções de suporte](service-fabric-support.md) lista fóruns no StackOverflow e no MSDN para fazer perguntas, bem como opções para relatar problemas, obter suporte e enviar comentários sobre o produto.


## <a name="next-steps"></a>Próximas etapas
- [Crie um cluster do Windows no Azure](service-fabric-tutorial-create-vnet-and-windows-cluster.md).
- Visualize o cluster, inclusive aplicativos implantados e layout físico, com [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
- [Versão e atualização de serviços](service-fabric-application-upgrade-tutorial.md)