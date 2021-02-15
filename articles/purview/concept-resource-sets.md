---
title: Compreensão dos conjuntos de recursos
description: Este artigo explica quais são os conjuntos de recursos e como o Azure alcance os cria.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 39a1400bfe642672ba276686851d4f0edebbeb00
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387845"
---
# <a name="understanding-resource-sets"></a>Compreensão dos conjuntos de recursos

Este artigo ajuda você a entender como o Azure alcance usa conjuntos de recursos para mapear ativos de dados para recursos lógicos.
## <a name="background-info"></a>Informações em segundo plano

Normalmente, os sistemas de processamento de dados em escala armazenam uma única tabela em um disco como vários arquivos. Esse conceito é representado no Azure alcance usando conjuntos de recursos. Um conjunto de recursos é um único objeto no catálogo que representa um grande número de ativos no armazenamento.

Por exemplo, suponha que o cluster Spark tenha persistido um dataframe em uma fonte de dados Gen2 do armazenamento do Azure Dataal Lake (ADLS). Embora no Spark a tabela se pareça com um único recurso lógico, o disco provavelmente tem milhares de arquivos parquet, cada um representando uma partição do conteúdo total do dataframe. Dados de IoT e dados de log da Web têm o mesmo desafio. Imagine que você tenha um sensor que produza arquivos de log várias vezes por segundo. Não levará muito tempo até que você tenha centenas de milhares de arquivos de log desse único sensor.

Para resolver o desafio de mapear um grande número de ativos de dados para um único recurso lógico, o Azure alcance usa conjuntos de recursos.

## <a name="how-azure-purview-detects-resource-sets"></a>Como o Azure alcance detecta conjuntos de recursos

O Azure alcance dá suporte à detecção de conjuntos de recursos no armazenamento de BLOBs do Azure, ADLS Gen1 e ADLS Gen2.

O Azure alcance detecta automaticamente conjuntos de recursos durante a verificação. Esse recurso analisa todos os dados que são ingeridos por meio da verificação e os compara com um conjunto de padrões definidos.

Por exemplo, suponha que você examine uma fonte de dados cuja URL é `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . O Azure alcance examina os segmentos de caminho e determina se eles correspondem a quaisquer padrões internos. Ele tem padrões internos para GUIDs, números, formatos de data, códigos de localização (por exemplo, en-US) e assim por diante. Nesse caso, o padrão numérico corresponde a *23*. O alcance do Azure pressupõe que esse arquivo é parte de um conjunto de recursos denominado `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Ou, para uma URL como `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` , o Azure alcance corresponde ao padrão de localização e ao padrão de número, produzindo um conjunto de recursos denominado `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Usando essa estratégia, o Azure alcance mapearia os seguintes recursos para o mesmo conjunto de recursos `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Tipos de arquivo que o Azure alcance não detectará como conjuntos de recursos

O alcance intencionalmente não tenta classificar a maioria dos tipos de arquivos de documentos como Word, Excel ou PDF como conjuntos de recursos. A exceção é o formato CSV, pois esse é um formato de arquivo particionado comum.

## <a name="how-azure-purview-scans-resource-sets"></a>Como o Azure alcance examina conjuntos de recursos

Quando o Azure alcance detecta os recursos que ele considera que fazem parte de um conjunto de recursos, ele alterna de uma verificação completa para uma verificação de exemplo. Em um exame de exemplo, ele abre apenas um subconjunto dos arquivos que ele acha que estão no conjunto de recursos. Para cada arquivo que ele abre, ele usa seu esquema e executa seus classificadores. Em seguida, o Azure alcance localiza o recurso mais recente entre os recursos abertos e usa o esquema e as classificações do recurso na entrada para todo o conjunto de recursos no catálogo.

## <a name="what-azure-purview-stores-about-resource-sets"></a>O que o Azure alcance armazena sobre conjuntos de recursos

Além de um único esquema e classificações, o Azure alcance armazena as seguintes informações sobre conjuntos de recursos:

- Dados do último recurso de partição que ele examinou em profundidade.
- Agregar informações sobre os recursos de partição que compõem o conjunto de recursos.
- Uma contagem de partições que mostra quantos recursos de partição foram encontrados.
- Uma contagem de esquema que mostra quantos esquemas exclusivos ele encontrou no exemplo de conjunto em que faziam verificações profundas. Esse valor é um número entre 1 e 5, ou para valores maiores que 5, 5 +.
- Uma lista de tipos de partição quando mais de um tipo de partição única é incluído no conjunto de recursos. Por exemplo, um sensor IoT pode produzir arquivos XML e JSON, embora ambos sejam logicamente parte do mesmo conjunto de recursos.

## <a name="built-in-resource-set-patterns"></a>Padrões de conjunto de recursos internos

O Azure alcance dá suporte aos seguintes padrões de conjunto de recursos. Esses padrões podem aparecer como um nome em um diretório ou como parte de um nome de arquivo.
### <a name="regex-based-patterns"></a>Padrões baseados em Regex

| Nome do padrão | Nome de exibição | Descrição |
|--------------|--------------|-------------|
| Guid         | VOLUME       | Um identificador global exclusivo, conforme definido no [RFC 4122](https://tools.ietf.org/html/rfc4122) |
| Número       | P          | Um ou mais dígitos |
| Formatos de data/hora | Year Mês Diário P     | Damos suporte a vários formatos de data/hora, mas todos são representados com {Year} [delimitador] {month} [delimitador] {Day} ou série de {N} s. |
| 4ByteHex     | PRESSÃO        | Um número HEXADECIMAL de 4 dígitos. |
| Localização | LOCALIZAÇÃO        | Uma marca de idioma conforme definida no [BCP 47](https://tools.ietf.org/html/bcp47), ambos os nomes são suportados (por exemplo, en_CA e en-CA) |

### <a name="complex-patterns"></a>Padrões complexos

| Nome do padrão | Nome de exibição | Descrição |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Identificador de arquivo de partição do Spark |
| Incaminho de data (aaaa/mm/dd)  | {Year}/{Month}/{Day} | Padrão de ano/mês/dia abrangendo várias pastas |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Como os conjuntos de recursos são exibidos no catálogo alcance do Azure

Quando o Azure alcance corresponde a um grupo de ativos em um conjunto de recursos, ele tenta extrair as informações mais úteis a serem usadas como um nome de exibição no catálogo. Alguns exemplos da Convenção de nomenclatura padrão são aplicadas: 

### <a name="example-1"></a>Exemplo 1

Nome qualificado: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Nome de exibição: "nome da saída do Spark"

### <a name="example-2"></a>Exemplo 2

Nome qualificado: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Nome de exibição: "meus dados particionados"

### <a name="example-3"></a>Exemplo 3

Nome qualificado: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Nome de exibição: "dados"

## <a name="known-issues-with-resource-sets"></a>Problemas conhecidos com conjuntos de recursos

Embora os conjuntos de recursos funcionem bem na maioria dos casos, você pode encontrar os seguintes problemas, nos quais o Azure alcance:

- Marca incorretamente um ativo como um conjunto de recursos
- Coloca um ativo no conjunto de recursos errado
- Marca incorretamente um ativo como não sendo um conjunto de recursos

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o Azure alcance, consulte [início rápido: criar uma conta do Azure alcance](create-catalog-portal.md).
