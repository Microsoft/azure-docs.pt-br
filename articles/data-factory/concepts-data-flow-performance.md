---
title: Guia de desempenho e ajuste do fluxo de dados de mapeamento
description: Saiba mais sobre os principais fatores que afetam o desempenho de fluxos de dados de mapeamento no Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 01/29/2021
ms.openlocfilehash: 01c448165e6d1f4d6103c61387298f2d9eb40254
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2021
ms.locfileid: "99222926"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Guia de desempenho e ajuste de fluxos de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O mapeamento de fluxos de dados no Azure Data Factory fornece uma interface sem código para projetar e executar transformações de dados em escala. Se você não conhecer bem os fluxos de dados de mapeamento, consulte a [Visão geral do fluxo de dados de mapeamento](concepts-data-flow-overview.md). Este artigo destaca várias maneiras de ajustar e otimizar seus fluxos de dados para que eles atendam aos benchmarks de desempenho.

Assista ao vídeo abaixo para ver alguns intervalos de exemplo transformando dados com fluxos de dados.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="testing-data-flow-logic"></a>Testando a lógica do fluxo de dados

Ao projetar e testar fluxos de dados da UX do ADF, o modo de depuração permite que você teste interativamente em um cluster Spark ativo. Isso permite que você visualize dados e execute seus fluxos de dados sem esperar que um cluster fique quente. Para saber mais, consulte [Modo de depuração](concepts-data-flow-debug-mode.md).

## <a name="monitoring-data-flow-performance"></a>Monitoramento de desempenho do fluxo de dados

Depois de verificar a lógica de transformação usando o modo de depuração, execute seu fluxo de dados de ponta a ponta como uma atividade em um pipeline. Os fluxos de dados são operados em um pipeline usando a [atividade executar fluxo de dados](control-flow-execute-data-flow-activity.md). A atividade de fluxo de dados tem uma experiência de monitoramento exclusiva em comparação com outras atividades Azure Data Factory que exibe um plano de execução detalhado e um perfil de desempenho da lógica de transformação. Para exibir informações detalhadas de monitoramento de um fluxo de dados, clique no ícone de óculos na saída de execução da atividade de um pipeline. Para saber mais, consulte [Monitoramento de fluxos de dados de mapeamento](concepts-data-flow-monitoring.md).

![Monitor do Fluxo de Dados](media/data-flow/monitoring-details.png "Monitor de Fluxo de Dados 2")

Ao monitorar o desempenho do fluxo de dados, há quatro possíveis afunilamentos a serem verificados:

* Hora de início do cluster
* Lendo de uma fonte
* Hora da transformação
* Gravando em um coletor 

![Monitoramento de fluxo de dados](media/data-flow/monitoring-performance.png "Monitor de Fluxo de Dados 3")

O tempo de inicialização do cluster é o tempo necessário para criar um cluster Apache Spark. Esse valor está localizado no canto superior direito da tela de monitoramento. Os fluxos de dados são executados em um modelo just-in-time em que cada trabalho usa um cluster isolado. Esse tempo de inicialização geralmente leva 3-5 minutos. Para trabalhos sequenciais, isso pode ser reduzido ao habilitar um valor de vida útil. Para obter mais informações, consulte [otimizando o Azure Integration Runtime](#ir).

Os fluxos de dados utilizam um otimizador Spark que reordena e executa sua lógica de negócios em ' estágios ' para realizar o mais rápido possível. Para cada coletor no qual o fluxo de dados grava, a saída de monitoramento lista a duração de cada estágio de transformação, junto com o tempo necessário para gravar dados no coletor. A hora maior é provavelmente o afunilamento do fluxo de dados. Se o estágio de transformação que usa o maior contiver uma origem, talvez você queira examinar ainda mais a otimização do tempo de leitura. Se uma transformação estiver demorando muito tempo, talvez seja necessário reparticionar ou aumentar o tamanho do tempo de execução de integração. Se o tempo de processamento do coletor for grande, talvez seja necessário escalar verticalmente seu banco de dados ou verificar se você não está gerando uma saída para um único arquivo.

Depois de identificar o afunilamento do fluxo de dados, use as estratégias de otimização abaixo para melhorar o desempenho.

## <a name="optimize-tab"></a>Otimizar guia

A guia **otimizar** contém configurações para configurar o esquema de particionamento do cluster Spark. Essa guia existe em todas as transformações de fluxo de dados e especifica se você deseja reparticionar os dados **após** a conclusão da transformação. Ajustar o particionamento fornece controle sobre a distribuição de seus dados em nós de computação e otimizações de localidade de dados que podem ter efeitos positivos e negativos no desempenho geral do fluxo de dados.

![Captura de tela mostra a guia otimizar, que inclui a opção de partição, o tipo de partição e o número de partições.](media/data-flow/optimize.png)

Por padrão, o *uso do particionamento atual* é selecionado, o que instrui Azure data Factory manter o particionamento de saída atual da transformação. À medida que os dados de reparticionamento levam tempo, é recomendável *usar o particionamento atual* na maioria dos cenários. Os cenários em que você talvez queira reparticionar seus dados incluem após agregações e junções que distorcem significativamente seus dados ou ao usar o particionamento de origem em um banco de dados SQL.

Para alterar o particionamento em qualquer transformação, selecione a guia **otimizar** e selecione o botão de opção **definir particionamento** . Você verá uma série de opções de particionamento. O melhor método de particionamento difere com base em seus volumes de dados, chaves candidatas, valores nulos e cardinalidade. 

> [!IMPORTANT]
> A partição única combina todos os dados distribuídos em uma única partição. Essa é uma operação muito lenta que também afeta significativamente todas as transformações e gravações de downstream. A Azure Data Factory é altamente recomendável em relação ao uso dessa opção, a menos que haja um motivo de negócios explícito para fazer isso.

As seguintes opções de particionamento estão disponíveis em todas as transformações:

### <a name="round-robin"></a>Round Robin 

Round Robin distribui dados igualmente entre partições. Use Round Robin quando não tiver bons candidatos importantes para implementar uma estratégia sólida de particionamento inteligente. É possível definir o número de partições físicas.

### <a name="hash"></a>Hash

Azure Data Factory produz um hash de colunas para produzir partições uniformes, de modo que as linhas com valores semelhantes se enquadram na mesma partição. Ao usar a opção de hash, teste a possível distorção de partição. É possível definir o número de partições físicas.

### <a name="dynamic-range"></a>Intervalo dinâmico

O intervalo dinâmico usa intervalos dinâmicos do Spark com base nas colunas ou expressões que você fornecer. É possível definir o número de partições físicas. 

### <a name="fixed-range"></a>Intervalo fixo

Crie uma expressão que forneça um intervalo fixo para valores em suas colunas de dados particionados. Para evitar a distorção de partição, você deve ter uma boa compreensão dos seus dados antes de usar essa opção. Os valores inseridos para a expressão são usados como parte de uma função de partição. É possível definir o número de partições físicas.

### <a name="key"></a>Chave

Se você tiver uma boa compreensão da cardinalidade de seus dados, o particionamento de chave poderá ser uma boa estratégia. O particionamento de chave cria partições para cada valor exclusivo em sua coluna. Você não pode definir o número de partições porque o número é baseado em valores exclusivos nos dados.

> [!TIP]
> Configurar manualmente o esquema de particionamento reembaralha os dados e pode deslocar os benefícios do otimizador do Spark. Uma prática recomendada é não definir manualmente o particionamento, a menos que você precise.

## <a name="logging-level"></a>Nível de log

Se você não precisar de cada execução de pipeline de suas atividades de fluxo de dados para registrar completamente todos os logs de telemetria detalhados, você pode, opcionalmente, definir seu nível de log como "básico" ou "nenhum". Ao executar seus fluxos de dados no modo "detalhado" (padrão), você está solicitando que o ADF Registre totalmente a atividade em cada nível de partição individual durante a transformação dos dados. Isso pode ser uma operação cara, portanto, somente habilitar o modo detalhado ao solucionar problemas pode melhorar o fluxo geral de dados e o desempenho do pipeline. O modo "básico" só registrará as durações de transformação, enquanto "nenhum" fornecerá apenas um resumo das durações.

![Nível de log](media/data-flow/logging.png "Definir nível de log")

## <a name="optimizing-the-azure-integration-runtime"></a><a name="ir"></a> Otimizando o Azure Integration Runtime

Os fluxos de dados são executados em clusters Spark que são girados em tempo de execução. A configuração do cluster usado é definida no IR (Integration Runtime) da atividade. Há três considerações de desempenho a serem feitas ao definir seu tempo de execução de integração: tipo de cluster, tamanho do cluster e vida útil.

Para saber mais sobre como criar um Integration Runtime, consulte [Integration Runtime no Azure Data Factory](concepts-integration-runtime.md).

### <a name="cluster-type"></a>Tipo de cluster

Há três opções disponíveis para o tipo de cluster Spark rotacionado: uso geral, otimizado para memória e otimizado para computação.

Os clusters de **uso geral** são a seleção padrão e serão ideais para a maioria das cargas de trabalho de fluxo de dados. Eles tendem a ser o melhor equilíbrio entre desempenho e custo.

Se o fluxo de dados tiver muitas junções e pesquisas, talvez você queira usar um cluster com **otimização de memória** . Os clusters com otimização de memória podem armazenar mais dados na memória e minimizar quaisquer erros de memória insuficiente que você possa obter. A memória otimizada tem o ponto de preço mais alto por núcleo, mas também tendem a resultar em pipelines mais bem-sucedidos. Se você tiver erros de memória insuficiente ao executar fluxos de dados, mude para uma configuração de Azure IR com otimização de memória. 

A **computação otimizada** não é ideal para fluxos de trabalho ETL e não é recomendada pela equipe de Azure data Factory para a maioria das cargas de trabalho de produção. Para transformações de dados mais simples e sem memória, como filtragem de dados ou adição de colunas derivadas, clusters com otimização de computação podem ser usados a um preço mais barato por núcleo.

### <a name="cluster-size"></a>Tamanho do cluster

Os fluxos de dados distribuem o processamento de dados em nós diferentes em um cluster do Spark para executar operações em paralelo. Um cluster Spark com mais núcleos aumenta o número de nós no ambiente de computação. Mais nós aumentam a potência de processamento do fluxo de dados. O aumento do tamanho do cluster geralmente é uma maneira fácil de reduzir o tempo de processamento.

O tamanho do cluster padrão é de quatro nós de driver e quatro nós de trabalho.  Conforme você processa mais dados, são recomendados clusters maiores. Abaixo estão as opções de dimensionamento possíveis:

| Núcleos de trabalho | Núcleos de driver | Total de núcleos | Observações |
| ------------ | ------------ | ----------- | ----- |
| 4 | 4 | 8 | Não disponível para computação otimizada |
| 8 | 8 | 16 | |
| 16 | 16 | 32 | |
| 32 | 16 | 48 | |
| 64 | 16 | 80 | |
| 128 | 16 | 144 | |
| 256 | 16 | 272 | |

Os fluxos de dados são cobrados no VCORE-h, o que significa que o tamanho do cluster e o fator de tempo de execução para isso. À medida que você escalar verticalmente, o custo do cluster por minuto aumentará, mas o tempo total será reduzido.

> [!TIP]
> Há um teto em quanto o tamanho de um cluster afeta o desempenho de um fluxo de dados. Dependendo do tamanho dos dados, há um ponto em que aumentar o tamanho de um cluster deixará de melhorar o desempenho. Por exemplo, se você tiver mais nós do que partições de dados, adicionar nós adicionais não ajudará. Uma prática recomendada é começar pequena e escalar verticalmente para atender às suas necessidades de desempenho. 

### <a name="time-to-live"></a>Vida útil

Por padrão, cada atividade de fluxo de dados gira um novo cluster com base na configuração de IR. O tempo de inicialização do cluster leva alguns minutos e o processamento de dados não pode ser iniciado até ser concluído. Se seus pipelines contiverem vários fluxos de dados **sequenciais** , você poderá habilitar um valor TTL (vida útil). Especificar um valor de vida útil mantém um cluster ativo por um determinado período de tempo após sua execução ser concluída. Se um novo trabalho começar a usar o IR durante o tempo de vida útil, ele reutilizará o cluster existente e o tempo de inicialização será muito reduzido. Após a conclusão do segundo trabalho, o cluster permanecerá ativo novamente durante o tempo de vida útil.

Somente um trabalho pode ser executado em um único cluster por vez. Se houver um cluster disponível, mas dois fluxos de dados forem iniciados, apenas um usará o cluster ao vivo. O segundo trabalho criará seu próprio cluster isolado.

Se a maioria dos fluxos de dados for executada em paralelo, não é recomendável habilitar o TTL. 

> [!NOTE]
> A vida útil não está disponível ao usar o tempo de execução de integração de resolução automática

## <a name="optimizing-sources"></a>Otimizando fontes

Para cada fonte, exceto o banco de dados SQL do Azure, é recomendável que você continue a **usar o particionamento atual** como o valor selecionado. Ao ler de todos os outros sistemas de origem, os fluxos de dados particionam automaticamente os dados uniformemente com base no tamanho dos dados. Uma nova partição é criada para aproximadamente a cada 128 MB de dados. À medida que o tamanho dos dados aumenta, aumenta o número de partições.

Qualquer particionamento personalizado acontece *depois* que o Spark lê os dados e afeta negativamente o desempenho do fluxo de dados. Como os dados são particionados uniformemente na leitura, isso não é recomendado. 

> [!NOTE]
> As velocidades de leitura podem ser limitadas pela taxa de transferência do sistema de origem.

### <a name="azure-sql-database-sources"></a>Fontes do banco de dados SQL do Azure

O banco de dados SQL do Azure tem uma opção de particionamento exclusiva chamada particionamento de ' origem '. Habilitar o particionamento de origem pode melhorar seus tempos de leitura do banco de BD SQL do Azure habilitando conexões paralelas no sistema de origem. Especifique o número de partições e como particionar seus dados. Use uma coluna de partição com alta cardinalidade. Você também pode inserir uma consulta que corresponda ao esquema de particionamento da tabela de origem.

> [!TIP]
> Para o particionamento de origem, a e/s do SQL Server é o afunilamento. Adicionar muitas partições pode saturar o banco de dados de origem. Geralmente, quatro ou cinco partições são ideais ao usar essa opção.

![Particionamento de origem](media/data-flow/sourcepart3.png "Particionamento de origem")

#### <a name="isolation-level"></a>Nível de isolamento

O nível de isolamento da leitura em um sistema de origem do SQL do Azure tem um impacto no desempenho. Escolher ' ler não confirmado ' fornecerá o desempenho mais rápido e evitará qualquer bloqueio de banco de dados. Para saber mais sobre os níveis de isolamento do SQL, consulte [noções básicas sobre níveis de isolamento](/sql/connect/jdbc/understanding-isolation-levels).

#### <a name="read-using-query"></a>Ler usando consulta

Você pode ler no banco de dados SQL do Azure usando uma tabela ou uma consulta SQL. Se você estiver executando uma consulta SQL, a consulta deverá ser concluída antes que a transformação possa ser iniciada. As consultas SQL podem ser úteis para enviar por push operações que podem ser executadas mais rapidamente e reduzir a quantidade de dados lidos de um SQL Server como instruções SELECT, WHERE e JOIN. Ao enviar operações por push, você perde a capacidade de controlar a linhagem e o desempenho das transformações antes que os dados cheguem ao fluxo de dados.

### <a name="azure-synapse-analytics-sources"></a>Fontes do Azure Synapse Analytics

Ao usar o Azure Synapse Analytics, uma configuração chamada **habilitar preparo** existe nas opções de origem. Isso permite que o ADF leia do Synapse usando ```Staging``` , o que melhora muito o desempenho de leitura. Habilitar ```Staging``` requer que você especifique um armazenamento de BLOBs do Azure ou Azure data Lake Storage local de preparo de Gen2 nas configurações de atividade do fluxo de dados.

![Habilitar preparo](media/data-flow/enable-staging.png "Habilitar preparo")

### <a name="file-based-sources"></a>Fontes baseadas em arquivo

Embora os fluxos de dados ofereçam suporte a uma variedade de tipos de arquivo, o Azure Data Factory recomenda o uso do formato Spark-Native parquet para tempos de leitura e gravação ideais.

Se você estiver executando o mesmo fluxo de dados em um conjunto de arquivos, recomendamos a leitura de uma pasta, o uso de caminhos curinga ou a leitura de uma lista de arquivos. Uma execução de atividade de fluxo de dados único pode processar todos os arquivos no lote. Mais informações sobre como definir essas configurações podem ser encontradas na documentação do conector, como [armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#source-transformation).

Se possível, evite usar a atividade For-Each para executar fluxos de dados em um conjunto de arquivos. Isso fará com que cada iteração do para-cada para criar seu próprio cluster Spark, que geralmente não é necessário e pode ser caro. 

## <a name="optimizing-sinks"></a>Otimizando coletores

Quando os fluxos de dados gravam em coletores, qualquer particionamento personalizado ocorrerá imediatamente antes da gravação. Como a origem, na maioria dos casos, é recomendável que você continue a **usar o particionamento atual** como a opção de partição selecionada. Os dados particionados serão gravados significativamente mais rapidamente do que os dados não particionados, até mesmo o destino não é particionado. Abaixo estão as considerações individuais para vários tipos de coletor. 

### <a name="azure-sql-database-sinks"></a>Coletores do banco de dados SQL do Azure

Com o banco de dados SQL do Azure, o particionamento padrão deve funcionar na maioria dos casos. Há uma chance de que seu coletor possa ter muitas partições para o seu banco de dados SQL lidar. Se você estiver executando isso, reduza o número de partições emitidas por seu coletor de banco de dados SQL.

#### <a name="impact-of-error-row-handling-to-performance"></a>Impacto da manipulação de linha de erro no desempenho

Quando você habilita o tratamento de linhas de erro ("continuar se houver erro") na transformação do coletor, o ADF executará uma etapa adicional antes de gravar as linhas compatíveis na tabela de destino. Essa etapa adicional terá uma pequena penalidade de desempenho que pode estar no intervalo de 5% adicionada para essa etapa com um pequeno impacto de desempenho adicional também se você definir a opção como também com as linhas incompatíveis em um arquivo de log.

#### <a name="disabling-indexes-using-a-sql-script"></a>Desabilitando índices usando um script SQL

A desabilitação de índices antes de uma carga em um banco de dados SQL pode melhorar muito o desempenho da gravação na tabela. Execute o comando abaixo antes de gravar no coletor SQL.

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

Após a conclusão da gravação, recompile os índices usando o seguinte comando:

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

Ambos podem ser feitos nativamente usando scripts Pre e post-SQL dentro de um banco de dados SQL do Azure ou coletor Synapse no mapeamento de fluxos.

![Desabilitar índices](media/data-flow/disable-indexes-sql.png "Desabilitar índices")

> [!WARNING]
> Ao desabilitar índices, o fluxo de dados está efetivamente assumindo o controle de um banco e as consultas são improvável de serem bem sucedidos no momento. Como resultado, muitos trabalhos de ETL são disparados no meio da noite para evitar esse conflito. Para obter mais informações, saiba mais sobre as [restrições de desabilitar índices](/sql/relational-databases/indexes/disable-indexes-and-constraints)

#### <a name="scaling-up-your-database"></a>Escalar verticalmente seu banco de dados

Agende um redimensionamento do Banco de Dados SQL do Azure e do DW da origem e do coletor antes de executar o pipeline para aumentar a taxa de transferência e minimizar a limitação do Azure depois de atingir os limites de DTU. Depois que a execução do pipeline for concluída, redimensione os bancos de dados de volta à sua taxa de execução normal.

### <a name="azure-synapse-analytics-sinks"></a>Coletores do Azure Synapse Analytics

Ao gravar no Azure Synapse Analytics, verifique se **habilitar preparo** está definido como true. Isso permite que o ADF grave usando o [comando SQL Copy](/sql/t-sql/statements/copy-into-transact-sql) que efetivamente carrega os dados em massa. Você precisará fazer referência a um Azure Data Lake Storage Gen2 ou conta de armazenamento de BLOBs do Azure para preparo dos dados ao usar o preparo.

Além de preparo, as mesmas práticas recomendadas se aplicam ao Azure Synapse Analytics como banco de dados SQL do Azure.

### <a name="file-based-sinks"></a>Coletores baseados em arquivo 

Embora os fluxos de dados ofereçam suporte a uma variedade de tipos de arquivo, o Azure Data Factory recomenda o uso do formato Spark-Native parquet para tempos de leitura e gravação ideais.

Se os dados forem distribuídos uniformemente, **use o particionamento atual** será a opção de particionamento mais rápida para gravar arquivos.

#### <a name="file-name-options"></a>Opções de nome de arquivo

Ao gravar arquivos, você tem uma opção de opções de nomenclatura que cada um tem um impacto no desempenho.

![Opções de coletor](media/data-flow/file-sink-settings.png "opções de coletor")

A seleção da opção **padrão** gravará o mais rápido. Cada partição será igual a um arquivo com o nome padrão do Spark. Isso será útil se você estiver apenas lendo a partir da pasta de dados.

Definir um **padrão** de nomenclatura renomeará cada arquivo de partição para um nome mais amigável. Essa operação ocorre após a gravação e é um pouco mais lenta do que escolher o padrão. Por partição permite que você nomeie cada partição individual manualmente.

Se uma coluna corresponder ao modo como você deseja gerar os dados, você poderá selecionar **como dados na coluna**. Isso reembaralha os dados e pode afetar o desempenho se as colunas não forem distribuídas uniformemente.

**A saída para um único arquivo** combina todos os dados em uma única partição. Isso leva a longos tempos de gravação, especialmente para grandes conjuntos de altos. A equipe de Azure Data Factory altamente recomenda **não** escolher essa opção, a menos que haja um motivo de negócios explícito para fazer isso.

### <a name="cosmosdb-sinks"></a>Coletores CosmosDB

Ao gravar em CosmosDB, alterar a taxa de transferência e o tamanho do lote durante a execução do fluxo de dados pode melhorar o desempenho. Essas alterações só entram em vigor durante a execução da atividade de fluxo de dados e retornarão às configurações da coleção original após a conclusão. 

**Tamanho do lote:** Normalmente, a partir do tamanho de lote padrão é suficiente. Para ajustar esse valor, calcule o tamanho do objeto aproximado dos dados e verifique se o tamanho do lote * tamanho do objeto é menor que 2MB. Se for, você poderá aumentar o tamanho do lote para obter uma melhor taxa de transferência.

**Taxa de transferência:** Defina uma configuração de taxa de transferência mais alta aqui para permitir que os documentos sejam gravados mais rapidamente no CosmosDB. Tenha em mente os custos de RU maiores com base em uma configuração de alta taxa de transferência.

**Orçamento de taxa de transferência de gravação:** Use um valor que seja menor do que o total de RUs por minuto. Se você tiver um fluxo de dados com um número alto de partições do Spark, a definição de uma taxa de transferência de orçamento permitirá mais saldo entre essas partições.

## <a name="optimizing-transformations"></a>Otimizando transformações

### <a name="optimizing-joins-exists-and-lookups"></a>Otimizando junções, existentes e pesquisas

#### <a name="broadcasting"></a>Difundindo

Em junções, pesquisas e transformações existentes, se um ou ambos os fluxos de dados forem pequenos o suficiente para caber na memória do nó de trabalho, você poderá otimizar o desempenho habilitando a **transmissão**. A difusão é quando você envia quadros de dados pequenos para todos os nós no cluster. Isso permite que o mecanismo do Spark execute uma junção sem embaralhando os dados no fluxo grande. Por padrão, o mecanismo do Spark decidirá automaticamente se deseja ou não difundir um lado de uma junção. Se você estiver familiarizado com seus dados de entrada e souber que um fluxo será significativamente menor do que o outro, poderá selecionar a difusão **fixa** . A difusão fixa força o Spark a transmitir o fluxo selecionado. 

Se o tamanho dos dados transmitidos for muito grande para o nó do Spark, você poderá receber um erro de memória insuficiente. Para evitar erros de memória insuficiente, use clusters com **otimização de memória** . Se você tiver tempos limite de difusão durante execuções de fluxo de dados, poderá desativar a otimização de difusão. No entanto, fará com que os fluxos de dados fiquem mais lentos.

![Otimização da transformação de junção](media/data-flow/joinoptimize.png "Otimização de junção")

#### <a name="cross-joins"></a>Junções cruzadas

Se você usar valores literais em suas condições de junção ou tiver várias correspondências em ambos os lados de uma junção, o Spark executará a junção como uma junção cruzada. Uma junção cruzada é um produto cartesiano completo que filtra os valores associados. Isso é significativamente mais lento do que outros tipos de junção. Verifique se você tem referências de coluna em ambos os lados de suas condições de junção para evitar o impacto no desempenho.

#### <a name="sorting-before-joins"></a>Classificação antes de junções

Ao contrário da junção de mesclagem em ferramentas como o SSIS, a transformação de junção não é uma operação de junção de mesclagem obrigatória. As chaves de junção não exigem classificação antes da transformação. A equipe de Azure Data Factory não recomenda usar transformações de classificação no mapeamento de fluxos de dados.

### <a name="window-transformation-performance"></a>Desempenho de transformação de janela

A [transformação janela](data-flow-window.md) particiona os dados por valor em colunas que você seleciona como parte da ```over()``` cláusula nas configurações de transformação. Há uma série de funções analíticas e de agregação muito populares que são expostas na transformação do Windows. No entanto, se seu caso de uso for gerar uma janela sobre todo o conjunto de linhas para fins de classificação ```rank()``` ou número de linha ```rowNumber()``` , é recomendável que você use a [transformação Classificação](data-flow-rank.md) e a [transformação chave substituta](data-flow-surrogate-key.md). Essas transformações executarão melhor as operações completas de conjunto de usuários usando essas funções.

### <a name="repartitioning-skewed-data"></a>Reparticionando dados distorcidos

Determinadas transformações, como junções e agregações, reembaralham suas partições de dados e, ocasionalmente, podem levar a dados distorcidos. Dados distorcidos significa que os dados não são distribuídos uniformemente entre as partições. Dados intensamente distorcidos podem levar a transformações de downstream mais lentas e gravações de coletor. Você pode verificar a distorção de seus dados em qualquer ponto em um fluxo de dados executado clicando na transformação na exibição monitoramento.

![Distorção e curtose](media/data-flow/skewness-kurtosis.png "Distorção e curtose")

A exibição de monitoramento mostrará como os dados são distribuídos em cada partição junto com duas métricas, distorção e curtose. A **distorção** é uma medida de quão assimétrico os dados são e pode ter um valor positivo, zero, negativo ou indefinido. Distorção negativa significa que a cauda esquerda é maior do que a direita. A **curtose** é a medida que indica se os dados são pesados ou leves. Valores de curtose alta não são desejáveis. Os intervalos ideais de distorção ficam entre-3 e 3 e os intervalos de curto-intervalo são inferiores a 10. Uma maneira fácil de interpretar esses números é examinar o gráfico de partição e ver se 1 barra é significativamente maior do que o restante.

Se os dados não forem particionados uniformemente após uma transformação, você poderá usar a [guia otimizar](#optimize-tab) para reparticionar. Os dados do embaralhando levam tempo e podem não melhorar o desempenho do fluxo de dados.

> [!TIP]
> Se você reparticionar seus dados, mas tiver transformações downstream que reembaralham seus dados, use o particionamento de hash em uma coluna usada como chave de junção.

## <a name="using-data-flows-in-pipelines"></a>Usando fluxos de dados em pipelines 

Ao criar pipelines complexos com vários fluxos de dados, o fluxo lógico pode ter um grande impacto no tempo e no custo. Esta seção aborda o impacto de diferentes estratégias de arquitetura.

### <a name="executing-data-flows-in-parallel"></a>Executando fluxos de dados em paralelo

Se você executar vários fluxos de dados em paralelo, o ADF girará clusters Spark separados para cada atividade. Isso permite que cada trabalho seja isolado e executado em paralelo, mas levará a vários clusters em execução ao mesmo tempo.

Se os fluxos de dados são executados em paralelo, é recomendável não habilitar a propriedade de Azure IR vida útil, pois ela levará a vários pools quentes não utilizados.

> [!TIP]
> Em vez de executar o mesmo fluxo de dados várias vezes em uma para cada atividade, prepare seus dados em um data Lake e use caminhos curinga para processar os dados em um único fluxo de dados.

### <a name="execute-data-flows-sequentially"></a>Executar fluxos de dados sequencialmente

Se você executar suas atividades de fluxo de dados em sequência, é recomendável definir um TTL na configuração de Azure IR. O ADF reutilizará os recursos de computação, resultando em um tempo de inicialização mais rápido do cluster. Cada atividade ainda será isolada receberá um novo contexto do Spark para cada execução.

Executar trabalhos sequencialmente provavelmente levará o tempo mais longo para ser executado de ponta a ponta, mas fornece uma separação limpa das operações lógicas.

### <a name="overloading-a-single-data-flow"></a>Sobrecarregando um fluxo de dados único

Se você colocar toda a lógica dentro de um fluxo de dados único, o ADF executará todo o trabalho em uma única instância do Spark. Embora isso possa parecer uma maneira de reduzir os custos, ele combina fluxos lógicos diferentes e pode ser difícil de monitorar e depurar. Se um componente falhar, todas as outras partes do trabalho falharão também. A equipe de Azure Data Factory recomenda organizar os fluxos de dados por fluxos independentes de lógica de negócios. Se o fluxo de dados ficar muito grande, dividi-lo em componentes separados tornará o monitoramento e a depuração mais fáceis. Embora não haja nenhum limite rígido no número de transformações em um fluxo de dados, ter muitos torna o trabalho complexo.

### <a name="execute-sinks-in-parallel"></a>Executar coletores em paralelo

O comportamento padrão dos coletores de fluxo de dados é executar cada coletor sequencialmente, de maneira serial, e falhar no fluxo de dados quando um erro for encontrado no coletor. Além disso, todos os coletores são padronizados para o mesmo grupo, a menos que você vá para as propriedades de fluxo de dados e defina prioridades diferentes para os coletores.

Os fluxos de dados permitem agrupar coletores em grupos da guia Propriedades de fluxo de dados no designer de interface do usuário. Você pode definir a ordem de execução de seus coletores, bem como agrupar coletores usando o mesmo número de grupo. Para ajudar a gerenciar grupos, você pode pedir que o ADF execute coletores no mesmo grupo, para ser executado em paralelo.

Na atividade pipeline, executar fluxo de dados na seção "Propriedades do coletor" é uma opção para ativar o carregamento paralelo do coletor. Quando você habilita "executar em paralelo", está instruindo a gravação de fluxos de dados a coletores conectados ao mesmo tempo em vez de de maneira sequencial. Para utilizar a opção Parallel, os coletores devem ser agrupados e conectados ao mesmo fluxo por meio de uma nova ramificação ou divisão condicional.

## <a name="next-steps"></a>Próximas etapas

Consulte outros artigos sobre Fluxo de Dados relacionados ao desempenho:

- [Atividade de Fluxo de Dados](control-flow-execute-data-flow-activity.md)
- [Monitorar o desempenho de Fluxo de Dados](concepts-data-flow-monitoring.md)