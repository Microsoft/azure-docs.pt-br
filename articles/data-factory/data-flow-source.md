---
title: Configurar uma transformação de origem no recurso de fluxo de dados de mapeamento do Azure Data Factory
description: Saiba como configurar uma transformação de origem no fluxo de dados de mapeamento.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: f6aed5d2ac1c4672d8d8868fe127ead053512e42
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314828"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Transformação de origem para mapeamento de fluxo de dados 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Uma transformação de origem configura sua fonte de dados para o fluxo de dados. Um fluxo de dados pode incluir mais de uma transformação de origem. Ao criar fluxos de dados, sempre comece com uma transformação de origem.

Cada fluxo de dados requer pelo menos uma transformação de origem. Adicione quantas fontes forem necessárias para concluir suas transformações de dados. Você pode unir essas fontes junto com uma transformação junção ou uma transformação Union.

> [!NOTE]
> Quando você depura o fluxo de dados, os dados são lidos da origem usando a configuração de amostragem ou os limites de origem da depuração. Para gravar dados em um coletor, você deve executar o fluxo de dados de uma atividade de fluxo de dados de pipeline. 

![Opções de transformação de origem na guia Configurações de origem](media/data-flow/source.png "origem") do

Associe sua transformação fonte de fluxo de dados a exatamente um conjunto de Data Factory. O DataSet define a forma e o local dos dados que você deseja gravar ou ler. Você pode usar caracteres curinga e listas de arquivos em sua fonte para trabalhar com mais de um arquivo por vez.

## <a name="data-flow-staging-areas"></a>Áreas de preparo do fluxo de dados

O fluxo de dados funciona com os DataSets de *preparo* que estão todos no Azure. Use esses conjuntos de dados para preparo quando você estiver transformando seus dados. 

Data Factory tem acesso a quase 80 conectores nativos. Para incluir dados dessas outras fontes em seu fluxo de dados, use a ferramenta de atividade de cópia para preparar esses dados em uma das áreas de preparo do conjunto de dados de fluxo.

## <a name="options"></a>Opções

Escolha o esquema e as opções de amostragem para seus dados.

### <a name="schema-drift"></a>Descompasso de esquema
A descompasso de [esquema](concepts-data-flow-schema-drift.md) é a capacidade do ADF de lidar nativamente com esquemas flexíveis em seus fluxos de dados sem a necessidade de definir explicitamente as alterações na coluna.

* Selecione **permitir descompasso de esquema** se as colunas de origem forem alteradas com frequência. Essa configuração permite que todos os campos de origem de entrada fluam por meio das transformações para o coletor.

* Escolher **inferir tipos de coluna** descompassos instruirá o ADF a definir tipos de dados para cada nova coluna descoberta. Com esse recurso desativado, o ADF assumirá a cadeia de caracteres.

### <a name="validate-schema"></a>Validar esquema

Se a versão de entrada dos dados de origem não corresponder ao esquema definido, o fluxo de dados não será executado.

![Configurações de origem pública, mostrando as opções para validar esquema, permitir descompasso de esquema e amostragem](media/data-flow/source1.png "fonte pública 1")

### <a name="sample-the-data"></a>Exemplo de dados
Habilite a **amostragem** para limitar o número de linhas de sua origem. Use essa configuração quando você testar ou criar amostras de dados de sua fonte para fins de depuração.

## <a name="define-schema"></a>Definir esquema

Quando os arquivos de origem não são fortemente tipados (por exemplo, arquivos simples em vez de arquivos parquet), defina os tipos de dados para cada campo aqui na transformação de origem.  

![Configurações de transformação de origem na guia definir esquema](media/data-flow/source2.png "fonte 2")

Posteriormente, você poderá alterar os nomes de coluna em uma transformação selecionar. Use uma transformação de coluna derivada para alterar os tipos de dados. Para fontes com rigidez de tipos, você pode modificar os tipos de dados em uma transformação Select posterior. 

![Tipos de dados em uma transformação selecionar](media/data-flow/source003.png "tipos de dados")

### <a name="optimize-the-source-transformation"></a>Otimizar a transformação de origem

Na guia **otimizar** da transformação origem, você poderá ver um tipo de partição de **origem** . Essa opção só está disponível quando sua origem é o banco de dados SQL do Azure. Isso ocorre porque Data Factory tenta fazer conexões paralelas para executar consultas grandes em sua origem do banco de dados SQL.

![Configurações de partição de origem](media/data-flow/sourcepart3.png "particionamento")

Você não precisa Particionar dados em sua origem do banco do dados SQL, mas as partições são úteis para consultas grandes. Você pode basear sua partição em uma coluna ou em uma consulta.

### <a name="use-a-column-to-partition-data"></a>Usar uma coluna para particionar dados

Na tabela de origem, selecione uma coluna na qual particionar. Defina também o número de partições.

### <a name="use-a-query-to-partition-data"></a>Usar uma consulta para particionar dados

Você pode optar por particionar as conexões com base em uma consulta. Basta inserir o conteúdo de um predicado WHERE. Por exemplo, digite year > 1980.

## <a name="source-file-management"></a>Gerenciamento de arquivos de origem

Escolha configurações para gerenciar arquivos em sua origem. 

![Novas configurações de origem](media/data-flow/source2.png "Novas configurações")

* **Caminho curinga**: Em seu contêiner de origem, escolha uma série de arquivos que correspondem a um padrão. Essa configuração substitui qualquer arquivo na definição do conjunto de seus.

Exemplos de curinga:

* ```*```Representa qualquer conjunto de caracteres
* ```**```Representa o aninhamento de diretório recursivo
* ```?```Substitui um caractere
* ```[]```Corresponde a um ou mais caracteres entre colchetes

* ```/data/sales/**/*.csv```Obtém todos os arquivos CSV em/data/Sales
* ```/data/sales/20??/**```Obtém todos os arquivos no século 20
* ```/data/sales/2004/*/12/[XY]1?.csv```Obtém todos os arquivos CSV em 2004 em dezembro, começando com X ou Y prefixados por um número de 2 dígitos

O contêiner deve ser especificado no DataSet. O caminho curinga, portanto, também deve incluir o caminho da pasta da pasta raiz.

* **Lista de arquivos**: Este é um conjunto de arquivos. Crie um arquivo de texto que inclua uma lista de arquivos de caminho relativo a serem processados. Aponte para este arquivo de texto.
* **Coluna para armazenar o nome do arquivo**: Armazene o nome do arquivo de origem em uma coluna em seus dados. Insira um novo nome para armazenar a cadeia de caracteres de nome de arquivo.
* **Após a conclusão**: Escolha não fazer nada com o arquivo de origem após a execução do fluxo de dados, exclua o arquivo de origem ou mova o arquivo de origem. Os caminhos para a movimentação são relativos.

Para mover os arquivos de origem para outro local de pós-processamento, primeiro selecione "mover" para a operação de arquivo. Em seguida, defina o diretório "de". Se você não estiver usando curingas para o caminho, a configuração "de" será a mesma pasta que a pasta de origem.

Se você tiver um caminho de origem com curinga, por exemplo:

```/data/sales/20??/**/*.csv```

Você pode especificar "from" como

```/data/sales```

E "para" como

```/backup/priorSales```

Nesse caso, todos os subdiretórios em/data/Sales que foram originados são movidos em relação ao/backup/priorSales.

### <a name="sql-datasets"></a>Conjuntos de valores SQL

Se sua fonte estiver no banco de dados SQL ou SQL Data Warehouse, você terá opções adicionais para o gerenciamento de arquivos de origem.

* **Consulta**: Insira uma consulta SQL para sua fonte. Essa configuração substitui qualquer tabela que você tenha escolhido no conjunto de um. Observe que não há suporte para cláusulas **order by** aqui, mas você pode definir uma instrução SELECT FROM completa. Você também pode usar funções de tabela definidas pelo usuário. **Select * de udfGetData ()** é um UDF no SQL que retorna uma tabela. Essa consulta produzirá uma tabela de origem que você pode usar em seu fluxo de dados.
* **Tamanho do lote**: Insira um tamanho de lote para dividir dados grandes em leituras.
* **Nível de isolamento**: O padrão para fontes SQL em fluxos de dados de mapeamento do ADF é leitura não confirmada. Você pode alterar o nível de isolamento aqui para um destes valores:
* Leitura confirmada
* Leitura não confirmada
* Leitura repetida
* Serializável
* Nenhum (ignorar nível de isolamento)

![Nível de isolamento](media/data-flow/isolationlevel.png "Nível de isolamento")

> [!NOTE]
> As operações de arquivo são executadas somente quando você inicia o fluxo de dados de uma execução de pipeline (uma depuração de pipeline ou execução de execução) que usa a atividade executar fluxo de dados em um pipeline. As operações de arquivo *não são* executadas no modo de depuração de fluxo de dados.

### <a name="projection"></a>Projeção

Como os esquemas em conjuntos de dados, a projeção em uma fonte define as colunas, os tipos e os formatos dos dados de origem. 

![Configurações na guia projeção](media/data-flow/source3.png "Projeção")

Se o arquivo de texto não tiver um esquema definido, selecione **detectar tipo de dados** para que data Factory obterá amostras e inferirá os tipos de dados. Selecione **definir formato padrão** para detectar automaticamente os formatos de dados padrão. 

Você pode modificar os tipos de dados de coluna em uma transformação de coluna derivada posterior. Use uma transformação selecionar para modificar os nomes de coluna.

![Configurações para formatos de dados padrão](media/data-flow/source2.png "Formatos padrão")

### <a name="add-dynamic-content"></a>Adicionar conteúdo dinâmico

Quando você clicar dentro de campos no painel de configuração, verá um hiperlink para "adicionar conteúdo dinâmico". Ao clicar aqui, você iniciará o construtor de expressões. É aí que você pode definir valores para configurações dinamicamente usando expressões, valores literais estáticos ou parâmetros.

![Parâmetros] do (media/data-flow/params6.png "Parâmetros") do

## <a name="next-steps"></a>Próximas etapas

Comece a criar uma [transformação de coluna derivada](data-flow-derived-column.md) e uma [transformação selecionar](data-flow-select.md).
