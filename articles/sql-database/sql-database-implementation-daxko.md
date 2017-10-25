---
title: Estudo de caso sobre o Banco de Dados SQL do Azure - Daxko/CSI | Microsoft Docs
description: "Saiba mais sobre como a Daxko/CSI usa o Banco de Dados SQL para acelerar o ciclo de desenvolvimento e aprimorar os serviços de atendimento ao cliente e o desempenho"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 00c8a713-f20c-4d6b-b8b7-0c1b9ba5f05b
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 3e43494721f03ce8e56cb069638ad24df7222386
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI usou o Azure para acelerar o ciclo de desenvolvimento e aprimorar o serviço de atendimento ao cliente e o desempenho
![Logotipo da CSI/Daxko](./media/sql-database-implementation-daxko/csidaxkologo25.png)

A Daxko/CSI Software enfrentou um desafio: sua base de clientes dos centros de bem-estar e recreação foi aumentando rapidamente, graças ao sucesso de sua abrangente solução de software corporativo, mas manter as necessidade de infraestrutura de TI para esse aumento da base de clientes foi uma prova para a equipe de TI da empresa. A empresa era cada vez mais restringida pela sobrecarga de operações crescentes, especialmente pelo gerenciamento dos bancos de dados em expansão. Pior ainda, essa sobrecarga de operações foi minando os recursos de desenvolvimento para novas iniciativas, como novos recursos de mobilidade para o software da empresa.

De acordo com David Molina, diretor de desenvolvimento de produtos da Daxko/CSI, o Azure forneceu à CSI Software o modelo de PaaS (plataforma como serviço), necessário para simplificar o gerenciamento de banco de dados, aumentar a escalabilidade e liberar recursos para concentração no software, e não em operações. "O Banco de Dados SQL do Azure foi uma ótima opção para nós. Não ter que se preocupar com a manutenção de um SQL Server, um cluster de failover e todas as necessidades de infraestrutura foi o ideal para nós."

Desde a migração para o Azure, a CSI Software precisa apenas de duas pessoas na equipe de operações para gerenciar mais de 600 bancos de dados de clientes. A empresa usa pools elásticos do Banco de Dados SQL do Azure para mover bancos de dados de clientes com base no tamanho e na necessidade.

Molina acrescenta, "Nossos clientes sentiram a mudança imediatamente. Antes dos pools elásticos, de vez em quando eles tinham tempos limite e outros problemas durante os períodos de intermitência. Com os pools elásticos do Azure, eles podem agir conforme a necessidade e usar o software sem problemas".

Além de melhorar o desempenho para os clientes, os pools elásticos do Azure liberaram o pessoal da CSI Software para focar no desenvolvimento de novos serviços e recursos, em vez de lidar com operações e gerenciamento. Esses recursos de TI ajudaram a CSI Software a aprimorar a oferta de software corporativo, SpectrumNG, para ajudar a atrair membros de academia, melhorar a eficiência da equipe e dar à equipe e aos membros acesso móvel para tarefas interativas e notificações em tempo real.

O Azure também ajudou a CSI Software a acelerar e melhorar o ciclo de desenvolvimento e QA (garantia de qualidade) habilitando opções de automação. Com a implementação do Azure na empresa, os gerentes de compilação podem empacotar componentes com o clique de um botão. Como Molina descreve, "Como parte do ciclo de liberação, o QA agora é capaz de implantar em um ambiente de teste no Azure, imitando de modo bem próximo nossa pilha de produção. Podemos implantar compilações imediatamente em nosso ambiente de desenvolvimento para verificar as alterações. Uma grande vitória para nós, pois não tínhamos paridade para testar antes disso".

## <a name="offloading-to-the-cloud"></a>Descarregando na nuvem
Antes de passar para a nuvem, a CSI Software tinha criado com sucesso sua própria infraestrutura multilocatária em um datacenter local em Houston. À medida que a empresa expandia, ela enfrentava cada vez mais dificuldades com a compra, o provisionamento e a manutenção de todos os componentes de hardware e software necessários para atender aos clientes. A equipe de TI se tornou outro obstáculo ao lidar com operações, o que diminuiu a velocidade do provisionamento de novos recursos e da distribuição de novos serviços aos clientes.

A CSI Software investigou as opções de nuvem para eliminar essa sobrecarga, de modo que ela pudesse focar em seu código, e não nas operações. A empresa descobriu que muitos dos mais importantes provedores de nuvem oferecem apenas soluções IaaS (infraestrutura como serviço), o que ainda exige uma grande equipe de TI para gerenciar a pilha IaaS. No fim, a CSI Software determinou que a solução PaaS do Azure foi a melhor opção para suas necessidades. Molina explica, "O Azure tira do caminho o hardware e o software de sistema para que possamos nos concentrar em nossas ofertas de software, ao mesmo tempo que reduzimos nossa sobrecarga de TI".

## <a name="making-the-transition-to-azure"></a>Fazendo a transição para o Azure
Depois de escolher o Azure para sua solução de PaaS, a CSI Software começou a migrar a infraestrutura de back-end e os bancos de dados para a nuvem. Antes do Azure, os clientes do SpectrumNG precisavam instalar um aplicativo cliente que se comunicava com um serviço do WCF (Windows Communication Foundation) no back-end. De acordo com Molina, "Embora alguns clientes hospedassem tudo em seus próprios datacenters, criamos o produto para ser multilocatário. Hospedávamos tudo em um datacenter em Houston, usando o SQL Server como o repositório de dados.

"Nossa oferta de produto também incluía um portal na Web voltado para o membro (escrito em ASP.net), que foi desenvolvido para corresponder à presença do cliente na Web, e uma API SOAP para dar suporte a páginas online e a qualquer integração de terceiros".

A migração para a nuvem não demorou muito para a arquitetura. De acordo com Molina, "O maior esforço foi modificar como lemos as informações do arquivos de configuração, a modificação centralizada da cadeia de conexão, e automatizar o empacotamento, o carregamento e a implantação de nossos lançamentos".

Para desenvolver a automação de compilação, os engenheiros da CSI Software usaram o Azure PowerShell e as APIs REST para criar pacotes e carregá-los em um ambiente de preparo para liberação toda noite.
A transição geral para uma implantação baseada em nuvem do Azure ocorreu de forma rápida e tranquila para a equipe de TI da CSI Software. Molina explica, "No geral, tivemos um ambiente beta na nuvem em três ou quatro semanas depois de iniciar o projeto. Foi uma surpreendente vitória para nós".

Depois de configurar e testar o ambiente, a CSI Software começou a migrar os clientes. Para os clientes que já estavam usando a hospedagem da CSI Software, a transição foi praticamente imperceptível. Migrando de uma implantação local, a transição para a nuvem levou um pouco mais de tempo, mas ainda assim não houve complicações, nem para os clientes, nem para a CSI Software.

Quanto aos clientes novos, a equipe de TI da CSI Software usa o seguinte processo para integrá-los ao Azure:

1. Os scripts do Azure PowerShell são usados para gerar um novo banco de dados para o cliente; todos os clientes começam em uma camada premium, de modo a garantir produtividade inicial suficiente para a transição.
2. Quando possível, a CSI Software usa o Assistente de Migração de SQL do Azure para mover dados existentes para uma instância do Banco de Dados SQL do Azure.
3. Por fim, os SSIS (SQL Server Integration Services) da Microsoft são usados para reconciliar todas as discrepâncias nos dados ou executar qualquer limpeza de dados, conforme exigido.

Hoje, aproximadamente 99% dos clientes da CSI Software são hospedados no Azure, distribuídos em quatro datacenters regionais (Centro-Norte, Centro-Sul, Leste e Oeste). Com datacenters na região geográfica de cada cliente, a latência é mantida no mínimo.

## <a name="azure-elastic-pools-free-up-it-resources"></a>Pools elásticos do Azure liberam recursos de TI
Vários recursos do Azure ajudaram a CSI Software a mudar: do foco na infraestrutura e nas operações para o foco nos recursos e no desenvolvimento. Talvez o maior benefício tenha sido dos pools elásticos.

Atualmente, a CSI Software fornece aproximadamente 550 bancos de dados para os clientes. Antes dos pools elásticos, era difícil gerenciar muitos bancos de dados em uma estrutura em camadas. Os gerentes de operações precisavam atribuir camadas de desempenho com base nas necessidades dos clientes, o que sobrecarregava significativamente os recursos de TI. Com os pools elásticos, os gerentes podem atribuir locatários a um pool premium ou standard, conforme apropriado e mover os clientes com base no tamanho e na necessidade. Os clientes sentiram os efeitos dos pools elásticos quase que instantaneamente; antes dos pools elásticos, os clientes tinham tempos limite e outros problemas durante os períodos de maior uso, mas com os pools elásticos, eles podem realizar uma grande quantidade de atividades, conforme a necessidade e podem continuar usando o SpectrumNG sem problemas.

## <a name="azure-active-geo-replication-accelerates-reporting"></a>A replicação geográfica ativa do Azure acelera a geração de relatórios
Vários clientes da CSI Software também estão se beneficiando com a replicação geográfica ativa do Azure. Com a replicação geográfica ativa, é possível configurar até quatro bancos de dados secundários legíveis na mesma região ou em regiões diferentes de datacenter. A CSI Software usa a replicação geográfica ativa de duas maneiras: primeira, os bancos de dados secundários são disponibilizados no caso de interrupção de um datacenter ou de incapacidade de conexão com o banco de dados primário; segunda, os bancos de dados secundários são legíveis e podem ser usados para transferir cargas de trabalho somente leitura, como trabalhos de geração de relatórios. Alguns clientes da CSI Software usam esse benefício para acelerar os fluxos de trabalho de relatório.

## <a name="csi-software-application-logic-and-architecture"></a>Arquitetura e lógica de aplicativo da CSI Software
O SpectrumNG usa funções web. Como o aplicativo é multilocatário, um serviço do WCF é usado para tratar da solicitação de conexão inicial dos clientes. Como Molina declara, "A solicitação identifica cada cliente, o que nos permite criar uma cadeia de conexão com seus bancos de dados para fazer tudo o que precisamos fazer".

Para a camada Web de seu serviço, a CSI Software aproveita o dimensionamento automático do Azure, com base no dia e na hora. Os recursos disponíveis são aumentados automaticamente para o maior uso durante o horário comercial, de acordo com o fuso horário de cada datacenter regional. Os recursos também são definidos para redução vertical nos finais de semana, quando as necessidades dos clientes são menores.

![Arquitetura da Daxko/CSI](./media/sql-database-implementation-daxko/figure1.png)

Figura 1. Uma função de trabalho dos serviços de nuvem extrai dados estruturados do Banco de Dados SQL do Azure e dados semiestruturados do armazenamento de tabelas. Os usuários do SpectrumNG interagem com esses dados por meio de uma função web dos serviços de nuvem.

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>Usando aplicativos Web e uma camada de plano da Web para aplicativos móveis
Usar o Banco de Dados SQL do Azure liberou os recursos para que a CSI Software permitisse novas iniciativas, incluindo uma plataforma móvel completa baseada em uma API personalizada hospedada nos aplicativos Web do Azure. A plataforma permite que membros e a equipe de academia usem dispositivos móveis para verificar programações, agendar aulas e receber mensagens.

A plataforma usa a SOA (arquitetura orientada a serviços) para obter um único componente — como um POS (sistema de ponto de venda) ou um sistema de vendas — movê-lo dinamicamente para outro plano da Web e criar um serviço que dê suporte a esse componente, enquanto deixa o restante no plano da Web original. Essa capacidade proporciona uma enorme flexibilidade à CSI Software, além de ajudar a reduzir os custos.

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>O Azure permite que os desenvolvedores da CSI Software se concentram em aplicativos e serviços
O Banco de Dados SQL do Azure não é apenas uma dádiva para os clientes do SpectrumNG, que aproveitam o serviço rápido e confiável, mas também uma grande conquista para a equipe de TI e os desenvolvedores da CSI Software. Ao transferir as operações para o Azure na nuvem, a CSI Software reduziu sua sobrecarga em recursos e infraestrutura, acelerou consideravelmente os ciclos de desenvolvimento e não precisa mais gerenciar minuciosamente os bancos de dados a fim de otimizar o desempenho para seus locatários.

## <a name="more-information"></a>Mais informações
* Para saber mais sobre os pools elásticos do Azure, consulte [pools elásticos](sql-database-elastic-pool.md).
* Para saber mais sobre as ferramentas de bancos de dados e dimensionamento elástico, confira as [ferramentas de banco de dados elástico e dimensionamento elástico](sql-database-elastic-scale-get-started.md).
* Para saber mais sobre como migrar um banco de dados do SQL Server, confira [Migrar um banco de dados do SQL Server para o Azure](sql-database-cloud-migrate.md).
* Para saber mais sobre a replicação geográfica ativa, confira [Replicação geográfica ativa](sql-database-geo-replication-overview.md).
* Para saber mais sobre funções web e funções de trabalho, confira [funções de trabalho](../fundamentals-introduction-to-azure.md#compute).    
* Para saber mais sobre o Barramento de Serviço, confira [Barramento de Serviço do Azure](https://azure.microsoft.com/services/service-bus/).
* Para saber mais sobre dimensionamento automático, confira o artigo sobre o [dimensionamento de serviços de nuvem](../cloud-services/cloud-services-how-to-scale.md).

