---
title: Relações de modelagem no design de armazenamento de tabelas do Azure | Microsoft Docs
description: Entenda o processo de modelagem ao projetar sua solução de armazenamento de tabelas do Azure. Leia sobre relações um-para-muitos, um para um e herança.
services: storage
ms.service: storage
author: tamram
ms.author: tamram
ms.topic: article
ms.date: 04/23/2018
ms.subservice: tables
ms.openlocfilehash: 3023b478ef7a4aaf6d9239e997bdf63282b56210
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88271185"
---
# <a name="modeling-relationships"></a>Relações de modelagem
Este artigo descreve o processo de modelagem para ajudar você a projetar suas soluções de armazenamento em Tabela do Azure.

A criação de modelos de domínio é uma etapa importante na criação de sistemas complexos. Normalmente, você usa o processo de modelagem para identificar entidades e as relações entre elas, como uma forma de compreender o domínio de negócios e informar o design do seu sistema. Esta seção se concentra em como você pode converter alguns dos tipos de relações comuns encontrados em modelos de domínio em designs para o serviço Tabela. O processo de mapeamento de um modelo de dados lógico para um modelo de dados baseado em NoSQL físico é diferente do usado durante a criação de um banco de dados relacional. Design de bancos de dados relacionais geralmente assume um processo de normalização de dados otimizado para minimizar a redundância – e uma funcionalidade de consulta declarativa que abstrai como a implementação do banco de dados funciona.  

## <a name="one-to-many-relationships"></a>Relações um-para-muitos
Relações um-para-muitos entre objetos de domínio de negócios ocorrerem com frequência: por exemplo, um departamento tem muitos funcionários. Há várias maneiras de implementar relações um-para-muitos no serviço Tabela, cada com prós e contras que podem ser relevantes ao cenário específico.  

Considere o exemplo de uma grande empresa multinacional com dezenas de milhares de departamentos e entidades de funcionário em que cada departamento tem muitos funcionários e cada funcionário associado a um departamento específico. Uma abordagem é armazenar departamento separado e entidades de funcionário como estes:  


![Armazenar entidades separadas de departamento e funcionário](media/storage-table-design-guide/storage-table-design-IMAGE01.png)

Este exemplo mostra uma relação implícita de um-para-muitos entre os tipos com base no valor **PartitionKey** . Cada departamento pode ter muitos funcionários.  

Este exemplo também mostra uma entidade de departamento e suas entidades de funcionário relacionadas na mesma partição. Você pode optar por usar diferentes partições, tabelas ou até mesmo contas de armazenamento para os diferentes tipos de entidade.  

Uma abordagem alternativa é desnormalizar seus dados e armazenar apenas entidades de funcionário com dados desnormalizados de departamento, conforme mostrado no exemplo a seguir. Neste cenário específico, essa abordagem desnormalizada pode não ser a melhor se você precisar ser capaz de alterar os detalhes de um gerente de departamento, pois para isso você precisa atualizar todos os funcionários do departamento.  

![Entidade de funcionário](media/storage-table-design-guide/storage-table-design-IMAGE02.png)

Para saber mais, confira [Padrão de desnormalização](table-storage-design-patterns.md#denormalization-pattern) mais adiante neste guia.  

A tabela a seguir resume os prós e contras de cada uma das abordagens descritas acima para o armazenamento de entidades de funcionário e departamento que têm uma relação um-para-muitos. Você também deve considerar a frequência com que pretende executar várias operações: pode ser aceitável ter um projeto que inclui uma operação cara, se essa operação ocorrer apenas raramente.  

<table>
<tr>
<th>Abordagem</th>
<th>Vantagens</th>
<th>Desvantagens</th>
</tr>
<tr>
<td>Separar tipos de entidade, mesma partição, mesma tabela</td>
<td>
<ul>
<li>Você pode atualizar uma entidade de departamento com uma única operação.</li>
<li>Você pode usar uma transação de grupo de entidades * (EGT) para manter a consistência se tiver um requisito para modificar uma entidade de departamento sempre que atualizar/inserir/excluir uma entidade de funcionário. Por exemplo, se você mantiver uma contagem de funcionários do departamento para cada departamento.</li>
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

* para obter mais informações, consulte [Transações de grupo de entidades](table-storage-design.md#entity-group-transactions)  


Como escolher entre essas opções e quais os prós e contras são mais significativos, depende de seus cenários específicos do aplicativo. Por exemplo, com que frequência você modifica entidades de departamento; todas as consultas de funcionário precisam de informações departamentais adicionais; a que distância você está dos limites de escalabilidade em suas partições ou em sua conta de armazenamento?  

## <a name="one-to-one-relationships"></a>Relações um-para-um
Modelos de domínio podem incluir relações um-para-um entre entidades. Se você precisar implementar uma relação individual no serviço Tabela, também deve escolher como vincular as duas entidades relacionadas, quando precisar recuperá-las. Esse link pode ser implícito, com base em uma convenção nos valores de chave, ou então explícito, armazenando um link na forma dos valores de **PartitionKey** e **RowKey** em cada entidade até sua entidade relacionada. Para ver uma discussão sobre a conveniência ou não de armazenar as entidades relacionadas na mesma partição, confira a seção [Relações de um-para-muitos](#one-to-many-relationships).  

Também há considerações de implementação que podem levá-lo a implementar relações um-para-um no serviço Tabela:  

* Tratando grandes entidades (para obter mais informações, consulte [padrão de entidades grandes](table-storage-design-patterns.md#large-entities-pattern)).  
* A implementação de controles de acesso (para saber mais, consulte Controlando o acesso com assinaturas de acesso compartilhado).  

## <a name="join-in-the-client"></a>Unindo o cliente
Embora haja maneiras de modelar relações no serviço Tabela, você não deve se esquecer de que os dois motivos principais para usar o serviço Tabela são a escalabilidade e o desempenho. Se você achar que está modelando muitas relações que comprometem o desempenho e a escalabilidade de sua solução, deverá se perguntar se é necessário criar todas as relações de dados no design de tabela. Você poderá simplificar o design e melhorar a escalabilidade e o desempenho de sua solução se permitir que o aplicativo cliente execute as junções necessárias.  

Por exemplo, se você tiver tabelas pequenas que contêm dados que não são alterados com frequência, você pode recuperar esses dados uma vez e armazená-los no cliente. Isso pode evitar idas e voltas repetidas para recuperar os mesmos dados. Nos exemplos que examinamos neste guia, o conjunto dos departamentos em uma pequena organização é provavelmente pequeno e alterado raramente, tornando-o um bom candidato para dados que o aplicativo cliente pode baixar uma vez e armazenar como dados de pesquisa.  

## <a name="inheritance-relationships"></a>Relações de herança
Se seu aplicativo cliente usa um conjunto de classes que fazem parte de uma relação de herança para representar entidades de negócios, você poderá persistir facilmente essas entidades no serviço Tabela. Por exemplo, você pode ter o seguinte conjunto de classes definidas em seu aplicativo cliente, em que **Pessoa** é uma classe abstrata.

![Classe de Pessoa Abstrata](media/storage-table-design-guide/storage-table-design-IMAGE03.png)

Você pode persistir instâncias das duas classes concretas no serviço Tabela usando uma única tabela Pessoa com entidades que têm a seguinte aparência:  

![Tabela de Pessoa](media/storage-table-design-guide/storage-table-design-IMAGE04.png)

Para saber mais sobre como trabalhar com vários tipos de entidade na mesma tabela no código cliente, confira a seção Trabalhando com tipos de entidade heterogênea mais adiante neste guia. Isso fornece exemplos de como reconhecer o tipo de entidade no código do cliente.  


## <a name="next-steps"></a>Próximas etapas

- [Padrões de design de tabela](table-storage-design-patterns.md)
- [Design para consulta](table-storage-design-for-query.md)
- [Criptografar dados de tabela](table-storage-design-encrypt-data.md)
- [Design para modificação de dados](table-storage-design-for-modification.md)
