---
title: Gerenciar vários bancos de dados com pools elásticos
description: Gerencie e dimensione vários bancos de dados no Azure SQL Database-centenas e milhares-usando pools elásticos. Um preço para os recursos que você pode distribuir quando necessário.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, sstein
ms.date: 12/9/2020
ms.openlocfilehash: c478edf95ae345d64da630400fbf63ac613b73a6
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653628"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>Pools elásticos ajudam a gerenciar e dimensionar vários bancos de dados no Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Os pools elásticos do banco de dados SQL do Azure são uma solução simples e econômica para gerenciar e dimensionar vários bancos de dados que têm demandas de uso variáveis e imprevisíveis. Os bancos de dados em um pool elástico estão em um único servidor e compartilham um número definido de recursos a um preço definido. Os pools elásticos no Banco de Dados SQL do Azure permitem que desenvolvedores de SaaS otimizem o desempenho de preço para um grupo de bancos de dados dentro de um orçamento prescrito oferecendo elasticidade de desempenho para cada banco de dados.

## <a name="what-are-sql-elastic-pools"></a>O que são pools elásticos SQL

Desenvolvedores de SaaS compilam aplicativos com base em camadas de dados de grande escala compostas por vários bancos de dados. Um padrão de aplicativo comum é provisionar um banco de dados individual para cada cliente. Mas clientes diferentes geralmente têm padrões de uso variados e imprevisíveis, e é difícil prever os requisitos de recursos de cada usuário de banco de dados individual. Tradicionalmente, você tinha duas opções:

- Provisionar excessivamente os recursos com base no uso de pico e pagamento, ou
- Provisionamento insuficiente para poupar custos, às custas do desempenho e satisfação do cliente durante picos.

Os pools elásticos resolvem esse problema, garantindo que os bancos de dados recebam os recursos de desempenho que precisam, quando precisam. Eles fornecem um mecanismo de alocação de recursos simples dentro de um orçamento previsível. Para saber mais sobre padrões de design para aplicativos SaaS usando pools elásticos, confira [Padrões de design para aplicativos de SaaS multilocatários com o banco de dados SQL do Azure](saas-tenancy-app-design-patterns.md).
>
> [!IMPORTANT]
> Não há cobrança por banco de dados para pools elásticos. Você é cobrado por cada hora de existência de um pool no vCores ou eDTU mais elevado, independentemente do uso ou se o pool estava ativo por menos de uma hora.

Os pools elásticos permitem que o desenvolvedor compre recursos para um pool compartilhado por vários bancos de dados para acomodar períodos imprevisíveis de uso por bancos de dados individuais. Você pode configurar recursos para o pool com base no [Modelo de compra baseado em DTU](service-tiers-dtu.md) ou o [Modelo de compra baseado em vCore](service-tiers-vcore.md). O requisito de recurso para um pool é determinado pela utilização agregada dos bancos de dados. A quantidade de recursos disponíveis para o pool é controlada pelo orçamento do desenvolvedor. O desenvolvedor simplesmente adiciona bancos de dados ao pool, opcionalmente, define os recursos mínimos e máximos para os bancos de dados (DTUs mínima e máxima ou mínimo ou máximo de vCores dependendo de sua escolha de modelo de origem) e, em seguida, define os recursos do pool com base em seu orçamento. Um desenvolvedor pode usar pools para aumentar seu serviço com perfeição desde uma startup lean até uma empresa madura em escala cada vez maior.

Dentro do pool, os bancos de dados individuais recebem a flexibilidade para dimensionar automaticamente no conjunto de parâmetros. Sob carga pesada, um banco de dados pode consumir mais recursos para atender à demanda. Bancos de dados sob cargas leves consomem menos e bancos de dados sem carga não consomem recursos. O provisionamento de recursos para o pool inteiro em vez do bancos de dados único simplifica as tarefas de gerenciamento. Além disso, há um orçamento previsível para o pool. Recursos adicionais podem ser adicionados a um pool existente com tempo de inatividade mínimo. Da mesma forma, se recursos adicionais não forem mais necessários, eles poderão ser removidos de um pool existente a qualquer momento. E você pode adicionar ou Remover bancos de dados do pool. Se um banco de dados é previsível por estar subutilizando recursos, mova-o.

> [!NOTE]
> Ao mover os bancos de dados para dentro ou para fora de um conjunto elástico, não há tempo de inatividade, exceto por um breve período de tempo (na ordem de segundos) no final da operação, quando as conexões com o banco de dados são descartadas.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quando um pool elástico do Banco de Dados SQL deve ser considerado

Os pools também são adequados para um grande número de bancos de dados com padrões de utilização específicos. Para um determinado banco de dados, esse padrão é caracterizado por baixa utilização média com picos de utilização relativamente pouco frequentes. Por outro lado, vários bancos de dados com utilização de média de alta persistente não devem ser colocados no mesmo pool elástico.

Quanto mais bancos de dados você conseguir adicionar a um pool, maior será a sua economia. Dependendo do padrão de utilização do aplicativo, é possível ver economias com apenas dois bancos de dados S3.

As seções a seguir ajudam a compreender como avaliar se sua coleção específica de bancos de dados se beneficia de estar em um pool. Os exemplos usam pools Standard, mas os mesmos princípios também se aplicam aos pools Basic e Premium.

### <a name="assessing-database-utilization-patterns"></a>Avaliar os padrões de utilização do banco de dados

A figura a seguir mostra um exemplo de um banco de dados que passa muito tempo ocioso, mas também apresenta picos de atividade periodicamente. Este é um padrão de utilização adequado para um pool:

   ![um banco de dados individual adequado para um pool](./media/elastic-pool-overview/one-database.png)

O gráfico ilustra o uso de DTU em um período de 1 hora de 12:00 a 1:00, em que cada ponto de dados tem uma granularidade de 1 minuto. A 12:10 DB1 atinge picos de até 90 DTUs, mas seu uso médio geral é inferior a cinco DTUs. O tamanho da computação S3 é necessário para executar essa carga de trabalho em um banco de dados individual, mas esse tamanho faz com que a maioria dos recursos fiquem utilizados durante períodos de baixa atividade.

Um pool permite que essas DTUs não utilizadas sejam compartilhadas entre vários bancos de dados, reduzindo as DTUs necessárias e os custos gerais.

Considerando ainda o exemplo anterior, suponha que há outros bancos de dados com padrões de utilização semelhantes ao do DB1. Nas próximas duas figuras abaixo, a utilização de quatro bancos de dados e 20 bancos de dados é sobreposta no mesmo gráfico para ilustrar a natureza não sobreposta da utilização ao longo do tempo usando o modelo de compra baseado em DTU:

   ![quatro bancos de dados com um padrão de utilização adequado para um pool](./media/elastic-pool-overview/four-databases.png)

   ![vinte bancos de dados com um padrão de utilização adequado para um pool](./media/elastic-pool-overview/twenty-databases.png)

A utilização de DTU agregada em todos os 20 bancos de dados é ilustrada pela linha preta na figura anterior. Ela mostra que a utilização de DTU agregada nunca excede 100 DTUs e indica que os 20 bancos de dados podem compartilhar 100 eDTUs durante esse período de tempo. Isso resulta em uma redução de 20 vezes das DTUs e de 13 vezes do preço em comparação à escolha dos tamanhos da computação S3 para cada banco de dados individual.

Este exemplo é ideal pelas seguintes razões:

- Há grandes diferenças entre o pico de utilização e a utilização média por banco de dados.
- O pico de utilização de cada banco de dados ocorre em diferentes momentos.
- eDTUs são compartilhados entre vários bancos de dados.

No modelo de compra DTU, o preço de um pool é uma função do pool eDTUs. Embora o preço unitário de eDTU para um pool seja 1,5x maior que o preço unitário de DTU para um banco de dados individual, **as eDTUs do pool podem ser compartilhadas por vários bancos de dados e, assim, menos eDTUs são necessárias no total**. Essas distinções no preço e compartilhamento de eDTU são a base do potencial de economia que os pools podem oferecer.

No modelo de compra vCore, o preço unitário vCore para pools elásticos é igual ao preço unitário vCore para bancos de dados individuais.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Como fazer para escolher o tamanho de pool correto

O melhor tamanho para um pool depende dos recursos agregados necessários para todos os bancos de dados no pool. Isso inclui determinar o seguinte:

- Máximo de recursos de computação utilizados por todos os bancos de dados no pool.  Os recursos de computação são indexados por eDTUs ou vCores, dependendo da sua escolha de modelo de compra.
- Bytes de armazenamento máximo utilizados por todos os bancos de dados no pool.

Para camadas de serviço e limites de recursos em cada modelo de compra, consulte o [modelo de compra baseado em DTU](service-tiers-dtu.md) ou o [modelo de compra baseado em vCore](service-tiers-vcore.md).

As etapas a seguir podem ajudá-lo a estimar se um pool é mais econômico do que bancos de dados individuais:

1. Faça estimativa de eDTUs ou vCores necessários para o pool, conforme a seguir:
   - Para o modelo de compra baseado em DTU:
     - Max (<*número total de máximo de bancos de> de* &times; *utilização de DTU por BD* , <*número de bancos de os de pico simultâneos* , &times; *pico de utilização de DTU por BD*>)
   - Para o modelo de compra baseado em vCore:
     - Max (<*número total de máximo de bancos de> de* &times; *utilização de VCORE por BD* , <*número de bancos de los de pico simultâneo de pico de* &times; *utilização do VCORE por BD*>)
2. Estime o espaço de armazenamento total necessário para o pool adicionando o tamanho de dados necessário para todos os bancos de dado no pool. Para o modelo de compra de DTU, determine o tamanho do pool de eDTU que fornece essa quantidade de armazenamento.
3. Para o modelo de compra baseado em DTU, obtenha as maiores estimativas de eDTU da Etapa 1 e Etapa 2. Para o modelo de compra baseado em vCore, obtenha a estimativa de vCore da Etapa 1.
4. Consulte a [página de preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) e localize o menor tamanho de pool que seja maior que a estimativa da Etapa 3.
5. Compare o preço do pool da etapa 4 com o preço de usar os tamanhos de computação apropriados para bancos de dados individuais.

> [!IMPORTANT]
> Se o número de bancos de dados em um pool se aproximar do máximo com suporte, certifique-se de considerar o [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md).

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Usando outros recursos de Banco de Dados SQL com pools elásticos

### <a name="elastic-jobs-and-elastic-pools"></a>Trabalhos e pools elásticos

Com um pool, as tarefas de gerenciamento são simplificadas com a execução de scripts em **[trabalhos elásticos](elastic-jobs-overview.md)**. Um trabalho elástico elimina a maioria do tédio associado a um grande número de bancos de dados.

Para saber mais sobre outras ferramentas de banco de dados para trabalhar com vários bancos de dados, veja [Expansão com o Banco de Dados SQL do Azure](elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opções de continuidade dos negócios para bancos de dados em um pool elástico

Os bancos de dados em pool normalmente dão suporte aos mesmos [recursos de continuidade dos negócios](business-continuity-high-availability-disaster-recover-hadr-overview.md) disponíveis para bancos de dados individuais.

- **Restauração em um momento determinado**

  A restauração pontual usa backups de banco de dados automáticos para recuperar um banco de dados em um pool para um ponto específico no tempo. Confira [Restauração pontual](recovery-using-backups.md#point-in-time-restore)

- **Restauração geográfica**

  A restauração geográfica fornecerá a opção de recuperação padrão quando um banco de dados estiver indisponível devido a um incidente na região onde está hospedado. Confira [Restaurar um Banco de Dados SQL do Azure ou fazer failover para um secundário](disaster-recovery-guidance.md)

- **Replicação geográfica ativa**

  Para aplicativos que têm requisitos de recuperação mais agressivos do que a restauração geográfica pode oferecer, configure a [replicação geográfica ativa](active-geo-replication-overview.md) ou um [grupo de failover automático](auto-failover-group-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Criar um novo pool elástico de um Banco de Dados SQL usando o Portal do Azure

Há duas maneiras de criar um pool elástico no Portal do Azure.

1. Vá para a [portal do Azure](https://portal.azure.com) para criar um pool elástico. Pesquise e selecione **SQL do Azure**.
2. Selecione **+Adicionar** para abrir a página **Selecionar opção de implantação do SQL**. Você pode exibir informações adicionais sobre pools elásticos selecionando **Mostrar detalhes** no bloco **bancos de dados** .
3. No bloco **bancos de dados** , selecione **pool elástico** na lista suspensa **tipo de recurso** e, em seguida, selecione **criar**:

   ![Criar um pool elástico](./media/elastic-pool-overview/create-elastic-pool.png)

4. Ou você pode criar um pool elástico navegando até um servidor existente e clicando em **+ novo pool** para criar um pool diretamente nesse servidor.

> [!NOTE]
> Você pode criar vários pools em um servidor, mas não pode adicionar bancos de dados de servidores diferentes ao mesmo pool.

A camada de serviço do pool determina os recursos disponíveis para os elásticos no pool e a quantidade máxima de recursos disponíveis para cada banco de dados. Para obter mais detalhes, veja Limites de recursos para pools elásticos no [modelo DTU](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Para saber os limites de recurso baseados em vCore para pools elásticos, confira [Limites de recurso baseados em vCore - pools elásticos](resource-limits-vcore-elastic-pools.md).

Para configurar os recursos e preços do pool, clique em **Configurar pool**. Em seguida, selecione uma camada de serviço, adicione bancos de dados ao pool e configure os limites de recursos para o pool e os bancos de dados.

Quando tiver concluído a configuração do pool, você poderá clicar em "Aplicar", nomear o pool e clicar em "OK" para criar o pool.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorar um pool elástico e os bancos de dados

No Portal do Azure, é possível monitorar a utilização de um pool elástico e os bancos de dados no pool. Você também pode criar um conjunto de alterações para o pool elástico e enviar todas as alterações ao mesmo tempo. Essas alterações incluem adicionar ou remover bancos de dados, alterar as configurações de pool elástico ou alterar suas configurações de banco de dados.

Você pode usar as ferramentas internas de [monitoramento](./performance-guidance.md) e [alerta](./alerts-insights-configure-portal.md)de desempenho, combinadas com as classificações de desempenho.  Além disso, o Banco de Dados SQL pode [emitir métrica e logs de recursos](./metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal) para facilitar o monitoramento.

## <a name="customer-case-studies"></a>Estudos de caso de cliente

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  O SnelStart usou pools elásticos com o banco de dados SQL do Azure para expandir rapidamente seus serviços de negócios a uma taxa de 1.000 novos bancos de dados SQL do Azure por mês.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  O Umbraco usa pools elásticos com o banco de dados SQL do Azure para provisionar e dimensionar rapidamente os serviços para milhares de locatários na nuvem.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   O Daxko/CSI usa pools elásticos com o banco de dados SQL do Azure para acelerar seu ciclo de desenvolvimento e aprimorar seus serviços e desempenho do cliente.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre preços, consulte [preços do pool elástico](https://azure.microsoft.com/pricing/details/sql-database/elastic).
- Para dimensionar pools elásticos, confira [Dimensionar pools elásticos](elastic-pool-scale.md) e [Dimensionar um pool elástico - exemplo de código](scripts/monitor-and-scale-pool-powershell.md)
- Para saber mais sobre padrões de design para aplicativos SaaS usando pools elásticos, confira [Padrões de design para aplicativos de SaaS multilocatários com o banco de dados SQL do Azure](saas-tenancy-app-design-patterns.md).
- Para ver um tutorial de SaaS usando pools elásticos, consulte [Introdução ao aplicativo Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md).
- Para saber mais sobre o gerenciamento de recursos em pools elásticos com muitos bancos de dados, consulte [Gerenciamento de recursos em pools elásticos densos](elastic-pool-resource-management.md).
