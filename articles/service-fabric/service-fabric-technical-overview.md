---
title: Aprenda a terminologia do Azure Service Fabric | Microsoft Docs
description: Uma visão geral da terminologia do Service Fabric. Aborda os principais conceitos de terminologia e os termos usados no restante da documentação.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: chackdan;subramar
ms.assetid: 3a970679-e19e-43b3-9be8-71773f307c57
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/17/2018
ms.author: masnider
ms.openlocfilehash: 085d5e560eec090ab76c263f8f93140786f2d734
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60543199"
---
# <a name="service-fabric-terminology-overview"></a>Visão geral da terminologia do Service Fabric
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microsserviços escalonáveis e confiáveis.  É possível [hospedar clusters de Service Fabric em qualquer lugar](service-fabric-deploy-anywhere.md): Azure, em um datacenter local ou em qualquer provedor de nuvem.  O Service Fabric é o orquestrador que fornece energia à [Malha do Azure Service Fabric](/azure/service-fabric-mesh). É possível usar qualquer estrutura para gravar os serviços e escolher onde executar o aplicativo a partir de várias opções de ambiente. Este artigo apresenta detalhes da terminologia usada pelo Service Fabric para compreensão dos termos usados na documentação.

## <a name="infrastructure-concepts"></a>Conceitos de infraestrutura
**Cluster**: um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados.  Os clusters podem ser dimensionados para milhares de máquinas.

**Nó**: um computador ou VM que faz parte de um cluster é chamado de *nó*. Cada nó recebe um nome de nó (uma cadeia de caracteres). Os nós têm características, como propriedades de posicionamento. Cada computador ou VM tem um serviço Windows de início automático, `FabricHost.exe` que começa a ser executado na inicialização e então inicia dois executáveis: `Fabric.exe` e `FabricGateway.exe`. Esses dois executáveis compõem o nó. Para cenários de teste, você pode hospedar vários nós em um único computador ou VM, executando várias instâncias do `Fabric.exe` e do `FabricGateway.exe`.

## <a name="application-and-service-concepts"></a>Conceitos de aplicativo e serviço

**Aplicativo de Malha do Service Fabric**: Os Aplicativos de Malha do Service Fabric são descritos pelo Modelo de Recurso (arquivos de recursos YAML e JSON) e podem ser implantados em qualquer ambiente em que o Service Fabric seja executado.

**Aplicativo Nativo do Service Fabric**: Os Aplicativos Nativos do Service Fabric são descritos pelo Modelo de Aplicativo Nativo (manifestos do serviço e aplicativo baseados em XML).  Os Aplicativos Nativos do Service Fabric não podem ser executados na Malha do Service Fabric.

### <a name="service-fabric-mesh-application-concepts"></a>Conceitos de Aplicativo de Malha do Service Fabric

**Aplicativo**: um aplicativo é a unidade de implantação, controle de versão e tempo de vida de um aplicativo de Malha. O ciclo de vida de cada instância do aplicativo pode ser gerenciado independentemente.  Os aplicativos são compostos de um ou mais pacotes de códigos de serviço e configurações. Um aplicativo é definido usando o esquema do RM (Modelo de Recurso) do Azure.  Os serviços são descritos como propriedades do recurso de aplicativo em um modelo RM.  Redes e volumes usados pelo aplicativo são referenciados pelo aplicativo.  Ao criar um aplicativo, o aplicativo, os serviços, a rede e os volumes são modelados usando o Modelo de Recurso do Service Fabric.

**Serviço**: Um serviço em um aplicativo representa um microsserviço e executa uma função autônoma e completa. Cada serviço é composto de um ou mais pacotes de códigos que descrevem tudo o que é necessário para executar a imagem de contêiner associada ao pacote de códigos.  O número de serviços em um aplicativo pode ser escalado e reduzido verticalmente.

**Rede**: um recurso de rede cria uma rede privada para os aplicativos e é independente dos aplicativos ou serviços que podem fazer referência à rede. Vários serviços de diferentes aplicativos podem fazer parte da mesma rede. Redes são recursos implantáveis que são referenciados por aplicativos.

**Pacote de códigos**: Os pacotes de códigos descrevem tudo o que é necessário para executar a imagem do contêiner associada ao pacote de códigos, incluindo o seguinte:

* Registro, a versão e o nome do contêiner
* Recursos de CPU e memória necessários para cada contêiner
* Pontos de extremidade de rede
* Volumes de montagem no contêiner, fazendo referência a um recurso de volume separado.

Todos os pacotes de códigos definidos como parte de um recurso de aplicativo são implantados e ativados juntos como um grupo.

**Volume**: Os volumes são diretórios que são montados dentro de suas instâncias de contêiner que você pode usar para manter o estado. O driver de volume dos Arquivos do Azure monta um compartilhamento de Arquivos do Azure em um contêiner e fornece armazenamento de dados confiável por meio de qualquer API que dê suporte ao armazenamento de arquivos. Volumes são recursos implantáveis que são referenciados por aplicativos.

### <a name="service-fabric-native-application-concepts"></a>Conceitos do Aplicativo Nativo do Service Fabric

**Aplicativo**: Um aplicativo é uma coleção de serviços membros que executam determinadas funções. O ciclo de vida de cada instância do aplicativo pode ser gerenciado independentemente.

**Serviço**: Um serviço executa uma função completa e autônoma e pode iniciar e executar independentemente de outros serviços. Um serviço é composto de código, configuração e dados. Para cada serviço, o código consiste em binários executáveis, a configuração consiste em configurações do serviço que podem ser carregadas no tempo de execução e os dados consistem em dados estáticos arbitrários a serem consumidos pelo serviço.

**Tipo de aplicativo**: o nome/versão atribuído a uma coleção de tipos de serviço. É definido em um arquivo `ApplicationManifest.xml` e inserido em um diretório do pacote de aplicativo. O diretório então é copiado para o repositório de imagens do cluster do Service Fabric. Assim, você pode criar um aplicativo nomeado desse tipo de aplicativo dentro do cluster.

Leia o artigo [Modelo de aplicativo](service-fabric-application-model.md) para obter mais informações.

**Pacote de aplicativos**: um diretório de disco contendo o arquivo `ApplicationManifest.xml` do tipo de aplicativo. Faz referência aos pacotes de serviço de cada tipo de serviço que compõe o tipo de aplicativo. Os arquivos no diretório do pacote de aplicativos são copiados no armazenamento de imagens do cluster do Service Fabric. Por exemplo, um pacote de aplicativos para um tipo de aplicativo de email pode conter referências a um pacote de serviços fila, um pacote de serviços front-end e um pacote de serviços de banco de dados.

**Aplicativo nomeado**: depois de copiar um pacote de aplicativos para o repositório de imagens, você cria uma instância do aplicativo no cluster. Você cria uma instância ao especificar o tipo de aplicativo do pacote de aplicativo usando seu nome ou versão. Cada instância de tipo de aplicativo recebe um nome de URI (Uniform Resource Identifier) que se parece com: `"fabric:/MyNamedApp"`. Em um cluster, você pode criar vários aplicativos nomeados a partir de um tipo de aplicativo único. Você também pode criar aplicativos nomeados a partir de diferentes tipos de aplicativos. Cada aplicativo nomeado é gerenciado e possui controle de versão independente.

**Tipo de serviço**: o nome/versão atribuído aos pacotes de códigos, pacotes de dados e pacotes de configuração de um serviço. O tipo de serviço é definido no arquivo `ServiceManifest.xml` e inserido em um diretório do pacote de serviço. O diretório do pacote de serviço então é referenciado por um arquivo `ApplicationManifest.xml` do pacote de aplicativo. Dentro do cluster, depois de criar um aplicativo nomeado, você pode criar um serviço nomeado a partir de um dos tipos de serviço do tipo de aplicativo. O arquivo `ServiceManifest.xml` do tipo de serviço descreve o serviço.

Leia o artigo [Modelo de aplicativo](service-fabric-application-model.md) para obter mais informações.

Há dois tipos de serviço:

* **Sem estado**: use um serviço sem estado quando o estado persistente do serviço for armazenado em um serviço de armazenamento externo, como o Armazenamento do Microsoft Azure, o Banco de Dados SQL do Azure ou o Azure Cosmos DB. Use um serviço sem estado quando o serviço não tiver armazenamento persistente. Por exemplo, para um serviço de calculadora no qual os valores são passados para o serviço, um cálculo é realizado usando esses valores e então um resultado é retornado.
* **Com estado**: use um serviço com estado quando quiser que o Service Fabric gerencie o estado de seu serviço por meio de seus modelos de programação Reliable Collections ou Reliable Actors. Ao criar um serviço nomeado, especifique quantas partições você deseja distribuir pelo seu estado para fins de escalabilidade. Especifique também quantas vezes replicar o estado entre os nós (para fins de confiabilidade). Cada serviço nomeado tem uma única réplica primária e várias réplicas secundárias. Você modifica o estado do serviço nomeado ao gravar na réplica primária. O Service Fabric então replica esse estado para todas as réplicas secundárias para manter seu estado em sincronia. O Service Fabric detecta automaticamente quando uma réplica primaria falha e promove uma réplica secundária existente a réplica primária. O Service Fabric cria então uma nova réplica secundária.  

**Réplicas ou instâncias** referem-se ao código (e ao estado para serviços com estado) de um serviço implantado e em execução. Consulte [Réplicas e instâncias](service-fabric-concepts-replica-lifecycle.md).

**Reconfiguração** refere-se ao processo de qualquer alteração no conjunto de réplicas de um serviço. Consulte [Reconfiguration](service-fabric-concepts-reconfiguration.md).

**Pacote de serviço**: um diretório de disco contendo o arquivo `ServiceManifest.xml` do tipo de serviço. Este arquivo faz referência ao código, aos dados estáticos e a pacotes de configuração para o tipo de serviço. Os arquivos no diretório do pacote de serviços são referenciados pelo arquivo `ApplicationManifest.xml` do tipo de aplicativo. Por exemplo, um pacote de serviços pode fazer referência ao código, aos dados estáticos e aos pacotes de configuração que compõem um serviço de banco de dados.

**Serviço nomeado**: depois de criar um aplicativo nomeado, você pode criar uma instância de um de seus tipos de serviço no cluster. Você especifica o tipo de serviço usando seu nome/versão. Cada instância de tipo de serviço recebe um nome de URI com escopo dentro do URI de seu aplicativo nomeado. Por exemplo, se você criar um serviço chamado "MyDatabase" dentro de um aplicativo chamado "MyNamedApp", o URI terá esta aparência: `"fabric:/MyNamedApp/MyDatabase"`. Dentro de um aplicativo nomeado, você pode criar vários serviços nomeados. Cada serviço nomeado pode ter seu próprio esquema de partição e contagens de instância ou réplica.

**Pacote de códigos**: um diretório de disco contendo arquivos executáveis do tipo de serviço, normalmente, arquivos EXE/DLL. Os arquivos no diretório do pacote de códigos são referenciados pelo arquivo `ServiceManifest.xml` do tipo de serviço. Quando você cria um serviço nomeado, o pacote de códigos é copiado para os nós selecionados para executar o serviço nomeado. Então o código começa a ser executado. Há dois tipos de arquivos executáveis de pacote de códigos:

* **Executáveis de convidado**: executáveis que são executados “no estado em que se encontram” no sistema operacional host (Windows ou Linux). Esses executáveis não criam um vínculo nem fazem referência a arquivos de tempo de execução do Service Fabric e, portanto, não usam nenhum modelo de programação do Service Fabric. Esses executáveis não conseguem usar alguns recursos do Service Fabric, como o serviço de nomenclatura para descoberta de ponto de extremidade. Executáveis convidados não podem relatar métricas de carga específicas para cada instância de serviço.
* **Executáveis do host de serviço**: são executáveis que usam modelos de programação do Service Fabric criando vínculos com arquivos de tempo de execução do Service Fabric, habilitando os recursos do Service Fabric. Por exemplo, uma instância de serviço nomeada pode registrar pontos de extremidade com o Serviço de Nomenclatura do Service Fabric, e também pode relatar métricas de carga.

**Pacote de dados**: um diretório de disco contendo os arquivos de dados estáticos somente leitura do tipo de serviço, normalmente arquivos de foto, som e vídeo. Os arquivos no diretório do pacote de dados são referenciados pelo arquivo `ServiceManifest.xml` do tipo de serviço. Quando você cria um serviço nomeado, o pacote de dados é copiado para os nós ou os nós selecionados para executar o serviço nomeado. O código começa a ser executado e agora pode acessar os arquivos de dados.

**Pacote de configuração**: um diretório de disco contendo os arquivos de configuração estáticos somente leitura do tipo de serviço, normalmente arquivos de texto. Os arquivos no diretório do pacote de configuração são referenciados pelo arquivo `ServiceManifest.xml` do tipo de serviço. Quando você cria um serviço nomeado, os arquivos no pacote de configuração são copiados para um ou mais nós selecionados para executar o serviço nomeado. O código começa a ser executado e agora pode acessar os arquivos de configuração.

**Contêineres**: Por padrão, o Service Fabric implanta e ativa esses serviços como processos. O Service Fabric também pode implantar serviços em imagens de contêiner. Contêineres são uma tecnologia de virtualização que virtualiza o sistema operacional subjacente de aplicativos. Um aplicativo e seu tempo de execução, dependências e bibliotecas do sistema são executados dentro de um contêiner. O contêiner tem acesso privado total à exibição isolada do próprio contêiner dos constructos de sistema operacional. O Service Fabric dá suporte a contêineres do Docker em contêineres Linux e Windows Server. Para obter mais informações, leia [Service Fabric e contêineres](service-fabric-containers-overview.md).

**Esquema de partição**: ao criar um serviço nomeado, você especifica um esquema de partição. Serviços com quantidades substanciais de estado dividem os dados entre as partições, que distribuem o estado pelos nós do cluster. Ao dividir os dados entre partições, você pode dimensionar o estado do serviço nomeado. Dentro de uma partição, serviços nomeados sem monitoração de estado têm instâncias, enquanto serviços nomeados com estado têm réplicas. Geralmente, serviços nomeados sem monitoração de estado têm apenas uma partição, pois não têm estado interno. As instâncias de partição proporcionam disponibilidade. Se uma instância falhar, outras instâncias continuarão funcionando normalmente, então o Service Fabric criará uma nova instância. Serviços nomeados com estado mantêm seu estado dentro de réplicas e cada partição tem sua própria réplica, de modo que o estado é mantido em sincronia. Caso uma réplica falhe, o Service Fabric compila uma nova réplica a partir das réplicas existentes.

Leia o artigo [Particionar os Reliable Services do Service Fabric](service-fabric-concepts-partitioning.md) para saber mais.

## <a name="system-services"></a>Serviços do sistema
Há serviços do sistema que são criados em cada cluster que fornece os recursos de plataforma do Service Fabric.

**Serviço de Nomenclatura**: cada cluster do Service Fabric tem um Serviço de Nomenclatura que resolve nomes de serviço para uma localização no cluster. Você gerencia os nomes de serviço e propriedades, como um DNS (Serviço de Nomes de Domínio ) da Internet para o cluster. Os clientes comunicam-se com segurança com qualquer nó no cluster usando o Serviço de Nomenclatura para resolver um nome de serviço e sua localização. Os aplicativos movem-se dentro do cluster. Por exemplo, isso pode ser devido a falhas, balanceamento de recursos ou o redimensionamento do cluster. Você pode desenvolver serviços e clientes que resolvem o local de rede atual. Os clientes obtêm o endereço IP do computador real e a porta em que ele está sendo executado.

Leia [Comunicar-se com os serviços](service-fabric-connect-and-communicate-with-services.md) para obter mais informações sobre as APIs de comunicação do serviço e do cliente que funcionam com o Serviço de Nomenclatura.

**Serviço de Repositório de Imagens**: cada cluster do Service Fabric tem um serviço de Repositório de Imagens em que os pacotes de aplicativos implantados com versão são mantidos. Copie um pacote de aplicativos para o Repositório de Imagens e registre o tipo de aplicativo nesse pacote de aplicativos. Após o provisionamento do tipo de aplicativo, você cria um aplicativo nomeado com base nele. Você pode cancelar o registro de um tipo de aplicativo do serviço de Repositório de Imagens após a exclusão de todos os seus aplicativos nomeados.

Leia [Entender a configuração ImageStoreConnectionString](service-fabric-image-store-connection-string.md) para saber mais sobre o serviço de Armazenamento de Imagens.

Leia o artigo [Implantar um aplicativo](service-fabric-deploy-remove-applications.md) para saber mais sobre como implantar aplicativos no serviço do Repositório de Imagens.

**Serviço do Gerenciador de Failover**: cada cluster do Service Fabric tem um serviço de Gerenciador de Failover responsável pelas seguintes ações:
   - Executa funções relacionadas a alta disponibilidade e consistência dos serviços.
   - Orquestra upgrades de aplicativo e de cluster.
   - Interage com outros componentes do sistema.

**Serviço do Gerenciador de Reparos**: este é um serviço de sistema opcional que permite que as ações de reparo sejam executadas em um cluster de maneira segura, automática e transparente. O Gerenciador de reparo é usado em:
   - Realizar reparos de manutenção do Azure na [durabilidade Silver e Gold](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) dos clusters do Azure Service Fabric.
   - Realizar ações de reparo para [Aplicativo de Orquestração de Patch](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Implantação e modelos de aplicativos 

Para implantar os serviços será necessário descrever como deverão ser executados. O Service Fabric dá suporte a três diferentes modelos de implantação:

### <a name="resource-model-preview"></a>Modelo de recurso (versão prévia)
Os Recursos do Service Fabric são tudo o que pode ser implantado individualmente no Service Fabric, incluindo aplicativos, serviços, redes e volumes. Os recursos são definidos usando um arquivo JSON, que pode ser implantado em um ponto de extremidade do cluster.  Para a Malha do Service Fabric, é usado o esquema do Modelo de Recurso do Azure. Um esquema de arquivo YAML também pode ser usado para criar arquivos de definição com mais facilidade. Os recursos podem ser implantados em qualquer lugar em que o Service Fabric execute. O modelo de recursos é a maneira mais simples de descrever os aplicativos do Service Fabric. O foco principal é a implantação e o gerenciamento simples de serviços de contêiner. Para saber mais, leia [Introdução ao Modelo de Recurso do Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Modelo nativo
O modelo de aplicativo nativo fornece aos aplicativos acesso de nível baixo completo ao Service Fabric. Os aplicativos e serviços são definidos como tipos registrados em arquivos de manifesto XML.

O modelo nativo dá suporte às estruturas Reliable Services e Reliable Actors, que fornecem acesso às APIs de tempo de execução do Service Fabric e APIs de gerenciamento de cluster em C# e Java. O modelo nativo também dá suporte a executáveis e contêineres arbitrários. O modelo nativo não é tem suporte no [ambiente de Malha do Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-overview).

**Reliable Services**: uma API para criar serviços com e sem estado. O serviço com estado armazena seu estado em Reliable Collections, como um dicionário ou uma fila. Você também pode conectar várias pilhas de comunicação, como API Web e WCF (Windows Communication Foundation).

**Reliable Actors**: uma API para criar objetos com e sem estado por meio do modelo de programação de ator virtual. Esse modelo é útil quando você tem muitas unidades independentes de computação ou estado. Esse modelo usa um modelo de threading baseado em turno, assim, é melhor evitar um código que chame outros atores ou serviços, já que um ator individual não pode processar outras solicitações de entrada até que todas as suas solicitações de saída estejam concluídas.

Você também pode executar os aplicativos existentes no Service Fabric:

**Contêineres**:  O Service Fabric dá suporte à implantação de contêineres do Docker em contêineres do Linux e do Windows Server no Windows Server 2016, juntamente com o suporte para o modo de isolamento do Hyper-V. No [modelo de aplicativo](service-fabric-application-model.md)do Service Fabric, um contêiner representa um host de aplicativo no qual várias réplicas de serviço são colocadas. O Service Fabric pode executar qualquer contêiner, e o cenário é semelhante ao cenário executável de convidado, no qual você pode empacotar um aplicativo existente dentro de um contêiner. Além disso, também é possível executar os [serviços do Service Fabric dentro de contêineres](service-fabric-services-inside-containers.md).

**Executáveis de convidado**: Você pode executar qualquer tipo de código, como Node.js, Java ou C++ no Service Fabric como um serviço. O Service Fabric se refere a esses tipos de serviço como executáveis convidados, que são tratados como serviços sem estado. As vantagens para executar um executável de convidado no cluster do Service Fabric inclui uma disponibilidade alta, monitoramento de integridade, gerenciamento do ciclo de vida do aplicativo, densidade alta e descoberta.

Leia o artigo [Escolher um modelo de programação para seu serviço](service-fabric-choose-framework.md) para saber mais.

### <a name="docker-compose"></a>Docker Compose 
O [Docker Compose](https://docs.docker.com/compose/) é parte do projeto Docker. O Service Fabric dá suporte limitado para [implantar aplicativos usando o modelo do Docker Compose](service-fabric-docker-compose.md).

## <a name="environments"></a>Ambientes

O Service Fabric é uma tecnologia de plataforma de software livre em que vários serviços e produtos diferentes são baseados. A Microsoft fornece as seguintes opções:

 - **Malha do Azure Service Fabric**: Um serviço totalmente gerenciado para executar aplicativos do Service Fabric no Microsoft Azure.
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

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o Service Fabric:

* [Visão geral da Malha do Serviço](service-fabric-overview.md)
* [Por que usar uma abordagem de microsserviço para construir aplicativos?](service-fabric-overview-microservices.md)
* [Cenários de aplicativos](service-fabric-application-scenarios.md)

Para saber mais sobre a Malha do Service Fabric:

* [Visão geral da Malha do Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-overview)
