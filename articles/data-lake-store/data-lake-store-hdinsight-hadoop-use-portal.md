---
title: Use o portal do Azure para criar clusters do HDInsight do Azure com o Armazenamento de Data Lake do Azure Gen1 | Microsoft Docs
description: Use o portal do Azure para criar e usar clusters do HDInsight com o Armazenamento de Data Lake do Azure Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 6f9064c6027499fff3a8551ee60722cd66c54dc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60877498"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Criar clusters do HDInsight com o Gen1 do Azure Data Lake Storage usando o portal do Azure
> [!div class="op_single_selector"]
> * [Usar o Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usar o PowerShell (para armazenamento padrão)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usar o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usar o Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Aprenda a usar o portal do Azure para criar um cluster do HDInsight com uma conta do Azure Data Lake Storage Gen1 como o armazenamento padrão ou um armazenamento adicional. Embora armazenamento adicional seja opcional para um cluster HDInsight, é recomendável armazenar seus dados comerciais em contas de armazenamento adicionais.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, verifique se você atendeu aos seguintes requisitos:

* **Uma assinatura do Azure**. Acesse [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Data Lake Storage Gen1**. Siga as instruções do [Introdução ao Azure Data Lake armazenamento Gen1, usando o portal do Azure](data-lake-store-get-started-portal.md). Você também deve criar uma pasta raiz na conta.  Neste tutorial, uma pasta raiz chamada __/clusters__ é usada.
* **Uma entidade de serviço do Azure Active Directory**. Este tutorial fornece instruções sobre como criar uma entidade de serviço no Azure AD (Azure Active Directory). No entanto, para criar uma entidade de serviço, você deve ser um administrador do Azure AD. Se você for um administrador, ignore esse pré-requisito e continue com o tutorial.

    >[!NOTE]
    >Você poderá criar uma entidade de serviço somente se for um administrador do Azure AD. O administrador do AD do Azure AD deve criar uma entidade de serviço antes de criar um cluster do HDInsight com o Data Lake Storage Gen1. Além disso, a entidade de serviço deve ser criada com um certificado, conforme descrito em [Criar uma entidade de serviço com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>Crie um cluster HDInsight

Nesta seção, você cria um cluster do HDInsight com contas do Data Lake Storage Gen1 como o padrão ou o armazenamento adicional. Este artigo enfoca somente a parte da configuração de contas do Data Lake Storage Gen1.  Para obter informações gerais sobre a criação do cluster, consulte [Criar clusters do Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Criar um cluster com o Data Lake Storage Gen1 como armazenamento padrão

**Para criar um cluster do HDInsight com uma conta do Data Lake Storage Gen1 como a conta de armazenamento padrão**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Siga [Criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter as informações gerais sobre a criação de clusters do HDInsight.
3. Na folha **Armazenamento**, em **Tipo de armazenamento principal**, selecione **Armazenamento do Data Lake do Azure Gen1** e insira as seguintes informações:

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Adicionar entidade de serviço ao cluster do HDInsight")

    - **Selecionar a conta do Data Lake Store**: selecione uma conta existente do Data Lake Storage Gen1. Uma conta existente do Data Lake armazenamento Gen1 é necessária.  Consulte [Pré-requisitos](#prerequisites).
    - **Caminho raiz**: insira o caminho no qual os arquivos específicos de cluster deverão ser armazenados. Na captura de tela, é __/clusters/myhdiadlcluster/__, em que a pasta __/clusters__ deve existir e o Portal cria a pasta *myhdicluster*.  O *myhdicluster* é o nome do cluster.
    - **Acesso ao Data Lake Store**: configure o acesso entre a conta do Data Lake Storage Gen1 e o cluster do HDInsight. Para obter instruções, consulte Configurar o acesso ao Data Lake Storage Gen1.
    - **Contas de armazenamento adicionais**: adicionar contas de armazenamento do Azure como contas de armazenamento adicionais para o cluster. Para adicionar outras contas do Data Lake Storage Gen1 é feito dando as permissões do cluster em dados em mais contas do Data Lake Storage Gen1 enquanto configura uma conta do Data Lake Storage Gen1 como o tipo de armazenamento primário. Confira Configurar o acesso ao Data Lake Storage Gen1.

4. Em **Acesso ao Data Lake Store**, clique em **Selecionar** e continue com a criação do cluster, conforme descrito em [Criar clusters Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Criar um cluster com o Data Lake Storage Gen1 como armazenamento adicional

As instruções a seguir criam um cluster do HDInsight com uma conta de armazenamento do Azure como armazenamento padrão e uma conta do Data Lake Storage Gen1 como um armazenamento adicional.

**Para criar um cluster do HDInsight com uma conta do Data Lake Storage Gen1 como uma conta de armazenamento adicional**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Siga [Criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter as informações gerais sobre a criação de clusters do HDInsight.
3. Na folha **Armazenamento**, em **Tipo de armazenamento primário**, selecione **Armazenamento do Azure** e, em seguida, insira as seguintes informações:

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Adicionar entidade de serviço ao cluster do HDInsight")

    - **Método de seleção**: use uma das seguintes opções:

        * Para especificar uma conta de armazenamento que faz parte de sua assinatura do Azure, selecione **Minhas assinaturas** e, em seguida, selecione a conta de armazenamento.
        * Para especificar uma conta de armazenamento que está fora de sua assinatura do Azure, selecione **Chave de acesso** e, em seguida, forneça as informações da conta de armazenamento externa.

    - **Contêiner padrão**: use o valor padrão ou especifique seu próprio nome.

    - Contas de armazenamento adicionais: adicione mais contas de armazenamento do Azure como armazenamento adicional.
    - Acesso ao Data Lake Store: configure o acesso entre a conta do Data Lake Storage Gen1 e o cluster do HDInsight. Para obter instruções, consulte [Configurar o acesso do Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Configurar o acesso ao Data Lake armazenamento Gen1 

Nesta seção, você configura o acesso do Data Lake Storage Gen1 a partir de clusters do HDInsight usando uma entidade de serviço do Azure Active Directory. 

### <a name="specify-a-service-principal"></a>Especificar uma entidade de serviço

No portal do Azure, você pode usar uma entidade de serviço existente ou criar uma nova.

**Para criar uma entidade de serviço do portal do Azure**

1. Clique em **acesso à Data Lake Store** a partir do blade de armazenamento.
2. Sobre o **acesso do Data Lake armazenamento Gen1** folha, clique em **criar novo**.
3. Clique em **entidade de serviço**e, em seguida, siga as instruções para criar uma entidade de serviço.
4. Baixe o certificado se você optar por usá-lo novamente no futuro. É útil baixar o certificado se você desejar usar a mesma entidade de serviço ao criar clusters HDInsight adicionais.

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Adicionar entidade de serviço ao cluster do HDInsight")

4. Clique em **Acessar** para configurar o acesso à pasta.  Consulte [Configurar permissões de arquivo](#configure-file-permissions).


**Para usar uma entidade de serviço existente do portal do Azure**

1. Clique em **Acesso ao Data Lake Store**.
1. Na lâmina de acesso ao **Data Lake Storage Gen1**, clique em **Use existing**.
2. Clique em **entidade de serviço**e, em seguida, selecione uma entidade de serviço. 
3. Carregue o certificado (arquivo .pfx) associado à entidade de serviço selecionada e insira a senha do certificado.

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Adicionar entidade de serviço ao cluster do HDInsight")

4. Clique em **Acessar** para configurar o acesso à pasta.  Consulte [Configurar permissões de arquivo](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Configurar permissões de arquivo

As configurações são diferentes dependendo de se a conta é usada como o armazenamento padrão ou uma conta de armazenamento adicional:

- Usado como o armazenamento padrão

    - permissão no nível raiz da conta do Data Lake armazenamento Gen1
    - permissão no nível raiz do armazenamento de cluster HDInsight. Por exemplo, a pasta __/clusters__ usada anteriormente no tutorial.
- Usar como um armazenamento adicional

    - Permissões nas pastas em que você precisa de acesso ao arquivo.

**Para atribuir permissão no nível raiz da conta do Data Lake Storage Gen1**

1. No **Acesso a Lago Data Storage Gen1** lâmina, clique em **Acesso**. A folha **Selecionar permissões de arquivo** é aberta. Ele lista todas as contas do Data Lake Storage Gen1 na sua assinatura.
2. Passe o mouse (não clique) com o mouse sobre o nome da conta Data Lake Storage Gen1 para tornar a caixa de seleção visível e marque a caixa de seleção.

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Adicionar entidade de serviço ao cluster do HDInsight")

   Por padrão, as opções __LER__, __GRAVAR__ E __EXECUTAR__ estão selecionadas.

3. Clique em **Selecionar** na parte inferior da página.
4. Clique em **Executar** para atribuir permissão.
5. Clique em **Concluído**.

**Para atribuir a permissão no nível de raiz do cluster HDInsight**

1. No **Acesso a Lago Data Storage Gen1** lâmina, clique em **Acesso**. A folha **Selecionar permissões de arquivo** é aberta. Ele lista todas as contas do Data Lake Storage Gen1 na sua assinatura.
1. Na folha **Selecionar permissões de arquivos**, clique no nome da conta do Data Lake Storage Gen1 para exibir seu conteúdo.
2. Selecione a raiz de armazenamento de cluster HDInsight marcando a caixa de seleção à esquerda da pasta. De acordo com a captura de tela anterior, a raiz de armazenamento do cluster é a pasta __/ clusters__ que você especificou ao selecionar o Data Lake Storage Gen1 como armazenamento padrão.
3. Defina as permissões na pasta.  Por padrão, as opções ler, gravar e executar estão selecionadas.
4. Clique em **Selecionar** na parte inferior da página.
5. Clique em **Executar**.
6. Clique em **Concluído**.

Se você estiver usando o Data Lake Storage Gen1 como armazenamento adicional, atribua permissão somente para as pastas que deseja acessar no cluster do HDInsight. Por exemplo, na captura de tela abaixo, você fornece acesso apenas à pasta **mynewfolder** em uma conta do Data Lake Storage Gen1.

![Atribuir permissões da entidade de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Atribuir permissões da entidade de serviço ao cluster HDInsight")


## <a name="verify-cluster-set-up"></a>Verificar a configuração do cluster

Após a conclusão da configuração do cluster, na folha do cluster, verifique os resultados executando uma ou ambas as etapas a seguir:

* Para verificar se o armazenamento associado ao cluster é a conta Gen1 do Data Lake Storage que você especificou, clique em **Storage accounts** no painel esquerdo.

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Adicionar entidade de serviço ao cluster do HDInsight")

* Para verificar se a entidade de serviço está corretamente associada ao cluster HDInsight, clique em **acesso do Data Lake armazenamento Gen1** no painel esquerdo.

    ![Adicionar entidade de serviço ao cluster do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Adicionar entidade de serviço ao cluster do HDInsight")


## <a name="examples"></a>Exemplos

Depois de configurar o cluster com o Data Lake Storage Gen1 como seu armazenamento, consulte estes exemplos de como usar o cluster HDInsight para analisar os dados armazenados no Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Executar uma consulta do Hive em relação a dados em uma conta do Data Lake Storage Gen1 (como armazenamento primário)

Para executar uma consulta do Hive, use a interface de exibições do Hive no portal do Ambari. Para obter instruções sobre como usar as exibições do Hive no Ambari, consulte [Usar a exibição do Hive com o Hadoop no HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Quando você trabalha com dados em uma conta do Data Lake Storage Gen1, há algumas sequências a serem alteradas.

Se você usar, por exemplo, o cluster criado com o Data Lake Storage Gen1 como armazenamento primário, o caminho para os dados será: *adl: // <data_lake_storage_gen1_account_name> /azuredatalakestore.net/path/to/file*. Uma consulta do Hive para criar uma tabela a partir de dados de amostra armazenados na conta do Data Lake Storage Gen1 se parece com a seguinte instrução:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Descrições:
* `adl://hdiadlsg1storage.azuredatalakestore.net/` é a raiz da conta do Data Lake armazenamento Gen1.
* `/clusters/myhdiadlcluster` é a raiz dos dados do cluster especificada durante a criação do cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` é a localização do arquivo de exemplo usado na consulta.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Executar uma consulta do Hive em relação a dados em uma conta do Data Lake Storage Gen1 (como armazenamento adicional)

Se o cluster que você criou usar o armazenamento de Blobs como armazenamento padrão, os dados de amostra não estarão contidos na conta Data Lake Storage Gen1 usada como armazenamento adicional. Nesse caso, primeiro transfira os dados do armazenamento do Blob para a conta do Data Lake Storage Gen1 e execute as consultas conforme mostrado no exemplo anterior.

Para obter informações sobre como copiar dados do armazenamento do Blob para uma conta do Data Lake Storage Gen1, consulte os seguintes artigos:

* [Use o Distcp para copiar dados entre os blobs de Armazenamento do Azure e o Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Use o AdlCopy para copiar dados de blobs do Azure Storage para o Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Usar o Data Lake armazenamento Gen1 com um cluster Spark
Você pode usar um cluster do Spark para executar trabalhos do Spark em dados armazenados em uma conta do Data Lake Storage Gen1. Para obter mais informações, consulte [Use o cluster do HDInsight Spark para analisar dados no Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Use o Data Lake Storage Gen1 em uma topologia do Storm
Você pode usar a conta Data Lake Storage Gen1 para gravar dados de uma topologia do Storm. Para obter instruções sobre como alcançar esse cenário, consulte [Usar o armazenamento de dados do Windows Azure Gen1 com o Apache Storm com o HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Consulte também
* [Usar Data Lake Storage Gen1 com clusters HDInsight do Azure](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: criar um cluster HDInsight para usar o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
