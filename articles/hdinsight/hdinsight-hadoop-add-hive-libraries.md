---
title: Bibliotecas de Apache Hive durante a criação do cluster-Azure HDInsight
description: Saiba como adicionar bibliotecas de Apache Hive (arquivos jar) a um cluster HDInsight durante a criação do cluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: b6695e5e985a30d6f912095225c4899e1c910e34
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945947"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Adicionar bibliotecas Apache Hive personalizadas ao criar seu cluster HDInsight

Saiba como carregar previamente [Apache Hive](https://hive.apache.org/) bibliotecas no HDInsight. Este documento contém informações sobre como usar uma Ação de script para pré-carregar bibliotecas durante a criação do cluster. As bibliotecas adicionadas usando as etapas deste documento estão globalmente disponíveis no hive – não há necessidade de usar [Add jar](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) para carregá-las.

## <a name="how-it-works"></a>Como ele funciona

Ao criar um cluster, você pode usar uma ação de script para modificar nós de cluster à medida que eles são criados. O script neste documento aceita um único parâmetro, que é a localização das bibliotecas. Essa localização deve estar em uma conta de Armazenamento do Azure e as bibliotecas devem ser armazenadas como arquivos jar.

Durante a criação do cluster, o script enumera os arquivos, copia-os para o diretório `/usr/lib/customhivelibs/` nos nós de cabeçalho e de trabalho e adiciona-os à propriedade `hive.aux.jars.path` no arquivo `core-site.xml`. Em clusters baseados em Linux, ele também atualiza o arquivo `hive-env.sh` com o local dos arquivos.

O uso da ação de script neste artigo torna as bibliotecas disponíveis ao usar um cliente do hive para **WebHCat** e **HiveServer2**.

## <a name="the-script"></a>O script

**Local do script**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Requisitos

* Os scripts devem ser aplicados nos **Nós do cabeçalho** e nos **Nós de trabalho**.

* Os jars que você deseja instalar devem estar armazenados no Armazenamento de Blobs do Azure em um **único contêiner**.

* A conta de armazenamento contendo a biblioteca de arquivos jar **deve** ser vinculada ao cluster HDInsight durante a criação. Ele deve ser a conta de armazenamento padrão ou uma conta adicionada por meio de __configurações de conta de armazenamento__.

* O caminho WASB para o contêiner deve ser especificado como um parâmetro para a Ação de Script. Por exemplo, se os jars estivessem armazenados em um contêiner denominado **libs** em uma conta de armazenamento denominada **mystorage**, o parâmetro seria `wasbs://libs@mystorage.blob.core.windows.net/`.

  > [!NOTE]  
  > Este documento supõe que você já criou uma conta de armazenamento, um contêiner de blobs e carregou os arquivos nele.
  >
  > Se você ainda não criou uma conta de armazenamento, faça isso usando o [portal do Azure](https://portal.azure.com). Lá, você pode usar um utilitário como o [Gerenciador de Armazenamento do Azure](https://storageexplorer.com/) para criar um contêiner na conta e carregar arquivo nele.

## <a name="create-a-cluster-using-the-script"></a>Criar um cluster usando o script

1. Inicie o provisionamento de um cluster usando as etapas em [provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md), mas não conclua o provisionamento. Você também pode usar o Azure PowerShell ou o SDK do .NET do HDInsight para criar um cluster usando esse script. Para obter mais informações sobre como usar esses métodos, consulte [Personalizar clusters HDInsight com Ações de Script](hdinsight-hadoop-customize-cluster-linux.md). Para o portal do Azure, na guia **configuração + preço** , selecione a **ação + adicionar script**.

1. Para **armazenamento**, se a conta de armazenamento que contém a biblioteca de arquivos jar for diferente da conta usada para o cluster, conclua **as contas de armazenamento adicionais**.

1. Para **ações de script**, forneça as seguintes informações:

    |Propriedade |Valor |
    |---|---|
    |Tipo de script|- Personalizado|
    |Nome|Bibliotecas |
    |URI do script Bash|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Tipo(s) de nó|Cabeçalho, trabalhador|
    |Parâmetros|Insira o endereço WASB para o contêiner e a conta de armazenamento que contém os jars. Por exemplo, `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > Para Apache Spark 2,1, use este URI de script bash: `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh` .

1. Continue a provisionar o cluster como descrito em [Provisionar clusters HDInsight no Linux](hdinsight-hadoop-provision-linux-clusters.md).

Quando a criação do cluster for concluída, você poderá usar os jars adicionados por meio desse script do hive sem precisar usar a `ADD JAR` instrução.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como trabalhar com o Hive, consulte [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
