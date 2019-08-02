---
title: Saiba mais sobre o Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre os principais conceitos e as principais áreas do Azure Service Fabric. Fornece uma visão geral estendida do Service Fabric e como criar microsserviços.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/08/2017
ms.author: atsenthi
ms.openlocfilehash: 1227871f2003ded7b9cb92eaf32bd9a984958f9f
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537819"
---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Então você deseja saber mais sobre o Service Fabric?
O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita o empacotamento, implantação e gerenciamento de microsserviços escalonáveis e confiáveis.  No entanto, o Service Fabric tem uma área de superfície grande, e há muito a aprender.  Este artigo fornece um resumo do Service Fabric e descreve os principais conceitos, modelos de programação, ciclo de vida do aplicativo, teste, clusters e monitoramento de integridade. Leia a [Visão geral](service-fabric-overview.md) e [O que são microsserviços?](service-fabric-overview-microservices.md) para obter uma introdução e saber como o Service Fabric pode ser usado para criar microsserviços. Este artigo não contém uma lista abrangente de conteúdo, mas vincula a visão geral e os artigos de introdução de cada área do Service Fabric. 

## <a name="core-concepts"></a>Conceitos principais
[Terminologia do Service Fabric](service-fabric-technical-overview.md), [Modelo de aplicativo](service-fabric-application-model.md) e [Modelos de programação com suporte](service-fabric-choose-framework.md) oferecem mais conceitos e descrições, mas aqui estão os fundamentos básicos.

### <a name="design-time-service-type-service-package-and-manifest-application-type-application-package-and-manifest"></a>Tempo de design: tipo de serviço, o pacote de serviço e manifesto, tipo de aplicativo, pacote de aplicativo e manifesto
Um tipo de serviço é o nome/versão atribuída aos pacotes de códigos, pacotes de dados e pacotes de configuração de um serviço. Isso é definido em um arquivo servicemanifest. XML. O tipo de serviço é composto de código executável e definições de configuração de serviço, que são carregadas em tempo de execução, e dados estáticos que são consumidos pelo serviço.

Um pacote de serviço é um diretório de disco que contém o arquivo de servicemanifest. XML do tipo de serviço, que faz referência a código, dados estáticos e pacotes de configuração para o tipo de serviço. Por exemplo, um pacote de serviços pode fazer referência ao código, aos dados estáticos e aos pacotes de configuração que compõem um serviço de banco de dados.

Um tipo de aplicativo é o nome/versão atribuído a uma coleção de tipos de serviço. Isso é definido em um arquivo applicationmanifest. XML.

![Tipos de aplicativos do Service Fabric e tipos de serviço][cluster-imagestore-apptypes]

O pacote de aplicativo é um diretório de disco que contém o arquivo applicationmanifest. XML do tipo de aplicativo, que faz referência os pacotes de serviço para cada tipo de serviço que compõe o tipo de aplicativo. Por exemplo, um pacote de aplicativos para um tipo de aplicativo de email pode conter referências a um pacote de serviços Fila, um pacote de serviços front-end e um pacote de serviços de banco de dados.  

Os arquivos no diretório do pacote de aplicativos são copiados no armazenamento de imagens do cluster do Service Fabric. Assim, você pode criar um aplicativo nomeado desse tipo de aplicativo que, em seguida, é executado no cluster. Depois de criar um aplicativo nomeado, você pode criar um serviço nomeado de um dos tipos de serviço do tipo de aplicativo. 

### <a name="run-time-clusters-and-nodes-named-applications-named-services-partitions-and-replicas"></a>Tempo de execução: clusters e nós, chamados de aplicativos, chamado de serviços, partições e réplicas
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados. Os clusters podem ser dimensionados para milhares de máquinas.

Uma máquina ou VM que faz parte de um cluster é chamado de nó. Cada nó recebe um nome de nó (uma cadeia de caracteres). Os nós têm características como propriedades de posicionamento. Cada máquina ou VM tem um serviço do Windows de início automático, `FabricHost.exe` que começa a ser executado na inicialização e então inicia dois executáveis: `Fabric.exe` e `FabricGateway.exe`. Esses dois executáveis compõem o nó. Para cenários de desenvolvimento ou teste, você pode hospedar vários nós em um único computador ou VM executando várias instâncias de `Fabric.exe` e `FabricGateway.exe`.

Um aplicativo nomeado é uma coleção de serviços membros que executam uma determinada função ou funções. Um serviço executa uma função autônoma completa (que pode iniciar e ser executada independentemente de outros serviços) e é composto de código, configuração e dados. Depois de copiar um pacote de aplicativos para o repositório de imagens, você cria uma instância do aplicativo no cluster, especificando o tipo de aplicativo do pacote de aplicativos (usando seu nome/versão). Cada instância do tipo de aplicativo tem um nome URI atribuído que se parece com *fabric:/MyNamedApp*. Em um cluster, você pode criar vários aplicativos nomeados a partir de um tipo de aplicativo único. Você também pode criar aplicativos nomeados a partir de diferentes tipos de aplicativos. Cada aplicativo nomeado é gerenciado e possui controle de versão independente.

Depois de criar um aplicativo nomeado, você pode criar uma instância de um de seus tipos de serviço (um serviço nomeado) no cluster, especificando o tipo de serviço (usando seu nome/versão). Cada instância de tipo de serviço recebe um nome de URI com escopo dentro do URI de seu aplicativo nomeado. Por exemplo, se você criar um serviço chamado "MeuBancoDeDados" dentro de um aplicativo chamado "MeuAplicativoNomeado”, o URI terá esta aparência: *fabric:/MeuAplicativoNomeado/MeuBancoDeDados*. Dentro de um aplicativo nomeado, você pode criar um ou mais serviços nomeados. Cada serviço nomeado pode ter seu próprio esquema de partição e contagens de instância/réplica. 

Há dois tipos de serviços: com e sem monitoração. Serviços sem estado não armazenam o estado dentro do serviço. Os serviços sem estado não têm nenhum armazenamento persistente ou armazenam o estado persistente em um serviço de armazenamento externo, como o Armazenamento do Azure, o Banco de Dados SQL do Azure ou o Banco de Dados do Azure Cosmos. Um serviço stateful armazena o estado dentro do serviço e usa modelos de programação Reliable Collections ou Reliable Actors para gerenciar o estado. 

Ao criar um serviço nomeado, você especifica um esquema de partição. Serviços com grandes quantidades de estado dividem os dados entre partições. Cada partição é responsável por uma parte do estado completo do serviço, que é distribuído entre os nós do cluster.  

O diagrama a seguir mostra a relação entre aplicativos e instâncias de serviço, partições e réplicas.

![Partições e réplicas dentro de um serviço][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>Particionamento, dimensionamento e disponibilidade
O [particionamento](service-fabric-concepts-partitioning.md) não é exclusivo do Service Fabric. Uma forma bem conhecida de particionamento é o particionamento de dados ou fragmentação. Serviços com estado, com grandes quantidades de estado, dividem os dados entre partições. Cada partição é responsável por uma parte do estado completo do serviço. 

As réplicas de cada partição são distribuídas entre os nós do cluster, o que permite que o estado do serviço nomeado seja [escalonado](service-fabric-concepts-scalability.md). Conforme os dados precisam crescer, as partições crescem e o Service Fabric redistribui as partições entre os nós para fazer uso eficiente dos recursos de hardware. Se você adicionar novos nós ao cluster, o Service Fabric reequilibrará as réplicas de partição entre o número aumentado de nós. O desempenho geral do aplicativo é melhorado e a contenção para o acesso à memória é reduzida. Se os nós no cluster não estiverem sendo usados com eficiência, você poderá diminuir o número de nós no cluster. O Service Fabric redistribui novamente as réplicas de partição entre o número reduzido de nós, para fazer melhor uso do hardware em cada nó.

Dentro de uma partição, serviços nomeados sem estado têm instâncias, enquanto serviços nomeados com estado têm réplicas. Normalmente, os serviços nomeadas sem estado têm apenas uma partição, já que não têm estado interno. As instâncias de partição proporcionam [disponibilidade](service-fabric-availability-services.md). Se uma instância falha, outras instâncias continuam a funcionar normalmente e, em seguida, o Service Fabric cria uma nova instância. Os serviços nomeados com estado mantêm seu estado dentro de réplicas e cada partição tem seu próprio conjunto de réplicas. As operações de leitura e gravação são realizadas em uma réplica (chamada de Primária). As alterações no estado, devidas a operações de gravação, são replicadas para outras réplicas (chamadas de Secundárias Ativas). Caso uma réplica falhe, o Service Fabric compila uma nova réplica a partir das réplicas existentes.

## <a name="stateless-and-stateful-microservices-for-service-fabric"></a>Microsserviços com e sem estado para o Service Fabric
O Service Fabric permite compilar aplicativos que consistam em microsserviços ou contêineres. Os microsserviços sem estado (como gateways de protocolo e proxies Web) não mantêm um estado mutável fora de uma solicitação e sua resposta do serviço. As funções de trabalho dos Serviços de Nuvem do Azure são um exemplo de serviço sem estado. Os microsserviços com estado (como contas de usuário, bancos de dados, dispositivos, carrinhos de compra e filas) mantêm um estado mutável e autoritativo além da solicitação e sua resposta. Os aplicativos em escala da Internet de hoje consistem em uma combinação de microsserviços com e sem estado. 

Uma diferença importante com o Service Fabric é seu foco forte na criação de serviços com monitoração de estado, com o [modelos de programação internas](service-fabric-choose-framework.md) ou com serviços com monitoração de estado em contêineres. Os [cenários de aplicativo](service-fabric-application-scenarios.md) descrevem os cenários em que os serviços com monitoração de estado são usados.

Por que ter microsserviços com estado junto microsserviços sem estado? Os dois motivos principais são:

* É possível compilar serviços OLTP (processamento de transações online tolerante a falha) de alta taxa de transferência e baixa latência mantendo o códigos e dados próximos no mesmo computador. Alguns exemplos são vitrines interativas, pesquisa, sistemas de Internet das coisas (IoT), sistemas de comércio, sistemas de detecção de fraudes e processamento de cartão de crédito e gerenciamento de registros pessoais.
* Você pode simplificar o design do aplicativo. Os microsserviços com estado eliminam a necessidade de filas e caches adicionais tradicionalmente necessários para atender aos requisitos de disponibilidade e latência de um aplicativo puramente sem estado. Os serviços com estado são naturalmente de alta disponibilidade e baixa latência, reduzindo o número de partes móveis para gerenciar em seu aplicativo como um todo.

## <a name="supported-programming-models"></a>Modelos de programação com suporte
O Service Fabric oferece várias maneiras de escrever e gerenciar seus serviços. Os serviços podem usar as APIs do Service Fabric para aproveitarem ao máximo os recursos e as estruturas de aplicativo da plataforma. Os serviços também podem ser um programa executável compilado, escrito em qualquer linguagem e hospedado em um cluster do Service Fabric. Para saber mais, veja [Modelos de programação com suporte](service-fabric-choose-framework.md).

### <a name="containers"></a>Contêineres
Por padrão, o Service Fabric implanta e ativa esses serviços como processos. O Service Fabric também pode implantar serviços em [contêineres](service-fabric-containers-overview.md). É importante observar que você pode misturar serviços em processos e serviços em contêineres no mesmo aplicativo. O Service Fabric dá suporte à implantação de contêineres do Linux e do Windows no Windows Server 2016. Você pode implantar aplicativos existentes, serviços sem monitoração de estado ou serviços com estado em contêineres. 

### <a name="reliable-services"></a>Reliable Services
O [Reliable Services](service-fabric-reliable-services-introduction.md) é uma estrutura leve para o desenvolvimento de serviços que são integrados à plataforma do Service Fabric e se beneficiam do conjunto completo de recursos da plataforma. O Reliable Services pode ser sem monitoração de estado (semelhante à maioria das plataformas de serviço, como servidores Web ou Funções de Trabalho nos Serviços de Nuvem do Azure), em que o estado é persistido em uma solução externa, como o BD do Azure ou o Armazenamento de Tabelas do Azure. O Reliable Services também pode ser com estado, em que o estado é persistido diretamente no próprio serviço usando Coleções Confiáveis. O estado se torna [altamente disponível](service-fabric-availability-services.md) por meio de replicação e é distribuído por meio de [particionamento](service-fabric-concepts-partitioning.md), tudo gerenciado automaticamente pelo Service Fabric.

### <a name="reliable-actors"></a>Reliable Actors
Criada com base no Reliable Services, a estrutura [Reliable Actor](service-fabric-reliable-actors-introduction.md) é uma estrutura de aplicativo que implementa o padrão Ator Virtual baseado no padrão de design de ator. A estrutura Reliable Actor usa unidades independentes de computação e de estado com execução single-threaded chamadas atores. A estrutura Reliable Actor fornece comunicação interna para atores e configurações de expansão e persistência de estado predefinido.

### <a name="aspnet-core"></a>ASP.NET Core
O Service Fabric é integrado ao [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) como um modelo de programação de primeira classe para a criação de aplicativos Web e de API.  O Núcleo do ASP.NET pode ser usado de duas maneiras diferentes no Service Fabric:

- Hospedado como um executável convidado. Isso é usado principalmente para executar aplicativos do Núcleo do ASP.NET existentes no Service Fabric sem alterações de código.
- Executar em um Serviço Confiável. Isso permite uma melhor integração com o tempo de execução do Service Fabric e permite os serviços de Núcleo do ASP.NET com monitoração de estado.

### <a name="guest-executables"></a>Executáveis de convidado
Um [executável convidado](service-fabric-guest-executables-introduction.md) é um executável existente e arbitrário (escrito em qualquer linguagem), hospedado em um cluster do Service Fabric junto com outros serviços. Os executáveis convidados não são integrados diretamente às APIs do Service Fabric. No entanto, eles ainda se beneficiam dos recursos que a plataforma oferece, como relatórios personalizados de integridade e carregamento e a capacidade de descoberta de serviço com a chamada às APIs REST. Eles também têm suporte completo do ciclo de vida do aplicativo. 

## <a name="application-lifecycle"></a>Ciclo de vida do aplicativo
Semelhante a outras plataformas, um aplicativo no Service Fabric geralmente passa pelas seguintes fases: design, desenvolvimento, teste, implantação, atualização, manutenção e remoção. O Service Fabric dá um excelente suporte ao ciclo de vida completo dos aplicativos em nuvem, desde o desenvolvimento até a implantação, gerenciamento diário, manutenção e possível encerramento. O modelo de serviço permite que várias funções diferentes participem do ciclo de vida do aplicativo de forma independente. O [ciclo de vida de aplicativo do Service Fabric](service-fabric-application-lifecycle.md) fornece uma visão geral das APIs e de como elas são usadas pelas diferentes funções em todas as fases do ciclo de vida de aplicativo do Service Fabric. 

O ciclo de vida inteiro do aplicativo pode ser gerenciado usando [cmdlets do PowerShell](/powershell/module/ServiceFabric/), [comandos CLI](service-fabric-sfctl.md), [c# APIs](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient), [APIs Java](/java/api/overview/azure/servicefabric), e [ APIs REST](/rest/api/servicefabric/). Você também pode configurar pipelines de integração contínua/implantação contínua, usando ferramentas como o [Azure Pipelines](service-fabric-set-up-continuous-integration.md) ou [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md).

## <a name="test-applications-and-services"></a>Testar aplicativos e serviços
Para criar serviços que são realmente em escala de nuvem, é essencial verificar se os aplicativos e serviços podem dar suporte a falhas reais. O Serviço de Análise de Falha foi desenvolvido para testar serviços criados com base no Service Fabric. Com o [Serviço de Análise de Falha](service-fabric-testability-overview.md), é possível induzir falhas significativas e executar cenários de teste completos em seus aplicativos. Esses cenários e falhas praticam e validam os vários estados e transições pelos quais um serviço passa durante seu tempo de vida, tudo de maneira consistente, segura e controlada.

As [Ações](service-fabric-testability-actions.md) destinam-se ao teste de um serviço, usando falhas individuais. Um desenvolvedor de serviço pode usá-las como blocos de construção para escrever cenários complicados. Exemplos de falhas simuladas são:

* Reinicie um nó para simular qualquer quantidade de situações nas quais um computador ou VM é reinicializada.
* Mova uma réplica de seu serviço com estado para simular o balanceamento de carga, failover ou atualização do aplicativo.
* Invoque a perda de quorum em um serviço com estado para criar uma situação na qual não é possível realizar operações de gravação, pois não há réplicas "secundárias" ou de "backup" suficientes para aceitar novos dados.
* Invoque a perda de dados em um serviço com estado para criar uma situação na qual todo o estado na memória é completamente apagado.

Os [cenários](service-fabric-testability-scenarios.md) são operações complexas compostas de uma ou mais ações. O Serviço de Análise de Falha fornece dois cenários internos completos:

* [Cenário caótico](service-fabric-controlled-chaos.md) – simula falhas intercaladas contínuas (amigáveis e não amigáveis), em todo o cluster durante longos períodos de tempo.
* [Cenário de failover](service-fabric-testability-scenarios.md#failover-test) – uma versão do cenário de teste de caos que tem como alvo uma partição de serviço específica, deixando outros serviços não afetados.

## <a name="clusters"></a>Clusters
Um [cluster do Service Fabric](service-fabric-deploy-anywhere.md) é um conjunto de máquinas físicas ou virtuais conectadas em rede, no qual os microsserviços são implantados e gerenciados. Os clusters podem ser dimensionados para milhares de máquinas. Um computador ou VM que faz parte de um cluster é chamado de nó de cluster. Cada nó recebe um nome de nó (uma cadeia de caracteres). Os nós têm características como propriedades de posicionamento. Cada máquina ou VM tem um serviço de início automático, `FabricHost.exe`, que começa a ser executado na inicialização e então inicia dois executáveis: Fabric.exe e FabricGateway.exe. Esses dois executáveis compõem o nó. Para cenários de teste, você pode hospedar vários nós em um único computador ou VM, executando várias instâncias do `Fabric.exe` e do `FabricGateway.exe`.

Os clusters do Service Fabric podem ser criados em máquinas virtuais ou computadores físicos que executam o Windows Server ou Linux. Você pode implantar e executar aplicativos do Service Fabric em qualquer ambiente em que tenha um conjunto de computadores do Windows Server ou do Linux interconectados: locais, no Microsoft Azure ou em qualquer provedor de nuvem.

### <a name="clusters-on-azure"></a>Clusters no Azure
A execução de clusters do Service Fabric no Azure proporciona integração a outros recursos e serviços do Azure, o que facilita e torna as operações e o gerenciamento do cluster mais confiável. Um cluster é um recurso do Azure Resource Manager, portanto você pode modelar clusters da mesma maneira que qualquer outro recurso no Azure. O Resource Manager também proporciona o gerenciamento fácil de todos os recursos usados pelo cluster como uma única unidade. Clusters no Azure são integrados com o diagnóstico do Azure e logs do Azure Monitor. Os tipos de nó de cluster são [conjuntos de dimensionamento de máquinas virtuais](/azure/virtual-machine-scale-sets/index) e, portanto, a funcionalidade de dimensionamento automático é interna.

Você pode criar um cluster no Azure por meio do [Portal do Azure](service-fabric-cluster-creation-via-portal.md), de um [modelo](service-fabric-cluster-creation-via-arm.md) ou do [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md).

O Service Fabric no Linux permite criar, implantar e gerenciar aplicativos altamente disponíveis e altamente escalonáveis no Linux, assim como você faria no Windows. As estruturas do Service Fabric (Reliable Services e Reliable Actors) estão disponíveis em Java no Linux, além do C# (.NET Core). Você também pode compilar os [serviços executáveis convidados](service-fabric-guest-executables-introduction.md) com qualquer linguagem ou estrutura. Também há suporte para a organização de contêineres do Docker. Os contêineres de Docker podem executar arquivos executáveis de convidado ou serviços nativos do Service Fabric, que usam as estruturas do Service Fabric. Para obter mais informações, leia sobre o [Azure Service Fabric no Linux](service-fabric-deploy-anywhere.md).

Há alguns recursos que têm suporte no Windows, mas não no Linux. Para saber mais, leia [Diferenças entre o Service Fabric no Linux e no Windows](service-fabric-linux-windows-differences.md).

### <a name="standalone-clusters"></a>Clusters independentes
O Service Fabric fornece um pacote de instalação para você criar clusters autônomos do Service Fabric localmente ou em qualquer provedor de nuvem. Os clusters autônomos proporcionam a liberdade de hospedar um cluster em qualquer local que você desejar. Se seus dados estão sujeitos a restrições de normas ou de conformidade, ou se deseja manter seus dados locais, você pode hospedar seu próprio cluster e aplicativos. Os aplicativos do Service Fabric podem ser executados em vários ambientes de hospedagem sem alterações, portanto, seu conhecimento de criação de aplicativos é transportado de um ambiente de hospedagem para o outro. 

[Criar seu primeiro cluster autônomo do Service Fabric](service-fabric-cluster-creation-for-windows-server.md)

Ainda não há suporte para clusters autônomos do Linux.

### <a name="cluster-security"></a>Segurança de cluster
Os clusters sempre devem ser protegidos para evitar que usuários não autorizados se conectem ao cluster, especialmente quando ele tiver cargas de trabalho de produção em execução. Embora seja possível criar um cluster não seguro, fazer isso permitirá que usuários anônimos se conectem a ele, caso os pontos de extremidade de gerenciamento sejam expostos à Internet pública. Não é possível habilitar posteriormente a segurança em um cluster não seguro: a segurança de cluster é habilitada no momento da criação do cluster.

Os cenários de segurança do cluster são:
* Segurança de nó para nó
* Segurança de cliente para nó
* RBAC (Controle de Acesso Baseado em Função)

Para obter mais informações, leia [Proteger um cluster](service-fabric-cluster-security.md).

### <a name="scaling"></a>Dimensionamento
Se você adiciona novos nós ao cluster, o Service Fabric reequilibra as réplicas de partição e instâncias entre o número aumentado de nós. O desempenho geral do aplicativo é melhorado e a contenção para o acesso à memória é reduzida. Se os nós no cluster não estiverem sendo usados com eficiência, você poderá diminuir o número de nós no cluster. O Service Fabric redistribui novamente as réplicas de partição e instâncias entre o número reduzido de nós, para fazer melhor uso do hardware em cada nó. Você pode dimensionar clusters no Azure [manualmente](service-fabric-cluster-scale-up-down.md) ou [programaticamente](service-fabric-cluster-programmatic-scaling.md). Clusters autônomos podem ser dimensionados [manualmente](service-fabric-cluster-windows-server-add-remove-nodes.md).

### <a name="cluster-upgrades"></a>Atualizações do cluster
Periodicamente, são lançadas novas versões do tempo de execução do Service Fabric. Realize as atualizações de tempo de execução ou de malha no cluster para que você sempre tenha uma [versão com suporte](service-fabric-support.md) em execução. Além das atualizações de malha, você também pode atualizar a configuração do cluster, como os certificados ou as portas de aplicativo.

Um cluster do Service Fabric é um recurso cujo proprietário é você, mas que é parcialmente gerenciado pela Microsoft. A Microsoft é responsável por corrigir o sistema operacional subjacente e realizar atualizações de malha em seu cluster. Você pode definir para que o cluster receba atualizações automáticas de malha, quando a Microsoft lança uma nova versão, ou optar por selecionar uma versão de malha com suporte que você queira. As atualizações de malha e de configuração podem ser definidas por meio do Portal do Azure ou por meio do Resource Manager. Para obter mais informações, leia [Atualizar um cluster do Service Fabric](service-fabric-cluster-upgrade.md). 

Um cluster autônomo é um recurso que é totalmente seu. Você é responsável por corrigir o sistema operacional subjacente e iniciar atualizações de malha. Se seu cluster puder se conectar ao [https://www.microsoft.com/download](https://www.microsoft.com/download), você poderá configurá-lo para fazer o download e provisionar automaticamente o novo pacote de tempo de execução do Service Fabric. Em seguida, você iniciaria a atualização. Se seu cluster não puder acessar o [https://www.microsoft.com/download](https://www.microsoft.com/download), você poderá fazer o download manualmente o novo pacote de tempo de execução em um computador conectado à Internet e, em seguida, iniciar a atualização. Para obter mais informações, leia [Atualizar um cluster autônomo do Service Fabric](service-fabric-cluster-upgrade-windows-server.md).

## <a name="health-monitoring"></a>Monitoramento da integridade
O Service Fabric apresenta um [modelo de integridade](service-fabric-health-introduction.md) desenvolvido para sinalizar condições de cluster e aplicativo não íntegras em entidades específicas (como nós de cluster e réplicas de serviço). O modelo de integridade usa relatores de integridade (componentes do sistema e watchdogs). O objetivo é facilitar e agilizar o diagnóstico e o reparo. Os desenvolvedores de serviço precisam pensar antecipadamente a respeito da integridade e como [criar relatórios de integridade](service-fabric-report-health.md#design-health-reporting). Qualquer condição que possa afetar a integridade deve ser apontada, especialmente se ela puder ajudar a sinalizar problemas próximos da raiz. As informações de integridade podem economizar tempo e esforço na depuração e investigação quando o serviço estiver funcionando em escala na produção.

Os relatores da Malha de Serviço monitoram condições identificadas de interesse. Eles informam essas condições com base na respectiva exibição local. O [repositório de integridade](service-fabric-health-introduction.md#health-store) agrega dados de integridade enviados por todos os relatores para determinar se as entidades estão globalmente íntegras. O modelo foi desenvolvido com a intenção de ser completo, flexível e fácil de usar. A qualidade dos relatórios de integridade determina a precisão da exibição de integridade do cluster. Falsos positivos que mostram incorretamente problemas de integridade podem afetar negativamente atualizações ou outros serviços que usam dados de integridade. Exemplos de tais serviços são os serviços de reparo e os mecanismos de alerta. Portanto, é necessário um pouco de criatividade para fornecer relatórios que capturem condições de interesse da melhor forma possível.

A emissão de relatórios pode ser feita:
* Na réplica ou instância de serviço monitorado do Service Fabric.
* De watchdogs internos implantados como um serviço do Service Fabric (por exemplo, um serviço sem estado do Service Fabric que monitora as condições e emite relatórios). Os watchdogs podem ser implantados em todos os nós ou podem ser relacionados por afinidade com o serviço monitorado.
* Em watchdogs internos que são executados nos nós do Service Fabric, mas que não são implementados como serviços do Service Fabric.
* Em watchdogs externos que testam o recurso de fora do cluster do Service Fabric (por exemplo, um serviço de monitoramento, como o Gomez).

Os componentes do Service Fabric apresentam relatórios de integridade prontos para uso sobre todas as entidades do cluster. Os [relatórios de integridade do sistema](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) fornecem visibilidade da funcionalidade do cluster e de aplicativos e sinalizam problemas por meio da integridade. Para aplicativos e serviços, os relatórios de integridade do sistema verificam se as entidades estão implementadas e se comportando corretamente da perspectiva do tempo de execução do Service Fabric. Os relatórios não fornecem monitoramento de integridade da lógica de negócios do serviço nem detectam processos que pararam de responder. Para adicionar informações de integridade específicas à lógica do seu serviço, [implemente relatórios de integridade personalizados](service-fabric-report-health.md) em seus serviços.

O Service Fabric fornece várias maneiras de [exibir relatórios de integridade](service-fabric-view-entities-aggregated-health.md) agregados no repositório de integridade:
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização.
* Consultas de integridade (por meio do [PowerShell](/powershell/module/ServiceFabric/), da [CLI](service-fabric-sfctl.md), das [APIs FabricClient de C#](/dotnet/api/system.fabric.fabricclient.healthclient) e das [APIs FabricClient de Java](/java/api/system.fabric), ou das [APIs REST](/rest/api/servicefabric)).
* Consultas gerais que retornam uma lista de entidades que têm a integridade como uma das propriedades (por meio do PowerShell, da CLI, das APIs ou de REST).

## <a name="monitoring-and-diagnostics"></a>Monitoramento e diagnóstico
[O monitoramento e o diagnóstico](service-fabric-diagnostics-overview.md) são essenciais para o desenvolvimento, os testes e a implantação de aplicativos e serviços em qualquer ambiente. As soluções do Service Fabric funcionam melhor quando você planeja e implementa o monitoramento e o diagnóstico que ajudam a garantir que aplicativos e serviços funcionem conforme o esperado em um ambiente de desenvolvimento local ou na produção.

As principais metas do monitoramento e diagnóstico são:

- Detectar e diagnosticar problemas de hardware e infraestrutura
- Detectar problemas de software e aplicativos e reduzir o tempo de inatividade do serviço
- Entender o consumo de recursos e ajudar a orientar as decisões de operações
- Otimizar o desempenho de aplicativos, serviços e infraestrutura
- Gerar informações de negócios e identificar áreas de aprimoramento
 
O fluxo de trabalho geral do monitoramento e diagnóstico consiste em três etapas:

1. Geração de eventos: isso inclui eventos (logs, rastreamentos, eventos personalizados) nos níveis de infraestrutura (cluster), plataforma e aplicativo/serviço
2. Agregação de eventos: os eventos gerados precisam ser coletados e agregados antes que possam ser exibidos
3. Análise: os eventos precisam ser visualizados e estar acessíveis em algum formato, para permitir a análise e a exibição conforme necessário

Estão disponíveis vários produtos que abrangem essas três áreas, e você é livre para escolher tecnologias diferentes para cada um. Para obter mais informações, leia [Monitoramento e diagnóstico no Azure Service Fabric](service-fabric-diagnostics-overview.md).

## <a name="next-steps"></a>Próximas etapas
* Saiba como criar um [cluster no Azure](service-fabric-cluster-creation-via-portal.md) ou um [cluster autônomo no Windows](service-fabric-cluster-creation-for-windows-server.md).
* Tente criar um serviço usando os modelos de programação [Reliable Services](service-fabric-reliable-services-quick-start.md) ou [Reliable Actors](service-fabric-reliable-actors-get-started.md).
* Saiba como [migrar dos Serviços de Nuvem](service-fabric-cloud-services-migration-differences.md).
* Saiba como [monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). 
* Aprenda a [testar seus aplicativos e serviços](service-fabric-testability-overview.md).
* Aprenda a [gerenciar e orquestrar recursos do cluster](service-fabric-cluster-resource-manager-introduction.md).
* Examine os [exemplos do Service Fabric](https://aka.ms/servicefabricsamples).
* Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md).
* Leia o [blog da equipe](https://blogs.msdn.microsoft.com/azureservicefabric/) para artigos e comunicados.


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png
