---
title: Criar tabelas do Azure Cosmos DB para dar suporte ao dimensionamento e desempenho
description: 'Guia de design de Tabela do Armazenamento do Azure: Criar tabelas escalonáveis e de alto desempenho no Azure Cosmos DB e na Tabela do Armazenamento do Azure'
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: conceptual
ms.date: 12/07/2018
author: wmengmsft
ms.author: wmeng
ms.custom: seodec18
ms.openlocfilehash: 84749332c5b7ab5fec2905c0fc36d89863adc3d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60579176"
---
# <a name="azure-storage-table-design-guide-designing-scalable-and-performant-tables"></a>Guia de design de Tabela do Armazenamento do Azure: Criar tabelas escalonáveis e de alto desempenho

[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

Para projetar tabelas escalonáveis e de alto desempenho, você deve considerar vários fatores, como desempenho, escalabilidade e custo. Se você tiver criado anteriormente esquemas de bancos de dados relacionais, essas considerações serão familiares para você, mas embora haja algumas semelhanças entre o modelo de armazenamento do serviço Tabela do Azure e os modelos relacionais, também há várias diferenças importantes. Normalmente, essas diferenças resultam em designs diferentes que podem parecer contraintuitivos ou errados para alguém que esteja familiarizado com bancos de dados relacionais, mas que fazem sentido se você estiver criando para um armazenamento de chave/valor de NoSQL, como o serviço Tabela do Azure. Muitas das suas diferenças de design refletirão o fato de que o serviço Tabela foi projetado para dar suporte a aplicativos de escala de nuvem que podem conter bilhões de entidades (linhas na terminologia de banco de dados relacional) de dados ou de conjuntos de dados que devem dar suporte a grandes volumes de transações: por isso, você precisa pensar de forma diferente sobre como armazenar seus dados e entender como funciona o serviço Tabela. Um repositório de dados NoSQL bem projetado pode permitir que sua solução seja muito mais dimensionável (e a um custo menor) do que uma solução que usa um banco de dados relacional. Este guia ajuda você com esses tópicos.  

## <a name="about-the-azure-table-service"></a>Sobre o serviço Tabela do Azure
Esta seção destaca alguns dos principais recursos do serviço Tabela que são especialmente relevantes para o projeto de desempenho e escalabilidade. Se você não tiver experiência no Armazenamento do Azure e no serviço Tabela, leia primeiro [Introdução ao Armazenamento do Microsoft Azure](../storage/common/storage-introduction.md) e [Introdução ao armazenamento de Tabelas do Azure usando o .NET](table-storage-how-to-use-dotnet.md) antes de ler o restante deste artigo. Embora o foco deste guia seja no serviço Tabela, ele incluirá alguma discussão dos serviços Blob e fila do Azure e como você pode usá-los junto com o serviço Tabela em uma solução.  

O que é o serviço Tabela? Como você pode esperar do nome, o serviço Tabela usa um formato tabular para armazenar dados. Na terminologia padrão, cada linha da tabela representa uma entidade e as colunas armazenam várias propriedades da entidade. Cada entidade tem um par de chaves para identificá-la exclusivamente, e uma coluna de carimbo de data/hora que o serviço Tabela usa para controlar quando a entidade foi atualizada (o campo do carimbo de data/hora é adicionado automaticamente e não é possível substituir manualmente o carimbo de data/hora por um valor arbitrário). O serviço Tabela usa este carimbo de data/hora (LMT) da última modificação para gerenciar a simultaneidade otimista.  

> [!NOTE]
> As operações de API REST do serviço Tabela também retornam um valor **ETag** obtido com o LMT. Neste documento, você observará os termos ETag e LMT indistintamente porque eles se referem aos mesmos dados subjacentes.  
> 
> 

O exemplo a seguir mostra uma estrutura de tabela simples para armazenar entidades de funcionário e departamento. Muitos dos exemplos mostrados posteriormente neste guia baseiam-se neste design simples.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td>Marketing</td>
<td>00001</td>
<td>2014-08-22T00:50:32Z</td>
<td>
<table>
<tr>
<th>Nome</th>
<th>Sobrenome</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Don</td>
<td>Hall</td>
<td>34</td>
<td>donh@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>00002</td>
<td>2014-08-22T00:50:34Z</td>
<td>
<table>
<tr>
<th>Nome</th>
<th>Sobrenome</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Jun</td>
<td>Cao</td>
<td>47</td>
<td>junc@contoso.com</td>
</tr>
</table>
</tr>
<tr>
<td>Marketing</td>
<td>department</td>
<td>2014-08-22T00:50:30Z</td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>Marketing</td>
<td>153</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>Vendas</td>
<td>00010</td>
<td>2014-08-22T00:50:44Z</td>
<td>
<table>
<tr>
<th>Nome</th>
<th>Sobrenome</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Ken</td>
<td>Kwok</td>
<td>23</td>
<td>kenk@contoso.com</td>
</tr>
</table>
</td>
</tr>
</table>


Até agora, esse design parece semelhante a uma tabela no banco de dados relacional com as diferenças principais sendo as colunas obrigatórias e a capacidade de armazenar vários tipos de entidade na mesma tabela. Além disso, cada uma das propriedades definidas pelo usuário, como **FirstName** ou **Age**, tem um tipo de dados, como número inteiro ou cadeia de caracteres, semelhante a uma coluna em um banco de dados relacional. Embora diferente em um banco de dados relacional, a natureza sem esquema do serviço Tabela significa que uma propriedade não precisa ter o mesmo tipo de dados em cada entidade. Para armazenar tipos de dados complexos em uma única propriedade, você deve usar um formato serializado como JSON ou XML. Para obter mais informações sobre o serviço Tabela, como tipos de dados com suporte, intervalos de datas com suporte, regras de nomenclatura e restrições de tamanho, consulte [Noções básicas sobre o modelo de dados do serviço Tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Como você verá, sua escolha de **PartitionKey** e **RowKey** é fundamental para um bom design da tabela. Cada entidade armazenada em uma tabela deve ter uma combinação exclusiva de **PartitionKey** e **RowKey**. Assim como acontece com as chaves em uma tabela de banco de dados relacional, os valores de **PartitionKey** e **RowKey** são indexados para criar um índice clusterizado que habilita pesquisas rápidas; no entanto, o serviço Tabela não cria índices secundários, de modo que estas são as duas únicas propriedades indexadas (alguns dos padrões descritos posteriormente mostram como você pode contornar essa limitação aparente).  

Uma tabela é composta por uma ou mais partições, e como você verá, muitas das decisões de design tomadas serão em torno da escolha de uma **PartitionKey** e uma **RowKey** adequadas para otimizar sua solução. Uma solução poderia consistir em apenas uma única tabela que contém todas as suas entidades organizadas em partições, mas normalmente uma solução terá várias tabelas. Tabelas o ajudarão a organizar suas entidades logicamente, gerenciar o acesso aos dados usando listas de controle de acesso, e você pode remover uma tabela inteira usando uma única operação de armazenamento.  

### <a name="table-partitions"></a>Partições de tabela
O nome da conta, o nome de tabela e **PartitionKey** juntas identificam a partição dentro do serviço de armazenamento no qual o serviço Tabela armazena a entidade. Além de ser parte do esquema de endereçamento de entidades, as partições definem um escopo para transações (consulte [Transações de Grupo de Entidades](#entity-group-transactions) abaixo) e forme a base de como o serviço Tabela pode ser dimensionado. Para obter mais informações sobre partições, consulte [Metas de desempenho e escalabilidade de armazenamento do Azure](../storage/common/storage-scalability-targets.md).  

No serviço Tabela, um nó individual atende a uma ou mais partições completas e o serviço é dimensionado pelo balanceamento dinâmico de carga das partições nos nós. Se um nó estiver sob carga, o serviço Tabela pode *dividir* o intervalo de partições atendidas por esse nó em nós diferentes. Quando o tráfego baixa, o serviço pode *mesclar* os intervalos de partições de nós silenciosos de volta para um único nó.  

Para obter mais informações sobre os detalhes internos do serviço Tabela e, em particular, sobre como o serviço gerencia partições, confira o artigo [Armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

### <a name="entity-group-transactions"></a>Transações de Grupo de Entidades
No serviço Tabela, EGTs (Transações de Grupo de Entidades) são o único mecanismo interno para realizar atualizações atômicas entre várias entidades. EGTs também são conhecidas como *transações de lote* em alguns documentos. EGTs só podem operar em entidades armazenadas na mesma partição (compartilhar a mesma chave de partição em determinada tabela), portanto, sempre que você precisar de um comportamento transacional atômico nas várias entidades, precisará garantir que as entidades estejam na mesma partição. Isso geralmente é um motivo para manter vários tipos de entidade na mesma tabela (e partição) e não usar várias tabelas para tipos de entidade diferentes. Uma única EGT pode operar no máximo 100 entidades.  Se você enviar várias EGTs simultâneas para processamento, é importante garantir que essas EGTs não operem em entidades comuns entre as EGTs; caso contrário, o processamento pode ser retardado.

As EGTs também apresentam uma desvantagem potencial para avaliação do design: o uso de mais partições aumenta a escalabilidade do seu aplicativo porque o Azure tem mais oportunidades de balancear a carga de solicitações entre os nós, mas isso pode limitar a capacidade do seu aplicativo para executar transações atômicas e manter a coerência forte dos dados. Além disso, existem metas de escalabilidade específica no nível de uma partição que podem limitar a taxa de transferência de transações esperada para um único nó: para obter mais informações sobre metas de escalabilidade para contas de armazenamento do Azure e o serviço Tabela, consulte [Metas de desempenho e escalabilidade de armazenamento do Azure](../storage/common/storage-scalability-targets.md). As seções posteriores deste guia discutem diversas estratégias de design que o ajudarão a gerenciar compensações como esta e descrevem a melhor escolha para sua chave de partição com base nos requisitos específicos do aplicativo cliente.  

### <a name="capacity-considerations"></a>Considerações sobre a capacidade
A tabela a seguir inclui alguns dos valores de chave, portanto, fique atento ao projetar uma solução de serviço Tabela:  

| Capacidade total de uma conta de armazenamento do Azure | 500 TB |
| --- | --- |
| Número de tabelas em uma conta de armazenamento do Azure |Limitado apenas pela capacidade da conta de armazenamento |
| Número de partições em uma tabela |Limitado apenas pela capacidade da conta de armazenamento |
| Número de entidades em uma partição |Limitado apenas pela capacidade da conta de armazenamento |
| Tamanho de uma entidade individual |Até 1 MB com um máximo de 255 propriedades (incluindo **PartitionKey**, **RowKey** e **Timestamp**) |
| Tamanho do **PartitionKey** |Uma cadeia de caracteres até 1 KB |
| Tamanho do **RowKey** |Uma cadeia de caracteres até 1 KB |
| Tamanho de uma Transação de Grupo de Entidades |Uma transação pode incluir no máximo 100 entidades e a carga deve ser menor que 4 MB. Uma EGT só pode atualizar uma entidade uma vez. |

Para obter informações, consulte [Noções básicas sobre o modelo de dados do serviço Tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).  

### <a name="cost-considerations"></a>Considerações de custo
Armazenamento de tabela é relativamente barato, mas você deve incluir estimativas de custo para a utilização da capacidade e a quantidade de transações como parte de sua avaliação de qualquer solução que usa o serviço Tabela. No entanto, em muitos cenários, o armazenamento de dados duplicados ou desnormalizados para melhorar o desempenho ou a escalabilidade de sua solução é uma abordagem válida. Para obter mais informações sobre preços, consulte [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="guidelines-for-table-design"></a>Diretrizes de design da tabela
Essas listas resumem algumas as diretrizes importantes que você deve ter em mente ao criar tabelas, e este guia tratará delas em mais detalhes posteriormente. Essas diretrizes são diferentes daquelas que você normalmente seguiria para design de banco de dados relacional.  

Criando sua solução do serviço Tabela para ser eficiente em *leitura* :

* ***Design para consulta em aplicativos com alta taxa de leitura.***  Quando você está criando tabelas, pense sobre as consultas (especialmente aquelas sensíveis a latência) que você executará antes de pensar em como atualizará as entidades. Isso normalmente resulta em uma solução eficiente e de alto desempenho.  
* ***Especifique PartitionKey e RowKey em suas consultas.*** *Consultas de ponto* como essas são as consultas de serviço Tabela mais eficientes.  
* ***Considere armazenar cópias duplicadas de entidades.***  O armazenamento de tabela é barato, portanto, considere armazenar a mesma entidade várias vezes (com chaves diferentes) para permitir consultas mais eficientes.  
* ***Considere a desnormalização de seus dados.***  O armazenamento de tabela é barato, então considere desnormalizar seus dados. Por exemplo, armazene entidades resumidas para que consultas a dados agregados só tenham de acessar uma única entidade.  
* ***Use valores de chave composta.*** As únicas chaves que você tem são **PartitionKey** e **RowKey**. Por exemplo, use valores de chave composta para habilitar caminhos alternativo com chave de acesso para entidades.  
* ***Use a projeção de consulta.***  Você pode reduzir a quantidade de dados transferidos pela rede por meio de consultas que selecionam apenas os campos necessários.  

Criar a solução de serviço Tabela para ser eficiente em *gravação* :  

* ***Não crie partições ativas.***  Escolha chaves que permitam que você distribua suas solicitações por várias partições em qualquer momento.  
* ***Evite picos no tráfego.***  Suavize o tráfego por um período razoável de tempo e evite picos no tráfego.
* ***Não crie, necessariamente, uma tabela separada para cada tipo de entidade.***  Quando você precisar de transações atômicas nos tipos de entidade, pode armazenar esses vários tipos de entidade na mesma partição, na mesma tabela.
* ***Considere a produtividade máxima que deve ser atingida.***  Você deve estar ciente dos destinos de escalabilidade para o serviço Tabela e garantir que seu design não fará com que você os exceda.  

À medida que você ler este guia, verá exemplos que colocam todos esses princípios em prática.  

## <a name="design-for-querying"></a>Design para consulta
Soluções de serviço Tabela podem ser de leitura intensiva, gravação intensiva ou uma combinação dos dois. Esta seção aborda as coisas a serem lembradas ao criar seu serviço Tabela para dar suporte a operações de leitura com eficiência. Normalmente, um design que dá suporte a operações de leitura com eficiência também é eficiente para operações de gravação. No entanto, na próxima seção, [Design para modificação de dados](#design-for-data-modification), existem e são discutidas considerações adicionais para se ter em mente ao projetar de modo a dar suporte a operações de gravação.

Um bom ponto de partida para criar a solução serviço Tabela para que você possa ler os dados com eficiência é perguntar "Quais consultas meu aplicativo precisará executar para recuperar os dados necessários do serviço Tabela?"  

> [!NOTE]
> Com o serviço Tabela, é importante obter o design correto com antecedência porque é difícil e caro alterá-lo depois. Por exemplo, em um banco de dados relacional é frequentemente possível solucionar problemas de desempenho simplesmente adicionando índices a um banco de dados: isso não é uma opção com o serviço Tabela.  
> 
> 

Esta seção se concentra nos principais problemas que você deve abordar ao projetar suas tabelas para consulta. Os tópicos abordados nesta seção incluem:

* [Como sua escolha de PartitionKey e RowKey afeta o desempenho da consulta](#how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance)
* [Escolhendo uma PartitionKey apropriada](#choosing-an-appropriate-partitionkey)
* [Otimizando consultas para o serviço Tabela](#optimizing-queries-for-the-table-service)
* [Armazenando dados no serviço Tabela](#sorting-data-in-the-table-service)

### <a name="how-your-choice-of-partitionkey-and-rowkey-impacts-query-performance"></a>Como sua escolha de PartitionKey e RowKey afeta o desempenho da consulta
Os exemplos a seguir pressupõem que o serviço Tabela é armazenar entidades de funcionário com a seguinte estrutura (a maioria dos exemplos omite a propriedade **Timestamp** por motivos de clareza):  

| *Nome da coluna* | *Tipo de dados* |
| --- | --- |
| **PartitionKey** (nome de departamento) |Cadeia de caracteres |
| **RowKey** (Id do funcionário) |Cadeia de caracteres |
| **Nome** |Cadeia de caracteres |
| **Sobrenome** |Cadeia de caracteres |
| **Idade** |Número inteiro |
| **EmailAddress** |Cadeia de caracteres |

A seção anterior, Visão geral do serviço Tabela do Azure, descreve alguns dos principais recursos do serviço Tabela do Azure, que têm uma influência direta no design para consulta. Isso resulta nas seguintes diretrizes gerais para a criação de consultas do serviço Tabela. A sintaxe de filtro usada nos exemplos a seguir é proveniente da API REST do serviço Tabela. Para obter mais informações, veja [Consultar Entidades](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

* Uma ***Consulta de Ponto*** é a pesquisa mais eficiente a ser usada e é recomendada para pesquisas de alto volume ou pesquisas que exigem a latência mais baixa. Tal consulta pode usar os índices para localizar uma entidade individual de modo eficiente, especificando os valores **PartitionKey** e **RowKey**. Por exemplo: $filter=(PartitionKey eq 'Sales') and (RowKey eq '2')  
* A segunda melhor opção é uma ***Consulta de Intervalo***, que usa **PartitionKey** e filtros em um intervalo de valores de **RowKey** para retornar mais de uma entidade. O valor de **PartitionKey** identifica uma partição específica e os valores de **RowKey** identificam um subconjunto das entidades na partição. Por exemplo: $filter=PartitionKey eq 'Sales' and RowKey ge 'S' and RowKey lt 'T'  
* A terceira melhor opção é uma ***Verificação de Partição***, que usa **PartitionKey** e filtros em outra propriedade não chave e que pode retornar mais de uma entidade. O valor **PartitionKey** identifica uma partição específica e os valores de propriedades selecionados para um subconjunto das entidades nessa partição. Por exemplo: $filter=PartitionKey eq 'Sales' and LastName eq 'Smith'  
* Uma ***Verificação de Tabela*** não inclui **PartitionKey** e é ineficiente, pois pesquisa todas as partições que, por sua vez, compõem sua tabela para qualquer entidade correspondente. A verificação da tabela será realizada, independentemente de o filtro usar ou não a **RowKey**. Por exemplo: $filter=LastName eq 'Dias'  
* As consultas do Armazenamento de Tabelas do Azure que retornam várias entidades as retornam classificadas na ordem **PartitionKey** e **RowKey**. Para evitar reclassificar as entidades no cliente, escolha uma **RowKey** que define a ordem de classificação mais comum. Resultados de consulta retornados pela API de tabela do Azure no Azure Cosmos DB não são classificados por chave de partição ou a chave de linha. Para obter uma lista detalhada das diferenças entre os recursos, confira [Diferenças entre a API de Tabela no Azure Cosmos DB e no Armazenamento de Tabela do Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

O uso de um operador "**or**" para especificar um filtro com base em valores de **RowKey** resulta em uma verificação de partição, e não é tratado como uma consulta de intervalo. Portanto, você deve evitar consultas que usam filtros, como: $filter=PartitionKey eq 'Sales' and (RowKey eq '121' or RowKey eq '322')  

Para obter exemplos de código de cliente que usam a Biblioteca de Cliente de Armazenamento para executar consultas eficientes, consulte:  

* [Executando uma consulta de ponto usando a Biblioteca de Cliente de Armazenamento](#executing-a-point-query-using-the-storage-client-library)
* [Recuperando várias entidades usando LINQ](#retrieving-multiple-entities-using-linq)
* [Projeção do lado do servidor](#server-side-projection)  

Para obter exemplos de código do lado do cliente que pode lidar com vários tipos de entidade armazenados na mesma tabela, consulte:  

* [Trabalhando com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)  

### <a name="choosing-an-appropriate-partitionkey"></a>Escolhendo uma PartitionKey apropriada
Sua escolha de **PartitionKey** deve equilibrar a necessidade de habilitar o uso de EGTs (para garantir a consistência) com a necessidade de distribuir suas entidades por várias partições (para garantir uma solução escalonável).  

Por um lado, você pode armazenar todas as suas entidades em uma única partição, mas isso pode limitar a escalabilidade da solução e impedir que o serviço Tabela seja capaz de solicitar balanceamento de carga. Por outro lado, você pode armazenar uma entidade por partição, o que seria altamente escalonável e permitiria que o serviço Tabela balanceasse a carga das solicitações, mas impediria o uso de transações em grupo de entidades.  

Uma **PartitionKey** ideal é aquela que permite que você use consultas eficientes e que tenha partições suficientes para garantir que sua solução seja escalonável. Normalmente, você descobrirá que as entidades terão uma propriedade adequada que distribui suas entidades em partições suficientes.

> [!NOTE]
> Por exemplo, em um sistema que armazena informações sobre usuários ou funcionários, UserID pode ser uma boa PartitionKey. Você pode ter várias entidades que usam uma determinada UserID como a chave da partição. Cada entidade que armazena dados sobre um usuário é agrupada em uma única partição, e pode ser acessada por meio de transações do grupo de entidades, continuando altamente escalonável.
> 
> 

Há considerações adicionais na sua escolha de **PartitionKey** relacionadas a como você vai inserir, atualizar e excluir entidades: consulte a seção [Design para modificação de dados](#design-for-data-modification) abaixo.  

### <a name="optimizing-queries-for-the-table-service"></a>Otimizando consultas para o serviço Tabela
O serviço Tabela indexa automaticamente suas entidades usando os valores de **PartitionKey** e **RowKey** em um único índice clusterizado; por isso as consultas de ponto são as mais eficientes. No entanto, não há qualquer índice além daquele no índice clusterizado em **PartitionKey** e **RowKey**.

Muitos designs devem atender aos requisitos para habilitar a pesquisa de entidades com base em vários critérios. Por exemplo, localizar entidades de funcionário com base em email, ID de funcionário ou sobrenome. Os padrões a seguir, na seção [Padrões de design de tabela](#table-design-patterns) , abordam esses tipos de requisito e descrevem maneiras de contornar o fato de que o serviço Tabela não fornece índices secundários:  

* [Padrão de índice secundário intrapartição](#intra-partition-secondary-index-pattern) - armazene várias cópias de cada entidade usando valores diferentes de **RowKey** (na mesma partição) para permitir pesquisas rápidas e eficientes, bem como ordens de classificação alternativas usando valores de **RowKey** diferentes.  
* [Padrão de índice secundário entre partições](#inter-partition-secondary-index-pattern) – armazene várias cópias de cada entidade usando valores diferentes de **RowKey** em partições separadas ou em tabelas separadas, a fim de permitir pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de **RowKey**.  
* [Padrão de entidades de índice](#index-entities-pattern) - Mantenha entidades de índice para permitir pesquisas eficientes que retornem listas de entidades.  

### <a name="sorting-data-in-the-table-service"></a>Armazenando dados no serviço Tabela

Os resultados de consulta retornados pelo serviço Tabela são classificados em ordem crescente com base em **PartitionKey** e, em seguida, em **RowKey**.

> [!NOTE]
> Resultados de consulta retornados pela API de tabela do Azure no banco de dados do Azure não são classificados por chave de partição ou a chave de linha. Para obter uma lista detalhada das diferenças entre os recursos, confira [Diferenças entre a API de Tabela no Azure Cosmos DB e no Armazenamento de Tabela do Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

As chaves na tabela do Armazenamento do Azure são valores de cadeia de caracteres e, para garantir que os valores numéricos sejam classificados corretamente, você deve convertê-los em um comprimento fixo e preenchê-los com zeros. Por exemplo, se o valor da ID de funcionário que você usa como a **RowKey** for um valor inteiro, você deverá converter a ID do funcionário **123** em **00000123**. 

Muitos aplicativos têm requisitos para usar dados classificados em ordens diferentes: por exemplo, classificação de funcionários por nome ou por data de ingresso. Os seguintes padrões, na seção [Padrões de Design de tabela](#table-design-patterns) , abordam como alternar as ordens de classificação para suas entidades:  

* [Padrão de índice secundário intrapartição](#intra-partition-secondary-index-pattern) - Armazene várias cópias de cada entidade usando valores diferentes de RowKey (na mesma partição), para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores de RowKey diferentes.  
* [Padrão de índice secundário entre partições](#inter-partition-secondary-index-pattern) - Armazene várias cópias de cada entidade usando valores diferentes de RowKey em partições e tabelas separadas, para permitir pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de RowKey.
* [Padrão de rastro do log](#log-tail-pattern) - Recupere as entidades *n* adicionadas recentemente em uma partição, usando um valor **RowKey** que classifica em ordem de data e hora inversa.  

## <a name="design-for-data-modification"></a>Design para modificação de dados
Esta seção enfoca as considerações de design para otimizar inserções, atualizações e exclusões. Em alguns casos, você precisará avaliar a compensação entre designs que otimizam para consulta em relação a designs que otimizam para modificação de dados, da mesma forma que em designs de bancos de dados relacionais (embora as técnicas para gerenciar vantagens e desvantagens do design sejam diferentes em um banco de dados relacional). A seção [Padrões de design de tabela](#table-design-patterns) descreve alguns padrões de design detalhados para o serviço Tabela e destaca algumas dessas compensações. Na prática, você descobrirá que muitos designs otimizados para entidades de consulta também funcionam bem para modificar entidades.  

### <a name="optimizing-the-performance-of-insert-update-and-delete-operations"></a>Otimizando o desempenho das operações de inserção, atualização e exclusão
Para atualizar ou excluir uma entidade, você deve ser capaz de identificá-la usando os valores de **PartitionKey** e **RowKey**. Nesse sentido, a escolha de **PartitionKey** e **RowKey** para modificar entidades deve seguir critérios semelhantes à escolha para dar suporte a consultas de ponto, a fim identificar entidades do modo mais eficiente possível. Não convém usar uma verificação ineficiente de tabela ou de partição para localizar uma entidade, a fim de descobrir os valores de **PartitionKey** e **RowKey** necessários para atualizá-la ou excluí-la.  

Os padrões a seguir, na seção [Padrões de design de tabela](#table-design-patterns) , abordam a otimização do desempenho ou suas operações de inserção, atualização e exclusão:  

* [Padrão de exclusão de alto volume](#high-volume-delete-pattern) - Habilite a exclusão de um alto volume de entidades armazenando todas as entidades para exclusão simultânea em suas próprias tabelas separadas; exclua as entidades por meio da exclusão da tabela.  
* [Padrão de série de dados](#data-series-pattern) - Armazene séries completas de dados em uma única entidade para minimizar o número de solicitações feitas.  
* [Padrão de entidades longas](#wide-entities-pattern) - Use várias entidades físicas para armazenar entidades lógicas com mais de 252 propriedades.  
* [Padrão de entidades grandes](#large-entities-pattern) - Use o armazenamento de blobs para armazenar grandes valores de propriedade.  

### <a name="ensuring-consistency-in-your-stored-entities"></a>Garantindo a consistência nas suas entidades armazenadas
Outro fator-chave que influencia sua escolha de chaves para otimizar as modificações de dados é como garantir a consistência usando transações atômicas. Você só pode usar uma EGT para operar em entidades armazenadas na mesma partição.  

Os padrões a seguir, na seção [Padrões de design de tabela](#table-design-patterns) , tratam da consistência de gerenciamento:  

* [Padrão de índice secundário intrapartição](#intra-partition-secondary-index-pattern) - armazene várias cópias de cada entidade usando valores diferentes de **RowKey** (na mesma partição) para permitir pesquisas rápidas e eficientes, bem como ordens de classificação alternativas usando valores de **RowKey** diferentes.  
* [Padrão de índice secundário entre partições](#inter-partition-secondary-index-pattern) - Armazene várias cópias de cada entidade usando valores diferentes de RowKey em partições separadas ou em tabelas separadas, a fim de permitir pesquisas rápidas e eficientes e ordens de classificação alternativas usando valores diferentes de **RowKey** .  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) - Habilite comportamento eventualmente consistente entre limites de partição ou limites do sistema de armazenamento usando filas do Azure.
* [Padrão de entidades de índice](#index-entities-pattern) - Mantenha entidades de índice para permitir pesquisas eficientes que retornem listas de entidades.  
* [Padrão de desnormalização](#denormalization-pattern) - Combine dados relacionados juntos em uma única entidade, para que você possa recuperar todos os dados de que precisa com uma única consulta de ponto.  
* [Padrão de série de dados](#data-series-pattern) - Armazene séries completas de dados em uma única entidade para minimizar o número de solicitações feitas.  

Para saber mais sobre transações de grupo, consulte a seção [Transações de grupo de entidades](#entity-group-transactions).  

### <a name="ensuring-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Garantir seu design para modificações eficientes facilita consultas eficientes
Em muitos casos, um design para consultas eficientes resulta em modificações eficientes, mas você sempre deve avaliar se esse é o caso para seu cenário específico. Alguns dos padrões na seção [Padrões de design de tabela](#table-design-patterns) avaliam explicitamente as compensações entre a consulta e a modificação de entidades, e você deve sempre levar em consideração o número de cada tipo de operação.  

Os padrões a seguir, na seção [Padrões de design de tabela](#table-design-patterns) , abordam as compensações entre o design para consultas eficientes e o design para modificação eficiente de dados:  

* [Padrão de chave composta](#compound-key-pattern) - Use valores **RowKey** compostos para permitir que um cliente pesquise dados relacionados com uma consulta de único ponto.  
* [Padrão de rastro do log](#log-tail-pattern) - Recupere as entidades *n* adicionadas recentemente em uma partição, usando um valor **RowKey** que classifica em ordem de data e hora inversa.  

## <a name="encrypting-table-data"></a>Criptografando dados de tabela
A Biblioteca de Cliente do Armazenamento do Azure para .NET dá suporte à criptografia de propriedades de entidade para as operações de inserção e substituição. As cadeias de caracteres criptografadas são armazenadas no serviço como propriedades binárias, e são convertidas novamente em cadeias de caracteres após a descriptografia.    

Para tabelas, além da política de criptografia, os usuários devem especificar as propriedades que devem ser criptografadas. Isso pode ser feito especificando o atributo [EncryptProperty] \(para entidades POCO que derivam de TableEntity) ou um resolvedor de criptografia nas opções de solicitação. Um resolvedor de criptografia é um delegado que usa uma chave de partição, a chave de linha e o nome da propriedade e retorna um valor booliano que indica se essa propriedade deve ser criptografada. Durante a criptografia, a biblioteca de cliente usará essas informações para decidir se uma propriedade deve ser criptografada durante a gravação para a transmissão. O representante também oferece a possibilidade de lógica em torno de como as propriedades são criptografadas. (Por exemplo, se X, então criptografar a propriedade A; caso contrário, criptografar as propriedades A e B.) Não é necessário fornecer essas informações durante a leitura ou ao consultar entidades.

Atualmente não há suporte para a mesclagem. Como um subconjunto de propriedades pode ter sido criptografado anteriormente usando uma chave diferente, simplesmente mesclar as novas propriedades e atualizar os metadados resultará em perda de dados. Mesclar requer fazer chamadas de serviço extra para ler a entidade já existente no serviço ou usar uma nova chave por propriedade, os quais não são ambos adequados por motivos de desempenho.     

Para obter informações sobre como criptografar dados de tabela, confira [Criptografia do lado do cliente e Cofre da Chave do Azure para Armazenamento do Microsoft Azure](../storage/common/storage-client-side-encryption.md).  

## <a name="modeling-relationships"></a>Relações de modelagem
A criação de modelos de domínio é uma etapa importante na criação de sistemas complexos. Normalmente, você usa o processo de modelagem para identificar entidades e as relações entre elas, como uma forma de compreender o domínio de negócios e informar o design do seu sistema. Esta seção se concentra em como você pode converter alguns dos tipos de relações comuns encontrados em modelos de domínio em designs para o serviço Tabela. O processo de mapeamento de um modelo de dados lógico para um modelo de dados baseado em NoSQL físico é diferente do usado durante a criação de um banco de dados relacional. Design de bancos de dados relacionais geralmente assume um processo de normalização de dados otimizado para minimizar a redundância – e uma funcionalidade de consulta declarativa que abstrai como a implementação do banco de dados funciona.  

### <a name="one-to-many-relationships"></a>Relações um-para-muitos
Relações um-para-muitos entre objetos de domínio de negócios ocorrerem com frequência: por exemplo, um departamento tem muitos funcionários. Há várias maneiras de implementar relações um-para-muitos no serviço Tabela, cada com prós e contras que podem ser relevantes ao cenário específico.  

Considere o exemplo de uma grande empresa multinacional com dezenas de milhares de departamentos e entidades de funcionário em que cada departamento tem muitos funcionários e cada funcionário associado a um departamento específico. Uma abordagem é armazenar departamento separado e entidades de funcionário como estes:  

![Entidade de departamento e funcionário][1]

Este exemplo mostra uma relação implícita de um-para-muitos entre os tipos com base no valor **PartitionKey** . Cada departamento pode ter muitos funcionários.  

Este exemplo também mostra uma entidade de departamento e suas entidades de funcionário relacionadas na mesma partição. Você pode optar por usar diferentes partições, tabelas ou até mesmo contas de armazenamento para os diferentes tipos de entidade.  

Uma abordagem alternativa é desnormalizar seus dados e armazenar apenas entidades de funcionário com dados desnormalizados de departamento, conforme mostrado no exemplo a seguir. Neste cenário específico, essa abordagem desnormalizada pode não ser a melhor se você precisar ser capaz de alterar os detalhes de um gerente de departamento, pois para isso você precisa atualizar todos os funcionários do departamento.  

![Entidade de funcionário][2]

Para saber mais, confira [Padrão de desnormalização](#denormalization-pattern) mais adiante neste guia.  

A tabela a seguir resume os prós e contras de cada uma das abordagens descritas acima para o armazenamento de entidades de funcionário e departamento que têm uma relação um-para-muitos. Você também deve considerar a frequência com que pretende executar várias operações: pode ser aceitável ter um projeto que inclui uma operação cara, se essa operação ocorrer apenas raramente.  

<table>
<tr>
<th>Abordagem</th>
<th>Prós</th>
<th>Contras</th>
</tr>
<tr>
<td>Separar tipos de entidade, mesma partição, mesma tabela</td>
<td>
<ul>
<li>Você pode atualizar uma entidade de departamento com uma única operação.</li>
<li>Você pode usar uma EGT para manter a consistência se precisar modificar uma entidade de departamento sempre que atualizar/inserir/excluir uma entidade de funcionário. Por exemplo, se você mantiver uma contagem de funcionários do departamento para cada departamento.</li>
</ul>
</td>
<td>
<ul>
<li>Talvez seja necessário recuperar uma entidade de funcionário e departamento para algumas atividades do cliente.</li>
<li>As operações de armazenamento ocorrem na mesma partição. Em grandes volumes de transações, isso pode resultar em um ponto de acesso.</li>
<li>Não é possível mover um funcionário para outro departamento usando uma EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Tipos de entidade separada, diferentes partições ou tabelas ou contas de armazenamento</td>
<td>
<ul>
<li>Você pode atualizar uma entidade de departamento ou funcionário com uma única operação.</li>
<li>Em grandes volumes de transações, isso pode ajudar a distribuir a carga entre mais partições.</li>
</ul>
</td>
<td>
<ul>
<li>Talvez seja necessário recuperar uma entidade de funcionário e departamento para algumas atividades do cliente.</li>
<li>Não é possível usar EGTs para manter a consistência quando você atualiza/insere/exclui um funcionário e atualiza um departamento. Por exemplo, a atualização de uma contagem de funcionários em uma entidade de departamento.</li>
<li>Não é possível mover um funcionário para outro departamento usando uma EGT.</li>
</ul>
</td>
</tr>
<tr>
<td>Desnormalizar em um único tipo de entidade</td>
<td>
<ul>
<li>Você pode recuperar todas as informações necessárias com uma única solicitação.</li>
</ul>
</td>
<td>
<ul>
<li>Pode ser caro manter a consistência se você precisar atualizar informações do departamento (o exigiria que você atualizasse todos os funcionários em um departamento).</li>
</ul>
</td>
</tr>
</table>

Como escolher entre essas opções e quais os prós e contras são mais significativos, depende de seus cenários específicos do aplicativo. Por exemplo, com que frequência você modifica entidades de departamento; todas as consultas de funcionário precisam de informações departamentais adicionais; a que distância você está dos limites de escalabilidade em suas partições ou em sua conta de armazenamento?  

### <a name="one-to-one-relationships"></a>Relações um-para-um
Modelos de domínio podem incluir relações um-para-um entre entidades. Se você precisar implementar uma relação individual no serviço Tabela, também deve escolher como vincular as duas entidades relacionadas, quando precisar recuperá-las. Esse link pode ser implícito, com base em uma convenção nos valores de chave, ou então explícito, armazenando um link na forma dos valores de **PartitionKey** e **RowKey** em cada entidade até sua entidade relacionada. Para ver uma discussão sobre a conveniência ou não de armazenar as entidades relacionadas na mesma partição, confira a seção [Relações de um-para-muitos](#one-to-many-relationships).  

Também há considerações de implementação que podem levá-lo a implementar relações um-para-um no serviço Tabela:  

* Controlando grandes entidades (para obter mais informações, consulte [Padrão de grandes entidades](#large-entities-pattern)).  
* A implementação de controles de acesso (para saber mais, consulte [Controlando o acesso com assinaturas de acesso compartilhado](#controlling-access-with-shared-access-signatures)).  

### <a name="join-in-the-client"></a>Unindo o cliente
Embora haja maneiras de modelar relações no serviço Tabela, você não deve se esquecer de que os dois motivos principais para usar o serviço Tabela são a escalabilidade e o desempenho. Se você achar que está modelando muitas relações que comprometem o desempenho e a escalabilidade de sua solução, deverá se perguntar se é necessário criar todas as relações de dados no design de tabela. Você poderá simplificar o design e melhorar a escalabilidade e o desempenho de sua solução se permitir que o aplicativo cliente execute as junções necessárias.  

Por exemplo, se você tiver tabelas pequenas que contêm dados que não são alterados com frequência, você pode recuperar esses dados uma vez e armazená-los no cliente. Isso pode evitar idas e voltas repetidas para recuperar os mesmos dados. Nos exemplos que examinamos neste guia, o conjunto dos departamentos em uma pequena organização é provavelmente pequeno e alterado raramente, tornando-o um bom candidato para dados que o aplicativo cliente pode fazer o download uma vez e armazenar como dados de pesquisa.  

### <a name="inheritance-relationships"></a>Relações de herança
Se seu aplicativo cliente usa um conjunto de classes que fazem parte de uma relação de herança para representar entidades de negócios, você poderá persistir facilmente essas entidades no serviço Tabela. Por exemplo, você pode ter o seguinte conjunto de classes definidas em seu aplicativo cliente, em que **Pessoa** é uma classe abstrata.

![Diagrama de ER das relações de herança][3]

Você pode persistir instâncias das duas classes concretas no serviço Tabela usando uma única tabela Pessoa com entidades que têm a seguinte aparência:  

![Diagrama da entidade Cliente e da entidade Funcionário][4]

Para saber mais sobre como trabalhar com vários tipos de entidade na mesma tabela no código cliente, confira a seção [Trabalhando com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types) mais adiante neste guia. Isso fornece exemplos de como reconhecer o tipo de entidade no código do cliente.  

## <a name="table-design-patterns"></a>Padrões de design de tabela
Nas seções anteriores, você viu que algumas discussões detalhadas sobre como otimizar o design da tabela para recuperar dados de entidade usando consultas e para inserir, atualizar e excluir dados de entidade. Esta seção descreve alguns padrões adequados para uso com soluções de serviço Tabela. Além disso, você verá como abordar praticamente alguns dos problemas e compensações gerados anteriormente neste guia. O diagrama a seguir resume as relações entre os diferentes padrões:  

![Imagem dos padrões de design de tabela][5]

O mapa padrão acima destaca algumas relações entre padrões (azul) e antipadrões (laranja) documentados neste guia. Certamente há muitos outros padrões que vale a pena considerar. Por exemplo, um dos principais cenários para o serviço Tabela é utilizar o [Padrão de Exibição Materializada](https://msdn.microsoft.com/library/azure/dn589782.aspx) do padrão [CQRS (Segregação de Responsabilidade da Consulta de Comando)](https://msdn.microsoft.com/library/azure/jj554200.aspx).  

### <a name="intra-partition-secondary-index-pattern"></a>Padrão de índice secundário intrapartição
Armazene várias cópias de cada entidade usando valores diferentes de **RowKey** (na mesma partição) para permitir pesquisas rápidas e eficientes, bem como ordens de classificação alternativas usando valores diferentes de **RowKey**. Atualizações entre as cópias podem ser mantidas consistentes usando EGTs.  

#### <a name="context-and-problem"></a>Contexto e problema
O serviço Tabela indexa automaticamente as entidades usando os valores de **PartitionKey** e **RowKey**. Isso habilita um aplicativo cliente a recuperar uma entidade com eficiência usando esses valores. Por exemplo, usando a estrutura de tabela mostrada abaixo, um aplicativo cliente pode usar uma consulta de ponto para recuperar uma entidade de funcionário individual usando o nome do departamento e a ID do funcionário (os valores de **PartitionKey** e **RowKey**). Um cliente também pode recuperar entidades classificadas por ID de funcionário dentro de cada departamento.

![Entidade de funcionário][6]

Se você quiser ser capaz de encontrar uma entidade funcionário com base no valor de outra propriedade, como o endereço de email, deve usar uma verificação de partição menos eficiente para localizar uma correspondência. Isso ocorre porque o serviço Tabela não fornece índices secundários. Além disso, não há opção para solicitar uma lista de funcionários classificados em uma ordem diferente da ordem **RowKey** .  

#### <a name="solution"></a>Solução
Para solucionar a falta de índices secundários, armazene várias cópias de cada entidade com cada cópia, usando um valor diferente de **RowKey** . Se você armazenar uma entidade com as estruturas mostradas abaixo, poderá recuperar com eficiência entidades de funcionário com base na ID do funcionário ou endereço de email. Os valores de prefixo para **RowKey**, "empid_" e "email_" permitem a consulta de um único funcionário ou um intervalo de funcionários usando um intervalo de endereços de email ou IDs de funcionário.  

![Entidade Funcionário com diferentes valores de RowKey][7]

Os seguintes dois critérios de filtro (uma pesquisa por ID funcionário e uma por endereço de email) especificam consultas de ponto:  

* $filter=(PartitionKey eq 'Sales') and (RowKey eq 'empid_000223')  
* $filter=(PartitionKey eq 'Sales') e (RowKey eq 'email_jonesj@contoso.com')  

Ao consultar um intervalo de entidades de funcionário, você poderá, usando uma consulta às entidades com o prefixo apropriado em **RowKey**, especificar um intervalo classificado por ordem de ID de funcionário ou por ordem de endereços de email.  

* Para localizar todos os funcionários do departamento de vendas com uma ID de funcionário no intervalo 000100 a 000199, use: $filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid_000100') and (RowKey le 'empid_000199')  
* Para localizar todos os funcionários do departamento de Vendas com um endereço de email que começa com a letra 'a', use: $filter=(PartitionKey eq 'Sales') and (RowKey ge 'email_a') and (RowKey lt 'email_b')  
  
  A sintaxe de filtro usada nos exemplos acima é proveniente da API REST do serviço Tabela. Para saber mais, confira [Consultar Entidades](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Armazenamento de tabela é relativamente barato, portanto a sobrecarga de custos de armazenar dados duplicados não deve ser uma preocupação importante. No entanto, você deve sempre avaliar o custo de seu design com base nas necessidades de armazenamento previstas e só adicionar entidades duplicadas para dar suporte a consultas que seu aplicativo cliente executará.  
* Como as entidades de índice secundário são armazenadas na mesma partição que as entidades originais, você deve se certificar de não exceder as metas de escalabilidade para uma partição individual.  
* Você pode manter suas entidades duplicadas consistentes entre si usando EGTs para atualizar as duas cópias da entidade atomicamente. Isso significa que você deve armazenar todas as cópias de uma entidade na mesma partição. Para saber mais, confira a seção [Usando transações de grupo de entidades](#entity-group-transactions).  
* O valor usado para **RowKey** deve ser exclusivo para cada entidade. Considere o uso de valores de chave composta.  
* O preenchimento de valores numéricos na **RowKey** (por exemplo, a ID de funcionário 000223), permite classificação e filtragem corretas, com base em limites superiores e inferiores.  
* Você não precisa necessariamente duplicar todas as propriedades da entidade. Por exemplo, se as consultas que pesquisam as entidades usando o endereço de email em **RowKey** nunca precisarem da idade do funcionário, essas entidades poderão ter a seguinte estrutura:

![Entidade de funcionário][8]

* Normalmente é melhor armazenar dados duplicados e certificar-se de que você possa recuperar todos os dados que precisa com uma única consulta, do que usar uma consulta para localizar uma entidade e outra para pesquisar os dados necessários.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando o aplicativo cliente precisar recuperar entidades usando uma variedade de chaves diferentes, quando o cliente precisar recuperar entidades em diferentes ordens de classificação, e onde é possível identificar cada entidade usando uma variedade de valores exclusivos. No entanto, você deve ter certeza de que não excederá os limites de escalabilidade da partição durante a execução de pesquisas da entidade, usando valores diferentes de **RowKey** .  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de índice secundário entre partições](#inter-partition-secondary-index-pattern)
* [Padrão de chave composta](#compound-key-pattern)
* [Transações do Grupo de Entidades](#entity-group-transactions)
* [Trabalhando com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)

### <a name="inter-partition-secondary-index-pattern"></a>Padrão de índice secundário entre partições
Armazene várias cópias de cada entidade usando valores diferentes de **RowKey** em partições ou tabelas separadas para permitir pesquisas rápidas e eficientes, bem como ordens de classificação alternativas usando valores diferentes de **RowKey**.  

#### <a name="context-and-problem"></a>Contexto e problema
O serviço Tabela indexa automaticamente as entidades usando os valores de **PartitionKey** e **RowKey**. Isso habilita um aplicativo cliente a recuperar uma entidade com eficiência usando esses valores. Por exemplo, usando a estrutura de tabela mostrada abaixo, um aplicativo cliente pode usar uma consulta de ponto para recuperar uma entidade de funcionário individual usando o nome do departamento e a ID do funcionário (os valores de **PartitionKey** e **RowKey**). Um cliente também pode recuperar entidades classificadas por ID de funcionário dentro de cada departamento.  

![Entidade de funcionário][9]

Se você quiser ser capaz de encontrar uma entidade funcionário com base no valor de outra propriedade, como o endereço de email, deve usar uma verificação de partição menos eficiente para localizar uma correspondência. Isso ocorre porque o serviço Tabela não fornece índices secundários. Além disso, não há opção para solicitar uma lista de funcionários classificados em uma ordem diferente da ordem **RowKey** .  

Você está prevendo um volume alto de transações em relação a essas entidades e deseja minimizar o risco de a taxa do serviço Tabela limitar o seu cliente.  

#### <a name="solution"></a>Solução
Para solucionar a falta de índices secundários, você pode armazenar várias cópias de cada entidade com cada cópia usando valores diferentes de **PartitionKey** e **RowKey**. Se você armazenar uma entidade com as estruturas mostradas abaixo, poderá recuperar com eficiência entidades de funcionário com base na ID do funcionário ou endereço de email. Os valores de prefixo para **PartitionKey**, "empid_" e "email_" permitem que você identifique o índice que deseja usar para uma consulta.  

![Entidade Funcionário com o índice primário e entidade Funcionário com índice secundário][10]

Os seguintes dois critérios de filtro (uma pesquisa por ID funcionário e uma por endereço de email) especificam consultas de ponto:  

* $filter=(PartitionKey eq 'empid_Sales') and (RowKey eq '000223')
* $filter=(PartitionKey eq 'email_Sales') e (RowKey eq 'jonesj@contoso.com')  

Ao consultar um intervalo de entidades de funcionário, você poderá, usando uma consulta às entidades com o prefixo apropriado em **RowKey**, especificar um intervalo classificado por ordem de ID de funcionário ou por ordem de endereços de email.  

* Para localizar todos os funcionários do departamento de vendas com uma ID de funcionário no intervalo de **000100** a **000199**, classificados por ordem de ID de funcionário, use: $filter=(PartitionKey eq 'empid_Sales') e (RowKey ge '000100') e (RowKey le '000199')  
* Para localizar todos os funcionários do departamento de vendas com um endereço de email que comece com 'a', classificados por ordem de endereço de email, use: $filter=(PartitionKey eq 'email_Sales') and (RowKey ge 'a') and (RowKey lt 'b')  

Observe que a sintaxe de filtro usada nos exemplos acima é proveniente da API REST do serviço Tabela. Para saber mais, confira [Consultar Entidades](https://msdn.microsoft.com/library/azure/dd179421.aspx).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Você pode manter suas entidades duplicadas eventualmente consistentes entre si usando o [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) para manter as entidades de índice primário e secundário.  
* Armazenamento de tabela é relativamente barato, portanto a sobrecarga de custos de armazenar dados duplicados não deve ser uma preocupação importante. No entanto, você deve sempre avaliar o custo de seu design com base nas necessidades de armazenamento previstas e só adicionar entidades duplicadas para dar suporte a consultas que seu aplicativo cliente executará.  
* O valor usado para **RowKey** deve ser exclusivo para cada entidade. Considere o uso de valores de chave composta.  
* O preenchimento de valores numéricos na **RowKey** (por exemplo, a ID de funcionário 000223), permite classificação e filtragem corretas, com base em limites superiores e inferiores.  
* Você não precisa necessariamente duplicar todas as propriedades da entidade. Por exemplo, se as consultas que pesquisam as entidades usando o endereço de email em **RowKey** nunca precisarem da idade do funcionário, essas entidades poderão ter a seguinte estrutura:
  
  ![Entidade Funcionário com índice secundário][11]
* É geralmente melhor armazenar dados duplicados e garantir que você possa recuperar todos os dados que precisa com uma única consulta do que usar uma consulta para localizar uma entidade usando o índice secundário e outra para pesquisar os dados necessários no índice primário.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando o aplicativo cliente precisar recuperar entidades usando uma variedade de chaves diferentes, quando o cliente precisar recuperar entidades em diferentes ordens de classificação, e onde é possível identificar cada entidade usando uma variedade de valores exclusivos. Use esse padrão quando quiser evitar exceder os limites de escalabilidade da partição durante a execução de pesquisas de entidade usando os valores diferentes de **RowKey** .  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* [Padrão de índice secundário intrapartição](#intra-partition-secondary-index-pattern)  
* [Padrão de chave composta](#compound-key-pattern)  
* [Transações do Grupo de Entidades](#entity-group-transactions)  
* [Trabalhando com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)  

### <a name="eventually-consistent-transactions-pattern"></a>Padrão de transações eventualmente consistentes
Habilite comportamento eventualmente consistente entre limites de partição ou limites do sistema de armazenamento usando filas do Azure.  

#### <a name="context-and-problem"></a>Contexto e problema
EGTs habilitam transações atômicas entre várias entidades que compartilham a mesma chave de partição. Por motivos de escalabilidade e desempenho, você pode optar por armazenar entidades que têm requisitos de consistência em partições separadas ou em um sistema de armazenamento separado: nesse cenário, você não pode usar EGTs para manter a consistência. Por exemplo, você pode ter um requisito para manter a consistência eventual entre:  

* Entidades armazenadas em duas partições diferentes na mesma tabela, em tabelas diferentes ou em diferentes contas de armazenamento.  
* Uma entidade armazenada no serviço Tabela e um blob armazenado no serviço Blob.  
* Uma entidade armazenada no serviço Tabela e um arquivo em um sistema de arquivos.  
* Um repositório de entidades no serviço Tabela ainda indexado usando o serviço de Azure Search.  

#### <a name="solution"></a>Solução
Ao usar as filas do Azure, você pode implementar uma solução que fornece consistência eventual em duas ou mais partições ou sistemas de armazenamento.
Para ilustrar essa abordagem, suponha que você tenha de ser capaz de arquivar entidades antigas de funcionário. Entidades antigas de funcionário raramente são consultadas e devem ser excluídas de todas as atividades que lidam com funcionários atuais. Para implementar esse requisito, armazene funcionários ativos na tabela **Atual** e funcionários antigos na tabela **Arquivo morto**. O arquivamento de um funcionário exige a exclusão da entidade da tabela **Atual** e a adição da entidade à tabela **Arquivo morto**, mas não é possível usar uma EGT para executar essas duas operações. Para evitar o risco de que uma falha faça com que uma entidade seja exibida nas tabelas ou em nenhuma, a operação de arquivamento deve ser eventualmente consistente. O diagrama de sequência a seguir descreve as etapas nesta operação. Mais detalhes são fornecidos para caminhos de exceção no seguinte texto.  

![Diagrama de solução para consistência eventual][12]

Um cliente inicia a operação de arquivamento colocando uma mensagem em uma fila do Azure, neste exemplo para arquivar funcionário nº456. Uma função de trabalho controla a fila para novas mensagens; quando encontra uma, lê a mensagem e deixa uma cópia oculta na fila. A função de trabalho, em seguida, busca uma cópia da entidade na tabela **Atual**, insere uma cópia na tabela **Arquivo morto** e exclui o original da tabela **Atual**. Finalmente, se não houve erros das etapas anteriores, a função de trabalho exclui a mensagem oculta da fila.  

Neste exemplo, a etapa 4 insere o funcionário na tabela **Arquivo morto** . Ele pode adicionar o funcionário em um blob no serviço Blob ou um arquivo em um sistema de arquivos.  

#### <a name="recovering-from-failures"></a>Recuperando de falhas
Caso a função de trabalho precise reiniciar a operação de arquivamento, é importante que as operações nas etapas **4** e **5** sejam *idempotentes*. Se estiver usando o serviço Tabela, na etapa **4**, você deverá usar uma operação "inserir ou substituir"; na etapa **5**, você deverá usar uma operação "excluir se existir" na biblioteca de cliente que está usando. Se você estiver usando outro sistema de armazenamento, deve usar uma operação idempotente apropriada.  

Se a função de trabalho nunca concluir a etapa **6**, após um tempo limite a mensagem reaparecerá na fila, pronta para ser reprocessada pela função de trabalho. A função de trabalho pode verificar quantas vezes uma mensagem na fila foi lida e, se necessário, sinalizá-la como uma mensagem "suspeita" para investigação, enviando-a para uma fila separada. Para saber mais sobre a leitura de mensagens da fila e verificar a contagem de remoção da fila, consulte [Obter mensagens](https://msdn.microsoft.com/library/azure/dd179474.aspx).  

Alguns erros dos serviços Tabela e Fila são erros transitórios e o aplicativo cliente deve incluir uma lógica de repetição adequada para lidar com eles.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Esta solução não fornece isolamento da transação. Por exemplo, um cliente pode ler as tabelas **Atual** e **Arquivo morto** quando a função de trabalho estiver entre as etapas **4** e **5** e ver uma exibição inconsistente dos dados. Os dados serão consistentes eventualmente.  
* Você deve se certificar de que as etapas 4 e 5 sejam idempotentes para garantir a consistência eventual.  
* Você pode dimensionar a solução usando várias filas e instâncias de função de trabalho.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando quiser garantir a consistência eventual entre entidades que existem nas tabelas ou partições diferentes. Você pode estender esse padrão para garantir a consistência eventual de operações entre o serviço Tabela e o serviço Blob e outras fontes de dados de armazenamento não Azure, como banco de dados ou sistema de arquivos.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Transações do Grupo de Entidades](#entity-group-transactions)  
* [Mesclar ou substituir](#merge-or-replace)  

> [!NOTE]
> Se o isolamento da transação for importante para sua solução, você deve considerar a recriação das tabelas para poder usar as EGTs.  
> 
> 

### <a name="index-entities-pattern"></a>Padrão de entidades de índice
Mantenha entidades de índice para habilitar pesquisas eficientes que retornam listas de entidades.  

#### <a name="context-and-problem"></a>Contexto e problema
O serviço Tabela indexa automaticamente as entidades usando os valores de **PartitionKey** e **RowKey**. Isso habilita um aplicativo cliente a recuperar uma entidade com eficiência usando uma consulta de ponto. Por exemplo, usando a estrutura de tabela mostrada abaixo, um aplicativo cliente pode recuperar de maneira eficiente uma entidade de funcionário individual pelo uso do nome do departamento e da ID do funcionário (**PartitionKey** e **RowKey**).  

![Entidade de funcionário][13]

Se você quiser ser capaz de recuperar uma lista de entidades de funcionário com base no valor de outra propriedade não exclusiva, como seu sobrenome, deve usar uma verificação de partição menos eficiente para localizar correspondências em vez de usar um índice para examiná-las diretamente. Isso ocorre porque o serviço Tabela não fornece índices secundários.  

#### <a name="solution"></a>Solução
Para habilitar a pesquisa por sobrenome com a estrutura de entidade mostrada acima, você deve manter listas de ids de funcionário. Para recuperar entidades de funcionário com determinado sobrenome, como Dias, primeiro localize a lista de ids de funcionário para os funcionários com Dias como sobrenome e recupere essas entidades. Há três opções principais para armazenar listas de ids de funcionário:  

* Use o armazenamento de blobs.  
* Crie entidades de índice na mesma partição que as entidades do funcionário.  
* Crie entidades de índice em uma partição ou tabela separada.  

<u>Opção nº 1: Usar o Armazenamento de Blobs</u>  

Para a primeira opção, crie um blob para todos os sobrenomes exclusivos e em cada repositório de blobs uma lista de valores **PartitionKey** (departamento) e **RowKey** (ID do funcionário) para os funcionários com esse sobrenome. Quando você adiciona ou exclui um funcionário, deve garantir que o conteúdo do blob relevante seja eventualmente consistente com as entidades do funcionário.  

<u>Opção nº 2:</u> Criar entidades de índice na mesma partição  

Para a segunda opção, use as entidades de índice que armazenam os dados a seguir:  

![Entidade Funcionário com a cadeia de caracteres que contém uma lista de IDs de funcionário com o mesmo sobrenome][14]

A propriedade **EmployeeIDs** contém uma lista de IDs de funcionário para os funcionários com o sobrenome armazenado em **RowKey**.  

As etapas a seguir descrevem o processo que você deve seguir ao adicionar um novo funcionário, se você estiver usando a segunda opção. Neste exemplo, estamos adicionando um funcionário com ID 000152 e um sobrenome Jones no departamento de Vendas:  

1. Recupere a entidade de índice com um valor de **PartitionKey** igual a "Vendas" e valor de **RowKey** igual a "Dias". Salve o ETag dessa entidade para usar na etapa 2.  
2. Crie uma transação de grupo de entidades (ou seja, uma operação em lote) que insira a nova entidade de funcionário (valor de **PartitionKey** igual a "Vendas" e valor de **RowKey** igual a "000152") e atualize a entidade de índice (valor de **PartitionKey** igual a "Vendas" e valor de **RowKey** igual a "Dias"), adicionando a ID do novo funcionário à lista no campo EmployeeIDs. Para saber mais sobre transações de grupo de entidades, confira a seção [Transações de grupo de entidades](#entity-group-transactions).  
3. Se a transação de grupo de entidades falhar devido a um erro de simultaneidade otimista (alguém modificou a entidade de índice), será necessário recomeçar na etapa 1.  

Você pode usar uma abordagem semelhante à exclusão de um funcionário se usar a segunda opção. Alterar o sobrenome do funcionário é um pouco mais complexo, pois você precisará executar uma transação de grupo de entidades que atualiza as três entidades: a entidade funcionário, a entidade de índice para o sobrenome antigo e a entidade de índice para o novo sobrenome. Você deve recuperar cada entidade antes de fazer alterações para recuperar os valores de ETag que depois pode usar para executar as atualizações usando a simultaneidade otimista.  

As etapas abaixo descrevem o processo que você deve seguir quando precisar procurar todos os funcionários com determinado sobrenome em um departamento, se estiver usando a segunda opção. Neste exemplo, estamos procurando todos os funcionários com o sobrenome Dias no departamento de Vendas:  

1. Recupere a entidade de índice com um valor de **PartitionKey** igual a "Vendas" e valor de **RowKey** igual a "Dias".  
2. Analise a lista de Ids no campo EmployeeIDs.  
3. Se precisar de informações adicionais sobre cada um desses funcionários (como endereços de email), recupere cada uma das entidades de funcionário usando o valor de **PartitionKey** igual a "Vendas" e os valores de **RowKey** da lista de funcionários obtida na etapa 2.  

<u>Opção nº 3:</u> Criar entidades de índice em uma partição ou tabela separada  

Para a terceira opção, use as entidades de índice que armazenam os dados a seguir:  

![Entidade Funcionário com a cadeia de caracteres que contém uma lista de IDs de funcionário com o mesmo sobrenome][15]

A propriedade **EmployeeIDs** contém uma lista de IDs de funcionário para os funcionários com o sobrenome armazenado em **RowKey**.  

Com a terceira opção, você não pode usar EGTs para manter a consistência porque as entidades de índice estão em uma partição separada das entidades de funcionário. Certifique-se de que as entidades de índice sejam eventualmente consistentes com as entidades de funcionário.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Essa solução exige, pelo menos, duas consultas para recuperar entidades correspondentes: uma para consultar as entidades de índice para obter a lista de valores de **RowKey** e consultas para recuperar cada entidade na lista.  
* Considerando que uma entidade individual tem um tamanho máximo de 1 MB, as opções nº2 e 3 na solução supõem que a lista de IDs de funcionário para determinado sobrenome nunca é maior que 1 MB. Se a lista de ids de funcionário é provavelmente maior que 1 MB em tamanho, use a opção nº1 e armazene os dados de índice no armazenamento de blob.  
* Se você usar a opção n. 2 (usando EGTs para controlar a adição e exclusão de funcionários e alterar o sobrenome do funcionário), você deverá avaliar se o volume de transações abordará os limites de escalabilidade em uma determinada partição. Se esse for o caso, você deve considerar uma solução eventualmente consistente (opção nº1 ou 3) que usa filas para manipular solicitações de atualização e permite que você armazene suas entidades de índice em uma partição separada das entidades de funcionário.  
* A opção nº2 nesta solução pressupõe que você deseja pesquisar por sobrenome dentro de um departamento: por exemplo, você quer recuperar uma lista de funcionários com um sobrenome Jones no departamento de Vendas. Para pesquisar todos os funcionários com um sobrenome Jones em toda a organização, use a opção nº1 ou 3.
* É possível implementar uma solução baseada em fila que fornece consistência eventual (veja [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) para obter mais detalhes).  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando quiser pesquisar um conjunto de entidades que compartilham um valor da propriedade comum, como todos os funcionários com o sobrenome Dias.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* [Transações do Grupo de Entidades](#entity-group-transactions)  
* [Trabalhando com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)  

### <a name="denormalization-pattern"></a>Padrão de desnormalização
Combine dados relacionados juntos em uma única entidade para que você possa recuperar todos os dados que precisa com uma ponto único de consulta.  

#### <a name="context-and-problem"></a>Contexto e problema
Em um banco de dados relacional, você geralmente normaliza dados para remover a duplicação, resultando em consultas que recuperam dados de várias tabelas. Se você normalizar dados em tabelas do Azure, terá de ir e voltar várias vezes do cliente ao servidor para recuperar os dados relacionados. Por exemplo, com a estrutura de tabela mostrada abaixo, você precisa de dois viagens de ida e volta para recuperar os detalhes de um departamento: uma para buscar a entidade do departamento que inclui a ID do gerente e outra solicitação para buscar detalhes do gerente em uma entidade de funcionário.  

![Entidade de departamento e funcionário][16]

#### <a name="solution"></a>Solução
Em vez de armazenar os dados em duas entidades separadas, desnormalize os dados e mantenha uma cópia dos detalhes do gerente na entidade de departamento. Por exemplo:   

![Entidade Departamento desnormalizada e combinada][17]

Com entidades de departamento armazenadas com essas propriedades, agora você pode recuperar todos os detalhes de que precisa sobre um departamento usando uma consulta de ponto.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Existe alguma sobrecarga de custo associada ao armazenar alguns dados duas vezes. O benefício de desempenho (resultante de menos solicitações ao serviço de armazenamento) normalmente supera o aumento marginal nos custos de armazenamento (e esse custo é parcialmente compensado por uma redução no número de transações que você precisa para obter detalhes de um departamento).  
* Você deve manter a consistência das duas entidades que armazenam informações sobre os gerentes. Você pode tratar o problema de consistência usando EGTs para atualizar várias entidades em uma única transação atômica: nesse caso, a entidade de departamento e a entidade de funcionário para o gerente do departamento são armazenadas na mesma partição.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar pesquisar informações relacionadas com frequência. Esse padrão reduz o número de consultas que o cliente deve fazer para recuperar os dados necessários.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Transações do Grupo de Entidades](#entity-group-transactions)  
* [Trabalhando com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)

### <a name="compound-key-pattern"></a>Padrão de chave composta
Use valores **RowKey** compostos para permitir que um cliente pesquise dados relacionados a uma consulta de ponto único.  

#### <a name="context-and-problem"></a>Contexto e problema
Em um banco de dados relacional, é neutro usar junções em consultas para retornar partes relacionadas de dados para o cliente em uma única consulta. Por exemplo, você pode usar a ID do funcionário para pesquisar uma lista de entidades relacionadas que contém o desempenho e analisar os dados desse funcionário.  

Suponhamos que você esteja armazenando entidades de funcionário no serviço Tabela usando a seguinte estrutura:  

![Entidade de funcionário][18]

Você também precisa armazenar dados históricos relacionados a revisões e desempenho de cada ano que o funcionário trabalhou para a sua organização e precisa ser capaz de acessar essas informações por ano. Uma opção é criar outra tabela que armazena entidades com a seguinte estrutura:  

![Entidade Análise do funcionário][19]

Observe que com essa abordagem você poderá duplicar algumas informações (como nome e sobrenome) na nova entidade para recuperar seus dados com uma única solicitação. No entanto, não é possível manter uma coerência forte porque você não pode usar uma EGT para atualizar as duas entidades atomicamente.  

#### <a name="solution"></a>Solução
Armazene um novo tipo de entidade em sua tabela original usando entidades com a seguinte estrutura:  

![Entidade Funcionário com chave composta][20]

Observe como a **RowKey** agora é uma chave composta de ID de funcionário e o ano dos dados de revisão que permitem recuperar o desempenho do funcionário e analisar os dados com uma única solicitação para uma única entidade.  

O exemplo a seguir descreve como você pode recuperar todos os dados de revisão de um funcionário específico (por exemplo, um funcionário 000123 no departamento de Vendas):  

$filter=(PartitionKey eq 'Sales') and (RowKey ge 'empid_000123') and (RowKey lt 'empid_000124')&$select=RowKey,Manager Rating,Peer Rating,Comments  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Você deve usar um caractere separador adequado que facilite a análise do valor de **RowKey**: por exemplo, **000123_2012**.  
* Você também está armazenando essa entidade na mesma partição que outras entidades que contêm dados relacionados para o mesmo funcionário, o que significa que você pode usar EGTs para manter a coerência forte.
* Você deve considerar com que frequência consultará os dados para determinar se esse padrão é apropriado.  Por exemplo, se você for acessar os dados de revisão com pouca frequência e os dados principais do funcionário com mais frequência, deve mantê-los como entidades separadas.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar armazenar uma ou mais entidades relacionadas que você consulta com frequência.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Transações do Grupo de Entidades](#entity-group-transactions)  
* [Trabalhando com tipos de entidade heterogênea](#working-with-heterogeneous-entity-types)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  

### <a name="log-tail-pattern"></a>Padrão de rastro do log
Recupere as *n* entidades adicionadas recentemente em uma partição usando um valor de **RowKey** que classifica em ordem de data e hora inversa.  

> [!NOTE]
> Resultados de consulta retornados pela API de tabela do Azure no banco de dados do Azure não são classificados por chave de partição ou chave de linha. Portanto, esse padrão é adequado para o Armazenamento de Tabelas do Azure e não para o Azure Cosmos DB. Para obter uma lista detalhada das diferenças entre os recursos, consulte [Diferenças entre a API de Tabela no Azure Cosmos DB e no Armazenamento de Tabelas do Azure](faq.md#where-is-table-api-not-identical-with-azure-table-storage-behavior).

#### <a name="context-and-problem"></a>Contexto e problema
Um requisito comum é conseguir recuperar as entidades criadas mais recentemente, por exemplo, as dez solicitações de despesas mais recentes enviadas por um funcionário. As consultas de tabela dão suporte a uma operação de consulta **$top** para retornar as primeiras *n* entidades de consulta de um conjunto: não há uma operação de consulta equivalente para retornar as últimas n entidades de um conjunto.  

#### <a name="solution"></a>Solução
Armazene as entidades usando uma **RowKey** que classifica naturalmente em ordem inversa de data/hora. Fazendo isso, a entrada mais recente será sempre a primeira na tabela.  

Por exemplo, para recuperar os dez reembolsos de despesa mais recentes enviadas por um funcionário, você pode usar um valor de escala inversa derivado de data/hora atual. O seguinte exemplo de código C# mostra uma maneira de criar um valor adequado de "escala invertida" para uma **RowKey** que classifica do mais recente ao mais antigo:  

`string invertedTicks = string.Format("{0:D19}", DateTime.MaxValue.Ticks - DateTime.UtcNow.Ticks);`  

Você pode retornar ao valor de data/hora usando o seguinte código:  

`DateTime dt = new DateTime(DateTime.MaxValue.Ticks - Int64.Parse(invertedTicks));`  

A consulta a tabela tem esta aparência:  

`https://myaccount.table.core.windows.net/EmployeeExpense(PartitionKey='empid')?$top=10`  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Você precisa preencher o valor da escala invertida com zeros para garantir que o valor de cadeia de caracteres seja classificado conforme o esperado.  
* Você deve estar ciente das metas de escalabilidade no nível de uma partição. Cuidado não criar partições com ponto de acesso.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar acessar entidades na ordem inversa de data/hora ou quando precisar acessar entidades adicionadas mais recentemente.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Antipadrão prefixar/acrescentar](#prepend-append-anti-pattern)  
* [Recuperando entidades](#retrieving-entities)  

### <a name="high-volume-delete-pattern"></a>Padrão de exclusão de alto volume
Habilite a exclusão de um alto volume de entidades armazenando todas as entidades para exclusão simultânea em suas próprias tabelas separadas; você exclui as entidades excluindo a tabela.  

#### <a name="context-and-problem"></a>Contexto e problema
Muitos aplicativos excluem dados antigos que não precisam mais estar disponíveis para um aplicativo cliente, ou que o aplicativo tenha arquivado em outro meio de armazenamento. Você geralmente identifica esses dados por uma data: por exemplo, você tem uma solicitação para excluir registros de todas as solicitações de entrada com mais de 60 dias.  

Um design possível é usar a data e a hora da solicitação de entrada na **RowKey**:  

![Entidade Tentativa de logon][21]

Essa abordagem evita sobrecargas de partição porque o aplicativo pode inserir e excluir entidades de entrada para cada usuário em uma partição separada. No entanto, essa abordagem pode ser cara e demorada se você tiver um grande número de entidades porque primeiro é necessário executar uma verificação de tabela para identificar todas as entidades a excluir e, em seguida, excluir todas as entidades antigas. Você pode reduzir o número de viagens de ida e volta ao servidor, necessário para excluir as entidades antigas por envio em lote de várias solicitações de exclusão para as EGTs.  

#### <a name="solution"></a>Solução
Use uma tabela separada para cada dia de tentativas de entrada. Você pode usar o design de entidade acima para evitar pontos de acesso ao inserir entidades e excluir entidades antigas agora é simplesmente uma questão de excluir uma tabela a cada dia (uma única operação de armazenamento), em vez de localizar e excluir centenas de milhares de entidades de entrada individuais todos os dias.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Seu design dá suporte a outras formas de uso dos dados pelo aplicativo, como pesquisa em entidades específicas, vinculação com outros dados ou geração de informações agregadas?  
* O design evita pontos de acesso quando você está inserindo novas entidades?  
* Espere um atraso, se você quiser reutilizar o mesmo nome de tabela após a exclusão. É melhor sempre usar nomes de tabela exclusivos.  
* Espere alguma limitação de taxa quando você usar pela primeira vez uma nova tabela enquanto o serviço Tabela aprende os padrões de acesso e distribui as partições entre os nós. Você deve considerar com que frequência precisa criar novas tabelas.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando tiver um alto volume de entidades que devem ser excluídas ao mesmo tempo.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Transações do Grupo de Entidades](#entity-group-transactions)
* [Modificando entidades](#modifying-entities)  

### <a name="data-series-pattern"></a>Padrão de série de dados
Armazene série de dados completa em uma única entidade para minimizar o número de solicitações que você faz.  

#### <a name="context-and-problem"></a>Contexto e problema
Um cenário comum é um aplicativo armazenar uma série de dados que ele normalmente precisa recuperar uma só vez. Por exemplo, seu aplicativo pode registrar a quantidade de mensagens instantâneas que cada funcionário envia por hora e usar essas informações para avaliar quantas mensagens cada usuário enviou nas 24 horas anteriores. Um design pode ser armazenar 24 entidades para cada funcionário:  

![Entidade de estatísticas de mensagem][22]

Com esse design, você pode facilmente localizar e atualizar a entidade a ser atualizada para cada funcionário sempre que o aplicativo precisar atualizar o valor de contagem das mensagem. No entanto, para recuperar as informações e traçar um gráfico de atividades das 24 horas anteriores, você deve recuperar 24 entidades.  

#### <a name="solution"></a>Solução
Use o design a seguir com uma propriedade separada para armazenar a contagem de mensagens para cada hora:  

![Entidade Estatísticas de mensagem com propriedades separadas][23]

Com esse design, você pode usar uma operação de mesclagem para atualizar a contagem de mensagens de um funcionário, em uma hora específica. Agora, você pode recuperar todas as informações necessárias traçar o gráfico usando uma solicitação para uma única entidade.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Se a sua série de dados completa não couber em uma única entidade (uma entidade pode ter até 252 propriedades), use um repositório de dados alternativo, como um blob.  
* Caso você tenha vários clientes atualizando uma entidade simultaneamente, será necessário usar o **Etag** para implementar a simultaneidade otimista. Se você tiver muitos clientes, poderá enfrentar alta contenção.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar atualizar e recuperar uma série de dados associados a uma entidade individual.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de grandes entidades](#large-entities-pattern)  
* [Mesclar ou substituir](#merge-or-replace)  
* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) (se você estiver armazenando a série de dados em um blob)  

### <a name="wide-entities-pattern"></a>Padrão de entidades longas
Use várias entidades físicas para armazenar entidades lógicas com mais de 252 propriedades.  

#### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual pode ter no máximo 252 propriedades (exceto as propriedades de sistema obrigatórias) e não pode armazenar mais de 1 MB de dados no total. Em um banco de dados relacional, você normalmente contornaria os limites de tamanho de uma linha adicionando uma nova tabela e impondo uma relação de 1 para 1 entre elas.  

#### <a name="solution"></a>Solução
Usando o serviço Tabela, você pode armazenar várias entidades para representar um único objeto grande de negócios com mais de 252 propriedades. Por exemplo, para armazenar uma contagem do número de mensagens instantâneas enviadas por cada funcionário nos últimos 365 dias, você poderia usar o design a seguir que usa duas entidades com diferentes esquemas:  

![Entidade Estatísticas de mensagem com Rowkey 01 e entidade Estado da mensagem com Rowkey 02][24]

Se você precisar fazer uma alteração que requer a atualização de ambas as entidades para mantê-las sincronizadas entre si, pode usar uma EGT. Caso contrário, você pode usar uma operação de mesclagem única para atualizar a contagem de mensagens de um dia específico. Para recuperar todos os dados de um funcionário individual, você deverá recuperar as duas entidades, o que pode ser feito com duas solicitações eficientes que usam um valor de **PartitionKey** e um de **RowKey**.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* A recuperação de uma entidade lógica completa envolve pelo menos duas transações de armazenamento: uma para recuperar cada entidade física.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar armazenar entidades cujo tamanho ou número de propriedades excede os limites de uma entidade individual no serviço Tabela.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Transações do Grupo de Entidades](#entity-group-transactions)
* [Mesclar ou substituir](#merge-or-replace)

### <a name="large-entities-pattern"></a>Padrão de entidades grandes
Use armazenamento de blobs para armazenar grandes valores de propriedade.  

#### <a name="context-and-problem"></a>Contexto e problema
Uma entidade individual não pode armazenar mais de 1 MB de dados no total. Se uma ou várias de suas propriedades armazenam valores que fazem com que o tamanho total da entidade exceda esse valor, você não pode armazenar a entidade inteira no serviço Tabela.  

#### <a name="solution"></a>Solução
Se a entidade exceder 1 MB de tamanho porque uma ou mais propriedades contêm uma grande quantidade de dados, você poderá armazenar dados no serviço Blob e, em seguida, armazenar o endereço do blob em uma propriedade na entidade. Por exemplo, você pode armazenar a foto de um funcionário no armazenamento de blobs e armazenar um link para a foto na propriedade **Foto** da entidade funcionário:  

![Entidade Funcionário com a cadeia de caracteres para a Foto que aponta para o Armazenamento de Blobs][25]

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* Para manter a consistência eventual entre a entidade do serviço Tabela e os dados no serviço Blob, use o [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern) para manter suas entidades.
* A recuperação de uma entidade completa envolve pelo menos duas transações de armazenamento: uma para recuperar a entidade e outra para recuperar os dados do blob.  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Use esse padrão quando precisar armazenar entidades cujo tamanho excede os limites de uma entidade individual no serviço Tabela.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de transações eventualmente consistentes](#eventually-consistent-transactions-pattern)  
* [Padrão de entidades longas](#wide-entities-pattern)

<a name="prepend-append-anti-pattern"></a>

### <a name="prependappend-anti-pattern"></a>Antipadrão prefixar/acrescentar
Aumente a escalabilidade quando tiver um alto volume de inserções, distribuindo as inserções em várias partições.  

#### <a name="context-and-problem"></a>Contexto e problema
A prefixação ou o acréscimo de entidades às suas entidades armazenadas normalmente fazem com que o aplicativo adicione novas entidades à primeira ou última partição de uma sequência de partições. Nesse caso, todas as inserções em um determinado momento estão ocorrendo na mesma partição, criando um ponto de acesso que impede o serviço Tabela de carregar inserções de balanceamento em vários nós e, possivelmente, fazendo com que o aplicativo atinja as metas de escalabilidade para partição. Por exemplo, se você tiver um aplicativo que registre acesso a rede e recursos por funcionários, então uma estrutura de entidade, conforme mostrado a seguir, poderá fazer com que a partição de hora atual se transforme em um ponto de acesso, caso o volume de transações atinja a meta de escalabilidade de uma partição individual:  

![Entidade de funcionário][26]

#### <a name="solution"></a>Solução
A seguinte estrutura de entidade alternativa evita um ponto de acesso em qualquer partição específica quando o aplicativo registra eventos:  

![Entidade Funcionário com RowKey composto pelo Ano, Mês, Dia, Hora e ID de Evento][27]

Observe com este exemplo como **PartitionKey** e **RowKey** são chaves compostas. A **PartitionKey** usa a ID de funcionário e departamento para distribuir o registro em log entre várias partições.  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como implementar esse padrão:  

* A estrutura de chave alternativa que evita a criação de partições ativas em inserções dá suporte eficiente a consultas que o aplicativo cliente faz?  
* O volume antecipado de transações significa que você tem probabilidade de atingir as metas de escalabilidade para uma partição individual e ser limitado pelo serviço de armazenamento?  

#### <a name="when-to-use-this-pattern"></a>Quando usar esse padrão
Evite o antipadrão prefixar/acrescentar quando o volume de transações tiver a probabilidade de resultar em limitação de taxa pelo serviço de armazenamento quando você acessar uma partição ativa.  

#### <a name="related-patterns-and-guidance"></a>Diretrizes e padrões relacionados
Os padrões e diretrizes a seguir também podem ser relevantes ao implementar esse padrão:  

* [Padrão de chave composta](#compound-key-pattern)  
* [Padrão de final do log](#log-tail-pattern)  
* [Modificando entidades](#modifying-entities)  

### <a name="log-data-anti-pattern"></a>Antipadrão de dados de log
Normalmente, você deveria usar o serviço Blob em vez do serviço Tabela para armazenar dados de log.  

#### <a name="context-and-problem"></a>Contexto e problema
Um caso de uso comum de dados de log é recuperar uma seleção de entradas de log para um intervalo de data/hora específico: por exemplo, você deseja localizar todos os erros e mensagens críticas que seu aplicativo registrou entre 15:04 e 15:06 em uma data específica. Você não deseja usar a data e hora da mensagem de log para determinar a partição em que você salvou as entidades de log: isso resulta em uma partição ativa porque, a qualquer momento, todas as entidades de log compartilharão o mesmo valor **PartitionKey** (confira a seção [Antipadrão de prefixação/acréscimo](#prepend-append-anti-pattern)). Por exemplo, o seguinte esquema de entidade para uma mensagem de log resulta em uma partição ativa porque o aplicativo grava todas as mensagens de log na partição para a data e hora atual:  

![Entidade de mensagem de log][28]

Neste exemplo, a **RowKey** inclui a data e hora da mensagem de log para garantir que as mensagens de log sejam armazenadas classificadas em ordem de data/hora; inclui também uma ID de mensagem, caso várias mensagens de log compartilhem a mesma data e hora.  

Outra abordagem é usar uma **PartitionKey** que garanta que o aplicativo grave mensagens em uma variedade de partições. Por exemplo, se a origem da mensagem de log fornecer uma maneira de distribuir mensagens através de várias partições, você pode usar o seguinte esquema de entidade:  

![Entidade de mensagem de log][29]

No entanto, o problema com esse esquema é que para recuperar todas as mensagens de log para um período de tempo específico você deve pesquisar cada partição na tabela.

#### <a name="solution"></a>Solução
A seção anterior realçou o problema de uma tentativa de usar o serviço Tabela para armazenar entradas de log e sugeriu dois designs insatisfatórios. Uma solução resultou em uma partição ativa com o risco de mau desempenho ao gravar mensagens de log; a outra solução resultou em mau desempenho de consulta por causa do requisito de verificar cada partição da tabela para recuperar mensagens de log de um período específico. O Armazenamento de blob oferece uma solução melhor para esse tipo de cenário e é assim que o Azure Storage Analytics armazena os dados de log que coleta.  

Esta seção descreve como o Storage Analytics armazena dados de log no armazenamento de blobs como uma ilustração dessa abordagem de armazenamento de dados que você normalmente consulta por intervalo.  

O Storage Analytics armazena mensagens de log em um formato delimitado em vários blobs. O formato delimitado facilita para um aplicativo cliente analisar os dados na mensagem de log.  

O Storage Analytics usa uma convenção de nomenclatura para blobs que permite que você localize o blob (ou blobs) que contêm as mensagens de log que você está pesquisando. Por exemplo, um blob denominado "queue/2014/07/31/1800/000001.log" contém mensagens de log que se relacionam com o serviço Fila para a hora de início às 18:00 em 31 de julho de 2014. "000001" indica que este é o primeiro arquivo de log para esse período. O Storage Analytics também registra carimbos de data/hora das últimas e primeiras mensagens de log armazenadas no arquivo como parte dos metadados do blob. A API do armazenamento ativo permite localizar blobs em um contêiner com base em um prefixo de nome: para localizar todos os blobs que contêm dados de log na fila para o horário com início às 18:00, você pode usar o prefixo "queue/2014/07/31/1800."  

A Análise de Armazenamento armazena em buffer as mensagens de log internamente, e periodicamente atualiza o blob apropriado ou cria um novo com o último lote de entradas de log. Isso reduz o número de gravações que ele deve realizar para o serviço Blob.  

Se você estiver implementando uma solução semelhante em seu próprio aplicativo, deve considerar como gerenciar a compensação entre a confiabilidade (gravar todas as entradas de log no armazenamento de blobs à medida que ocorre) e o custo e a escalabilidade (armazenar as atualizações em buffer em seu aplicativo e gravá-las no armazenamento de blobs em lotes).  

#### <a name="issues-and-considerations"></a>Problemas e considerações
Considere os seguintes pontos ao decidir como armazenar dados de log:  

* Se você criar um design de tabela que evita potenciais partições ativas, verá que não pode acessar os dados do log com eficiência.  
* Para processar dados de log, um cliente normalmente precisa carregar vários registros.  
* Embora os dados de log sejam geralmente estruturados, o armazenamento de blobs pode ser uma solução melhor.  

### <a name="implementation-considerations"></a>Considerações sobre a implementação
Esta seção discute algumas das considerações a serem lembradas ao implementar os padrões descritos nas seções anteriores. Grande parte dessa seção usa exemplos escritos em c# que usam a Biblioteca de Cliente de Armazenamento (versão 4.3.0 no momento da redação).  

### <a name="retrieving-entities"></a>Recuperando entidades
Conforme discutido na seção [Design para consulta](#design-for-querying), a consulta mais eficiente é uma consulta de ponto. Entretanto, em alguns cenários talvez seja necessário recuperar várias entidades. Esta seção descreve algumas abordagens comuns para recuperar entidades usando a Biblioteca de Cliente de Armazenamento.  

#### <a name="executing-a-point-query-using-the-storage-client-library"></a>Executando uma consulta de ponto usando a Biblioteca de Cliente de Armazenamento
A maneira mais fácil de executar uma consulta de ponto é usar a operação da tabela **Recuperar**, conforme mostrado no snippet de código de C# a seguir, que recupera uma entidade com uma **PartitionKey** de valor "Vendas" e uma **RowKey** de valor "212":  

```csharp
TableOperation retrieveOperation = TableOperation.Retrieve<EmployeeEntity>("Sales", "212");
var retrieveResult = employeeTable.Execute(retrieveOperation);
if (retrieveResult.Result != null)
{
    EmployeeEntity employee = (EmployeeEntity)retrieveResult.Result;
    ...
}  
```

Observe como esse exemplo espera que a entidade recuperada seja do tipo **EmployeeEntity**.  

#### <a name="retrieving-multiple-entities-using-linq"></a>Recuperando várias entidades usando LINQ
Você pode recuperar várias entidades usando LINQ com a Biblioteca de cliente de armazenamento e especificando uma consulta com uma cláusula **where** . Para evitar uma verificação de tabela, você sempre deve incluir o valor de **PartitionKey** na cláusula where e, se possível, o valor de **RowKey** para evitar verificações de tabela e de partição. O serviço Tabela dá suporte a um conjunto limitado de operadores de comparação (maior que, maior que ou igual a, menor que, menor que ou igual a, igual a, e diferente de) para usar na cláusula where. O seguinte snippet de código em C# localiza todos os funcionários cujo sobrenome começa com "B" (supondo que **RowKey** armazena o sobrenome) no departamento de vendas (supondo que **PartitionKey** armazena o nome do departamento):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = employeeTable.CreateQuery<EmployeeEntity>();
var query = (from employee in employeeQuery
            where employee.PartitionKey == "Sales" &&
            employee.RowKey.CompareTo("B") >= 0 &&
            employee.RowKey.CompareTo("C") < 0
            select employee).AsTableQuery();
var employees = query.Execute();  
```

Observe como a consulta especifica uma **RowKey** e também uma **PartitionKey** para garantir um melhor desempenho.  

O exemplo de código a seguir mostra a funcionalidade equivalente usando a API fluente (para saber mais sobre APIs fluentes em geral, confira [Práticas recomendadas para a criação de uma API fluente](https://visualstudiomagazine.com/articles/2013/12/01/best-practices-for-designing-a-fluent-api.aspx)):  

```csharp
TableQuery<EmployeeEntity> employeeQuery = new TableQuery<EmployeeEntity>().Where(
    TableQuery.CombineFilters(
    TableQuery.CombineFilters(
        TableQuery.GenerateFilterCondition(
    "PartitionKey", QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.GenerateFilterCondition(
    "RowKey", QueryComparisons.GreaterThanOrEqual, "B")
),
TableOperators.And,
TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "C")
    )
);
var employees = employeeTable.ExecuteQuery(employeeQuery);  
```

> [!NOTE]
> O exemplo aninha vários métodos **CombineFilters** para incluir as três condições de filtro.  
> 
> 

#### <a name="retrieving-large-numbers-of-entities-from-a-query"></a>Recuperando grande número de entidades de uma consulta
Uma consulta ideal retorna uma entidade individual com base em um valor de **PartitionKey** e um valor de **RowKey**. No entanto, em alguns cenários, você pode precisar retornar muitas entidades da mesma partição ou até mesmo de várias partições.  

Você deve sempre testar totalmente o desempenho do seu aplicativo nesses cenários.  

Uma consulta no serviço Tabela pode retornar um máximo de 1.000 entidades de uma só vez e ser executada por um máximo de cinco segundos. Se o conjunto de resultados contiver mais de 1.000 entidades, se a consulta não for concluída em até cinco segundos, ou se a consulta ultrapassar o limite da partição, o serviço Tabela retornará um token de continuação para habilitar o aplicativo cliente a solicitar o próximo conjunto de entidades. Para saber mais sobre como funcionam os tokens de continuação, confira [Tempo limite e paginação de consulta](https://msdn.microsoft.com/library/azure/dd135718.aspx).  

Se você estiver usando a Biblioteca de Cliente de Armazenamento, ela pode automaticamente controlar os tokens de continuação para você, à medida que retorna entidades do serviço Tabela. O seguinte exemplo de código C# usando a Biblioteca de cliente de armazenamento trata automaticamente os tokens de continuação, se o serviço Tabela retorná-los em uma resposta:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

var employees = employeeTable.ExecuteQuery(employeeQuery);
foreach (var emp in employees)
{
        ...
}  
```

O código c# a seguir trata os tokens de continuação explicitamente:  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

TableContinuationToken continuationToken = null;

do
{
        var employees = employeeTable.ExecuteQuerySegmented(
        employeeQuery, continuationToken);
    foreach (var emp in employees)
    {
    ...
    }
    continuationToken = employees.ContinuationToken;
} while (continuationToken != null);  
```

Usando tokens de continuação explicitamente, você pode controlar quando o aplicativo recupera o próximo segmento de dados. Por exemplo, se seu aplicativo cliente habilitar os usuários a percorrer as entidades armazenadas em uma tabela, um usuário poderá decidir não percorrer todas as entidades recuperadas pela consulta para que seu aplicativo só use um token de continuação para recuperar o próximo segmento quando o usuário tive terminado a paginação de todas as entidades no segmento atual. Essa abordagem tem vários benefícios:  

* Ela permite que você limite a quantidade de dados a ser recuperada do serviço Tabela e mover pela rede.  
* Ela permite que você execute E/s assíncrona no .NET.  
* Ela permite que você serialize o token de acompanhamento para o armazenamento persistente para que você possa continuar caso um aplicativo falhe.  

> [!NOTE]
> Um token de continuação normalmente retorna um segmento que contém 1.000 entidades, embora possa ser menos. Este também será o caso se você limitar o número de entradas que uma consulta retorna, usando **Take** para retornar as primeiras n entidades que correspondem aos seus critérios de pesquisa: o serviço Tabela pode retornar um segmento contendo menos de n entidades, junto com um token de continuação para permitir que você recupere as entidades restantes.  
> 
> 

O código c# a seguir mostra como modificar o número de entidades retornadas dentro de um segmento:  

```csharp
employeeQuery.TakeCount = 50;  
```

#### <a name="server-side-projection"></a>Projeção do lado do servidor
Uma única entidade pode ter até 255 propriedades e até 1 MB de tamanho. Ao consultar a tabela e recuperar entidades, você talvez não precise de todas as propriedades e pode evitar a transferência desnecessária de dados (para ajudar a reduzir a latência e custo). Você pode usar a projeção do lado do servidor para transferir apenas as propriedades que precisa. O exemplo a seguir recupera apenas a propriedade **Email** (juntamente com **PartitionKey**, **RowKey**, **Timestamp**, e **ETag**) das entidades selecionadas pela consulta.  

```csharp
string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
List<string> columns = new List<string>() { "Email" };
TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter).Select(columns);

var entities = employeeTable.ExecuteQuery(employeeQuery);
foreach (var e in entities)
{
        Console.WriteLine("RowKey: {0}, EmployeeEmail: {1}", e.RowKey, e.Email);
}  
```

Observe como o valor **RowKey** fica disponível, mesmo que não tenha sido incluído na lista de propriedades para recuperação.  

### <a name="modifying-entities"></a>Modificando entidades
A Biblioteca de Cliente de Armazenamento permite que você modifique suas entidades armazenadas no serviço Tabela, inserindo, excluindo e atualizando entidades. Você pode usar EGTs para várias inserção, atualização e exclusão para reduzir o número de viagens de ida e volta necessárias e melhorar o desempenho da solução.  

Exceções geradas quando a Biblioteca de Cliente de Armazenamento executa uma EGT normalmente incluem o índice da entidade que causou a falha no lote. Isso é útil quando você está depurando código que usa EGTs.  

Você também deve considerar como seu design afeta a forma de tratamento, por parte do cliente, das operações de simultaneidade e atualização.  

#### <a name="managing-concurrency"></a>Gerenciando simultaneidade
Por padrão, o serviço Tabela implementa verificações de simultaneidade otimista no nível de entidades individuais para as operações **Inserir**, **Mesclar** e **Excluir**, embora um cliente possa forçar o serviço Tabela a ignorar essas verificações. Para obter mais informações sobre como o serviço Tabela gerencia a simultaneidade, confira [Gerenciando a simultaneidade no Armazenamento do Microsoft Azure](../storage/common/storage-concurrency.md).  

#### <a name="merge-or-replace"></a>Mesclar ou substituir
O método **Replace** da classe **TableOperation** sempre substitui a entidade completa no serviço Tabela. Se você não incluir uma propriedade na solicitação quando essa propriedade existe na entidade armazenada, a solicitação removerá a propriedade da entidade armazenada. A menos que você queira remover uma propriedade explicitamente de uma entidade armazenada, você deve incluir todas as propriedades na solicitação.  

Você pode usar o método **Merge** da classe **TableOperation** para reduzir a quantidade de dados enviados para o serviço Tabela quando quiser atualizar uma entidade. O método **Mesclar** substitui todas as propriedades na entidade armazenada por valores de propriedade da entidade incluída na solicitação, mas deixa intactas quaisquer eventuais propriedades na entidade armazenada que não estejam incluídas na solicitação. Isso é útil se você tiver grandes entidades e só precisar atualizar um pequeno número de propriedades em uma solicitação.  

> [!NOTE]
> Os métodos **Replace** e **Merge** falharão se a entidade não existir. Como alternativa, você pode usar os métodos **InsertOrReplace** e **InsertOrMerge**, que criam uma nova entidade se ela não existir.  
> 
> 

### <a name="working-with-heterogeneous-entity-types"></a>Trabalhando com tipos de entidade heterogênea
O serviço Tabela é um armazenamento de tabela *sem esquema* , o que significa que uma única tabela pode armazenar entidades de vários tipos, fornecendo grande flexibilidade no design. O exemplo a seguir ilustra uma tabela que armazena as entidades funcionário e departamento:  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Nome</th>
<th>Sobrenome</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Nome</th>
<th>Sobrenome</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>Nome</th>
<th>Sobrenome</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

Cada entidade deve ter ainda os valores de **PartitionKey**, **RowKey** e **Timestamp**, mas pode ter qualquer conjunto de propriedades. Além disso, não há nada para indicar o tipo de uma entidade, a menos que você opte por armazenar essa informação em algum lugar. Há duas opções para identificar o tipo de entidade:  

* Prefixe o tipo de entidade à **RowKey** (ou possivelmente à **PartitionKey**). Por exemplo, **EMPLOYEE_000123** ou **DEPARTMENT_SALES** como valores de **RowKey**.  
* Use uma propriedade separada para registrar o tipo de entidade, conforme mostrado na tabela a seguir.  

<table>
<tr>
<th>PartitionKey</th>
<th>RowKey</th>
<th>Timestamp</th>
<th></th>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Nome</th>
<th>Sobrenome</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Funcionário</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Nome</th>
<th>Sobrenome</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Funcionário</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>DepartmentName</th>
<th>EmployeeCount</th>
</tr>
<tr>
<td>department</td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
<tr>
<td></td>
<td></td>
<td></td>
<td>
<table>
<tr>
<th>EntityType</th>
<th>Nome</th>
<th>Sobrenome</th>
<th>Idade</th>
<th>Email</th>
</tr>
<tr>
<td>Funcionário</td>
<td></td>
<td></td>
<td></td>
<td></td>
</tr>
</table>
</td>
</tr>
</table>

A primeira opção, prefixar o tipo de entidade a **RowKey**, é útil quando há uma possibilidade de que duas entidades de tipos diferentes possam ter o mesmo valor de chave. Ela também agrupa entidades do mesmo tipo juntas na partição.  

As técnicas discutidas nesta seção são importantes principalmente para a discussão sobre [Relações de herança](#inheritance-relationships), anteriormente neste guia, na seção Relações de modelagem.  

> [!NOTE]
> Você deve considerar a inclusão do número de versão no valor do tipo de entidade para habilitar aplicativos clientes a desenvolverem objetos POCO e trabalhem com diferentes versões.  
> 
> 

O restante desta seção descreve alguns dos recursos na Biblioteca de Cliente de Armazenamento que facilitam o trabalho com vários tipos de entidade na mesma tabela.  

#### <a name="retrieving-heterogeneous-entity-types"></a>Recuperando tipos de entidade heterogênea
Se você estiver usando a Biblioteca de Cliente de Armazenamento, tem três opções para trabalhar com vários tipos de entidade.  

Se souber o tipo de entidade armazenado com determinados valores de **RowKey** e **PartitionKey**, você poderá especificar o tipo de entidade ao recuperar a entidade, conforme mostrado nos dois exemplos anteriores que recuperam entidades do tipo **EmployeeEntity**: [Executar uma consulta de ponto usando a Biblioteca de Clientes de Armazenamento](#executing-a-point-query-using-the-storage-client-library) e [Recuperar várias entidades usando LINQ](#retrieving-multiple-entities-using-linq).  

A segunda opção é usar o tipo **DynamicTableEntity** (um recipiente de propriedades), em vez de um tipo concreto de entidade POCO (essa opção também pode melhorar o desempenho, porque não é necessário serializar e desserializar a entidade para tipos .NET). O código C# a seguir recupera potencialmente várias entidades de diferentes tipos de tabela, mas retorna todas as entidades como instâncias **DynamicTableEntity** . Ele usa a propriedade **EntityType** para determinar o tipo de cada entidade:  

```csharp
string filter = TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("PartitionKey",
    QueryComparisons.Equal, "Sales"),
    TableOperators.And,
    TableQuery.CombineFilters(
    TableQuery.GenerateFilterCondition("RowKey",
                    QueryComparisons.GreaterThanOrEqual, "B"),
        TableOperators.And,
        TableQuery.GenerateFilterCondition("RowKey",
        QueryComparisons.LessThan, "F")
    )
);
TableQuery<DynamicTableEntity> entityQuery =
    new TableQuery<DynamicTableEntity>().Where(filter);
var employees = employeeTable.ExecuteQuery(entityQuery);

IEnumerable<DynamicTableEntity> entities = employeeTable.ExecuteQuery(entityQuery);
foreach (var e in entities)
{
EntityProperty entityTypeProperty;
if (e.Properties.TryGetValue("EntityType", out entityTypeProperty))
{
    if (entityTypeProperty.StringValue == "Employee")
    {
        // Use entityTypeProperty, RowKey, PartitionKey, Etag, and Timestamp
        }
    }
}  
```

Para recuperar outras propriedades, você deve usar o método **TryGetValue** na propriedade **Properties** da classe **DynamicTableEntity**.  

Uma terceira opção é combinar o uso do tipo **DynamicTableEntity** e uma instância **EntityResolver**. Isso permite que você resolver para vários tipos POCO na mesma consulta. Neste exemplo, o **EntityResolver** delegado usa a propriedade **EntityType** para distinguir entre os dois tipos de entidade retornados pela consulta. O método **Resolve** usa o **resolvedor** delegado para resolver instâncias **DynamicTableEntity** para instâncias **TableEntity**.  

```csharp
EntityResolver<TableEntity> resolver = (pk, rk, ts, props, etag) =>
{

        TableEntity resolvedEntity = null;
        if (props["EntityType"].StringValue == "Department")
        {
        resolvedEntity = new DepartmentEntity();
        }
        else if (props["EntityType"].StringValue == "Employee")
        {
        resolvedEntity = new EmployeeEntity();
        }
        else throw new ArgumentException("Unrecognized entity", "props");

        resolvedEntity.PartitionKey = pk;
        resolvedEntity.RowKey = rk;
        resolvedEntity.Timestamp = ts;
        resolvedEntity.ETag = etag;
        resolvedEntity.ReadEntity(props, null);
        return resolvedEntity;
};

string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, "Sales");
TableQuery<DynamicTableEntity> entityQuery =
        new TableQuery<DynamicTableEntity>().Where(filter);

var entities = employeeTable.ExecuteQuery(entityQuery, resolver);
foreach (var e in entities)
{
        if (e is DepartmentEntity)
        {
    ...
        }
        if (e is EmployeeEntity)
        {
    ...
        }
}  
```

#### <a name="modifying-heterogeneous-entity-types"></a>Modificando tipos de entidade heterogênea
Você não precisa saber o tipo de uma entidade para excluí-la, e você sempre sabe o tipo de uma entidade quando a insere. No entanto, você pode usar o tipo **DynamicTableEntity** para atualizar uma entidade sem saber seu tipo e sem usar uma classe de entidade POCO. O exemplo de código a seguir recupera uma única entidade e verifica se a propriedade **EmployeeCount** existe antes de atualizá-la.  

```csharp
TableResult result =
        employeeTable.Execute(TableOperation.Retrieve(partitionKey, rowKey));
DynamicTableEntity department = (DynamicTableEntity)result.Result;

EntityProperty countProperty;

if (!department.Properties.TryGetValue("EmployeeCount", out countProperty))
{
        throw new
        InvalidOperationException("Invalid entity, EmployeeCount property not found.");
}
countProperty.Int32Value += 1;
employeeTable.Execute(TableOperation.Merge(department));  
```

### <a name="controlling-access-with-shared-access-signatures"></a>Controlando o acesso com assinaturas de acesso compartilhado
Você pode usar tokens de SAS (Assinatura de Acesso Compartilhado) para habilitar aplicativos cliente a modificarem (e consultarem) diretamente, sem a necessidade de autenticação direta no serviço Tabela. Normalmente, há três benefícios principais para usar SAS em seu aplicativo:  

* Você não precisa distribuir sua chave de conta de armazenamento para uma plataforma insegura (como um dispositivo móvel) para permitir que esse dispositivo acesse e modifique entidades no serviço Tabela.  
* Você pode descarregar parte do trabalho que as funções Web e de trabalho desempenham no gerenciamento de suas entidades em dispositivos clientes, como computadores de usuários finais e dispositivos móveis.  
* Você pode atribuir um conjunto de permissões restrito e de tempo limitado a um cliente (como acesso somente leitura a recursos específicos).  

Para obter mais informações sobre como usar tokens SAS com o serviço Tabela, consulte [Uso de SAS (Assinaturas de Acesso Compartilhado)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).  

No entanto, você ainda deve gerar os tokens SAS que concedem a um aplicativo cliente para as entidades no serviço Tabela: faça isso em um ambiente com acesso seguro às chaves de conta de armazenamento. Geralmente, você usa uma função de trabalho ou Web para gerar tokens SAS e enviá-los aos aplicativos clientes que precisam acessar suas entidades. Como ainda há uma sobrecarga envolvida na geração e fornecimento de tokens SAS aos clientes, você deve considerar a melhor maneira de reduzir essa sobrecarga, especialmente em cenários de alto volume.  

É possível gerar um token SAS que conceda acesso a um subconjunto de entidades em uma tabela. Por padrão, você cria um token SAS para uma tabela inteira, mas também é possível especificar que o token SAS conceda acesso a um intervalo de valores de **PartitionKey** ou a um intervalo de valores de **PartitionKey** e **RowKey**. Você pode optar por gerar tokens SAS para usuários individuais do sistema, de modo que o token SAS de cada usuário só permita acesso às suas próprias entidades no serviço Tabela.  

### <a name="asynchronous-and-parallel-operations"></a>Operações paralelas e assíncronas
Desde que você esteja distribuindo suas solicitações por várias partições, pode melhorar a capacidade de resposta do cliente e a taxa de transferência usando consultas assíncronas ou paralelas.
Por exemplo, você pode ter duas ou mais instâncias de função de trabalho acessando suas tabelas em paralelo. Você pode ter funções de trabalho individuais responsáveis por determinados conjuntos de partições ou simplesmente ter várias instâncias de função de trabalho, cada uma capaz de acessar todas as partições em uma tabela.  

Dentro de uma instância do cliente, você pode melhorar o desempenho executando operações de armazenamento de forma assíncrona. A Biblioteca de Cliente de Armazenamento facilita a gravação de consultas e modificações assíncronas. Por exemplo, você pode começar com o método síncrono que recupera todas as entidades em uma partição, como mostra o código c# a seguir:  

```csharp
private static void ManyEntitiesQuery(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);

        TableContinuationToken continuationToken = null;

        do
        {
        var employees = employeeTable.ExecuteQuerySegmented(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
    {
        ...
    }
        continuationToken = employees.ContinuationToken;
        } while (continuationToken != null);
}  
```

Você pode facilmente modificar esse código para que a consulta seja executada de forma assíncrona, da seguinte maneira:  

```csharp
private static async Task ManyEntitiesQueryAsync(CloudTable employeeTable, string department)
{
        string filter = TableQuery.GenerateFilterCondition(
        "PartitionKey", QueryComparisons.Equal, department);
        TableQuery<EmployeeEntity> employeeQuery =
        new TableQuery<EmployeeEntity>().Where(filter);
        TableContinuationToken continuationToken = null;

        do
        {
        var employees = await employeeTable.ExecuteQuerySegmentedAsync(
                employeeQuery, continuationToken);
        foreach (var emp in employees)
        {
            ...
        }
        continuationToken = employees.ContinuationToken;
            } while (continuationToken != null);
}  
```

Neste exemplo assíncrono, você pode ver as seguintes alterações da versão síncrona:  

* A assinatura do método agora inclui o modificador **async** e retorna uma instância **Task**.  
* Em vez de chamar o método **ExecuteSegmented** para recuperar os resultados, agora o método chama o método **ExecuteSegmentedAsync** e usa o modificador **await** para recuperar resultados de forma assíncrona.  

O aplicativo cliente pode chamar esse método várias vezes (com valores diferentes para o parâmetro **department** ) e cada consulta será executada em um thread separado.  

Não há qualquer versão assíncrona do método **Execute** na classe **TableQuery**, pois a interface **IEnumerable** não dá suporte à enumeração assíncrona.  

Você também pode inserir, atualizar e excluir entidades de forma assíncrona. O exemplo c# a seguir mostra um método síncrono simples para inserir ou substituir uma entidade funcionário:  

```csharp
private static void SimpleEmployeeUpsert(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = employeeTable
        .Execute(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Você pode modificar este código facilmente para que a atualização seja executada de forma assíncrona da seguinte maneira:  

```csharp
private static async Task SimpleEmployeeUpsertAsync(CloudTable employeeTable,
        EmployeeEntity employee)
{
        TableResult result = await employeeTable
        .ExecuteAsync(TableOperation.InsertOrReplace(employee));
        Console.WriteLine("HTTP Status: {0}", result.HttpStatusCode);
}  
```

Neste exemplo assíncrono, você pode ver as seguintes alterações da versão síncrona:  

* A assinatura do método agora inclui o modificador **async** e retorna uma instância **Task**.  
* Em vez de chamar o método **Execute** para atualizar a entidade, agora o método chama o método **ExecuteAsync** e usa o modificador **await** para recuperar resultados de modo assíncrono.  

O aplicativo cliente pode chamar vários métodos assíncronos como esse, e cada invocação de método será executado em um thread separado.  

### <a name="credits"></a>Credits
Gostaríamos de agradecer os seguintes membros da equipe do Azure por suas contribuições: Dominic Betts, Jason Hogg, Jean Ghanem, Jai Haridas, Jeff Irwin, Vamshidhar Kommineni, Vinay Shah e Serdar Ozler, bem como Tom Hollander da Microsoft DX. 

Também gostaríamos de agradecer aos seguintes MVPs da Microsoft por seus valiosos comentários durante os ciclos de revisão: Igor Papirov e Edward Bakker.

[1]: ./media/storage-table-design-guide/storage-table-design-IMAGE01.png
[2]: ./media/storage-table-design-guide/storage-table-design-IMAGE02.png
[3]: ./media/storage-table-design-guide/storage-table-design-IMAGE03.png
[4]: ./media/storage-table-design-guide/storage-table-design-IMAGE04.png
[5]: ./media/storage-table-design-guide/storage-table-design-IMAGE05.png
[6]: ./media/storage-table-design-guide/storage-table-design-IMAGE06.png
[7]: ./media/storage-table-design-guide/storage-table-design-IMAGE07.png
[8]: ./media/storage-table-design-guide/storage-table-design-IMAGE08.png
[9]: ./media/storage-table-design-guide/storage-table-design-IMAGE09.png
[10]: ./media/storage-table-design-guide/storage-table-design-IMAGE10.png
[11]: ./media/storage-table-design-guide/storage-table-design-IMAGE11.png
[12]: ./media/storage-table-design-guide/storage-table-design-IMAGE12.png
[13]: ./media/storage-table-design-guide/storage-table-design-IMAGE13.png
[14]: ./media/storage-table-design-guide/storage-table-design-IMAGE14.png
[15]: ./media/storage-table-design-guide/storage-table-design-IMAGE15.png
[16]: ./media/storage-table-design-guide/storage-table-design-IMAGE16.png
[17]: ./media/storage-table-design-guide/storage-table-design-IMAGE17.png
[18]: ./media/storage-table-design-guide/storage-table-design-IMAGE18.png
[19]: ./media/storage-table-design-guide/storage-table-design-IMAGE19.png
[20]: ./media/storage-table-design-guide/storage-table-design-IMAGE20.png
[21]: ./media/storage-table-design-guide/storage-table-design-IMAGE21.png
[22]: ./media/storage-table-design-guide/storage-table-design-IMAGE22.png
[23]: ./media/storage-table-design-guide/storage-table-design-IMAGE23.png
[24]: ./media/storage-table-design-guide/storage-table-design-IMAGE24.png
[25]: ./media/storage-table-design-guide/storage-table-design-IMAGE25.png
[26]: ./media/storage-table-design-guide/storage-table-design-IMAGE26.png
[27]: ./media/storage-table-design-guide/storage-table-design-IMAGE27.png
[28]: ./media/storage-table-design-guide/storage-table-design-IMAGE28.png
[29]: ./media/storage-table-design-guide/storage-table-design-IMAGE29.png

