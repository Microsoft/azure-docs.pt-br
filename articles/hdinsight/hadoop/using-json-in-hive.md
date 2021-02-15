---
title: Analisar & processo JSON com Apache Hive-Azure HDInsight
description: Saiba como usar documentos JSON e analisá-los usando Apache Hive no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 5bc9acea219e5d111700840149a26c127b47514d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943059"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Processar e analisar documentos JSON usando o Apache Hive no HDInsight do Azure

Saiba como processar e analisar arquivos JSON (JavaScript Object Notation) usando o Apache Hive no HDInsight do Azure. Este artigo usa o seguinte documento JSON:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

O arquivo pode ser encontrado em `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Para obter mais informações sobre como usar armazenamento de Blobs do Azure com HDInsight, consulte [Usar armazenamento de Blobs do Azure compatível com HDFS com Apache Hadoop no HDInsight](../hdinsight-hadoop-use-blob-storage.md). Você pode copiar o arquivo para o contêiner padrão do seu cluster.

Neste artigo, você usa o console do Apache Hive. Para obter instruções sobre como abrir o console do hive, consulte [usar o modo de exibição do Apache Ambari Hive com o Apache Hadoop no HDInsight](apache-hadoop-use-hive-ambari-view.md).

> [!NOTE]  
> A exibição do Hive não está mais disponível no HDInsight 4.0.

## <a name="flatten-json-documents"></a>Nivelar os documentos JSON

Os métodos listados na próxima seção exigem que o documento JSON seja composto por uma única linha. Portanto, você deve nivelar o documento JSON com uma cadeia de caracteres. Se seu documento JSON já está nivelado, ignore esta etapa e vá diretamente para a próxima seção sobre como analisar dados JSON. Para nivelar o documento JSON, execute o seguinte script:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

O arquivo JSON bruto está localizado em `wasb://processjson@hditutorialdata.blob.core.windows.net/`. A tabela Hive **StudentsRaw** aponta para o documento JSON bruto que não é achatado.

A tabela **StudentsOneLine** do Hive armazena os dados no sistema de arquivos padrão do HDInsight no caminho **/json/students/**.

A instrução **INSERT** preenche a tabela **StudentOneLine** com os dados JSON nivelados.

A instrução **SELECT** retorna apenas uma linha.

Veja abaixo a saída da instrução **SELECT**:

![O HDInsight mescla o documento JSON](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Analisar documentos JSON no Hive

O Hive fornece três mecanismos diferentes para executar consultas em documentos JSON, ou você pode escrever o seu:

* Use a função definida pelo usuário get_json_object (UDF).
* Use o json_tuple UDF.
* Use o Serializador/Desserializador (SerDe) personalizado.
* Grave seu próprio UDF usando Python ou outras linguagens. Para saber mais sobre como executar seu próprio código Python com o Hive, consulte [UDF do Python com Apache Hive e Pig](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Usar o UDF get_json_object

O hive fornece um UDF interno chamado [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que consulta JSON durante o tempo de execução. Esse método usa dois argumentos: o nome da tabela e o nome do método. O nome do método tem o documento JSON nivelado e o campo JSON que precisa ser analisado. Vejamos um exemplo para ver como esse UDF funciona.

A consulta a seguir retorna o nome e o sobrenome de cada aluno:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Aqui está a saída quando você executar essa consulta na janela do console:

![Apache Hive obtém UDF do objeto JSON](./media/using-json-in-hive/hdinsight-get-json-object.png)

Há limitações do UDF get_json_object:

* Uma vez que cada campo na consulta requer uma nova análise da consulta, o desempenho é afetado.
* **GET\_JSON_OBJECT()** retorna a representação da cadeia de caracteres de uma matriz. Para converter essa matriz em uma matriz do Hive, você precisará usar expressões regulares para substituir os colchetes “[” e “]” e depois também realizar a divisão para obter a matriz.

Essa conversão é o motivo pelo qual o wiki do hive recomenda que você use **json_tuple**.  

### <a name="use-the-json_tuple-udf"></a>Usar o json_tuple UDF

Outro UDF fornecido pelo hive é chamado de [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), que faz melhor do que [get_ _object JSON](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Esse método usa um conjunto de chaves e uma cadeia de caracteres JSON. Em seguida, retorna uma tupla de valores. A consulta a seguir retorna a ID e a série do aluno por meio do documento JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

A saída deste script no console do Hive:

![Apache Hive resultados da consulta JSON](./media/using-json-in-hive/hdinsight-json-tuple.png)

O `json_tuple` UDF usa a sintaxe de [exibição lateral](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) no hive, que permite que a \_ tupla JSON crie uma tabela virtual aplicando a função UDT a cada linha da tabela original. JSONs complexos se tornam muito complicados devido ao uso repetido de **LATERAL VIEW**. Além disso, **JSON_TUPLE** não pode manipular jsons aninhados.

### <a name="use-a-custom-serde"></a>Usar um SerDe personalizado

SerDe é a melhor opção para análise de documentos JSON aninhados. Ele permite que você defina o esquema JSON e então use o esquema para analisar os documentos. Para obter instruções, veja [Como usar um SerDe JSON personalizado com o Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Resumo

O tipo de operador JSON no hive que você escolher dependerá do seu cenário. Com um documento JSON simples e um campo para pesquisar, escolha o **GET_JSON_OBJECT** UDF do hive. Se você tiver mais de uma chave para pesquisar, poderá usar **json_tuple**. Para documentos aninhados, use o **SerDe JSON**.

## <a name="next-steps"></a>Próximas etapas

Para artigos relacionados, veja:

* [Usar Apache Hive e HiveQL com Apache Hadoop no HDInsight para analisar um exemplo do arquivo log4j do Apache](./hdinsight-use-hive.md)
* [Analisar dados de atraso de voo usando a consulta interativa no HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)