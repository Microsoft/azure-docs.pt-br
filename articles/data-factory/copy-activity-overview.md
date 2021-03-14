---
title: Atividade de cópia no Azure Data Factory
description: Saiba mais sobre a atividade de cópia no Azure Data Factory. Você pode usá-lo para copiar dados de um armazenamento de dados de origem com suporte para um armazenamento de dados de coletor com suporte.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: jingwang
ms.openlocfilehash: d52a0bba5fddaa865b8fad74b778ba7a3838b2a4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387896"
---
# <a name="copy-activity-in-azure-data-factory"></a>Atividade de cópia no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-data-movement-activities.md)
> * [Versão atual](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

No Azure Data Factory, você pode usar a atividade de cópia para copiar dados entre repositórios de dados localizados localmente e na nuvem. Depois de copiar os dados, você pode usar outras atividades para transformá-los e analisá-los ainda mais. Você também pode usar a atividade de cópia para publicar os resultados da transformação e da análise para business intelligence (BI) e o consumo de aplicativos.

![A função da atividade de cópia](media/copy-activity-overview/copy-activity.png)

A atividade de cópia é executada em um [tempo de execução de integração](concepts-integration-runtime.md). Você pode usar diferentes tipos de tempos de execução de integração para diferentes cenários de cópia de dados:

* Ao copiar dados entre dois armazenamentos de dados publicamente acessíveis pela Internet de qualquer IP, você pode usar o tempo de execução de integração do Azure para a atividade de cópia. Esse tempo de execução de integração é seguro, confiável, escalonável e [globalmente disponível](concepts-integration-runtime.md#integration-runtime-location).
* Quando você está copiando dados de e para armazenamentos de dados que estão localizados localmente ou em uma rede com controle de acesso (por exemplo, uma rede virtual do Azure), você precisa configurar um tempo de execução de integração auto-hospedado.

Um tempo de execução de integração precisa ser associado a cada armazenamento de dados de origem e de coletor. Para obter informações sobre como a atividade de cópia determina qual tempo de execução de integração usar, consulte [determinando qual ir usar](concepts-integration-runtime.md#determining-which-ir-to-use).

Para copiar dados de uma origem para um coletor, o serviço que executa a atividade de cópia executa estas etapas:

1. Lê dados de um armazenamento de dados de origem.
2. Executa a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e assim por diante. Ele executa essas operações com base na configuração do conjunto de dados de entrada, no conjunto de informações de saída e na atividade de cópia.
3. Grava dados no armazenamento de dados de coletor/destino.

![Copiar a visão geral da atividade](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>Fontes de dados e formatos com suporte

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>Formatos de arquivo com suporte

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Você pode usar a atividade de cópia para copiar arquivos no estado em que se encontram entre dois armazenamentos de dados baseados em arquivo; nesse caso, os dados são copiados com eficiência sem nenhuma serialização ou desserialização. Além disso, você também pode analisar ou gerar arquivos de um determinado formato, por exemplo, você pode executar o seguinte:

* Copie dados de um banco de SQL Server e grave em Azure Data Lake Storage Gen2 no formato parquet.
* Copiar arquivos no formato de texto (CSV) de um sistema de arquivos local e gravar no armazenamento de BLOBs do Azure no formato Avro.
* Copie arquivos compactados de um sistema de arquivos local, descompacte-os imediatamente e grave arquivos extraídos em Azure Data Lake Storage Gen2.
* Copie dados no formato de texto compactado gzip (CSV) do armazenamento de BLOBs do Azure e grave-os no banco de dados SQL do Azure.
* Muitas outras atividades que exigem serialização/desserialização ou compactação/descompactação.

## <a name="supported-regions"></a>Regiões com suporte

O serviço que habilita a atividade de cópia está disponível globalmente nas regiões e geografias listados em [locais de tempo de execução de integração do Azure](concepts-integration-runtime.md#integration-runtime-location). A topologia globalmente disponível garante a movimentação de dados eficiente, o que geralmente evita saltos entre regiões. Consulte [produtos por região](https://azure.microsoft.com/regions/#services) para verificar a disponibilidade de data Factory e a movimentação de dados em uma região específica.

## <a name="configuration"></a>Configuração

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Em geral, para usar a atividade de cópia no Azure Data Factory, você precisa:

1. **Crie serviços vinculados para o armazenamento de dados de origem e o armazenamento de dados do coletor.** Você pode encontrar a lista de conectores com suporte na seção [armazenamentos de dados e formatos com suporte](#supported-data-stores-and-formats) deste artigo. Consulte a seção "Propriedades do serviço vinculado" do artigo do conector para obter informações de configuração e propriedades com suporte. 
2. **Crie conjuntos de valores para a origem e o coletor.** Consulte as seções "Propriedades do conjunto de dados" dos artigos do conector de origem e do coletor para obter informações de configuração e propriedades com suporte.
3. **Crie um pipeline com a atividade de cópia.** A próxima seção fornece um exemplo.

### <a name="syntax"></a>Syntax

O modelo a seguir de uma atividade de cópia contém uma lista completa de propriedades com suporte. Especifique as adequadas para o seu cenário.

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
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
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

#### <a name="syntax-details"></a>Detalhes da sintaxe

| Propriedade | Descrição | Necessário? |
|:--- |:--- |:--- |
| tipo | Para uma atividade de cópia, defina como `Copy` | Sim |
| entradas | Especifique o conjunto de dados que você criou que aponta para a origem. A atividade de cópia dá suporte a apenas uma única entrada. | Sim |
| outputs | Especifique o conjunto de dados que você criou que aponta para o coletor. A atividade de cópia dá suporte a apenas uma única saída. | Sim |
| typeProperties | Especifique as propriedades para configurar a atividade de cópia. | Sim |
| source | Especifique o tipo de origem da cópia e as propriedades correspondentes para recuperar dados.<br/>Para obter mais informações, consulte a seção "Propriedades da atividade de cópia" no artigo do conector listado em [formatos e armazenamentos de dados com suporte](#supported-data-stores-and-formats). | Sim |
| coletor | Especifique o tipo de coletor de cópia e as propriedades correspondentes para gravar dados.<br/>Para obter mais informações, consulte a seção "Propriedades da atividade de cópia" no artigo do conector listado em [formatos e armazenamentos de dados com suporte](#supported-data-stores-and-formats). | Sim |
| tradutor | Especifique mapeamentos de coluna explícita da origem para o coletor. Essa propriedade se aplica quando o comportamento de cópia padrão não atende às suas necessidades.<br/>Para obter mais informações, consulte [mapeamento de esquema na atividade de cópia](copy-activity-schema-and-type-mapping.md). | Não |
| dataIntegrationUnits | Especifique uma medida que represente a quantidade de energia que o [tempo de execução de integração do Azure](concepts-integration-runtime.md) usa para a cópia de dados. Essas unidades eram anteriormente conhecidas como DMU (unidades de movimentação de dados de nuvem). <br/>Para obter mais informações, consulte [unidades de integração de dados](copy-activity-performance-features.md#data-integration-units). | Não |
| parallelCopies | Especifique o paralelismo que você deseja que a atividade de cópia use ao ler dados da origem e gravar dados no coletor.<br/>Para obter mais informações, consulte [cópia paralela](copy-activity-performance-features.md#parallel-copy). | Não |
| preservar | Especifique se deseja preservar metadados/ACLs durante a cópia de dados. <br/>Para obter mais informações, consulte [preservar metadados](copy-activity-preserve-metadata.md). |Não |
| enableStaging<br/>stagingSettings | Especifique se deseja preparar os dados provisórios no armazenamento de BLOBs em vez de copiar dados diretamente da origem para o coletor.<br/>Para obter informações sobre cenários úteis e detalhes de configuração, consulte [cópia em etapas](copy-activity-performance-features.md#staged-copy). | Não |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| Escolha como manipular linhas incompatíveis ao copiar dados da origem para o coletor.<br/>Para obter mais informações, consulte [tolerância a falhas](copy-activity-fault-tolerance.md). | Não |

## <a name="monitoring"></a>Monitoramento

Você pode monitorar a execução da atividade de cópia no Azure Data Factory visualmente e de forma programática. Para obter detalhes, consulte [monitorar atividade de cópia](copy-activity-monitoring.md).

## <a name="incremental-copy"></a>Cópia incremental

Data Factory permite copiar incrementalmente dados Delta de um armazenamento de dados de origem para um armazenamento de dados de coletor. Para obter detalhes, consulte [tutorial: copiar dados incrementalmente](tutorial-incremental-copy-overview.md).

## <a name="performance-and-tuning"></a>Desempenho e ajuste

A experiência de [monitoramento da atividade de cópia](copy-activity-monitoring.md) mostra as estatísticas de desempenho de cópia para cada execução da atividade. O [guia desempenho e escalabilidade da atividade de cópia](copy-activity-performance.md) descreve os principais fatores que afetam o desempenho da movimentação de dados por meio da atividade de cópia no Azure data Factory. Ele também lista os valores de desempenho observados durante o teste e discute como otimizar o desempenho da atividade de cópia.

## <a name="resume-from-last-failed-run"></a>Retomar da última execução com falha

A atividade de cópia dá suporte à continuação da última execução com falha quando você copia grande tamanho de arquivos no estado em que se encontram com formato binário entre repositórios baseados em arquivo e optam por preservar a hierarquia de pastas/arquivos da origem para o coletor, por exemplo, para migrar dados do Amazon S3 para o Azure Data Lake Storage Gen2. Ele se aplica aos seguintes conectores baseados em arquivo: [Amazon S3](connector-amazon-simple-storage-service.md), [blob do Azure](connector-azure-blob-storage.md), [Azure data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md), [armazenamento de arquivos do Azure](connector-azure-file-storage.md), [sistema de arquivos](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md)e [SFTP](connector-sftp.md).

Você pode aproveitar o reinício da atividade de cópia das duas maneiras a seguir:

- **Repetição de nível de atividade:** Você pode definir a contagem de repetições na atividade de cópia. Durante a execução do pipeline, se a execução dessa atividade de cópia falhar, a próxima tentativa automática será iniciada no ponto de falha da última avaliação.
- **Executar novamente a partir da atividade com falha:** Após a conclusão da execução do pipeline, você também pode disparar uma reexecução da atividade com falha na exibição de monitoramento da interface do usuário do ADF ou programaticamente. Se a atividade com falha for uma atividade de cópia, o pipeline não será executado novamente a partir dessa atividade, mas também retomará a partir do ponto de falha da execução anterior.

    ![Copiar retomar](media/copy-activity-overview/resume-copy.png)

Alguns pontos a serem observados:

- A retomada acontece no nível do arquivo. Se a atividade de cópia falhar ao copiar um arquivo, na próxima execução, esse arquivo específico será copiado novamente.
- Para que o currículo continue a funcionar corretamente, não altere as configurações da atividade de cópia entre o execute novamente.
- Quando você copia dados do Amazon S3, BLOB do Azure, Azure Data Lake Storage Gen2 e Google Cloud Storage, a atividade de cópia pode ser retomada de um número arbitrário de arquivos copiados. Enquanto para o restante de conectores baseados em arquivo como fonte, a atividade de cópia atualmente dá suporte à retomada de um número limitado de arquivos, geralmente no intervalo de dezenas de milhares e varia dependendo do comprimento dos caminhos de arquivo; os arquivos que ultrapassarem esse número serão copiados novamente durante a reinicialização.

Para outros cenários que a cópia de arquivo binário, a atividade de cópia executa novamente começa desde o início.

## <a name="preserve-metadata-along-with-data"></a>Preservar metadados junto com os dados

Ao copiar dados da origem para o coletor, em cenários como a migração do data Lake, você também pode optar por preservar os metadados e ACLs juntamente com os dados usando a atividade de cópia. Consulte [preservar metadados](copy-activity-preserve-metadata.md) para obter detalhes.

## <a name="schema-and-data-type-mapping"></a>Mapeamento de tipo de dados e de esquema

Consulte [mapeamento de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para obter informações sobre como a atividade de cópia mapeia os dados de origem para o coletor.

## <a name="add-additional-columns-during-copy"></a>Adicionar colunas adicionais durante a cópia

Além de copiar dados do armazenamento de dados de origem para o coletor, você também pode configurar o para adicionar colunas de dados adicionais para copiar junto com o coletor. Por exemplo:

- Ao copiar da fonte baseada em arquivo, armazene o caminho relativo do arquivo como uma coluna adicional para rastrear de qual arquivo os dados vêm.
- Duplicar a coluna de origem especificada como outra coluna. 
- Adicione uma coluna com a expressão do ADF para anexar variáveis de sistema do ADF, como nome do pipeline/ID do pipeline, ou armazenar outro valor dinâmico da saída da atividade upstream.
- Adicione uma coluna com valor estático para atender à necessidade de consumo downstream.

Você pode encontrar a seguinte configuração na guia origem da atividade de cópia. Você também pode mapear essas colunas adicionais no [mapeamento de esquema](copy-activity-schema-and-type-mapping.md#schema-mapping) da atividade de cópia como de costume usando os nomes de coluna definidos. 

![Adicionar colunas adicionais na atividade de cópia](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>Esse recurso funciona com o modelo de conjunto de informações mais recente. Se você não vir essa opção na interface do usuário, tente criar um novo conjunto de um.

Para configurá-lo programaticamente, adicione a `additionalColumns` propriedade em sua fonte de atividade de cópia:

| Propriedade | Descrição | Necessária |
| --- | --- | --- |
| additionalColumns | Adicione colunas de dados adicionais para copiar para o coletor.<br><br>Cada objeto sob a `additionalColumns` matriz representa uma coluna extra. O `name` define o nome da coluna e `value` indica o valor de dados dessa coluna.<br><br>Os valores de dados permitidos são:<br>- **`$$FILEPATH`** -uma variável reservada indica armazenar o caminho relativo dos arquivos de origem para o caminho da pasta especificado no conjunto de uma. Aplicar à fonte baseada em arquivo.<br>- **`$$COLUMN:<source_column_name>`** -um padrão de variável reservada indica a duplicação da coluna de origem especificada como outra coluna<br>- **Expressão**<br>- **Valor estático** | Não |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "newColName",
                        "value": "$$COLUMN:SourceColumnA"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="auto-create-sink-tables"></a>Criar tabelas de coletor automaticamente

Ao copiar os dados para o SQL Database/Azure Synapse Analytics, se a tabela de destino não existir, a atividade de cópia dará suporte à criação automática com base nos dados de origem. Ele tem o objetivo de ajudá-lo a começar a carregar os dados e avaliar o SQL Database/Azure Synapse Analytics. Após a ingestão de dados, você pode revisar e ajustar o esquema da tabela de coletor de acordo com suas necessidades.

Esse recurso tem suporte ao copiar dados de qualquer fonte nos armazenamentos de dados do coletor a seguir. Você pode encontrar a opção na *interface do usuário de criação do ADF* – opção > o *coletor de atividade de cópia* – > *tabela* – > *criação automática de tabela* ou por meio `tableOption` da propriedade na carga do coletor da atividade de cópia.

- [Banco de Dados SQL do Azure](connector-azure-sql-database.md)
- [Instância Gerenciada do Banco de Dados SQL do Azure](connector-azure-sql-managed-instance.md)
- [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)
- [SQL Server](connector-sql-server.md)

![Criar tabelas de coletor](media/copy-activity-overview/create-sink-table.png)

## <a name="fault-tolerance"></a>Tolerância a falhas

Por padrão, a atividade de cópia interrompe a cópia de dados e retorna uma falha quando as linhas de dados de origem são incompatíveis com as linhas de dados do coletor. Para que a cópia seja realizada com sucesso, você pode configurar a atividade de cópia para ignorar e registrar em log as linhas incompatíveis e copiar apenas os dados compatíveis. Consulte [tolerância a falhas da atividade de cópia](copy-activity-fault-tolerance.md) para obter detalhes.

## <a name="data-consistency-verification"></a>Verificação de consistência de dados

Quando você move dados do repositório de origem para o de destino, a atividade de cópia do Azure Data Factory fornece uma opção para realizar a verificação de consistência de dados adicional a fim de garantir que os dados não sejam apenas copiados com êxito da origem para o repositório de destino, mas também verificados como consistentes entre o repositório de origem e destino. Depois que arquivos inconsistentes forem encontrados durante a movimentação de dados, você poderá abortar a atividade de cópia ou continuar a copiar o restante habilitando a configuração de tolerância a falhas para ignorar arquivos inconsistentes. Você pode obter os nomes de arquivo ignorados habilitando a configuração de log de sessão na atividade de cópia. Consulte [verificação de consistência de dados na atividade de cópia](copy-activity-data-consistency.md) para obter detalhes.

## <a name="session-log"></a>Log de sessão
Você pode registrar em log os nomes de arquivo copiados, o que pode ajudá-lo a garantir que os dados não sejam copiados com êxito da origem para o repositório de destino, mas também consistentes entre o repositório de origem e destino, revisando os logs de sessão da atividade de cópia. Consulte [log de sessão na atividade de cópia](copy-activity-log.md) para obter detalhes.

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes guias de início rápido, tutoriais e exemplos:

- [Copiar dados de um local para outro na mesma conta de armazenamento de BLOBs do Azure](quickstart-create-data-factory-dot-net.md)
- [Copiar dados do armazenamento de BLOBs do Azure para o banco de dados SQL do Azure](tutorial-copy-data-dot-net.md)
- [Copiar dados de um banco de dado SQL Server para o Azure](tutorial-hybrid-copy-powershell.md)
