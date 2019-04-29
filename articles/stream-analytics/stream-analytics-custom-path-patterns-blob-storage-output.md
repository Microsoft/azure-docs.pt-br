---
title: Particionamento de saída de blob personalizado do Azure Stream Analytics
description: Este artigo descreve os padrões de caminho DateTime personalizados e os recursos de campo ou de atributos personalizados para a saída do armazenamento de blobs de trabalhos do Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9cdf99884845a9cb83ac26723c3ea0e7a779ebff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60771745"
---
# <a name="azure-stream-analytics-custom-blob-output-partitioning"></a>Particionamento de saída de blob personalizado do Azure Stream Analytics

O Azure Stream Analytics dá suporte a particionamento de saída de blob personalizado com campos ou atributos personalizados e padrões de caminho DateTime personalizados. 

## <a name="custom-field-or-attributes"></a>Campo ou atributos personalizados

Campo personalizado ou atributos de entrada melhoram os fluxos de trabalho de processamento de dados e relatório downstream permitindo mais controle sobre a saída.

### <a name="partition-key-options"></a>Opções de chave de partição

A chave de partição ou o nome da coluna, usado para particionar dados de entrada, pode conter caracteres alfanuméricos com espaços, sublinhados e hifens. Não é possível usar campos aninhados como uma chave de partição, a menos que usados em conjunto com aliases.

### <a name="example"></a>Exemplo

Suponha que um trabalho pegue dados de entrada de sessões de usuário ativas conectadas a um serviço de videogame externo em que os dados ingeridos contêm uma coluna **client_id** para identificar as sessões. Para particionar os dados por **client_id**, defina o campo Padrão de Caminho de Blob para incluir um token de partição **{client_id}** nas propriedades de saída de blob ao criar um trabalho. Como dados com vários valores de **client_id** fluem pelo trabalho do Stream Analytics, os dados de saída são salvos em pastas separadas com base em um único valor **client_id** por pasta.

![Padrão de caminho com a ID do cliente](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-path-pattern-client-id.png)

Da mesma forma, se a entrada do trabalho for composta por dados do sensor de milhões de sensores em que cada sensor tem uma **sensor_id**, o padrão de caminho será **{sensor_id}** para particionar os dados de cada sensor em pastas diferentes.  


Usando a API REST, a seção de saída de um arquivo JSON usado para essa solicitação pode se parecer com a seguinte:  

![Saída da API REST](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-rest-output.png)

Depois que o trabalho começa a ser executado, o contêiner de *clientes* pode ser semelhante ao seguinte:  

![Contêiner de clientes](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-clients-container.png)

Cada pasta pode conter vários blobs em que cada blob contém um ou mais registros. No exemplo acima, há um único blob em uma pasta rotulada "06000000" com o seguinte conteúdo:

![Conteúdo do blob](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-contents.png)

Observe que cada registro no blob tem uma coluna **client_id** correspondendo ao nome da pasta, já que a coluna usada para particionar a saída no caminho de saída era **client_id**.

### <a name="limitations"></a>Limitações

1. Apenas uma chave de partição personalizada é permitida na propriedade de saída de blob do Padrão de Caminho. Todos os Padrões de Caminho a seguir são válidos:

   * cluster1/{date}/{aFieldInMyData}  
   * cluster1/{time}/{aFieldInMyData}  
   * cluster1 / {aFieldInMyData}  
   * cluster1 / {data} / {hora} / {aFieldInMyData}  

2. Chaves de partição diferenciam maiúsculas de minúsculas, portanto, as chaves de partição, como "John" e "john" são equivalentes. Além disso, as expressões não podem ser usadas como chaves de partição. Por exemplo, **{columnA + columnB}** não funciona.  

3. Quando um fluxo de entrada consiste em registros com uma cardinalidade de chave de partição abaixo de 8000, os registros serão acrescentados a blobs existentes e apenas criarão novos quando necessário. Se a cardinalidade for superior a 8000, não haverá nenhuma garantia de que os blobs existentes serão gravados e novos blobs não serão criados para um número arbitrário de registros com a mesma chave de partição.  

## <a name="custom-datetime-path-patterns"></a>Padrões de caminho de DateTime personalizados

Padrões de caminho de DateTime personalizados permitem que você especifique um formato de saída que se alinhe com as convenções de Streaming de Hive, possibilitando ao Azure Stream Analytics enviar dados para o Azure HDInsight e o Azure Databricks para processamento downstream. Padrões de caminho de DateTime personalizados são implementados facilmente usando a palavra-chave `datetime` no campo de Prefixo do caminho de sua saída de blob, juntamente com o especificador de formato. Por exemplo, `{datetime:yyyy}`.

### <a name="supported-tokens"></a>Tokens com suporte

Os seguintes tokens especificadores de formato podem ser usados sozinhos ou de forma combinada para chegar aos formatos de DateTime personalizados:

|Especificador de formato   |DESCRIÇÃO   |Resulta na hora de exemplo 2018-01-02T10:06:08|
|----------|-----------|------------|
|{datetime:yyyy}|O ano como um número de quatro dígitos|2018|
|{datetime:MM}|Mês de 01 a 12|01|
|{datetime:M}|Mês de 1 a 12|1|
|{datetime:dd}|Dia de 01 a 31|02|
|{datetime:d}|Dia de 1 a 12|2|
|{datetime:HH}|Hora usando o formato de 24 horas, de 00 a 23|10|
|{datetime:mm}|Minutos de 00 a 24|06|
|{datetime:m}|Minutos de 0 a 24|6|
|{datetime:ss}|Segundos de 00 a 60|08|

Se não quiser usar padrões de DateTime personalizados, você poderá adicionar o token {date} e/ou {time} ao prefixo do caminho para gerar um menu suspenso com os formatos de DateTime internos.

![Formatos de DateTime antigos do Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

### <a name="extensibility-and-restrictions"></a>Extensibilidade e restrições

Você pode usar tantos tokens, `{datetime:<specifier>}`, quantos quiser no padrão do caminho, até alcançar o limite de caracteres do Prefixo do caminho. Especificadores de formato não podem ser combinados em um único token além das combinações já listadas pelos menus suspensos de data e hora. 

Para uma partição de caminho de `logs/MM/dd`:

|Expressão válida   |Expressão inválida   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

Você pode usar o mesmo especificador de formato várias vezes no Prefixo do caminho. O token deve ser repetido a cada vez.

### <a name="hive-streaming-conventions"></a>Convenções de streaming de Hive

Padrões de caminho personalizados para o Armazenamento de Blobs podem ser usados com a convenção de streaming de Hive, que espera que as pastas sejam rotuladas com `column=` no nome da pasta.

Por exemplo, `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`.

A saída personalizada elimina o incômodo de alterar as tabelas e adicionar partições manualmente para mover dados entre o Azure Stream Analytics e o Hive. Em vez disso, muitas pastas podem ser adicionadas automaticamente usando:

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>Exemplo

Crie uma conta de armazenamento, um grupo de recursos, um trabalho do Stream Analytics e uma fonte de entrada de acordo com o guia de início rápido do [Azure Stream Analytics no Portal do Azure](stream-analytics-quick-create-portal.md) guia de início rápido. Use os mesmos dados de exemplo usados no guia de início rápido, também disponíveis no [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).

Crie um coletor de saída de blob com a seguinte configuração:

![Criar coletor de saída de blob do Stream Analytics](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

O padrão de caminho completo é o seguinte:


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


Quando você inicia o trabalho, uma estrutura de pastas com base no padrão do caminho é criada em seu contêiner de blob. Você pode fazer drill down até o nível do dia.

![Saída de blob do Stream Analytics com padrão de caminho personalizado](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>Próximas etapas

* [Entender as saídas do Azure Stream Analytics](stream-analytics-define-outputs.md)
