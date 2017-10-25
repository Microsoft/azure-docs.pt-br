---
title: Estudo de caso sobre o Banco de Dados SQL do Azure - Umbraco | Microsoft Docs
description: "Saiba como a Umbraco usa o Banco de Dados SQL para provisionar e dimensionar serviços rapidamente a milhares de locatários na nuvem"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5243d31e-3241-4cb0-9470-ad488ff28572
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: 6e9c56874bf4bda7f4248a44e274532ed2555153
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco usa o Banco de Dados SQL do Azure para provisionar e dimensionar serviços rapidamente a milhares de locatários na nuvem
![Logotipo da Umbraco](./media/sql-database-implementation-umbraco/umbracologo.png)

A Umbraco é um conhecido CMS (sistema de gerenciamento de conteúdo) de software livre que pode executar qualquer coisa, de pequenos sites de campanha e folhetos a aplicativos complexos para empresas da Fortune 500 e sites de mídia global. 

> "Temos uma grande comunidade de desenvolvedores que usam o sistema, com mais de 100 mil desenvolvedores em nossos fóruns e mais de 350 mil sites que estão ativos, executando a Umbraco".
> 
> — Morten Christensen, líder técnico da Umbraco
> 
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-Umbraco/player]
> 
> 

Para simplificar as implantações do cliente, a Umbraco adicionou a UaaS (Umbraco como serviço): uma oferta de SaaS (software como serviço) que elimina a necessidade de implantações locais, fornece dimensionamento interno e elimina a sobrecarga de gerenciamento permitindo que os desenvolvedores se concentrem na inovação de produtos em vez de no gerenciamento de soluções. A Umbraco é capaz de fornecer todos esses benefícios confiando no modelo flexível de PaaS (plataforma como serviço) oferecido pelo Microsoft Azure.

A UaaS permite que os clientes de SaaS usem os recursos de CMS da Umbraco que anteriormente estavam fora de seu alcance. Esses clientes são provisionados com um ambiente de trabalho CMS que inclui um banco de dados de produção. Os clientes podem incluir até dois bancos de dados adicionais para ambientes de desenvolvimento e preparo, de acordo com os respectivos requisitos. Quando um novo ambiente é solicitado, um processo automatizado atribui esse cliente a um banco de dados automaticamente. O novo banco de dados fica pronto em segundos, pois ele já foi pré-provisionado pela Umbraco de um pool elástico de bancos de dados disponíveis do Azure (veja a Figura 1).

![Ciclo de vida de provisionamento da Umbraco](./media/sql-database-implementation-umbraco/figure1.png)

Figura 1. Provisionando o ciclo de vida da UaaS (Umbraco como serviço)

## <a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Pools elásticos do Azure e automação simplificam implantações
Com o Banco de Dados SQL do Azure e outros serviços do Azure, os clientes da Umbraco podem autoprovisionar seus ambientes, e a Umbraco pode monitorar e gerenciar facilmente bancos de dados como parte de um fluxo de trabalho intuitivo:

1. Provisão
   
   A Umbraco mantém uma capacidade de 200 bancos de dados pré-provisionados disponíveis de pools elásticos. Quando um novo cliente se inscreve para a UaaS, a Umbraco fornece a ele um novo ambiente CMS quase em tempo real atribuindo-lhe um banco de dados do pool de disponibilidade.
   
   Quando um pool de disponibilidade atinge seu limite, um novo pool elástico é criado e novos bancos de dados são pré-provisionados para serem atribuídos aos clientes conforme a necessidade.
   
   A implementação é totalmente automatizada usando bibliotecas de gerenciamento do C# e filas do Barramento de Serviço do Azure.
2. Utilização
   
   Os clientes usam de um a três ambientes (para produção, preparo e/ou desenvolvimento), cada um com seu próprio banco de dados. Os bancos de dados do cliente estão em pools elásticos, o que permite à Umbraco fornecer dimensionamento eficiente sem a necessidade de provisionamento excessivo.
   
   ![Visão geral do projeto da Umbraco](./media/sql-database-implementation-umbraco/figure2.png)
   
   ![Detalhes do projeto da Umbraco](./media/sql-database-implementation-umbraco/figure3.png)
   
   Figura 2. Site do cliente da UaaS (Umbraco como serviço), mostrando a visão geral e os detalhes do projeto
   
   O Banco de Dados SQL do Azure usa DTUs (Unidades de Transação de Banco de Dados) para representar o poder relativo exigido para transações reais de banco de dados. Para os clientes da UaaS, os bancos de dados costumam operar em aproximadamente 10 DTUs, mas cada um tem a elasticidade para dimensionamento sob demanda. Isso significa que a UaaS pode garantir que os clientes sempre tenham os recursos necessários, mesmo durante os horários de pico. Por exemplo, durante um evento esportivo recente no domingo à noite, um cliente da UaaS vivenciou picos de banco de dados de até 100 DTUs durante o jogo. Os pools elásticos do Azure permitiram que a Umbraco desse suporte a essa alta demanda sem degradação de desempenho.
3. Monitoramento
   
   A Umbraco monitora a atividade do banco de dados usando painéis no portal do Azure, com alertas de email personalizados.
4. Recuperação de desastre
   
   O Azure fornece duas opções de DR (recuperação de desastre): replicação geográfica ativa e restauração geográfica. A opção de DR que uma empresa deve selecionar depende de seus [objetivos de continuidade de negócios](sql-database-business-continuity.md).
   
   A replicação geográfica ativa fornece o nível mais rápido de resposta em caso de tempo de inatividade. Usando a replicação geográfica ativa, você pode criar até quatro secundários legíveis nos servidores em diferentes regiões e, assim, pode iniciar o failover para qualquer um dos secundários em caso de falha.
   
   A Umbraco não exige a replicação geográfica, mas aproveita a restauração geográfica do Azure para ajudar a garantir mínimo tempo de inatividade no caso de uma interrupção. A restauração geográfica conta com backups de banco de dados no armazenamento do Azure com redundância geográfica. Isso permite aos usuários restaurar usando uma cópia de backup quando há uma interrupção na região primária.
5. Cancelar provisionamento
   
   Quando um ambiente de projeto é excluído, todos os bancos de dados associados (desenvolvimento, preparo ou ativo) são removidos durante a limpeza da fila do Barramento de Serviço do Azure. Esse processo automatizado restaura os bancos de dados não utilizados no pool de disponibilidade de banco de dados elástico da Umbraco, disponibilizando-os para provisionamento futuro e mantendo a utilização máxima.

## <a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>Pools elásticos permitem que a UaaS seja dimensionada com facilidade
Aproveitando os pools elásticos do Azure, a Umbraco pode otimizar o desempenho para seus clientes sem que haja provisionamento excessivo ou escasso. Atualmente, a Umbraco tem cerca de 3.000 bancos de dados em 19 pools elásticos, com a capacidade de serem dimensionados facilmente conforme a necessidade, para acomodar qualquer um dos seus 325.000 clientes existentes ou novos clientes que estão prontos para implantar um CMS na nuvem.

Na verdade, de acordo com Morten Christensen, líder técnico da Umbraco, "Agora, a UaaS está com um crescimento de 30 novos clientes por dia. Nossos clientes estão muito satisfeitos com a conveniência de poderem provisionar novos projetos em questão de segundos, publicar instantaneamente atualizações em seus sites ativos de um ambiente de desenvolvimento usando a 'implantação de um clique' e fazer alterações rapidamente caso encontrem erros".

Se um cliente não precisar mais de um segundo e/ou terceiro ambiente, ele simplesmente poderá removê-los. Isso libera recursos que podem ser usados por outros clientes como parte do pool de disponibilidade de bancos de dados elásticos da Umbraco.

![Arquitetura de implantação da Umbraco](./media/sql-database-implementation-umbraco/figure4.png)

Figura 3. Arquitetura de implantação da UaaS no Microsoft Azure

## <a name="the-path-from-datacenter-to-cloud"></a>O caminho do datacenter para a nuvem
Quando os desenvolvedores da Umbraco inicialmente tomaram a decisão de mudar para um modelo de SaaS, eles sabiam que precisariam de uma maneira econômica e escalonável de criar o serviço.

> "Os pools elásticos são ideais para nossa oferta de SaaS, pois podemos aumentar ou diminuir a capacidade conforme a necessidade. O provisionamento é fácil, e com a nossa configuração, podemos manter a utilização no máximo”.
> 
> — Morten Christensen, líder técnico da Umbraco
> 
> 

"Queríamos usar nosso tempo resolvendo problemas de nossos clientes, não gerenciando a infraestrutura. Queríamos facilitar para nossos clientes a obtenção do valor máximo", afirma Niels Hartvig, fundado da Umbraco. "Inicialmente, pensamos em nós mesmos hospedar os servidores, mas o planejamento de capacidade teria sido um pesadelo“. Coincidentemente, a Umbraco não emprega administradores de banco de dados, o que ressalta uma importante proposta de valor para usar a UaaS.

Uma meta importante dos desenvolvedores da Umbraco era fornecer uma maneira para os clientes da UaaS de provisionar ambientes rapidamente e sem limitações de capacidade. Mas, fornecer um serviço hospedado dedicado em datacenters da Umbraco exigiria muita capacidade excedente para lidar com picos de processamento. Isso significaria adicionar infraestrutura considerável de computação que teria sido regulamente subutilizada.

Além disso, a equipe de desenvolvimento da Umbraco queria uma solução que a permitisse reutilizar o código existente o quanto fosse possível. Como o desenvolvedor da Umbraco Mikkel Madsen afirma, "Estávamos felizes com as ferramentas de desenvolvimento da Microsoft com as quais já estávamos familiarizados, como o Microsoft SQL Server, Banco de Dados SQL do Microsoft Azure, ASP.net e IIS (Serviços de Informações da Internet). Antes de investir em uma IaaS ou em uma solução de nuvem PaaS, queríamos ter certeza de que ela daria suporte às nossas ferramentas e plataformas Microsoft, assim não teríamos que fazer mudanças massivas em nossa base de código".

Para atender a todos os critérios, a Umbraco buscou um parceiro de nuvem com as seguintes qualificações:

* Confiabilidade e capacidade suficientes
* Suporte a ferramentas de desenvolvimento da Microsoft para que os engenheiros da Umbraco não fossem forçados a reinventar completamente o ambiente de desenvolvimento
* Presença em todos os mercados geográficos nos quais a UaaS compete (as empresas precisam ter certeza de que elas podem acessar seus dados rapidamente e que seus dados sejam armazenados em um local que atenda aos requisitos normativos regionais)

## <a name="why-umbraco-chose-azure-for-uaas"></a>Por que a Umbraco escolheu o Azure para a UaaS
De acordo com Morten Christensen, "Depois de considerar todas as nossas opções, escolhemos o Azure porque ele atende a todos os nossos critérios, da capacidade de gerenciamento e escalabilidade à familiaridade e economia. Configuramos os ambientes em VMs do Azure e cada ambiente tem sua própria instância do Banco de Dados SQL do Azure, com todas as instâncias em pools elásticos. Ao separar os bancos de dados entre ambientes de desenvolvimento, preparo e ativo, podemos oferecer aos nossos clientes isolamento de desempenho robusto correspondente ao dimensionamento — uma grande vitória".

Morten continua, "Antes, tínhamos que provisionar servidores para bancos de dados da Web manualmente. Agora, não precisamos pensar nisso. Tudo é automatizado — do provisionamento à limpeza".

Morten também está satisfeito com os recursos de dimensionamento fornecidos pelo Azure. "Os pools elásticos são ideais para nossa oferta de SaaS, pois podemos aumentar ou diminuir a capacidade conforme a necessidade. O provisionamento é fácil, e com a nossa configuração, podemos manter a utilização no máximo”. Morten afirma, "A simplicidade dos pools elásticos, com a garantia das DTUs baseadas em camada de serviço, nos dá o poder de provisionar novos pools de recursos sob demanda. Recentemente, um de nossos maiores clientes chegou ao pico de 100 DTUs em seu ambiente ativo. Usando o Azure, nosso pools elásticos forneceram aos bancos de dados do cliente os recursos que eles precisavam em tempo real sem precisar prever os requisitos de DTU. De modo simplista, nossos clientes obtém o tempo de mudança que esperavam, e podemos cumprir nossos contratos de nível de serviço de desempenho".

Mikkel Madsen resume da seguinte maneira: "Adotamos o potente algoritmo do Azure que conecta um cenário comum de SaaS (integrando novos clientes em tempo real em escala) ao nosso padrão de aplicativo (pré-provisionando bancos de dados, tanto de desenvolvimento quanto ativos) sobre a tecnologia subjacente (usando as filas do Barramento de Serviço do Azure em conjunto com o Banco de Dados SQL do Azure)".

## <a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Com o Azure, a UaaS está superando as expectativas dos clientes
Desde que escolheu o Azure como seu parceiro de nuvem, a Umbraco tem sido capaz de fornecer aos clientes da UaaS desempenho otimizado de gerenciamento de conteúdo, sem o investimento em recursos de TI exigido por uma solução auto-hospedada. Como Morten afirma, "Adoramos a conveniência e a escalabilidade de desenvolvedor que o Azure nos proporciona, e nossos clientes estão empolgados com os recursos e a confiabilidade. De modo geral, tem sido uma grande vitória para nós!"

## <a name="more-information"></a>Mais informações
* Para saber mais sobre os pools elásticos do Azure, consulte [pools elásticos](sql-database-elastic-pool.md).
* Para saber mais sobre o Barramento de Serviço, confira [Barramento de Serviço do Azure](https://azure.microsoft.com/services/service-bus/).
* Para saber mais sobre funções web e funções de trabalho, confira [funções de trabalho](../fundamentals-introduction-to-azure.md#compute).    
* Para saber mais sobre redes virtuais, confira [redes virtuais](https://azure.microsoft.com/documentation/services/virtual-network/).    
* Para saber mais sobre backup e recuperação, confira [continuidade de negócios](sql-database-business-continuity.md).    
* Para saber mais sobre o monitoramento de pools, confira [monitoramento de pools](sql-database-elastic-pool-manage-portal.md).    
* Para saber mais sobre a Umbraco como serviço, confira [Umbraco](https://umbraco.com/cloud).

