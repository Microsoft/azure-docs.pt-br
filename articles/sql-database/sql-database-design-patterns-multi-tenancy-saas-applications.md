---
title: "Padrões de design para aplicativos SaaS multilocatários e o Banco de Dados SQL do Azure | Microsoft Docs"
description: "Este artigo aborda os requisitos e padrões de arquitetura de dados comuns que aplicativos de banco de dados multilocatários em execução em um ambiente de nuvem precisam considerar e as várias compensações associadas a esses padrões. Ele também explica como o Banco de Dados SQL do Azure com seus pools elásticos e suas ferramentas elásticas ajudam a atender a esses requisitos sem comprometimento."
keywords: 
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 1dd20c6b-ddbb-40ef-ad34-609d398d008a
ms.service: sql-database
ms.custom: scale out apps
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-design
ms.date: 02/01/2017
ms.author: srinia
ms.openlocfilehash: 0f6ba62a01f3211ccaae6b6c48f72e0de54aad78
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="design-patterns-for-multi-tenant-saas-applications-and-azure-sql-database"></a>Padrões de design para aplicativos SaaS multilocatários e o Banco de Dados SQL do Azure
Neste artigo, você pode aprender sobre os requisitos e padrões comuns de arquitetura de dados de aplicativos de banco de dados SaaS (software como serviço) multilocatários em execução em um ambiente de nuvem. Ele também explica os fatores que você precisa considerar e as compensações dos diferentes padrões de design. O pool elástico e as ferramentas elásticas no Banco de Dados SQL podem ajudar você a atender os requisitos específicos sem comprometer outros objetivos.

Os desenvolvedores, às vezes, fazem escolhas que funcionam para seus interesses de longo prazo ao criar modelos de locatário para as camadas de dados de aplicativos multilocatários. Pelo menos inicialmente, o desenvolvedor poderá perceber uma facilidade de desenvolvimento e um provedor de serviço de nuvem de menor custo como mais importantes do que o isolamento de locatário ou a escalabilidade de um aplicativo. Essa escolha pode levar a preocupações de satisfação do cliente e uma posterior correção de curso dispendiosa.

Um aplicativo multilocatário é um aplicativo hospedado em um ambiente de nuvem e fornece o mesmo conjunto de serviços para centenas ou milhares de locatários que não compartilham ou veem os dados uns dos outros. Um exemplo é um aplicativo SaaS que fornece serviços para locatários em um ambiente hospedado em nuvem.

## <a name="multi-tenant-applications"></a>Aplicativos multilocatários
Em aplicativos multilocatários, os dados e a carga de trabalho podem ser facilmente particionados. Você pode particionar dados e a carga de trabalho, por exemplo, com limites de locatário, porque a maioria das solicitações ocorrem dentro dos limites de um locatário. Essa propriedade é inerente aos dados e à carga de trabalho e favorece os padrões de aplicativos discutidos neste artigo.

Os desenvolvedores usam este tipo de aplicativos distribuídos em todo o espectro de aplicativos baseados em nuvem, incluindo:

* Aplicativos de banco de dados de parceiros que estão migrados para a nuvem como aplicativos SaaS
* Aplicativos SaaS compilados para a nuvem desde o princípio
* Aplicativos diretos voltados para o cliente
* Aplicativos empresariais voltados para funcionários

Aplicativos SaaS que são projetados para a nuvem e aqueles com raízes como aplicativos de banco de dados de parceiros normalmente serão aplicativos multilocatários. Esses aplicativos SaaS entregam um aplicativo de software especializado, como um serviço para seus locatários. Os locatários podem acessar o serviço de aplicativo e ter propriedade completa dos dados associados armazenados como parte do aplicativo. Mas, para aproveitar os benefícios de SaaS, os locatários devem ceder algum controle sobre seus próprios dados. Eles confiam no provedor de serviços SaaS para manter seus dados seguros e isolados de dados de outros locatários. MYOB, SnelStart e Salesforce.com são exemplos desse tipo de aplicativo SaaS multilocatário. Cada um desses aplicativos pode ser particionado por limites de locatário e oferecer suporte aos padrões de design de aplicativo discutidos neste artigo.

Os aplicativos que fornecem um serviço direto a consumidores ou funcionários dentro de uma organização (também conhecidos como usuários, em vez de locatários) são outra categoria no espectro de aplicativos multilocatários. Os clientes assinam o serviço e não possuem os dados coletados e armazenados pelo provedor de serviços. Os provedores de serviços têm requisitos menos rigorosos para manter os dados de seus clientes isolados uns dos outros, além de normas de privacidade determinadas pelo governo. Exemplos desse tipo de aplicativo multilocatário voltado para o cliente são provedores de conteúdo de mídia como Netflix, Spotify e Xbox LIVE. Outros exemplos de aplicativos facilmente particionáveis são aplicativos de escala da Internet voltados para clientes ou aplicativos IoT (Internet das Coisas) em que cada cliente ou dispositivo pode servir como uma partição. Limites de partição podem separar usuários e dispositivos.

Nem todos os aplicativos são particionados facilmente com uma única propriedade, como o locatário, cliente, usuário ou dispositivo. Um aplicativo de ERP (planejamento de recursos da empresa) complexo, por exemplo, tem produtos, pedidos e clientes. Ele normalmente tem um esquema complexo com milhares de tabelas altamente interconectadas.

Nenhuma estratégia de partição única poderá ser aplicada a todas as tabelas e funcionar na carga de trabalho do aplicativo. Este artigo se concentra em aplicativos multilocatários que possuem dados e cargas de trabalho facilmente particionáveis.

## <a name="multi-tenant-application-design-trade-offs"></a>Compensações do design de aplicativo multilocatário
O padrão de design que um desenvolvedor de aplicativos multilocatários escolhe normalmente é baseado em uma consideração dos seguintes fatores:

* **Isolamento de locatário**. O desenvolvedor precisa garantir que nenhum locatário tenha acesso indesejado aos dados de outros locatários. O requisito de isolamento se estende a outras propriedades, como o fornecimento de proteção contra vizinhos barulhentos, sendo capaz de restaurar dados de um determinado locatário e implementar personalizações específicas de locatário.
* **Custo de recursos de nuvem**. Um aplicativo SaaS precisa ter um custo competitivo. Um desenvolvedor de aplicativos multilocatários pode optar por otimizar para custos mais reduzidos no uso de recursos de nuvem, como os custos de armazenamento e de computação.
* **Facilidade de Operações de Desenvolvimento**. Um desenvolvedor de aplicativos multilocatários precisa incorporar a proteção de isolamento, manter e monitorar a integridade do esquema de seus aplicativos e banco de dados e solucionar problemas de locatários. A complexidade na operação e no desenvolvimento de aplicativos se traduz diretamente em custos adicionais e a desafios relacionados à satisfação do locatário.
* **Escalabilidade**. A capacidade de adicionar incrementalmente mais locatários e a capacidade para locatários que precisam dela é essencial para uma operação de SaaS bem-sucedida.

Se forem comparados, cada um desses fatores tem compensações. A oferta de nuvem de menor custo pode não oferecer a experiência de desenvolvimento mais conveniente. É importante para o desenvolvedor fazer escolhas informadas sobre essas opções e compensações durante o processo de design do aplicativo.

Um padrão de desenvolvimento popular é agrupar vários locatários em um ou alguns bancos de dados. Os benefícios dessa abordagem são um custo menor porque você paga por um pequeno número de bancos de dados e a simplicidade relativa do trabalho com um número limitado de bancos de dados. No entanto, ao longo do tempo, um desenvolvedor de aplicativo SaaS multilocatário perceberá que essa opção tem desvantagens significativas no isolamento de locatários e na escalabilidade. Se o isolamento de locatário se tornar importante, será necessário um esforço adicional para proteger os dados de locatário no armazenamento compartilhado contra acesso não autorizado ou vizinhos com ruídos. Esse esforço adicional pode aumentar significativamente os esforços de desenvolvimento e os custos de manutenção do isolamento. Da mesma forma, se for necessário adicionar locatários, esse padrão de design normalmente requer conhecimento para redistribuir dados de locatário em bancos de dados para dimensionar corretamente a camada de dados de um aplicativo.  

O isolamento de locatários geralmente é um requisito fundamental em aplicativos SaaS multilocatário que atendem às empresas e organizações. Os desenvolvedores podem ser tentados para as vantagens percebidas em relação a simplicidade e o custo sobre o isolamento de locatários e a escalabilidade. Essa compensação pode se provar complexa e cara conforme o serviço cresce e os requisitos de isolamento de locatário se tornam mais importantes e precisam ser gerenciados na camada de aplicativo. No entanto, para aplicativos multilocatários que fornecem um serviço direto voltado para o cliente, o isolamento de locatários pode ser uma prioridade menor que a otimização de custo de recursos de nuvem.

## <a name="multi-tenant-data-models"></a>Modelos de dados de multilocatário
Práticas de design comuns para a colocação de dados de locatário seguem esses três modelos distintos, mostrados na Figura 1.

![Modelos de dados de aplicativo multilocatário](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)

Figura 1: Práticas comuns de design para modelos de dados de multilocatário

* **Banco de dados por locatário**. Cada locatário tem seu próprio banco de dados. Todos os dados específicos de locatário estão restritos ao seu banco de dados e isolados de outros locatários e seus dados.
* **Banco de dados fragmentados compartilhados**. Vários inquilinos compartilham um de vários bancos de dados. Um conjunto distinto de locatários é atribuído a cada banco de dados usando uma estratégia de particionamento como hash, intervalo ou lista de particionamento. Essa estratégia de distribuição de dados é frequentemente conhecida como fragmentação.
* **Banco de dados individuais compartilhados**. Um banco de dados único, às vezes grande, contém dados para todos os locatários, sem ambiguidade em uma coluna de ID de locatário.

> [!NOTE]
> Um desenvolvedor de aplicativos pode optar por colocar locatários diferentes em esquemas de banco de dados diferentes e, em seguida, usar o nome do esquema para remover a ambiguidade de locatários diferentes. Não recomendamos essa abordagem porque ela geralmente requer o uso de SQL dinâmico e isso não pode ser eficaz em cache de plano. No restante deste artigo, vamos nos concentrar na abordagem de tabela compartilhada para essa categoria de aplicativo multilocatário.
> 
> 

## <a name="popular-multi-tenant-data-models"></a>Modelos populares de dados multilocatários
É importante avaliar os diferentes tipos de modelos de dados de multilocatários em termos de compensações de design do aplicativo que nós já identificamos. Esses fatores ajudam a caracterizar os três modelos de dados multilocatários mais comuns descritos anteriormente e o uso do banco de dados, como mostrado na Figura 2.

* **Isolamento**. O nível de isolamento entre locatários pode ser medido pela quantidade de isolamento de locatário que um modelo de dados alcança.
* **Custo de recursos de nuvem**. A quantidade de compartilhamento de recursos entre locatários pode otimizar o custo de recursos de nuvem. Um recurso pode ser definido como o custo de computação e armazenamento.
* **Custo de Operações de Desenvolvimento**. A facilidade de desenvolvimento de aplicativos, implantação e gerenciamento reduz o custo geral de operação de SaaS.  

Na Figura 2, o eixo Y mostra o nível de isolamento de locatário. O eixo X mostra o nível de compartilhamento de recursos. A seta diagonal cinza no meio indica a direção dos custos de Operações de Desenvolvimento, que tendem a aumentar ou diminuir.

![Padrões populares do design de aplicativo multilocatário](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png)

Figura 2: Modelos populares de dados multilocatários

O quadrante inferior direito na Figura 2 mostra um padrão de aplicativo que usa um banco de dados individual compartilhado potencialmente grande e a abordagem de tabela compartilhada (ou esquema separado). É bom para o compartilhamento de recursos porque todos os locatários usam os mesmos recursos de banco de dados (CPU, memória, entrada/saída) em um banco de dados individual. No entanto, o isolamento de locatários é limitado. Talvez seja necessário executar etapas adicionais para proteger locatários uns dos outros na camada de aplicativo. Essas etapas adicionais podem aumentar significativamente o custo de DevOps do desenvolvimento e gerenciamento de aplicativos. A escalabilidade é limitada pela escala do hardware que hospeda o banco de dados.

O quadrante inferior esquerdo da Figura 2 ilustra vários locatários fragmentados em vários bancos de dados (normalmente, unidades de escala de um outro hardware). Cada banco de dados hospeda um subconjunto de locatários que lida com problemas de escalabilidade de outros padrões. Se mais capacidade for necessária para mais locatários, você pode facilmente colocar os locatários em novos bancos de dados alocados para novas unidades de escala de hardware. No entanto, a quantidade de compartilhamento de recursos é reduzida. Somente locatários colocados nas mesmas unidades de escala compartilham recursos. Essa abordagem fornece poucas melhorias ao isolamento de locatários pois ainda vários locatários são colocados em locais sem que sejam protegidos automaticamente contra ações uns dos outros. A complexidade do aplicativo permanece alta.

O quadrante superior esquerdo da Figura 2 é a terceira abordagem. Ela coloca cada dado de locatário em seu próprio banco de dados. Essa abordagem tem ótimas propriedades de isolamento de locatário, mas limita o compartilhamento de recursos quando cada banco de dados tem seus próprios recursos dedicados. Essa será uma abordagem boa se todos os locatários tiverem cargas de trabalho previsíveis. Se as cargas de trabalho do locatário se tornarem menos previsíveis, o provedor não poderá otimizar o compartilhamento de recurso. Falta de previsibilidade é comum para aplicativos SaaS. O provedor deve ser provisionado em excesso para atender às demandas ou para reduzir os recursos. Qualquer ação resultará em custos mais elevados ou diminuirá a satisfação do locatário. Um grau maior de compartilhamento de recursos entre locatários se torna desejável para tornar a solução mais econômica. Aumentar o número de bancos de dados também aumenta o custo de Operações de Desenvolvimento na implantação e manutenção de aplicativos. Apesar dessas preocupações, esse método proporciona o melhor e mais fácil isolamento para locatários.

Esses fatores também influenciam o padrão de design escolhido por um cliente:

* **Propriedade dos dados de locatário**. Um aplicativo no qual os locatários mantêm a propriedade de seus próprios dados favorece o padrão de um banco de dados individual por locatário.
* **Escala**. Um aplicativo que se destina a centenas de milhares ou milhões de locatários favorece abordagens de compartilhamento de banco de dados, como a fragmentação. Os requisitos de isolamento ainda podem apresentar desafios.
* **Modelo de negócios e valor**. Se a receita por locatário de um aplicativo for pequena (menos de um dólar), os requisitos de isolamento se tornarão menos essenciais e o banco de dados compartilhado fará sentido. Se a receita por locatário for de alguns dólares ou mais, um modelo de banco de dados por locatário será mais viável. Ele poderá ajudar a reduzir os custos de desenvolvimento.

Considerando as compensações de design mostradas na Figura 2, um modelo ideal multilocatário precisa incorporar boas propriedades de isolamento de locatário com o compartilhamento de recursos ideal entre locatários. Esse modelo se encaixa na categoria descrita no quadrante superior direito da Figura 2.

## <a name="multi-tenancy-support-in-azure-sql-database"></a>Suporte a multilocação no Banco de Dados SQL do Azure
O Banco de Dados SQL do Azure oferece suporte a todos os padrões de aplicativos multilocatários descritos na Figura 2. Com os pools elásticos, ele também dá suporte a um padrão de aplicativo que combina benefícios de isolamento e de compartilhamento de recursos excelentes da abordagem de banco de dados por locatário (consulte o quadrante superior direito na Figura 3). As ferramentas de banco de dados elástico e os recursos no Banco de Dados SQL podem ajudar a reduzir o custo de desenvolvimento e operação de um aplicativo que tenha muitos bancos de dados (mostrados na área sombreada na Figura 3). Essas ferramentas podem ajudá-lo a criar e gerenciar aplicativos que usam qualquer um dos padrões de vários bancos de dados.

![Padrões no Banco de Dados SQL Azure](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png)

Figura 3: Padrões de aplicativos multilocatários no Banco de Dados SQL do Azure

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>Modelo de banco de dados por locatário com ferramentas e pools elásticos
Os pools elásticos no Banco de Dados SQL combina o isolamento de locatários com o compartilhamento de recursos entre bancos de dados de locatário para oferecer um melhor suporte à abordagem de banco de dados por locatário. O Banco de Dados SQL é uma solução de camada de dados para provedores de SaaS que compilam aplicativos multilocatários. A responsabilidade do compartilhamento de recursos entre locatários passa da camada de aplicativo para a camada de serviço do banco de dados. A complexidade do gerenciamento e consulta em escala entre bancos de dados é simplificada com trabalhos elásticos, consulta elástica, transações elásticas e com a biblioteca de cliente do banco de dados elástico.

| Requisitos do aplicativo | Recursos de Banco de Dados SQL |
| --- | --- |
| Isolamento de locatários e compartilhamento de recursos |[Pools elásticos](sql-database-elastic-pool.md): alocam um pool de recursos de Banco de Dados SQL e compartilham recursos entre vários bancos de dados. Além disso, os bancos de dados individuais podem retirar a quantidade de recursos do pool necessária para acomodar picos de demanda de capacidade devido a alterações nas cargas de trabalho de locatário. O próprio pool elástico pode ser aumentado ou reduzido conforme necessário. Os pools elásticos também fornecem facilidade de gerenciamento, monitoramento e solução de problemas no nível do pool. |
| Facilidade de DevOps em bancos de dados |[Pools elásticos](sql-database-elastic-pool.md): conforme mencionado anteriormente. |
| | [Consulta elástica:](sql-database-elastic-query-horizontal-partitioning.md)consulta relatórios ou análises entre locatários nos bancos de dados. |
| | [Trabalhos elásticos:](sql-database-elastic-jobs-overview.md)empacotam e implantam confiavelmente as operações de manutenção de banco de dados ou alterações de esquema do banco de dados para vários bancos de dados. |
| | [Transações elásticas:](sql-database-elastic-transactions-overview.md)processam alterações em vários bancos de dados de uma maneira atômica e isolada. Transações elásticas são necessárias quando os aplicativos precisam de garantias de "tudo ou nada" sobre várias operações de banco de dados. |
| | [Biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md): gerencia distribuições de dados e mapeiam locatários para bancos de dados. |

## <a name="shared-models"></a>Modelos compartilhados
Conforme descrito anteriormente, para a maioria dos provedores de SaaS, uma abordagem de modelo compartilhado pode apresentar problemas com questões de isolamento de locatário e complexidades de desenvolvimento e manutenção do aplicativo. No entanto, para aplicativos multilocatários que fornecem um serviço diretamente para clientes, os requisitos de isolamento de locatário podem não ser uma prioridade tão alta como a minimização do custo. Eles poderão empacotar locatários em um ou mais bancos de dados em uma densidade bastante alta para reduzir os custos. Os modelos de banco de dados compartilhados usando um banco de dados individual ou vários bancos de dados fragmentados podem resultar em uma eficiência adicional no compartilhamento de recursos e na redução do custo geral. O Banco de Dados SQL do Azure fornece alguns recursos que ajudam os clientes a criar um isolamento aprimorado de segurança e gerenciamento em escala na camada de dados.

| Requisitos do aplicativo | Recursos de Banco de Dados SQL |
| --- | --- |
| Recursos de isolamento de segurança |[Segurança em nível de linha](https://msdn.microsoft.com/library/dn765131.aspx) |
| [Esquema de banco de dados](https://msdn.microsoft.com/library/dd207005.aspx) | |
| Facilidade de DevOps em bancos de dados |[Consulta elástica](sql-database-elastic-query-horizontal-partitioning.md) |
| | [Trabalhos elásticos](sql-database-elastic-jobs-overview.md) |
| | [Transações elásticas](sql-database-elastic-transactions-overview.md) |
| | [Biblioteca de cliente do banco de dados elástico](sql-database-elastic-database-client-library.md) |
| | [Divisão e mesclagem do banco de dados elástico](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>Resumo
Os requisitos de isolamento de locatário são importantes para a maioria dos aplicativos SaaS multilocatários. A melhor opção para fornecer isolamento é voltada fortemente para a abordagem banco de dados por locatário. As outras duas abordagens exigem investimentos em camadas de aplicativo complexas que exigem uma equipe de desenvolvimento especializada para fornecer isolamento, que aumenta significativamente custos e riscos. Se os requisitos de isolamento não forem considerados no início do desenvolvimento de serviços, retrocedê-los poderá ser ainda mais oneroso do que os primeiros dois modelos. As principais desvantagens associadas ao modelo de banco de dados por locatário estão relacionadas aos custos dos recursos de nuvem maiores devido à redução de compartilhamento e à manutenção e gerenciamento de um grande número de bancos de dados. Os desenvolvedores de aplicativos SaaS muitas vezes têm dificuldades ao fazer essas compensações.

Enquanto essas compensações podem ser grandes barreiras para a maioria dos provedores de serviço de banco de dados de nuvem, o Banco de Dados SQL do Azure elimina essas barreiras com o pool elástico e os recursos de banco de dados elástico. Os desenvolvedores de SaaS podem combinar as características de isolamento do modelo de banco de dados por locatário e otimizar o compartilhamento de recursos e os aprimoramentos no gerenciamento de um grande número de bancos de dados com pools elásticos e ferramentas associadas.

Provedores de aplicativos multilocatários que não têm requisitos de isolamento de locatário e são capazes de empacotar locatários em banco de dados em uma densidade alta podem considerar que os modelos de dados compartilhados resultam em eficiência adicional no compartilhamento de recursos e na redução do custo geral. As ferramentas de banco de dados elástico do Banco de Dados SQL do Azure, as bibliotecas de fragmentação e os recursos de segurança ajudam os provedores de SaaS a criar e gerenciar esses aplicativos multilocatários.

## <a name="next-steps"></a>Próximas etapas
[Introdução às ferramentas do banco de dados elástico](sql-database-elastic-scale-get-started.md) .

Crie um [painel personalizado do pool elástico para SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) com um aplicativo de exemplo que usa pools elásticos para uma solução de banco de dados econômica e escalonável.

Use as ferramentas de Banco de Dados SQL para [migrar bancos de dados existentes para escalar horizontalmente](sql-database-elastic-convert-to-use-elastic-tools.md).

Para criar um pool elástico usando o Portal do Azure, consulte [criar um pool elástico](sql-database-elastic-pool-manage-portal.md).  

Aprenda como [monitorar e gerenciar um pool elástico](sql-database-elastic-pool-manage-portal.md).

## <a name="additional-resources"></a>Recursos adicionais

* [Implantar e explorar um aplicativo multilocatário que usa o Banco de dados SQL do Azure - Wingtip SaaS](sql-database-saas-tutorial.md)
* [O que é um pool elástico do Azure?](sql-database-elastic-pool.md)
* [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md)
* [Aplicativos multilocatários com ferramentas de banco de dados elástico e segurança em nível de linha](sql-database-elastic-tools-multi-tenant-row-level-security.md)
* [Autenticação em aplicativos multilocatários usando o Azure Active Directory e o OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
* [Aplicativo Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)


## <a name="questions-and-feature-requests"></a>Perguntas e solicitações de recursos

Para fazer perguntas, encontre-se no [Fórum do Banco de Dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Adicione uma solicitação de recursos no [Fórum de comentários do Banco de Dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

