---
title: Gerenciar vários bancos de dados SQL com pools elásticos – Azure | Microsoft Docs
description: Gerenciar e dimensionar vários Bancos de Dados SQL – centenas de milhares – usando pools elásticos. Um preço para os recursos que você pode distribuir quando necessário.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
manager: craigg
ms.date: 02/28/2019
ms.openlocfilehash: 775e1abe1f3d6412171b8ff5427fd905e37480b5
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489681"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Os pools elásticos ajudam você a gerenciar e dimensionar vários bancos de dados SQL do Azure

Pools elásticos do Banco de Dados SQL são uma solução simples e econômica para gerenciar e dimensionar a vários bancos de dados com demandas de uso variadas e imprevisíveis. Os bancos de dados em um pool elástico estão em um único servidor do Banco de Dados SQL do Azure e compartilham um número definido de recursos por um preço definido. Os pools elásticos no Banco de Dados SQL do Azure permitem que desenvolvedores de SaaS otimizem o desempenho de preço para um grupo de bancos de dados dentro de um orçamento prescrito oferecendo elasticidade de desempenho para cada banco de dados.

## <a name="what-are-sql-elastic-pools"></a>O que são pools elásticos SQL

Desenvolvedores de SaaS compilam aplicativos com base em camadas de dados de grande escala compostas por vários bancos de dados. Um padrão de aplicativo comum é provisionar um banco de dados individual para cada cliente. Mas diferentes clientes geralmente têm padrões de uso variados e imprevisíveis, e é difícil prever os requisitos de recursos de cada usuário de banco de dados individual. Tradicionalmente, você tinha duas opções:

- Provisionar excessivamente os recursos com base no uso de pico e pagamento, ou
- Provisionamento insuficiente para poupar custos, às custas do desempenho e satisfação do cliente durante picos.

Os pools elásticos resolvem esse problema, garantindo que os bancos de dados recebam os recursos de desempenho que precisam, quando precisam. Eles fornecem um mecanismo de alocação de recursos simples dentro de um orçamento previsível. Para saber mais sobre padrões de design para aplicativos SaaS usando pools elásticos, confira [Padrões de design para aplicativos de SaaS multilocatários com o banco de dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Não há cobrança por banco de dados para pools elásticos. Você é cobrado por cada hora de existência de um pool no vCores ou eDTU mais elevado, independentemente do uso ou se o pool estava ativo por menos de uma hora.

Os pools elásticos permitem que o desenvolvedor compre recursos para um pool compartilhado por vários bancos de dados para acomodar períodos imprevisíveis de uso por bancos de dados individuais. Você pode configurar recursos para o pool com base no [Modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) ou o [Modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md). O requisito de recurso para um pool é determinado pela utilização agregada dos bancos de dados. A quantidade de recursos disponíveis para o pool é controlada pelo orçamento do desenvolvedor. O desenvolvedor simplesmente adiciona bancos de dados ao pool, define os recursos mínimo e máximo para os bancos de dados (DTUs ou vCores mínimo e máximo, dependendo da opção de modelo de recurso) e define os recursos do pool com base no orçamento. Um desenvolvedor pode usar pools para aumentar seu serviço com perfeição desde uma startup lean até uma empresa madura em escala cada vez maior.

Dentro do pool, os bancos de dados individuais recebem a flexibilidade para dimensionar automaticamente no conjunto de parâmetros. Sob carga pesada, um banco de dados pode consumir mais recursos para atender à demanda. Bancos de dados sob cargas leves consomem menos e bancos de dados sem carga não consomem recursos. O provisionamento de recursos para o pool inteiro em vez do bancos de dados único simplifica as tarefas de gerenciamento. Além disso, há um orçamento previsível para o pool. Recursos adicionais podem ser adicionados a um pool existente sem tempo de inatividade do banco de dados, exceto que os bancos de dados podem precisar ser movidos para fornecer os recursos de computação adicionais para a nova reserva eDTU. Da mesma forma, se recursos adicionais não forem mais necessários, eles poderão ser removidos de um pool existente a qualquer momento. E você pode adicionar ou subtrair bancos de dados para o pool. Se um banco de dados é previsível por estar subutilizando recursos, mova-o.

> [!NOTE]
> Ao mover os bancos de dados para dentro ou para fora de um conjunto elástico, não há tempo de inatividade, exceto por um breve período de tempo (na ordem de segundos) no final da operação, quando as conexões com o banco de dados são descartadas.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quando um pool elástico do Banco de Dados SQL deve ser considerado

Pools também são indicados para um grande número de bancos de dados com padrões de utilização específicos. Para um determinado banco de dados, esse padrão é caracterizado por baixa utilização média com picos de utilização relativamente pouco frequentes.

Quanto mais bancos de dados você conseguir adicionar a um pool, maior será a sua economia. Dependendo de seu padrão de utilização do aplicativo, é possível ver economias com apenas dois bancos de dados S3.

As seções a seguir ajudam a compreender como avaliar se sua coleção específica de bancos de dados se beneficia de estar em um pool. Os exemplos usam pools Standard, mas os mesmos princípios também se aplicam aos pools Basic e Premium.

### <a name="assessing-database-utilization-patterns"></a>Avaliar os padrões de utilização do banco de dados

A figura a seguir mostra um exemplo de um banco de dados que passa muito tempo ocioso, mas também apresenta picos de atividade periodicamente. Este é um padrão de utilização adequado para um pool:

   ![um banco de dados individual adequado para um pool](./media/sql-database-elastic-pool/one-database.png)

Para o período de cinco minutos ilustrado, DB1 apresenta picos de até 90 DTUs, mas seu uso geral médio é inferior a cinco DTUs. O tamanho da computação S3 é necessário para executar essa carga de trabalho em um banco de dados individual, mas esse tamanho faz com que a maioria dos recursos fiquem utilizados durante períodos de baixa atividade.

Um pool permite que essas DTUs não utilizadas sejam compartilhadas entre vários bancos de dados, reduzindo as DTUs necessárias e os custos gerais.

Considerando ainda o exemplo anterior, suponha que há outros bancos de dados com padrões de utilização semelhantes ao do DB1. Nas próximas duas figuras abaixo, a utilização de quatro bancos de dados e 20 bancos de dados é sobreposta no mesmo gráfico para ilustrar a natureza não sobreposta da utilização ao longo do tempo usando o modelo de compra baseado em DTU:

   ![quatro bancos de dados com um padrão de utilização adequado para um pool](./media/sql-database-elastic-pool/four-databases.png)

   ![vinte bancos de dados com um padrão de utilização adequado para um pool](./media/sql-database-elastic-pool/twenty-databases.png)

A utilização de DTU agregada em todos os 20 bancos de dados é ilustrada pela linha preta na figura anterior. Ela mostra que a utilização de DTU agregada nunca excede 100 DTUs e indica que os 20 bancos de dados podem compartilhar 100 eDTUs durante esse período de tempo. Isso resulta em uma redução de 20 vezes das DTUs e de 13 vezes do preço em comparação à escolha dos tamanhos da computação S3 para cada banco de dados individual.

Este exemplo é ideal pelas seguintes razões:

- Há grandes diferenças entre o pico de utilização e a utilização média por banco de dados.
- O pico de utilização de cada banco de dados ocorre em diferentes momentos.
- eDTUs são compartilhados entre vários bancos de dados.

O preço de um pool é uma função das eDTUs do pool. Embora o preço unitário de eDTU para um pool seja 1,5x maior que o preço unitário de DTU para um banco de dados individual, **as eDTUs do pool podem ser compartilhadas por vários bancos de dados e, assim, menos eDTUs são necessárias no total**. Essas distinções no preço e compartilhamento de eDTU são a base do potencial de economia que os pools podem oferecer.

As seguintes regras básicas relacionadas à contagem de banco de dados e à utilização do banco de dados ajudam a garantir que um pool ofereça um custo reduzido em comparação ao uso de tamanhos da computação para bancos de dados individuais.

### <a name="minimum-number-of-databases"></a>Número mínimo de bancos de dados

Se a quantidade agregada de recursos para bancos de dados individuais for maior que 1,5x de recursos necessários para o pool, será mais econômico usar um pool elástico.

***Exemplo de modelo de compra baseado em DTU***<br>
Pelo menos dois bancos de dados S3 ou 15 bancos de dados S0 são necessários para que um pool de 100 eDTUs seja mais econômico do que o uso de tamanhos da computação para bancos de dados individuais.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Número máximo de banco de dados em pico simultaneamente

Ao compartilhar recursos, nem todos os bancos de dados em um pool podem usar recursos simultaneamente até o limite disponível para bancos de dados individuais. Quanto menos bancos de dados em pico simultaneamente, menores serão os recursos do conjunto e mais econômico será o pool. Em geral, não mais que 2/3 (ou 67%) dos bancos de dados no pool devem atingir simultaneamente o limite de recursos.

***Exemplo de modelo de compra baseado em DTU***

para reduzir os custos de três bancos de dados S3 em um pool com 200 eDTUs, no máximo dois desses bancos de dados podem atingir simultaneamente o pico em sua utilização. Caso contrário, se mais de dois desses quatro bancos de dados S3 entrarem em pico simultaneamente, o pool precisará ser dimensionado para mais de 200 eDTUs. Se o pool for redimensionado para mais de 200 eDTUs, mais bancos de dados S3 precisarão ser adicionados ao pool para manter os custos menores do que os tamanhos da computação para bancos de dados individuais.

Observe que esse exemplo não considera a utilização de outros bancos de dados no pool. Se todos os bancos de dados tiverem uma certa utilização em um determinado momento, menos de 2/3 (ou 67%) dos bancos de dados pode atingir o pico simultaneamente.

### <a name="resource-utilization-per-database"></a>Utilização de recursos por banco de dados

Uma grande diferença entre o máximo e média de utilização de um banco de dados indica longos períodos de baixa utilização e curtos períodos de alta utilização. Esse padrão de utilização é ideal para compartilhar recursos entre bancos de dados. Um banco de dados deve ser considerado para um pool quando seu pico de utilização for aproximadamente 1,5 vez maior que sua utilização média.

**Exemplo de modelo de compra baseado em DTU**:  um banco de dados S3 com picos de 100 DTUs e que usa em média 67 DTUs, ou menos, é um bom candidato para o compartilhamento de eDTUs em um pool. Outra opção de bom candidato para um pool elástico seria um banco de dados S1 com pico de 20 DTUs e média de uso de 13 DTUs ou menos.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Como fazer para escolher o tamanho de pool correto

O melhor tamanho para um pool depende dos recursos agregados necessários para todos os bancos de dados no pool. Isso inclui determinar o seguinte:

- O máximo de recursos utilizados por todos os bancos de dados no pool (seja o máximo de DTUs ou de vCores, dependendo da escolha do modelo de recursos).
- Bytes de armazenamento máximo utilizados por todos os bancos de dados no pool.

Para as camadas de serviço disponíveis para cada modelo de recurso, consulte [Modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) ou [Modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).

Em casos em que você não pode usar as ferramentas, os procedimentos passo a passo a seguir podem ajudá-lo a estimar se um pool é mais econômico do que bancos de dados individuais:

1. Faça estimativa de eDTUs ou vCores necessários para o pool, conforme a seguir:

   Para modelos de compra baseados em DTU: MAX (<*Número total de bancos de dados* X *utilização média de DTU por banco de dados*>,<br>  
   <*Número de bancos de dados em pico simultaneamente* X *Utilização de DTU em pico por banco de dados*)

   Para o modelo de compra baseado em vCore: MAX (<*Número total de BDs* X *utilização média de vCore por BD*>,<br>  
   <*Número de BDs em pico simultaneamente* X *Utilização máxima de vCore por BD*)

2. Estime o espaço de armazenamento necessário para o pool adicionando o número de bytes necessários para todos os bancos de dados no pool. Determine o tamanho do pool em eDTU que fornece essa quantidade de armazenamento.
3. Para o modelo de compra baseado em DTU, obtenha as maiores estimativas de eDTU da Etapa 1 e Etapa 2. Para o modelo de compra baseado em vCore, obtenha a estimativa de vCore da Etapa 1.
4. Consulte a [página de preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) e localize o menor tamanho de pool que seja maior que a estimativa da Etapa 3.
5. Compare o preço do pool da Etapa 5 com o preço do uso dos tamanhos da computação apropriados para bancos de dados individuais.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Usando outros recursos de Banco de Dados SQL com pools elásticos

### <a name="elastic-jobs-and-elastic-pools"></a>Trabalhos e pools elásticos

Com um pool, as tarefas de gerenciamento são simplificadas com a execução de scripts em **[trabalhos elásticos](elastic-jobs-overview.md)** . Um trabalho elástico elimina a maioria do tédio associado a um grande número de bancos de dados.

Para saber mais sobre outras ferramentas de banco de dados para trabalhar com vários bancos de dados, veja [Expansão com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opções de continuidade dos negócios para bancos de dados em um pool elástico

Os bancos de dados em pool normalmente dão suporte aos mesmos [recursos de continuidade dos negócios](sql-database-business-continuity.md) disponíveis para bancos de dados individuais.

- **Restauração pontual**

  A restauração pontual usa backups de banco de dados automáticos para recuperar um banco de dados em um pool para um ponto específico no tempo. Confira [Restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Restauração geográfica**

  A restauração geográfica fornecerá a opção de recuperação padrão quando um banco de dados estiver indisponível devido a um incidente na região onde está hospedado. Confira [Restaurar um Banco de Dados SQL do Azure ou fazer failover para um secundário](sql-database-disaster-recovery.md)

- **Replicação geográfica ativa**

  Para aplicativos que têm requisitos de recuperação mais agressivos do que a restauração geográfica pode oferecer, configure a [replicação geográfica ativa](sql-database-active-geo-replication.md) ou um [grupo de failover automático](sql-database-auto-failover-group.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Criar um novo pool elástico de um Banco de Dados SQL usando o Portal do Azure

Há duas maneiras de criar um pool elástico no Portal do Azure.

1. É possível criar um pool elástico, pesquisando **pool elástico do SQL** no **Marketplace** ou clicando em **+ Adicionar** na folha de procura de pools elásticos do SQL. Você pode especificar um servidor novo ou existente por este fluxo de trabalho de provisionamento de pool.
2. Ou você pode criar um pool elástico, navegando até um servidor SQL existente e clicando em **Criar pool** para criar um pool diretamente nesse servidor. A única diferença aqui é ignorar a etapa em que você especifica o servidor durante o fluxo de trabalho de provisionamento de pools.

> [!NOTE]
> Você pode criar vários pools em um servidor, mas não pode adicionar bancos de dados de servidores diferentes ao mesmo pool.

A camada de serviço do pool determina os recursos disponíveis para os elásticos no pool e a quantidade máxima de recursos disponíveis para cada banco de dados. Para obter mais detalhes, veja Limites de recursos para pools elásticos no [modelo DTU](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Para saber os limites de recurso baseados em vCore para pools elásticos, confira [Limites de recurso baseados em vCore - pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md).

Para configurar os recursos e preços do pool, clique em **Configurar pool**. Em seguida, selecione uma camada de serviço, adicione bancos de dados ao pool e configure os limites de recursos para o pool e os bancos de dados.

Quando tiver concluído a configuração do pool, você poderá clicar em "Aplicar", nomear o pool e clicar em "OK" para criar o pool.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorar um pool elástico e os bancos de dados

No Portal do Azure, é possível monitorar a utilização de um pool elástico e os bancos de dados no pool. Você também pode criar um conjunto de alterações para o pool elástico e enviar todas as alterações ao mesmo tempo. Essas alterações incluem adicionar ou remover bancos de dados, alterar as configurações de pool elástico ou alterar suas configurações de banco de dados.

Para começar a monitorar o pool elástico, localize e abra um pool elástico no portal. Primeiro, você verá uma tela que fornece uma visão geral do status do pool elástico. Isso inclui:

- Monitorar gráficos mostrando o uso de recursos do pool elástico
- Alertas e recomendações recentes, se disponíveis, para o pool elástico

O gráfico a seguir mostra um exemplo de pool elástico:

![Exibição de pool](./media/sql-database-elastic-pool-manage-portal/basic.png)

Se você quiser mais informações sobre o pool, clique em qualquer uma das informações disponíveis nessa visão geral. Ao clicar no gráfico **Utilização de Recursos**, você será direcionado para o modo de exibição do Monitoramento do Azure, onde poderá personalizar a janela de tempo e métricas mostrada no gráfico. Ao clicar em qualquer notificação disponível, você será direcionado para uma folha que exibe todos os detalhes desse alerta ou recomendação.

Se você quiser monitorar os bancos de dados dentro do pool, clique em **Utilização de recursos de banco de dados** na seção**Monitoramento** do menu de recursos esquerdo.

![Página Utilização de recursos do banco de dados](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Para personalizar a exibição do gráfico

Edite o gráfico e a página de métrica para exibir outras métricas como percentual da CPU, percentual de E/S de dados e percentual de E/S de log usado.

No formulário **Editar Gráfico**, é possível selecionar um intervalo de tempo fixo ou clicar em **personalizar** para selecionar qualquer janela de 24 horas nas últimas duas semanas e, em seguida, selecionar os recursos a serem monitorados.

### <a name="to-select-databases-to-monitor"></a>Para selecionar os bancos de dados a serem monitorados

Por padrão, o gráfico na folha **Utilização de Recursos de Banco de Dados** mostrará os 5 principais bancos de dados por DTU ou CPU (dependendo da camada de serviço). É possível alternar os bancos de dados nesse gráfico, selecionando e desmarcando os bancos de dados da lista abaixo do gráfico por meio das caixas de seleção esquerdas.

Você também pode selecionar mais métricas para exibir lado a lado nessa tabela de banco de dados para obter uma exibição mais completa do desempenho dos bancos de dados.

Para obter mais informações, consulte [Criar alertas do Banco de Dados SQL no portal do Azure](sql-database-insights-alerts-portal.md).

## <a name="customer-case-studies"></a>Estudos de caso de cliente

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  O SnelStart usou pools elásticos com o banco de dados SQL do Azure para expandir rapidamente seus serviços de negócios a uma taxa de 1.000 novos bancos de dados SQL do Azure por mês.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  O Umbraco usa pools elásticos com o banco de dados SQL do Azure para provisionar e dimensionar rapidamente os serviços para milhares de locatários na nuvem.

- [Daxko/CSI](https://customers.microsoft.com/story/csi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services)

  O Daxko/CSI usa pools elásticos com o banco de dados SQL do Azure para acelerar seu ciclo de desenvolvimento e aprimorar seus serviços e desempenho do cliente.

## <a name="next-steps"></a>Próximas etapas

- Para dimensionar pools elásticos, confira [Dimensionar pools elásticos](sql-database-elastic-pool-scale.md) e [Dimensionar um pool elástico - exemplo de código](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- Para obter um vídeo, confira [Curso em vídeo da Microsoft Virtual Academy sobre os recursos elásticos do Banco de Dados SQL do Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- Para saber mais sobre padrões de design para aplicativos SaaS usando pools elásticos, confira [Padrões de design para aplicativos de SaaS multilocatários com o banco de dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).
- Para ver um tutorial de SaaS usando pools elásticos, consulte [Introdução ao aplicativo Wingtip SaaS](sql-database-wtp-overview.md).
