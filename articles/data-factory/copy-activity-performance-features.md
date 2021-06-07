---
title: Recursos de otimização de desempenho da atividade de cópia
description: Saiba mais sobre os principais recursos que ajudam a otimizar o desempenho da atividade de cópia no Azure Data Factory Marketplace.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/24/2020
ms.openlocfilehash: ecb4550b218b069273cba2e3d70a9510c1cc74ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387794"
---
# <a name="copy-activity-performance-optimization-features"></a>Recursos de otimização de desempenho da atividade de cópia

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreve os recursos de otimização de desempenho da atividade de cópia que você pode aproveitar em Azure Data Factory.

## <a name="data-integration-units"></a>Unidades de integração de dados

Uma unidade de integração de dados é uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos de rede) de uma única unidade no Azure Data Factory. A unidade de integração de dados só se aplica ao [Integration Runtime do Azure](concepts-integration-runtime.md#azure-integration-runtime), mas não ao [tempo de execução de integração auto-hospedado](concepts-integration-runtime.md#self-hosted-integration-runtime).

O DIUs permitido para capacitar uma execução da atividade de cópia é **entre 2 e 256**. Se não for especificado ou você escolher "auto" na interface do usuário, Data Factory aplicará dinamicamente a configuração ideal de DIU com base no seu par de coletor de origem e padrão de dados. A tabela a seguir lista os intervalos de DIU com suporte e o comportamento padrão em diferentes cenários de cópia:

| Copiar cenário | Intervalo de DIU com suporte | DIUs padrão determinadas pelo serviço |
|:--- |:--- |---- |
| Entre repositórios de arquivos |- **Copiar de ou para arquivo único**: 2-4 <br>- **Copiar de e para vários arquivos**: 2-256 dependendo do número e do tamanho dos arquivos <br><br>Por exemplo, se você copiar dados de uma pasta com 4 arquivos grandes e optar por preservar a hierarquia, o DIU máximo efetivo será 16; Quando você opta por mesclar o arquivo, o máximo de DIU efetivo é 4. |Entre 4 e 32, dependendo do número e do tamanho dos arquivos |
| Do repositório de arquivos para um repositório que não seja de arquivo |- **Copiar de um único arquivo**: 2-4 <br/>- **Copiar de vários arquivos**: 2-256 dependendo do número e do tamanho dos arquivos <br/><br/>Por exemplo, se você copiar dados de uma pasta com 4 arquivos grandes, o DIU máximo efetivo será 16. |- **Copiar no banco de dados SQL do Azure ou Azure Cosmos DB**: entre 4 e 16 dependendo da camada de coletor (DTUs/RUs) e do padrão de arquivo de origem<br>- **Copiar para o Azure Synapse Analytics** usando o polybase ou a instrução de cópia: 2<br>-Outro cenário: 4 |
| De um repositório que não seja de arquivo para o repositório de arquivos |- **Copiar de armazenamentos de dados habilitados para opção de partição** (incluindo o banco de dados [SQL](connector-azure-sql-database.md#azure-sql-database-as-the-source)do azure, [Azure SQL instância gerenciada](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)e [Teradata](connector-teradata.md#teradata-as-source)): 2-256 ao gravar em uma pasta e 2-4 ao gravar em um único arquivo. Observação por partição de dados de origem pode usar até 4 DIUs.<br>- **Outros cenários**: 2-4 |- **Copiar do REST ou http**: 1<br/>- **Copiar do Amazon redshift** usando Unload: 2<br>- **Outro cenário**: 4 |
| Entre repositórios que não são de arquivos |- **Copiar de armazenamentos de dados habilitados para opção de partição** (incluindo o banco de dados [SQL](connector-azure-sql-database.md#azure-sql-database-as-the-source)do azure, [Azure SQL instância gerenciada](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)e [Teradata](connector-teradata.md#teradata-as-source)): 2-256 ao gravar em uma pasta e 2-4 ao gravar em um único arquivo. Observação por partição de dados de origem pode usar até 4 DIUs.<br/>- **Outros cenários**: 2-4 |- **Copiar do REST ou http**: 1<br>- **Outro cenário**: 4 |

Você pode ver o DIUs usado para cada execução de cópia na exibição de monitoramento da atividade de cópia ou na saída da atividade. Para obter mais informações, consulte [monitoramento de atividade de cópia](copy-activity-monitoring.md). Para substituir esse padrão, especifique um valor para a `dataIntegrationUnits` propriedade da seguinte maneira. O *número real de DIUs* que a operação de cópia usa na execução é igual ou menor que o valor configurado, dependendo do seu padrão de dados.

Você será cobrado **n º de DIUs da \* unidade de duração da cópia de tempo \* /DIU-hora**. Veja os preços atuais [aqui](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/). A moeda local e a descontagem separada podem ser aplicadas por tipo de assinatura.

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
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>Escalabilidade do tempo de execução de integração auto-hospedado

Se você quiser obter uma taxa de transferência mais alta, poderá escalar verticalmente ou escalar horizontalmente o IR do modo auto-hospedado:

- Se a CPU e a memória disponível no nó IR de hospedagem interna não forem totalmente utilizadas, mas a execução de trabalhos simultâneos estiver atingindo o limite, você deverá escalar verticalmente aumentando o número de trabalhos simultâneos que podem ser executados em um nó.  Consulte [aqui](create-self-hosted-integration-runtime.md#scale-up) para obter instruções.
- Se, por outro lado, a CPU estiver alta no nó IR auto-hospedado ou se a memória disponível estiver baixa, você poderá adicionar um novo nó para ajudar a escalar horizontalmente a carga entre os vários nós.  Consulte [aqui](create-self-hosted-integration-runtime.md#high-availability-and-scalability) para obter instruções.

Observação nos cenários a seguir, a execução da atividade de cópia única pode aproveitar vários nós de IR de hospedagem interna:

- Copie dados de armazenamentos baseados em arquivo, dependendo do número e do tamanho dos arquivos.
- Copie dados de um armazenamento de dados habilitado para opção de partição (incluindo o [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL instância gerenciada](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)e [Teradata](connector-teradata.md#teradata-as-source)), dependendo do número de partições de dados.

## <a name="parallel-copy"></a>Cópia paralela

Você pode definir cópia paralela ( `parallelCopies` Propriedade) na atividade de cópia para indicar o paralelismo que você deseja que a atividade de cópia use. Você pode considerar essa propriedade como o número máximo de threads na atividade de cópia que leu da origem ou gravar em armazenamentos de dados de coletor em paralelo.

A cópia paralela é ortogonal a [unidades de integração de dados](#data-integration-units) ou [nós de ir hospedados internamente](#self-hosted-integration-runtime-scalability). Ele é contado em todos os nós de IR DIUs ou auto-hospedados.

Para cada execução de atividade de cópia, por padrão Azure Data Factory aplica dinamicamente a configuração de cópia paralela ideal com base no seu par de fonte-coletor e padrão de dados. 

> [!TIP]
> O comportamento padrão da cópia paralela geralmente oferece a melhor taxa de transferência, que é determinada automaticamente pelo ADF com base em seu par de coletor de origem, padrão de dados e número de DIUs ou a contagem de CPU/memória/nó do IR auto-hospedado. Consulte [solucionar problemas de desempenho da atividade de cópia](copy-activity-performance-troubleshooting.md) em quando ajustar cópia paralela.

A tabela a seguir lista o comportamento de cópia paralela:

| Copiar cenário | Comportamento de cópia paralela |
| --- | --- |
| Entre repositórios de arquivos | `parallelCopies` determina o paralelismo **no nível do arquivo**. O agrupamento em cada arquivo ocorre abaixo de forma automática e transparente. Ele foi projetado para usar o melhor tamanho de bloco adequado para um determinado tipo de armazenamento de dados para carregar dados em paralelo. <br/><br/>O número real de cópias paralelas que a atividade de cópia usa em tempo de execução não é maior do que o número de arquivos que você tem. Se o comportamento de cópia for **mesclar** no coletor de arquivos, a atividade de cópia não poderá tirar proveito do paralelismo de nível de arquivo. |
| Do repositório de arquivos para um repositório que não seja de arquivo | -Ao copiar dados no banco de dados SQL do Azure ou Azure Cosmos DB, a cópia paralela padrão também depende da camada de coletor (número de DTUs/RUs).<br>-Ao copiar dados na tabela do Azure, a cópia paralela padrão é 4. |
| De um repositório que não seja de arquivo para o repositório de arquivos | -Ao copiar dados de um armazenamento de dados habilitado para opção de partição (incluindo o [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL instância gerenciada](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)e [Teradata](connector-teradata.md#teradata-as-source)), a cópia paralela padrão é 4. O número real de cópias paralelas que a atividade de cópia usa em tempo de execução não é maior do que o número de partições de dados que você tem. Ao usar o Integration Runtime auto-hospedado e copiar para o blob/ADLS Gen2 do Azure, observe que a cópia paralela máxima efetiva é 4 ou 5 por nó IR.<br>-Para outros cenários, a cópia paralela não entrará em vigor. Mesmo se o paralelismo for especificado, ele não será aplicado. |
| Entre repositórios que não são de arquivos | -Ao copiar dados no banco de dados SQL do Azure ou Azure Cosmos DB, a cópia paralela padrão também depende da camada de coletor (número de DTUs/RUs).<br/>-Ao copiar dados de um armazenamento de dados habilitado para opção de partição (incluindo o [Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source), [Azure SQL instância gerenciada](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source), [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source), [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [SAP Hana](connector-sap-hana.md#sap-hana-as-source), [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source), [SAP Table](connector-sap-table.md#sap-table-as-source), [SQL Server](connector-sql-server.md#sql-server-as-a-source)e [Teradata](connector-teradata.md#teradata-as-source)), a cópia paralela padrão é 4.<br>-Ao copiar dados na tabela do Azure, a cópia paralela padrão é 4. |

Para controlar a carga em computadores que hospedam seus armazenamentos de dados ou para ajustar o desempenho da cópia, você pode substituir o valor padrão e especificar um valor para a `parallelCopies` propriedade. O valor deve ser um inteiro maior ou igual a 1. Em tempo de execução, para obter o melhor desempenho, a atividade de cópia usa um valor menor ou igual ao valor que você definiu.

Quando você especificar um valor para a `parallelCopies` propriedade, faça com que o aumento de carga em seus armazenamentos de dados de origem e de coletor sejam contados. Considere também o aumento de carga para o tempo de execução de integração auto-hospedado se a atividade de cópia for habilitada por ela. Esse aumento de carga ocorre especialmente quando você tem várias atividades ou execuções simultâneas das mesmas atividades executadas no mesmo armazenamento de dados. Se você observar que o armazenamento de dados ou o tempo de execução de integração auto-hospedado está sobrecarregado com a carga, diminua o `parallelCopies` valor para aliviar a carga.

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
            "parallelCopies": 32
        }
    }
]
```

## <a name="staged-copy"></a>Cópia em etapas

Ao copiar dados de um armazenamento de dados de origem para um armazenamento de dados de coletor, você pode optar por usar o armazenamento de BLOBs do Azure ou Azure Data Lake Storage Gen2 como um armazenamento de preparo provisório. Esse preparo é especialmente útil nos seguintes casos:

- **Você deseja ingerir dados de vários armazenamentos de dados para o Azure Synapse Analytics por meio do polybase, copiar dados de/para floco de neve ou ingerir dados do Amazon redshift/HDFS forma definitiva.** Saiba mais detalhes em:
  - [Use o polybase para carregar dados no Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics).
  - [Conector floco de neve](connector-snowflake.md)
  - [Conector do Amazon Redshift](connector-amazon-redshift.md)
  - [HDFS connector](connector-hdfs.md) (Conector de HDFS)
- **Você não quer abrir portas que não sejam a porta 80 e a porta 443 em seu firewall devido a políticas corporativas de ti.** Por exemplo, ao copiar dados de um armazenamento de dados local para um banco de dado SQL do Azure ou para uma análise de Synapse do Azure, você precisa ativar a comunicação TCP de saída na porta 1433 para o Firewall do Windows e o firewall corporativo. Nesse cenário, a cópia em etapas pode aproveitar o tempo de execução de integração auto-hospedado para primeiro copiar dados para um armazenamento de preparo via HTTP ou HTTPS na porta 443 e, em seguida, carregar os dados de preparo no banco de dados SQL ou no Azure Synapse Analytics. Nesse fluxo, você não precisa habilitar a porta 1433.
- **Às vezes, demora um pouco para executar uma movimentação de dados híbrido (ou seja, copiar de um armazenamento de dados local para um armazenamento de dados de nuvem) em uma conexão de rede lenta.** Para melhorar o desempenho, você pode usar cópia em etapas para compactar os dados locais, de modo que leve menos tempo para mover dados para o armazenamento de dados de preparo na nuvem. Em seguida, você pode descompactar os dados no armazenamento de preparo antes de carregar no armazenamento de dados de destino.

### <a name="how-staged-copy-works"></a>Como funciona a cópia em etapas

Quando você ativa o recurso de preparo, primeiro os dados são copiados do armazenamento de dados de origem para o armazenamento de preparo (Traga seu próprio blob do Azure ou Azure Data Lake Storage Gen2). Em seguida, os dados são copiados do preparo para o armazenamento de dados do coletor. Azure Data Factory atividade de cópia gerencia automaticamente o fluxo de dois estágios para você e também limpa os dados temporários do armazenamento de preparo após a conclusão da movimentação de dados.

![Cópia em etapas](media/copy-activity-performance/staged-copy.png)

Ao ativar a movimentação de dados usando um armazenamento de preparo, você pode especificar se deseja que os dados sejam compactados antes de mover os dados do armazenamento de dados de origem para o repositório de preparo e, em seguida, descompactados antes de mover dados de um armazenamento de dados provisório ou de preparo para o armazenamento de dados do coletor.

No momento, não é possível copiar dados entre dois armazenamentos de dados que estão conectados por meio do IRs hospedado internamente diferente, nem com nem sem cópia preparada. Para esse cenário, você pode configurar duas atividades de cópia encadeadas explicitamente para copiar da origem para o preparo e, em seguida, do preparo para o coletor.

### <a name="configuration"></a>Configuração

Defina a configuração **enableStaging** na atividade de cópia para especificar se deseja que os dados sejam preparados no armazenamento antes de carregá-los em um armazenamento de dados de destino. Ao definir **enableStaging** como `TRUE` , especifique as propriedades adicionais listadas na tabela a seguir. 

| Propriedade | Descrição | Valor padrão | Obrigatório |
| --- | --- | --- | --- |
| enableStaging |Especifique se você deseja copiar os dados por meio de um armazenamento de preparo provisório. |Falso |Não |
| linkedServiceName |Especifique o nome de um [armazenamento de BLOBs do Azure](connector-azure-blob-storage.md#linked-service-properties) ou [Azure data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) serviço vinculado, que se refere à instância de armazenamento que você usa como um armazenamento de preparo provisório. |N/D |Sim, quando **enableStaging** está definido para TRUE |
| caminho |Especifique o caminho que você deseja que contenha os dados preparados. Se você não fornecer um caminho, o serviço criará um contêiner para armazenar dados temporários. |N/D |Não |
| enableCompression |Especifica se os dados devem ser compactados antes de serem copiados para o destino. Essa configuração reduz o volume de dados que são transferidos. |Falso |Não |

>[!NOTE]
> Se você usar cópia em etapas com compactação habilitada, a entidade de serviço ou a autenticação MSI para o serviço vinculado de blob de preparo não terá suporte.

Aqui está uma definição de exemplo de uma atividade de cópia com as propriedades descritas na tabela anterior:

```json
"activities":[
    {
        "name": "CopyActivityWithStaging",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
            },
            "sink": {
                "type": "SqlDWSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path"
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>Impacto de cobrança de cópia em etapas

Você é cobrado com base em duas etapas: copiar duração e copiar tipo.

* Quando você usa o preparo durante uma cópia de nuvem, que está copiando dados de um armazenamento de dados de nuvem para outro armazenamento de dados de nuvem, ambos os estágios capacitados pelo tempo de execução de integração do Azure, você é cobrado pela [soma da duração da cópia para a etapa 1 e etapa 2] x [preço unitário da cópia de nuvem].
* Quando você usa o preparo durante uma cópia híbrida, que está copiando dados de um armazenamento de dados local para um armazenamento de dados de nuvem, um estágio capacitado por um Integration Runtime de hospedagem interna, você é cobrado pelo [duração da cópia híbrida] x [preço unitário da cópia híbrida] + [duração da cópia da nuvem] x [preço unitário da cópia da nuvem].

## <a name="next-steps"></a>Próximas etapas
Confira os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
- [Guia de desempenho e escalabilidade da atividade de cópia](copy-activity-performance.md)
- [Solucionar problemas de desempenho da atividade de cópia](copy-activity-performance-troubleshooting.md)
- [Use Azure Data Factory para migrar dados de seu data Lake ou data warehouse para o Azure](data-migration-guidance-overview.md)
- [Migrar dados do Amazon S3 para o Armazenamento do Azure](data-migration-guidance-s3-azure-storage.md)