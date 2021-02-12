---
title: Desempenho do Phoenix no Azure HDInsight
description: Práticas recomendadas para otimizar o desempenho de Apache Phoenix para clusters do Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: ffba3b986b35c375d0404d9d2bae5af79f93c54f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944794"
---
# <a name="apache-phoenix-performance-best-practices"></a>Práticas recomendadas de desempenho do Apache Phoenix

O aspecto mais importante do desempenho do [Apache Phoenix](https://phoenix.apache.org/) é otimizar o [Apache HBase](https://hbase.apache.org/) subjacente. O Phoenix cria um modelo de dados relacional sobre o HBase, que converte consultas SQL em operações de HBase, como exames. O design de seu esquema de tabela, a seleção e ordenação dos campos na sua chave primária e o uso de todos os índices afetam o desempenho do Phoenix.

## <a name="table-schema-design"></a>Design do esquema de tabela

Ao criar uma tabela no Phoenix, ela é armazenada em uma tabela do HBase. A tabela do HBase contém grupos de colunas (famílias de colunas) que são acessados em conjunto. Uma linha na tabela do Phoenix é uma linha na tabela do HBase em que cada linha consiste em células com versão associadas a uma ou mais colunas. Logicamente, uma única linha do HBase é uma coleção de pares chave-valor, cada um com o mesmo valor de rowkey. Ou seja, cada par chave-valor tem um atributo rowkey, e o valor desse atributo rowkey é o mesmo para uma linha específica.

O design de esquema de uma tabela do Phoenix inclui design de chave primária, design de família de colunas, design de coluna individual e como os dados são particionados.

### <a name="primary-key-design"></a>Design de chave primária

A chave primária definida em uma tabela no Phoenix determina como os dados são armazenados dentro do rowkey da tabela subjacente do HBase. No HBase, a única maneira de acessar uma linha específica é com o rowkey. Além disso, os dados armazenados em uma tabela do HBase são classificados pelo rowkey. O Phoenix cria o valor RowKey concatenando os valores de cada uma das colunas na linha, na ordem em que eles são definidos na chave primária.

Por exemplo, uma tabela para contatos tem o nome, sobrenome, número de telefone e endereço, todos na mesma família de colunas. Você pode definir uma chave primária com base em um número de sequência crescente:

|rowkey|       address|   phone| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

No entanto, se você consultar com frequência por lastName, esta chave primária pode não ser bem executada, porque cada consulta requer uma verificação de tabela completa para ler o valor de cada lastName. Em vez disso, você pode definir uma chave primária nas colunas lastName, firstName e número do seguro social. Esta última coluna é para evitar a ambiguidade de dois residentes no mesmo endereço com o mesmo nome, como pai e filho.

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Com essa nova chave primária, chaves de linhas geradas pelo Phoenix seriam:

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Na primeira linha acima, os dados para o rowkey são representados conforme mostrado:

|rowkey|       chave|   value|
|------|--------------------|---|
|  Dole-John-111|address |1111 San Gabriel Dr.|  
|  Dole-John-111|phone |1-425-000-0002|  
|  Dole-John-111|firstName |John|  
|  Dole-John-111|lastName |Dole|  
|  Dole-John-111|socialSecurityNum |111|

Agora, este rowkey armazena uma cópia duplicada dos dados. Considere o tamanho e o número de colunas que você inclui em sua chave primária, pois esse valor é incluído com todas as células na tabela subjacente do HBase.

Além disso, se a chave primária tiver valores que estão crescendo de forma monotônica, você deve criar a tabela com *salt buckets* para ajudar a evitar a criação de pontos de acesso de gravação – consulte [Particionar dados](#partition-data).

### <a name="column-family-design"></a>Design de família de colunas

Se algumas colunas são acessadas com mais frequência do que outras, você deve criar várias famílias de colunas para separar as que são acessadas frequentemente das que são raramente acessadas.

Além disso, se determinadas colunas tendem a ser acessadas em conjunto, coloque-as na mesma família de colunas.

### <a name="column-design"></a>Design da coluna

* Mantenha as colunas VARCHAR em cerca de 1 MB devido aos custos de e/s de colunas grandes. Ao processar consultas, o HBase materializa células por completo antes de enviá-las para o cliente, que as recebe por completo antes de repassá-las ao código do aplicativo.
* Armazene valores de coluna usando um formato compacto como protobuf, Avro, msgpack ou BSON. JSON não é recomendado, pois é maior.
* Considere compactar os dados antes de armazenar para reduzir a latência e custos de E/S.

### <a name="partition-data"></a>Dados de partição

O Phoenix permite que você controle o número de regiões em que seus dados são distribuídos, o que pode aumentar significativamente o desempenho de leitura/gravação. Ao criar uma tabela do Phoenix, você pode incrementar ou dividir previamente os dados.

Para incrementar uma tabela durante a criação, especifique o número de salt buckets:

```sql
CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16
```

Este incremento divide a tabela entre os valores de chaves primárias, escolhendo os valores automaticamente. 

Para controlar onde as divisões de tabela ocorrerem, você pode dividir previamente a tabela fornecendo os valores de intervalo ao longo do qual a divisão ocorre. Por exemplo, para criar uma tabela dividida em três regiões:

```sql
CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')
```

## <a name="index-design"></a>Design de índice

Um índice do Phoenix é uma tabela do HBase que armazena uma cópia de alguns ou todos os dados da tabela indexada. Um índice melhora o desempenho para tipos específicos de consultas.

Quando você tiver vários índices definidos e, em seguida, consultar uma tabela, o Phoenix selecionará automaticamente o melhor índice para a consulta. O índice primário é criado automaticamente com base nas chaves primárias que você seleciona.

Para consultas previstas, você também pode criar índices secundários especificando suas colunas.

Durante a criação dos índices:

* Crie somente os índices que você precisa.
* Limite o número de índices em tabelas atualizadas com frequência. Atualizações em uma tabela são convertidas em gravações para a tabela principal e as tabelas de índice.

## <a name="create-secondary-indexes"></a>Criar índice secundário

Índices secundários podem melhorar o desempenho de leitura ativando o que seria uma verificação completa de tabela em uma pesquisa de ponto, às custas de espaço de armazenamento e velocidade de gravação. Índices secundários podem ser adicionados ou removidos após a criação de tabela e não requerem alterações para as consultas existentes – as consultas apenas são executadas mais rapidamente. Dependendo de suas necessidades, considere a criação de índices abrangidos, índices funcionais ou ambos.

### <a name="use-covered-indexes"></a>Usar índices abrangidos

Índices abrangidos são índices que incluem dados da linha além os valores que são indexados. Depois de encontrar a entrada de índice desejada, não há necessidade de acessar a tabela primária.

Por exemplo, no exemplo de tabela de contato, você pode criar um índice secundário apenas na coluna socialSecurityNum. Esse índice secundário aceleraria as consultas que filtram por valores socialSecurityNum, mas recuperar outros valores de campo exigiria outra leitura em relação à tabela principal.

|rowkey|       address|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

No entanto, se você geralmente deseja procurar o FirstName e o lastName dado o socialSecurityNum, pode criar um índice abrangido que inclui o FirstName e o lastName como dados reais na tabela de índice:

```sql
CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);
```

Esse índice abrangido permite que a consulta a seguir obtenha todos os dados apenas com a leitura da tabela que contém o índice secundário:

```sql
SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;
```

### <a name="use-functional-indexes"></a>Usar índices funcionais

Índices funcionais permitem que se crie um índice em uma expressão arbitrária que você espera que seja usada em consultas. Após ter um índice funcional em vigor e uma consulta usar essa expressão, o índice pode ser usado para recuperar os resultados em vez da tabela de dados.

Por exemplo, você pode criar um índice para que possa fazer pesquisas que façam distinção entre maiúsculas e minúsculas no conjunto de nome e sobrenome de uma pessoa:

```sql
CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));
```

## <a name="query-design"></a>Design de consulta

As principais considerações de design de consulta são:

* Entender o plano de consulta e verifique o comportamento esperado.
* Unir com eficiência.

### <a name="understand-the-query-plan"></a>Entender o plano de consulta

Em [SQLLine](http://sqlline.sourceforge.net/), use EXPLAIN seguido por sua consulta SQL para exibir o plano de operações que o Phoenix executará. Verifique se o plano:

* Usa a chave primária quando apropriado.
* Usa índices secundários apropriados, em vez da tabela de dados.
* Usa RANGE SCAN ou RANGE SCAN sempre que possível, em vez de TABLE SCAN.

#### <a name="plan-examples"></a>Exemplos de planos

Por exemplo, digamos que você tenha uma tabela chamada VOOS que armazena informações de atrasos de voo.

Para selecionar todos os vôos com uma linha de entrada do `19805` , em que o traço de linha é um campo que não está na chave primária ou em qualquer índice:

```sql
select * from "FLIGHTS" where airlineid = '19805';
```

Execute o comando explicar como a seguir:

```sql
explain select * from "FLIGHTS" where airlineid = '19805';
```

O plano de consulta tem esta aparência:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
   SERVER FILTER BY AIRLINEID = '19805'
```

Nesse plano, observe a expressão FULL SCAN OVER FLIGHTS. Essa expressão indica que a execução faz uma VERIFICAÇÃO DE TABELA em todas as linhas na tabela, em vez de usar a opção mais eficiente de VERIFICAÇÃO DE INTERVALO ou IGNORAR VERIFICAÇÃO.

Agora, digamos que você deseja consultar voos em 2 de janeiro de 2014 para a operadora `AA` onde seu flightnum era maior que 1. Vamos supor que as colunas ano, mês, dia do mês, operadora e número do voo existem na tabela de exemplo e fazem parte da chave primária composta. A consulta deve ser semelhante ao seguinte:

```sql
select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

Vamos examinar o plano para essa consulta com:

```sql
explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

O plano resultante é:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]
```

Os valores entre colchetes são o intervalo de valores para as chaves primárias. Nesse caso, os valores do intervalo são corrigidos com o ano 2014, mês 1 e dia do mês 2, mas permitem valores de número de voo iniciando com 2 e aumentando (`*`). Esse plano de consulta confirma que a chave primária está sendo usada como o esperado.

Em seguida, crie um índice na tabela VOOS denominada `carrier2_idx` que esteja apenas no campo da operadora. Esse índice também inclui a data do voo, número de cauda, origem e número do voo como colunas abrangidas cujos dados também são armazenados no índice.

```sql
CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);
```

Digamos que você deseja obter a operadora juntamente com a data do voo e o número de cauda, como a consulta a seguir:

```sql
explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';
```

Você deve ver este índice sendo usado:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']
```

Para obter uma lista completa dos itens que podem aparecer nos resultados de explicação de planos, consulte a seção explicam Explicar Planos no [Guia de Ajuste do Apache Phoenix](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Unir com eficiência

Em geral, convém evitar junções, a menos que um lado seja pequeno, especialmente em consultas frequentes.

Se necessário, você pode fazer junções grandes com a dica do `/*+ USE_SORT_MERGE_JOIN */`, mas uma junção grande é uma operação cara sobre grandes números de linhas. Se o tamanho total de todas as tabelas do lado direito exceder a memória disponível, use a dica do `/*+ NO_STAR_JOIN */`.

## <a name="scenarios"></a>Cenários

As diretrizes a seguir descrevem alguns padrões comuns.

### <a name="read-heavy-workloads"></a>Cargas de trabalho com uso intenso de leitura

Para casos de uso intensivo de leitura, verifique se você está usando índices. Além disso, para economizar em sobrecarga de tempo de leitura, considere a criação de índices abrangidos.

### <a name="write-heavy-workloads"></a>Cargas de trabalho com uso intenso de gravação

Para cargas de trabalho com excesso de gravação em que a chave primária está aumentando de forma monotônico, crie buckets Salt para ajudar a evitar pontos de interrupções de gravação, às custas da taxa de transferência de leitura geral, devido às verificações adicionais necessárias. Além disso, ao usar UPSERT para gravar um grande número de registros, desative a confirmação automática e armazene os registros em lote.

### <a name="bulk-deletes"></a>Exclusões em massa

Ao excluir um conjunto de dados grande, ative a confirmação automática antes de emitir a consulta de exclusão, para que o cliente não precise se lembrar das chaves de linha de todas as linhas excluídas. A confirmação automática impede que o cliente armazene as linhas afetadas buffer com DELETE, de forma que o Phoenix possa excluí-las diretamente nos servidores regionais sem a despesa de retorná-los para o cliente.

### <a name="immutable-and-append-only"></a>Imutável e somente acréscimo

Se seu cenário favorece a velocidade de gravação na integridade dos dados, considere desabilitar o log write-ahead durante a criação de tabelas:

```sql
CREATE TABLE CONTACTS (...) DISABLE_WAL=true;
```

Para obter mais detalhes sobre esta e outras opções, consulte [Gramática do Apache Phoenix](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Próximas etapas

* [Guia de ajuste do Apache Phoenix](https://phoenix.apache.org/tuning_guide.html)
* [Índices secundários](https://phoenix.apache.org/secondary_indexing.html)
