---
title: Mover dados usando a Atividade de Cópia | Microsoft Docs
description: 'Saiba mais sobre a movimentação de dados em pipelines do Data Factory: migração de dados entre repositórios na nuvem e entre um repositório local e um repositório na nuvem. Use a Atividade de Cópia.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: bfb15e717e3cb726aba782d9a9506330d7ea39fe
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839328"
---
# <a name="move-data-by-using-copy-activity"></a>Mover dados usando a Atividade de Cópia
> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](data-factory-data-movement-activities.md)
> * [Versão 2 (versão atual)](../copy-activity-overview.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Copiar atividade na V2](../copy-activity-overview.md).

## <a name="overview"></a>Visão geral
No Azure Data Factory, você pode usar a Atividade de Cópia para copiar dados entre armazenamentos de dados locais e na nuvem. Após os dados serem copiados, eles ainda podem ser transformados e analisados. Também é possível usar a Atividade de Cópia a fim de publicar resultados de análise e transformação para consumo do aplicativo e BI (business intelligence).

![Função da Atividade de Cópia](media/data-factory-data-movement-activities/copy-activity.png)

A Atividade de Cópia é alimentada por um [serviço disponível globalmente](#global)seguro, confiável e escalonável. Este artigo fornece detalhes sobre a movimentação de dados no Data Factory e na Atividade de Cópia.

Primeiramente, vejamos como a migração de dados ocorre entre dois repositórios de dados na nuvem e entre um repositório de dados local e um repositório de dados na nuvem.

> [!NOTE]
> Para saber mais sobre atividades em geral, confira o artigo sobre [noções básicas de pipelines e atividades](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Copiando dados entre dois armazenamentos de dados em nuvem
Quando os repositórios de dados de origem e coletor estiverem na nuvem, a Atividade de Cópia passará pelas fases a seguir para copiar os dados da origem para o coletor. O serviço que capacita a Atividade de Cópia:

1. Lê dados do repositório de dados de origem.
2. Executa a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e a conversão de tipo. Ele realiza essas operações com base nas configurações do conjunto de dados de entrada, no conjunto de dados de saída e na Atividade de Cópia.
3. Grava dados no repositório de dados de destino.

O serviço escolhe automaticamente a região ideal para realizar a movimentação de dados. Essa região geralmente é a mais próxima do repositório de dados do coletor.

![Cópia de nuvem para nuvem](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar dados entre um armazenamento de dados local e um armazenamento de dados em nuvem
Para mover dados com segurança entre o armazenamento de dados local e um armazenamento de dados na nuvem, instale o Gateway de Gerenciamento de Dados no computador local. O Gateway de Gerenciamento de Dados é um agente que possibilita a movimentação e o processamento de dados híbridos. Você pode instalá-lo no mesmo computador que o repositório de dados em si ou em outro computador que tenha acesso ao repositório de dados.

Neste cenário, o Gateway de Gerenciamento de Dados executa a serialização/desserialização, a compactação/descompactação, o mapeamento de coluna e a conversão de tipo. Os dados não fluem pelo serviço Azure Data Factory. Em vez disso, o Gateway de Gerenciamento de Dados grava os dados diretamente no repositório de destino.

![Cópia do local para a nuvem](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Confira [Mover dados entre repositórios de dados locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para ver uma introdução e instruções passo a passo. Confira o artigo [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) para obter informações detalhadas sobre esse agente.

Você também pode mover dados entre repositórios de dados compatíveis hospedados nas VMs (máquinas virtuais) de IaaS do Azure usando o Gateway de Gerenciamento de Dados. Nesse caso, é possível instalar o Gateway de Gerenciamento de Dados na mesma VM do repositório de dados em si ou em uma VM separada que tenha acesso ao repositório de dados.

## <a name="supported-data-stores-and-formats"></a>Fontes de dados e formatos com suporte
A Atividade de Cópia no Data Factory copia os dados de um repositório de dados de origem para um repositório de dados de coletor. A Data Factory dá suporte aos repositórios de dados a seguir. Os dados de qualquer origem podem ser gravados em qualquer coletor. Clique em um repositório de dados para saber como copiar dados dentro e fora do repositório.

> [!NOTE] 
> Se precisar mover dados para dentro e fora de um armazenamento de dados ao qual a Atividade de Cópia não dá suporte, use uma **atividade personalizada** no Data Factory com sua própria lógica para copiar/mover dados. Para obter detalhes sobre como criar e usar uma atividade personalizada, confira [Usar atividades personalizadas em um pipeline do Azure Data Factory](data-factory-use-custom-activities.md).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Os repositórios de dados com * podem estar no local ou no Azure IaaS e exigem a instalação do [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) em um computador Azure IaaS/local.

### <a name="supported-file-formats"></a>Formatos de arquivo com suporte
Você pode usar a Atividade de Cópia para **copiar arquivos no estado em que se encontram** entre dois armazenamentos de dados baseados em arquivo, ignore a [seção de formato](data-factory-create-datasets.md) nas duas definições de conjunto de dados de entrada e de saída. Os dados são copiados com eficiência sem qualquer serialização/desserialização.

A Atividade de Cópia também lê e grava em arquivos em formatos especificados: **Texto, JSON, Avro, ORC e Parquet** e os codecs de compactação **GZip, Deflate, BZip2 e ZipDeflate** têm suporte. Consulte [Formatos de arquivo e compactação com suporte](data-factory-supported-file-and-compression-formats.md) para obter detalhes.

Por exemplo, você pode fazer as seguintes atividades de cópia:

* Copiar dados no SQL Server local e gravar ao no Azure Data Lake Store no formato ORC.
* Copiar arquivos no formato de texto (CSV) do Sistema de Arquivos local e gravá-los no Blob do Azure no formato Avro.
* Copiar os arquivos compactados do Sistema de Arquivos local e, em seguida, descompactá-los no Azure Data Lake Store.
* Copiar dados em formato de texto (CSV) compactado por GZip do Blob do Azure e gravá-los no Banco de Dados SQL do Azure.

## <a name="global"></a>Movimentação de dados globalmente disponível
O Azure Data Factory está disponível apenas nas regiões Oeste dos EUA, Leste dos EUA e Europa Setentrional. No entanto, o serviço que possibilita a Atividade de cópia está disponível globalmente nas seguintes regiões e regiões geográficas. A topologia globalmente disponível garante a movimentação de dados eficiente, o que geralmente evita saltos entre regiões. Confira [Serviços por região](https://azure.microsoft.com/regions/#services) para ver a disponibilidade do Data Factory e da Movimentação de Dados em uma região.

### <a name="copy-data-between-cloud-data-stores"></a>Copiar dados entre armazenamentos de dados em nuvem
Quando os armazenamentos de dados de origem e de coletor residem na nuvem, o Data Factory usa uma implantação de serviço na região que está mais perto do local do coletor na mesma região geográfica para realizar a movimentação de dados. Consulte a tabela a seguir para ver o mapeamento:

| Geografia dos armazenamentos de dados de destino | Região do armazenamento de dados de destino | Região usada para movimentação de dados |
|:--- |:--- |:--- |
| Estados Unidos | East US | East US |
| &nbsp; | Leste dos EUA 2 | Leste dos EUA 2 |
| &nbsp; | Centro dos EUA | Centro dos EUA |
| &nbsp; | Centro-Norte dos EUA | Centro-Norte dos EUA |
| &nbsp; | Centro-Sul dos Estados Unidos | Centro-Sul dos Estados Unidos |
| &nbsp; | Centro-Oeste dos EUA | Centro-Oeste dos EUA |
| &nbsp; | Oeste dos EUA | Oeste dos EUA |
| &nbsp; | Oeste dos EUA 2 | Oeste dos EUA 2 |
| Canadá | Leste do Canadá | Canadá Central |
| &nbsp; | Canadá Central | Canadá Central |
| Brasil | Sul do Brasil | Sul do Brasil |
| Europa | Norte da Europa | Norte da Europa |
| &nbsp; | Europa Ocidental | Europa Ocidental |
| Reino Unido | Oeste do Reino Unido | Sul do Reino Unido |
| &nbsp; | Sul do Reino Unido | Sul do Reino Unido |
| Pacífico Asiático | Sudeste Asiático | Sudeste Asiático |
| &nbsp; | Ásia Oriental | Sudeste Asiático |
| Austrália | Leste da Austrália | Leste da Austrália |
| &nbsp; | Sudeste da Austrália | Sudeste da Austrália |
| Índia | Índia Central | Índia Central |
| &nbsp; | Índia Ocidental | Índia Central |
| &nbsp; | Sul da Índia | Índia Central |
| Japão | Leste do Japão | Leste do Japão |
| &nbsp; | Oeste do Japão | Leste do Japão |
| Coreia do Sul | Coreia Central | Coreia Central |
| &nbsp; | Sul da Coreia | Coreia Central |

Como alternativa, você pode indicar explicitamente a região do serviço Data Factory a ser usada para realizar a cópia especificando a propriedade `executionLocation` em Atividade de Cópia `typeProperties`. Os valores com suporte para essa propriedade estão listados acima da coluna **Região usada para movimentação de dados**. Observe que seus dados passarão para essa região na transferência realizada durante a cópia. Por exemplo, para copiar entre os repositórios do Azure na Coreia do Sul, especifique `"executionLocation": "Japan East"` para rotear pela região do Japão (veja [exemplo de JSON](#by-using-json-scripts) como referência).

> [!NOTE]
> Se a região do repositório de dados de destino não estiver na lista anterior ou não puder ser detectada, a Atividade de Cópia falhará em vez de passar por uma região alternativa, a menos que `executionLocation` seja especificado. A lista de regiões com suporte será expandida ao longo do tempo.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Copiar dados entre um armazenamento de dados local e um armazenamento de dados em nuvem
Quando os dados estiverem sendo copiados entre repositórios locais (ou IaaS/máquinas virtuais do Azure) e na nuvem, o [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) executa a movimentação de dados em um computador local ou máquina virtual. Os dados não fluem por meio do serviço na nuvem, a menos que você use o recurso [cópia em etapas](data-factory-copy-activity-performance.md#staged-copy) . Nesse caso, os dados fluem por meio de Armazenamento de Blobs do Azure de preparo antes de serem gravados no repositório de dados do coletor.

## <a name="create-a-pipeline-with-copy-activity"></a>Criar um pipeline com Atividade de Cópia
Você pode criar um pipeline com uma Atividade de Cópia de duas maneiras:

### <a name="by-using-the-copy-wizard"></a>Usando o Assistente de cópia
O Assistente de Cópia do Data Factory ajuda você a criar um pipeline com a Atividade de Cópia. Esse pipeline permite copiar dados de fontes compatíveis para destinos *sem gravar definições JSON* para serviços vinculados, conjuntos de dados e pipelines. Veja [Assistente de Cópia do Data Factory](data-factory-copy-wizard.md) para obter detalhes sobre o assistente.  

### <a name="by-using-json-scripts"></a>Usando scripts JSON
Você pode usar o Editor do Data Factory no Visual Studio ou o Azure PowerShell para criar uma definição de JSON para um pipeline (usando a atividade de cópia). Em seguida, você pode implantá-la para criar o pipeline no Data Factory. Consulte [Tutorial: Usar Atividade de Cópia em um pipeline do Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para ver um tutorial com instruções detalhadas.    

As propriedades JSON (como nome, descrição, tabelas de entrada e saída, e políticas) estão disponíveis para todos os tipos de atividade. As propriedades que estão disponíveis na seção `typeProperties` da atividade variam de acordo com cada tipo de atividade.

Para a Atividade de Cópia, a seção `typeProperties` varia de acordo com os tipos de fonte e coletor. Clique em uma fonte/coletor na seção [Fontes e coletores com suporte](#supported-data-stores-and-formats) para saber mais sobre as propriedades do tipo às quais a Atividade de Cópia dá suporte para esse repositório de dados.

Veja um exemplo de definição JSON:

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
A agenda que é definida no conjunto de dados de saída determina quando a atividade é executada (por exemplo, **diariamente**, frequência como **dia** e intervalo como **1**). A atividade copia dados de um conjunto de dados de entrada (**origem**) para um conjunto de dados de saída (**coletor**).

Você pode especificar mais de um conjunto de dados de entrada para a Atividade de Cópia. Eles são usados para verificar as dependências antes que a atividade seja executada. No entanto, somente os dados do primeiro conjunto de dados são copiados para o conjunto de dados de destino. Para saber mais, confira [Agendamento e execução](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Desempenho e ajuste
Confira o artigo [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md)que descreve os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory. Ele também lista o desempenho observado durante os testes internos e discute várias maneiras de otimizar o desempenho da Atividade de Cópia.

## <a name="fault-tolerance"></a>Tolerância a falhas
Por padrão, a atividade de cópia interromperá a cópia de dados e retornará uma falha ao encontrar dados incompatíveis entre a origem e o coletor, embora seja possível configurar explicitamente para ignorar e registrar as linhas incompatíveis e copiar apenas esses dados compatíveis para tornar a cópia bem-sucedida. Consulte a [Tolerância a falhas da Atividade de Cópia](data-factory-copy-activity-fault-tolerance.md) para obter mais detalhes.

## <a name="security-considerations"></a>Considerações sobre segurança
Consulte as [Considerações sobre segurança](data-factory-data-movement-security-considerations.md), que descreve a infraestrutura básica de segurança usada pelos serviços de movimentação de dados no Azure Data Factory para proteger os dados.

## <a name="scheduling-and-sequential-copy"></a>Agendamento e cópia sequencial
Confira [Agendamento e execução](data-factory-scheduling-and-execution.md) para obter informações detalhadas sobre como funcionam o agendamento e a execução no Data Factory. É possível executar várias operações de cópia, uma após a outra de maneira sequencial/ordenada. Veja a seção [Copiar sequencialmente](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="type-conversions"></a>Conversões de tipo
Armazenamentos de dados diferentes têm sistemas de tipo nativo diferentes. A Atividade de Cópia executa conversões de tipo automáticas dos tipos de origem para os tipos de coletor a seguinte abordagem de duas etapas:

1. Converter de tipos de origem nativos em um tipo .NET.
2. Converter de um tipo .NET em um tipo de coletor nativo.

O mapeamento de um sistema de tipo nativo para o tipo .NET do armazenamento de dados está no respectivo artigo sobre armazenamento de dados. (Clique no link específico da tabela Repositórios de dados com suporte). Você pode usar esses mapeamentos para determinar os tipos apropriados ao criar tabelas para que a Atividade de Cópia execute as conversões certas.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a Atividade de Cópia, consulte [Copiar dados de armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Para saber mais sobre como mover dados de um repositório de dados local para um repositório de dados na nuvem, confira [Mover dados de repositórios de dados locais para repositórios de dados na nuvem](data-factory-move-data-between-onprem-and-cloud.md).
