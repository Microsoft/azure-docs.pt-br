---
title: Projete tabelas escalonáveis e de alto desempenho no Armazenamento de Tabelas do Azure. | Microsoft Docs
description: Projete tabelas escalonáveis e de alto desempenho no Armazenamento de Tabelas do Azure.
services: storage
author: SnehaGunda
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.subservice: tables
ms.openlocfilehash: 8387e41d57edfa0e54ac930c9462714aca571f2a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848275"
---
# <a name="design-scalable-and-performant-tables"></a>Projetar tabelas escalonáveis e de alto desempenho

[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-tip-include.md)]

Para projetar tabelas escalonáveis e de alto desempenho, você deve considerar fatores como desempenho, escalabilidade e custo. Se você tiver criado anteriormente esquemas de bancos de dados relacionais, essas considerações serão familiares, mas embora haja algumas semelhanças entre o modelo de armazenamento do serviço Tabela do Azure e os modelos relacionais, também há diferenças importantes. Normalmente, essas diferenças resultam em designs diferentes que podem parecer contraintuitivos ou errados para alguém que esteja familiarizado com bancos de dados relacionais, mas que fazem sentido se você estiver criando para um armazenamento de chave/valor de NoSQL, como o serviço Tabela do Azure. Muitas das suas diferenças de design refletem o fato de que o serviço Tabela foi projetado para dar suporte a aplicativos de escala de nuvem que podem conter bilhões de entidades (ou linhas na terminologia de banco de dados relacional) de dados ou de conjuntos de dados que devem dar suporte a grandes volumes de transações. Portanto, você deve pensar de modo diferente sobre como armazenar os dados e entender como funciona o serviço tabela. Um repositório de dados NoSQL bem projetado pode permitir que sua solução seja muito mais dimensionável e a um custo menor do que uma solução que usa um banco de dados relacional. Este guia ajuda você com esses tópicos.  

## <a name="about-the-azure-table-service"></a>Sobre o serviço Tabela do Azure
Esta seção destaca alguns dos principais recursos do serviço Tabela que são especialmente relevantes para o projeto de desempenho e escalabilidade. Se você não tiver experiência no Armazenamento do Azure e no serviço Tabela, leia primeiro [Introdução ao Armazenamento do Microsoft Azure](../../storage/common/storage-introduction.md) e [Introdução ao Armazenamento de Tabelas do Azure usando o .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) antes de ler o restante deste artigo. Embora o foco deste guia seja no serviço Tabela, ele inclui discussão dos serviços de Blob e fila do Azure e como você pode usá-los com o serviço Tabela.  

O que é o serviço Tabela? Como você pode esperar do nome, o serviço Tabela usa um formato tabular para armazenar dados. Na terminologia padrão, cada linha da tabela representa uma entidade e as colunas armazenam várias propriedades da entidade. Cada entidade tem um par de chaves para identificar exclusivamente e uma coluna de carimbo de data/hora que o serviço Tabela usa para controlar quando a entidade foi atualizada pela última vez. O carimbo de data/hora é aplicado automaticamente e não é possível substituir manualmente o carimbo de data/hora com um valor arbitrário. O serviço Tabela usa este carimbo de data/hora (LMT) da última modificação para gerenciar a simultaneidade otimista.  

> [!NOTE]
> As operações de API REST do serviço Tabela também retornam um valor **ETag** que deriva do LMT. Este documento usa os termos ETag e LMT indistintamente porque eles se referem aos mesmos dados subjacentes.  
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


Até agora, esses dados parecem semelhantes a uma tabela no banco de dados relacional com as diferenças principais sendo as colunas obrigatórias e a capacidade de armazenar vários tipos de entidade na mesma tabela. Além disso, cada uma das propriedades definidas pelo usuário, como **FirstName** ou **Age**, tem um tipo de dados, como número inteiro ou cadeia de caracteres, semelhante a uma coluna em um banco de dados relacional. Embora diferente em um banco de dados relacional, a natureza sem esquema do serviço Tabela significa que uma propriedade não precisa ter o mesmo tipo de dados em cada entidade. Para armazenar tipos de dados complexos em uma única propriedade, você deve usar um formato serializado como JSON ou XML. Para obter mais informações sobre o serviço Tabela, como tipos de dados com suporte, intervalos de datas com suporte, regras de nomenclatura e restrições de tamanho, consulte [Noções básicas sobre o modelo de dados do serviço Tabela](https://msdn.microsoft.com/library/azure/dd179338.aspx).

Sua escolha de **PartitionKey** e **RowKey** é fundamental para um bom design da tabela. Cada entidade armazenada em uma tabela deve ter uma combinação exclusiva de **PartitionKey** e **RowKey**. Assim como acontece com as chaves em uma tabela de banco de dados relacional, os valores **PartitionKey** e **RowKey** são indexados para criar um índice clusterizado para habilitar pesquisas rápidas. No entanto, o serviço Tabela não cria qualquer índice secundário, portanto **PartitionKey** e **RowKey** são as únicas propriedades indexadas. Alguns dos padrões descritos em [Padrões de design de tabela](table-storage-design-patterns.md) ilustram como você pode contornar essa limitação aparente.  

Uma tabela contém uma ou mais partições, e muitas das decisões de design tomadas serão em torno da escolha de uma **PartitionKey** e uma **RowKey** adequadas para otimizar sua solução. Uma solução pode consistir em uma única tabela que contém todas as suas entidades organizadas em partições, mas normalmente uma solução tem várias tabelas. Tabelas o ajudarão a organizar suas entidades logicamente, gerenciar o acesso aos dados usando listas de controle de acesso, e você pode remover uma tabela inteira usando uma única operação de armazenamento.  

## <a name="table-partitions"></a>Partições de tabela
O nome da conta, o nome de tabela e **PartitionKey** juntas identificam a partição dentro do serviço de armazenamento no qual o serviço Tabela armazena a entidade. Além de ser parte do esquema de endereçamento de entidades, as partições definem um escopo para transações (consulte [Transações de Grupo de Entidades](#entity-group-transactions) abaixo) e forme a base de como o serviço Tabela pode ser dimensionado. Para obter mais informações sobre partições, consulte [Metas de desempenho e escalabilidade de armazenamento do Azure](../../storage/common/storage-scalability-targets.md).  

No serviço Tabela, um nó individual atende a uma ou mais partições completas e o serviço é dimensionado pelo balanceamento dinâmico de carga das partições nos nós. Se um nó estiver sob carga, o serviço Tabela pode *dividir* o intervalo de partições atendidas por esse nó em nós diferentes. Quando o tráfego baixa, o serviço pode *mesclar* os intervalos de partições de nós silenciosos de volta para um único nó.  

Para obter mais informações sobre os detalhes internos do serviço Tabela e, em particular, sobre como o serviço gerencia partições, confira o artigo [Armazenamento do Microsoft Azure: um serviço de armazenamento em nuvem altamente disponível com coerência forte](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx).  

## <a name="entity-group-transactions"></a>Transações de Grupo de Entidades
No serviço Tabela, EGTs (Transações de Grupo de Entidades) são o único mecanismo interno para realizar atualizações atômicas entre várias entidades. EGTs são, às vezes, também conhecidas como *transações de lote*. EGTs só podem operar em entidades armazenadas na mesma partição (ou seja, compartilham a mesma chave de partição em uma determinada tabela). Portanto, sempre que você precisar de comportamento transacional atômico entre várias entidades, certifique-se de que as entidades estejam na mesma partição. Isso geralmente é um motivo para manter vários tipos de entidade na mesma tabela (e partição) e não usar várias tabelas para tipos de entidade diferentes. Uma única EGT pode operar no máximo 100 entidades.  Se você enviar várias EGTs simultâneas para processamento, é importante garantir que essas EGTs não operem em entidades comuns entre as EGTs; caso contrário, o processamento pode ser retardado.

EGTs também apresentam uma desvantagem potencial para avaliar em seu design. Ou seja, usar mais partições aumenta a escalabilidade do seu aplicativo, porque o Azure tem mais oportunidades para solicitações de balanceamento de carga entre nós. Mas o uso de mais partições pode limitar a capacidade de seu aplicativo para executar transações atômicas e manter a consistência sólida para seus dados. Além disso, existem destinos de escalabilidade específica no nível de uma partição que pode limitar a taxa de transferência de transações que você pode esperar para um único nó. Para obter mais informações sobre os destinos de escalabilidade para contas de armazenamento do Azure e o serviço de tabela, consulte [Escalabilidade do armazenamento do Azure e metas de desempenho](../../storage/common/storage-scalability-targets.md).   

## <a name="capacity-considerations"></a>Considerações sobre a capacidade
A tabela a seguir descreve alguns dos valores de chave a serem consideradas quando você estiver criando uma solução de serviço Tabela:  

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

## <a name="cost-considerations"></a>Considerações de custo
Armazenamento de tabela é relativamente barato, mas você deve incluir estimativas de custo para a utilização da capacidade e a quantidade de transações como parte de sua avaliação de qualquer solução do serviço Tabela. No entanto, em muitos cenários, o armazenamento de dados duplicados ou desnormalizados para melhorar o desempenho ou a escalabilidade de sua solução é uma abordagem válida. Para obter mais informações sobre preços, consulte [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/).  

## <a name="next-steps"></a>Próximas etapas

- [Padrões de design de tabela](table-storage-design-patterns.md)
- [Relações de modelagem](table-storage-design-modeling.md)
- [Design para consulta](table-storage-design-for-query.md)
- [Criptografando dados de tabela](table-storage-design-encrypt-data.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)
