---
title: Terminologia para Malha do Microsoft Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre os termos comuns para Malha do Microsoft Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: dkkapur
ms.author: dekapur
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 2d2661593ba3d9be2755d81803c8e248a2f7d0e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60810596"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologia da Malha do Service Fabric

A Malha do Microsoft Azure Service Fabric é um serviço totalmente gerenciado que permite aos desenvolvedores implantar aplicativos de microsserviços sem gerenciar máquinas virtuais, armazenamento ou rede. Este artigo detalha a terminologia usada pela Malha do Microsoft Azure Service Fabric para compreender os termos usados na documentação.

## <a name="service-fabric"></a>Service Fabric

O [Service Fabric](/azure/service-fabric/) é uma plataforma de sistemas distribuídos de software livre que facilita o empacotamento, a implantação e o gerenciamento de microsserviços escalonáveis e confiáveis. O Service Fabric é o orquestrador que fornece energia à Malha do Service Fabric. O Service Fabric fornece opções de como você pode compilar e executar os aplicativos de microsserviços. É possível usar qualquer estrutura para gravar os serviços e escolher onde executar o aplicativo a partir de várias opções de ambiente.

## <a name="application-and-service-concepts"></a>Conceitos de aplicativo e serviço

**Aplicativo de Malha do Service Fabric**: Os Aplicativos de Malha do Service Fabric são descritos pelo [Modelo de Recurso](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (arquivos de recursos YAML e JSON) e podem ser implantados em qualquer ambiente em que o Service Fabric executa.

**Aplicativo Nativo do Service Fabric**: Os Aplicativos Nativos do Service Fabric são descritos pelo [Modelo de Aplicativo Nativo](/azure/service-fabric/service-fabric-application-model) (manifestos do serviço e aplicativo baseados em XML).  Os Aplicativos Nativos do Service Fabric não podem ser executados na Malha do Service Fabric.

**Aplicativo**: Um aplicativo de Malha do Service Fabric é a unidade de implantação, controle de versão e tempo de vida de um aplicativo de Malha. O ciclo de vida de cada instância do aplicativo pode ser gerenciado independentemente.  Os aplicativos são compostos de um ou mais pacotes de códigos de serviço e configurações. Um aplicativo é definido usando o esquema do RM (Modelo de Recurso) do Azure.  Os serviços são descritos como propriedades do recurso de aplicativo em um modelo RM.  Redes e volumes usados pelo aplicativo são referenciados pelo aplicativo.  Ao criar um aplicativo, o aplicativo, os serviços, a rede e os volumes são modelados usando o Modelo de Recurso do Service Fabric.

**Serviço**: Um serviço em um aplicativo representa um microsserviço e executa uma função autônoma e completa. Cada serviço é composto de um ou mais pacotes de códigos que descrevem tudo o que é necessário para executar a imagem de contêiner associada ao pacote de códigos.  O número de réplicas de serviço em um aplicativo pode ser dimensionado para dentro e para fora.

**Pacote de códigos**: Os pacotes de códigos descrevem tudo o que é necessário para executar a imagem do contêiner associada ao pacote de códigos, incluindo o seguinte:

* Registro, a versão e o nome do contêiner
* Recursos de CPU e memória necessários para cada contêiner
* Pontos de extremidade de rede
* Volumes de montagem no contêiner, fazendo referência a um recurso de volume separado.

## <a name="deployment-and-application-models"></a>Implantação e modelos de aplicativos 

Para implantar os serviços será necessário descrever como deverão ser executados. O Service Fabric dá suporte a três diferentes modelos de implantação:

### <a name="resource-model"></a>Modelo de recursos
Os Recursos do Service Fabric são tudo o que pode ser implantado individualmente no Service Fabric, incluindo aplicativos, serviços, redes e volumes. Os recursos são definidos usando um arquivo JSON, que pode ser implantado em um ponto de extremidade do cluster.  Para a Malha do Service Fabric, é usado o esquema do Modelo de Recurso do Azure. Um esquema de arquivo YAML também pode ser usado para criar arquivos de definição com mais facilidade. Os recursos podem ser implantados em qualquer lugar em que o Service Fabric execute. O modelo de recursos é a maneira mais simples de descrever os aplicativos do Service Fabric. O foco principal é a implantação e o gerenciamento simples de serviços de contêiner. Para saber mais, leia [Introdução ao Modelo de Recurso do Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Modelo nativo
O modelo de aplicativo nativo fornece aos aplicativos acesso de nível baixo completo ao Service Fabric. Os aplicativos e serviços são definidos como tipos registrados em arquivos de manifesto XML.

O modelo nativo dá suporte à estrutura de Reliable Services que fornece acesso às APIs de tempo de execução do Service Fabric e APIs de gerenciamento de cluster em C# e Java. O modelo nativo também dá suporte a executáveis e contêineres arbitrários.

O modelo nativo não tem suporte no ambiente de Malha do Service Fabric.  Para obter mais informações, consulte [visão geral do modelo de programação](/azure/service-fabric/service-fabric-choose-framework).

### <a name="docker-compose"></a>Docker Compose 
O [Docker Compose](https://docs.docker.com/compose/) é parte do projeto Docker. O Service Fabric dá suporte limitado para implantar aplicativos usando o modelo do Docker Compose.

## <a name="environments"></a>Ambientes

O Service Fabric é uma tecnologia de plataforma de software livre em que vários serviços e produtos diferentes são baseados. A Microsoft fornece as seguintes opções:

 - **Malha do Service Fabric**: Um serviço totalmente gerenciado para executar aplicativos do Service Fabric no Microsoft Azure.
 - **Microsoft Azure Service Fabric**: A oferta de cluster do Service Fabric hospedada no Azure. Fornece integração entre o Service Fabric e a infraestrutura do Azure, juntamente com gerenciamento de configuração e upgrade de clusters do Service Fabric.
 - **Service Fabric autônomo**: Um conjunto de ferramentas de instalação e configuração para [implantar clusters do Service Fabric em qualquer local](/azure/service-fabric/service-fabric-deploy-anywhere) (no local ou em qualquer provedor de nuvem). Não gerenciado pelo Azure.
 - **Cluster de desenvolvimento do Service Fabric**: Fornece uma experiência de desenvolvimento local no Windows, Linux ou Mac para o desenvolvimento de aplicativos do Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matriz de suporte do modelo de implantação, estrutura e ambiente
Diferentes ambientes possuem diferentes níveis de suporte para modelos de implantação e estrutura. A tabela a seguir descreve as combinações de modelo de implantação e estrutura com suporte.

| Tipo de aplicativo | Descrito por | Malha do Microsoft Azure Service Fabric | Clusters do Microsoft Azure Service Fabric (qualquer sistema operacional)| Cluster local | Cluster autônomo |
|---|---|---|---|---|---|
| Aplicativos de Malha do Service Fabric | Modelo de recurso (YAML & JSON) | Com suporte |Sem suporte | Windows- com suporte, Linux e Mac - sem suporte | Windows- sem suporte |
|Aplicativos nativos do Service Fabric | Modelo de aplicativo nativo (XML) | Sem suporte| Com suporte|Com suporte|Windows- sem suporte|

A tabela a seguir descreve os diferentes modelos de aplicativos e as ferramentas existentes no Service Fabric.

| Tipo de aplicativo | Descrito por | Visual Studio | Eclipse | SFCTL | AZ CLI | Powershell|
|---|---|---|---|---|---|---|
| Aplicativos de Malha do Service Fabric | Modelo de recurso (YAML & JSON) | VS 2017 |Sem suporte |Sem suporte | Com suporte - apenas ambiente de Malha | Sem suporte|
|Aplicativos nativos do Service Fabric | Modelo de aplicativo nativo (XML) | VS 2017 e VS 2015| Com suporte|Com suporte|Com suporte|Com suporte|

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Malha do Service Fabric, leia [visão geral sobre Malha do Service Fabric](service-fabric-mesh-overview.md).

Encontre respostas para [dúvidas comuns](service-fabric-mesh-faq.md).
