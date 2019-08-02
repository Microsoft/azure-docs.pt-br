---
title: Copie o guia de ajuste e desempenho da atividade no Azure Data Factory | Microsoft Docs
description: Saiba mais sobre os principais fatores que afetam o desempenho da movimentação de dados no Azure Data Factory quando você usa a atividade de cópia.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: face3719f32ccb44e7479150e94417496141f90b
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509569"
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Guia ajuste e desempenho da atividade de cópia
> [!div class="op_single_selector" title1="Selecione a versão do Azure Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-copy-activity-performance.md)
> * [Versão atual](copy-activity-performance.md)


A atividade de cópia do Azure Data Factory fornece uma primeira classe de dados seguro, confiável e de alto desempenho ao carregar a solução. Você pode usá-lo a copie dezenas de terabytes de dados diariamente em uma grande variedade de armazenamentos de dados de nuvem e locais. Desempenho de carregamento rápido de dados é fundamental para garantir que você possa se concentrar no problema de big data core: desenvolvimento de soluções de análise avançada e obtenção de informações aprofundadas de todos esses dados.

O Azure fornece soluções de warehouse de dados e armazenamento de dados de um conjunto de nível empresarial. A atividade de cópia oferece uma experiência que é fácil de configurar e de carregamento de dados altamente otimizados. Com uma atividade de cópia única, você pode carregar dados:

* Azure SQL Data Warehouse a 1,2 GBps.
* Armazenamento de BLOBs do Azure em 1,0 GBps.
* Azure Data Lake Store, a 1,0 GBps.

Este artigo descreve:

* [Números de referência de desempenho](#performance-reference) para suporte para armazenamentos de dados de origem e coletor para ajudá-lo a planejar seu projeto.
* Recursos que podem aumentar a taxa de transferência de cópia em cenários diferentes, que inclui [unidades de dados de integração](#data-integration-units) (DIUs) [cópia paralela](#parallel-copy), e [cópia em etapas](#staged-copy).
* [Diretrizes de ajuste de desempenho](#performance-tuning-steps) sobre como ajustar o desempenho e os principais fatores que podem afetar o desempenho da cópia.

> [!NOTE]
> Se você não estiver familiarizado com a atividade de cópia em geral, consulte o [visão geral da atividade de cópia](copy-activity-overview.md) antes de ler este artigo.
>

## <a name="performance-reference"></a>Referência de desempenho

Como referência, a tabela a seguir mostra o número de taxa de transferência de cópia em MBps para a origem especificada e pares de coletor na execução de uma atividade de cópia única com base em testes internos. Para comparação, ele também demonstra como as diferentes configurações de [unidades de dados de integração](#data-integration-units) ou [escalabilidade do tempo de execução de integração auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime) (vários nós) podem ajudar no desempenho da cópia.

![Matriz de desempenho](./media/copy-activity-performance/CopyPerfRef.png)

> [!IMPORTANT]
> Quando a atividade de cópia é executada em um tempo de execução de integração do Azure, o mínimo permitido integração unidades de dados de (anteriormente conhecido como unidades de movimentação de dados) é dois. Se não for especificado, consulte as unidades de integração de dados padrão que está sendo usado na [unidades de dados de integração](#data-integration-units).

**Pontos a serem observados:**

* Taxa de transferência é calculada usando a fórmula a seguir: [tamanho dos dados lidos na origem] / [duração da execução de atividade de cópia].
* Os números de referência de desempenho na tabela foram medidos usando um [TPC-H](http://www.tpc.org/tpch/) conjunto de dados em uma execução de atividade de cópia única. Arquivos de teste para repositórios com base em arquivo são vários arquivos com 10 GB de tamanho.
* Nos armazenamentos de dados do Azure, a origem e o coletor estão na mesma região do Azure.
* Para a cópia híbrida entre local e na nuvem armazenamentos de dados, cada nó de tempo de execução de integração auto-hospedado estava em execução em um computador separado do armazenamento de dados com a seguinte especificação. Quando uma única atividade foi executada, a operação de cópia consumiu apenas uma pequena parte da CPU, da memória ou da largura de banda da rede do computador de teste.
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
        <td>Interface de Internet: 10 Gbps; interface de Intranet: 40 Gbps</td>
    </tr>
    </table>


> [!TIP]
> Você pode alcançar maior taxa de transferência por meio de DIUs mais. Por exemplo, com 100 DIUs, você pode copiar dados do armazenamento de BLOBs do Azure no Azure Data Lake Store em 1,0 GBps. Para obter mais informações sobre esse recurso e o cenário com suporte, consulte o [unidades de dados de integração](#data-integration-units) seção. 

## <a name="data-integration-units"></a>Unidades de integração de dados

Uma unidade de integração de dados é uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos de rede) de uma unidade única no Azure Data Factory. Unidade de integração de dados só se aplica aos [tempo de execução de integração do Azure](concepts-integration-runtime.md#azure-integration-runtime), mas não [integration runtime auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime).

Os mínimo DIUs para capacitar a execução de uma atividade de cópia é dois. Se ele não for especificado, a seguinte tabela listará as DIUs padrão usadas em diferentes cenários de cópia:

| Copiar cenário | DIUs padrão determinadas pelo serviço |
|:--- |:--- |
| Copiar dados entre repositórios baseados em arquivo | Entre 4 e 32, dependendo do número e tamanho dos arquivos |
| Copiar dados para o banco de dados SQL ou o Azure Cosmos DB |Entre 4 e 16 dependendo do coletor de camada do banco de dados SQL ou do Cosmos DB (número de DTUs/RUs) |
| Todos os outros cenários de cópia | 4 |

Para substituir esse padrão, especifique um valor para a propriedade **dataIntegrationUnits** da seguinte maneira. O *valores permitidos* para o **dataIntegrationUnits** propriedade é até 256. O *número real de DIUs* que a operação de cópia usa na execução é igual ou menor que o valor configurado, dependendo do seu padrão de dados. Para obter informações sobre o nível de ganho de desempenho que você pode obter ao configurar mais unidades para uma origem e coletor de cópia específicos, consulte a [referência de desempenho](#performance-reference).

Você pode ver os DIUs usados para cada execução na saída de atividade de cópia ao monitorar uma execução de atividade. Para obter mais informações, consulte [monitoramento da atividade de cópia](copy-activity-overview.md#monitoring).

> [!NOTE]
> Configuração de DIUs maior do que quatro atualmente se aplica somente quando você copia vários arquivos do armazenamento do Azure, armazenamento do Azure Data Lake, Amazon S3, Google Cloud Storage, nuvem FTP ou SFTP na nuvem para outros armazenamentos de dados de nuvem.
>

**Exemplo**

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
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="data-integration-units-billing-impact"></a>Impacto da cobrança de Unidades de Integração de Dados

Lembre-se de que você é cobrado com base no tempo total da operação de cópia. A duração total dados que será cobrada movimentação de dados é a soma da duração de DIUs. Se um trabalho de cópia costumava levar uma hora com duas unidades de nuvem e agora leva 15 minutos com oito unidades de nuvem, a fatura geral fica quase igual.

## <a name="parallel-copy"></a>Cópia paralela

Você pode usar o **parallelCopies** propriedade para indicar o paralelismo que você deseja usar a atividade de cópia. Você pode pensar essa propriedade como o número máximo de threads dentro da atividade de cópia que pode ler sua fonte ou gravar em seus repositórios de dados em paralelo.

Para cada execução de atividade de cópia, o Azure Data Factory determina o número de cópias paralelas a usar para copiar dados da fonte de armazenamento de dados e para os dados de destino do repositório. O número padrão de cópias paralelas usadas depende do tipo de fonte e coletor que você usa.

| Copiar cenário | Contagem de cópia paralela padrão determinada pelo serviço |
| --- | --- |
| Copiar dados entre repositórios baseados em arquivo |Depende do tamanho dos arquivos e o número de DIUs usadas para copiar dados entre dois armazenamentos de dados de nuvem ou da configuração física da máquina de tempo de execução de integração auto-hospedado. |
| Copiar dados de qualquer armazenamento de origem para o armazenamento de tabelas do Azure |4 |
| Todos os outros cenários de cópia |1 |

> [!TIP]
> Quando você copia dados entre repositórios baseados em arquivo, o comportamento padrão normalmente fornece melhor taxa de transferência. O comportamento padrão é determinado automaticamente com base no seu padrão de arquivo de origem.

Para controlar a carga em computadores que hospedam seus dados de armazenamentos de ou para ajustar o desempenho da cópia, você pode substituir o valor padrão e especificar um valor para o **parallelCopies** propriedade. O valor deve ser um inteiro maior ou igual a 1. Em tempo de execução para o melhor desempenho, a atividade de cópia usa um valor que é menor que ou igual ao valor que você definir.

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

**Pontos a serem observados:**

* Quando você copia dados entre repositórios baseados em arquivo, **parallelCopies** determina o paralelismo no nível de arquivo. O agrupamento em um único arquivo acontece nos bastidores, automática e transparente. Ele foi projetado para usar a melhor parte adequada tamanho para um tipo de repositório de dados de origem especificado carregar dados em paralelo e ortogonal para **parallelCopies**. O número real de cópias paralelas que o serviço de movimentação de dados usa para a operação de cópia no tempo de execução não é superior ao número de arquivos existentes. Se o comportamento de cópia **mergeFile**, a atividade de cópia não pode tirar proveito do paralelismo em nível de arquivo.
* Quando você copia dados de armazenamentos que não são baseados em arquivo (exceto o banco de dados Oracle como fonte com o particionamento de dados habilitado) para as lojas que são baseados em arquivo, o serviço de movimentação de dados ignora a **parallelCopies** propriedade. Mesmo se o paralelismo for especificado, ele não será aplicado neste caso.
* O **parallelCopies** propriedade é ortogonal ao **dataIntegrationUnits**. O primeiro é contado em todas as unidades de integração de dados.
* Quando você especifica um valor para o **parallelCopies** propriedade, considere o aumento de carga em sua fonte e armazenamentos de dados de coletor. Além disso, considere o aumento de carga para o tempo de execução de integração auto-hospedado se a atividade de cópia for capacitada por ele, por exemplo, para a cópia híbrida. Esse aumento de carga ocorre especialmente quando você tiver várias atividades ou execuções simultâneas das mesmas atividades que são executados no mesmo repositório de dados. Se você perceber que o armazenamento de dados ou o tempo de execução de integração auto-hospedado está sobrecarregado com a carga, diminua a **parallelCopies** valor para aliviar a carga.

## <a name="staged-copy"></a>Cópia em etapas

Ao copiar dados de um armazenamento de dados de origem para um armazenamento de dados do coletor, você pode escolher usar um armazenamento de Blobs como um armazenamento de preparação provisório. Esse preparo é especialmente útil nos seguintes casos:

- **Você deseja incluir dados de vários armazenamentos de dados no SQL Data Warehouse via PolyBase.** O SQL Data Warehouse usa o PolyBase como um mecanismo de alta taxa de transferência para carregar uma grande quantidade de dados no SQL Data Warehouse. Os dados de origem devem estar no armazenamento de BLOBs ou Azure Data Lake Store, e ela deve atender aos critérios adicionais. Quando você carrega dados de um armazenamento de dados diferente do armazenamento de Blobs ou do Azure Data Lake Store, pode ativar a cópia dos dados por meio do armazenamento de Blobs de preparo provisório. Nesse caso, o Azure Data Factory executa as transformações de dados necessários para garantir que ele cumpra os requisitos do PolyBase. Em seguida, ele usa o PolyBase para carregar os dados no SQL Data Warehouse de forma eficaz. Para ver mais informações, confira [Usar o PolyBase para carregar dados para o SQL Data Warehouse do Azure](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
- **Às vezes, leva algum tempo para realizar a movimentação de dados híbridos (ou seja, para copiar de um local do armazenamento de dados para um armazenamento de dados de nuvem) ao longo de uma conexão de rede lenta.** Para melhorar o desempenho, você pode usar a cópia em etapas para compactar os dados locais para que ele leva menos tempo para mover dados para o armazenamento de dados de preparo na nuvem. Em seguida, você pode descompactar os dados no armazenamento de preparo antes de carregar no armazenamento de dados de destino.
- **Você não quiser abrir portas diferentes da porta 80 e 443 em seu firewall devido a políticas corporativas de TI.** Por exemplo, quando você copia dados de um armazenamento de dados local para um coletor do Banco de Dados SQL do Azureou um coletor do SQL Data Warehouse, precisa ativar a comunicação de saída TCP na porta 1433 para o firewall do Windows e o firewall corporativo. Nesse cenário, cópia em etapas pode tirar proveito do tempo de execução de integração auto-hospedado para primeiro copiar dados para um armazenamento de Blob de preparo instância via HTTP ou HTTPS na porta 443. Em seguida, ele pode carregar os dados no banco de dados SQL ou SQL Data Warehouse de preparo do armazenamento de Blob. Nesse fluxo, você não precisa habilitar a porta 1433.

### <a name="how-staged-copy-works"></a>Como funciona a cópia em etapas

Quando você ativa o recurso de preparo, primeiro os dados são copiados do armazenamento de dados de origem para o armazenamento de blobs de preparo (traga seu próprio). Em seguida, os dados são copiados do armazenamento de dados de preparo para o armazenamento de dados do coletor. O Azure Data Factory gerencia automaticamente o fluxo de dois estágios para você. O Azure Data Factory também limpa os dados temporários do armazenamento de preparo após a movimentação de dados é concluída.

![Cópia em etapas](media/copy-activity-performance/staged-copy.png)

Quando você ativa a movimentação de dados usando um armazenamento de preparo, você pode especificar se deseja no repositório de dados a serem compactados antes de mover dados dos dados de origem para um provisório ou armazenamento de dados de preparo e, em seguida, descompactados antes de mover dados de um preparo ou provisório dat um repositório para o armazenamento de dados de coletor.

Atualmente, é possível copiar dados entre dois armazenamentos de dados que estão conectados por meio de diferentes IRs auto-hospedado, nem com sem cópia em etapas. Para esse cenário, você pode configurar duas atividades de cópia explicitamente encadeadas para copiar da fonte para o preparo e de preparo para o coletor.

### <a name="configuration"></a>Configuração

Configurar o **enableStaging** definindo na atividade de cópia para especificar se deseja que os dados sejam preparados no armazenamento de BLOBs antes de carregá-lo em um armazenamento de dados de destino. Quando você define **enableStaging** para `TRUE`, especifique as propriedades adicionais listadas na tabela a seguir. Você também precisará criar um armazenamento do Azure ou armazenamento compartilhado de serviço vinculado de assinatura de acesso para preparo se você não tiver uma.

| Propriedade | Descrição | Valor padrão | Obrigatório |
| --- | --- | --- | --- |
| enableStaging |Especifique se você deseja copiar os dados por meio de um armazenamento de preparo provisório. |Falso |Não |
| linkedServiceName |Especifique o nome de um serviço vinculado [AzureStorage](connector-azure-blob-storage.md#linked-service-properties), que se refere à instância do Armazenamento que você usa como um repositório de preparo provisório. <br/><br/> Você não pode usar o armazenamento com uma assinatura de acesso compartilhado para carregar dados no SQL Data Warehouse via PolyBase. Pode usar em todos os outros cenários. |N/D |Sim, quando **enableStaging** está definido para TRUE |
| caminho |Especifique o caminho do armazenamento de Blobs que você deseja que contenha os dados preparados. Se você não fornecer um caminho, o serviço cria um contêiner para armazenar dados temporários. <br/><br/> Especifique um caminho somente se você usar o Armazenamento com uma assinatura de acesso compartilhado ou precisar que os dados temporários fiquem em um local específico. |N/D |Não |
| enableCompression |Especifica se os dados devem ser compactados antes de serem copiado para o destino. Essa configuração reduz o volume de dados que são transferidos. |Falso |Não |

>[!NOTE]
> Se você usar a cópia em etapas com compactação habilitada, a entidade de serviço ou não há suporte para autenticação do MSI para o serviço vinculado de blob de preparo.

Aqui está um exemplo de definição de uma atividade de cópia com as propriedades que são descritos na tabela anterior:

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

Você será cobrado com base em duas etapas: duração da cópia e tipo de cópia.

* Quando você usa o preparo durante uma cópia de nuvem, que está copiando dados de um armazenamento de dados de nuvem para outro armazenamento de dados de nuvem, ambos os estágios capacitados pelo tempo de execução de integração do Azure, você será cobrado pela [soma da duração da cópia das etapas 1 e 2] x [preço unitário da cópia nuvem].
* Quando você usa o preparo durante uma cópia híbrida, que está copiando dados de um armazenamento de dados local para um armazenamento de dados de nuvem, um estágio capacitados por um tempo de execução de integração auto-hospedado, você será cobrado pela [duração da cópia híbrida] x [preço unitário da cópia híbrida] + [duração da cópia de nuvem] x [preço unitário da cópia nuvem].

## <a name="performance-tuning-steps"></a>Etapas de ajuste do desempenho

Siga estas etapas para ajustar o desempenho do seu serviço Azure Data Factory com a atividade de cópia.

1. **Estabelece uma linha de base.** Durante a fase de desenvolvimento, teste seu pipeline usando a atividade de cópia em relação a uma amostra representativa de dados. Coletar detalhes de execução e características de desempenho a seguir [monitoramento da atividade de cópia](copy-activity-overview.md#monitoring).

2. **Diagnosticar e otimizar o desempenho.** Se o desempenho observado não atender às suas expectativas, identificar gargalos de desempenho. Em seguida, otimize o desempenho para remover ou reduzir o efeito dos gargalos.

    Em alguns casos, quando você executa uma atividade de cópia no Azure Data Factory, você verá uma mensagem "Dicas de ajuste de desempenho" na parte superior dos [página de monitoramento de atividade de cópia](copy-activity-overview.md#monitor-visually), conforme mostrado no exemplo a seguir. A mensagem informa o gargalo que foi identificado para a execução de cópia determinada. Ele também orienta você sobre o que alterar a taxa de transferência de cópia de aumento. As dicas de ajuste de desempenho atualmente fornecem sugestões, como:

    - Use o PolyBase ao copiar dados para o Azure SQL Data Warehouse.
    - Aumente as unidades de solicitação do Azure Cosmos DB ou DTUs de banco de dados SQL do Azure (unidades de taxa de transferência de banco de dados) quando o recurso no lado do repositório de dados é o gargalo.
    - Remova a cópia em etapas desnecessária.

    As regras de ajuste de desempenho serão aprimoradas gradualmente também.

    **Exemplo: Copie no banco de dados SQL com dicas de ajuste de desempenho**

    Neste exemplo, durante uma cópia de executar, Azure Data Factory observa o coletor do que banco de dados SQL atinge a alta utilização de DTU, o que diminui as operações de gravação. A sugestão é aumentar a camada de banco de dados SQL com mais DTUs. 

    ![Monitoramento de cópia com dicas de ajuste de desempenho](./media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

    Além disso, a seguir estão algumas considerações comuns. Uma descrição completa do diagnóstico de desempenho está além do escopo deste artigo.

   * Recursos de desempenho:
     * [Cópia paralela](#parallel-copy)
     * [Unidades de Integração de Dados](#data-integration-units)
     * [Cópia em etapas](#staged-copy)
     * [Escalabilidade do tempo de execução de integração auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime)
   * [Integration Runtime auto-hospedado](#considerations-for-self-hosted-integration-runtime)
   * [Fonte](#considerations-for-the-source)
   * [Coletor](#considerations-for-the-sink)
   * [Serialização e desserialização](#considerations-for-serialization-and-deserialization)
   * [Compactação](#considerations-for-compression)
   * [Mapeamento de coluna](#considerations-for-column-mapping)
   * [Outras considerações](#other-considerations)

3. **Expanda a configuração para todo o conjunto de dados.** Quando estiver satisfeito com os resultados da execução e o desempenho, você pode expandir a definição e o pipeline para cobrir todo o conjunto de dados.

## <a name="considerations-for-self-hosted-integration-runtime"></a>Considerações para o integration runtime auto-hospedado

Se sua atividade de cópia for executado em um tempo de execução de integração auto-hospedado, observe o seguinte:

**Configuração**: É recomendável que você use um computador dedicado para o tempo de execução de integração de host. Ver [considerações para usar o integration runtime auto-hospedado](concepts-integration-runtime.md).

**Expandir**: Um tempo de execução única de integração auto-hospedado lógico com um ou mais nós pode servir a várias execuções de atividade de cópia ao mesmo tempo. Se você tiver uma grande necessidade em movimentação de dados híbridos com um grande número de execuções de atividade de cópia simultânea ou com um grande volume de dados a serem copiados, considere [expandindo o tempo de execução de integração auto-hospedado](create-self-hosted-integration-runtime.md#high-availability-and-scalability) para provisionar mais recursos para Capacite a cópia.

## <a name="considerations-for-the-source"></a>Considerações para a origem

### <a name="general"></a>Geral

Certifique-se de que o armazenamento de dados subjacente não está sobrecarregado por outras cargas de trabalho que estão executando em ou em relação a ela.

Para armazenamentos de dados da Microsoft, consulte [monitoramento e ajuste tópicos](#performance-reference) que são específicos para armazenamentos de dados. Esses tópicos o ajudarão a entender as características de desempenho do armazenamento de dados, minimizar os tempos de resposta e maximizar a taxa de transferência.

* Se você copiar dados do armazenamento de Blob ao SQL Data Warehouse, considere usar o PolyBase para melhorar o desempenho. Para ver mais informações, confira [Usar o PolyBase para carregar dados para o SQL Data Warehouse do Azure](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Se você copiar dados do HDFS para o armazenamento de BLOBs do Azure ou Azure Data Lake Store, considere usar o DistCp para melhorar o desempenho. Para obter mais informações, consulte [usar DistCp para copiar dados do HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Se você copiar dados do Redshift para o Azure SQL Data Warehouse, o armazenamento de BLOBs do Azure ou Azure Data Lake Store, considere usar UNLOAD para melhorar o desempenho. Para obter mais informações, consulte [usar UNLOAD para copiar dados do Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Armazenamentos de dados baseado em arquivo

* **Tamanho médio de arquivo e contagem de arquivos**: A atividade de cópia transfere um arquivo de dados por vez. Com a mesma quantidade de dados a ser movidos, a taxa de transferência geral será menor se os dados consistirem em muitos arquivos pequenos, em vez de alguns arquivos grandes, devido à fase de inicialização de cada arquivo. Se possível, combine arquivos pequenos em arquivos maiores para obter maior taxa de transferência.
* **Formato e compactação de arquivo**: para ver outras maneiras de melhorar o desempenho, consulte as seções [Considerações sobre serialização e desserialização](#considerations-for-serialization-and-deserialization) e [Considerações sobre compactação](#considerations-for-compression).

### <a name="relational-data-stores"></a>Armazenamentos de dados relacionais

* **Padrão de dados**: O esquema da tabela afeta a taxa de transferência da cópia. Um tamanho de linha grande oferece desempenho melhor do que um tamanho de linha pequeno para copiar a mesma quantidade de dados. O motivo é que o banco de dados pode recuperar de forma eficiente menos lotes de dados que contêm menos linhas.
* **Consulta ou procedimento armazenado**: Otimize a lógica da consulta ou procedimento armazenado que você especificar na fonte da atividade de cópia para buscar dados com mais eficiência.

## <a name="considerations-for-the-sink"></a>Considerações do coletor

### <a name="general"></a>Geral

Certifique-se de que o armazenamento de dados subjacente não está sobrecarregado por outras cargas de trabalho que estão executando em ou em relação a ela.

Para armazenamentos de dados da Microsoft, consulte [monitoramento e ajuste tópicos](#performance-reference) que são específicos para armazenamentos de dados. Esses tópicos o ajudarão a entender as características de desempenho do armazenamento de dados, minimizar os tempos de resposta e maximizar a taxa de transferência.

* Se você copiar dados de qualquer armazenamento de dados para o Azure SQL Data Warehouse, considere usar o PolyBase para melhorar o desempenho. Para ver mais informações, confira [Usar o PolyBase para carregar dados para o SQL Data Warehouse do Azure](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse).
* Se você copiar dados do HDFS para o armazenamento de BLOBs do Azure ou Azure Data Lake Store, considere usar o DistCp para melhorar o desempenho. Para obter mais informações, consulte [usar DistCp para copiar dados do HDFS](connector-hdfs.md#use-distcp-to-copy-data-from-hdfs).
* Se você copiar dados do Redshift para o Azure SQL Data Warehouse, o armazenamento de BLOBs do Azure ou Azure Data Lake Store, considere usar UNLOAD para melhorar o desempenho. Para obter mais informações, consulte [usar UNLOAD para copiar dados do Amazon Redshift](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift).

### <a name="file-based-data-stores"></a>Armazenamentos de dados baseado em arquivo

* **Comportamento da cópia**: Se você copiar dados de um armazenamento de dados diferentes com base em arquivo, a atividade de cópia tem três opções por meio de **copyBehavior** propriedade. Preserva a hierarquia, nivela a hierarquia ou mescla os arquivos. Preservar ou nivelar a hierarquia tem pouca ou nenhuma sobrecarga de desempenho, mas mesclar os arquivos faz aumentar a sobrecarga dele.
* **Formato e compactação de arquivo**: para ver outras maneiras de melhorar o desempenho, consulte as seções [Considerações sobre serialização e desserialização](#considerations-for-serialization-and-deserialization) e [Considerações sobre compactação](#considerations-for-compression).

### <a name="relational-data-stores"></a>Armazenamentos de dados relacionais

* **Copie a implicação de desempenho e comportamento de**: Há diferentes maneiras de gravar dados em um coletor do SQL. Saiba mais sobre [práticas recomendadas para carregar dados no banco de dados SQL](connector-azure-sql-database.md#best-practice-for-loading-data-into-azure-sql-database).

* **Padrão de dados e tamanho do lote**:
  * O esquema da tabela afeta a taxa de transferência da cópia. Para copiar a mesma quantidade de dados, um tamanho de linha grande fornece um desempenho melhor do que um tamanho de linha pequeno, pois o banco de dados poderá confirmar com mais eficiência menos lotes de dados.
  * A atividade de cópia insere dados em uma série de lotes. Você pode definir o número de linhas em um lote usando a propriedade **writeBatchSize** . Se os dados tiverem linhas pequenas, você poderá definir a propriedade **writeBatchSize** com um valor mais alto para aproveitar uma sobrecarga de lote menor e uma taxa de transferência maior. Se o tamanho da linha de dados for grande, tenha cuidado ao aumentar **writeBatchSize**. Um valor alto pode levar a uma falha de cópia causada pela sobrecarga do banco de dados.

### <a name="nosql-stores"></a>Repositórios NoSQL

* Para o **Armazenamento de Tabelas**:
  * **Partição**: gravar os dados em partições intercaladas diminui drasticamente o desempenho. Classificar os dados de origem por chave de partição para que os dados são inseridos com eficiência em uma partição após a outra. Ou, você pode ajustar a lógica para gravar os dados em uma única partição.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerações da serialização e desserialização

Serialização e desserialização podem ocorrer quando seu conjunto de dados de entrada ou um conjunto de dados de saída é um arquivo. Para obter mais informações sobre formatos de arquivo com suporte pela atividade de cópia, consulte [suporte para formatos de arquivo e compactação](supported-file-formats-and-compression-codecs.md).

**Comportamento da cópia**:

* Copiar arquivos entre os armazenamentos de dados baseados em arquivos:
  * Quando a entrada e saídos conjuntos de dados os dois têm a mesma ou nenhuma configuração do formato de arquivo, o serviço de movimentação de dados executa um *cópia binária* sem nenhuma serialização ou desserialização. Você vê uma maior taxa de transferência em comparação com o cenário no qual as configurações do formato de arquivo de origem e do coletor são diferentes entre si.
  * Quando a entrada e saída datasets são em formato de texto e apenas a codificação tipo é diferente, o serviço de movimentação de dados apenas faz a conversão de codificação. Ele não faz nenhuma serialização e desserialização, causando uma sobrecarga do desempenho em comparação com uma cópia binária.
  * Quando a entrada e saídos conjuntos de dados de ambas as tem diferentes formatos de arquivos ou configurações diferentes, como delimitadores, o serviço de movimentação de dados desserializa os dados de origem para transmitir, transformar e, em seguida, serializá-lo no formato de saída que você indicou. Essa operação resulta em uma sobrecarga do desempenho mais significativa em comparação com outros cenários.
* Quando você copia arquivos de ou para um armazenamento de dados que não seja o arquivo com base, por exemplo, de um armazenamento baseado em arquivo para um armazenamento relacional, a etapa de serialização ou desserialização é necessária. Essa etapa resulta em uma sobrecarga significativa do desempenho.

**Formato de arquivo**: o formato de arquivo escolhido pode afetar o desempenho da cópia. Por exemplo, Avro é um formato binário compacto que armazena metadados com dados. Ele tem um amplo suporte no ecossistema do Hadoop para processar e consultar. Avro é mais caro para serialização e desserialização, o que resulta na menor taxa de transferência de cópia em comparação comparada o formato de texto. 

Faça a escolha do formato de arquivo em todo o fluxo de processamento de forma global. Inicie com:

- O que forma os dados é armazenado em armazenamentos de dados de origem ou a extração de sistemas externos.
- O melhor formato para armazenamento, processamento analítico e consulta.
- Em qual formato os dados devem ser exportados para os data marts para as ferramentas de relatório e visualização.

Às vezes, um formato de arquivo abaixo do ideal para o desempenho de leitura e gravação pode ser uma boa escolha ao considerar o processo analítico geral.

## <a name="considerations-for-compression"></a>Considerações da compactação

Quando o conjunto de dados de entrada ou saído é um arquivo, você pode definir a atividade de cópia para executar a compactação ou descompactação conforme grava dados no destino. Quando você escolher a compactação, faça uma compensação entre a entrada/saída (E/S) e a CPU. Compactar os dados tem um custo extra nos recursos de computação. Mas, por sua vez, reduz a E/S da rede e o armazenamento. Dependendo de seus dados, você poderá ver um aumento na taxa de transferência geral de cópia.

**Codec**: Cada codec de compactação tem suas vantagens. Por exemplo, o bzip2 tem a menor taxa de transferência de cópia, mas você obtém o melhor desempenho de consulta do Hive com o bzip2 porque pode dividi-lo para o processamento. Gzip é a opção mais equilibrada e ele tem usadas com mais frequência. Escolha o codec mais adequado para seu cenário completo.

**Nível**: você pode escolher entre duas opções para cada codec de compactação, compactação mais rápida ou compactação ideal. A opção compactada mais rápida compacta os dados assim que possível, mesmo se o arquivo resultante não é compactado da maneira ideal. A opção de compactação ideal leva mais tempo na compactação e produz uma quantidade mínima de dados. Você pode testar as duas opções para qual fornece o melhor desempenho no seu caso.

**Uma consideração**: para copiar uma grande quantidade de dados entre um repositório local e a nuvem, considere usar a [Cópia em etapas](#staged-copy) com a compactação habilitada. Usar o armazenamento provisório é útil quando a largura de banda de sua rede corporativa e os serviços do Azure é o fator limitante, e você deseja que o conjunto de dados de entrada e saída de dataset para estar em formato descompactado.

## <a name="considerations-for-column-mapping"></a>Considerações do mapeamento de coluna

Você pode definir as **columnMappings** propriedade em uma atividade de cópia para mapear tudo ou um subconjunto de colunas de entrada para as colunas de saída. Depois do serviço de movimentação de dados ler os dados de origem, ele precisa executar o mapeamento de coluna nos dados antes de gravar os dados no coletor. Esse processamento extra reduz a taxa de transferência de cópia.

Se o armazenamento de dados de origem for de consulta, por exemplo, for um armazenamento relacional como o Banco de Dados SQL ou o SQL Server, ou se for um armazenamento NoSQL, como o Armazenamento de tabelas ou o Azure Cosmos DB, considere enviar a filtragem da coluna por push e reordenar a lógica para a propriedade **query**, em vez de usar o mapeamento de coluna. Dessa forma, a projeção ocorrerá enquanto o serviço de movimentação de dados lê os dados do armazenamento de dados de origem, onde é muito mais eficiente.

Saiba mais sobre [mapeamento de esquema de atividade de cópia](copy-activity-schema-and-type-mapping.md).

## <a name="other-considerations"></a>Outras considerações

Se o tamanho dos dados que você deseja copiar for grande, você pode ajustar sua lógica de negócios para particionar ainda mais os dados. Você pode agendar a atividade de cópia para ser executado com mais frequência para reduzir o tamanho dos dados para cada atividade de cópia que é executado.

Tenha cuidado com o número de conjuntos de dados e copiar as atividades que exigem o Azure Data Factory para se conectar ao mesmo armazenamento de dados ao mesmo tempo. Vários trabalhos de cópia simultâneos pode restringir um armazenamento de dados e levar a um desempenho reduzido, repetições internas do trabalho de cópia e, em alguns casos, falhas de execução.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Cenário de exemplo: Copiar de um SQL server no local para o armazenamento de BLOBs

**Cenário**: Um pipeline é criado para copiar dados de um SQL server no local para o armazenamento de BLOBs no formato CSV. Para acelerar o trabalho de cópia, os arquivos CSV devem ser compactados no formato bzip2.

**Análise e teste**: A taxa de transferência da atividade de cópia é menor que 2 MBps, que é muito mais lento do que o parâmetro de comparação de desempenho.

**Análise e ajuste do desempenho**: para solucionar o problema de desempenho, vejamos como os dados são processados e movidos.

- **Ler dados**: O integration runtime abre uma conexão com o SQL Server e envia a consulta. SQL Server responde enviando o fluxo de dados para o tempo de execução de integração por meio da intranet.
- **Serializar e compactar dados**: O integration runtime serializa o fluxo de dados para o formato CSV e compacta os dados em um fluxo bzip2.
- **Gravar dados**: O integration runtime carrega o fluxo bzip2 no armazenamento de BLOBs via internet.

Como você pode ver, os dados são processados e movidos de forma sequencial streaming: SQL Server > LAN > Integration Runtime > WAN > armazenamento de blobs. O desempenho geral é limitado pela taxa de transferência mínima no pipeline.

![Fluxo de dados](./media/copy-activity-performance/case-study-pic-1.png)

Um ou mais dos seguintes fatores pode causar o gargalo do desempenho:

* **Fonte**: o próprio SQL Server tem uma baixa taxa de transferência devido às cargas pesadas.
* **Tempo de execução de integração auto-hospedado**:
  * **LAN**: o Integration Runtime está localizado longe do computador do SQL Server e tem uma conexão com baixa largura de banda.
  * **Integration Runtime**: o Integration Runtime atingiu suas limitações de carga para executar as seguintes operações:
    * **Serialização**: serializar o fluxo de dados para o formato CSV com taxa de transferência lenta.
    * **Compactação**: Você escolheu um codec de compactação lenta, por exemplo, bzip2, que é 2.8 MBps com Core i7.
  * **WAN**: A largura de banda entre a rede corporativa e os serviços do Azure é baixa, por exemplo, T1 = 1,544 kbps; T2 = 6,312 kbps.
* **Coletor**: o armazenamento de blobs tem baixa taxa de transferência. Esse cenário é improvável porque seu contrato de nível de serviço (SLA) garante um mínimo de 60 MBps.

Nesse caso, a compactação de dados bzip2 pode estar desacelerando todo o pipeline. Trocar para o codec de compactação gzip pode aliviar esse gargalo.

## <a name="references"></a>Referências

Aqui estão as referências de monitoramento e ajuste do desempenho para alguns dos armazenamentos de dados com suporte:

* Armazenamento do Azure, que inclui o armazenamento de BLOBs e armazenamento de tabela: [Destinos de escalabilidade do armazenamento do Azure](../storage/common/storage-scalability-targets.md) e [lista de verificação de desempenho e escalabilidade do armazenamento do Azure](../storage/common/storage-performance-checklist.md).
* Banco de Dados SQL do Azure: Você pode [monitorar o desempenho](../sql-database/sql-database-single-database-monitor.md) e verificar o percentual de unidade de transação de banco de dados (DTU).
* SQL Data Warehouse do Azure: Sua capacidade é medida em unidades de Data Warehouse (DWUs). Ver [gerenciar poder de computação no SQL Data Warehouse do Azure (visão geral)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md).
* O Azure Cosmos DB: [Níveis de desempenho no Azure Cosmos DB](../cosmos-db/performance-levels.md).
* SQL Server local: [Monitorar e ajustar o desempenho](https://msdn.microsoft.com/library/ms189081.aspx).
* Servidor de arquivos local: [Ajuste de desempenho para servidores de arquivos](https://msdn.microsoft.com/library/dn567661.aspx).

## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos de atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Mapeamento de esquema de atividade de cópia](copy-activity-schema-and-type-mapping.md)
- [Tolerância a falhas da atividade de cópia](copy-activity-fault-tolerance.md)
