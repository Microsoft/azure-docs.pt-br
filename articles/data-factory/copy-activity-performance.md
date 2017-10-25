---
title: "Guia de desempenho e ajuste da atividade de cópia o Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre os principais fatores que afetam o desempenho da movimentação de dados no Azure Data Factory quando você usa a Atividade de Cópia."
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
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: 3f2b95e57e34905bf1128e9aee2862110a598f75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Guia Desempenho e ajuste da Atividade de Cópia
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-copy-activity-performance.md)
> * [Versão 2 – Versão prévia](copy-activity-performance.md)


A Atividade de cópia do Azure Data Factory fornece uma solução de dados excelente, segura, confiável e de alto desempenho. Ela permite que você a copie dezenas de terabytes de dados diariamente em uma grande variedade de repositórios de dados na nuvem e locais. Desempenho de carregamento de dados de rápido são a chave para garantir que você possa se concentrar no principal problema de "Big Data": a criação de soluções de análise avançada e obtenção de informações aprofundadas de todos esses dados.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte o [desempenho da atividade de cópia no Data Factory versão 1](v1/data-factory-copy-activity-performance.md).

O Azure fornece um conjunto de soluções de armazenamento de dados e data warehouse de nível empresarial, e atividade de cópia oferece uma experiência de carregamento de dados altamente otimizada que é fácil de configurar. Com apenas uma atividade de cópia única, você pode obter:

* Carregar dados no **SQL Data Warehouse do Azure** a **1,2 GBps**.
* O carregamento de dados no **Armazenamento de Blobs do Azure** a **1,0 GBps**
* O carregamento de dados no **Azure Data Lake Store** a **1,0 GBps**

Este artigo descreve:

* [números de referência de desempenho](#performance-reference) para armazenamentos de dados de origem e coletor com suporte, para ajudá-lo a planejar o projeto;
* Recursos que podem aumentar a taxa de transferência de cópia em cenários diferentes, incluindo [unidades de movimentação de dados em nuvem](#cloud-data-movement-units), [cópia paralela](#parallel-copy) e [cópia em etapas](#staged-copy);
* [diretrizes de ajuste de desempenho](#performance-tuning-steps) sobre como ajustar o desempenho e os principais fatores que podem afetar o desempenho da cópia.

> [!NOTE]
> Se você não estiver familiarizado com a Atividade de Cópia em geral, consulte [Visão geral da atividade de cópia](copy-activity-overview.md) antes de ler este artigo.
>

## <a name="performance-reference"></a>Referência de desempenho

Como uma referência, a tabela abaixo mostra o número da taxa de transferência **em MBps** para determinados pares de origem e coletor **em uma única execução de atividade de cópia** com base em testes internos. Para comparação, ela também demonstra como as diferentes configurações das [unidades de movimentação de dados na nuvem](#cloud-data-movement-units) ou da [escalabilidade do Integration Runtime auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime) (vários nós) podem ajudar no desempenho da cópia.

![Matriz de desempenho](./media/copy-activity-performance/CopyPerfRef.png)

>[!IMPORTANT]
>No Azure Data Factory versão 2, quando a atividade de cópia é executada em um Integration Runtime do Azure, o número mínimo de Unidades de Movimentação de dados na nuvem é dois.

Pontos a serem observados:

* A taxa de transferência é calculada usando a seguinte fórmula: [tamanho dos dados lidos na origem]/[duração da execução da Atividade de Cópia].
* Os números de referência de desempenho na tabela foram medidos usando o conjunto de dados [TPC-H](http://www.tpc.org/tpch/) em uma execução de atividade de cópia única.
* Nos armazenamentos de dados do Azure, a origem e o coletor estão na mesma região do Azure.
* Para a cópia híbrida entre os armazenamentos de dados local e na nuvem, cada nó do Integration Runtime auto-hospedado estava em execução em um computador separado do armazenamento de dados com a especificação abaixo. Quando uma única atividade foi executada, a operação de cópia consumiu apenas uma pequena parte da CPU, da memória ou da largura de banda da rede do computador de teste.
    <table>
    <tr>
        <td>CPU</td>
        <td>32 núcleos, Intel Xeon E5-2660 v2 de 2.20 GHz</td>
    </tr>
    <tr>
        <td>Memória</td>
        <td>128 GB</td>
    </tr>
    <tr>
        <td>Rede</td>
        <td>Interface da Internet: 10 Gbps; Interface da intranet: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> Você pode obter uma maior taxa de transferência usando mais DMUs (unidades de movimentação de dados) do que o padrão máximo permitido, que é 32 para a execução de uma atividade de cópia de nuvem para nuvem. Por exemplo, com 100 DMUs, é possível copiar dados do Blob do Azure para o Azure Data Lake Store a **1 GBps**. Confira a seção [Unidades de movimentação de dados de nuvem](#cloud-data-movement-units) para obter detalhes sobre esse recurso e o cenário com suporte. Entre em contato com [suporte do Azure](https://azure.microsoft.com/support/) para solicitar mais DMUs.

## <a name="cloud-data-movement-units"></a>Unidades de movimentação de dados de nuvem

Uma **unidade de movimentação de dados de nuvem (DMU)** é uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos da rede) de uma unidade única no Data Factory. **A DMU se aplica somente ao [Integration Runtime do Azure](concepts-integration-runtime.md#azure-integration-runtime)**, mas não ao [Integration Runtime auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime).

**O mínimo de unidades de movimentação de dados em nuvem para capacitar execução da atividade de cópia é dois.** A tabela a seguir lista as DMUs padrão usadas em cenários de cópia diferentes.

| Copiar cenário | DMUs padrão determinadas pelo serviço |
|:--- |:--- |
| Copiar dados entre repositórios baseados em arquivo | Entre 2 e 16 dependendo do número e tamanho dos arquivos. |
| Copiar dados do Salesforce/Dynamics | 4 |
| Todos os outros cenários de cópia | 2 |

Para substituir esse padrão, especifique um valor para a propriedade **cloudDataMovementUnits** da seguinte maneira. Os **valores permitidos** para a propriedade **cloudDataMovementUnits** são 2, 4, 8, 16, 32. O **número real de DMUs de nuvem** que a operação de cópia usa na execução é igual ou menor que o valor configurado, dependendo do seu padrão de dados. Para obter informações sobre o nível de ganho de desempenho que você pode obter ao configurar mais unidades para uma origem e coletor de cópia específicos, consulte a [referência de desempenho](#performance-reference).

Você pode ver as unidades de movimentação de dados na nuvem realmente usadas para cada execução na saída da atividade de cópia ao monitorar uma execução da atividade. Obtenha detalhes de [Monitoramento da atividade de cópia](copy-activity-overview.md#monitoring).

> [!NOTE]
> Se precisar de mais DMUs de nuvem para uma taxa de transferência maior, entre em contato com o [suporte do Azure](https://azure.microsoft.com/support/). A configuração de 8 e superior atualmente funciona apenas quando você **copia vários arquivos do Armazenamento de Blobs/Data Lake Store/Amazon S3/FTP na nuvem/SFTP na nuvem para quaisquer outros armazenamentos de dados de nuvem**.
>

**Exemplo:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "cloudDataMovementUnits": 32
        }
    }
]
```

### <a name="cloud-data-movement-units-billing-impact"></a>Impacto na cobrança das unidades de movimentação de dados de nuvem

É **importante** lembrar que você é cobrado com base no tempo total da operação de cópia. A duração total pela qual você é cobrado para a movimentação de dados é a soma da duração entre as DMUs. Se um trabalho de cópia costumava levar uma hora com duas unidades de nuvem e agora leva 15 minutos com oito unidades de nuvem, a fatura geral fica quase igual.

## <a name="parallel-copy"></a>Cópia paralela

Você pode usar a propriedade **parallelCopies** para indicar o paralelismo que deseja que a Atividade de Cópia use. Você pode considerar essa propriedade como o número máximo de threads na Atividade de Cópia que podem ler a partir de sua fonte ou gravar em seus armazenamentos do coletor de dados em paralelo.

Para cada execução da Atividade de Cópia, o Data Factory determina o número de cópias paralelas a usar para copiar os dados do armazenamento de dados de origem para o de destino. O número padrão de cópias paralelas usadas depende do tipo de fonte e coletor usado:

| Copiar cenário | Contagem de cópia paralela padrão determinada pelo serviço |
| --- | --- |
| Copiar dados entre repositórios baseados em arquivo |Entre 1 e 32. Depende do tamanho dos arquivos e do número de DMUs (unidades de movimentação de dados) de nuvem usadas para copiar os dados entre dois armazenamentos de dados de nuvem ou da configuração física do computador do Integration Runtime auto-hospedado. |
| Copiar dados de qualquer armazenamento de dados de origem para o armazenamento de Tabelas do Azure |4 |
| Todos os outros cenários de cópia |1 |

Normalmente, o comportamento padrão deve fornecer a melhor taxa de transferência. No entanto, para controlar a carga em computadores que hospedam os armazenamentos de dados ou ajustar o desempenho da cópia, você pode optar por substituir o valor padrão e especificar um valor para a propriedade **parallelCopies** . O valor deve ser um inteiro maior ou igual a 1. Na execução, para ter o melhor desempenho, a Atividade de Cópia usa um valor menor ou igual ao valor definido.

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

Pontos a serem observados:

* Quando você copia dados entre repositórios baseados em arquivo, **parallelCopies** determina o paralelismo no nível de arquivo. O agrupamento em um único arquivo aconteceria abaixo de modo automático e transparente, sendo projetado para usar o tamanho da parte mais adequado para um determinado tipo de repositório de dados de modo a carregar dados de modo paralelo e ortogonal em parallelCopies. O número real de cópias paralelas que o serviço de movimentação de dados usa para a operação de cópia no tempo de execução não é superior ao número de arquivos existentes. Se o comportamento da cópia for **mergeFile**, a Atividade de Cópia não poderá aproveitar o paralelismo no nível de arquivo.
* Quando você especificar um valor para a propriedade **parallelCopies**, considere o aumento da carga nos armazenamentos de dados de origem e de coletor e para o Integration Runtime auto-hospedado se a atividade de cópia for capacitada por ele, por exemplo, para cópia híbrida. Isso ocorre especialmente quando você tem várias atividades ou execuções simultâneas das mesmas atividades executadas em relação ao mesmo armazenamento de dados. Se você perceber que o armazenamento de dados ou o Integration Runtime auto-hospedado está sobrecarregado com a carga, diminua o valor de **parallelCopies** para aliviá-la.
* Quando você copia dados de armazenamentos que não são baseados em arquivos para os armazenamentos que são, o serviço de movimentação de dados ignora a propriedade **parallelCopies** . Mesmo se o paralelismo for especificado, ele não será aplicado neste caso.
* **parallelCopies** é ortogonal para **cloudDataMovementUnits**. O primeiro é contado em todas as unidades de movimentação de dados de nuvem.

## <a name="staged-copy"></a>Cópia em etapas

Ao copiar dados de um armazenamento de dados de origem para um armazenamento de dados do coletor, você pode escolher usar um armazenamento de Blobs como um armazenamento de preparação provisório. Esse preparo é especialmente útil nos seguintes casos:

- **Você deseja ingerir dados de vários armazenamentos de dados no SQL Data Warehouse via PolyBase**. O SQL Data Warehouse usa o PolyBase como um mecanismo de alta taxa de transferência para carregar uma grande quantidade de dados no SQL Data Warehouse. No entanto, os dados de origem devem estar no armazenamento de Blobs ou no Azure Data Lake Store e devem atender a critérios adicionais. Quando você carrega dados de um armazenamento de dados diferente do armazenamento de Blobs ou do Azure Data Lake Store, pode ativar a cópia dos dados por meio do armazenamento de Blobs de preparo provisório. Nesse caso, o Data Factory executa as transformações de dados necessárias para garantir que eles atenderão aos requisitos do PolyBase. Em seguida, ele usa o PolyBase para carregar os dados no SQL Data Warehouse de forma eficaz. Para ver mais informações, confira [Usar o PolyBase para carregar dados para o SQL Data Warehouse do Azure](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Às vezes, leva algum tempo para realizar a movimentação de dados híbridos (ou seja, copiar de um armazenamento de dados local para um armazenamento de dados de nuvem) em uma conexão de rede lenta**. Para melhorar o desempenho, você pode usar a cópia em etapas para compactar os dados locais para que ela leve menos tempo para mover dados para o armazenamento de dados de preparo na nuvem e descompactar os dados no repositório de preparo antes de carregar no armazenamento de dados de destino.
- **Você não deseja abrir portas diferentes da porta 80 e da porta 443 em seu firewall, devido às políticas corporativas de TI**. Por exemplo, quando você copia dados de um armazenamento de dados local para um coletor do Banco de Dados SQL ou um coletor do SQL Data Warehouse, precisa ativar a comunicação de saída TCP na porta 1433 para o firewall do Windows e o firewall corporativo. Nesse cenário, a cópia em etapas pode tirar proveito do Integration Runtime auto-hospedado para primeiro copiar dados para uma instância de preparo de armazenamento de Blobs por HTTP ou HTTPS na porta 443, em seguida carregar os dados no Banco de Dados SQL ou no SQL Data Warehouse do Azure do preparo do armazenamento de blobs. Nesse fluxo, você não precisa habilitar a porta 1433.

### <a name="how-staged-copy-works"></a>Como funciona a cópia em etapas

Quando você ativa o recurso de preparo, primeiro os dados são copiados do armazenamento de dados de origem para o armazenamento de blobs de preparo (traga seu próprio). Em seguida, os dados são copiados do armazenamento de dados de preparo para o armazenamento de dados do coletor. O Data Factory gerencia automaticamente o fluxo de dois estágios para você. O Data Factory também limpa os dados temporários do armazenamento de preparo após a movimentação de dados ser concluída.

![Cópia em etapas](media/copy-activity-performance/staged-copy.png)

Quando você ativa o movimento de dados usando um armazenamento de preparo, pode especificar se deseja que os dados sejam compactados antes de mover os dados do armazenamento de dados de origem para um armazenamento de dados de preparo ou provisório, em seguida, descompactados antes de mover os dados de um armazenamento de preparo ou provisório para o armazenamento de dados do coletor.

Atualmente, não é possível copiar dados entre dois armazenamentos de dados locais usando um armazenamento de preparo.

### <a name="configuration"></a>Configuração

Configure a definição **enableStaging** na Atividade de Cópia para especificar se deseja que os dados sejam preparados no Armazenamento de Blobs do Azure antes de carregá-los em um armazenamento de dados de destino. Quando você definir **enableStaging** como `TRUE`, especifique as propriedades adicionais listadas na tabela a seguir. Se não tiver um, também precisará criar um Armazenamento do Azure ou um serviço vinculado de assinatura de acesso compartilhado do Armazenamento para o preparo.

| Propriedade | Descrição | Valor padrão | Obrigatório |
| --- | --- | --- | --- |
| **enableStaging** |Especifique se você deseja copiar os dados por meio de um armazenamento de preparo provisório. |Falso |Não |
| **linkedServiceName** |Especifique o nome de um serviço vinculado [AzureStorage](connector-azure-blob-storage.md#linked-service-properties), que se refere à instância do Armazenamento que você usa como um repositório de preparo provisório. <br/><br/> Você não pode usar o Armazenamento com uma assinatura de acesso compartilhado para carregar dados no SQL Data Warehouse via PolyBase. Pode usar em todos os outros cenários. |N/D |Sim, quando **enableStaging** está definido para TRUE |
| **path** |Especifique o caminho do armazenamento de Blobs que você deseja que contenha os dados preparados. Se você não fornecer um caminho, o serviço criará um contêiner para armazenar os dados temporários. <br/><br/> Especifique um caminho somente se você usar o Armazenamento com uma assinatura de acesso compartilhado ou precisar que os dados temporários fiquem em um local específico. |N/D |Não |
| **enableCompression** |Especifica se os dados devem ser compactados antes de serem copiados para o destino. Essa configuração reduz o volume de dados que são transferidos. |Falso |Não |

Aqui está um exemplo de definição da Atividade de Cópia com as propriedades descritas na tabela anterior:

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Impacto de cobrança de cópia em etapas

Você é cobrado com base em duas etapas: duração da cópia e tipo de cópia.

* Quando você usa o preparo durante uma cópia de nuvem (copiar dados de um armazenamento de dados de nuvem para outro armazenamento de dados de nuvem, ambos os estágios capacitados pelo Integration Runtime do Azure), é cobrado pela [soma da duração da cópia das etapas 1 e 2] x [preço unitário da cópia de nuvem].
* Quando você usa o preparo durante uma cópia híbrida (copiar dados de um armazenamento de dados local para um armazenamento de dados de nuvem, um estágio capacitado pelo Integration Runtime auto-Hospedado), é cobrado pela [duração da cópia híbrida] x [preço unitário da cópia híbrida] + [duração da cópia de nuvem] x [preço unitário da cópia de nuvem].

## <a name="performance-tuning-steps"></a>Etapas de ajuste do desempenho

Sugerimos que você realize estas etapas para ajustar o desempenho do serviço Data Factory com a Atividade de Cópia:

1. **Estabelecer uma linha de base**. Durante a fase de desenvolvimento, teste seu pipeline com a Atividade de Cópia em relação a um exemplo de dados representativo. Colete detalhes de execução e características de desempenho após o [monitoramento da atividade de cópia](copy-activity-overview.md#monitoring).

2. **Diagnosticar e otimizar o desempenho**. Se o desempenho observado não atender às suas expectativas, você precisará identificar os afunilamentos do desempenho. Em seguida, otimize o desempenho para remover ou reduzir o efeito dos afunilamentos. Uma descrição completa do diagnóstico de desempenho está além do escopo deste artigo, mas aqui estão algumas considerações comuns:

   * Recursos de desempenho:
     * [Cópia paralela](#parallel-copy)
     * [Unidades de movimentação de dados de nuvem](#cloud-data-movement-units)
     * [Cópia em etapas](#staged-copy)
     * [Escalabilidade do Integration Runtime auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Integration Runtime auto-hospedado](#considerations-for-self-hosted-integration-runtime)
   * [Fonte](#considerations-for-the-source)
   * [Coletor](#considerations-for-the-sink)
   * [Serialização e desserialização](#considerations-for-serialization-and-deserialization)
   * [Compactação](#considerations-for-compression)
   * [Mapeamento de coluna](#considerations-for-column-mapping)
   * [Outras considerações](#other-considerations)

3. **Expanda a configuração para todo o conjunto de dados**. Quando você estiver satisfeito com os resultados e o desempenho da execução, poderá expandir a definição e o pipeline para cobrir todo o conjunto de dados.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Considerações para o Integration Runtime auto-hospedado

Se a atividade de cópia for executada em um Integration Runtime auto-hospedado, observe o seguinte:

**Configuração**: é recomendável usar um computador dedicado para hospedar Integration Runtime. Consulte [Considerações para o uso do Integration Runtime auto-hospedado](concepts-integration-runtime.md).

**Expandir**: um único Integration Runtime auto-hospedado lógico com um ou mais nós pode atender a várias execuções da atividade de cópia ao mesmo tempo. Caso haja uma grande necessidade de movimentação de dados híbrida, seja com um grande número de execuções simultâneas da atividade de cópia, seja com um grande volume de dados a serem copiados, considere [expandir o Integration Runtime auto-hospedado](create-self-hosted-integration-runtime.md#high-availability-and-scalability) para provisionar mais recursos para capacitar a cópia.

## <a name="considerations-for-the-source"></a>Considerações para a origem

### <a name="general"></a>Geral

Verifique se o armazenamento de dados subjacente não está sobrecarregado por outras cargas de trabalho em execução nele ou em relação a ele.

Para os armazenamentos de dados da Microsoft, confira os [tópicos de monitoramento e ajuste](#performance-reference) específicos dos armazenamentos de dados que o ajudam a entender as características de desempenho do armazenamento, minimizar os tempos de resposta e maximizar a taxa de transferência.

* Se você copiar os dados **do armazenamento de Blobs para o SQL Data Warehouse**, considere o uso do **PolyBase** para melhorar o desempenho. Veja [Usar o PolyBase para carregar dados para o Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para ver mais detalhes.
* Se você copiar dados **do HDFS para o Blob do Azure/Azure Data Lake Store**, considere usar **DistCp** para melhorar o desempenho. Consulte [Usar DistCp para copiar dados do HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) para obter detalhes.
* Se você copiar dados **do Redshift para o SQL Data Warehouse do Azure/Blob do Azure/Azure Data Lake Store**, considere usar **UNLOAD** para melhorar o desempenho. Consulte [Usar UNLOAD para copiar dados do Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) para obter detalhes.

### <a name="file-based-data-stores"></a>Armazenamentos de dados baseado em arquivo

* **Média de tamanho do arquivo e contagem de arquivos**: a Atividade de Cópia transfere os dados um arquivo por vez. Com a mesma quantidade de dados a ser movidos, a taxa de transferência geral será menor se os dados consistirem em muitos arquivos pequenos, em vez de alguns arquivos grandes, devido à fase de inicialização de cada arquivo. Portanto, se possível, combine arquivos pequenos em arquivos maiores para obter uma maior taxa de transferência.
* **Formato de arquivo e compactação**: para ver outras maneiras de melhorar o desempenho, consulte as seções [Considerações da serialização e desserialização](#considerations-for-serialization-and-deserialization) e [Considerações da compactação](#considerations-for-compression).

### <a name="relational-data-stores"></a>Armazenamentos de dados relacionais

* **Padrão de dados**: o esquema da tabela afeta a taxa de transferência de cópia. Um tamanho de linha grande oferece um desempenho melhor do que o tamanho de linha pequeno para copiar a mesma quantidade de dados. O motivo é que o banco de dados pode recuperar de forma eficiente menos lotes de dados que contêm menos linhas.
* **Consulta ou procedimento armazenado**: otimize a lógica da consulta ou do procedimento armazenado especificado na origem da Atividade de Cópia para buscar os dados com mais eficiência.

## <a name="considerations-for-the-sink"></a>Considerações do coletor

### <a name="general"></a>Geral

Verifique se o armazenamento de dados subjacente não está sobrecarregado por outras cargas de trabalho em execução nele ou em relação a ele.

Para os armazenamentos de dados da Microsoft, consulte os [tópicos de monitoramento e ajuste](#performance-reference) específicos dos armazenamentos de dados. Esses tópicos o ajudarão a entender as características de desempenho do armazenamento de dados, minimizar os tempos de resposta e maximizar a taxa de transferência.

* Se você copiar os dados **do armazenamento de Blobs para o SQL Data Warehouse**, considere o uso do **PolyBase** para melhorar o desempenho. Veja [Usar o PolyBase para carregar dados para o Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para ver mais detalhes.
* Se você copiar dados **do HDFS para o Blob do Azure/Azure Data Lake Store**, considere usar **DistCp** para melhorar o desempenho. Consulte [Usar DistCp para copiar dados do HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs) para obter detalhes.
* Se você copiar dados **do Redshift para o SQL Data Warehouse do Azure/Blob do Azure/Azure Data Lake Store**, considere usar **UNLOAD** para melhorar o desempenho. Consulte [Usar UNLOAD para copiar dados do Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) para obter detalhes.

### <a name="file-based-data-stores"></a>Armazenamentos de dados baseado em arquivo

* **Comportamento da cópia**: se você copiar os dados de um armazenamento de dados diferente com base em arquivos, a Atividade de Cópia terá três opções por meio da propriedade **copyBehavior**. Preserva a hierarquia, nivela a hierarquia ou mescla os arquivos. Preservar ou nivelar a hierarquia tem pouca ou nenhuma sobrecarga de desempenho, mas mesclar os arquivos faz aumentar a sobrecarga dele.
* **Formato do arquivo e compactação**: consulte as seções [Considerações da serialização e desserialização](#considerations-for-serialization-and-deserialization) e [Considerações da compactação](#considerations-for-compression) para ver outras maneiras de melhorar o desempenho.

### <a name="relational-data-stores"></a>Armazenamentos de dados relacionais

* **Comportamento de cópia**: dependendo das propriedades configuradas para **sqlSink**, a Atividade de Cópia gravará os dados no banco de dados de destino de maneiras diferentes.
  * Por padrão, o serviço de movimentação de dados usa a API de Cópia em Massa para inserir dados no modo de anexação, o que fornece o melhor desempenho.
  * Se você configurar um procedimento armazenado no coletor, o banco de dados aplicará os dados uma linha por vez, em vez de um carregamento em massa. O desempenho cai significativamente. Se o conjunto de dados for grande, quando aplicável, considere trocar para usar a propriedade **preCopyScript**.
  * Se você configurar a propriedade **preCopyScript** para cada execução da atividade de cópia, o serviço disparará o script, então, usará a API de Cópia em Massa para inserir os dados. Por exemplo, para substituir a tabela inteira pelos dados mais recentes, você pode especificar um script para excluir primeiro todos os registros, antes de carregar em massa os novos dados da origem.
* **Padrão de dados e tamanho do lote**:
  * O esquema da tabela afeta a taxa de transferência da cópia. Para copiar a mesma quantidade de dados, um tamanho de linha grande fornece um desempenho melhor do que um tamanho de linha pequeno, pois o banco de dados poderá confirmar com mais eficiência menos lotes de dados.
  * A Atividade de Cópia insere dados em uma série de lotes. Você pode definir o número de linhas em um lote usando a propriedade **writeBatchSize** . Se os dados tiverem linhas pequenas, você poderá definir a propriedade **writeBatchSize** com um valor mais alto para aproveitar uma sobrecarga de lote menor e uma taxa de transferência maior. Se o tamanho da linha de dados for grande, tenha cuidado ao aumentar **writeBatchSize**. Um valor alto pode levar a uma falha de cópia causada pela sobrecarga do banco de dados.

### <a name="nosql-stores"></a>Repositórios NoSQL

* Para o **Armazenamento de Tabelas**:
  * **Partição**: gravar os dados em partições intercaladas diminui drasticamente o desempenho. Classifique os dados de origem por chave de partição para que os dados sejam inseridos com eficiência em partições sucessivas ou ajuste a lógica para gravar os dados em uma única partição.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerações da serialização e desserialização

A serialização e desserialização podem acontecer quando o conjunto de dados de entrada ou saída é um arquivo. Consulte [Formatos de arquivo e compactação com suporte](supported-file-formats-and-compression-codecs.md) para obter detalhes sobre os formatos de arquivo com suporte na Atividade de Cópia.

**Comportamento da cópia**:

* Copiar arquivos entre os armazenamentos de dados baseados em arquivos:
  * Quando os conjuntos de dados de entrada e saída têm a mesma ou nenhuma configuração do formato de arquivo, o serviço de movimentação de dados executa uma **cópia binária** sem nenhuma serialização ou desserialização. Você vê uma maior taxa de transferência em comparação com o cenário no qual as configurações do formato de arquivo de origem e do coletor são diferentes entre si.
  * Quando os conjuntos de dados de entrada e saída estão no formato de texto e apenas o tipo de codificação é diferente, o serviço de movimentação de dados apenas faz a conversão da codificação. Ele não faz nenhuma serialização e desserialização, causando uma sobrecarga do desempenho em comparação com uma cópia binária.
  * Quando os conjuntos de dados de entrada e saída têm formatos de arquivo diferentes ou configurações diferentes, como delimitadores, o serviço de movimentação de dados desserializa os dados de origem para transmitir, transformar, então, serializa no formato de saída desejado. Essa operação resulta em uma sobrecarga do desempenho mais significativa em comparação com outros cenários.
* Quando você copia os arquivos de/para um armazenamento de dados que não é baseado em arquivos (por exemplo, de um armazenamento baseado em arquivos para um armazenamento relacional), a etapa da serialização ou desserialização é necessária. Essa etapa resulta em uma sobrecarga significativa do desempenho.

**Formato de arquivo**: O formato de arquivo escolhido pode afetar o desempenho da cópia. Por exemplo, Avro é um formato binário compacto que armazena metadados com dados. Ele tem um amplo suporte no ecossistema do Hadoop para processar e consultar. No entanto, o Avro é mais caro para a serialização e a desserialização, o que resulta em uma menor taxa de transferência de cópia em comparação com o formato de texto. Faça a escolha do formato de arquivo em todo o fluxo de processamento de forma global. Inicie com qual forma os dados são armazenados, os armazenamentos dos dados de origem ou a extração dos sistemas externos; o melhor formato para o armazenamento, processamento analítico e consulta; e em qual formato os dados devem ser exportados para os data marts para as ferramentas de relatório e visualização. Às vezes, um formato de arquivo abaixo do ideal para o desempenho de leitura e gravação pode ser uma boa escolha ao considerar o processo analítico geral.

## <a name="considerations-for-compression"></a>Considerações da compactação

Quando o conjunto de dados de entrada ou saída é um arquivo, você pode definir a Atividade de Cópia para executar a compactação ou a descompactação conforme grava os dados no destino. Quando você escolher a compactação, faça uma compensação entre a entrada/saída (E/S) e a CPU. Compactar os dados tem um custo extra nos recursos de computação. Mas, por sua vez, reduz a E/S da rede e o armazenamento. Dependendo de seus dados, você poderá ver um aumento na taxa de transferência geral da cópia.

**Codec**: cada codec de compactação tem suas vantagens. Por exemplo, o bzip2 tem a menor taxa de transferência de cópia, mas você obtém o melhor desempenho de consulta do Hive com o bzip2 porque pode dividi-lo para o processamento. O gzip é a opção mais equilibrada e é usado com mais frequência. Escolha o codec mais adequado para seu cenário completo.

**Nível**: você pode escolher entre duas opções para cada codec de compactação: compactado mais rápido e compactado ideal. A opção compactada mais rápida compacta os dados o mais rapidamente possível, mesmo que o arquivo resultante não tenha uma compactação ideal. A opção de compactação ideal leva mais tempo na compactação e produz uma quantidade mínima de dados. Você pode testar as duas opções para qual fornece o melhor desempenho no seu caso.

**Uma consideração**: para copiar uma grande quantidade de dados entre um repositório local e a nuvem, considere usar a [Cópia em etapas](#staged-copy) com a compactação habilitada. Usar o armazenamento provisório é útil quando a largura de banda da rede corporativa e os serviços do Azure é o fator limitante e você deseja que os conjuntos de dados de entrada e saída estejam descompactados.

## <a name="considerations-for-column-mapping"></a>Considerações do mapeamento de coluna

Você pode definir a propriedade **columnMappings** na Atividade de Cópia para mapear tudo ou um subconjunto de colunas de entrada para as colunas de saída. Depois do serviço de movimentação de dados ler os dados de origem, ele precisa executar o mapeamento de coluna nos dados antes de gravar os dados no coletor. Esse processamento extra reduz a taxa de transferência de cópia.

Se o armazenamento de dados de origem for de consulta, por exemplo, for um armazenamento relacional como o Banco de Dados SQL ou o SQL Server, ou se for um armazenamento NoSQL, como o Armazenamento de tabelas ou o Azure Cosmos DB, considere enviar a filtragem da coluna por push e reordenar a lógica para a propriedade **query**, em vez de usar o mapeamento de coluna. Dessa forma, a projeção ocorrerá enquanto o serviço de movimentação de dados lê os dados no armazenamento de dados de origem, onde é muito mais eficiente.

Saiba mais no [Mapeamento de esquema de atividade de cópia](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Outras considerações

Se o tamanho dos dados que deseja copiar for grande, você pode ajustar sua lógica de negócios para particionar ainda mais os dados e agendar a execução da atividade de cópia com mais frequência para reduzir o tamanho dos dados para cada execução da atividade de cópia.

Tenha cuidado com o número de conjuntos de dados e atividades de cópia que requerem o Data Factory para conectar o mesmo armazenamento de dados ao mesmo tempo. Vários trabalhos de cópia simultâneos pode restringir um armazenamento de dados e levar a um desempenho reduzido, repetições internas do trabalho de cópia e, em alguns casos, falhas de execução.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Cenário de exemplo: copiar de um SQL Server local para o armazenamento de Blobs

**Cenário:**um pipeline é criado para copiar os dados de um SQL Server local para um armazenamento de Blobs no formato CSV. Para acelerar o trabalho de cópia, os arquivos CSV devem ser compactados no formato bzip2.

**Análise e teste**: A taxa de transferência da Atividade de Cópia é menor que 2 MBps, que é muito mais lento do que o parâmetro de comparação de desempenho.

**Análise e ajuste do desempenho:**para solucionar o problema de desempenho, vejamos como os dados são processados e movidos.

1. **Ler dados**: o Integration Runtime abre uma conexão com o SQL Server e envia a consulta. SQL Server responde enviando o fluxo de dados para o Integration Runtime por meio da intranet.
2. **Serializar e compactar dados**: o Integration Runtime serializa o fluxo de dados para o formato CSV e compacta os dados em um fluxo bzip2.
3. **Gravar dados**: o Integration Runtime carrega o fluxo bzip2 no armazenamento de Blobs via Internet.

Como você pode ver, os dados estão sendo processados e movidos de forma sequencial e em fluxo: SQL Server > LAN > Integration Runtime > WAN > armazenamento de Blobs. **O desempenho geral é limitado pela taxa de transferência mínima no pipeline**.

![Fluxo de dados](./media/copy-activity-performance/case-study-pic-1.png)

Um ou mais dos seguintes fatores pode causar o afunilamento do desempenho:

* **Origem:**o próprio SQL Server tem uma baixa taxa de transferência devido às cargas pesadas.
* **Integration Runtime auto-hospedado**:
  * **LAN**: o Integration Runtime está localizado longe do computador do SQL Server e tem uma conexão de baixa largura de banda.
  * **Integration Runtime**: o Integration Runtime atingiu suas limitações de carga para executar as seguintes operações:
    * **Serialização**: serializar o fluxo de dados para o formato CSV tem a taxa de transferência lenta.
    * **Compactação**: você escolheu um codec de compactação lenta (por exemplo, bzip2, que é 2.8 MBps com Core i7).
  * **WAN**: a largura de banda entre a rede corporativa e os serviços do Azure é baixa (por exemplo, T1 = 1,544 kbps; T2 = 6,312 kbps).
* **Coletor**: o armazenamento de Blobs tem baixa taxa de transferência. (Esse cenário é improvável porque seu SLA garante um mínimo de 60 MBps.)

Nesse caso, a compactação de dados bzip2 pode estar desacelerando todo o pipeline. Trocar para o codec de compactação gzip pode aliviar esse afunilamento.

## <a name="reference"></a>Referência

Aqui estão as referências de monitoramento e ajuste do desempenho para alguns dos armazenamentos de dados com suporte:

* Armazenamento do Azure (incluindo o armazenamento de Blobs e o armazenamento de Tabelas): [metas de escalabilidade do Armazenamento do Azure](../storage/common/storage-scalability-targets.md) e [Lista de verificação de escalabilidade e desempenho do Armazenamento do Azure](../storage/common/storage-performance-checklist.md)
* Banco de dados SQL do Azure: é possível [monitorar o desempenho](../sql-database/sql-database-single-database-monitor.md) e verificar o percentual DTU (unidade de transação do banco de dados)
* SQL Data Warehouse do Azure: Sua capacidade é medida em unidades de data warehouse (DWUs); consulte [Gerenciar poder de computação no SQL Data Warehouse do Azure (Visão Geral)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [níveis de desempenho no Azure Cosmos DB](../cosmos-db/performance-levels.md)
* SQL Server local: [monitoramento e ajuste do desempenho](https://msdn.microsoft.com/library/ms189081.aspx)
* Servidor de arquivos local: [ajuste de desempenho para servidores de arquivos](https://msdn.microsoft.com/library/dn567661.aspx)

## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Mapeamento de esquema de atividade de cópia](copy-activity-schema-and-type-mapping.md)
- [Tolerância a falhas da atividade de cópia](copy-activity-fault-tolerance.md)
