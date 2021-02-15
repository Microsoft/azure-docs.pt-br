---
title: Criar clusters do Azure HDInsight com o Data Lake Storage Gen1-Portal
description: Use o portal do Azure para criar e usar clusters do HDInsight com o Armazenamento de Data Lake do Azure Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 666b39e2a600fe6ca004798ed4f8371cdd1dfe5f
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340247"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Criar clusters do HDInsight com o Gen1 do Azure Data Lake Storage usando o portal do Azure

> [!div class="op_single_selector"]
> * [Use o Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usar o PowerShell (para armazenamento padrão)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usar o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usar o Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Saiba como usar o portal do Azure para criar um cluster HDInsight com Azure Data Lake Storage Gen1 como o armazenamento padrão ou um armazenamento adicional. Embora o armazenamento adicional seja opcional para um cluster HDInsight, é recomendável armazenar seus dados corporativos nas contas de armazenamento adicionais.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você atende aos seguintes requisitos:

* **Uma assinatura do Azure**. Acesse [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta do Azure Data Lake Storage Gen1**. Siga as instruções do [Introdução ao Azure Data Lake armazenamento Gen1, usando o portal do Azure](data-lake-store-get-started-portal.md). Você também deve criar uma pasta raiz na conta.  Neste artigo, é usada uma pasta raiz chamada __/clusters__ .
* **Uma entidade de serviço Azure Active Directory**. Este guia de instrução fornece instruções sobre como criar uma entidade de serviço no Azure Active Directory (AD do Azure). No entanto, para criar uma entidade de serviço, você deve ser um administrador do Azure AD. Se você for um administrador, poderá ignorar esse pré-requisito e continuar.

>[!NOTE]
>Você poderá criar uma entidade de serviço somente se você for um administrador do Azure AD. O administrador do AD do Azure AD deve criar uma entidade de serviço antes de criar um cluster do HDInsight com o Data Lake Storage Gen1. Além disso, a entidade de serviço deve ser criada com um certificado, conforme descrito em [Criar uma entidade de serviço com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Crie um cluster HDInsight

Nesta seção, você cria um cluster HDInsight com Data Lake Storage Gen1 como o padrão ou o armazenamento adicional. Este artigo se concentra apenas na parte da configuração de Data Lake Storage Gen1. Para obter informações gerais sobre a criação do cluster, consulte [Criar clusters do Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Criar um cluster com o Data Lake Storage Gen1 como armazenamento padrão

Para criar um cluster HDInsight com um Data Lake Storage Gen1 como a conta de armazenamento padrão:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Siga [Criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter as informações gerais sobre a criação de clusters do HDInsight.
3. Na folha **Armazenamento**, em **Tipo de armazenamento principal**, selecione **Armazenamento do Data Lake do Azure Gen1** e insira as seguintes informações:

    ![Configurações da conta de armazenamento do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png)

    * **Selecione a conta do Data Lake Store**: Selecione uma conta existente do Data Lake Storage Gen1. Uma conta existente do Data Lake armazenamento Gen1 é necessária.  Consulte [Pré-requisitos](#prerequisites).
    * **Caminho raiz**: insira um caminho em que os arquivos específicos de cluster deverão ser armazenados. Na captura de tela, é __/clusters/myhdiadlcluster/__, em que a pasta __/clusters__ deve existir e o Portal cria a pasta *myhdicluster*.  O *myhdicluster* é o nome do cluster.
    * **Acesso ao Data Lake Store**: Configure o acesso entre a conta do Data Lake Storage Gen1 e o cluster do HDInsight. Para obter instruções, consulte [Configurar o acesso do Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).
    * **Contas de armazenamento adicionais**: Adicione contas de armazenamento do Azure como contas de armazenamento adicionais para o cluster. Para adicionar outras contas do Data Lake Storage Gen1 é feito dando as permissões do cluster em dados em mais contas do Data Lake Storage Gen1 enquanto configura uma conta do Data Lake Storage Gen1 como o tipo de armazenamento primário. Ver [acesso de configurar o Data Lake armazenamento Gen1](#configure-data-lake-storage-gen1-access).

4. Em **Acesso ao Data Lake Store**, clique em **Selecionar** e continue com a criação do cluster, conforme descrito em [Criar clusters Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Criar um cluster com o Data Lake Storage Gen1 como armazenamento adicional

As instruções a seguir criam um cluster HDInsight com uma conta de armazenamento de BLOBs do Azure como o armazenamento padrão e uma conta de armazenamento com Data Lake Storage Gen1 como um armazenamento adicional.

Para criar um cluster HDInsight com Data Lake Storage Gen1 como uma conta de armazenamento adicional:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Siga [Criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter as informações gerais sobre a criação de clusters do HDInsight.
3. Na folha **Armazenamento**, em **Tipo de armazenamento primário**, selecione **Armazenamento do Azure** e, em seguida, insira as seguintes informações:

    ![Armazenamento adicional das configurações da conta de armazenamento do HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png)

    * **Método de seleção** – para especificar uma conta de armazenamento que faça parte de sua assinatura do Azure, selecione **minhas assinaturas** e, em seguida, selecione a conta de armazenamento. Para especificar uma conta de armazenamento que está fora de sua assinatura do Azure, selecione **Chave de acesso** e, em seguida, forneça as informações da conta de armazenamento externa.

    * **Contêiner padrão** – use o valor padrão ou especifique seu próprio nome.
    * **Contas de armazenamento adicionais** – adicione mais contas de armazenamento do Azure como o armazenamento adicional.
    * **Acesso data Lake Store** -configure o acesso entre a conta de data Lake Storage Gen1 e o cluster HDInsight. Para obter instruções, consulte [Configurar o acesso de data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Configurar o acesso ao Data Lake armazenamento Gen1

Nesta seção, você configura o acesso do Data Lake Storage Gen1 a partir de clusters do HDInsight usando uma entidade de serviço do Azure Active Directory.

### <a name="specify-a-service-principal"></a>Especificar uma entidade de serviço

No portal do Azure, você pode usar uma entidade de serviço existente ou criar uma nova.

Para criar uma entidade de serviço do portal do Azure:
1. Consulte [criar entidade de serviço e certificados](../active-directory/develop/howto-create-service-principal-portal.md) usando Azure Active Directory.

Para usar uma entidade de serviço existente do portal do Azure:

1. A entidade de serviço deve ter permissões de proprietário na conta de armazenamento. Consulte [configurar permissões para a entidade de serviço como proprietário na conta de armazenamento](#configure-serviceprincipal-permissions).
1. Selecione **acesso a data Lake Store**.
1. Na folha **acesso data Lake Storage Gen1** , selecione **usar existente**.
1. Selecione **entidade de serviço** e, em seguida, selecione uma entidade de serviço.
1. Carregue o certificado (arquivo .pfx) associado à entidade de serviço selecionada e insira a senha do certificado.

    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png)

1. Selecione **acesso** para configurar o acesso à pasta.  Consulte [Configurar permissões de arquivo](#configure-file-permissions).

### <a name="set-up-permissions-for-the-service-principal-to-be-owner-on-the-storage-account"></a><a name="configure-serviceprincipal-permissions"></a>Configurar permissões para a entidade de serviço ser proprietária na conta de armazenamento
1. Na folha controle de acesso (IAM) da conta de armazenamento, clique em adicionar uma atribuição de função. 
2. Na folha adicionar uma atribuição de função, selecione função como ' proprietário ' e selecione o SPN e clique em salvar.

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Configurar permissões de arquivo

A configuração é diferente dependendo se a conta é usada como o armazenamento padrão ou uma conta de armazenamento adicional:

* Usado como o armazenamento padrão

  * permissão no nível raiz da conta do Data Lake armazenamento Gen1
  * permissão no nível raiz do armazenamento de cluster HDInsight. Por exemplo, a pasta __/clusters__ usada anteriormente no tutorial.

* Usar como um armazenamento adicional

  * Permissões nas pastas em que você precisa de acesso ao arquivo.

Para atribuir permissão na conta de armazenamento com Data Lake Storage Gen1 no nível de raiz:

1. Na folha **acesso data Lake Storage Gen1** , selecione **acesso**. A folha **Selecionar permissões de arquivo** é aberta. Ele lista todas as contas de armazenamento em sua assinatura.
1. Focalize (não clique) o mouse sobre o nome da conta com Data Lake Storage Gen1 para tornar a caixa de seleção visível e marque a caixa de seleção.

    ![Selecionar permissões de arquivo](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png)

   Por padrão, __ler__, __gravar__ e __executar__ estão todos selecionados.

1. Clique em **Selecionar** na parte inferior da página.
1. Selecione **executar** para atribuir permissão.
1. Selecione **Concluído**.

Para atribuir permissão no nível de raiz do cluster HDInsight:

1. Na folha **acesso data Lake Storage Gen1** , selecione **acesso**. A folha **Selecionar permissões de arquivo** é aberta. Ele lista todas as contas de armazenamento com Data Lake Storage Gen1 em sua assinatura.
1. Na folha **selecionar permissões de arquivo** , selecione a conta de armazenamento com o nome data Lake Storage Gen1 para mostrar seu conteúdo.
1. Selecione a raiz de armazenamento de cluster HDInsight marcando a caixa de seleção à esquerda da pasta. De acordo com a captura de tela anterior, a raiz de armazenamento do cluster é a pasta __/ clusters__ que você especificou ao selecionar o Data Lake Storage Gen1 como armazenamento padrão.
1. Defina as permissões na pasta.  Por padrão, as opções ler, gravar e executar estão selecionadas.
1. Clique em **Selecionar** na parte inferior da página.
1. Selecione **Executar**.
1. Selecione **Concluído**.

Se você estiver usando o Data Lake Storage Gen1 como armazenamento adicional, atribua permissão somente para as pastas que deseja acessar no cluster do HDInsight. Por exemplo, na captura de tela abaixo, você fornece acesso somente à pasta **mynewfolder** em uma conta de armazenamento com data Lake Storage Gen1.

![Atribuir permissões de entidade de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png)

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Verificar a configuração do cluster

Após a conclusão da configuração do cluster, na folha do cluster, verifique os resultados executando uma ou ambas as etapas a seguir:

* Para verificar se o armazenamento associado para o cluster é a conta com Data Lake Storage Gen1 especificado, selecione contas de **armazenamento** no painel esquerdo.

    ![Verificar armazenamento associado](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png)

* Para verificar se a entidade de serviço está corretamente associada ao cluster HDInsight, selecione **Data Lake Storage Gen1 acesso** no painel esquerdo.

    ![Verificar entidade de serviço](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png)

## <a name="examples"></a>Exemplos

Depois de configurar o cluster com Data Lake Storage Gen1 como seu armazenamento, consulte estes exemplos de como usar o cluster HDInsight para analisar os dados armazenados no Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-primary-storage"></a>Executar uma consulta de Hive em relação a dados em um Data Lake Storage Gen1 (como armazenamento primário)

Para executar uma consulta do Hive, use a interface de exibições do Hive no portal do Ambari. Para obter instruções sobre como usar as exibições do Hive no Ambari, consulte [Usar a exibição do Hive com o Hadoop no HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Quando você trabalha com dados em um Data Lake Storage Gen1, há algumas cadeias de caracteres a serem alteradas.

Se você usar, por exemplo, o cluster criado com o Data Lake Storage Gen1 como armazenamento primário, o caminho para os dados será: *adl: // <data_lake_storage_gen1_account_name> /azuredatalakestore.net/path/to/file*. Uma consulta de Hive para criar uma tabela a partir de dados de exemplo armazenados no Data Lake Storage Gen1 é semelhante à seguinte instrução:

```console
CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'
```

Descrições:

* `adl://hdiadlsg1storage.azuredatalakestore.net/` é a raiz da conta com Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster` é a raiz dos dados do cluster especificada durante a criação do cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` é a localização do arquivo de exemplo usado na consulta.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-additional-storage"></a>Executar uma consulta de Hive em relação a dados em um Data Lake Storage Gen1 (como armazenamento adicional)

Se o cluster que você criou usa o armazenamento de blob como armazenamento padrão, os dados de exemplo não estão contidos na conta de armazenamento com Data Lake Storage Gen1 que são usados como armazenamento adicional. Nesse caso, primeiro transfira os dados do armazenamento de BLOBs para a conta de armazenamento com Data Lake Storage Gen1 e, em seguida, execute as consultas, conforme mostrado no exemplo anterior.

Para obter informações sobre como copiar dados do armazenamento de BLOBs para uma conta de armazenamento com Data Lake Storage Gen1, consulte os seguintes artigos:

* [Use Distcp para copiar dados entre o armazenamento de BLOBs do Azure e o Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Use AdlCopy para copiar dados do armazenamento de BLOBs do Azure para Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Usar o Data Lake armazenamento Gen1 com um cluster Spark

Você pode usar um cluster Spark para executar trabalhos do Spark em dados armazenados em um Data Lake Storage Gen1. Para obter mais informações, consulte [usar o cluster HDInsight Spark para analisar dados em data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Use o Data Lake Storage Gen1 em uma topologia do Storm

Você pode usar a conta de armazenamento com Data Lake Storage Gen1 para gravar dados de uma topologia do Storm. Para obter instruções sobre como alcançar esse cenário, consulte [Usar o armazenamento de dados do Windows Azure Gen1 com o Apache Storm com o HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Confira também

* [Usar Data Lake Storage Gen1 com clusters HDInsight do Azure](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [PowerShell: crie um cluster do HDInsight para usar o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: /windows-hardware/drivers/devtest/pvk2pfx