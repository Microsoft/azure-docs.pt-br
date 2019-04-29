---
title: Restringir o acesso usando Assinaturas de Acesso Compartilhado – HDInsight do Azure
description: Saiba como usar Assinaturas de Acesso Compartilhado para restringir o acesso do HDInsight a dados armazenados em blobs de armazenamento do Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: 7fa46e3a5f0ed6504e4bc927caa0378d75fcc4a7
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763401"
---
# <a name="use-azure-storage-shared-access-signatures-to-restrict-access-to-data-in-hdinsight"></a>Usar Assinaturas de Acesso Compartilhado do Armazenamento do Azure para restringir o acesso a dados no HDInsight

O HDInsight tem acesso completo aos dados nas contas de Armazenamento do Azure associadas ao cluster. Você pode usar Assinaturas de Acesso Compartilhado no contêiner de blob para restringir o acesso aos dados. As Assinaturas de Acesso Compartilhado (SAS) são recursos das contas de armazenamento do Azure que permitem que você limite o acesso aos dados. Por exemplo, oferecendo acesso somente leitura aos dados.

> [!IMPORTANT]  
> Para uma solução que usa o Apache Ranger, considere usar HDInsight ingressado em domínio. Para obter mais informações, consulte o documento [Configurar HDInsight ingressado em domínio](./domain-joined/apache-domain-joined-configure.md).

> [!WARNING]  
> O HDInsight deve ter acesso completo ao armazenamento padrão para o cluster.

## <a name="requirements"></a>Requisitos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Uma assinatura do Azure
* C# ou Python. O código de exemplo do C# é fornecido como uma solução do Visual Studio.

  * A versão do Visual Studio deve ser a 2013, 2015 ou 2017
  * A versão do Python deverá ser a 2.7 ou superior

* Um cluster HDInsight baseado em Linux OU o [Azure PowerShell][powershell] – Se você tiver um cluster existente baseado em Linux, poderá usar o Apache Ambari para adicionar uma Assinatura de Acesso Compartilhado ao cluster. Caso contrário, você poderá usar o Azure PowerShell para criar um cluster e adicionar uma Assinatura de Acesso Compartilhado durante a criação do cluster.

    > [!IMPORTANT]  
    > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Os arquivos de exemplo de [https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature). Esse repositório contém os seguintes itens:

  * Um projeto do Visual Studio que pode criar um contêiner de armazenamento, a política armazenada e a SAS a ser usada com o HDInsight
  * Um script Python que pode criar um contêiner de armazenamento, a política armazenada e a SAS a ser usada com o HDInsight
  * Um script do PowerShell que pode criar um cluster HDInsight e configurá-lo para usar a SAS.

## <a name="shared-access-signatures"></a>As Assinaturas de Acesso Compartilhado

Há duas formas de Assinaturas de Acesso Compartilhado:

* Ad hoc: a hora de início, o tempo de expiração e as permissões para a SAS são todos especificados no URI SAS.

* Política de acesso armazenada: uma política de acesso armazenada é definida em um contêiner de recurso, como um contêiner de blob. Uma política pode ser usada para gerenciar as restrições de uma ou mais assinaturas de acesso compartilhado. Quando você associa uma SAS a uma política de acesso armazenada, a SAS herda as restrições - a hora de início, a hora de expiração e as permissões - definidas para a política de acesso armazenada.

A diferença entre as duas formas é importante para um cenário fundamental: revogação. Uma SAS é uma URL, portanto qualquer pessoa que obtiver a SAS poderá usá-la, independentemente de quem a tiver solicitado em primeiro lugar. Se uma SAS for publicada publicamente, ela poderá ser usada por qualquer pessoa no mundo. Uma SAS distribuída será válida até que ocorra um destes quatro fatores:

1. A hora de expiração especificada na SAS é atingida.

2. O tempo de expiração especificado na política de acesso armazenada referenciada por SAS é atingido. Os seguintes cenários fazem com que o tempo de expiração seja atingido:

    * O intervalo de tempo esgotou-se.
    * A política de acesso armazenada foi modificada para ter um tempo de expiração no passado. Alterar o tempo de expiração é uma maneira de revogar a SAS.

3. A política de acesso armazenada referenciada pelas SAS é excluída, que é uma outra maneira de revogar a SAS. Se você recriar a política de acesso armazenado com o mesmo nome, todos os tokens SAS para a política anterior serão válidos (se o tempo de expiração na SAS não tiver passado). Se você estiver pretendendo revogar a SAS, use um nome diferente se recriar a política de acesso com uma hora de expiração no futuro.

4. A chave de conta usada para criar as SAS é regenerada. A regeneração da chave faz com que todos os aplicativos que usam a chave anterior falhem na autenticação. Atualize todos os componentes para a nova chave.

> [!IMPORTANT]  
> Um URI de assinatura de acesso compartilhado é associado com a chave de conta usada para criar a assinatura e a política de acesso armazenado associada (se houver). Se nenhuma política de acesso armazenado for especificada, a única maneira de revogar uma assinatura de acesso compartilhado é alterar a chave da conta.

É recomendável sempre usar políticas de acesso armazenadas. Ao usar políticas armazenadas, você pode revogar assinaturas ou estender a data de expiração conforme a necessidade. As etapas deste documento usam políticas de acesso armazenado para gerar a SAS.

Para saber mais sobre as Assinaturas de Acesso Compartilhado, consulte [Noções básicas sobre o modelo de SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="create-a-stored-policy-and-sas-using-c"></a>Criar uma política armazenada e uma SAS usando C\#

1. Abra a solução no Visual Studio.

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **SASToken** e selecione **Propriedades**.

3. Escolha **Configurações** e adicione valores às seguintes entradas:

   * StorageConnectionString: A cadeia de conexão da conta de armazenamento para a qual você deseja criar uma política armazenada e uma SAS. O formato deve ser `DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey` onde `myaccount` é o nome de sua conta de armazenamento e `mykey` é a chave da conta de armazenamento.

   * ContainerName: O contêiner na conta de armazenamento para o qual você deseja restringir o acesso.

   * SASPolicyName: O nome a ser usado para a política armazenada que será criada.

   * FileToUpload: O caminho para um arquivo que é carregado no contêiner.

4. Execute o projeto. Informações semelhantes ao seguinte texto serão exibidas assim que a SAS for gerada:

        Container SAS token using stored access policy: sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Salve o token da política SAS, o nome da conta de armazenamento e o nome do contêiner. Esses valores são usados ao associar a conta de armazenamento com seu cluster HDInsight.

### <a name="create-a-stored-policy-and-sas-using-python"></a>Criar uma política armazenada e uma SAS usando Python

1. Abra o arquivo SASToken.py e altere os valores a seguir:

   * policy\_name: O nome a ser usado para a política armazenada que será criada.

   * storage\_account\_name: O nome da sua conta de armazenamento.

   * storage\_account\_key: A chave da conta de armazenamento.

   * storage\_container\_name: O contêiner na conta de armazenamento para o qual você deseja restringir o acesso.

   * example\_file\_path: O caminho para um arquivo que é carregado no contêiner.

2. Execute o script. Isso exibirá o token SAS semelhante ao texto a seguir quando o script for concluído:

        sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14

    Salve o token da política SAS, o nome da conta de armazenamento e o nome do contêiner. Esses valores são usados ao associar a conta de armazenamento com seu cluster HDInsight.

## <a name="use-the-sas-with-hdinsight"></a>Usar a SAS com o HDInsight

Ao criar um cluster HDInsight, você deverá especificar uma conta de armazenamento primária e, opcionalmente, poderá especificar contas de armazenamento adicionais. Ambos os métodos de adição de armazenamento exigem acesso total às contas de armazenamento e aos contêineres usados.

Para usar uma Assinatura de Acesso Compartilhado para limitar o acesso a um contêiner, você deverá adicionar uma entrada personalizada para a configuração **core-site** do cluster.

* Para os clusters HDInsight **baseados no Windows** ou **baseados em Linux**, você pode adicionar a entrada durante a criação do cluster usando o PowerShell.
* Para clusters HDInsight **baseados em Linux**, altere a configuração após a criação do cluster usando o Ambari.

### <a name="create-a-cluster-that-uses-the-sas"></a>Criar um cluster que use a SAS

Um exemplo de criação de um cluster HDInsight que use a SAS foi incluído no diretório `CreateCluster` do repositório. Para usá-lo, execute estas etapas:

1. Abra o arquivo `CreateCluster\HDInsightSAS.ps1` em um editor de texto e modifique os valores a seguir no início do documento.

    ```powershell
    # Replace 'mycluster' with the name of the cluster to be created
    $clusterName = 'mycluster'
    # Valid values are 'Linux' and 'Windows'
    $osType = 'Linux'
    # Replace 'myresourcegroup' with the name of the group to be created
    $resourceGroupName = 'myresourcegroup'
    # Replace with the Azure data center you want to the cluster to live in
    $location = 'North Europe'
    # Replace with the name of the default storage account to be created
    $defaultStorageAccountName = 'mystorageaccount'
    # Replace with the name of the SAS container created earlier
    $SASContainerName = 'sascontainer'
    # Replace with the name of the SAS storage account created earlier
    $SASStorageAccountName = 'sasaccount'
    # Replace with the SAS token generated earlier
    $SASToken = 'sastoken'
    # Set the number of worker nodes in the cluster
    $clusterSizeInNodes = 3
    ```

    Por exemplo, altere `'mycluster'` para o nome do cluster que você deseja criar. Os valores da SAS devem corresponder aos valores das etapas anteriores durante a criação de uma conta de armazenamento e de um token SAS.

    Depois de alterar os valores, salve o arquivo.

2. Abra um novo prompt do Azure PowerShell. Se você não estiver familiarizado com o Azure PowerShell ou se não o tiver instalado, consulte [Instalar e configurar o Azure PowerShell][powershell].

1. No prompt, use o seguinte comando para se autenticar em sua assinatura do Azure:

    ```powershell
    Connect-AzAccount
    ```

    Quando solicitado, entre com a conta para sua assinatura do Azure.

    Se a conta estiver associada a várias assinaturas do Azure, talvez seja necessário utilizar `Select-AzSubscription` para selecionar a assinatura que você deseja usar.

4. No prompt, altere os diretórios para o diretório `CreateCluster` que contém o arquivo HDInsightSAS.ps1. Em seguida, use o comando a seguir para executar o script

    ```powershell
    .\HDInsightSAS.ps1
    ```

    À medida que o script for executado, ele registrará a saída em log do prompt do PowerShell enquanto cria o grupo de recursos e as contas de armazenamento. Ele solicitará que você insira o usuário HTTP para o cluster HDInsight. Essa conta é usada para proteger o acesso HTTP/s ao cluster.

    Se você estiver criando um cluster baseado em Linux, também será solicitado que você forneça um nome de conta de usuário SSH e uma senha. Essa conta é usada para entrar remotamente no cluster.

   > [!IMPORTANT]  
   > Quando o nome de usuário ou a senha HTTP/s ou SSH forem solicitados, você deverá fornecer uma senha que atenda a estes critérios:
   >
   > * Deve ter pelo menos 10 caracteres.
   > * Deve conter pelo menos um dígito.
   > * Deve conter pelo menos um caractere não alfanumérico.
   > * Deve conter pelo menos uma letra maiúscula ou minúscula.

Esse script demora um pouco para terminar, normalmente cerca de 15 minutos. Quando o script for concluído sem erros, o cluster terá sido criado.

### <a name="use-the-sas-with-an-existing-cluster"></a>Usar a SAS com um cluster existente

Se você tiver um cluster baseado em Linux existente, poderá adicionar a SAS à configuração **core-site** usando as seguintes etapas:

1. Abra a UI da Web do Ambari para seu cluster. O endereço para essa página é https://YOURCLUSTERNAME.azurehdinsight.net. Quando solicitado, faça a autenticação no cluster usando o nome do administrador (admin) e a senha usados na criação do cluster.

2. No lado esquerdo da interface do usuário da Web do Ambari, escolha **HDFS** e selecione a guia **Configurações** na metade da página.

3. Selecione a guia **Avançado** e então role até encontrar a seção **Core-site personalizado**.

4. Expanda a seção **Core-site personalizado**, role até o fim e escolha o link **Adicionar propriedade...**. Use os valores a seguir para os campos **Chave** e **Valor**:

   * **Chave**: fs.azure.sas.CONTAINERNAME.STORAGEACCOUNTNAME.blob.core.windows.net
   * **Valor**: A SAS retornada pelo aplicativo C# ou Python executado anteriormente

     Substitua **CONTAINERNAME** pelo nome do contêiner usado com o aplicativo C# ou SAS. Substitua **STORAGEACCOUNTNAME** pelo nome da conta de armazenamento usada.

5. Clique no botão **Adicionar** para salvar essa chave e esse valor e clique no botão **Salvar** para salvar as alterações de configuração. Quando solicitado, adicione uma descrição da alteração ("adicionando acesso de armazenamento SAS", por exemplo) e clique em **Salvar**.

    Clique em **OK** quando as alterações forem concluídas.

   > [!IMPORTANT]  
   > Você deverá reiniciar vários serviços antes que a alteração entre em vigor.

6. Na interface do usuário da Web do Ambari, selecione **HDFS** na lista à esquerda e, em seguida, selecione **Reiniciar Todos os Afetados** na lista suspensa **Ações de Serviço** à direita. Quando solicitado, selecione __Confirmar Reiniciar Tudo__.

    Repita esse processo para MapReduce2 e YARN.

7. Depois os serviços tiverem sido reiniciados, selecione cada uma e desabilite o modo de manutenção na lista suspensa **Ações de Serviço**.

## <a name="test-restricted-access"></a>Testar o acesso restrito

Para verificar se você restringiu o acesso, use SSH para se conectar ao cluster. Para obter mais informações, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Uma vez conectado ao cluster, use as etapas a seguir para verificar se você só pode ler e listar itens na conta de armazenamento SAS:

1. Para listar o conteúdo do contêiner, use o seguinte comando no prompt: 

    ```bash
    hdfs dfs -ls wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/
    ```

    Substitua **SASCONTAINER** pelo nome do contêiner criado para a conta de armazenamento SAS. Substitua **SASACCOUNTNAME** pelo nome da conta de armazenamento usada para a SAS.

    A lista inclui o arquivo carregado quando o contêiner e a SAS foram criados.

2. Use o comando a seguir para verificar se você pode ler o conteúdo do arquivo. Substitua **SASCONTAINER** e **SASACCOUNTNAME** como na etapa anterior. Substitua **FILENAME** pelo nome do arquivo exibido no comando anterior:

    ```bash
    hdfs dfs -text wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME
    ```

    Esse comando lista o conteúdo do arquivo.

3. Use o comando a seguir para baixar o arquivo para o sistema de arquivos local:

    ```bash
    hdfs dfs -get wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/FILENAME testfile.txt
    ```

    Esse comando baixa o arquivo para um arquivo local chamado **testfile.txt**.

4. Use o comando a seguir para carregar o arquivo local para um novo arquivo chamado **testupload.txt** no armazenamento SAS:

    ```bash
    hdfs dfs -put testfile.txt wasb://SASCONTAINER@SASACCOUNTNAME.blob.core.windows.net/testupload.txt
    ```

    Você receberá uma mensagem semelhante ao texto a seguir:

        put: java.io.IOException

    Esse erro só ocorrerá porque o local do armazenamento é somente leitura+lista. Use o comando a para colocar os dados no armazenamento padrão do cluster, que pode ser gravável:

    ```bash
    hdfs dfs -put testfile.txt wasb:///testupload.txt
    ```

    Dessa vez, a operação deverá ser concluída com êxito.

## <a name="troubleshooting"></a>solução de problemas

### <a name="a-task-was-canceled"></a>Uma tarefa foi cancelada

**Sintomas**: Ao criar um cluster usando o script do PowerShell, talvez você receba a seguinte mensagem de erro:

    New-AzHDInsightCluster : A task was canceled.
    At C:\Users\larryfr\Documents\GitHub\hdinsight-azure-storage-sas\CreateCluster\HDInsightSAS.ps1:62 char:5
    +     New-AzHDInsightCluster `
    +     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
        + CategoryInfo          : NotSpecified: (:) [New-AzHDInsightCluster], CloudException
        + FullyQualifiedErrorId : Hyak.Common.CloudException,Microsoft.Azure.Commands.HDInsight.NewAzureHDInsightClusterCommand

**Causa**: Esse erro poderá ocorrer se você usar uma senha para o usuário admin/HTTP do cluster ou, para clusters baseados em Linux, o usuário SSH.

**Resolução**: Use uma senha que atenda aos seguintes critérios:

* Deve ter pelo menos 10 caracteres.
* Deve conter pelo menos um dígito.
* Deve conter pelo menos um caractere não alfanumérico.
* Deve conter pelo menos uma letra maiúscula ou minúscula.

## <a name="next-steps"></a>Próximos passos

Agora que você aprendeu a adicionar armazenamento de acesso limitado ao seu cluster HDInsight, conheça outras maneiras de trabalhar com dados no cluster:

* [Usar o Apache Hive com o HDInsight](hadoop/hdinsight-use-hive.md)
* [Usar o Apache Pig com o HDInsight](hadoop/hdinsight-use-pig.md)
* [Usar o MapReduce com o HDInsight](hadoop/hdinsight-use-mapreduce.md)

[powershell]: /powershell/azureps-cmdlets-docs
