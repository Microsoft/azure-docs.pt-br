---
title: "Comparação do Serviço de Aplicativo, Máquinas Virtuais, Service Fabric e Serviços de Nuvem do Azure | Microsoft Docs"
description: "Saiba como escolher entre o Serviço de Aplicativo, as Máquinas Virtuais, o Service Fabric e os Serviços de Nuvem do Azure para hospedar aplicativos Web."
services: app-service\web, virtual-machines, cloud-services
documentationcenter: 
author: ggailey777
manager: erikre
editor: jimbe
ms.assetid: 7d346a23-532a-42a9-98a8-23b7286d32a8
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/07/2016
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: 3fca6023fedcfc64ecfe66c0f4ed60a495953cfb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-virtual-machines-service-fabric-and-cloud-services-comparison"></a>Comparação de Serviço de Aplicativo, Máquinas Virtuais, Service Fabric e Serviços de Nuvem do Azure
## <a name="overview"></a>Visão geral
O Azure oferece várias maneiras de hospedar sites da Web: [Serviço de Aplicativo do Azure][Azure App Service], [Máquinas Virtuais][Virtual Machines], [Service Fabric][Service Fabric] e [Serviços de Nuvem][Cloud Services]. Este artigo ajuda você a entender as opções e fazer a escolha certa para seu aplicativo Web.

O Serviço de Aplicativo do Azure é a melhor opção para a maioria dos aplicativos Web. A implantação e o gerenciamento estão integrados na plataforma, os sites podem ser dimensionados rapidamente para suportar altas cargas de tráfego e o gerenciador de balanceamento de carga e tráfego integrado oferece alta disponibilidade. Você pode mover sites existentes para o Serviço de Aplicativo do Azure facilmente com uma [ferramenta de migração online](https://www.migratetoazure.net/), usar um aplicativo de software livre da Galeria de Aplicativos Web ou criar um novo site usando a estrutura e as ferramentas de sua escolha. O recurso [WebJobs][WebJobs] facilita a adição do processamento de trabalhos em segundo plano ao seu aplicativo Web do Serviço de Aplicativo.

O Service Fabric será uma boa opção se você estiver criando um novo aplicativo ou reescrevendo um existente para usar uma arquitetura de microsserviço. Os aplicativos, que são executados em um pool compartilhado de computadores, podem começar pequenos e aumentar para uma escala massiva de centenas ou milhares de computadores, conforme a necessidade. Os serviços com estado facilitam o armazenamento consistente e confiável do estado do aplicativo, e o Service Fabric gerencia automaticamente o particionamento do serviço, o dimensionamento e a disponibilidade para você.  O Service Fabric também permite a API Web com OWIN (Open Web Interface for .NET) e ASP.NET Core.  Em comparação com o Serviço de Aplicativo, o Service Fabric também fornece mais controle sobre a infraestrutura subjacente, ou acesso direto a ela. Você pode acessar remotamente seus servidores ou configurar as tarefas de inicialização do servidor. Os Serviços de Nuvem são semelhantes ao Service Fabric no nível de controle vs. facilidade de uso, mas como agora se trata de um serviço herdado, o Service Fabric é recomendado para novo desenvolvimento.

Caso tenha um aplicativo que precise de modificações substanciais para ser executado no Serviço de Aplicativo ou no Service Fabric, você pode escolher as Máquinas Virtuais para simplificar a migração para a nuvem. No entanto, configurar, proteger e manter corretamente as VMs requer muito mais tempo e conhecimento em TI em comparação com o Serviço de Aplicativo do Azure e o Service Fabric. Se estiver considerando as Máquinas Virtuais do Azure, assegure-se de levar em conta o contínuo esforço de manutenção necessário para corrigir, atualizar e gerenciar seu ambiente de máquinas virtuais. Máquinas Virtuais do Azure são IaaS (Infraestrutura como serviço), enquanto o Serviço de Aplicativo e o Service Fabric são Paas (plataforma como serviço). 

## <a name="features"></a>Comparação de Recursos
A tabela a seguir compara os recursos do Serviço de Aplicativo, Serviços de Nuvem, Máquinas Virtuais e Service Fabric para ajudar você a fazer a melhor escolha. Para obter as informações mais recentes sobre SLA para cada opção, consulte os [Acordos de Nível de Serviço do Azure](https://azure.microsoft.com/support/legal/sla/).

| Recurso | Serviço de Aplicativo (aplicativos Web) | Serviços de nuvem (funções Web) | Máquinas Virtuais | Service Fabric | Observações |
| --- | --- | --- | --- | --- | --- |
| Implantação quase instantânea |X | | |X |Implantar um aplicativo ou uma atualização de um aplicativo em um Serviço de Nuvem ou criar uma Máquina Virtual leva no mínimo alguns minutos; implantar um aplicativo em um aplicativo Web leva segundos. |
| Dimensionar para máquinas maiores sem reimplantação |X | | |X | |
| Instâncias do servidor da Web compartilham conteúdos e configuração, o que significa que você não precisa implantar ou configurar novamente conforme realiza o dimensionamento. |X | | |X | |
| Vários ambientes de implantação (produção e preparo) |X |X | |X |O Service Fabric permite ter vários ambientes para seus aplicativos ou implantar diferentes versões do seu aplicativo lado a lado. |
| Gerenciamento de atualização automática do sistema operacional |X |X | | |Atualizações automáticas do sistema operacional estão planejadas para uma futura versão do Service Fabric. |
| Alternância ininterrupta entre plataformas (alterne facilmente entre 32 bits e 64 bits) |X |X | | | |
| Implantar código com GIT, FTP |X | |X | | |
| Implantar o código com a implantação da Web |X | |X | |Os Serviços de Nuvem oferecem suporte ao uso da Implantação da Web para implantar atualizações em instâncias de função individuais. No entanto, eles não podem ser usados para a implantação inicial de uma função, e se a Implantação da Web for usada para uma atualização, você precisará implantar separadamente em cada instância de uma função. Várias instâncias são necessárias para se qualificar para SLA de Serviço de Nuvem para ambientes de produção. |
| Suporte do WebMatrix |X | |X | | |
| Acesso a serviços como o Barramento de Serviço, Armazenamento, Banco de Dados SQL |X |X |X |X | |
| Camada de serviços Web ou Web hospedada de uma arquitetura multicamada |X |X |X |X | |
| Camada intermediária de host de uma arquitetura multicamada |X |X |X |X |Os aplicativo Web do Serviço de Aplicativo podem hospedar facilmente uma camada média da API REST, e o recurso [WebJobs](http://go.microsoft.com/fwlink/?linkid=390226) pode hospedar tarefas de processamento em segundo plano. Você pode executar o WebJobs em um site dedicado para alcançar a escalabilidade independente para a camada. |
| Suporte integrado do MySQL como serviço |X |X |X | |Os Serviços de Nuvem podem integrar o MySQL como serviço por meio de ofertas do ClearDB, mas não como parte do fluxo de trabalho do Portal do Azure. |
| Suporte para ASP.NET, classic ASP, Node.js, PHP, Python |X |X |X |X |O Service Fabric oferece suporte à criação de um front-end da Web o usando [ASP.NET 5](../service-fabric/service-fabric-add-a-web-frontend.md) ou você pode implantar qualquer tipo de aplicativo (Node.js, Java etc.) como um [executável convidado](../service-fabric/service-fabric-deploy-existing-app.md). |
| Dimensionamento para várias instâncias sem reimplantação |X |X |X |X |Máquinas Virtuais podem ser dimensionadas para várias instâncias, mas os serviços em execução nessas máquinas devem ser escritos para lidar com este dimensionamento. Você precisa configurar um balanceador de carga para rotear solicitações entre máquinas e criar um Grupo de Afinidade para impedir reinícios simultâneos de todas as instâncias devido a manutenções ou falhas de hardware. |
| Suporte para SSL |X |X |X |X |Para aplicativos Web do Serviço de Aplicativo, o SSL para nomes de domínio personalizados só tem suporte no modo Básico e Padrão. Para obter informações sobre como usar o SSL com os aplicativos Web, consulte [Configurar um certificado SSL para um site do Azure](app-service-web-tutorial-custom-ssl.md). |
| Integração do Visual Studio |X |X |X |X | |
| Depuração Remota |X |X |X | | |
| Implantar código com TFS |X |X |X |X | |
| Isolamento de rede com a [Rede Virtual do Azure](/azure/virtual-network/) |X |X |X |X |Consulte também [Integração de Rede Virtual dos Websites do Azure](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) |
| Suporte a [Gerenciador de Tráfego do Azure](/azure/traffic-manager/) |X |X |X |X | |
| Monitoramento do ponto de extremidade integrado |X |X |X | | |
| Acesso remoto a área de trabalho para servidores | |X |X |X | |
| Instalação de qualquer MSI personalizado | |X |X |X |O Service Fabric permite hospedar qualquer arquivo executável como um [executável convidado](../service-fabric/service-fabric-deploy-existing-app.md) , ou você pode instalar qualquer aplicativo nas VMs. |
| Capacidade de definir/executar tarefas de inicialização | |X |X |X | |
| Capacidade de ouvir eventos de ETW | |X |X |X | |

## <a name="scenarios"></a>Cenários e recomendações
Abaixo estão alguns cenários de aplicação comuns com recomendações sobre qual opção de hospedagem na Web do Azure pode ser a mais adequada para cada um deles.

* [Preciso de um front-end da Web com processamento em segundo plano e back-end de banco de dados para executar aplicativos de negócios integrados a ativos locais.](#onprem)
* [Preciso de uma maneira confiável de hospedar meu site corporativo que seja bem escalada e ofereça alcance global.](#corp)
* [Tenho um aplicativo IIS6 em execução no Windows Server 2003.](#iis6)
* [Eu sou um pequeno empresário e preciso de uma maneira barata de hospedar meu site, mas com crescimento futuro em mente.](#smallbusiness)
* [Sou um designer gráfico ou Web designer e desejo projetar e criar sites para meus clientes.](#designer)
* [Eu estou migrando o meu aplicativo multicamadas com um front-end da Web para a nuvem.](#multitier)
* [Meu aplicativo depende de ambientes do Windows ou Linux altamente personalizados e quero movê-lo para a nuvem.](#custom)
* [O meu site usa software livre e quero hospedá-lo no Azure.](#oss)
* [Eu tenho um aplicativo de linha de negócios que precisa se conectar à rede corporativa.](#lob)
* [Desejo hospedar uma API REST ou um serviço Web para clientes móveis.](#mobile)

### <a id="onprem"></a> Preciso de um front-end da Web com processamento em segundo plano e back-end de banco de dados para executar aplicativos de negócios integrados a ativos locais.
Os Serviço de Aplicativo do Azure é uma ótima solução para aplicativos de negócios complexos. Eles permitem desenvolver aplicativos que são escalados automaticamente em uma plataforma com carga equilibrada, são protegidos pelo Active Directory e se conectam aos seus recursos no local. Eles facilitam o gerenciamento desses aplicativos por meio de um portal e APIs de nível mundial, e permitem que você obtenha informações sobre como os clientes estão os utilizando com ferramentas de informações sobre os aplicativos. O recurso [Webjobs][Webjobs] permite executar processos e tarefas em segundo plano como parte de sua camada da Web, ao passo que a conectividade híbrida e os recursos de VNET facilitam a conexão com os recursos locais. O Serviço de Aplicativo do Azure fornece SLA três noves para aplicativos Web e permite que você:

* Execute seus aplicativos de maneira confiável em uma plataforma de nuvem de autorrecuperação e autocorreção.
* Escale automaticamente em uma rede global de datacenters.
* Faça backup e restaurações para recuperação de desastres.
* Esteja de acordo com os padrões ISO, SOC2 e PCI.
* Integre-se com o Active Directory

### <a id="corp"></a> Preciso de uma maneira confiável de hospedar meu site corporativo que seja bem escalada e ofereça alcance global.
O Serviço de Aplicativo do Azure é uma ótima solução para hospedar sites corporativos. Ele permite que os aplicativos Web tenham a escala ajustada rápida e facilmente para atender à demanda em uma rede global de datacenters. Ele oferece alcance local, tolerância a falhas e gerenciamento de tráfego inteligente. Tudo em uma plataforma que oferece ferramentas de gerenciamento de nível mundial, permitindo que você obtenha informações sobre o funcionamento e o tráfego do site de forma rápida e fácil. O Serviço de Aplicativo do Azure fornece SLA três noves para aplicativos Web e permite que você:

* Execute seus sites de maneira confiável em uma plataforma de nuvem de autorrecuperação e autocorreção.
* Escale automaticamente em uma rede global de datacenters.
* Faça backup e restaurações para recuperação de desastres.
* Gerencie logs e o tráfego com ferramentas integradas.
* Esteja de acordo com os padrões ISO, SOC2 e PCI.
* Integre-se com o Active Directory

### <a id="iis6"></a> Tenho um aplicativo IIS6 em execução no Windows Server 2003.
O Serviço de Aplicativo do Azure torna fácil evitar os custos com infraestrutura associados à migração de aplicativos IIS6 mais antigos. A Microsoft criou [ferramentas de migração de fácil uso e instruções de migração detalhadas](https://www.movemetowebsites.net/) que permitem que você verifique a compatibilidade e identifique mudanças que precisam ser feitas. A integração com Visual Studio, TFS e ferramentas de CMS comuns torna mais fácil implantar aplicativos IIS6 diretamente na nuvem. Uma vez implantado, o Portal do Azure oferece ferramentas de gerenciamento robustas que permitem reduzir verticalmente para gerenciar os custos e atender à demanda conforme o necessário. Com a ferramenta de migração, você pode:

* Migrar rápida e facilmente seu aplicativo Web do Windows Server 2003 legado para a nuvem.
* Escolha deixar seu banco de dados SQL conectado localmente para criar um aplicativo híbrido.
* Mova automaticamente seu banco de dados SQL juntamente com o aplicativo legado.

### <a id="smallbusiness"></a>Eu sou um pequeno empresário e preciso de uma maneira barata de hospedar meu site, mas com crescimento futuro em mente.
O Serviço de Aplicativo do Azure é uma ótima solução para esse cenário, pois você pode começar a usá-lo gratuitamente e, em seguida, adicionar mais recursos quando precisar. Cada aplicativo Web gratuito vem com um domínio fornecido pelo Azure (*sua_empresa*.azurewebsites.net) e a plataforma inclui ferramentas de implantação e gerenciamento integradas, bem como uma galeria de aplicativos que facilitam começar. Há muitos outros serviços e opções de dimensionamento que permite que o site evolua com maior demanda do usuário. Com o Serviço de Aplicativo do Azure, você pode:

* Começar com a camada livre e, em seguida, dimensionar conforme necessário.
* Use a Galeria de Aplicativos para a rápida criação de aplicativos Web populares, como o WordPress.
* Adicione mais recursos e serviços do Azure para seu aplicativo conforme necessário.
* Proteja seu aplicativo web com HTTPS.

### <a id="designer"></a> Sou um designer gráfico ou Web designer e desejo projetar e criar sites para meus clientes
Para desenvolvedores da Web e Web designers, o Serviço de Aplicativo do Azure se integra facilmente com uma variedade de estruturas e ferramentas, inclui suporte à implantação para Git e FTP e oferece uma integração coesa com ferramentas e serviços como Visual Studio e Banco de dados SQL. Com o Serviço de Aplicativo, você pode:

* Use as ferramentas de linha de comando para [tarefas automatizadas][scripting].
* Trabalhe com linguagens populares como [.Net][dotnet], [PHP][PHP], [Node.js][nodejs] e [Python][Python].
* Selecione três níveis diferentes de dimensionamento para aumentar proporcionalmente capacidades muito altas.
* Integração com outros serviços do Azure, como [Banco de Dados SQL][sqldatabase], [Barramento de Serviço][servicebus] e [Armazenamento][Storage] ou ofertas de parceiros do [Azure Store][azurestore], como MySQL and MongoDB.
* Integração com ferramentas como o Visual Studio, Git, WebMatrix, WebDeploy, TFS e FTP.

### <a id="multitier"></a>Eu estou migrando o meu aplicativo multicamada com um front-end da Web para a nuvem
Se você estiver executando um aplicativo multicamada, como um servidor Web que se comunica com um banco de dados, o Serviço de Aplicativo do Azure é uma boa opção que oferece uma integração coesa com o Banco de Dados SQL do Azure. E você pode usar o novo recurso WebJobs para executar processos de back-end.

Escolha Service Fabric para uma ou mais de suas camadas se precisar de mais controle sobre o ambiente do servidor, como a capacidade de acessar remotamente seu servidor ou configurar as tarefas de inicialização do servidor.

Escolha Máquinas Virtuais para uma ou mais de suas camadas se quiser usar sua própria imagem de máquina ou executar o software do servidor ou serviços que não podem ser configurados no Service Fabric.

### <a id="custom"></a>Meu aplicativo depende de ambientes do Windows ou Linux altamente personalizados e quero movê-lo para a nuvem.
Se seu aplicativo requer instalação complexa ou configuração do software e do sistema operacional, máquinas virtuais é provavelmente a melhor solução. Com Máquinas Virtuais, você pode:

* Usar a Galeria de Máquina Virtual para iniciar com um sistema operacional, como Windows ou Linux, e personalizar para seus requisitos de aplicativo.
* Criar e carregar uma imagem personalizada de um servidor local existente para ser executado em uma máquina virtual no Azure.

### <a id="oss"></a>Meu site usa software livre e quero hospedá-lo no Azure
Se sua estrutura de software livre tiver suporte no Serviço de Aplicativo, as linguagens e estruturas de que seu aplicativo precisa serão configuradas automaticamente. O Serviço de Aplicativo permite que você:

* Use várias linguagens de software livre populares como [.NET][dotnet], [PHP][PHP], [Node.js][nodejs] e [Python][Python].
* Instale o WordPress, Drupal, Umbraco, DNN e muitos outros aplicativos Web de terceiros.
* Migre um aplicativo existente ou crie um novo aplicativo na Galeria de Aplicativos.

Se sua estrutura de software livre não tiver suporte no Serviço de Aplicativo, você pode executá-la em uma das outras opções de hospedagem na Web do Azure. Com as Máquinas Virtuais, você instala e configura o software na imagem da máquina, que pode ser baseada em Windows ou Linux.

### <a id="lob"></a>Eu tenho um aplicativo de linha de negócios que precisa se conectar à rede corporativa
Se você deseja criar um aplicativo de linha de negócios, seu site pode exigir acesso direto a serviços ou dados da rede corporativa. Isso é possível no Serviço de Aplicativo, no Service Fabric e nas Máquinas Virtuais usando o [serviço de Rede Virtual do Azure](/azure/virtual-network/). No Serviço de Aplicativo, você pode usar o [recurso de integração do VNET](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/), que permite que seus aplicativos Azure sejam executados como se estivessem em sua rede corporativa.

### <a id="mobile"></a>Desejo hospedar uma API REST ou um serviço Web para clientes móveis
Serviços Web baseados em HTTP permitem que você ofereça suporte a uma ampla variedade de clientes, inclusive clientes móveis. Estruturas como a API da Web do ASP.NET são integradas com o Visual Studio para fazer com que seja mais fácil criar e consumir serviços REST.  Esses serviços são expostos a partir de um ponto de extremidade da Web, portanto, é possível usar qualquer técnica de hospedagem na Web no Azure para dar suporte a esse cenário. No entanto, o Serviço de Aplicativo é uma ótima opção para hospedar APIs REST. Com o Serviço de Aplicativo, você pode:

* Crie rapidamente um [aplicativo móvel](../app-service-mobile/app-service-mobile-value-prop.md) ou aplicativo de API para hospedar o serviço da Web HTTP em dos datacenters distribuídos globalmente do Azure.
* Migrar serviços existentes ou criar novos.
* Obter o SLA de disponibilidade com uma única instância ou dimensionar para várias máquinas exclusivas.
* Use o site publicado para fornecer APIs REST para quaisquer clientes HTTP, incluindo clientes móveis.

> [!NOTE]
> Se você quiser começar a usar o Serviço de Aplicativo do Azure antes de se inscrever para uma conta, acesse <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, em que é possível criar imediatamente um aplicativo de iniciante de curta duração no Serviço de Aplicativo do Azure gratuitamente. Nenhum cartão de crédito é exigido, sem compromissos.
> 
> 

## <a id="nextsteps"></a> Próximas etapas
Para saber mais sobre as três opções de hospedagem da Web, confira [Introdução ao Azure](../fundamentals-introduction-to-azure.md).

Para se familiarizar com as opções escolhidas para seu aplicativo, consulte os seguintes recursos:

* [Serviço de Aplicativo do Azure](/azure/app-service/)
* [Serviços de nuvem do Azure](/azure/cloud-services/)
* [Máquinas Virtuais do Azure](/azure/virtual-machines/)
* [Service Fabric](/azure/service-fabric/)

<!-- URL List -->

[Azure App Service]: /azure/app-service/
[Cloud Services]: /azure/cloud-services/
[Virtual Machines]: /azure/virtual-machines/
[Service Fabric]: /azure/service-fabric/
[ClearDB]: http://www.cleardb.com/
[WebJobs]: http://go.microsoft.com/fwlink/?linkid=390226&clcid=0x409
[Configuring an SSL certificate for an Azure Website]: app-service-web-tutorial-custom-ssl.md
[azurestore]: https://azuremarketplace.microsoft.com/en-us/marketplace/apps
[scripting]: https://azure.microsoft.com/documentation/scripts/?services=web-sites
[dotnet]: https://azure.microsoft.com/develop/net/
[nodejs]: https://azure.microsoft.com/develop/nodejs/
[PHP]: https://azure.microsoft.com/develop/php/
[Python]: https://azure.microsoft.com/develop/python/
[servicebus]: /azure/service-bus/
[sqldatabase]: /azure/sql-database/
[Storage]: /azure/storage/

<!-- IMG List -->

[ChoicesDiagram]: ./media/choose-web-site-cloud-service-vm/Websites_CloudServices_VMs_3.png
