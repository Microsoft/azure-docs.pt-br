---
title: "Atividade de cópia no Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre a atividade de cópia no Azure Data Factory que você pode usar apenas para copiar dados de um armazenamento de dados de origem com suporte para um armazenamento de dados de coletor com suporte."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 784b9489911be0d9a559b6fe7795a6c2cbdb09fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-activity-in-azure-data-factory"></a>Atividade de cópia no Azure Data Factory

## <a name="overview"></a>Visão geral

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-data-movement-activities.md)
> * [Versão 2 – Versão prévia](copy-activity-overview.md)

No Azure Data Factory, você pode usar a atividade de cópia para copiar dados entre armazenamentos de dados locais e na nuvem. Após os dados serem copiados, eles ainda podem ser transformados e analisados. Também é possível usar a Atividade de Cópia a fim de publicar resultados de análise e transformação para consumo do aplicativo e BI (business intelligence).

![Função da Atividade de Cópia](media/copy-activity-overview/copy-activity.png)

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Atividade de cópia na V1](v1/data-factory-data-movement-activities.md).

A atividade de cópia é executada em um [Integration Runtime](concepts-integration-runtime.md). Para cenários de cópia de dados diferentes, podem ser utilizados tipos diferentes do Integration Runtime:

* Ao copiar dados entre armazenamentos de dados que são publicamente acessíveis, a atividade de cópia pode ser capacitada pelo **Integration Runtime do Azure**, que é seguro, confiável, escalonável e tem [disponibilidade global](concepts-integration-runtime.md#integration-runtime-location).
* Ao copiar dados de/para armazenamentos de dados locais ou em uma rede com controle de acesso (por exemplo, Rede Virtual do Azure), você precisa configurar um **Tempo de Execução Integrado auto-hospedado** para capacitar a cópia de dados.

O Integration Runtime precisa ser associado a cada armazenamento de dados de origem e de coletor. Obtenha detalhes sobre como a atividade de cópia [determina qual IR usar](concepts-integration-runtime.md#determining-which-ir-to-use).

A atividade de cópia passa pelos seguintes estágios para copiar dados de uma origem para um coletor. O serviço que capacita a Atividade de Cópia:

1. Lê dados de um armazenamento de dados de origem.
2. Executa a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna etc. Ele realiza essas operações com base nas configurações do conjunto de dados de entrada, no conjunto de dados de saída e na Atividade de Cópia.
3. Grava dados no armazenamento de dados de coletor/destino.

![Visão Geral da Atividade de Cópia](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Fontes de dados e formatos com suporte

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formatos de arquivo com suporte

Você pode usar a atividade de cópia para **copiar arquivos como estão** entre dois armazenamentos de dados baseados em arquivo, caso em que os dados são copiados de forma eficiente sem nenhuma serialização/desserialização.

A atividade de cópia também dá suporte à leitura de e à gravação em arquivos em formatos especificados: há suporte para **texto, JSON, Avro, ORC e Parquet** e codecs de compactação **GZip, Deflate, BZip2 e ZipDeflate**. Consulte [Formatos de arquivo e compactação com suporte](supported-file-formats-and-compression-codecs.md) para obter detalhes.

Por exemplo, você pode fazer as seguintes atividades de cópia:

* Copiar dados no SQL Server local e gravar ao no Azure Data Lake Store no formato ORC.
* Copiar arquivos no formato de texto (CSV) do Sistema de Arquivos local e gravá-los no Blob do Azure no formato Avro.
* Copiar os arquivos compactados do Sistema de Arquivos local e, em seguida, descompactá-los no Azure Data Lake Store.
* Copiar dados em formato de texto (CSV) compactado por GZip do Blob do Azure e gravá-los no Banco de Dados SQL Azure.

## <a name="configuration"></a>Configuração

Para usar a atividade de cópia no Azure Data Factory, você precisa:

1. **Criar serviços vinculados para armazenamentos de dados de origem e de coletor.** Consulte a seção “Propriedades de serviço vinculadas” do artigo do conector sobre como configurar e as propriedades com suporte. Você pode encontrar a lista de conectores com suporte na seção [Formatos e armazenamentos de dados com suporte](#supported-data-stores-and-formats).
2. **Criar conjuntos de dados de origem e do coletor.** Consulte a seção “Propriedades do conjunto de dados” dos artigos do conector de origem e de coletor sobre como configurar e as propriedades com suporte.
3. **Criar um pipeline com atividade de cópia.** A próxima seção fornece um exemplo.  

### <a name="syntax"></a>Sintaxe

O modelo a seguir de uma atividade de cópia lista as propriedades com suporte esgotadas. Especifique as adequadas para seu cenário.

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "ColumnMappings": "<column mapping>"
            },
            "cloudDataMovementUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

### <a name="syntax-details"></a>Detalhes da sintaxe

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do coletor da atividade de cópia deve ser definida como: **Copy** | Sim |
| inputs | Especifique o conjunto de dados criado que aponta para os dados de origem. A atividade de cópia dá suporte a apenas uma entrada. | Sim |
| outputs | Especifique o conjunto de dados criado que aponta para os dados do coletor. A atividade de cópia dá suporte a apenas uma saída. | Sim |
| typeProperties | Um grupo de propriedades para configurar a atividade de cópia. | Sim |
| fonte | Especifique o tipo de origem de cópia e as propriedades correspondentes sobre como recuperar dados.<br/><br/>Saiba mais detalhes na seção "Propriedades da atividade de cópia” no artigo do conector listado em [Formatos e armazenamentos de dados com suporte](#supported-data-stores-and-formats). | Sim |
| coletor | Especifique o tipo de coletor de cópia e as propriedades correspondentes sobre como gravar dados.<br/><br/>Saiba mais detalhes na seção "Propriedades da atividade de cópia” no artigo do conector listado em [Formatos e armazenamentos de dados com suporte](#supported-data-stores-and-formats). | Sim |
| tradutor | Especifique mapeamentos de coluna explícita da origem para o coletor. Aplica-se quando o comportamento de cópia padrão não pode atender às suas necessidades.<br/><br/>Obtenha detalhes do [Mapeamento de tipo de dados e de esquema](copy-activity-schema-and-type-mapping.md). | Não |
| cloudDataMovementUnits | Especifique o poder do [Integration Runtime do Azure](concepts-integration-runtime.md) para capacitar a cópia de dados.<br/><br/>Obtenha detalhes em [Unidades de movimentação de dados de nuvem](copy-activity-performance.md). | Não |
| parallelCopies | Especifique o paralelismo que você deseja que a atividade de cópia use ao ler dados da origem e gravar dados no coletor.<br/><br/>Obtenha detalhes em [Cópia paralela](copy-activity-performance.md#parallel-copy). | Não |
| enableStaging<br/>stagingSettings | Opte por preparar os dados provisórios em um armazenamento de blobs em vez de copiar os dados diretamente da origem para o coletor.<br/><br/>Conheça os cenários úteis e os detalhes de configuração em [Cópia em etapas](copy-activity-performance.md#staged-copy). | Não |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Escolha como lidar com linhas incompatíveis ao copiar dados da origem para o coletor.<br/><br/>Obtenha detalhes em [Tolerância a falhas](copy-activity-fault-tolerance.md). | Não |

## <a name="monitoring"></a>Monitoramento

As características de desempenho e detalhes da execução da atividade de cópia são retornadas na seção Resultado da execução da atividade de cópia -> Saída. Abaixo está uma lista esgotada. Saiba como monitorar a execução da atividade na [seção de monitoramento de início rápido](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run). Você pode comparar o desempenho e a configuração do seu cenário com a [referência de desempenho](copy-activity-performance.md#performance-reference) da atividade de cópia de testes internos.

| Nome da propriedade  | Descrição | Unidade |
|:--- |:--- |:--- |
| dataRead | Leitura do tamanho de dados da origem | Valor Int64 em bytes |
| dataWritten | Tamanho dos dados gravado no coletor | Valor Int64 em bytes |
| rowsCopied | Número de linhas que estão sendo copiadas (não aplicável para cópia binária). | Valor Int64 (nenhuma unidade) |
| rowsSkipped | Número de linhas incompatíveis que está sendo ignoradas. Você pode ativar o recurso definindo "enableSkipIncompatibleRow" como true. | Valor Int64 (nenhuma unidade) |
| throughput | Taxa em que os dados são transferidos | Número de ponto flutuante em KB/s |
| copyDuration | A duração da cópia | Valor Int32 em segundos |
| sqlDwPolyBase | Se o PolyBase é usado ao copiar dados para o SQL Data Warehouse. | Booliano |
| redshiftUnload | Se UNLOAD é usado ao copiar dados do Redshift. | Booliano |
| hdfsDistcp | Se DistCp é usado ao copiar dados do HDFS. | Booliano |
| effectiveIntegrationRuntime | Mostre quais Integration Runtime(s) são usados para capacitar a execução da atividade, no formato de "`<IR name> (<region if it's Azure IR>)`". | Texto (cadeia de caracteres) |
| usedCloudDataMovementUnits | As unidades de movimentação de dados de nuvem eficaz durante a cópia. | Valor Int32 |
| redirectRowPath | Caminho para o log de linhas incompatíveis ignoradas no armazenamento de blobs que você configurou em "redirectIncompatibleRowSettings". Consulte o exemplo abaixo. | Texto (cadeia de caracteres) |
| billedDuration | A duração sendo cobrada para a movimentação de dados. | Valor Int32 em segundos |

```json
"output": {
    "dataRead": 1024,
    "dataWritten": 2048,
    "rowsCopies": 100,
    "rowsSkipped": 2,
    "throughput": 1024.0,
    "copyDuration": 3600,
    "redirectRowPath": "https://<account>.blob.core.windows.net/<path>/<pipelineRunID>/",
    "redshiftUnload": true,
    "sqlDwPolyBase": true,
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 8,
    "billedDuration": 28800
}
```

## <a name="schema-and-data-type-mapping"></a>Mapeamento de tipo de dados e de esquema

Consulte o [Mapeamento de tipo de dados e de esquema](copy-activity-schema-and-type-mapping.md), que descreve como a atividade de cópia mapeia os dados de origem com o coletor.

## <a name="fault-tolerance"></a>Tolerância a falhas

Por padrão, a atividade de cópia para de copiar dados e retorna uma falha quando encontra dados incompatíveis entre a origem e o coletor. Você pode configurar explicitamente para ignorar e registrar e log as linhas incompatíveis e copiar apenas os dados compatíveis para fazer com que a cópia seja bem-sucedida. Consulte a [Tolerância a falhas da Atividade de Cópia](copy-activity-fault-tolerance.md) para obter mais detalhes.

## <a name="performance-and-tuning"></a>Desempenho e ajuste

Confira o artigo [Guia de desempenho e ajuste da Atividade de Cópia](copy-activity-performance.md)que descreve os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory. Ele também lista o desempenho observado durante os testes internos e discute várias maneiras de otimizar o desempenho da Atividade de Cópia.

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes guias de início rápido, tutoriais e exemplos:

- [Copiar dados de um local para outro no mesmo Armazenamento de Blobs do Azure](quickstart-create-data-factory-dot-net.md)
- [Copiar dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure](tutorial-copy-data-dot-net.md)
- [Copiar dados do SQL Server local para o Azure](tutorial-hybrid-copy-powershell.md)
