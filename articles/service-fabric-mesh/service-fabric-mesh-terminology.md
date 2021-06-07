---
title: Terminologia da malha de Service Fabric do Azure
description: Este artigo detalha a terminologia usada pela malha de Service Fabric do Azure para ajudá-lo a entender melhor os termos usados na documentação.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 1fda83ec00bed0f0c67d45e1e1af61baa5de5e78
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626749"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologia da Malha do Service Fabric

> [!IMPORTANT]
> A versão prévia da Malha do Azure Service Fabric foi desativada. Não serão mais permitidas novas implantações por meio da API da Malha do Service Fabric. O suporte para as implantações existentes continuará até 28 de abril de 2021.
> 
> Para obter detalhes, confira [Desativação da versão prévia da Malha do Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

A Malha do Microsoft Azure Service Fabric é um serviço totalmente gerenciado que permite aos desenvolvedores implantar aplicativos de microsserviços sem gerenciar máquinas virtuais, armazenamento ou rede. Este artigo detalha a terminologia usada pela malha de Service Fabric do Azure para ajudá-lo a entender melhor os termos usados na documentação.

## <a name="service-fabric"></a>Service Fabric

O [Service Fabric](../service-fabric/index.yml) é uma plataforma de sistemas distribuídos de software livre que facilita o empacotamento, a implantação e o gerenciamento de microsserviços escalonáveis e confiáveis. O Service Fabric é o orquestrador que fornece energia à Malha do Service Fabric. O Service Fabric fornece opções de como você pode compilar e executar os aplicativos de microsserviços. É possível usar qualquer estrutura para gravar os serviços e escolher onde executar o aplicativo a partir de várias opções de ambiente.

## <a name="application-and-service-concepts"></a>Conceitos de aplicativo e serviço

**Aplicativo de malha do Service Fabric**: aplicativos de malha do Service Fabric são descritos pela [Modelo de Recurso](./service-fabric-mesh-service-fabric-resources.md) (arquivos de recurso YAML e JSON) e pode ser implantado em qualquer ambiente em que o Service Fabric é executado.

**Aplicativo nativo do Service Fabric**: aplicativos nativos do Service Fabric são descritos pelo [modelo de aplicativo nativo](../service-fabric/service-fabric-application-model.md) (aplicativo baseado em XML e manifestos de serviço).  Os Aplicativos Nativos do Service Fabric não podem ser executados na Malha do Service Fabric.

**Aplicativo**: aplicativo de malha Microsoft Azure Service Fabric é a unidade de implantação, controle de versão e o tempo de vida de um aplicativo de malha. O ciclo de vida de cada instância do aplicativo pode ser gerenciado independentemente.  Os aplicativos são compostos de um ou mais pacotes de códigos de serviço e configurações. Um aplicativo é definido usando o esquema do RM (Modelo de Recurso) do Azure.  Os serviços são descritos como propriedades do recurso de aplicativo em um modelo RM.  Redes e volumes usados pelo aplicativo são referenciados pelo aplicativo.  Ao criar um aplicativo, o aplicativo, os serviços, a rede e os volumes são modelados usando o Modelo de Recurso do Service Fabric.

**Serviço**: um serviço em um aplicativo representa um microsserviço e executa uma função completa e independente. Cada serviço é composto de um ou mais pacotes de códigos que descrevem tudo o que é necessário para executar a imagem de contêiner associada ao pacote de códigos.  O número de réplicas de serviço em um aplicativo pode ser dimensionado para dentro e para fora.

**Pacote de códigos**: os pacotes de códigos descrevem tudo o que é necessário para executar a imagem de contêiner associada ao pacote de códigos, incluindo o seguinte:

* Registro, a versão e o nome do contêiner
* Recursos de CPU e memória necessários para cada contêiner
* Pontos de extremidade de rede
* Volumes de montagem no contêiner, fazendo referência a um recurso de volume separado.

## <a name="deployment-and-application-models"></a>Implantação e modelos de aplicativos 

Para implantar os serviços será necessário descrever como deverão ser executados. O Service Fabric dá suporte a três diferentes modelos de implantação:

### <a name="resource-model"></a>Modelo de recursos
Os Recursos do Service Fabric são tudo o que pode ser implantado individualmente no Service Fabric, incluindo aplicativos, serviços, redes e volumes. Os recursos são definidos usando um arquivo JSON, que pode ser implantado em um ponto de extremidade do cluster.  Para a Malha do Service Fabric, é usado o esquema do Modelo de Recurso do Azure. Um esquema de arquivo YAML também pode ser usado para criar arquivos de definição com mais facilidade. Os recursos podem ser implantados em qualquer lugar em que o Service Fabric execute. O modelo de recursos é a maneira mais simples de descrever os aplicativos do Service Fabric. O foco principal é a implantação e o gerenciamento simples de serviços de contêiner. Para saber mais, leia [Introdução ao Modelo de Recurso do Service Fabric](./service-fabric-mesh-service-fabric-resources.md).

### <a name="native-model"></a>Modelo nativo
O modelo de aplicativo nativo fornece aos aplicativos acesso de nível baixo completo ao Service Fabric. Os aplicativos e serviços são definidos como tipos registrados em arquivos de manifesto XML.

O modelo nativo dá suporte à estrutura de Reliable Services que fornece acesso às APIs de runtime do Service Fabric e APIs de gerenciamento de cluster em C# e Java. O modelo nativo também dá suporte a executáveis e contêineres arbitrários.

O modelo nativo não tem suporte no ambiente de Malha do Service Fabric.  Para obter mais informações, consulte [visão geral do modelo de programação](../service-fabric/service-fabric-choose-framework.md).

### <a name="docker-compose"></a>Docker Compose 
O [Docker Compose](https://docs.docker.com/compose/) é parte do projeto Docker. O Service Fabric dá suporte limitado para implantar aplicativos usando o modelo do Docker Compose.

## <a name="environments"></a>Ambientes

O Service Fabric é uma tecnologia de plataforma de software livre em que vários serviços e produtos diferentes são baseados. A Microsoft fornece as seguintes opções:

 - **Malha do Service Fabric**: um serviço totalmente gerenciado para executar aplicativos do Service Fabric no Microsoft Azure.
 - **Microsoft Azure Service Fabric**: a oferta de cluster do Service Fabric hospedada no Azure. Fornece integração entre o Service Fabric e a infraestrutura do Azure, juntamente com gerenciamento de configuração e upgrade de clusters do Service Fabric.
 - **Service Fabric independente**: um conjunto de ferramentas de configuração e instalação para [implantar clusters do Service Fabric em qualquer lugar](../service-fabric/service-fabric-deploy-anywhere.md) (no local ou em qualquer provedor de nuvem). Não gerenciado pelo Azure.
 - **Cluster de desenvolvimento do Service Fabric**: fornece uma experiência de desenvolvimento local no Windows, Linux ou Mac para o desenvolvimento de aplicativos do Service Fabric.

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
