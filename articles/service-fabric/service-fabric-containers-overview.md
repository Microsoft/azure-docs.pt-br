---
title: "Visão Geral do Service Fabric e contêineres | Microsoft Docs"
description: "Uma visão geral do Service Fabric e o uso de contêineres para implantar aplicativos de microsserviço. Este artigo fornece uma visão geral de como os contêineres podem ser usados e dos recursos disponíveis no Service Fabric."
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: c98b3fcb-c992-4dd9-b67d-2598a9bf8aab
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/20/2017
ms.author: msfussell
ms.openlocfilehash: f47a855b94a29a2e9bbf4ca509e68612423aa65d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-fabric-and-containers"></a>Service Fabric e contêineres
> [!NOTE]
> Ainda não há suporte para a implantação de contêineres em um cluster do Service Fabric no Windows 10. 
>   

## <a name="introduction"></a>Introdução
O Azure Service Fabric é um [orquestrador](service-fabric-cluster-resource-manager-introduction.md) de serviços entre um cluster de computadores, com anos de uso e otimização em grande escala dos serviços da Microsoft. Os serviços podem ser desenvolvidos de várias maneiras, desde usando os [modelos de programação do Service Fabric ](service-fabric-choose-framework.md) até implantando [executáveis convidados](service-fabric-deploy-existing-app.md). Por padrão, o Service Fabric implanta e ativa esses serviços como processos. Processos fornecem a ativação mais rápida e o uso de densidade mais alto dos recursos em um cluster. O Service Fabric também pode implantar serviços em imagens de contêiner. É importante observar que você pode misturar serviços em processos e serviços em contêineres no mesmo aplicativo.   

## <a name="what-are-containers"></a>O que são contêineres?
Contêineres são componentes encapsulados e implantados individualmente executados como instâncias isoladas no mesmo kernel para beneficiarem-se da virtualização que um sistema operacional oferece. Assim, cada aplicativo e seu tempo de execução, suas dependências e suas bibliotecas de sistema são executados dentro de um contêiner com acesso privado e completo à exibição isolada própria do contêiner dos constructos do sistema operacional. Junto com a portabilidade, esse nível de isolamento de segurança e de recursos é o principal benefício para o uso de contêineres com o Service Fabric, que, caso contrário, executa serviços em processos.

Contêineres são uma tecnologia de virtualização que virtualiza o sistema operacional subjacente de aplicativos. Os contêineres fornecem um ambiente imutável para que aplicativos sejam executados com graus variados de isolamento. Os contêineres são executados sobre o kernel e têm uma exibição isolada do sistema de arquivos e outros recursos. Em comparação a máquinas virtuais, contêineres têm as seguintes vantagens:

* **Pequeno**: os contêineres usam um único espaço de armazenamento e versões e atualizações em camadas para aumentar a eficiência.
* **Rápido**: os contêineres não precisam inicializar um sistema operacional inteiro, por isso podem começar com muito mais rapidez, normalmente em questão de segundos.
* **Portabilidade**: uma imagem de aplicativo em contêiner pode ser portada para execução na nuvem, local, dentro de máquinas virtuais ou diretamente em computadores físicos.
* **Governança de recursos**: um contêiner pode limitar os recursos físicos que ele pode consumir em seu host.

## <a name="container-types-and-supported-environments"></a>Tipos de contêineres e ambientes com suporte
O Service Fabric dá suporte a contêineres no Linux e no Windows e também oferece suporte ao modo de isolamento de Hyper-V no segundo caso. 

> [!NOTE]
> No momento, não há suporte para a implantação de contêineres em um cluster do Service Fabric no Windows 10. 
> 

### <a name="docker-containers-on-linux"></a>Contêineres de Docker no Linux
O docker oferece APIs de alto nível para criar e gerenciar contêineres sobre contêineres de kernel do Linux. O Hub do Docker é um repositório central para armazenar e recuperar imagens de contêiner.
Para obter um tutorial, confira [Implantar um contêiner do Docker no Service Fabric](service-fabric-get-started-containers-linux.md).

### <a name="windows-server-containers"></a>Contêineres do Windows Server
O Windows Server 2016 fornece dois tipos diferentes de contêineres que diferem no nível de isolamento fornecido. Contêineres do Windows Server e contêineres do Docker são semelhantes porque ambos têm isolamento de sistema de arquivos e namespace, mas compartilham o kernel com o host no qual estão sendo executados. No Linux, esse isolamento sempre foi fornecido por `cgroups` e `namespaces`, e os contêineres do Windows Server se comportam da mesma forma.

Contêineres do Windows com suporte a Hyper-V fornecem mais segurança e isolamento, porque nenhum contêiner compartilha o kernel do sistema operacional com outros contêineres ou com o host. Com esse nível mais alto de isolamento de segurança, os contêineres habilitados para Hyper-V são indicados para cenários hostis e multilocatários.
Para obter um tutorial, confira [Implantar um contêiner do Windows no Service Fabric](service-fabric-get-started-containers.md).

A figura a seguir mostra os diferentes tipos de virtualização e níveis isolamento disponíveis no sistema operacional.
![Plataforma Service Fabric][Image1]

## <a name="scenarios-for-using-containers"></a>Cenários de uso de contêineres
Estes são exemplos típicos em que um contêiner é uma boa opção:

* **Comparar e deslocar IIS**: se você tiver aplicativos [ASP.NET MVC](https://www.asp.net/mvc) existentes que deseje continuar usando, coloque-os em um contêiner, em vez de migrá-los para ASP.NET Core. Esses aplicativos ASP.NET MVC dependem do IIS (Serviços de Informações da Internet). Você pode empacotar esses aplicativos em imagens de contêiner a partir da imagem do IIS pré-criada e implantá-los com o Service Fabric. Consulte [Container Images on Windows Server](https://docs.microsoft.com/en-us/virtualization/windowscontainers/quick-start/quick-start-windows-server) (Imagens de contêiner no Windows Server) para obter informações sobre contêineres do Windows.
* **Combinar contêineres e microsserviços do Service Fabric**: use uma imagem existente do contêiner para parte do seu aplicativo. Por exemplo, é possível usar o [contêiner NGINX](https://hub.docker.com/_/nginx/) para o front-end da Web do seu aplicativo e serviços com estado para obter a computação mais intensiva de back-end.
* **Reduzir o impacto de serviços "vizinhos ruidosos"**: você pode usar a capacidade de governança de recursos de contêineres para restringir os recursos que um serviço usa em um host. Se os serviços consumirem muitos recursos e afetarem o desempenho dos outros (como uma operação demorada, parecida com uma consulta), considere colocar esses serviços em contêineres com governança de recursos.

## <a name="service-fabric-support-for-containers"></a>Suporte ao Service Fabric para contêineres
O Service Fabric dá suporte à implantação de contêineres do Docker em contêineres do Linux e do Windows Server no Windows Server 2016, juntamente com o suporte para o modo de isolamento do Hyper-V. 

No [modelo de aplicativo](service-fabric-application-model.md)do Service Fabric, um contêiner representa um host de aplicativo no qual várias réplicas de serviço são colocadas. O Service Fabric pode executar qualquer contêiner, e o cenário é semelhante ao [cenário executável de convidado](service-fabric-deploy-existing-app.md), no qual você pode empacotar um aplicativo existente dentro de um contêiner. Este cenário é o caso de uso comum para contêineres, e os exemplos incluem a execução de um aplicativo escrito usando qualquer linguagem ou estruturas, mas não usando os modelos de programação internos do Service Fabric.

Além disso, também é possível executar os [serviços do Service Fabric dentro de contêineres](service-fabric-services-inside-containers.md). No momento, o suporte para execução de serviços do Service Fabric dentro de contêineres é limitado, mas será melhorado em versões futuras.

O Service Fabric tem vários recursos de contêiner que ajudam a compilar de aplicativos compostos por microsserviços em contêineres. O Service Fabric oferece os seguintes recursos para serviços em contêineres:

* Implantação e ativação de imagem de contêiner.
* Governança de recursos, incluindo a configuração de valores de recursos, por padrão, nos clusters do Azure.
* Autenticação do repositório.
* Porta do contêiner para o mapeamento da porta de host.
* Descoberta e comunicação de contêiner para contêiner.
* Capacidade de configurar e definir as variáveis de ambiente.
* Capacidade de definir credenciais de segurança no contêiner.
* A opção de diferentes modos de rede para contêineres.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu sobre contêineres, que o Service Fabric é um orquestrador de contêiner e que o Service Fabric tem recursos que dão suporte a contêineres. Como uma próxima etapa, apresentaremos exemplos de cada um dos recursos para mostrar como usá-los.

[Criar seu primeiro aplicativo de contêiner do Service Fabric no Windows](service-fabric-get-started-containers.md)

[Criar seu primeiro aplicativo de contêiner do Service Fabric no Linux](service-fabric-get-started-containers-linux.md)

[Saiba mais sobre contêineres do Windows](https://docs.microsoft.com/en-us/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
