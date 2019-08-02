---
title: Mapeando o guia de desempenho e ajuste do fluxo de dados no Azure Data Factory | Microsoft Docs
description: Saiba mais sobre os principais fatores que afetam o desempenho dos fluxos de dados no Azure Data Factory quando você usa o mapeamento de fluxos de dados.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.date: 05/16/2019
ms.openlocfilehash: 090c229c5e97ede8eb7a397ce8f4d13d8735a346
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404616"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Mapeando o guia de desempenho e ajuste do fluxo de dados

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Os fluxos de dados de mapeamento de Azure Data Factory fornecem uma interface de navegador sem código para projetar, implantar e orquestrar transformações de dados em escala.

> [!NOTE]
> Se você não estiver familiarizado com os fluxos de dados de mapeamento do ADF em geral, consulte [visão geral de fluxos de dados](concepts-data-flow-overview.md) antes de ler este artigo.
>

> [!NOTE]
> Quando você estiver projetando e testando fluxos de dados da interface do usuário do ADF, certifique-se de ativar a opção de depuração para que você possa executar os fluxos de dados em tempo real sem esperar que um cluster fique quente.
>

![Botão de depuração](media/data-flow/debugb1.png "Depurar")

## <a name="monitor-data-flow-performance"></a>Monitorar o desempenho do fluxo de dados

Ao criar seus fluxos de dados de mapeamento no navegador, você pode testar cada transformação individual clicando na guia Visualização de dados no painel configurações inferior para cada transformação. A próxima etapa que você deve seguir é testar seu fluxo de dados de ponta a ponta no designer de pipeline. Adicione uma atividade executar fluxo de dados e use o botão depurar para testar o desempenho do fluxo de dados. No painel inferior da janela pipeline, você verá um ícone de eyeglass em "ações":

![Monitor de fluxo de dados](media/data-flow/mon002.png "Monitor de fluxo de dados 2")

Clicar nesse ícone exibirá o plano de execução e o perfil de desempenho subsequente do seu fluxo de dados. Você pode usar essas informações para estimar o desempenho do fluxo de dados em diferentes fontes de dados de tamanho. Observe que você pode supor um minuto de tempo de configuração de execução de trabalho de cluster nos cálculos de desempenho gerais e, se estiver usando o Azure Integration Runtime padrão, talvez seja necessário adicionar 5 minutos de tempo de rotação de cluster também.

![Monitoramento de fluxo de dados](media/data-flow/mon003.png "Monitor de fluxo de dados 3")

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse"></a>Otimizando para o banco de dados SQL do Azure e o Azure SQL Data Warehouse

![Parte de origem](media/data-flow/sourcepart3.png "Parte de origem")

### <a name="partition-your-source-data"></a>Particionar os dados de origem

* Vá para "otimizar" e selecione "origem". Defina uma coluna de tabela específica ou um tipo em uma consulta.
* Se você escolher "coluna", selecione a coluna partição.
* Além disso, defina o número máximo de conexões com seu banco de BD SQL do Azure. Você pode tentar uma configuração mais alta para obter conexões paralelas com seu banco de dados. No entanto, alguns casos podem resultar em um desempenho mais rápido com um número limitado de conexões.
* As tabelas do banco de dados de origem não precisam ser particionadas.
* Definir uma consulta em sua transformação de origem que corresponda ao esquema de particionamento de sua tabela de banco de dados permitirá que o mecanismo de banco de dados de origem Aproveite a eliminação de partição.
* Se sua fonte ainda não estiver particionada, o ADF ainda usará o particionamento de dados no ambiente de transformação do Spark com base na chave que você selecionar na transformação origem.

### <a name="set-batch-size-and-query-on-source"></a>Definir o tamanho do lote e a consulta na origem

![Origem] do (media/data-flow/source4.png "Origem") do

* Definir o tamanho do lote instruirá o ADF a armazenar dados em conjuntos na memória, em vez de linha por linha. É uma configuração opcional e você pode ficar sem recursos nos nós de computação se eles não forem dimensionados corretamente.
* A definição de uma consulta pode permitir que você filtre linhas diretamente na origem antes que elas cheguem ao fluxo de dados para processamento, o que pode tornar a aquisição de dados inicial mais rápida.
* Se você usar uma consulta, poderá adicionar dicas de consulta opcionais para seu BD SQL do Azure, ou seja, ler não confirmada

### <a name="set-isolation-level-on-source-transformation-settings-for-sql-datasets"></a>Definir o nível de isolamento nas configurações de transformação de origem para conjuntos de SQL

* A leitura não confirmada fornecerá resultados de consulta mais rápidos na transformação de origem

![Nível de isolamento](media/data-flow/isolationlevel.png "Nível de isolamento")

### <a name="set-sink-batch-size"></a>Definir tamanho do lote do coletor

![Coletor](media/data-flow/sink4.png "Coletor")

* Para evitar o processamento de linha por linha de seus fluxos de dados, defina o "tamanho do lote" nas configurações do coletor para o BD SQL do Azure. Isso fará com que o ADF processe gravações de banco de dados em lotes com base no tamanho fornecido.

### <a name="set-partitioning-options-on-your-sink"></a>Definir opções de particionamento em seu coletor

* Mesmo que você não tenha seus dados particionados em suas tabelas de destino do BD SQL do Azure, vá para a guia otimizar e defina particionamento.
* Com muita frequência, simplesmente dizer ao ADF para usar o particionamento Round Robin nos clusters de execução do Spark resulta em um carregamento de dados muito mais rápido, em vez de forçar todas as conexões de um único nó/partição.

### <a name="increase-size-of-your-compute-engine-in-azure-integration-runtime"></a>Aumentar o tamanho do seu mecanismo de computação no Azure Integration Runtime

![Novo ir](media/data-flow/ir-new.png "Novo ir")

* Aumente o número de núcleos, o que aumentará o número de nós e fornecerá mais poder de processamento para consultar e gravar em seu BD SQL do Azure.
* Experimente as opções "computação otimizada" e "memória otimizada" para aplicar mais recursos aos nós de computação.

### <a name="unit-test-and-performance-test-with-debug"></a>Teste de unidade e teste de desempenho com depuração

* Quando os dados de teste de unidade fluem, defina o botão "depuração de fluxo de dados" como ativado.
* Dentro do designer de fluxo de dados, use a guia Visualização de dados em transformações para exibir os resultados da lógica de transformação.
* Teste de unidade seus dados fluem do designer de pipeline, colocando uma atividade de fluxo de dados na tela de design do pipeline e usando o botão "depurar" para testar.
* O teste no modo de depuração funcionará em um ambiente de cluster dinâmico quente sem a necessidade de aguardar uma rotação de cluster just-in-time.

### <a name="disable-indexes-on-write"></a>Desabilitar índices na gravação
* Use uma atividade de procedimento armazenado do pipeline do ADF antes de sua atividade de fluxo de dados que desabilita os índices nas tabelas de destino que estão sendo gravadas do seu coletor.
* Após sua atividade de fluxo de dados, adicione outra atividade proc armazenada que habilitou esses índices.

### <a name="increase-the-size-of-your-azure-sql-db"></a>Aumentar o tamanho do seu banco de BD SQL do Azure
* Agende um redimensionamento da origem e do coletor do banco de BD SQL do Azure antes de executar seu pipeline para aumentar a taxa de transferência e minimizar a limitação do Azure depois de atingir os limites de DTU.
* Depois que a execução do pipeline for concluída, você poderá redimensionar os bancos de dados de volta à sua taxa de execução normal.

## <a name="optimizing-for-azure-sql-data-warehouse"></a>Otimizando para o Azure SQL Data Warehouse

### <a name="use-staging-to-load-data-in-bulk-via-polybase"></a>Usar preparo para carregar dados em massa por meio do polybase

* Para evitar o processamento de linha por linha de seus fluxos de dados, defina a opção "preparo" nas configurações do coletor para que o ADF possa aproveitar o polybase para evitar inserções de linha por linha no DW. Isso instruirá o ADF a usar o polybase para que os dados possam ser carregados em massa.
* Ao executar a atividade de fluxo de dados de um pipeline, com o preparo ativado, você precisará selecionar o local do repositório de blob dos dados de preparo para o carregamento em massa.

### <a name="increase-the-size-of-your-azure-sql-dw"></a>Aumentar o tamanho do SQL DW do Azure

* Agende um redimensionamento da origem e do coletor do Azure SQL DW antes de executar o pipeline para aumentar a taxa de transferência e minimizar a limitação do Azure depois de atingir os limites de DWU.

* Depois que a execução do pipeline for concluída, você poderá redimensionar os bancos de dados de volta à sua taxa de execução normal.

## <a name="optimize-for-files"></a>Otimizar para arquivos

* Você pode controlar quantas partições serão usadas pelo ADF. Em cada fonte & transformação do coletor, bem como cada transformação individual, você pode definir um esquema de particionamento. Para arquivos menores, você pode achar que a seleção de "partição única" às vezes pode funcionar melhor e mais rápido do que pedir ao Spark para particionar seus arquivos pequenos.
* Se você não tiver informações suficientes sobre seus dados de origem, poderá escolher particionamento "Round Robin" e definir o número de partições.
* Se você explorar seus dados e achar que tem colunas que podem ser boas chaves de hash, use a opção de particionamento hash.

### <a name="file-naming-options"></a>Opções de nomenclatura de arquivo

* A natureza padrão da gravação de dados transformados em fluxos de dados de mapeamento do ADF é gravar em um DataSet que tenha um serviço vinculado de BLOB ou ADLS. Você deve definir esse conjunto de um para apontar para uma pasta ou contêiner, não um arquivo nomeado.
* Os fluxos de dados usam Azure Databricks Spark para execução, o que significa que a saída será dividida em vários arquivos com base no particionamento padrão do Spark ou no esquema de particionamento que você escolheu explicitamente.
* Uma operação muito comum nos fluxos de dados do ADF é escolher "saída para um único arquivo" para que todos os arquivos da parte de saída sejam mesclados em um único arquivo de saída.
* No entanto, essa operação requer que a saída seja reduzida para uma única partição em um único nó de cluster.
* Tenha isso em mente ao escolher essa opção popular. Você poderá ficar sem recursos de nó de cluster se estiver combinando muitos arquivos de origem grandes em uma única partição de arquivo de saída.
* Para evitar o esgotamento dos recursos do nó de computação, você pode manter o esquema de particionamento padrão ou explícito no ADF, o que otimiza o desempenho e, em seguida, adicionar uma atividade de cópia subsequente no pipeline que mescla todos os arquivos da parte da pasta de saída para um novo Grupo. Essencialmente, essa técnica separa a ação de transformação da mesclagem de arquivos e Obtém o mesmo resultado que a definição de "saída para arquivo único".

### <a name="looping-through-file-lists"></a>Loop por meio de listas de arquivos

Na maioria dos casos, os fluxos de dados no ADF serão executados melhor de um pipeline que permite que a transformação de origem do fluxo de dados Itere em vários arquivos. Em outras palavras, é preferível usar curingas ou listas de arquivos dentro de sua origem no fluxo de dados do que iterar em uma grande lista de arquivos usando ForEach no pipeline, chamando um fluxo de dados de execução em cada iteração. O processo de fluxo de dados será executado mais rapidamente, permitindo que o loop ocorra dentro do fluxo de dados.

Por exemplo, se eu tiver uma lista de arquivos de dados de julho de 2019 que desejo processar em uma pasta no armazenamento de BLOBs, seria mais eficaz chamar uma atividade executar fluxo de dados uma vez a partir de seu pipeline e usar um curinga em sua fonte como esta :

```DateFiles/*_201907*.txt```

Isso terá um desempenho melhor do que uma pesquisa no repositório de BLOB em um pipeline que, em seguida, itera em todos os arquivos correspondentes usando um ForEach com uma atividade executar fluxo de dados dentro do.

## <a name="next-steps"></a>Próximas etapas

Consulte os outros artigos de fluxo de dados relacionados ao desempenho:

- [Guia otimizar fluxo de dados](concepts-data-flow-optimize-tab.md)
- [Atividade de fluxo de dados](control-flow-execute-data-flow-activity.md)
- [Monitorar o desempenho do fluxo de dados](concepts-data-flow-monitoring.md)
