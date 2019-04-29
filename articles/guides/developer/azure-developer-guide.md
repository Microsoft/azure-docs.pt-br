---
title: Guia de introdução para desenvolvedores no Azure | Microsoft Docs
description: Este tópico fornece informações essenciais aos desenvolvedores que desejam começar a usar a plataforma Microsoft Azure para suas necessidades de desenvolvimento.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: glenga
ms.openlocfilehash: dc44cfbd24bd04caeede03dcbcfc60da06f61135
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921675"
---
# <a name="get-started-guide-for-azure-developers"></a>Guia de introdução para desenvolvedores do Azure

## <a name="what-is-azure"></a>O que é o Azure?

O Azure é uma plataforma de nuvem que pode hospedar seus aplicativos existentes, simplificar o desenvolvimento de novos aplicativos e ainda aprimorar aplicativos locais. O Azure integra os serviços de nuvem necessários para desenvolver, testar, implantar e gerenciar seus aplicativos — e aproveitar as eficiências da computação em nuvem.

Ao hospedar seus aplicativos no Azure, você pode começar por algo pequeno e facilmente escalar seu aplicativo à medida que aumenta a demanda do cliente. O Azure também oferece a confiabilidade necessária para aplicativos de alta disponibilidade, incluindo failover entre diferentes regiões. O [portal do Azure](https://portal.azure.com) permite que você gerencie facilmente todos os serviços do Azure. Além disso, também é possível gerenciar seus serviços programaticamente, utilizando modelos e APIs e específicos do serviço.

**A quem se destina este guia**: Este guia é uma introdução à plataforma Azure para desenvolvedores de aplicativos. Este guia fornece diretrizes e direção necessárias para começar a criar novos aplicativos no Azure ou migrar aplicativos existentes para o Azure.

## <a name="where-do-i-start"></a>Por onde começo?

Com todos os serviços que o Azure oferece, pode ser uma tarefa assustadora descobrir quais serviços são necessários para suportar a arquitetura da solução. Esta seção destaca os serviços do Azure que normalmente são utilizados pelos desenvolvedores. Para obter uma lista de todos os serviços do Azure, consulte a [Documentação do Azure](../../index.md).

Primeiro, você deve decidir como hospedar o aplicativo no Azure. É necessário gerenciar toda a sua infraestrutura como uma VM (máquina virtual). É possível utilizar as instalações de gerenciamento da plataforma que o Azure fornece? Você precisa de uma estrutura sem servidor para hospedar a execução de código somente?

Seu aplicativo precisa de armazenamento em nuvem para qual o Azure oferece várias opções. Você pode usufruir da autenticação empresarial do Azure. Além disso, há ferramentas para monitoramento e desenvolvimento baseado em nuvem e a maioria dos serviços de hospedagem oferece integração DevOps.

Agora, vejamos alguns dos serviços específicos que recomendamos investigar para suas aplicações.

### <a name="application-hosting"></a>Hospedagem de aplicativos

O Azure fornece várias ofertas de computação baseada em nuvem para executar seu aplicativo, de modo que você não precise se preocupar com os detalhes da infraestrutura. Você pode facilmente escalar verticalmente ou escalar horizontalmente seus recursos à medida que o uso do aplicativo aumenta.

O Azure oferece serviços que dão suporte ao desenvolvimento de aplicativos e necessidades de hospedagem. O Azure fornece IaaS (Infraestrutura como Serviço) para oferecer-lhe controle total sobre a hospedagem do aplicativo. As ofertas de PaaS (Plataforma como Serviço) do Azure fornecem os serviços totalmente gerenciados necessários para potencializar seus aplicativos. Há, inclusive, verdadeira hospedagem sem servidor no Azure, onde tudo o que você precisa fazer é escrever seu código.

![Opções de hospedagem de aplicativo do Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Serviço de aplicativo do Azure 

Ao buscar o caminho mais rápido para publicar seus projetos baseados na web, considere o Serviço de Aplicativo do Azure. O Serviço de Aplicativo facilita a extensão de seus aplicativos Web para oferecer suporte aos clientes móveis e publicar facilmente as APIs REST consumidas. Essa plataforma fornece autenticação utilizando provedores sociais, dimensionamento automático baseado em tráfego, teste em produção e implantações baseadas em contêiner e contínuas.

Você pode criar aplicativos Web, back-ends de aplicativos móveis e aplicativos de API.

Como todos os três tipos de aplicativos compartilham o tempo de execução do Serviço de Aplicativo, é possível hospedar um site, oferecer suporte a clientes móveis e expor suas APIs no Azure, tudo a partir do mesmo projeto ou solução. Para saber mais sobre o Serviço de Aplicativo, consulte [O que são Aplicativos Web do Azure](../../app-service/overview.md).

O Serviço de Aplicativo foi projetado com o DevOps em mente. Ele dá suporte a várias ferramentas para implantações de integração contínua e publicações, incluindo webhooks GitHub, Jenkins, Azure DevOps, TeamCity e outros.

É possível migrar seus aplicativos existentes para o Serviço de Aplicativo, utilizando a [ferramenta de migração online](https://www.migratetoazure.net/).

> **Quando usar**: Use o Serviço de Aplicativo ao migrar aplicativos Web existentes para o Azure e quando precisar de uma plataforma de hospedagem totalmente gerenciada para seus aplicativos Web. Al´me disso, é possível utilizar o Serviço de Aplicativo quando precisar dar suporte a clientes móveis ou expor APIs REST com seu aplicativo.
> 
> **Introdução**: O Serviço de Aplicativo facilita a criação e a implantação de seu primeiro [aplicativo Web](../../app-service/app-service-web-get-started-dotnet.md), [aplicativo móvel](../../app-service-mobile/app-service-mobile-ios-get-started.md) ou [aplicativo de API](../../app-service/app-service-web-tutorial-rest-api.md).
> 
> **Experimentar agora**: O Serviço de Aplicativo permite que você provisione um aplicativo de curta duração para experimentar a plataforma sem precisar se inscrever em uma conta do Azure. Experimente a plataforma e [crie seu aplicativo do Serviço de Aplicativo do Azure](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Máquinas Virtuais do Azure

Como um provedor da IaaS (Infraestrutura como Serviço), o Azure permite implantar ou migrar seu aplicativo para VMs Linux ou Windows. Juntamente com a Rede Virtual do Azure, as Máquinas Virtuais do Azure dão suporte para a implantação de VMs Linux ou Windows para o Azure. Com VMs, você tem total controle sobre a configuração da máquina. Ao utilizar VMs, você será responsável por toda a instalação de software para servidores, configuração, manutenção e patches do sistema operacional.

Devido ao nível de controle que você possui com VMs, será possível executar uma ampla gama de cargas de trabalho do servidor no Azure que não se ajustam em um modelo de PaaS. Essas cargas de trabalho incluem servidores de banco de dados, Windows Server Active Directory e Microsoft SharePoint. Para obter mais informações, consulte a documentação sobre Máquinas Virtuais para [Linux](/azure/virtual-machines/linux/) ou [Windows](/azure/virtual-machines/windows/).

> **Quando usar**: Use Máquinas Virtuais quando desejar ter controle total sobre a infraestrutura do aplicativo ou para migrar cargas de trabalho do aplicativo local para o Azure sem precisar fazer alterações.
> 
> **Introdução**: Criar uma [VM do Linux](../../virtual-machines/virtual-machines-linux-quick-create-portal.md) ou uma [VM do Windows](../../virtual-machines/virtual-machines-windows-hero-tutorial.md) no portal do Azure.

#### <a name="azure-functions-serverless"></a>Azure Functions (sem servidor)

Em vez de se preocupar em construir e gerenciar um aplicativo inteiro ou a infraestrutura para executar seu código. E se você pudesse apenas escrever seu código e executá-lo em resposta a eventos ou em um agendamento?  [Azure Functions](../../azure-functions/functions-overview.md) é uma oferta de estilo "sem servidor" que permite escrever apenas o código que você precisa. Com o Azure Functions, a execução do código é disparada por solicitações HTTP, webhooks, eventos de serviço de nuvem ou em um agendamento. É possível codificar em sua linguagem de desenvolvimento de preferência, como C\#, F\#, Node.js, Python ou PHP. Com a cobrança baseada no consumo, você paga apenas pelo tempo que seu código é executado, e escalas do Azure, conforme necessário.

> **Quando usar**: Use o Azure Functions quando tiver um código disparado por outros serviços do Azure, por eventos baseados na Web ou com base em um agendamento. Também é possível utilizar o Azure Functions quando não há necessidade de sobrecarga de um projeto hospedado completo ou quando você somente quer pagar o tempo que o código é executado. Para saber mais, consulte [Visão Geral do Azure Functions](../../azure-functions/functions-overview.md).
> 
> **Introdução**: Siga o tutorial de Início Rápido do Functions para [criar sua primeira função](../../azure-functions/functions-create-first-azure-function.md) no portal.
> 
> **Experimentar agora**: O Azure Functions permite que você execute seu código sem precisar se inscrever em uma conta do Azure. Experimente agora em e [crie sua primeira função do Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

O Azure Service Fabric é uma plataforma de sistemas distribuídos que facilita a construção, o pacote, a implantação e o gerenciamento de microsserviços confiáveis e escalonáveis. Ele também fornece recursos abrangentes de gerenciamento de aplicativos para provisionamento, implantação, monitoramento, atualização/aplicação de patch e exclusão de aplicativos implantados. Os aplicativos executados em um pool compartilhado de máquinas podem começar pequenos e escalar para centenas ou milhares de máquinas, conforme necessário.

O Service Fabric dá suporte para WebAPI com Open Web Interface para .NET (OWIN) e ASP.NET Core. Ele fornece SDKs para a construção de serviços no Linux em tanto em .NET Core como Java. Para saber mais sobre o Service Fabric, consulte a documentação do [Service Fabric](https://docs.microsoft.com/azure/service-fabric/).

> **Quando usar:** O Service Fabric é uma boa escolha quando você está criando um aplicativo ou reescrevendo um aplicativo existente para usar uma arquitetura de microsserviço. Utilize o Service Fabric quando precisar de mais controle ou acesso direto à infraestrutura subjacente.
> 
> **Introdução:** [Criar seu primeiro aplicativo do Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Aprimore seus aplicativos com os serviços do Azure

Além de hospedagem de aplicativos, o Azure oferece ofertas de serviços que podem aprimorar a funcionalidade, o desenvolvimento e a manutenção de seus aplicativo, tanto na nuvem quanto no local.

#### <a name="hosted-storage-and-data-access"></a>Armazenamento hospedado e acesso a dados

A maioria dos aplicativos deve armazenar dados, portanto, independentemente de como você decide hospedar o aplicativo no Azure, considere um ou mais dos seguintes serviços de dados e armazenamento.

- **Azure Cosmos DB**: Um serviço de banco de dados multimodelo distribuído globalmente que permite dimensionar de forma elástica a produtividade e o armazenamento em qualquer número de regiões geográficas com um SLA abrangente. 
  > **Quando usar:** Quando o aplicativo precisar de bancos de dados de documento, tabela ou grafo, incluindo bancos de dados do MongoDB, com vários modelos de consistência bem definidos. 
  > 
  > **Introdução**: [Criar um aplicativo Web do Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Se você for um desenvolvedor do MongoDB, consulte [Compilar um aplicativo Web do MongoDB com o Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Armazenamento do Azure**: Oferece armazenamento durável e altamente disponível para blobs, filas, arquivos e outros tipos de dados não relacionais. O armazenamento fornece a base de armazenamento para VMs.

  > **Quando usar**: Quando o aplicativo armazenar dados não relacionais, como pares chave-valor (tabelas), blobs, compartilhamentos de arquivos ou mensagens (filas).
  > 
  > **Introdução**: Escolha um desses tipos de armazenamento: [blobs](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabelas](../../cosmos-db/table-storage-how-to-use-dotnet.md), [filas](../../storage/queues/storage-dotnet-how-to-use-queues.md) ou [arquivos](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Banco de Dados SQL do Azure**: Uma versão baseada no Azure do mecanismo do Microsoft SQL Server para armazenar dados tabulares relacionais na nuvem. O Banco de Dados SQL fornece desempenho previsível, escalabilidade sem tempo de inatividade, continuidade dos negócios e proteção de dados.

  > **Quando usar**: Quando o aplicativo precisar de armazenamento de dados com integridade referencial, suporte transacional e suporte para consultas TSQL.
  > 
  > **Introdução**: [Criar um Banco de Dados SQL em minutos usando o portal do Azure](../../sql-database/sql-database-get-started.md).


É possível utilizar o [Azure Data Factory](../../data-factory/introduction.md) para mover dados locais existentes para o Azure. Caso não esteja pronto para mover dados para a nuvem, as [Conexões Híbridas](../../biztalk-services/integration-hybrid-connection-overview.md) nos Serviços BizTalk permitem que você conecte o aplicativo hospedado no Serviço de Aplicativo para recursos locais. Além disso, é possível conectar-se aos dados do Azure e aos serviços de armazenamento a partir de seus aplicativos locais.

#### <a name="docker-support"></a>Suporte ao Docker

Os contêineres do Docker, uma forma de virtualização de SO, permitem implantar aplicativos de forma mais eficiente e previsível. Um aplicativo em contêineres funciona em produção da mesma maneira que em seus sistemas de desenvolvimento e teste. É possível gerenciar contêineres utilizando ferramentas do Docker padrão. Você pode utilizar suas ferramentas de software livre populares e habilidades existentes para implantar e gerenciar aplicativos baseados em contêineres no Azure.

O Azure fornece várias maneiras de utilizar contêineres em seus aplicativos.

- **Extensão de VM do Docker no Azure**: Permite configurar sua VM com ferramentas do Docker para funcionar como um host do Docker.

  > **Quando usar**: Quando desejar gerar implantações de contêiner consistentes para seus aplicativos em uma VM ou quando desejar usar o [Docker Compose](https://docs.docker.com/compose/overview/).
  > 
  > **Introdução**: [Criar um ambiente do Docker no Azure usando a extensão de VM do Docker](../../virtual-machines/virtual-machines-linux-dockerextension.md).

- **Serviço de Contêiner do Azure**: Permite criar, configurar e gerenciar um cluster de máquinas virtuais pré-configuradas para executar aplicativos em contêineres. Para saber mais sobre o Serviço de Contêiner, consulte  a [Introdução ao Serviço de Contêiner do Azure](../../container-service/container-service-intro.md).

  > **Quando usar**: Quando você precisar criar ambientes escalonáveis prontos para produção que fornecem ferramentas de gerenciamento e agendamento adicionais ou quando estiver implantando um cluster do Docker Swarm.
  > 
  > **Introdução**: [Implantar um cluster do Serviço de Contêiner](../../container-service/dcos-swarm/container-service-deployment.md).

- **Computador do Docker**: Permite instalar e gerenciar um Mecanismo do Docker em hosts virtuais usando comandos docker-machine.

  >**Quando usar**: Quando você precisar desenvolver rapidamente um protótipo de um aplicativo criando um único host do Docker.

- **Imagem personalizada do Docker para o Serviço de Aplicativo**: Permite usar contêineres do Docker em um registro de contêiner ou um contêiner de cliente ao implantar um aplicativo Web no Linux.

  > **Quando usar**: Ao implantar um aplicativo Web no Linux em uma imagem do Docker.
  > 
  > **Introdução**: [Usar uma imagem personalizada do Docker para o Serviço de Aplicativo no Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Authentication

É crucial não apenas saber quem está utilizando seus aplicativos, como também impedir o acesso não autorizado aos seus recursos. O Azure fornece várias maneiras de autenticar os clientes do aplicativo.

- **Azure AD (Azure Active Directory)**: O serviço de gerenciamento de acesso e identidade baseado em nuvem multilocatário da Microsoft. É possível adicionar SSO (login único) aos seus aplicativos integrando com o Azure AD. É possível acessar as propriedades do diretório utilizando diretamente a API do Graph do Azure AD ou a API do Microsoft Graph. É possível integrar com o suporte do Azure AD para a estrutura de autorização OAuth2.0 e Open ID Connect, utilizando os pontos de extremidade HTTP/REST nativos e as bibliotecas multiplataformas de autenticação do Azure AD.

  > **Quando usar**: Quando desejar fornecer uma experiência de SSO, trabalhar com os dados baseados em Grafo ou autenticar usuários baseados em domínio.
  > 
  > **Introdução**: Para saber mais, confira o [Guia do desenvolvedor do Azure Active Directory](../../active-directory/develop/v1-overview.md).

- **Autenticação do Serviço de Aplicativo**: Ao escolher o Serviço de Aplicativo para hospedar seu aplicativo, você também obterá suporte de autenticação interno para o Azure AD, juntamente com provedores de identidade social – incluindo Facebook, Google, Microsoft e Twitter.

  > **Quando usar**: Quando desejar habilitar a autenticação em um aplicativo do Serviço de Aplicativo usando o Azure AD, provedores de identidade social ou ambos.
  > 
  > **Introdução**: Para saber mais sobre a autenticação no Serviço de Aplicativo, confira [Autenticação e autorização no Serviço de Aplicativo do Azure](../../app-service/overview-authentication-authorization.md).

Para saber mais sobre as melhores práticas de segurança no Azure, consulte [Padrões e melhores práticas de segurança do Azure](../../security/security-best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitoramento

Com seu aplicativo instalado e executando no Azure será necessário monitorar o desempenho, inspecionar problemas e observar como os clientes estão utilizando seu aplicativo. O Azure fornece várias opções de monitoramento.

-   **Visual Studio Application Insights**: Um serviço analítico extensível hospedado no Azure integrado ao Visual Studio para monitorar seus aplicativos Web dinâmicos. Ele fornece os dados necessários para aprimorar continuamente o desempenho e a usabilidade de seus aplicativos, sejam eles hospedados no Azure ou não.

    >**Introdução**: Siga o [tutorial do Application Insights](../../azure-monitor/app/app-insights-overview.md).

-   **Azure Monitor**: Um serviço que ajuda você a visualizar, consultar, encaminhar, arquivar e tomar decisões com base nas métricas e nos logs gerados pela infraestrutura e pelos recursos do Azure. O Monitor fornece as exibições de dados que são visualizadas no portal do Azure e é uma fonte única para monitorar os recursos do Azure.
 
    >**Introdução**: [Introdução ao Azure Monitor](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integração de DevOps

Quer seja provisionando VMs ou publicando seus aplicativos Web com integração contínua, o Azure se integra com a maioria das ferramentas de DevOps populares. Com suporte para ferramentas como Jenkins, GitHub, Puppet, Chef, TeamCity, Ansible, Azure DevOps e outras, é possível trabalhar com as ferramentas que você já possui e maximizar sua experiência.

> **Experimentar agora:** [Experimente várias das integrações DevOps](https://azure.microsoft.com/try/devops/).
> 
> **Introdução**: Para ver as opções de DevOps para um aplicativo do Serviço de Aplicativo, confira [Implantação contínua no Serviço de Aplicativo do Azure](../../app-service/deploy-continuous-deployment.md).


## <a name="azure-regions"></a>Regiões do Azure

O Azure é uma plataforma de nuvem global que geralmente está disponível em muitas regiões em todo o mundo. Ao fornecer um serviço, aplicativo ou VM no Azure, você é solicitado a selecionar uma região, que representa um datacenter específico onde seu aplicativo é executado ou onde seus dados são armazenados. Essas regiões correspondem a locais específicos, os quais são publicados na página [Regiões do Azure](https://azure.microsoft.com/regions/).

### <a name="choose-the-best-region-for-your-application-and-data"></a>Escolha a melhor região para seu aplicativo e dados

Um dos benefícios de utilizar o Azure é que você pode implantar seus aplicativos em vários datacenters em todo o mundo. A região que você escolher poderá afetar o desempenho do aplicativo. Por exemplo, é melhor escolher uma região que esteja mais próxima da maioria dos seus clientes para reduzir a latência nas solicitações de rede. Além disso, é possível querer selecionar sua região visando cumprir os requisitos legais para distribuir seu aplicativo em determinados países. Sempre a melhor prática será armazenar os dados do aplicativo no mesmo datacenter ou em um datacenter o mais próximo possível do datacenter que hospeda o aplicativo.

### <a name="multi-region-apps"></a>Aplicativos de várias regiões

Embora seja improvável, não é impossível que todo um datacenter fique offline devido a um evento como um desastre natural ou uma falha na Internet. Uma melhor prática é hospedar aplicativos de negócios vitais em mais de um datacenter para fornecer a máxima disponibilidade. Utilizar várias regiões também pode reduzir a latência para usuários globais e oferecer oportunidades adicionais de flexibilidade ao atualizar aplicativos.

Alguns serviços, como Máquina Virtual e Serviços de Aplicativos utilizam o [Gerenciador de Tráfego do Azure](../../traffic-manager/traffic-manager-overview.md) para habilitar o suporte de várias regiões com failover entre regiões para suportar aplicativos empresariais de alta disponibilidade. Por exemplo, confira [Arquitetura de referência do Azure: Executar um aplicativo Web em várias regiões](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region).

>**Quando usar**: Quando você tiver aplicativos empresariais e de alta disponibilidade que se beneficiam do failover e da replicação.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Como fazer para gerenciar meus aplicativos e projetos?

O Azure fornece um avançado conjunto de experiências para você criar e gerenciar seus projetos, aplicativos e recursos do Azure — tanto programaticamente, quanto no [portal do Azure](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Interfaces de linha de comando e PowerShell

O Azure fornece duas maneiras de gerenciar seus aplicativos e serviços a partir da linha de comando utilizando Bash, Terminal, o prompt de comando ou sua ferramenta de linha de comando de preferência. Normalmente, é possível executar as mesmas tarefas a partir da linha de comando do portal do Azure — como criar e configurar máquinas virtuais, redes virtuais, aplicativos Web e outros serviços.

-   [CLI (interface de linha de comando) do Azure](../../xplat-cli-install.md): Permite conectar-se a uma assinatura do Azure e programar várias tarefas nos recursos do Azure por meio da linha de comando.

-   [Azure PowerShell](../../powershell-install-configure.md): Fornece um conjunto de módulos com cmdlets que permitem gerenciar recursos do Azure usando o Windows PowerShell.

### <a name="azure-portal"></a>Portal do Azure

O portal do Azure é um aplicativo baseado na Web que você pode utilizar para criar, gerenciar e remover recursos e serviços do Azure. O portal do Azure está localizado em <https://portal.azure.com>. Inclui um painel personalizável, ferramentas para gerenciar recursos do Azure e acesso às configurações de assinatura e informações de cobrança. Para obter mais informações, consulte a [Visão geral do portal do Azure](../../azure-portal-overview.md).

### <a name="rest-apis"></a>APIs REST

O Azure é desenvolvido em um conjunto de APIs REST que suportam a interface do usuário do Portal do Azure. A maioria dessas APIs REST também é suportada para permitir que você programa e gerencie seus recursos e aplicativos do Azure a partir de qualquer dispositivo habilitado para Internet. Para o conjunto completo da documentação de API REST, consulte a [Referência de SDK do REST do Azure](https://docs.microsoft.com/rest/api/).

### <a name="apis"></a>APIs

Além das APIs REST, muitos serviços do Azure também permitem que você gerencie recursos de seus aplicativos utilizando SDKs do Azure específicos da plataforma, incluindo SDKs para as seguintes plataformas de desenvolvimento:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/javascript/azure)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](https://docs.microsoft.com/python/azure)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/go/azure)

Serviços como [Aplicativos Móveis](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) e [Serviços de Mídia do Azure](../../media-services/previous/media-services-dotnet-how-to-use.md) fornecem SDKs do lado do cliente para permitir que você acesse serviços dos aplicativos Web e clientes móveis.

### <a name="azure-resource-manager"></a>Azure Resource Manager 
    
A execução do seu aplicativo no Azure provavelmente envolve o trabalho com vários serviços do Azure, todos os quais seguem o mesmo ciclo de vida e podem ser pensados como uma unidade lógica. Por exemplo, um aplicativo Web pode usar Aplicativos Web, Banco de Dados SQL, Armazenamento, Cache Redis do Azure e serviços de Rede de Distribuição de Conteúdo do Azure. O [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) permite que você trabalhe com os recursos em seu aplicativo como um grupo. É possível implantar, atualizar ou excluir todos os recursos em uma operação coordenada única.

Além de agrupar e gerenciar logicamente recursos relacionados, o Azure Resource Manager inclui capacidades de implantação que permitem personalizar a implantação e configuração de recursos relacionados. Por exemplo, utilizando o Resource Manager é possível implantar e configurar um aplicativo que consiste em máquinas virtuais múltiplas, um balanceador de carga e um Banco de Dados SQL do Azure como uma unidade única.

Essas implantações são desenvolvidas utilizando um modelo do Azure Resource Manager, que é um documento no formato JSON. Os modelos permitem que você defina uma implantação e gerencie seus aplicativos utilizando modelos declarativos, em vez de scripts. Seus modelos podem funcionar para diferentes ambientes, como teste, de preparo e produção. Por exemplo, utilizando modelos, você pode adicionar um botão para um repositório GitHub que implanta o código no repositório para um conjunto de serviços do Azure com um único clique.

> **Quando usar**: Use modelos do Resource Manager quando desejar obter uma implantação baseada em modelo para seu aplicativo, de modo que você possa gerenciar de forma programática usando APIs REST, a CLI do Azure e o Azure PowerShell.
> 
> **Introdução**: Para obter uma introdução ao uso de modelos, confira [Criando modelos do Azure Resource Manager](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Compreendendo contas, assinaturas e cobrança

Como desenvolvedores, gostamos de mergulhar diretamente no código e tentar começar o mais rápido possível, fazendo com que nossos aplicativos sejam executados. Nós, certamente, queremos incentivá-lo a começar a trabalhar no Azure da maneira mais fácil possível. Para torná-lo mais fácil, o Azure oferece uma [avaliação gratuita](https://azure.microsoft.com/free/). Alguns serviços ainda possuem uma funcionalidade "Experimente gratuitamente", como o [Serviço de Aplicativo do Azure](https://tryappservice.azure.com/) que não exige a criação de uma conta. Tanto quanto é divertido mergulhar na codificação e implantação do seu aplicativo no Azure, tanto como é importante empregar algum tempo para compreender como o Azure funciona do ponto de vista das contas de usuários, assinaturas e cobrança.

### <a name="what-is-an-azure-account"></a>O que é uma conta do Azure?

Para poder criar ou trabalhar com uma assinatura do Azure é necessário possuir uma conta do Azure. Uma conta do Azure é simplesmente uma identidade no Azure AD ou em um diretório, como uma organização corporativa ou de estudante, que é confiável pelo Azure AD. Caso não pertença a essa organização, sempre será possível criar uma assinatura utilizando sua Conta da Microsoft, que é confiável pelo Azure AD. Para saber mais sobre como integrar o Windows Server Active Directory local com o Azure AD, consulte [Integração de suas identidades locais com o Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Cada assinatura do Azure tem uma relação de confiança com uma instância do Azure AD. Isso significa que ela confia que esse diretório autentique usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório, mas uma única assinatura confia em apenas um diretório. Para saber mais, consulte [Como as assinaturas do Azure são associadas ao Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Além de definir identidades de conta do Azure individuais, chamadas de *usuários*, também é possível definir *grupos* no Azure AD. Criar grupos de usuários é uma boa maneira de gerenciar o acesso aos recursos em uma assinatura utilizando o controle de acesso baseado em função (RBAC). Para saber como criar grupos, consulte [Criar um grupo na versão prévia do Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Também é possível criar e gerenciar grupos [utilizando o PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Gerencie suas assinaturas

Uma assinatura é um agrupamento de serviços do Azure que está vinculada a uma conta do Azure. Uma única conta do Azure pode conter várias assinaturas. A cobrança dos serviços do Azure é feita por assinatura. Para obter uma lista das ofertas de assinatura disponíveis por tipo, consulte os [Detalhes da oferta do Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). As assinaturas do Azure têm um Administrador da Conta (que tem controle total sobre a assinatura) e um Administrador de Serviços (que tem controle sobre todos os serviços na assinatura). Para obter informações sobre os administradores clássicos de assinatura, confira [Adicionar ou alterar administradores de assinatura do Azure](../../billing/billing-add-change-azure-subscription-administrator.md). Além dos administradores, é possível conceder controle detalhado de recursos do Azure usando [RBAC (Controle de Acesso Baseado em Função)](../../role-based-access-control/overview.md) a contas individuais.

#### <a name="resource-groups"></a>Grupos de recursos

Ao fornecer novos serviços do Azure, isso é feito em uma determinada assinatura. Os serviços do Azure individuais, que também são chamados de recursos, são criados no contexto de um grupo de recursos. Os grupos de recursos facilitam a implantação e o gerenciamento dos recursos de seu aplicativo. Um grupo de recursos deve conter todos os recursos para o aplicativo que você quer trabalhar como uma unidade. É possível mover recursos entre grupos de recursos e até diferentes assinaturas. Para saber mais sobre como mover recursos, consulte [Mover recursos para o novo grupo de recursos ou assinatura](../../resource-group-move-resources.md).

O Azure Resource Explorer é uma ótima ferramenta para visualizar os recursos já criados em sua assinatura. Para saber mais, consulte [Utilizar o Azure Resource Explorer para visualizar e modificar recursos](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Conceder acesso aos recursos

Ao permitir acesso aos recursos do Azure, a melhor prática sempre será fornecer aos usuários o privilégio mínimo necessário para executar uma determinada tarefa.

- **RBAC (controle de acesso baseado em função)**: No Azure, é possível permitir acesso a contas de usuários (entidades de segurança) em um escopo especificado: assinatura, grupo de recursos ou recursos individuais. O RBAC permite implantar um conjunto de recursos em um grupo de recursos e conceder permissões a um usuário ou grupo específico. Ele também permite limitar o acesso aos recursos que pertencem ao grupo de recursos de destino. Além disso, é possível conceder acesso a um recurso único, como uma máquina virtual ou uma rede virtual. Para conceder acesso, você atribui uma função ao usuário, ao grupo ou à entidade de serviço. Há muitas funções predefinidas, no entanto, você também pode definir suas próprias funções personalizadas. Para saber mais, confira [O que é RBAC (controle de acesso baseado em função)?](../../role-based-access-control/overview.md).

  > **Quando usar**: Quando você precisar de gerenciamento de acesso refinado para usuários e grupos ou quando precisar transformar um usuário em proprietário de uma assinatura.
  > 
  > **Introdução**: Para saber mais, confira [Gerenciar o acesso usando o RBAC e o portal do Azure](../../role-based-access-control/role-assignments-portal.md).

- **Objetos da entidade de serviço**: Além de fornecer acesso a entidades de usuários e grupos, é possível conceder o mesmo acesso a uma entidade de serviço.

  > **Quando usar**: Ao gerenciar de forma programática os recursos do Azure ou conceder acesso para aplicativos. Para obter mais informações, consulte [Criar entidade de serviço e aplicativo do Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Marcas

O Azure Resource Manager permite atribuir marcas personalizadas para recursos individuais. As marcas, que são pares chave-valor, podem ser úteis quando for necessário organizar recursos para cobrança ou monitoramento. As marcas fornecem uma maneira de rastrear recursos em vários grupos de recursos. É possível atribuir marcas no portal, no modelo do Azure Resource Manager ou programaticamente utilizando a API REST, a CLI do Azure ou o PowerShell. Além disso, é possível atribuir várias marcas para cada recurso. Para saber mais, consulte [Usando marcas para organizar os recursos do Azure](../../resource-group-using-tags.md).

### <a name="billing"></a>Cobrança

Na mudança da computação local para serviços hospedados na nuvem, acompanhar e estimar o uso do serviço e os custos relacionados são preocupações significativas. É importante poder estimar o custo dos novos recursos para serem executados mensalmente. Também é necessário projetar como a cobrança procura um determinado mês baseado nos gastos atuais.

#### <a name="get-resource-usage-data"></a>Obter dados de uso do recurso

O Azure fornece um conjunto de API REST de cobrança que fornecem acesso ao consumo do recurso e informações de metadados para assinaturas do Azure. Essas APIs de cobrança fornecem-lhe a capacidade de prever melhor e gerenciar os custos do Azure. É possível acompanhar e analisar os gastos em incrementos por hora, criar alertas de gastos e prever a cobrança futura com base nas tendências de uso atuais.

>**Introdução**: Para saber mais sobre como usar as APIs de Cobrança, confira [Visão geral das APIs de Uso de Cobrança e RateCard do Azure](../../billing-usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Prever os custos futuros

Embora seja um desafio estimar custos antecipadamente, o Azure possui uma [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) que pode ser utilizada ao estimar os custos dos recursos implantados. Você também pode utilizar a folha de Cobrança no portal e as APIs REST de Cobrança para estimar os custos futuros baseado no consumo atual.

>**Introdução**: Confira [Visão geral das APIs de Uso de Cobrança e RateCard do Azure](../../billing-usage-rate-card-overview.md).
