---
title: Usar o rData Lake Store com Hadoop no HDInsight do Azure | Microsoft Docs
description: "Aprenda a consultar dados do Azure Data Lake Store e a armazenar os resultados da sua análise."
keywords: "armazenamento de blobs, hdfs, dados estruturados, dados não estruturados, data lake store"
services: hdinsight,storage
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: jgao
ms.openlocfilehash: 28a836aff65636ef0031ac63f633d746436d7e4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-data-lake-store-with-azure-hdinsight-clusters"></a>Usar o Data Lake Store com clusters Azure HDInsight

Para analisar dados no cluster HDInsight, você pode armazenar os dados no [Armazenamento do Azure](../storage/common/storage-introduction.md), no [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) ou em ambos. As duas opções de armazenamento permitem que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário.

Neste artigo, você aprenderá como o Data Lake Store funciona com clusters do HDInsight. Para saber como o Armazenamento do Azure funciona com clusters HDInsight, consulte [Usar o Armazenamento do Azure com clusters do Azure HDInsight](hdinsight-hadoop-use-blob-storage.md). Para saber mais sobre a criação de um cluster HDInsight, veja [Criar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

> [!NOTE]
> O Data Lake Store é sempre acessado por meio de um canal seguro, portanto não há nenhum nome do esquema de sistema de arquivos `adls`. Use sempre `adl`.
> 
> 

## <a name="availabilities-for-hdinsight-clusters"></a>Disponibilidade para clusters do HDInsight

O Hadoop dá suporte a uma noção do sistema de arquivos padrão. O sistema de arquivos padrão implica esquema e autoridade padrões. Ele também pode ser usado para resolver caminhos relativos. Durante o processo de criação do cluster HDInsight, você pode especificar um contêiner de blobs no Armazenamento do Azure como o sistema de arquivos padrão ou, com o HDInsight 3.5 e versões posteriores, você pode selecionar o Armazenamento do Azure ou o Azure Data Lake Store como o sistema de arquivos padrão. 

Os clusters HDInsight podem usar o Data Lake Store de duas maneiras:

* Como armazenamento padrão
* Como armazenamento adicional, com o Azure Storage Blob como o armazenamento padrão.

A partir de agora, somente alguns tipos/versões do cluster HDInsight dão suporte ao uso do Data Lake Store como armazenamento padrão e contas de armazenamento adicionais:

| Tipo de cluster HDInsight | Data Lake Store como armazenamento padrão | Data Lake Store como armazenamento adicional| Observações |
|------------------------|------------------------------------|---------------------------------------|------|
| HDInsight versão 3.6 | Sim | Sim | |
| HDInsight versão 3.5 | Sim | Sim | Com exceção do HBase|
| HDInsight versão 3.4 | Não | Sim | |
| HDInsight versão 3.3 | Não | Não | |
| HDInsight versão 3.2 | Não | Sim | |
| HDInsight Premium (camada)| Não | Não | |
| Storm | | |Você pode usar o Data Lake Store para gravar os dados de uma topologia do Storm. Também é possível usar o Data Lake Store para dados de referência que, em seguida, podem ser lidos por uma topologia do Storm.|

O uso do Data Lake Store como uma conta de armazenamento adicional não afeta o desempenho nem a capacidade de ler ou gravar do cluster para o armazenamento do Azure.


## <a name="use-data-lake-store-as-default-storage"></a>Usar o Data Lake Store como armazenamento padrão

Quando HDInsight é implantado com o Data Lake Store como o armazenamento padrão, os arquivos relacionados a cluster são armazenados no Data Lake Store no seguinte local:

    adl://mydatalakestore/<cluster_root_path>/

em que `<cluster_root_path>` é o nome de uma pasta que você cria no Data Lake Store. Ao especificar um caminho raiz para cada cluster, você pode usar a mesma conta do Data Lake Store para mais de um cluster. Portanto, você terá uma configuração em que:

* O Cluster1 pode usar o caminho `adl://mydatalakestore/cluster1storage`
* O Cluster2 pode usar o caminho `adl://mydatalakestore/cluster2storage`

Observe que os dois clusters usam a mesma conta **mydatalakestore** do Data Lake Store. Cada cluster tem acesso ao seu próprio sistema de arquivos raiz no Data Lake Store. A experiência de implantação do Portal do Azure em particular, solicita que você use um nome de pasta como **/clusters/\<clustername>** para o caminho raiz.

Para poder usar um Data Lake Store como armazenamento padrão, você deve conceder o acesso à entidade do serviço para os seguintes caminhos:

- A raiz da conta do Data Lake Store.  Por exemplo: adl://mydatalakestore/.
- A pasta para todas as pastas de cluster.  Por exemplo: adl://mydatalakestore/clusters.
- A pasta para o cluster.  Por exemplo: adl://mydatalakestore/clusters/cluster1storage.

Para saber mais sobre como criar a entidade de segurança e conceder acesso, consulte [Configurar acesso ao Data Lake Store](#configure-data-lake-store-access).


## <a name="use-data-lake-store-as-additional-storage"></a>Usar o Data Lake Store como armazenamento adicional

Você também pode usar o Data Lake Store como armazenamento adicional para o cluster. Nesses casos, o armazenamento padrão do cluster pode ser um Azure Storage Blob ou uma conta do Data Lake Store. Se você estiver executando trabalhos de HDInsight com os dados armazenados no Data Lake Store como armazenamento adicional, use o caminho totalmente qualificado para os arquivos. Por exemplo:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Observe que agora não há **cluster_root_path** na URL. Isso ocorre porque o Data Lake Store não é um armazenamento padrão nesse caso, portanto, tudo o que você precisa fazer é fornecer o caminho para os arquivos.

Para poder usar um Data Lake Store como armazenamento adicional, você só precisa conceder acesso à entidade principal para os caminhos onde os arquivos são armazenados.  Por exemplo:

    adl://mydatalakestore.azuredatalakestore.net/<file_path>

Para saber mais sobre como criar a entidade de segurança e conceder acesso, consulte [Configurar acesso ao Data Lake Store](#configure-data-lake-store-access).


## <a name="use-more-than-one-data-lake-store-accounts"></a>Usar mais de uma conta do Data Lake Store

Adicionar uma conta do Data Lake Store como adicional e adicionar mais de uma conta do Data Lake Store é feito dando ao cluster do HDInsight permissão sobre dados em uma ou mais contas do Data Lake Store. Consulte [Configurar acesso ao Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Configurar acesso ao Data Lake Store

Para configurar o acesso ao Data Lake Store do seu cluster HDInsight, você deve ter uma entidade de serviço do Azure AD (AzureActive directory). Somente um administrador do Azure AD pode criar uma entidade de serviço. A entidade de serviço deve ser criada com um certificado. Para obter mais informações, consulte [Configurar acesso ao Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#configure-data-lake-store-access) e [Criar entidade de serviço com certificado autoassinado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

> [!NOTE]
> Se você pretende usar o Azure Data Lake Store como armazenamento adicional para o cluster HDInsight, é altamente recomendável que faça isso ao criar o cluster, conforme descrito neste artigo. Adicionar o Azure Data Lake Store como mais armazenamento para um cluster HDInsight existente é um processo complicado e propenso a erros.
>

## <a name="access-files-from-the-cluster"></a>Acessar arquivos do cluster

Há várias maneiras para acessar os arquivos no Data Lake Store em um cluster HDInsight.

* **Usando o nome totalmente qualificado**. Com essa abordagem, é necessário fornecer o caminho completo para o arquivo que você deseja acessar.

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/<file_path>

* **Usando o formato de caminho encurtado**. Com essa abordagem, você substitui o caminho até a raiz do cluster com adl:///. Portanto, no exemplo acima, é possível substituir `adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/` por `adl:///`.

        adl:///<file path>

* **Usando o caminho relativo**. Com essa abordagem, você só fornece o caminho relativo para o arquivo que deseja acessar. Por exemplo, se o caminho completo para o arquivo é:

        adl://mydatalakestore.azuredatalakestore.net/<cluster_root_path>/example/data/sample.log

    Você pode acessar o mesmo arquivo sample.log usando esse caminho relativo em vez disso.

        /example/data/sample.log

## <a name="create-hdinsight-clusters-with-access-to-data-lake-store"></a>Criar um cluster HDInsight com acesso ao Data Lake Store

Use os links a seguir para obter instruções detalhadas sobre como criar clusters HDInsight com acesso ao Data Lake Store.

* [Usando o Portal](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
* [Usando o PowerShell (com o Data Lake Store como o armazenamento padrão)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
* [Usando o PowerShell (com o Data Lake Store como o armazenamento adicional)](../data-lake-store/data-lake-store-hdinsight-hadoop-use-powershell.md)
* [Usando modelos do Azure](../data-lake-store/data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar o Azure Data Lake Store, compatível com HDFS, com o HDInsight. Isso permite que você crie soluções de aquisição de dados para arquivamento de longo prazo escalonáveis e use o HDInsight para desbloquear as informações nos dados armazenados estruturados e não estruturados.

Para obter mais informações, confira:

* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Introdução ao Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md)
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Usar Assinaturas de Acesso Compartilhado do Armazenamento do Azure para restringir o acesso a dados com o HDInsight][hdinsight-use-sas]

[hdinsight-use-sas]: hdinsight-storage-sharedaccesssignature-permissions.md
[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]:../storage/common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png  
