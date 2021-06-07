---
title: Visão geral do Service Fabric e contêineres
description: Uma visão geral do Service Fabric e o uso de contêineres para implantar aplicativos de microsserviço. Este artigo fornece uma visão geral de como os contêineres podem ser usados e dos recursos disponíveis no Service Fabric.
ms.topic: conceptual
ms.date: 7/9/2020
ms.openlocfilehash: cd0ec7dd2247fdd791df362fa34542178c17df4d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87091650"
---
# <a name="service-fabric-and-containers"></a>Service Fabric e contêineres

## <a name="introduction"></a>Introdução

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microsserviços e contêineres escalonáveis e confiáveis.

O Service Fabric é um [orquestrador de cotêiner](service-fabric-cluster-resource-manager-introduction.md) da Microsoft que implanta microsserviços em um cluster de computadores. Benefícios do Service Fabric das lições aprendidas durante seus anos executando os serviços da Microsoft em grande escala.

Os microsserviços podem ser desenvolvidos de várias maneiras usando os [modelos de programação do Service Fabric](service-fabric-choose-framework.md), o [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), para implantar [qualquer código que você escolher](service-fabric-guest-executables-introduction.md). Se você quiser apenas [implantar e gerenciar contêineres](service-fabric-containers-overview.md) o Service Fabric também é uma ótima escolha.

Por padrão, o Service Fabric implanta e ativa esses serviços como processos. Processos fornecem a ativação mais rápida e o uso de densidade mais alto dos recursos em um cluster. O Service Fabric também pode implantar serviços em imagens de contêiner. Você também pode misturar serviços em processos e serviços em contêineres no mesmo aplicativo.

Para ir diretamente e experimentar os contêineres no Service Fabric, experimente um início rápido, tutorial ou exemplo:  

[Início Rápido: implantar um aplicativo do contêiner do Linux ao Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Início Rápido: implantar um aplicativo do contêiner do Windows ao Service Fabric](service-fabric-quickstart-containers.md)  
[Colocar um aplicativo .NET existente em um contêiner](service-fabric-host-app-in-a-container.md)  
[Exemplos de Contêiner do Service Fabric](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>O que são contêineres?

Os contêineres resolvem o problema dos aplicativos em execução com confiança em diferentes ambientes de computação, fornecendo um ambiente imutável para o aplicativo ser executado. Os contêineres encapsulam um aplicativo e todas as suas dependências, como bibliotecas e arquivos de configuração, em sua própria isolada 'caixa' que contém todos os componentes necessários para executar o software dentro do contêiner. Sempre que o contêiner é executado, o aplicativo dentro dele sempre tem tudo o que precisa para executar, como as versões corretas de suas bibliotecas dependentes, quaisquer arquivos de configuração e qualquer outra coisa, precisa ser executado.

Os contêineres são executados sobre o kernel e têm uma exibição isolada do sistema de arquivos e outros recursos. Um aplicativo em um contêiner não tem conhecimento de outros aplicativos ou processos fora do seu contêiner. Cada aplicativo e seu runtime, suas dependências e suas bibliotecas de sistema são executados dentro de um contêiner com acesso privado e completo à exibição isolada própria do contêiner do sistema operacional. Além de tornar fácil fornecer todas as dependências do seu aplicativo, precisa ser executado em diferentes ambientes de computação, segurança e isolamento de recursos são importantes benefícios do uso de contêineres com o Service Fabric – que executa outra forma de serviços em um processo.

Em comparação a máquinas virtuais, contêineres têm as seguintes vantagens:

* **Pequeno**: os contêineres usam um único espaço de armazenamento e versões e atualizações em camadas para aumentar a eficiência.
* **Rápido**: os contêineres não precisam inicializar um sistema operacional inteiro, por isso podem começar com muito mais rapidez, normalmente em questão de segundos.
* **Portabilidade**: uma imagem de aplicativo em contêiner pode ser portada para execução na nuvem, local, dentro de máquinas virtuais ou diretamente em computadores físicos.
* **Governança de recursos**: um contêiner pode limitar os recursos físicos que ele pode consumir em seu host.

### <a name="container-types-and-supported-environments"></a>Tipos de contêineres e ambientes com suporte

O Service Fabric dá suporte a contêineres no Linux e no Windows e oferece suporte ao modo de isolamento de Windows no segundo caso.

#### <a name="docker-containers-on-linux"></a>Contêineres de Docker no Linux

O docker oferece APIs para criar e gerenciar contêineres sobre contêineres de kernel do Linux. O Hub do Docker fornece um repositório central para armazenar e recuperar imagens de contêiner.
Para o tutorial baseado em Linux, consulte [Criar seu primeiro aplicativo de contêiner do Service Fabric no Linux](service-fabric-get-started-containers-linux.md).

#### <a name="windows-server-containers"></a>Contêineres do Windows Server

O Windows Server 2016 e posterior fornecem dois tipos diferentes de contêineres que diferem por nível de isolamento. Contêineres do Windows Server e contêineres do Docker são semelhantes porque ambos têm isolamento de sistema de arquivos e namespace, mas compartilham o kernel com o host no qual estão sendo executados. No Linux, esse isolamento tradicionalmente foi fornecido por cgroups e namespaces, e contêineres do Windows Server se comportam da mesma forma.

Contêineres do Windows com suporte a Hyper-V fornecem mais segurança e isolamento, porque nenhum contêiner compartilha o kernel do sistema operacional com outros contêineres ou com o host. Com esse nível mais alto de isolamento de segurança, os contêineres habilitados para Hyper-V são indicados para cenários hostis e multilocatários.
Para o tutorial baseado em Windows, consulte [Criar seu primeiro aplicativo de contêiner do Service Fabric no Windows](service-fabric-get-started-containers.md).

A figura a seguir mostra os diferentes tipos de níveis de isolamento e virtualização disponíveis.
![Plataforma Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Cenários de uso de contêineres

Estes são exemplos típicos em que um contêiner é uma boa opção:

* **Comparar e deslocar IIS**: você pode colocar um aplicativo [ASP.NET MVC](https://www.asp.net/mvc) existente em um contêiner em vez de migrar para ASP.NET Core. Esses aplicativos ASP.NET MVC dependem do IIS (Serviços de Informações da Internet). Você pode empacotar esses aplicativos em imagens de contêiner a partir da imagem do IIS pré-criada e implantá-los com o Service Fabric. Consulte [Container Images on Windows Server](/virtualization/windowscontainers/quick-start/quick-start-windows-server) (Imagens de contêiner no Windows Server) para obter informações sobre contêineres do Windows.

* **Combinar contêineres e microsserviços do Service Fabric**: use uma imagem existente do contêiner para parte do seu aplicativo. Por exemplo, é possível usar o [contêiner NGINX](https://hub.docker.com/_/nginx/) para o front-end da Web do seu aplicativo e serviços com estado para obter a computação mais intensiva de back-end.

* **Reduzir o impacto de serviços "vizinhos ruidosos"**: você pode usar a capacidade de governança de recursos de contêineres para restringir os recursos que um serviço usa em um host. Se os serviços consumirem muitos recursos e afetarem o desempenho dos outros (como uma operação demorada, parecida com uma consulta), considere colocar esses serviços em contêineres com governança de recursos.

## <a name="service-fabric-support-for-containers"></a>Suporte ao Service Fabric para contêineres

O Service Fabric dá suporte à implantação de contêineres do Docker no Linux e contêineres do Windows Server no Windows Server 2016 e posterior, juntamente com o suporte para o modo de isolamento do Hyper-V.

> [!NOTE]
> Não há suporte para contêineres no nó único local Service Fabric clusters (nem clusters do Linux em OneBox, nem clusters do Windows em instalações de Service Fabric locais).

O Service Fabric fornece um [modelo de aplicativo](service-fabric-application-model.md) em que um contêiner representa um host de aplicativo no qual várias réplicas de serviço são colocadas. Service Fabric também dá suporte a um [cenário de executável convidado](service-fabric-guest-executables-introduction.md) em que você não usa os modelos de programação do Service Fabric, mas em vez disso, empacotar um aplicativo existente, escrito usando qualquer linguagem ou estrutura, dentro de um contêiner. Esse cenário é o caso de uso comum para contêineres.

Você também pode executar [serviços do Service Fabric dentro de um contêiner](service-fabric-services-inside-containers.md). No momento, o suporte para execução de serviços do Service Fabric dentro de contêineres é limitado.

O Service Fabric fornece vários recursos de contêiner que ajudam a compilar de aplicativos compostos por microsserviços em contêineres, como:

* Implantação e ativação de imagem de contêiner.
* Governança de recursos, incluindo a configuração de valores de recursos, por padrão, nos clusters do Azure.
* Autenticação do repositório.
* Porta do contêiner para o mapeamento da porta de host.
* Descoberta e comunicação de contêiner para contêiner.
* Capacidade de configurar e definir as variáveis de ambiente.
* Capacidade de definir credenciais de segurança no contêiner.
* A opção de diferentes modos de rede para contêineres.

Para obter uma visão abrangente do contêiner de suporte no Azure, por exemplo, como criar um cluster Kubernetes com o Serviço de Kubernetes do Azure, como para criar um registro privado do Docker no Registro de Contêiner do Azure e muito mais, consulte [Azure para contêineres](../containers/index.yml).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu sobre o suporte que o Service Fabric fornece para contêineres em execução. Em seguida, apresentaremos exemplos de cada um dos recursos para mostrar como usá-los.

[Criar seu primeiro aplicativo de contêiner do Service Fabric no Linux](service-fabric-get-started-containers-linux.md)  
[Criar seu primeiro aplicativo de contêiner do Service Fabric no Windows](service-fabric-get-started-containers.md)  
[Saiba mais sobre contêineres do Windows](/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
