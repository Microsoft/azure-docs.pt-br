---
title: Copiar dados de blobs de armazenamento do Azure para Data Lake Storage Gen1
description: Usar a ferramenta AdlCopy para copiar dados dos Azure Storage Blobs para o Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 84ee65b05af4393f49696875bda41df39e283d5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85980082"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Copiar dados de Azure Storage Blobs para o Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Como usar DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Como usar AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Data Lake Storage Gen1 fornece uma ferramenta de linha de comando, [AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358), para copiar dados das seguintes fontes:

* De blobs de armazenamento do Azure para Data Lake Storage Gen1. Você não pode usar AdlCopy para copiar dados de Data Lake Storage Gen1 para os blobs de armazenamento do Azure.
* Entre duas contas de Data Lake Storage Gen1.

Além disso, você pode usar a ferramenta AdlCopy de dois modos diferentes:

* **Autônomo**, em que a ferramenta usa recursos do Azure Data Lake Storage Gen1 para executar a tarefa.
* **Usando uma conta da Análise Data Lake**, em que as unidades atribuídas à sua conta da Análise Data Lake são usadas para executar a operação de cópia. Você pode usar essa opção quando quiser executar as tarefas de cópia de forma previsível.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Contêiner de **blobs de armazenamento do Azure** com alguns dados.
* **Uma conta do Data Lake Storage Gen1**. Para obter instruções sobre como criar uma, consulte Introdução [ao Azure data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Conta de data Lake Analytics (opcional)** -consulte [introdução ao Azure data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obter instruções sobre como criar uma conta de data Lake Analytics.
* **Ferramenta AdlCopy**. Instale a [ferramenta AdlCopy](https://www.microsoft.com/download/details.aspx?id=50358).

## <a name="syntax-of-the-adlcopy-tool"></a>Sintaxe da ferramenta AdlCopy

Use a sintaxe a seguir para trabalhar com a ferramenta AdlCopy

```console
AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern
```

Os parâmetros na sintaxe estão descritos abaixo:

| Opção | Descrição |
| --- | --- |
| Fonte |Especifica o local da fonte de dados no blob de armazenamento do Azure. A origem pode ser um contêiner de blob, um blob ou outra conta do Data Lake Storage Gen1. |
| Dest |Especifica o destino do Data Lake Storage Gen1 para onde copiar. |
| SourceKey |Especifica a chave de acesso de armazenamento para a fonte de blob de armazenamento do Azure. Esse parâmetro será necessário apenas se a origem for um contêiner de blob ou um blob. |
| Conta |**Opcional**. Use essa opção se você deseja usar a conta do Azure Data Lake Analytics para executar o trabalho de cópia. Se você usar a opção /Account na sintaxe, mas não especificar uma conta do Data Lake Analytics, o AdlCopy usará uma conta padrão para executar o trabalho. Além disso, se você usar essa opção, deverá adicionar a origem (Azure Storage Blob) e o destino (Azure Data Lake Storage Gen1) como fontes de dados para sua conta do Data Lake Analytics. |
| Unidades |Especifica o número de unidades do Data Lake Analytics que será usado para o trabalho de cópia. Essa opção é obrigatória se você usar a opção **/Account** para especificar a conta do Data Lake Analytics. |
| Padrão |Especifica um padrão regex que indica quais blobs ou arquivos copiar. O AdlCopy usa correspondência que diferencia maiúsculas de minúsculas. O padrão quando nenhum padrão é especificado é copiar todos os itens. Não é possível especificar diversos padrões para os arquivos. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Usar o AdlCopy (como autônomo) para copiar dados de um blob de armazenamento do Azure

1. Abra um prompt de comando e navegue até o diretório onde AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.
1. Execute o seguinte comando para copiar um blob específico do contêiner de origem para um Data Lake Storage Gen1:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>
    ```

    Por exemplo:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

    >[!NOTE]
    >A sintaxe acima especifica o arquivo a ser copiado para uma pasta na conta do Data Lake Storage Gen1. A ferramenta AdlCopy criará uma pasta se o nome da pasta especificado não existir.

    Você precisará inserir as credenciais da assinatura do Azure vinculadas à conta do Data Lake Storage Gen1. Você verá uma saída semelhante ao seguinte:

    ```output
    Initializing Copy.
    Copy Started.
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```

1. Você também pode copiar todos os blobs de um contêiner para a conta do Azure Data Lake Storage Gen1 usando o seguinte comando:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>  
    ```      

    Por exemplo:

    ```console
    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==
    ```

### <a name="performance-considerations"></a>Considerações sobre o desempenho

Se você estiver copiando de uma conta de Armazenamento de Blobs do Azure, poderá haver limitação durante a cópia do lado do Armazenamento de Blobs. Isso poderá degradar o desempenho do seu trabalho de cópia. Para saber mais sobre os limites do Armazenamento de Blobs do Azure, confira Limites de Armazenamento do Azure em [Limites de serviço e assinatura do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Usar AdlCopy (como autônomo) para copiar dados de outra conta do Data Lake Storage Gen1

Você também pode usar o AdlCopy para copiar dados entre duas contas do Data Lake Storage Gen1.

1. Abra um prompt de comando e navegue até o diretório onde AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.
1. Execute o comando a seguir para copiar um arquivo específico de uma conta do Data Lake Storage Gen1 para outra.

    ```console
    AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/
    ```

    Por exemplo:

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

   > [!NOTE]
   > A sintaxe acima especifica o arquivo a ser copiado para uma pasta na conta de destino do Data Lake Storage Gen1. A ferramenta AdlCopy criará uma pasta se o nome da pasta especificado não existir.
   >
   >

    Você precisará inserir as credenciais da assinatura do Azure vinculadas à conta do Data Lake Storage Gen1. Você verá uma saída semelhante ao seguinte:

    ```output
    Initializing Copy.
    Copy Started.|
    100% data copied.
    Finishing Copy.
    Copy Completed. 1 file copied.
    ```
1. O comando a seguir copia todos os arquivos de uma pasta específica na conta de origem do Data Lake Storage Gen1 para uma pasta na conta de destino do Data Lake Storage Gen1.

    ```console
    AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/
    ```

### <a name="performance-considerations"></a>Considerações sobre o desempenho

Ao usar o AdlCopy como uma ferramenta autônoma, a cópia é executada em recursos compartilhados e gerenciados pelo Azure. O desempenho que pode ser obtido nesse ambiente depende da carga do sistema e dos recursos disponíveis. Esse modo é melhor usado para transferências ad hoc pequenas. Não há necessidade de ajustar nenhum parâmetro ao usar AdlCopy como uma ferramenta autônoma.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Usar o AdlCopy (com a conta do Data Lake Analytics) para copiar dados

Você também pode usar sua conta do Data Lake Analytics para executar o trabalho AdlCopy para copiar dados de blobs do armazenamento do Azure para o Data Lake Storage Gen1. Você normalmente usará essa opção quando os dados a serem movidos estiverem no intervalo de gigabytes e terabytes e desejar uma taxa de transferência com desempenho melhor e previsível.

Para usar a conta do Data Lake Analytics com o AdlCopy para copiar de um Blob de Armazenamento do Azure, a origem (Blob de Armazenamento do Azure) deve ser adicionada como uma fonte de dados para sua conta do Data Lake Analytics. Para obter instruções sobre como adicionar fontes de dados adicionais para sua conta da Data Lake Analytics, confira [Gerenciar fontes de dados de conta da Data Lake Analytics](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Se estiver copiando de uma conta do Azure Data Lake Storage Gen1 como a origem usando uma conta do Data Lake Analytics, você não precisará associar a conta do Data Lake Storage Gen1 à conta do Data Lake Analytics. O requisito para associar o repositório de origem à conta do Data Lake Analytics é somente quando a origem for uma conta de Armazenamento do Azure.
>
>

Execute o seguinte comando para copiar de um Azure Storage Blob para uma conta do Data Lake Storage Gen1 usando a conta do Data Lake Analytics:

```console
AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>
```

Por exemplo:

```console
AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2
```

Da mesma maneira, execute o comando a seguir para copiar todos os arquivos de uma pasta específica na conta de origem do Data Lake Storage Gen1 para uma pasta na conta de destino do Data Lake Storage Gen1, usando a conta do Data Lake Analytics:

```console
AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2
```

### <a name="performance-considerations"></a>Considerações sobre o desempenho

Ao copiar dados no intervalo de terabytes, usar AdlCopy com sua própria conta do Azure Data Lake Analytics fornece um desempenho melhor e mais previsível. O parâmetro que deve ser ajustado é o número de unidades do Azure Data Lake Analytics a ser usado para o trabalho de cópia. Aumentar o número de unidades elevará o desempenho do seu trabalho de cópia. Cada arquivo a ser copiado pode usar uma unidade no máximo. Especificar mais unidades do que o número de arquivos que estão sendo copiados não aumentará o desempenho.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Usar o AdlCopy para copiar dados usando a correspondência de padrões

Nesta seção, você aprenderá a usar o AdlCopy para copiar dados de uma origem (em nosso exemplo abaixo usamos o Azure Storage Blob) para uma conta de destino do Data Lake Storage Gen1 usando a correspondência de padrões. Por exemplo, é possível usar as etapas abaixo para copiar todos os arquivos com extensão .csv do blob de origem para o destino.

1. Abra um prompt de comando e navegue até o diretório onde AdlCopy está instalado, normalmente `%HOMEPATH%\Documents\adlcopy`.
1. Execute o seguinte comando para copiar todos os arquivos com a extensão *.csv de um blob específico do contêiner de origem para um Data Lake Storage Gen1:

    ```console
    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv
    ```

    Por exemplo:

    ```console
    AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv
    ```

## <a name="billing"></a>Cobrança

* Se você usar a ferramenta AdlCopy de maneira autônoma, será cobrado pelos custos de egresso para movimentação de dados se a conta de armazenamento do Azure de origem não estiver na mesma região que a conta do Data Lake Storage Gen1.
* Se você usar a ferramenta AdlCopy com sua conta da Análise Data Lake, as [taxas de cobrança da Análise Data Lake](https://azure.microsoft.com/pricing/details/data-lake-analytics/) padrão serão aplicadas.

## <a name="considerations-for-using-adlcopy"></a>Considerações para o uso do AdlCopy

* O AdlCopy (para a versão 1.0.5) permite copiar dados de origens que, juntas, têm mais de milhares de arquivos e pastas. No entanto, se você encontrar problemas ao copiar um grande conjunto de grandes, poderá distribuir os arquivos/pastas em diferentes subpastas e usar o caminho para essas subpastas como a origem.

## <a name="performance-considerations-for-using-adlcopy"></a>Considerações de desempenho para o uso do AdlCopy

O AdlCopy dá suporte à cópia de dados que contém milhares de arquivos e pastas. No entanto, se você encontrar problemas ao copiar um grande conjunto de grandes, poderá distribuir os arquivos/pastas em subpastas menores. O AdlCopy foi desenvolvido para cópias ad hoc. Se você estiver tentando copiar dados de forma recorrente, considere usar o [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md), que fornece gerenciamento completo sobre as operações de cópia.

## <a name="release-notes"></a>Notas de versão

* 1.0.13 – se você estiver copiando dados para a mesma conta do Azure Data Lake Storage Gen1 entre vários comandos adlcopy, você não precisará mais digitar suas credenciais novamente para cada execução. O Adlcopy agora gravará essas informações em cache entre várias execuções.

## <a name="next-steps"></a>Próximas etapas

* [Proteger dados no Armazenamento do Data Lake Gen1](data-lake-store-secure-data.md)
* [Usar o Azure Data Lake Analytics com o Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usar o Azure HDInsight com o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
