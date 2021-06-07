---
title: Migrar tabelas do banco de dados SQL do Azure para o Azure CosmosDB com Azure Data Factory
description: Pegue um esquema de banco de dados normalizado existente do banco de dados SQL do Azure e migre para um contêiner desnormalizado do Azure CosmosDB com Azure Data Factory.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 3d67ac9474704fac39dbe7eb91aead5c4babc4ce
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383935"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>Migrar um esquema de banco de dados normalizado do Banco de Dados SQL do Azure para um contêiner desnormalizado do Azure CosmosDB

Este guia explicará como pegar um esquema de banco de dados normalizado existente no banco de dados SQL do Azure e convertê-lo em um esquema desnormalizado do Azure CosmosDB para carregar no Azure CosmosDB.

Os esquemas SQL normalmente são modelados usando o terceiro formato normal, resultando em esquemas normalizados que fornecem altos níveis de integridade de dados e menos valores de dados duplicados. As consultas podem unir entidades em tabelas para leitura. O CosmosDB é otimizado para transações superrápidas e consultas em uma coleção ou contêiner por meio de esquemas desnormalizados com dados independentes em um documento.

Usando o Azure Data Factory, criaremos um pipeline que usa um fluxo de dados de mapeamento único para ler de duas tabelas normalizadas do Azure SQL Database que contêm chaves primárias e estrangeiras como a relação de entidade. O ADF unirá essas tabelas em um único fluxo usando o mecanismo Spark do fluxo de dados, coletará linhas Unidas em matrizes e produzirá documentos limpos individuais para inserção em um novo contêiner CosmosDB do Azure.

Este guia criará um novo contêiner em tempo real, chamado "Orders", que usará as ```SalesOrderHeader``` ```SalesOrderDetail``` tabelas e do banco de dados de exemplo SQL Server AdventureWorks padrão. Essas tabelas representam as transações de vendas Unidas pelo ```SalesOrderID``` . Cada registro de detalhes exclusivo tem sua própria chave primária de ```SalesOrderDetailID``` . A relação entre o cabeçalho e o detalhe é ```1:M``` . Entraremos no ```SalesOrderID``` ADF e, em seguida, acumularemos cada registro detalhado relacionado em uma matriz chamada "Detail".

A consulta SQL representativa para este guia é:

```
  SELECT
  o.SalesOrderID,
  o.OrderDate,
  o.Status,
  o.ShipDate,
  o.SalesOrderNumber,
  o.ShipMethod,
  o.SubTotal,
  (select SalesOrderDetailID, UnitPrice, OrderQty from SalesLT.SalesOrderDetail od where od.SalesOrderID = o.SalesOrderID for json auto) as OrderDetails
FROM SalesLT.SalesOrderHeader o;
```

O contêiner CosmosDB resultante incorporará a consulta interna em um único documento e terá a seguinte aparência:

![Coleção](media/data-flow/cosmosb3.png)

## <a name="create-a-pipeline"></a>Criar um pipeline

1. Selecione **+ novo pipeline** para criar um novo pipeline.

2. Adicionar uma atividade de fluxo de dados

3. Na atividade fluxo de dados, selecione **novo mapeamento fluxo de dados**.

4. Criaremos este gráfico de fluxo de dados abaixo

![Grafo de fluxo de dados](media/data-flow/cosmosb1.png)

5. Defina a origem para "SourceOrderDetails". Para DataSet, crie um novo conjunto de dados do Azure SQL Database que aponte para a ```SalesOrderDetail``` tabela.

6. Defina a origem para "SourceOrderHeader". Para DataSet, crie um novo conjunto de dados do Azure SQL Database que aponte para a ```SalesOrderHeader``` tabela.

7. Na origem superior, adicione uma transformação coluna derivada após "SourceOrderDetails". Chame a nova transformação "conversão". Precisamos arredondar a ```UnitPrice``` coluna e convertê-la em um tipo de dados duplo para CosmosDB. Defina a fórmula como: ```toDouble(round(UnitPrice,2))``` .

8. Adicione outra coluna derivada e chame-a de "MakeStruct". É aí que criaremos uma estrutura hierárquica para manter os valores da tabela detalhes. Lembre-se de que detalhes é uma ```M:1``` relação com o cabeçalho. Nomeie a nova estrutura ```orderdetailsstruct``` e crie a hierarquia dessa forma, configurando cada subcoluna para o nome da coluna de entrada:

![Criar Estrutura](media/data-flow/cosmosb9.png)

9. Agora, vamos para a fonte de cabeçalho Sales. Adicionar uma transformação de junção. Para o lado direito, selecione "MakeStruct". Deixe-o definido como junção interna e escolha ```SalesOrderID``` para ambos os lados da condição de junção.

10. Clique na guia Visualização de dados na nova junção que você adicionou para que você possa ver seus resultados até este ponto. Você deve ver todas as linhas de cabeçalho Unidas com as linhas de detalhes. Esse é o resultado da junção que está sendo formada a partir do ```SalesOrderID``` . Em seguida, combinaremos os detalhes das linhas comuns na estrutura de detalhes e agregaremos as linhas comuns.

![Ingressar](media/data-flow/cosmosb4.png)

11. Antes que possamos criar as matrizes para desnormalizar essas linhas, primeiro precisamos remover colunas indesejadas e verificar se os valores de dados corresponderão aos tipos de dados CosmosDB.

12. Adicione uma transformação selecionar próximo e defina o mapeamento de campo como este:

![Depurador de coluna](media/data-flow/cosmosb5.png)

13. Agora, vamos converter novamente uma coluna de moeda, desta vez ```TotalDue``` . Como fizemos acima na etapa 7, defina a fórmula como: ```toDouble(round(TotalDue,2))``` .

14. Aqui está onde desnormalizaremos as linhas agrupando pela chave comum ```SalesOrderID``` . Adicione uma transformação agregação e defina Group by como ```SalesOrderID``` .

15. Na fórmula de agregação, adicione uma nova coluna chamada "Details" e use esta fórmula para coletar os valores na estrutura que criamos anteriormente chamada ```orderdetailsstruct``` : ```collect(orderdetailsstruct)``` .

16. A transformação Agregação só produzirá colunas que fazem parte das fórmulas agregadas ou agrupar por. Portanto, também precisamos incluir as colunas do cabeçalho Sales. Para fazer isso, adicione um padrão de coluna na mesma transformação Agregação. Esse padrão incluirá todas as outras colunas na saída:

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. Use a sintaxe "This" nas outras propriedades para que possamos manter os mesmos nomes de coluna e usar a ```first()``` função como uma agregação:

![Agregado](media/data-flow/cosmosb6.png)

18. Estamos prontos para concluir o fluxo de migração adicionando uma transformação de coletor. Clique em "novo" ao lado de DataSet e adicione um conjunto de dados CosmosDB que aponte para o banco de dados CosmosDB. Para a coleção, vamos chamá-la de "Orders" e ela não terá nenhum esquema e nenhum documento, pois ela será criada imediatamente.

19. Em configurações do coletor, chave de partição para ```\SalesOrderID``` e ação de coleção para "recriar". Verifique se a guia mapeamento tem esta aparência:

![Captura de tela mostra a guia mapeamento.](media/data-flow/cosmosb7.png)

20. Clique em visualização de dados para certificar-se de que você está vendo essas 32 linhas definidas como inserir como novos documentos no novo contêiner:

![Captura de tela mostra a guia Visualização de dados.](media/data-flow/cosmosb8.png)

Se tudo estiver correto, agora você estará pronto para criar um novo pipeline, adicionar essa atividade de fluxo de dados a esse pipeline e executá-lo. Você pode executar a partir de debug ou de uma execução disparada. Após alguns minutos, você deve ter um novo contêiner desnormalizado de pedidos chamado "pedidos" no banco de dados CosmosDB.

## <a name="next-steps"></a>Próximas etapas

* Compile o restante da lógica de fluxo de dados usando as [transformações](concepts-data-flow-overview.md)de fluxos de dados de mapeamento.
* [Baixe o modelo de pipeline concluído](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip) para este tutorial e importe o modelo para sua fábrica.
