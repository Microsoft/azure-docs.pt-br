---
title: "Gerenciar vários bancos de dados SQL com pools elásticos – Azure | Microsoft Docs"
description: "Gerenciar e dimensionar vários Bancos de Dados SQL – centenas de milhares – usando pools elásticos. Um preço para os recursos que você pode distribuir quando necessário."
keywords: "vários bancos de dados, recursos de banco de dados, desempenho de banco de dados"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: NA
ms.date: 10/11/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.openlocfilehash: 38c8c83909fe6a68e8850c9ac9d796784c38c70a
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2017
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Os pools elásticos ajudam você a gerenciar e dimensionar vários bancos de dados SQL do Azure

Pools elásticos do Banco de Dados SQL são uma solução simples e econômica para gerenciar e dimensionar a vários bancos de dados com demandas de uso variadas e imprevisíveis. Os bancos de dados em um pool elástico estão em um único servidor de Banco de Dados SQL do Azure e compartilham um número definido de recursos ([Unidades de transação de banco de dados Elástico](sql-database-what-is-a-dtu.md) (eDTUs)) por um preço definido. Os pools elásticos no Banco de Dados SQL do Azure permitem que desenvolvedores de SaaS otimizem o desempenho de preço para um grupo de bancos de dados dentro de um orçamento prescrito oferecendo elasticidade de desempenho para cada banco de dados. 

> [!NOTE]
> Os pools elásticos têm uma disponibilidade geral (DG) em todas as regiões do Azure, exceto na Índia Ocidental, onde atualmente estão no modo de visualização. A GA dos pools elásticos nessa região ocorrerá assim que possível.
>

## <a name="what-are-sql-elastic-pools"></a>O que são pools elásticos SQL? 

Desenvolvedores de SaaS compilam aplicativos com base em camadas de dados de grande escala compostas por vários bancos de dados. Um padrão de aplicativo comum é provisionar um banco de dados individual para cada cliente. Mas diferentes clientes geralmente têm padrões de uso variados e imprevisíveis, e é difícil prever os requisitos de recursos de cada usuário de banco de dados individual. Tradicionalmente, você tinha duas opções: 

- Provisionar excessivamente os recursos com base no uso de pico e pagamento, ou
- Provisionamento insuficiente para poupar custos, às custas do desempenho e satisfação do cliente durante picos. 

Os pools elásticos resolvem esse problema, garantindo que os bancos de dados recebam os recursos de desempenho que precisam, quando precisam. Eles fornecem um mecanismo de alocação de recursos simples dentro de um orçamento previsível. Para saber mais sobre os padrões de design para aplicativos SaaS usando pools Elásticos, confira [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Padrões de Design para aplicativos de SaaS multilocatários com o Banco de Dados SQL).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Os pools permitem ao desenvolvedor comprar eDTUs [(Unidades de Transação de Banco de Dados elástico)](sql-database-what-is-a-dtu.md) para um pool compartilhado por vários bancos de dados para acomodar períodos de uso imprevisíveis em bancos de dados individuais. O requisito de eDTU para um pool é determinado pela utilização de agregação de seus bancos de dados. O número de eDTUs disponíveis para o pool é controlado pelo orçamento do desenvolvedor. O desenvolvedor simplesmente adiciona bancos de dados ao pool, define as eDTUs mínima e máxima para os bancos de dados e define a eDTU do pool com base no seu orçamento. Um desenvolvedor pode usar pools para aumentar seu serviço com perfeição desde uma startup lean até uma empresa madura em escala cada vez maior.

Dentro do pool, os bancos de dados individuais recebem a flexibilidade para dimensionar automaticamente no conjunto de parâmetros. Sob carga pesada, um banco de dados pode consumir mais eDTUs para atender à demanda. Bancos de dados sob cargas leves consumem menos e bancos de dados sem carga não consomem nenhum eDTU. O provisionamento de recursos para o pool inteiro em vez do bancos de dados único simplifica as tarefas de gerenciamento. Além disso, você tem um orçamento previsível para o pool. As eDTUs adicionais podem ser adicionadas a um pool existente sem tempo de inatividade do banco de dados, exceto que os bancos de dados podem precisar ser movidos para fornecer os recursos de computação adicionais para a nova reserva de eDTU. Da mesma forma, se eDTUs extras não forem mais necessários, eles poderão ser removidos de um pool existente a qualquer momento. E você pode adicionar ou subtrair bancos de dados para o pool. Se um banco de dados é previsível por estar subutilizando recursos, mova-o.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Quando um pool elástico do Banco de Dados SQL deve ser considerado?

Pools também são indicados para um grande número de bancos de dados com padrões de utilização específicos. Para um determinado banco de dados, esse padrão é caracterizado por baixa utilização média com picos de utilização relativamente pouco frequentes.

Quanto mais bancos de dados você conseguir adicionar a um pool, maior será a sua economia. Dependendo de seu padrão de utilização do aplicativo, é possível ver economias com apenas dois bancos de dados S3. 

As seções a seguir ajudam a compreender como avaliar se sua coleção específica de bancos de dados se beneficia de estar em um pool. Os exemplos usam pools Standard, mas os mesmos princípios também se aplicam aos pools Basic e Premium.

### <a name="assessing-database-utilization-patterns"></a>Avaliar os padrões de utilização do banco de dados

A figura a seguir mostra um exemplo de um banco de dados que passa muito tempo ocioso, mas também apresenta picos de atividade periodicamente. Este é um padrão de utilização adequado para um pool:

   ![um banco de dados individual adequado para um pool](./media/sql-database-elastic-pool/one-database.png)

Para o período de cinco minutos ilustrado, DB1 apresenta picos de até 90 DTUs, mas seu uso geral médio é inferior a cinco DTUs. Um nível de desempenho S3 é necessário para executar essa carga de trabalho em um banco de dados individual, mas isso deixa a maioria dos recursos não utilizados durante períodos de baixa atividade.

Um pool permite que essas DTUs não utilizadas sejam compartilhadas entre vários bancos de dados, reduzindo as DTUs necessárias e os custos gerais.

Considerando ainda o exemplo anterior, suponha que há outros bancos de dados com padrões de utilização semelhantes ao do DB1. Nas próximas duas figuras abaixo, a utilização de quatro e 20 bancos de dados é disposta no mesmo gráfico para ilustrar como sua utilização não se sobrepõe ao longo do tempo:

   ![quatro bancos de dados com um padrão de utilização adequado para um pool](./media/sql-database-elastic-pool/four-databases.png)

   ![vinte bancos de dados com um padrão de utilização adequado para um pool](./media/sql-database-elastic-pool/twenty-databases.png)

A utilização de DTU agregada em todos os 20 bancos de dados é ilustrada pela linha preta na figura anterior. Ela mostra que a utilização de DTU agregada nunca excede 100 DTUs e indica que os 20 bancos de dados podem compartilhar 100 eDTUs durante esse período de tempo. Isso resulta em uma redução de 20x em DTUs e em uma redução de 13x no preço quando comparado à colocação de cada um dos bancos de dados nos níveis de desempenho S3 para bancos de dados individuais.

Este exemplo é ideal pelas seguintes razões:

* Há grandes diferenças entre o pico de utilização e a utilização média por banco de dados. 
* O pico de utilização de cada banco de dados ocorre em diferentes momentos.
* eDTUs são compartilhados entre vários bancos de dados.

O preço de um pool é uma função das eDTUs do pool. Embora o preço unitário de eDTU para um pool seja 1,5x maior que o preço unitário de DTU para um banco de dados individual, **as eDTUs do pool podem ser compartilhadas por vários bancos de dados e, assim, menos eDTUs são necessárias no total**. Essas distinções no preço e compartilhamento de eDTU são a base do potencial de economia que os pools podem oferecer. 

As seguintes regras básicas relacionadas à contagem e utilização de banco de dados ajudam a garantir que um pool ofereça um custo reduzido em comparação ao uso de níveis de desempenho de bancos de dados individuais.

### <a name="minimum-number-of-databases"></a>Número mínimo de bancos de dados

Se a soma das DTUs dos níveis de desempenho para bancos de dados individuais for maior que 1,5x as eDTUs necessárias para o pool, um pool elástico será mais econômico. Para ver os tamanhos disponíveis, consulte [Limites de eDTU e armazenamento para pools e bancos de dados elásticos](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

***Exemplo***<br>
pelo menos dois bancos de dados S3 ou 15 bancos de dados S0 são necessários para um pool de 100 eDTUs ser mais econômico do que o uso de níveis de desempenho para bancos de dados individuais.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Número máximo de banco de dados em pico simultaneamente

Ao compartilhar eDTUs, nem todos os bancos de dados em um pool podem usar as eDTUs simultaneamente até o limite disponível ao usar níveis de desempenho de bancos de dados individuais. Quanto menos bancos de dados em pico simultaneamente, menor poderá ser o eDTU do pool e mais econômico ainda ele se torna. Em geral, no máximo 2/3 (ou 67%) dos bancos de dados no pool deve atingir o pico simultaneamente de seus limites de eDTU.

***Exemplo***<br>
para reduzir os custos de três bancos de dados S3 em um pool com 200 eDTUs, no máximo dois desses bancos de dados podem atingir simultaneamente o pico em sua utilização. Caso contrário, se mais de dois desses quatro bancos de dados S3 entrarem em pico simultaneamente, o pool precisará ser dimensionado para mais de 200 eDTUs. Se o pool for redimensionado para mais de 200 eDTUs, mais bancos de dados S3 precisarão ser adicionados ao pool para manter os custos menores do que os níveis de desempenho de bancos de dados individuais.

Observe que esse exemplo não considera a utilização de outros bancos de dados no pool. Se todos os bancos de dados tiverem uma certa utilização em um determinado momento, menos de 2/3 (ou 67%) dos bancos de dados pode atingir o pico simultaneamente.

### <a name="dtu-utilization-per-database"></a>Utilização de DTU por banco de dados
Uma grande diferença entre o máximo e média de utilização de um banco de dados indica longos períodos de baixa utilização e curtos períodos de alta utilização. Esse padrão de utilização é ideal para compartilhar recursos entre bancos de dados. Um banco de dados deve ser considerado para um pool quando seu pico de utilização for aproximadamente 1,5 vez maior que sua utilização média.

***Exemplo***<br>
um banco de dados S3 com picos de 100 DTUs e que usa em média 67 DTUs, ou menos, é um bom candidato para o compartilhamento de eDTUs em um pool. Outra opção de bom candidato para um pool elástico seria um banco de dados S1 com pico de 20 DTUs e média de uso de 13 DTUs ou menos.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Como fazer para escolher o tamanho de pool correto?

O melhor tamanho de um pool depende das eDTUs e recursos de armazenamento agregados necessários para todos os bancos de dados no pool. Isso inclui determinar a maior das quantidades a seguir:

* DTUs máximo utilizadas por todos os bancos de dados no pool.
* Bytes de armazenamento máximo utilizados por todos os bancos de dados no pool.

Para ver os tamanhos disponíveis, consulte [Limites de eDTU e armazenamento para pools e bancos de dados elásticos](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

O Banco de Dados SQL avalia automaticamente a utilização histórica de recursos dos bancos de dados em um servidor de Banco de Dados SQL existente e recomenda a configuração de pool apropriada no portal do Azure. Além das recomendações, uma experiência interna calcula o uso de eDTU para um grupo personalizado de bancos de dados no servidor. Isso permite que você faça uma análise "e se" interativamente adicionando bancos de dados ao pool e removendo-os para obter a análise de uso de recursos e conselhos de dimensionamento antes de confirmar as alterações. Para obter instruções, confira [Monitorar, gerenciar e dimensionar um pool elástico](sql-database-elastic-pool-manage-portal.md).

Em casos em que você não pode usar as ferramentas, os procedimentos passo a passo a seguir podem ajudá-lo a estimar se um pool é mais econômico do que bancos de dados individuais:

1. Estime as eDTUs necessárias para o pool da seguinte maneira:

   MAX (<*Número total de bancos de dados* X *utilização média de DTU por banco de dados*>,<br>
   <*Número de bancos de dados em pico simultaneamente* X *Utilização de DTU em pico por banco de dados*)
2. Estime o espaço de armazenamento necessário para o pool adicionando o número de bytes necessários para todos os bancos de dados no pool. Determine o tamanho do pool em eDTU que fornece essa quantidade de armazenamento. Para ver os limites de armazenamento em pool baseados no tamanho do pool em eDTUs, confira [eDTU and storage limits for elastic pools and elastic databases](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels)(Limites de armazenamento e eDTU para pools elásticos e bancos de dados elásticos).
3. Escolha a maior entre as estimativas de eDTU das etapas 1 e 2.
4. Consulte a [Página de preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/) e localize o menor pool de eDTU que seja maior que a estimativa da Etapa 3.
5. Compare o preço do pool da Etapa 5 com o preço de usar os níveis de desempenho adequados para bancos de dados individuais.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Usando outros recursos de Banco de Dados SQL com pools elásticos

### <a name="elastic-jobs-and-elastic-pools"></a>Trabalhos e pools elásticos

Com um pool, as tarefas de gerenciamento são simplificadas com a execução de scripts em **[trabalhos elásticos](sql-database-elastic-jobs-overview.md)**. Um trabalho elástico elimina a maioria do tédio associado a um grande número de bancos de dados. Para começar, consulte [Introdução aos Trabalhos Elásticos](sql-database-elastic-jobs-getting-started.md).

Para saber mais sobre outras ferramentas de banco de dados para trabalhar com vários bancos de dados, veja [Expansão com o Banco de Dados SQL](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Opções de continuidade dos negócios para bancos de dados em um pool elástico
Os bancos de dados elásticos normalmente dão suporte aos mesmos [recursos de continuidade dos negócios](sql-database-business-continuity.md) disponíveis para bancos de dados individuais.

- **Recuperação pontual**: a recuperação pontual usa backups de banco de dados automáticos para recuperar um banco de dados em um pool para um ponto no tempo específico. Confira [Restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore)

- **Restauração geográfica**: a restauração geográfica fornecerá a opção de recuperação padrão quando um banco de dados estiver indisponível devido a um incidente na região em que ele está hospedado. Confira [Restaurar um Banco de Dados SQL ou fazer failover para um secundário](sql-database-disaster-recovery.md)

- **Replicação geográfica ativa**: para aplicativos que têm requisitos de recuperação mais agressivos do que a restauração geográfica pode oferecer, configure a [replicação geográfica ativa](sql-database-geo-replication-overview.md).

## <a name="manage-elastic-pools-and-databases-using-the-azure-portal"></a>Gerenciar pools elásticos e bancos de dados usando o portal do Azure

### <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Criar um novo pool elástico de um Banco de Dados SQL usando o Portal do Azure

Há duas maneiras de criar um pool elástico no Portal do Azure. É possível fazer isso do zero, se você souber a configuração de pool que deseja, ou começar com uma recomendação do serviço. O banco de dados SQL possui inteligência interna que recomenda uma configuração de um pool elástico, caso seja mais econômico com base na telemetria de uso anterior de seus bancos de dados. 

A criação de um pool elástico com base em uma página de servidor existente no portal é a maneira mais fácil de mover os bancos de dados existentes para um pool elástico. Também crie um pool elástico pesquisando **pool elástico do SQL** no **Marketplace** ou clicando em **+Adicionar** na página de pools elásticos do SQL. Você pode especificar um servidor novo ou existente por este fluxo de trabalho de provisionamento de pool.

> [!NOTE]
> Você pode criar vários pools em um servidor, mas não pode adicionar bancos de dados de servidores diferentes ao mesmo pool.
> 

O tipo de preço do pool determina os recursos disponíveis para os elásticos no pool, o número máximo de eDTUs (MÁX DE eDTUs) e o armazenamento (GBs) disponível para cada banco de dados. Para obter detalhes, consulte [Limites de recursos para pools elásticos](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Para alterar o tipo de preço do pool, clique em **Tipo de preço**, clique no tipo desejado e em **Selecionar**.

> [!IMPORTANT]
> Depois de escolher o tipo de preço e confirmar suas alterações clicando em **OK** na última etapa, não será possível alterar o tipo de preço do pool. Para alterar o tipo de preço para um pool elástico existente, crie um pool elástico no tipo de preço desejado e migre os bancos de dados para esse pool novo.
>

Se os bancos de dados nos quais você está trabalhando tiverem telemetria de uso histórico suficiente, o gráfico **Uso estimado de eDTU e GB** e o gráfico de barras **Uso real de eDTU** serão atualizados para ajudá-lo a tomar decisões de configuração. Além disso, o serviço pode fornecer uma mensagem de recomendação para ajudá-lo a planejar o tamanho do pool.

O serviço do Banco de Dados SQL avalia o histórico de uso e recomenda um ou mais pools quando essa alternativa for mais econômica do que usar bancos de dados individuais. Cada recomendação é configurada com um subconjunto exclusivo de bancos de dados do servidor que melhor se enquadram no pool.

![pool recomendado](./media/sql-database-elastic-pool-create-portal/recommended-pool.png) 

A recomendação de pool inclui:

- Um tipo de preço para o pool (Básico, Standard, Premium ou Premium RS)
- Os **eDTUs do POOL** apropriados (também chamados de Máx. de eDTUs por pool)
- O **MÁX. DE eDTU** e o **MÍN. DE eDTU** por banco de dados
- A lista dos bancos de dados recomendados para o pool

> [!IMPORTANT]
> O serviço leva em consideração os últimos 30 dias de telemetria ao recomendar os pools. Para que um banco de dados seja considerado como candidato para um pool elástico, ele deve existir há pelo menos sete dias. Os bancos de dados que já estão em um pool elástico não são considerados candidatos para as recomendações de pool elástico.
>

O serviço avalia os recursos necessários e o custo-benefício de mover os bancos de dados individuais em cada camada de serviço para os pools na mesma camada. Por exemplo, todos os bancos de dados padrão em um servidor são avaliados para sua adaptação em um pool elástico Standard. Isso significa que o serviço não faz recomendações entre camadas diferentes, como mover um banco de dados Standard para um pool Premium.

Depois de adicionar os bancos de dados ao pool, as recomendações são geradas dinamicamente com base no uso histórico dos bancos de dados selecionados. Essas recomendações são mostradas no gráfico de uso de eDTU e GB e em uma faixa de recomendação na parte superior da página **Configurar pool**. Essas recomendações devem ajudar na criação de um pool elástico otimizado para bancos de dados específicos.

![Recomendações dinâmicas](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

### <a name="manage-and-monitor-an-elastic-pool"></a>Gerenciamento e monitoração um pool elástico

No Portal do Azure, é possível monitorar a utilização de um pool elástico e os bancos de dados no pool. Você também pode criar um conjunto de alterações para o pool elástico e enviar todas as alterações ao mesmo tempo. Essas alterações incluem adicionar ou remover bancos de dados, alterar as configurações de pool elástico ou alterar suas configurações de banco de dados.

O gráfico a seguir mostra um exemplo de pool elástico. A exibição inclui:

* Gráficos para monitorar o uso do recurso de pool elástico e os bancos de dados contidos no pool.
* O botão do pool **Configurar** para fazer alterações ao pool elástico.
* O botão **Criar banco de dados**, que cria um banco de dados e o adiciona ao pool elástico atual.
* Trabalhos elásticos que ajudam a gerenciar muitos bancos de dados executando scripts Transact SQL em todos os bancos de dados em uma lista.

![Exibição de pool](./media/sql-database-elastic-pool-manage-portal/basic.png)

Você pode ir para um determinado pool para ver sua utilização de recursos. Por padrão, o pool está configurado para mostrar o uso de armazenamento e de eDTU na última hora. O gráfico pode ser configurado para mostrar métricas diferentes em vários períodos. Clique no gráfico **Utilização de recursos** abaixo de **Monitoramento de pool elástico** para mostrar uma exibição detalhada das métricas especificadas durante a janela de tempo especificado.

![Monitoramento de pool elástico](./media/sql-database-elastic-pool-manage-portal/basic-2.png)

![Página Métrica](./media/sql-database-elastic-pool-manage-portal/metric.png)

### <a name="to-customize-the-chart-display"></a>Para personalizar a exibição do gráfico

Edite o gráfico e a página de métrica para exibir outras métricas como percentual da CPU, percentual de E/S de dados e percentual de E/S de log usado.

![Clique em editar](./media/sql-database-elastic-pool-manage-portal/edit-metric.png)

No formulário **Editar Gráfico**, selecione um intervalo de tempo (última hora, hoje ou semana passada) ou clique em **personalizado** para selecionar um intervalo de datas nas duas últimas semanas. Você pode escolher entre um gráfico de barras ou linhas e selecionar os recursos que serão monitorados.

> [!Note]
> Somente as métricas com a mesma unidade de medida podem ser exibidas no gráfico ao mesmo tempo. Por exemplo, caso selecione "percentual de eDTU", você só pode selecionar outras métricas com porcentagem como a unidade de medida.
>

![Clique em editar](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

### <a name="manage-and-monitor-databases-in-an-elastic-pool"></a>Gerenciamento e monitoração de bancos de dados um pool elástico

Bancos de dados individuais também podem ser monitorados para identificar potenciais problemas. Em **Monitoramento de Banco de Dados Elástico**, há um gráfico que exibe as métricas para cinco bancos de dados. Por padrão, o gráfico exibe os 5 principais bancos de dados no pool por uso médio de eDTU na última hora. 

![Monitoramento de pool elástico](./media/sql-database-elastic-pool-manage-portal/basic-3.png)

Clique em **Uso de eDTU para bancos de dados na última hora** em **Monitoramento de banco de dados elástico**. Isso abre **Utilização de Recursos de Banco de Dados** e fornece uma exibição detalhada do uso do banco de dados no pool. Usando a grade na parte inferior da página, você pode selecionar bancos de dados no pool para exibir seu uso no gráfico (até 5 bancos de dados). Você também pode personalizar a janela de métricas e tempo exibida no gráfico clicando em **Editar gráfico**.

![Página Utilização de recursos do banco de dados](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-view"></a>Para personalizar a exibição

É possível editar o gráfico para selecionar um intervalo de tempo (como última hora ou 24 horas) ou clicar em **personalizado** para selecionar um período diferente nas duas últimas semanas.

![Clique em Editar gráfico](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

![Clique em Personalizar](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

Você também pode clicar na lista suspensa **Comparar bancos de dados** para selecionar uma métrica diferente a ser usada ao comparar os bancos de dados.

![Edite o gráfico](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Para selecionar os bancos de dados a serem monitorados

Na lista de banco de dados da página **Utilização de Recursos do Banco de Dados**, encontre bancos de dados específicos examinando as páginas na lista ou digitando o nome de um banco de dados. Use a caixa de seleção para escolher o banco de dados.

![Pesquisar por bandos de dados a serem monitorados](./media/sql-database-elastic-pool-manage-portal/select-dbs.png)


### <a name="add-an-alert-to-an-elastic-pool-resource"></a>Adicionar um alerta para um recurso de pool elástico

Você pode adicionar regras a um pool elástico para enviar emails para pessoas ou cadeias de caracteres de alerta para pontos de extremidade da URL quando o pool elástico atingir um limite de utilização definido.

**Para adicionar um alerta a qualquer recurso:**

1. Clique no gráfico **Utilização de recursos** para abrir a página **Métrica**, clique em **Adicionar alerta** e, em seguida, preencha as informações na página **Adicionar uma regra de alerta** (**Recurso** é configurado automaticamente para ser o pool com o qual você está trabalhando).
2. Digite um **Nome** e uma **Descrição** que identifiquem o alerta para você e os destinatários.
3. Escolha na lista uma **Métrica** para a qual deseja o alerta.

   O gráfico mostra dinamicamente a utilização de recursos para a métrica para ajudá-lo a escolher um limite.

4. Escolha uma **Condição** (maior que, menor que, etc.) e um **Limite**.
5. Escolha um **Período** durante o qual a regra de métrica deve ser atendida antes que o alerta seja disparado.
6. Clique em **OK**.

Para obter mais informações, consulte [Criar alertas do Banco de Dados SQL no portal do Azure](sql-database-insights-alerts-portal.md).

### <a name="move-a-database-into-an-elastic-pool"></a>Mover um banco de dados para um pool elástico

Você pode adicionar ou remover bancos de dados de um pool existente. Os bancos de dados podem estar em outros pools. No entanto, você só pode adicionar bancos de dados que estão no mesmo servidor lógico.

 ![Clique em Configurar pool](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

![Clique em Adicionar ao pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

![Selecione os bancos de dados a serem adicionados](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

![Adições de pool pendentes](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

![Clique em Salvar](./media/sql-database-elastic-pool-manage-portal/click-save.png)

### <a name="move-a-database-out-of-an-elastic-pool"></a>Remover um banco de dados de um pool elástico

![lista de bancos de dados](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

### <a name="change-performance-settings-of-an-elastic-pool"></a>Alterar as configurações de desempenho de um pool elástico

À medida que monitora a utilização de recursos de um pool elástico, você pode perceber que alguns ajustes são necessários. Talvez o pool precise de uma alteração nos limites de armazenamento ou de desempenho. Pode ser útil alterar as configurações de banco de dados no pool. Você pode alterar a configuração do pool a qualquer momento para obter o melhor equilíbrio entre desempenho e custo. Veja [Quando um pool elástico deve ser usado?](sql-database-elastic-pool.md) para saber mais.

Para alterar as eDTUs ou os limites de armazenamento por pool e as eDTUs por banco de dados:

![Utilização de recursos de pool elástico](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

## <a name="manage-elastic-pools-and-databases-using-powershell"></a>Gerenciar pools elásticos e bancos de dados usando o PowerShell

Para criar e gerenciar pools elásticos do Banco de Dados SQL com o Azure PowerShell, use os seguintes cmdlets do PowerShell. Se você precisa instalar ou atualizar o PowerShell, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Para criar e gerenciar bancos de dados, servidores e regras de firewall, consulte [Criar e gerenciar servidores de Banco de Dados SQL do Microsoft Azure e bancos de dados usando o PowerShell](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell). 

> [!TIP]
> Para ver scripts de exemplo do PowerShell, consulte [Criar pools elásticos e mover bancos de dados entre pools e fora de um pool usando o PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) e [Usar o PowerShell para monitorar e dimensionar um pool elástico SQL no Banco de Dados SQL do Microsoft Azure](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Descrição |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Cria um pool de banco de dados elástico em um SQL Server lógico.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Obtém pools elásticos e seus valores de propriedade em um SQL Server lógico.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Modifica as propriedades de um pool de banco de dados elástico em um SQL Server lógico. Por exemplo, use a propriedade **StorageMB** para modificar o armazenamento máximo de um pool elástico.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Exclui um pool de banco de dados elástico de um SQL Server lógico.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Obtém o status de operações em um pool elástico em um SQL Server lógico.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Cria um novo banco de dados em um pool existente ou como um Banco de Dados Individual. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Obtém um ou mais bancos de dados.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Define as propriedades para um banco de dados ou move um banco de dados existente para um pool elástico, para fora dele ou entre pools elásticos.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Remove um banco de dados.|


> [!TIP]
> A criação de muitos bancos de dados em um pool elástico pode levar tempo quando feito usando o portal ou cmdlets do PowerShell que criam apenas um banco de dados individual por vez. Para automatizar a criação em um pool elástico, consulte [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="manage-elastic-pools-and-databases-using-the-azure-cli"></a>Gerenciar pools elásticos e bancos de dados usando a CLI do Azure

Para criar e gerenciar pools elásticos do Banco de Dados SQL com a [CLI do Azure](/cli/azure/overview), use os seguintes comandos do [Banco de Dados SQL da CLI do Azure](/cli/azure/sql/db). Use o [Cloud Shell](/azure/cloud-shell/overview) para executar a CLI no seu navegador ou [instale-o](/cli/azure/install-azure-cli) no macOS, Linux ou Windows. 

> [!TIP]
> Para scripts de exemplo da CLI do Azure, consulte [Usar a CLI para mover um Banco de Dados SQL do Microsoft Azure em um pool elástico SQL](scripts/sql-database-move-database-between-pools-cli.md) e [Usar a CLI do Azure para escalar um pool elástico do SQL no Banco de Dados SQL do Microsoft Azure](scripts/sql-database-scale-pool-cli.md).
>

| Cmdlet | Descrição |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Cria um pool elástico.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Retorna uma lista de pools elásticos em um servidor.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Retorna uma lista de bancos de dados em um pool elástico.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Também inclui as configurações DTU do pool disponível, limites de armazenamento e configurações por banco de dados. Para reduzir o detalhamento, os limites de armazenamento adicionais e as configurações por banco de dados ficam ocultos por padrão.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Atualiza um pool elástico.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|Excluir o pool elástico.|

## <a name="manage-databases-within-elastic-pools-using-transact-sql"></a>Gerenciar bancos de dados em pools elásticos usando o Transact-SQL

Para criar e mover bancos de dados dentro de pools elásticos existentes ou para retornar informações sobre um pool elástico de Banco de Dados SQL com o Transact-SQL, use os seguintes comandos do T-SQL. Você pode emitir esses comandos usando o Portal do Azure, o [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), o [Visual Studio Code](https://code.visualstudio.com/docs) ou qualquer outro programa que pode se conectar a um servidor de Banco de Dados SQL e passar comandos Transact-SQL. Para criar e gerenciar bancos de dados, servidores e regras de firewall, consulte [Criar e gerenciar servidores de Banco de Dados SQL do Microsoft Azure e bancos de dados usando o Transact-SQL](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql).

> [!IMPORTANT]
> Não é possível criar, atualizar ou excluir um pool elástico de Banco de Dados SQL do Microsoft Azure usando o Transact-SQL. Você pode adicionar ou remover bancos de dados de um pool elástico, bem como pode usar DMVs para retornar informações sobre os pools elásticos existentes.
>

| Command | Descrição |
| --- | --- |
|[CREATE DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/create-database-azure-sql-database)|Cria um novo banco de dados em um pool existente ou como um Banco de Dados Individual. Você deve estar conectado ao banco de dados mestre para criar um novo banco de dados.|
| [ALTER DATABASE (Banco de Dados SQL do Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) |Move um banco de dados para dentro de um pool elástico, para fora dele ou entre pools elásticos.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Exclui um banco de dados.|
|[sys.elastic_pool_resource_stats (Banco de Dados SQL do Microsoft Azure)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Retorna estatísticas de uso de recursos de todos os pools de banco de dados elástico em um servidor lógico. Para cada pool de banco de dados elástico, há uma linha para cada janela de relatórios de 15 segundos (quatro linhas por minuto). Isso inclui a utilização de CPU, E/S, log, consumo de armazenamento e solicitações/sessões simultâneas de todos os bancos de dados no pool.|
|[sys.database_service_objectives (Banco de Dados SQL do Azure)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Retorna a edição (nível de serviço), o objetivo do serviço (tipo de preço) e o nome do pool elástico, se houver um, para um Banco de Dados SQL do Azure ou um SQL Data Warehouse do Azure. Se estiver conectado ao banco de dados mestre em um servidor de Banco de Dados SQL do Azure, retornará informações sobre todos os bancos de dados. Para o SQL Data Warehouse do Azure, você deve estar conectado ao banco de dados mestre.|

## <a name="manage-elastic-pools-and-databases-using-the-rest-api"></a>Gerenciar pools elásticos e bancos de dados usando a API REST

Para criar e gerenciar pools elásticos do Banco de Dados SQL, use estas solicitações da API REST.

| Command | Descrição |
| --- | --- |
|[Pools Elásticos – Criar ou Atualizar](/rest/api/sql/elasticpools/createorupdate)|Cria um novo pool elástico ou atualiza um pool elástico existente.|
|[Pools Elásticos – Excluir](/rest/api/sql/elasticpools/delete)|Excluir o pool elástico.|
|[Pools Elásticos – Obter](/rest/api/sql/elasticpools/get)|Obtém um pool elástico.|
|[Pools Elásticos – Listar por Servidor](/rest/api/sql/elasticpools/listbyserver)|Retorna uma lista de pools elásticos em um servidor.|
|[Pools Elásticos – Atualizar](/rest/api/sql/elasticpools/update)|Atualiza um pool elástico existente.|
|[Pools Elásticos Recomendados – Obter](/rest/api/sql/recommendedelasticpools/get)|Obtém um pool elástico recomendado.|
|[Pools Elásticos Recomendados – Listar por Servidor](/rest/api/sql/recommendedelasticpools/listbyserver)|Retorna os pools elásticos recomendados.|
|[Pools Elásticos Recomendados – Listar Métricas](/rest/api/sql/recommendedelasticpools/listmetrics)|Retorna as métricas do pool elástico recomendado.|
|[Atividades do Pool Elástico](/rest/api/sql/elasticpoolactivities)|Retorna as atividades do pool elástico.|
|[Atividades do Banco de Dados de Pool Elástico](/rest/api/sql/elasticpooldatabaseactivities)|Retorna as atividades nos bancos de dados dentro de um pool elástico.|
|[Banco de Dados – Criar ou Atualizar](/rest/api/sql/databases/createorupdate)|Cria um novo banco de dados ou atualiza um banco de dados existente.|
|[Bancos de Dados – Obter](/rest/api/sql/databases/get)|Obtém um banco de dados.|
|[Bancos de Dados – Obter por Pool Elástico](/rest/api/sql/databases/getbyelasticpool)|Obtém um banco de dados dentro de um pool elástico.|
|[Bancos de Dados – Obter por Pool Elástico Recomendado](/rest/api/sql/databases/getbyrecommendedelasticpool)|Obtém um banco de dados dentro de um pool elástico recomendado.|
|[Bancos de Dados – Listar por Pool Elástico](/rest/api/sql/databases/listbyelasticpool)|Retorna uma lista de bancos de dados em um pool elástico.|
|[Bancos de Dados – Listar por Pool Elástico Recomendado](/rest/api/sql/databases/listbyrecommendedelasticpool)|Retorna uma lista de bancos de dados dentro de um pool elástico recomendado.|
|[Bancos de Dados – Listar por Servidor](/rest/api/sql/databases/listbyserver)|Retorna uma lista de bancos de dados em um servidor.|
|[Bancos de Dados – Atualizar](/rest/api/sql/databases/update)|Atualiza um banco de dados existente.|

## <a name="next-steps"></a>Próximas etapas

* Para obter um vídeo, confira [Curso em vídeo da Microsoft Virtual Academy sobre os recursos elásticos do Banco de Dados SQL do Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Para saber mais sobre os padrões de design para aplicativos SaaS usando pools Elásticos, confira [Design Patterns for Multi-tenant SaaS Applications with Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md)(Padrões de Design para aplicativos de SaaS multilocatários com o Banco de Dados SQL).
* Para ver um tutorial de SaaS usando pools elásticos, consulte [Introdução ao aplicativo Wingtip SaaS](sql-database-wtp-overview.md).
