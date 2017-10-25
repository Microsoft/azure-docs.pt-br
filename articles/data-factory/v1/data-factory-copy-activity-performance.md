---
title: "Guia de desempenho e ajuste da Atividade de Cópia | Microsoft Docs"
description: "Saiba mais sobre os principais fatores que afetam o desempenho da movimentação de dados no Azure Data Factory quando você usa a Atividade de Cópia."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 4b9a6a4f-8cf5-4e0a-a06f-8133a2b7bc58
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 155e29e467b9752d491f62973ffccc986041fcab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="copy-activity-performance-and-tuning-guide"></a>Guia Desempenho e ajuste da Atividade de Cópia
A Atividade de cópia do Azure Data Factory fornece uma solução de dados excelente, segura, confiável e de alto desempenho. Ela permite que você a copie dezenas de terabytes de dados diariamente em uma grande variedade de repositórios de dados na nuvem e locais. Desempenho de carregamento de dados de rápido são a chave para garantir que você possa se concentrar no principal problema de "Big Data": a criação de soluções de análise avançada e obtenção de informações aprofundadas de todos esses dados.

O Azure fornece um conjunto de soluções de armazenamento de dados e data warehouse de nível empresarial, e atividade de cópia oferece uma experiência de carregamento de dados altamente otimizada que é fácil de configurar. Com apenas uma atividade de cópia única, você pode obter:

* Carregar dados no **SQL Data Warehouse do Azure** a **1,2 GBps**. Para ver um passo a passo com um caso de uso, veja [Load 1 TB into Azure SQL Data Warehouse under 15 minutes with Azure Data Factory](data-factory-load-sql-data-warehouse.md) (Carregar 1 TB no SQL Data Warehouse do Azure em menos de 15 minutos com o Azure Data Factory).
* O carregamento de dados no **Armazenamento de Blobs do Azure** a **1,0 GBps**
* O carregamento de dados no **Azure Data Lake Store** a **1,0 GBps**

Este artigo descreve:

* [números de referência de desempenho](#performance-reference) para armazenamentos de dados de origem e coletor com suporte, para ajudá-lo a planejar o projeto;
* Recursos que podem aumentar a taxa de transferência de cópia em cenários diferentes, incluindo [unidades de movimentação de dados em nuvem](#cloud-data-movement-units), [cópia paralela](#parallel-copy) e [cópia em etapas](#staged-copy);
* [diretrizes de ajuste de desempenho](#performance-tuning-steps) sobre como ajustar o desempenho e os principais fatores que podem afetar o desempenho da cópia.

> [!NOTE]
> Se você não estiver familiarizado com a Atividade de Cópia em geral, consulte [Mover dados usando a Atividade de Cópia](data-factory-data-movement-activities.md) antes de ler este artigo.
>

## <a name="performance-reference"></a>Referência de desempenho

Como uma referência, a tabela abaixo mostra o número da taxa de transferência de cópia em MBps para a origem específica e os pares de coletores com base nos testes internos. Para comparação, ela também demonstra como as diferentes configurações das [unidades de movimentação de dados na nuvem](#cloud-data-movement-units) ou da [escalabilidade do Gateway de Gerenciamento de Dados](data-factory-data-management-gateway-high-availability-scalability.md) (vários nós de gateway) podem ajudar no desempenho da cópia.

![Matriz de desempenho](./media/data-factory-copy-activity-performance/CopyPerfRef.png)


**Pontos a serem observados:**
* A taxa de transferência é calculada usando a seguinte fórmula: [tamanho dos dados lidos na origem]/[duração da execução da Atividade de Cópia].
* Os números de referência de desempenho na tabela foram medidos usando o conjunto de dados [TPC-H](http://www.tpc.org/tpch/) em uma execução de atividade de cópia única.
* Nos armazenamentos de dados do Azure, a origem e o coletor estão na mesma região do Azure.
* Para a cópia híbrida entre o armazenamento de dados local e na nuvem, cada nó de gateway foi executado em um computador que foi separado do armazenamento de dados local com a especificação abaixo. Quando uma única atividade foi executada no gateway, a operação de cópia consumiu apenas uma pequena parte da CPU, da memória ou da largura de banda do computador de teste. Saiba mais em [Considerações do Gateway de Gerenciamento de Dados](#considerations-for-data-management-gateway).
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
> Você pode obter uma maior taxa de transferência aproveitando mais DMUs (unidades de movimentação de dados) do que o padrão máximo, que é 32 para a execução de uma atividade de cópia de nuvem para nuvem. Por exemplo, com 100 DMUs, é possível copiar dados do Blob do Azure para o Azure Data Lake Store a **1 GBps**. Confira a seção [Unidades de movimentação de dados de nuvem](#cloud-data-movement-units) para obter detalhes sobre esse recurso e o cenário com suporte. Entre em contato com [suporte do Azure](https://azure.microsoft.com/support/) para solicitar mais DMUs.

## <a name="parallel-copy"></a>Cópia paralela
Você pode ler os dados na origem ou gravar os dados no destino **em paralelo dentro de uma execução da Atividade de Cópia**. Esse recurso melhora a taxa de transferência de uma operação de cópia e reduz o tempo que leva para mover os dados.

Essa configuração é diferente da propriedade de **simultaneidade** na definição da atividade. A propriedade de **simultaneidade** determina o número de **execuções simultâneas** da Atividade de Cópia para processar os dados de diferentes janelas de atividade (1:00 às 2:00, 2:00 às 3:00, 3:00 às 4:00 e assim por diante). Esse recurso é útil quando você executa um carregamento histórico. O recurso de cópia paralela aplica-se a uma **simples execução da atividade**.

Vejamos um cenário de exemplo. No exemplo a seguir, várias fatias do passado precisam ser processadas. O Data Factory executa uma instância da Atividade de Cópia (uma execução da atividade) para cada fatia:

* Fatia de dados da primeira janela de atividade (1:00 às 2:00) = = > Execução da atividade 1
* Fatia de dados da segunda janela de atividade (2:00 às 3:00) = = > Execução da atividade 2
* Fatia de dados da terceira janela de atividade (3:00 às 4:00) == > Execução da atividade 3

E assim por diante.

Neste exemplo, quando o valor da **simultaneidade** é definido para 2, a **execução da atividade 1** e a **execução da atividade 2** copiam dos dados das duas janelas de atividade **simultaneamente** para melhorar o desempenho da movimentação de dados. No entanto, se vários arquivos estiverem associados à execução da Atividade 1, o serviço de movimentação de dados copiará os arquivos da origem para o destino um arquivo por vez.

### <a name="cloud-data-movement-units"></a>Unidades de movimentação de dados de nuvem
Uma **unidade de movimentação de dados de nuvem (DMU)** é uma medida que representa a potência (uma combinação de CPU, memória e alocação de recursos da rede) de uma unidade única no Data Factory. Uma DMU pode ser usada em uma operação de cópia de nuvem para nuvem, mas não em uma cópia híbrida.

Por padrão, o Data Factory usa uma única DMU de nuvem para fazer uma única execução da Atividade de Cópia. Para substituir esse padrão, especifique um valor para a propriedade **cloudDataMovementUnits** da seguinte maneira. Para obter informações sobre o nível de ganho de desempenho que você pode obter ao configurar mais unidades para uma origem e coletor de cópia específicos, consulte a [referência de desempenho](#performance-reference).

```json
"activities":[  
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
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
Os **valores permitidos** para a propriedade **cloudDataMovementUnits** são: 1 (padrão), 2, 4, 8, 16 e 32. O **número real de DMUs de nuvem** que a operação de cópia usa na execução é igual ou menor que o valor configurado, dependendo do seu padrão de dados.

> [!NOTE]
> Se precisar de mais DMUs de nuvem para uma taxa de transferência maior, entre em contato com o [suporte do Azure](https://azure.microsoft.com/support/). A configuração de 8 e superior atualmente funciona apenas quando você **copia vários arquivos do Armazenamento de Blobs/Data Lake Store/Amazon S3/FTP na nuvem/SFTP na nuvem para Armazenamento de Blobs/Data Lake Store/Banco de Dados SQL do Azure**.
>

### <a name="parallelcopies"></a>parallelCopies
Você pode usar a propriedade **parallelCopies** para indicar o paralelismo que deseja que a Atividade de Cópia use. Você pode considerar essa propriedade como o número máximo de threads na Atividade de Cópia que podem ler a partir de sua fonte ou gravar em seus armazenamentos do coletor de dados em paralelo.

Para cada execução da Atividade de Cópia, o Data Factory determina o número de cópias paralelas a usar para copiar os dados do armazenamento de dados de origem para o de destino. O número padrão de cópias paralelas usadas depende do tipo de fonte e coletor usado.  

| Fonte e coletor | Contagem de cópia paralela padrão determinada pelo serviço |
| --- | --- |
| Copiar dados entre os armazenamentos baseados em arquivo (armazenamento de Blobs; Data Lake Store; Amazon S3; sistema de arquivos local; HDFS (Sistema de Arquivos Distribuído Hadoop) local) |Entre 1 e 32. Depende do tamanho dos arquivos e do número de unidades de movimentação de dados de nuvem (DMUs) usados para copiar os dados entre dois armazenamentos de dados de nuvem ou da configuração física do computador do Gateway usado para obter uma cópia híbrida (para copiar os dados de ou para um armazenamento de dados local). |
| Copiar dados de **qualquer armazenamento de dados de origem para o armazenamento de Tabelas do Azure** |4 |
| Todos os outros pares de origem e coletor |1 |

Normalmente, o comportamento padrão deve fornecer a melhor taxa de transferência. No entanto, para controlar a carga em computadores que hospedam os armazenamentos de dados ou ajustar o desempenho da cópia, você pode optar por substituir o valor padrão e especificar um valor para a propriedade **parallelCopies** . O valor deve estar entre 1 e 32 (ambos incluídos). Na execução, para ter o melhor desempenho, a Atividade de Cópia usa um valor menor ou igual ao valor definido.

```json
"activities":[  
    {
        "name": "Sample copy activity",
        "description": "",
        "type": "Copy",
        "inputs": [{ "name": "InputDataset" }],
        "outputs": [{ "name": "OutputDataset" }],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 8
        }
    }
]
```
Pontos a serem observados:

* Quando você copia dados entre repositórios baseados em arquivo, **parallelCopies** determina o paralelismo no nível de arquivo. O agrupamento em um único arquivo aconteceria abaixo de modo automático e transparente, sendo projetado para usar o tamanho da parte mais adequado para um determinado tipo de repositório de dados de modo a carregar dados de modo paralelo e ortogonal em parallelCopies. O número real de cópias paralelas que o serviço de movimentação de dados usa para a operação de cópia no tempo de execução não é superior ao número de arquivos existentes. Se o comportamento da cópia for **mergeFile**, a Atividade de Cópia não poderá aproveitar o paralelismo no nível de arquivo.
* Quando você especificar um valor para a propriedade **parallelCopies** , considere o aumento de carga para seus armazenamentos de dados da origem e do coletor, e para o gateway, se for uma cópia híbrida. Isso ocorre especialmente quando você tem várias atividades ou execuções simultâneas das mesmas atividades executadas em relação ao mesmo armazenamento de dados. Se você perceber que o armazenamento de dados ou o Gateway está sobrecarregado com a carga, diminua o valor **parallelCopies** para aliviá-la.
* Quando você copia dados de armazenamentos que não são baseados em arquivos para os armazenamentos que são, o serviço de movimentação de dados ignora a propriedade **parallelCopies** . Mesmo se o paralelismo for especificado, ele não será aplicado neste caso.

> [!NOTE]
> Você deve usar o Gateway de Gerenciamento de Dados versão 1.11 ou posterior para utilizar o recurso **parallelCopies** quando faz uma cópia híbrida.
>
>

Para usar melhor essas duas propriedades e para melhorar a taxa de transferência de movimentação de dados, consulte os [casos de uso de exemplo](#case-study-use-parallel-copy). Você não precisa configurar **parallelCopies** para aproveitar o comportamento padrão. Se você configurar e **parallelCopies** for muito pequeno, várias DMUs de nuvem poderão não ser totalmente utilizadas.  

### <a name="billing-impact"></a>Impacto de cobrança
É **importante** lembrar que você é cobrado com base no tempo total da operação de cópia. Se um trabalho de cópia costumava levar uma hora com a unidade de nuvem e agora leva 15 minutos com quatro unidades de nuvem, a fatura geral fica quase igual. Por exemplo, você usa quatro unidades de nuvem. A primeira unidade de nuvem gasta 10 minutos, a segunda, 10 minutos, a terceira, 5 minutos e a quarta, 5 minutos, tudo em uma execução da Atividade de Cópia. Você é cobrado pelo tempo de cópia total (movimentação de dados), que é de 10 + 10 + 5 + 5 = 30 minutos. Usar **parallelCopies** não afeta a cobrança.

## <a name="staged-copy"></a>Cópia em etapas
Ao copiar dados de um armazenamento de dados de origem para um armazenamento de dados do coletor, você pode escolher usar um armazenamento de Blobs como um armazenamento de preparação provisório. Esse preparo é especialmente útil nos seguintes casos:

1. **Você deseja ingerir dados de vários armazenamentos de dados no SQL Data Warehouse via PolyBase**. O SQL Data Warehouse usa o PolyBase como um mecanismo de alta taxa de transferência para carregar uma grande quantidade de dados no SQL Data Warehouse. No entanto, os dados de origem devem estar no armazenamento de Blobs e devem atender a critérios adicionais. Quando você carrega dados de um armazenamento de dados diferente do armazenamento de Blobs, pode ativar a cópia dos dados por meio do armazenamento de Blobs de preparo intermediário. Nesse caso, o Data Factory executa as transformações de dados necessárias para garantir que eles atenderão aos requisitos do PolyBase. Em seguida, ele usa o PolyBase para carregar os dados no SQL Data Warehouse. Para ver mais detalhes, confira [Usar o PolyBase para carregar dados para o Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse). Para ver um passo a passo com um caso de uso, veja [Load 1 TB into Azure SQL Data Warehouse under 15 minutes with Azure Data Factory](data-factory-load-sql-data-warehouse.md) (Carregar 1 TB no SQL Data Warehouse do Azure em menos de 15 minutos com o Azure Data Factory).
2. **Às vezes, leva algum tempo para realizar a movimentação de dados híbridos (ou seja, copiar entre um armazenamento de dados local e um armazenamento de dados de nuvem) em uma conexão de rede lenta**. Para melhorar o desempenho, você pode compactar os dados locais para que leve menos tempo mover os dados para o armazenamento de dados de preparo na nuvem. Em seguida, você pode descompactar os dados no armazenamento de preparo antes de carregá-lo no armazenamento de dados de destino.
3. **Você não deseja abrir portas diferentes da porta 80 e da porta 443 em seu firewall, devido às políticas corporativas de TI**. Por exemplo, quando você copia dados de um armazenamento de dados local para um coletor do Banco de Dados SQL ou um coletor do SQL Data Warehouse, precisa ativar a comunicação de saída TCP na porta 1433 para o firewall do Windows e o firewall corporativo. Nesse cenário, aproveite o gateway para primeiro copiar os dados para uma instância de preparo do Armazenamento de Blobs via HTTP ou HTTPS na porta 443. Em seguida, carregue os dados no Banco de Dados SQL ou no SQL Data Warehouse a partir do preparo do armazenamento de Blobs. Nesse fluxo, você não precisa habilitar a porta 1433.

### <a name="how-staged-copy-works"></a>Como funciona a cópia em etapas
Quando você ativa o recurso de preparo, primeiro os dados são copiados do armazenamento de dados de origem para o armazenamento de dados de preparo (traga seu próprio). Em seguida, os dados são copiados do armazenamento de dados de preparo para o armazenamento de dados do coletor. O Data Factory gerencia automaticamente o fluxo de dois estágios para você. O Data Factory também limpa os dados temporários do armazenamento de preparo após a movimentação de dados ser concluída.

No cenário de cópia de nuvem (origem e coletor estão na nuvem), o gateway não é usado. O serviço Data Factory executa as operações de cópia.

![Cópia em etapas: cenário de nuvem](media/data-factory-copy-activity-performance/staged-copy-cloud-scenario.png)

No cenário de cópia híbrida, (fonte local e coletor na nuvem), o gateway move os dados do armazenamento de dados de origem para um armazenamento de dados de preparo. O serviço Data Factory também move os dados do armazenamento de dados de preparo para o armazenamento de dados do coletor. Copiar os dados de um armazenamento de dados de nuvem para um armazenamento de dados local por meio do preparo também é possível com o fluxo invertido.

![Cópia em etapas: cenário híbrido](media/data-factory-copy-activity-performance/staged-copy-hybrid-scenario.png)

Quando você ativa o movimento de dados usando um armazenamento de preparo, pode especificar se deseja que os dados sejam compactados antes de mover os dados do armazenamento de dados de origem para um armazenamento de dados de preparo ou provisório, em seguida, descompactados antes de mover os dados de um armazenamento de preparo ou provisório para o armazenamento de dados do coletor.

Atualmente, não é possível copiar dados entre dois armazenamentos de dados locais usando um armazenamento de preparo. Esperamos que essa opção esteja disponível em breve.

### <a name="configuration"></a>Configuração
Configure a definição **enableStaging** na Atividade de Cópia para especificar se deseja que os dados sejam preparados no Armazenamento de Blobs do Azure antes de carregá-los em um armazenamento de dados de destino. Quando você definir **enableStaging** para TRUE, especifique as propriedades adicionais listadas na tabela a seguir. Se não tiver um, também precisará criar um Armazenamento do Azure ou um serviço vinculado de assinatura de acesso compartilhado do Armazenamento para o preparo.

| Propriedade | Descrição | Valor padrão | Obrigatório |
| --- | --- | --- | --- |
| **enableStaging** |Especifique se você deseja copiar os dados por meio de um armazenamento de preparo provisório. |Falso |Não |
| **linkedServiceName** |Especifique o nome de um serviço vinculado [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service), que se refere à instância do Armazenamento que você usa como um armazenamento de preparo provisório. <br/><br/> Você não pode usar o Armazenamento com uma assinatura de acesso compartilhado para carregar dados no SQL Data Warehouse via PolyBase. Pode usar em todos os outros cenários. |N/D |Sim, quando **enableStaging** está definido para TRUE |
| **path** |Especifique o caminho do armazenamento de Blobs que você deseja que contenha os dados preparados. Se você não fornecer um caminho, o serviço criará um contêiner para armazenar os dados temporários. <br/><br/> Especifique um caminho somente se você usar o Armazenamento com uma assinatura de acesso compartilhado ou precisar que os dados temporários fiquem em um local específico. |N/D |Não |
| **enableCompression** |Especifica se os dados devem ser compactados antes de serem copiados para o destino. Essa configuração reduz o volume de dados que são transferidos. |Falso |Não |

Aqui está um exemplo de definição da Atividade de Cópia com as propriedades descritas na tabela anterior:

```json
"activities":[  
{
    "name": "Sample copy activity",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDBOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlSink"
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob",
            "path": "stagingcontainer/path",
            "enableCompression": true
        }
    }
}
]
```

### <a name="billing-impact"></a>Impacto de cobrança
Você é cobrado com base em duas etapas: duração da cópia e tipo de cópia.

* Quando você usa o preparo durante uma cópia de nuvem (copiar dados de um armazenamento de dados de nuvem para outro armazenamento de dados de nuvem), é cobrado pela [soma da duração da cópia das etapas 1 e 2] x [preço unitário da cópia de nuvem].
* Quando você usa o preparo durante uma cópia híbrida (copiar dados de um armazenamento de dados local para um armazenamento de dados de nuvem), é cobrado pela [duração da cópia híbrida] x [preço unitário da cópia híbrida] + [duração da cópia de nuvem] x [preço unitário da cópia de nuvem].

## <a name="performance-tuning-steps"></a>Etapas de ajuste do desempenho
Sugerimos que você realize estas etapas para ajustar o desempenho do serviço Data Factory com a Atividade de Cópia:

1. **Estabelecer uma linha de base**. Durante a fase de desenvolvimento, teste seu pipeline com a Atividade de Cópia em relação a um exemplo de dados representativo. Você pode usar o [modelo de divisão](data-factory-scheduling-and-execution.md) do Data Factory para limitar a quantidade de dados com a qual trabalha.

   Colete o tempo de execução e as características do desempenho usando o **Monitoramento e Gerenciamento de Aplicativos**. Escolha **Monitorar e Gerenciar** na página inicial do Data Factory. Na exibição em árvore, escolha o **conjunto de dados de saída**. Na lista **Janelas de Atividade** escolha a execução Atividade de Cópia. **Janelas de Atividade** listam a duração da Atividade de Cópia e o tamanho dos dados copiados. A taxa de transferência é listada no **Gerenciador de Janelas de Atividades**. Para saber mais sobre o aplicativo, consulte [Monitorar e gerenciar os pipelines do Azure Data Factory usando o Monitoramento e Gerenciamento de Aplicativos](data-factory-monitor-manage-app.md).

   ![Detalhes da execução da atividade](./media/data-factory-copy-activity-performance/mmapp-activity-run-details.png)

   Posteriormente neste artigo, você pode comparar o desempenho e a configuração do seu cenário com a [referência de desempenho](#performance-reference) da Atividade de Cópia de nossos testes.
2. **Diagnosticar e otimizar o desempenho**. Se o desempenho observado não atender às suas expectativas, você precisará identificar os afunilamentos do desempenho. Em seguida, otimize o desempenho para remover ou reduzir o efeito dos afunilamentos. Uma descrição completa do diagnóstico de desempenho está além do escopo deste artigo, mas aqui estão algumas considerações comuns:

   * Recursos de desempenho:
     * [Cópia paralela](#parallel-copy)
     * [Unidades de movimentação de dados de nuvem](#cloud-data-movement-units)
     * [Cópia em etapas](#staged-copy)
     * [Escalabilidade do Gateway de Gerenciamento de Dados](data-factory-data-management-gateway-high-availability-scalability.md)
   * [Gateway de gerenciamento de dados](#considerations-for-data-management-gateway)
   * [Fonte](#considerations-for-the-source)
   * [Coletor](#considerations-for-the-sink)
   * [Serialização e desserialização](#considerations-for-serialization-and-deserialization)
   * [Compactação](#considerations-for-compression)
   * [Mapeamento de coluna](#considerations-for-column-mapping)
   * [Outras considerações](#other-considerations)
3. **Expanda a configuração para todo o conjunto de dados**. Quando você estiver satisfeito com os resultados e o desempenho da execução, poderá expandir a definição e o período ativo do pipeline para cobrir todo o conjunto de dados.

## <a name="considerations-for-data-management-gateway"></a>Considerações do Gateway de Gerenciamento de Dados
**Configuração do gateway**: é recomendável usar um computador dedicado para hospedar o Gateway de Gerenciamento de Dados. Confira [Considerações para o uso do Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md#considerations-for-using-gateway).  

**Monitoramento e escala vertical/horizontal do gateway**: um único gateway lógico com um ou mais nós de gateway pode atender a várias execuções de Atividade de Cópia ao mesmo tempo. Você pode exibir um instantâneo quase em tempo real da utilização de recursos (CPU, memória, rede (entrada/saída) etc). em um computador de gateway, bem como o número de trabalhos simultâneos em execução versus o limite no portal do Azure, confira [Monitorar o gateway no portal](data-factory-data-management-gateway.md#monitor-gateway-in-the-portal). Caso haja uma grande necessidade de movimentação de dados híbrida, seja com um grande número de execuções simultâneas da atividade de cópia, seja com um grande volume de dados a serem copiados, considere a [escala vertical ou horizontal do gateway](data-factory-data-management-gateway-high-availability-scalability.md#scale-considerations) de modo a melhor utilizar seus recursos ou provisionar mais recursos a fim de fortalecer a cópia. 

## <a name="considerations-for-the-source"></a>Considerações para a origem
### <a name="general"></a>Geral
Verifique se o armazenamento de dados subjacente não está sobrecarregado por outras cargas de trabalho em execução nele ou em relação a ele.

Para os armazenamentos de dados da Microsoft, confira os [tópicos de monitoramento e ajuste](#performance-reference) específicos dos armazenamentos de dados que o ajudam a entender as características de desempenho do armazenamento, minimizar os tempos de resposta e maximizar a taxa de transferência.

Se você copiar os dados do armazenamento de Blobs para o SQL Data Warehouse, considere o uso do **PolyBase** para melhorar o desempenho. Veja [Usar o PolyBase para carregar dados para o Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para ver mais detalhes. Para ver um passo a passo com um caso de uso, veja [Load 1 TB into Azure SQL Data Warehouse under 15 minutes with Azure Data Factory](data-factory-load-sql-data-warehouse.md) (Carregar 1 TB no SQL Data Warehouse do Azure em menos de 15 minutos com o Azure Data Factory).

### <a name="file-based-data-stores"></a>Armazenamentos de dados baseado em arquivo
*(Inclui o armazenamento de Blobs, Data Lake Store, Amazon S3, sistemas de arquivos locais e HDFS local)*

* **Média de tamanho do arquivo e contagem de arquivos**: a Atividade de Cópia transfere os dados um arquivo por vez. Com a mesma quantidade de dados a ser movidos, a taxa de transferência geral será menor se os dados consistirem em muitos arquivos pequenos, em vez de alguns arquivos grandes, devido à fase de inicialização de cada arquivo. Portanto, se possível, combine arquivos pequenos em arquivos maiores para obter uma maior taxa de transferência.
* **Formato de arquivo e compactação**: para ver outras maneiras de melhorar o desempenho, consulte as seções [Considerações da serialização e desserialização](#considerations-for-serialization-and-deserialization) e [Considerações da compactação](#considerations-for-compression).
* Além disso, para o cenário do **sistema de arquivos local** no qual o uso do **Gateway de Gerenciamento de Dados** é obrigatório, consulte a seção [Considerações do Gateway de Gerenciamento de Dados](#considerations-for-data-management-gateway).

### <a name="relational-data-stores"></a>Armazenamentos de dados relacionais
*(Inclui o Banco de Dados SQL, SQL Data Warehouse, Amazon Redshift, bancos de dados do SQL Server e bancos de dados Oracle, MySQL, DB2, Teradata, Sybase e PostgreSQL, etc.)*

* **Padrão de dados**: o esquema da tabela afeta a taxa de transferência de cópia. Um tamanho de linha grande oferece um desempenho melhor do que o tamanho de linha pequeno para copiar a mesma quantidade de dados. O motivo é que o banco de dados pode recuperar de forma eficiente menos lotes de dados que contêm menos linhas.
* **Consulta ou procedimento armazenado**: otimize a lógica da consulta ou do procedimento armazenado especificado na origem da Atividade de Cópia para buscar os dados com mais eficiência.
* Para os **bancos de dados relacionais locais**, como o SQL Server e Oracle, que exigem o uso do **Gateway de Gerenciamento de Dados**, consulte a seção [Considerações do Gateway de Gerenciamento de Dados](#considerations-on-data-management-gateway).

## <a name="considerations-for-the-sink"></a>Considerações do coletor
### <a name="general"></a>Geral
Verifique se o armazenamento de dados subjacente não está sobrecarregado por outras cargas de trabalho em execução nele ou em relação a ele.

Para os armazenamentos de dados da Microsoft, consulte os [tópicos de monitoramento e ajuste](#performance-reference) específicos dos armazenamentos de dados. Esses tópicos o ajudarão a entender as características de desempenho do armazenamento de dados, minimizar os tempos de resposta e maximizar a taxa de transferência.

Se você estiver copiando os dados do **armazenamento de Blobs** para o **SQL Data Warehouse**, considere usar o **PolyBase** para melhorar o desempenho. Veja [Usar o PolyBase para carregar dados para o Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) para ver mais detalhes. Para ver um passo a passo com um caso de uso, veja [Load 1 TB into Azure SQL Data Warehouse under 15 minutes with Azure Data Factory](data-factory-load-sql-data-warehouse.md) (Carregar 1 TB no SQL Data Warehouse do Azure em menos de 15 minutos com o Azure Data Factory).

### <a name="file-based-data-stores"></a>Armazenamentos de dados baseado em arquivo
*(Inclui o armazenamento de Blobs, Data Lake Store, Amazon S3, sistemas de arquivos locais e HDFS local)*

* **Comportamento da cópia**: se você copiar os dados de um armazenamento de dados diferente com base em arquivos, a Atividade de Cópia terá três opções por meio da propriedade **copyBehavior**. Preserva a hierarquia, nivela a hierarquia ou mescla os arquivos. Preservar ou nivelar a hierarquia tem pouca ou nenhuma sobrecarga de desempenho, mas mesclar os arquivos faz aumentar a sobrecarga dele.
* **Formato do arquivo e compactação**: consulte as seções [Considerações da serialização e desserialização](#considerations-for-serialization-and-deserialization) e [Considerações da compactação](#considerations-for-compression) para ver outras maneiras de melhorar o desempenho.
* **Armazenamento de Blobs**: atualmente, o armazenamento de Blobs apenas suporta os blobs de blocos da transferência de dados otimizada e da taxa de transferência.
* Para os cenários dos **sistemas de arquivos locais** que exigem o uso do **Gateway de Gerenciamento de Dados**, consulte a seção [Considerações do Gateway de Gerenciamento de Dados](#considerations-for-data-management-gateway).

### <a name="relational-data-stores"></a>Armazenamentos de dados relacionais
*(Inclui o Banco de Dados SQL, SQL Data Warehouse, bancos de dados do SQL Server e bancos de dados Oracle)*

* **Comportamento de cópia**: dependendo das propriedades configuradas para **sqlSink**, a Atividade de Cópia gravará os dados no banco de dados de destino de maneiras diferentes.
  * Por padrão, o serviço de movimentação de dados usa a API de Cópia em Massa para inserir dados no modo de anexação, o que fornece o melhor desempenho.
  * Se você configurar um procedimento armazenado no coletor, o banco de dados aplicará os dados uma linha por vez, em vez de um carregamento em massa. O desempenho cai significativamente. Se o conjunto de dados for grande, quando aplicável, considere trocar para usar a propriedade **sqlWriterCleanupScript** .
  * Se você configurar a propriedade **sqlWriterCleanupScript** para cada execução da Atividade de Cópia, o serviço irá disparar o script, então, usará a API de Cópia em Massa para inserir os dados. Por exemplo, para substituir a tabela inteira pelos dados mais recentes, você pode especificar um script para excluir primeiro todos os registros, antes de carregar em massa os novos dados da origem.
* **Padrão de dados e tamanho do lote**:
  * O esquema da tabela afeta a taxa de transferência da cópia. Para copiar a mesma quantidade de dados, um tamanho de linha grande fornece um desempenho melhor do que um tamanho de linha pequeno, pois o banco de dados poderá confirmar com mais eficiência menos lotes de dados.
  * A Atividade de Cópia insere dados em uma série de lotes. Você pode definir o número de linhas em um lote usando a propriedade **writeBatchSize** . Se os dados tiverem linhas pequenas, você poderá definir a propriedade **writeBatchSize** com um valor mais alto para aproveitar uma sobrecarga de lote menor e uma taxa de transferência maior. Se o tamanho da linha de dados for grande, tenha cuidado ao aumentar **writeBatchSize**. Um valor alto pode levar a uma falha de cópia causada pela sobrecarga do banco de dados.
* Para os **bancos de dados relacionais locais**, como o SQL Server e o Oracle, que exigem o uso do **Gateway de Gerenciamento de Dados**, consulte a seção [Considerações do Gateway de Gerenciamento de Dados](#considerations-for-data-management-gateway).

### <a name="nosql-stores"></a>Repositórios NoSQL
*(Inclui o Armazenamento de tabelas e o Azure Cosmos DB)*

* Para o **Armazenamento de Tabelas**:
  * **Partição**: gravar os dados em partições intercaladas diminui drasticamente o desempenho. Classifique os dados de origem por chave de partição para que os dados sejam inseridos com eficiência em partições sucessivas ou ajuste a lógica para gravar os dados em uma única partição.
* Para o **Azure Cosmos DB**:
  * **Tamanho do lote**: a propriedade **writeBatchSize** define o número de solicitações paralelas para o serviço Azure Cosmos DB para criar documentos. Você pode esperar um melhor desempenho ao aumentar **writeBatchSize** porque mais solicitações paralelas são enviadas para o Azure Cosmos DB. No entanto, fique atento à limitação ao gravar no Azure Cosmos DB (a mensagem de erro é "A taxa de solicitação é grande"). Vários fatores podem causar a limitação, incluindo o tamanho do documento, o número de termos nos documentos e a política de indexação da coleção de destino. Para obter uma maior taxa de transferência de cópia, considere usar uma coleção melhor, por exemplo, S3.

## <a name="considerations-for-serialization-and-deserialization"></a>Considerações da serialização e desserialização
A serialização e desserialização podem acontecer quando o conjunto de dados de entrada ou saída é um arquivo. Consulte [Formatos de arquivo e compactação com suporte](data-factory-supported-file-and-compression-formats.md) para obter detalhes sobre os formatos de arquivo com suporte na Atividade de Cópia.

**Comportamento da cópia**:

* Copiar arquivos entre os armazenamentos de dados baseados em arquivos:
  * Quando os conjuntos de dados de entrada e saída têm a mesma ou nenhuma configuração do formato de arquivo, o serviço de movimentação de dados executa uma cópia binária sem nenhuma serialização ou desserialização. Você vê uma maior taxa de transferência em comparação com o cenário no qual as configurações do formato de arquivo de origem e do coletor são diferentes entre si.
  * Quando os conjuntos de dados de entrada e saída estão no formato de texto e apenas o tipo de codificação é diferente, o serviço de movimentação de dados apenas faz a conversão da codificação. Ele não faz nenhuma serialização e desserialização, causando uma sobrecarga do desempenho em comparação com uma cópia binária.
  * Quando os conjuntos de dados de entrada e saída têm formatos de arquivo diferentes ou configurações diferentes, como delimitadores, o serviço de movimentação de dados desserializa os dados de origem para transmitir, transformar, então, serializa no formato de saída desejado. Essa operação resulta em uma sobrecarga do desempenho mais significativa em comparação com outros cenários.
* Quando você copia os arquivos de/para um armazenamento de dados que não é baseado em arquivos (por exemplo, de um armazenamento baseado em arquivos para um armazenamento relacional), a etapa da serialização ou desserialização é necessária. Essa etapa resulta em uma sobrecarga significativa do desempenho.

**Formato de arquivo**: O formato de arquivo escolhido pode afetar o desempenho da cópia. Por exemplo, Avro é um formato binário compacto que armazena metadados com dados. Ele tem um amplo suporte no ecossistema do Hadoop para processar e consultar. No entanto, o Avro é mais caro para a serialização e a desserialização, o que resulta em uma menor taxa de transferência de cópia em comparação com o formato de texto. Faça a escolha do formato de arquivo em todo o fluxo de processamento de forma global. Inicie com qual forma os dados são armazenados, os armazenamentos dos dados de origem ou a extração dos sistemas externos; o melhor formato para o armazenamento, processamento analítico e consulta; e em qual formato os dados devem ser exportados para os data marts para as ferramentas de relatório e visualização. Às vezes, um formato de arquivo abaixo do ideal para o desempenho de leitura e gravação pode ser uma boa escolha ao considerar o processo analítico geral.

## <a name="considerations-for-compression"></a>Considerações da compactação
Quando o conjunto de dados de entrada ou saída é um arquivo, você pode definir a Atividade de Cópia para executar a compactação ou a descompactação conforme grava os dados no destino. Quando você escolher a compactação, faça uma compensação entre a entrada/saída (E/S) e a CPU. Compactar os dados tem um custo extra nos recursos de computação. Mas, por sua vez, reduz a E/S da rede e o armazenamento. Dependendo de seus dados, você poderá ver um aumento na taxa de transferência geral da cópia.

**Codec**: a atividade de cópia suporta gzip, bzip2 e os tipos de compactação Deflate. O Azure HDInsight pode consumir todos os três tipos de processamento. Cada codec de compactação tem suas vantagens. Por exemplo, o bzip2 tem a menor taxa de transferência de cópia, mas você obtém o melhor desempenho de consulta do Hive com o bzip2 porque pode dividi-lo para o processamento. O gzip é a opção mais equilibrada e é usado com mais frequência. Escolha o codec mais adequado para seu cenário completo.

**Nível**: você pode escolher entre duas opções para cada codec de compactação: compactado mais rápido e compactado ideal. A opção compactada mais rápida compacta os dados o mais rapidamente possível, mesmo que o arquivo resultante não tenha uma compactação ideal. A opção de compactação ideal leva mais tempo na compactação e produz uma quantidade mínima de dados. Você pode testar as duas opções para qual fornece o melhor desempenho no seu caso.

**Uma consideração**: para copiar uma grande quantidade de dados entre um armazenamento local e a nuvem, considere usar o armazenamento de blobs provisório com a compactação. Usar o armazenamento provisório é útil quando a largura de banda da rede corporativa e os serviços do Azure é o fator limitante e você deseja que os conjuntos de dados de entrada e saída estejam descompactados. Mais especificamente, é possível dividir uma atividade de cópia única em duas atividades de cópia. A primeira atividade de cópia copia da origem para um blob provisório ou de preparo no formato compactado. A segunda atividade de cópia copia os dados compactados a partir do preparo e descompacta enquanto grava no coletor.

## <a name="considerations-for-column-mapping"></a>Considerações do mapeamento de coluna
Você pode definir a propriedade **columnMappings** na Atividade de Cópia para mapear tudo ou um subconjunto de colunas de entrada para as colunas de saída. Depois do serviço de movimentação de dados ler os dados de origem, ele precisa executar o mapeamento de coluna nos dados antes de gravar os dados no coletor. Esse processamento extra reduz a taxa de transferência de cópia.

Se o armazenamento de dados de origem for de consulta, por exemplo, for um armazenamento relacional como o Banco de Dados SQL ou o SQL Server, ou se for um armazenamento NoSQL, como o Armazenamento de tabelas ou o Azure Cosmos DB, considere enviar a filtragem da coluna por push e reordenar a lógica para a propriedade **query**, em vez de usar o mapeamento de coluna. Dessa forma, a projeção ocorrerá enquanto o serviço de movimentação de dados lê os dados no armazenamento de dados de origem, onde é muito mais eficiente.

## <a name="other-considerations"></a>Outras considerações
Se o tamanho dos dados que você deseja copiar for grande, você poderá ajustar sua lógica de negócios para particionar ainda mais os dados usando o mecanismo de divisão no Data Factory. Em seguida, agende a Atividade de Cópia para ser executada com mais frequência para reduzir o tamanho dos dados para cada execução da Atividade.

Tenha cuidado com o número de conjuntos de dados e atividades de cópia que requerem o Data Factory para conectar o mesmo armazenamento de dados ao mesmo tempo. Vários trabalhos de cópia simultâneos pode restringir um armazenamento de dados e levar a um desempenho reduzido, repetições internas do trabalho de cópia e, em alguns casos, falhas de execução.

## <a name="sample-scenario-copy-from-an-on-premises-sql-server-to-blob-storage"></a>Cenário de exemplo: copiar de um SQL Server local para o armazenamento de Blobs
**Cenário:**um pipeline é criado para copiar os dados de um SQL Server local para um armazenamento de Blobs no formato CSV. Para acelerar o trabalho de cópia, os arquivos CSV devem ser compactados no formato bzip2.

**Análise e teste**: A taxa de transferência da Atividade de Cópia é menor que 2 MBps, que é muito mais lento do que o parâmetro de comparação de desempenho.

**Análise e ajuste do desempenho:**para solucionar o problema de desempenho, vejamos como os dados são processados e movidos.

1. **Ler dados:**o Gateway abre uma conexão com o SQL Server e envia a consulta. SQL Server responde enviando o fluxo de dados para o Gateway por meio da intranet.
2. **Serializar e compactar dados**: o Gateway serializa o fluxo de dados para o formato CSV e compacta os dados em um fluxo bzip2.
3. **Gravar dados**: o Gateway carrega o fluxo bzip2 no armazenamento de Blobs via Internet.

Como você pode ver, os dados estão sendo processados e movidos de forma sequencial e em fluxo: SQL Server > LAN > Gateway > WAN > armazenamento de Blobs. **O desempenho geral é limitado pela taxa de transferência mínima no pipeline**.

![Fluxo de dados](./media/data-factory-copy-activity-performance/case-study-pic-1.png)

Um ou mais dos seguintes fatores pode causar o afunilamento do desempenho:

* **Origem:**o próprio SQL Server tem uma baixa taxa de transferência devido às cargas pesadas.
* **Gateway de Gerenciamento de Dados**:
  * **LAN**: o Gateway está localizado longe do computador do SQL Server e tem uma conexão de baixa largura de banda.
  * **Gateway**: o Gateway atingiu suas limitações de carga para executar as seguintes operações:
    * **Serialização**: serializar o fluxo de dados para o formato CSV tem a taxa de transferência lenta.
    * **Compactação**: você escolheu um codec de compactação lenta (por exemplo, bzip2, que é 2.8 MBps com Core i7).
  * **WAN**: a largura de banda entre a rede corporativa e os serviços do Azure é baixa (por exemplo, T1 = 1,544 kbps; T2 = 6,312 kbps).
* **Coletor**: o armazenamento de Blobs tem baixa taxa de transferência. (Esse cenário é improvável porque seu SLA garante um mínimo de 60 MBps.)

Nesse caso, a compactação de dados bzip2 pode estar desacelerando todo o pipeline. Trocar para o codec de compactação gzip pode aliviar esse afunilamento.

## <a name="sample-scenarios-use-parallel-copy"></a>Cenários de exemplo: usar cópia paralela
**i cenário:** copiar 1.000 arquivos de 1 MB do sistema de arquivos local para o armazenamento de Blobs.

**Análise e ajuste do desempenho**: por exemplo, se você instalou o gateway em uma máquina com quatro núcleos, o Data Factory usará 16 cópias paralelas para mover os arquivos do sistema de arquivos para o Armazenamento de Blobs simultaneamente. Essa execução paralela deve resultar em alta taxa de transferência. Você também pode especificar explicitamente a contagem de cópias paralelas. Ao copiar muitos arquivos pequenos, as cópias paralelas ajudam muito a taxa de transferência usando os recursos com mais eficiência.

![Cenário 1](./media/data-factory-copy-activity-performance/scenario-1.png)

**Cenário II**: copiar 20 blobs de 500 MB cada do armazenamento de Blobs para o Data Lake Store Analytics, em seguida, ajustar o desempenho.

**Análise e ajuste do desempenho**: nesse cenário, o Data Factory copia os dados do armazenamento de Blobs para o Data Lake Store usando as unidades de movimentação de dados de cópia única (**parallelCopies** definida para 1) e de nuvem única. A taxa de transferência observada será semelhante à descrita na [seção de referência de desempenho](#performance-reference).   

![Cenário 2](./media/data-factory-copy-activity-performance/scenario-2.png)

**Cenário III**: o tamanho de arquivo Individual é maior que dezenas de MBs e o volume total é grande.

**Análise e ajuste do desempenho**: aumentar **parallelCopies** não resulta em um melhor desempenho da cópia devido às limitações de recursos de uma DMU de nuvem única. Em vez disso, você deve especificar mais DMUs de nuvem para obter mais recursos para realizar a movimentação de dados. Não especifique um valor para a propriedade **parallelCopies** . O Data Factory lida com o paralelismo para você. Nesse caso, se você definir **cloudDataMovementUnits** para 4, ocorrerá uma taxa de transferência de mais ou menos quatro vezes.

![Cenário 3](./media/data-factory-copy-activity-performance/scenario-3.png)

## <a name="reference"></a>Referência
Aqui estão as referências de monitoramento e ajuste do desempenho para alguns dos armazenamentos de dados com suporte:

* Armazenamento do Azure (incluindo o armazenamento de Blobs e o armazenamento de Tabelas): [metas de escalabilidade do Armazenamento do Azure](../../storage/common/storage-scalability-targets.md) e [Lista de verificação de escalabilidade e desempenho do Armazenamento do Azure](../../storage/common/storage-performance-checklist.md)
* Banco de dados SQL do Azure: é possível [monitorar o desempenho](../../sql-database/sql-database-single-database-monitor.md) e verificar o percentual DTU (unidade de transação do banco de dados)
* SQL Data Warehouse do Azure: Sua capacidade é medida em unidades de data warehouse (DWUs); consulte [Gerenciar poder de computação no SQL Data Warehouse do Azure (Visão Geral)](../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)
* Azure Cosmos DB: [níveis de desempenho no Azure Cosmos DB](../../cosmos-db/performance-levels.md)
* SQL Server local: [monitoramento e ajuste do desempenho](https://msdn.microsoft.com/library/ms189081.aspx)
* Servidor de arquivos local: [ajuste de desempenho para servidores de arquivos](https://msdn.microsoft.com/library/dn567661.aspx)
