---
title: Usar o portal do Azure para criar clusters Azure HDInsight com o Data Lake Store | Microsoft Docs
description: Usar o portal do Azure para criar e usar clusters HDInsight com o Azure Data Lake Store
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/14/2017
ms.author: nitinme
ms.openlocfilehash: 9dd56efb89e07ea61ae431d1ea2accd721cd6502
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>Criar clusters HDInsight com o Data Lake Store usando o portal do Azure
> [!div class="op_single_selector"]
> * [Usar o Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usar o PowerShell (para armazenamento padrão)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usar o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usar o Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Saiba como usar o portal do Azure para criar um cluster HDInsight com uma conta do Azure Data Lake Store como o armazenamento padrão ou um armazenamento adicional. Embora armazenamento adicional seja opcional para um cluster HDInsight, é recomendável armazenar seus dados comerciais em contas de armazenamento adicionais.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, verifique se você atendeu aos seguintes requisitos:

* **Uma assinatura do Azure**. Acesse [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do repositório Azure Data Lake**. Siga as instruções em [Introdução ao Azure Data Lake Store usando o portal do Azure](data-lake-store-get-started-portal.md). Você também deve criar uma pasta raiz na conta.  Neste tutorial, uma pasta raiz chamada __/clusters__ é usada.
* **Uma entidade de serviço do Azure Active Directory**. Este tutorial fornece instruções sobre como criar uma entidade de serviço no Azure AD (Azure Active Directory). No entanto, para criar uma entidade de serviço, você deve ser um administrador do Azure AD. Se você for um administrador, ignore esse pré-requisito e continue com o tutorial.

    >[!NOTE]
    >Você poderá criar uma entidade de serviço somente se for um administrador do Azure AD. O administrador do Azure AD deverá criar uma entidade de serviço antes que você possa criar um cluster HDInsight com o Data Lake Store. Além disso, a entidade de serviço deve ser criada com um certificado, conforme descrito em [Criar uma entidade de serviço com certificado](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>Crie um cluster HDInsight

Nesta seção, você cria um cluster HDInsight com contas do Data Lake Store como o padrão ou o armazenamento adicional. Este artigo concentra-se somente a parte da configuração de contas do Data Lake Store.  Para obter informações gerais sobre a criação do cluster, consulte [Criar clusters do Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-store-as-default-storage"></a>Criar um cluster com o Data Lake Store como armazenamento padrão

**Para criar um cluster HDInsight com um Data Lake Store como a conta de armazenamento padrão**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Siga [Criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter as informações gerais sobre a criação de clusters do HDInsight.
3. Na folha **Armazenamento**, em **Tipo de armazenamento primário**, selecione **Data Lake Store** e, em seguida, insira as seguintes informações:

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Adicionar entidade de serviço ao cluster do HDInsight")

    - **Selecionar conta do Data Lake Store**: selecione uma conta existente do Data Lake Store. Uma conta existente do Data Lake Store é necessária.  Consulte [Pré-requisitos](#prereuisites).
    - **Caminho raiz**: insira um caminho em que os arquivos específicos de cluster deverão ser armazenados. Na captura de tela, é __/clusters/myhdiadlcluster/__, em que a pasta __/clusters__ deve existir e o Portal cria a pasta *myhdicluster*.  O *myhdicluster* é o nome do cluster.
    - **Acesso ao Data Lake Store**: configure o acesso entre a conta do Data Lake Store e o cluster HDInsight. Para obter instruções, consulte [Configurar acesso ao Data Lake Store](#configure-data-lake-store-access).
    - **Contas de armazenamento adicionais**: adicione Contas de Armazenamento do Azure como contas de armazenamento adicionais para o cluster. Para adicionar mais Data Lake Stores, dê ao cluster permissões sobre os dados em mais contas do Data Lake Store enquanto configura uma conta do Data Lake Store como o tipo de armazenamento principal. Consulte [Configurar acesso ao Data Lake Store](#configure-data-lake-store-access).

4. Em **Acesso ao Data Lake Store**, clique em **Selecionar** e continue com a criação do cluster, conforme descrito em [Criar clusters Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-store-as-additional-storage"></a>Criar um cluster com o Data Lake Store como armazenamento adicional

As instruções a seguir criam um cluster HDInsight com uma conta de Armazenamento do Azure como o armazenamento padrão e uma conta do Data Lake Store como um armazenamento adicional.
**Para criar um cluster HDInsight com um Data Lake Store como a conta de armazenamento padrão**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Siga [Criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter as informações gerais sobre a criação de clusters do HDInsight.
3. Na folha **Armazenamento**, em **Tipo de armazenamento primário**, selecione **Armazenamento do Azure** e, em seguida, insira as seguintes informações:

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Adicionar entidade de serviço ao cluster do HDInsight")

    - **Método de seleção**: use uma das seguintes opções:

        * Para especificar uma conta de armazenamento que faz parte de sua assinatura do Azure, selecione **Minhas assinaturas** e, em seguida, selecione a conta de armazenamento.
        * Para especificar uma conta de armazenamento que está fora de sua assinatura do Azure, selecione **Chave de acesso** e, em seguida, forneça as informações da conta de armazenamento externa.

    - **Contêiner padrão**: use o valor padrão ou especifique seu próprio nome.

    - Contas de Armazenamento adicionais: adicione mais contas de Armazenamento do Azure como armazenamento adicional.
    - Acesso ao Data Lake Store: configure o acesso entre a conta do Data Lake Store e o cluster HDInsight. Para obter instruções, consulte [Configurar acesso ao Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Configurar acesso ao Data Lake Store 

Nesta seção, você configura o acesso ao Data Lake Store de clusters do HDInsight usando uma entidade de serviço do Azure Active Directory. 

### <a name="specify-a-service-principal"></a>Especificar uma entidade de serviço

No portal do Azure, você pode usar uma entidade de serviço existente ou criar uma nova.

**Para criar uma entidade de serviço do portal do Azure**

1. Clique em **Acesso ao Data Lake Store** na folha Repositório.
2. Na folha **Acesso ao Data Lake Store**, clique em **Criar novo**.
3. Clique em **Entidade de Serviço** e siga as instruções para criar uma entidade de serviço.
4. Baixe o certificado se você optar por usá-lo novamente no futuro. É útil baixar o certificado se você desejar usar a mesma entidade de serviço ao criar clusters HDInsight adicionais.

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Adicionar entidade de serviço ao cluster do HDInsight")

4. Clique em **Acessar** para configurar o acesso à pasta.  Consulte [Configurar permissões de arquivo](#configure-file-permissions).


**Para usar uma entidade de serviço existente do portal do Azure**

1. Clique em **Acesso ao Data Lake Store**.
1. Na folha **Acesso ao Data Lake Store**, clique em **Usar existente**.
2. Clique em **Entidade de Serviço** e, em seguida, selecione uma entidade de serviço. 
3. Carregue o certificado (arquivo .pfx) associado à entidade de serviço selecionada e insira a senha do certificado.

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Adicionar entidade de serviço ao cluster do HDInsight")

4. Clique em **Acessar** para configurar o acesso à pasta.  Consulte [Configurar permissões de arquivo](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Configurar permissões de arquivo

As configurações são diferentes dependendo de se a conta é usada como o armazenamento padrão ou uma conta de armazenamento adicional:

- Usado como o armazenamento padrão

    - permissão no nível raiz da conta do Data Lake Store
    - permissão no nível raiz do armazenamento de cluster HDInsight. Por exemplo, a pasta __/clusters__ usada anteriormente no tutorial.
- Usar como um armazenamento adicional

    - Permissões nas pastas em que você precisa de acesso ao arquivo.

**Para atribuir a permissão no nível raiz da conta do Data Lake Store**

1. Na folha **Acesso do Data Lake Store**, clique em **Acessar**. A folha **Selecionar permissões de arquivo** é aberta. Ela lista todas as contas do Data Lake Store na assinatura.
2. Focalize o mouse (não clique) no nome da conta do Data Lake Store para tornar a caixa de seleção visível, então selecione-a.

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Adicionar entidade de serviço ao cluster do HDInsight")

  Por padrão, as opções __LER__, __GRAVAR__ E __EXECUTAR__ estão selecionadas.

3. Clique em **Selecionar** na parte inferior da página.
4. Clique em **Executar** para atribuir permissão.
5. Clique em **Concluído**.

**Para atribuir a permissão no nível de raiz do cluster HDInsight**

1. Na folha **Acesso do Data Lake Store**, clique em **Acessar**. A folha **Selecionar permissões de arquivo** é aberta. Ela lista todas as contas do Data Lake Store na assinatura.
1. Na folha **Selecionar permissões de arquivo**, clique no nome do Data Lake Store para mostrar seu conteúdo.
2. Selecione a raiz de armazenamento de cluster HDInsight marcando a caixa de seleção à esquerda da pasta. De acordo com a captura de tela anterior, a raiz de armazenamento de cluster é a pasta __/clusters__ especificada durante a seleção do Data Lake Store como armazenamento padrão.
3. Defina as permissões na pasta.  Por padrão, as opções ler, gravar e executar estão selecionadas.
4. Clique em **Selecionar** na parte inferior da página.
5. Clique em **Executar**.
6. Clique em **Concluído**.

Se você estiver usando o Data Lake Store como armazenamento adicional, deverá atribuir permissões somente para as pastas que você deseja acessar por meio do cluster HDInsight. Por exemplo, na captura de tela abaixo, você fornece acesso apenas à pasta **hdiaddonstorage** em uma conta do Data Lake Store.

![Atribuir permissões da entidade de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Atribuir permissões da entidade de serviço ao cluster HDInsight")


## <a name="verify-cluster-set-up"></a>Verificar a configuração do cluster

Após a conclusão da configuração do cluster, na folha do cluster, verifique os resultados executando uma ou ambas as etapas a seguir:

* Para verificar se o armazenamento associado do cluster é a conta do Data Lake Store especificada, clique em **Contas de armazenamento** no painel esquerdo.

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Adicionar entidade de serviço ao cluster do HDInsight")

* Para verificar se a entidade de serviço está associada ao cluster HDInsight corretamente, clique em **Acesso ao Data Lake Store** no painel esquerdo.

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Adicionar entidade de serviço ao cluster do HDInsight")


## <a name="examples"></a>Exemplos

Depois de configurar o cluster com o Data Lake Store como o armazenamento, consulte estes exemplos de como usar o cluster HDInsight para analisar os dados armazenados no Data Lake Store.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>Executar uma consulta do Hive nos dados de um Data Lake Store (como armazenamento primário)

Para executar uma consulta do Hive, use a interface de exibições do Hive no portal do Ambari. Para obter instruções sobre como usar as exibições do Hive no Ambari, consulte [Usar a exibição do Hive com o Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-use-hive-ambari-view.md).

Ao trabalhar com os dados em um Data Lake Store, há algumas cadeias de caracteres a serem alteradas.

Se você usar, por exemplo, o cluster criado com o Data Lake Store como armazenamento primário, o caminho para os dados será: *adl://<data_lake_store_account_name>/azuredatalakestore.net/path/to/file*. Uma consulta do Hive para criar uma tabela com base em dados de exemplo armazenados na conta do Data Lake Store é parecida com a seguinte instrução:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Descrições:
* `adl://hdiadlstorage.azuredatalakestore.net/` é a raiz da conta do Data Lake Store.
* `/clusters/myhdiadlcluster` é a raiz dos dados do cluster especificada durante a criação do cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` é a localização do arquivo de exemplo usado na consulta.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>Executar uma consulta do Hive nos dados de um Data Lake Store (como armazenamento adicional)

Se o cluster criado usar o armazenamento de Blobs como armazenamento padrão, os dados de exemplo não estarão contidos na conta do Azure Data Lake Store usada como armazenamento adicional. Nesse caso, primeiro transfira os dados do armazenamento de Blobs para o Data Lake Store e, em seguida, execute as consultas, conforme mostrado no exemplo anterior.

Para obter informações sobre como copiar dados do armazenamento de Blobs para um Data Lake Store, consulte os seguintes artigos:

* [Usar o Distcp para copiar dados entre os Azure Storage blobs e o Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Usar o AdlCopy para copiar dados de Azure Storage blobs para o Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>Usar o Data Lake Store com o cluster Spark
É possível usar um cluster Spark para executar trabalhos do Spark nos dados armazenados em um Data Lake Store. Para obter mais informações, consulte [Usar um cluster Spark HDInsight para analisar dados no Data Lake Store](../hdinsight/hdinsight-apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Usar o Repositório Data Lake em uma topologia do Storm
Você pode usar o Repositório Data Lake para gravar os dados de uma topologia do Storm. Para obter instruções sobre como reproduzir esse cenário, confira [Usar o Azure Data Lake Store com o Apache Storm com HDInsight](../hdinsight/hdinsight-storm-write-data-lake-store.md).

## <a name="see-also"></a>Confira também
* [PowerShell: Criar um cluster HDInsight para usar o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
