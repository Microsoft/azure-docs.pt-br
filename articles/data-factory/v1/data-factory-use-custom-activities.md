---
title: Usar atividades personalizadas em um pipeline do Data Factory do Azure
description: Saiba como criar atividades personalizadas e usá-las em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.assetid: 8dd7ba14-15d2-4fd9-9ada-0b2c684327e9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: craigg
robots: noindex
ms.openlocfilehash: 0ddc235064d99e9d6385ab48e78f893952eefa15
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61254104"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Usar atividades personalizadas em um pipeline do Data Factory do Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](data-factory-use-custom-activities.md)
> * [Versão 2 (versão atual)](../transform-data-using-dotnet-custom-activity.md)

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Atividades personalizadas na V2](../transform-data-using-dotnet-custom-activity.md).

Há dois tipos de atividades que você pode usar em um pipeline do Azure Data Factory.

- [Atividades de Movimentação de Dados](data-factory-data-movement-activities.md) para mover dados entre [a fonte suportada e o armazenamento de dados de coletor](data-factory-data-movement-activities.md#supported-data-stores-and-formats).
- [Atividades de transformação de dados](data-factory-data-transformation-activities.md) para transformar dados usando serviços de computação como Azure HDInsight, Lote do Azure e Azure Machine Learning.

Para mover dados de/para um armazenamento de dados que não tenha suporte do Data Factory, você pode criar uma **atividade personalizada** com lógica de movimentação de dados própria e usar a atividade em um pipeline. De forma semelhante, se precisar transformar/processar dados de uma maneira que não tenha suporte do Data Factory, crie uma atividade personalizada com lógica de transformação de dados própria e use a atividade em um pipeline.

Você pode configurar uma atividade personalizada para execução em um pool de máquinas virtuais do **Lote do Azure**. Ao usar o Azure Batch, você só pode utilizar um pool de Lote do Azure existente.

O passo a passo a seguir fornece instruções para criar uma atividade personalizada do .NET e usá-la em um pipeline. O passo a passo usa um serviço vinculado do **Lote do Azure**.

> [!IMPORTANT]
> - Não é possível usar o Gateway de Gerenciamento de Dados de uma atividade personalizada para acessar fontes de dados locais. Atualmente, o [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) dá suporte apenas à atividade de cópia e à atividade de procedimento armazenado no Data Factory.

## <a name="walkthrough-create-a-custom-activity"></a>Passo a passo: criar uma atividade personalizada
### <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2012/2013/2015
* Baixar e instalar o [SDK .NET do Azure](https://azure.microsoft.com/downloads/)

### <a name="azure-batch-prerequisites"></a>Pré-requisitos de Lote do Azure
No passo a passo, você executa suas atividades personalizadas do .NET usando o Lote do Azure como um recurso de computação. O **Lote do Azure** é um serviço de plataforma para execução de aplicativos paralelos em grande escala e aplicativos HPC (computação de alto desempenho) com eficiência na nuvem. O Lote do Azure agenda o trabalho de computação intensiva para execução em uma **coleção de máquinas virtuais** gerenciada e pode dimensionar automaticamente os recursos de computação para atender às necessidades de seus trabalhos. Consulte o artigo [Noções básicas de Lote do Azure][batch-technical-overview] para obter uma visão geral do serviço de Lote do Azure.

Para o tutorial, crie uma conta do Lote do Azure com um pool de VMs. Siga estas etapas:

1. Crie uma **conta do Lote do Azure** usando o [Portal do Azure](https://portal.azure.com). Consulte o artigo [Criar e gerenciar uma conta do Lote do Azure][batch-create-account] para obter instruções.
2. Anote o nome, a chave, o URI e o nome do pool da conta do Lote do Azure. Você vai precisar destes dados para criar um serviço vinculado do Lote do Azure.
    1. Na home page de conta do Lote do Azure, você verá uma **URL** no seguinte formato: `https://myaccount.westus.batch.azure.com`. Neste exemplo, **myaccount** é o nome da conta do Lote do Azure. O URI usado na definição do serviço vinculado é a URL sem o nome da conta. Por exemplo: `https://<region>.batch.azure.com`.
    2. Clique em **Chaves** no menu à esquerda e copie a **CHAVE DE ACESSO PRIMÁRIA**.
    3. Para usar um pool existente, clique em **Pools** no menu e anote a **ID** do pool. Se você não tiver um pool existente, siga para a próxima etapa.
2. Crie um pool do **Lote do Azure**.

   1. No [Portal do Azure](https://portal.azure.com), clique em **Procurar** no menu à esquerda e clique em **Contas do Lote**.
   2. Selecione sua a conta do Lote do Azure para abrir a folha **Conta do Batch** .
   3. Clique no bloco **Pools** .
   4. Na folha **Pools** , clique no botão Adicionar na barra de ferramentas para adicionar um pool.
      1. Insira uma ID para o pool (ID do Pool). Observe a **ID do pool**; você precisa dela ao criar a solução Data Factory.
      2. Especifique **Windows Server 2012 R2** para a configuração da família do sistema operacional.
      3. Selecione um **camada de preços de nó**.
      4. Digite **2** como valor para a configuração **Destino Dedicado**.
      5. Digite **2** como valor para a configuração **Máximo de tarefas por nó**.
   5. Clique em **OK** para criar o pool.
   6. Anote a **ID** do pool.

### <a name="high-level-steps"></a>Etapas de alto nível
Aqui estão as duas etapas de alto nível que você deve realizar como parte deste passo a passo:

1. Crie uma atividade personalizada que contenha uma lógica simples de processamento/transformação de dados.
2. Crie um Azure data factory com um pipeline que usa a atividade personalizada.

### <a name="create-a-custom-activity"></a>Criar uma atividade personalizada
Para criar uma atividade personalizada do .NET, crie um projeto de **Biblioteca de Classes do .NET** com uma classe que implemente a interface **IDotNetActivity**. Essa interface tem apenas um método: [Execute](https://msdn.microsoft.com/library/azure/mt603945.aspx) e sua assinatura é:

```csharp
public IDictionary<string, string> Execute(
    IEnumerable<LinkedService> linkedServices,
    IEnumerable<Dataset> datasets,
    Activity activity,
    IActivityLogger logger)
```

O método utiliza quatro parâmetros:

- **linkedServices**. Esta propriedade é uma lista enumerável de serviços vinculados do Armazenamento de Dados referenciada por conjuntos de dados de entrada/saída da atividade.
- **conjuntos de dados**. Esta propriedade é uma lista enumerável de conjuntos de dados de entrada/saída da atividade. Você pode usar esse parâmetro para obter os locais e esquemas definidos por conjuntos de dados de entrada e saída.
- **atividade**. Esta propriedade representa a atividade atual. Ela pode ser usada para acessar as propriedades estendidas associadas à atividade personalizada. Consulte [Acessar propriedades estendidas](#access-extended-properties) para obter mais detalhes.
- **logger**. Este objeto permite que você escreva comentários de depuração que são exibidos no log do usuário no pipeline.

O método retorna um dicionário que pode ser usado para unir atividades personalizadas no futuro. Este recurso ainda não está implementado, portanto, retorne um dicionário vazio do método.

### <a name="procedure"></a>Procedimento
1. Crie um projeto de **Biblioteca de Classes do .NET** .
   <ol type="a">
     <li>Inicialize o <b>Visual Studio 2017</b> ou o <b>Visual Studio 2015</b> ou o <b>Visual Studio 2013</b> ou o <b>Visual Studio 2012</b>.</li>
     <li>Clique em <b>Arquivo</b>, aponte para <b>Novo</b> e clique em <b>Projeto</b>.</li>
     <li>Expanda <b>Modelos</b> e selecione <b>Visual C#</b>. Neste passo a passo, você pode usar C#, mas você pode usar qualquer linguagem .NET para desenvolver a atividade personalizada.</li>
     <li>Selecione <b>Biblioteca de Classes</b> na lista de tipos de projeto à direita. No VS 2017, escolha <b>Biblioteca de Classes (.NET Framework)</b> </li>
     <li>Insira <b>MyDotNetActivity</b> for the <b>Nome</b>.</li>
     <li>Selecione <b>C:\ADFGetStarted</b> como o <b>Local</b>.</li>
     <li>Clique em <b>OK</b> para criar o projeto.</li>
   </ol>

2. Clique em **Ferramentas**, aponte para **Gerenciador de Pacotes NuGet** e clique em **Console do Gerenciador de Pacotes**.

3. No Console do Gerenciador de Pacotes, execute o comando a seguir para importar **Microsoft.Azure.Management.DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importe o pacote NuGet do **Armazenamento do Azure** para o projeto.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    > [!IMPORTANT]
    > O iniciador do serviço de Fábrica de Dados requer a versão 4.3 do WindowsAzure.Storage. Se você adicionar uma referência para uma versão posterior do assembly de armazenamento do Azure em seu projeto de atividade personalizado, você verá um erro quando a atividade for executada. Para resolver o erro, consulte a seção [Isolamento de Appdomain](#appdomain-isolation).
5. Adicione as instruções **using** a seguir ao arquivo de origem no projeto.

    ```csharp

    // Comment these lines if using VS 2017
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    // --------------------

    // Comment these lines if using <= VS 2015
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    // ---------------------

    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
6. Altere o nome do **namespace** para **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Altere o nome da classe para **MyDotNetActivity** e derive-a da interface **IDotNetActivity**, conforme mostrado no snippet de código a seguir:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implemente (Adicione) o método **Execute** da interface **IDotNetActivity** à classe **MyDotNetActivity** e copie o seguinte código de exemplo para o método.

    O exemplo a seguir conta o número de ocorrências do termo de pesquisa ("Microsoft") em cada blob associado a uma fatia de dados.

    ```csharp
    /// <summary>
    /// Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.
    /// </summary>

    public IDictionary<string, string> Execute(
        IEnumerable<LinkedService> linkedServices,
        IEnumerable<Dataset> datasets,
        Activity activity,
        IActivityLogger logger)
    {
        // get extended properties defined in activity JSON definition
        // (for example: SliceStart)
        DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
        string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

        // to log information, use the logger object
        // log all extended properties
        IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
        logger.Write("Logging extended properties if any...");
        foreach (KeyValuePair<string, string> entry in extendedProperties)
        {
            logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
        }

        // linked service for input and output data stores
        // in this example, same storage is used for both input/output
        AzureStorageLinkedService inputLinkedService;

        // get the input dataset
        Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);

        // declare variables to hold type properties of input/output datasets
        AzureBlobDataset inputTypeProperties, outputTypeProperties;

        // get type properties from the dataset object
        inputTypeProperties = inputDataset.Properties.TypeProperties as AzureBlobDataset;

        // log linked services passed in linkedServices parameter
        // you will see two linked services of type: AzureStorage
        // one for input dataset and the other for output dataset
        foreach (LinkedService ls in linkedServices)
            logger.Write("linkedService.Name {0}", ls.Name);

        // get the first Azure Storage linked service from linkedServices object
        // using First method instead of Single since we are using the same
        // Azure Storage linked service for input and output.
        inputLinkedService = linkedServices.First(
            linkedService =>
            linkedService.Name ==
            inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
            as AzureStorageLinkedService;

        // get the connection string in the linked service
        string connectionString = inputLinkedService.ConnectionString;

        // get the folder path from the input dataset definition
        string folderPath = GetFolderPath(inputDataset);
        string output = string.Empty; // for use later.

        // create storage client for input. Pass the connection string.
        CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
        CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

        // initialize the continuation token before using it in the do-while loop.
        BlobContinuationToken continuationToken = null;
        do
        {   // get the list of input blobs from the input storage client object.
            BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                     true,
                                     BlobListingDetails.Metadata,
                                     null,
                                     continuationToken,
                                     null,
                                     null);

            // Calculate method returns the number of occurrences of
            // the search term (“Microsoft”) in each blob associated
            // with the data slice. definition of the method is shown in the next step.

            output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

        } while (continuationToken != null);

        // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
        Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

        // get type properties for the output dataset
        outputTypeProperties = outputDataset.Properties.TypeProperties as AzureBlobDataset;

        // get the folder path from the output dataset definition
        folderPath = GetFolderPath(outputDataset);

        // log the output folder path
        logger.Write("Writing blob to the folder: {0}", folderPath);

        // create a storage object for the output blob.
        CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
        // write the name of the file.
        Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

        // log the output file name
        logger.Write("output blob URI: {0}", outputBlobUri.ToString());

        // create a blob and upload the output text.
        CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
        logger.Write("Writing {0} to the output blob", output);
        outputBlob.UploadText(output);

        // The dictionary can be used to chain custom activities together in the future.
        // This feature is not implemented yet, so just return an empty dictionary.

        return new Dictionary<string, string>();
    }
    ```
9. Adicione os seguintes métodos auxiliares:

    ```csharp
    /// <summary>
    /// Gets the folderPath value from the input/output dataset.
    /// </summary>

    private static string GetFolderPath(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the folder path found in the type properties
        return blobDataset.FolderPath;
    }

    /// <summary>
    /// Gets the fileName value from the input/output dataset.
    /// </summary>

    private static string GetFileName(Dataset dataArtifact)
    {
        if (dataArtifact == null || dataArtifact.Properties == null)
        {
            return null;
        }

        // get type properties of the dataset
        AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
        if (blobDataset == null)
        {
            return null;
        }

        // return the blob/file name in the type properties
        return blobDataset.FileName;
    }

    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file,
    /// and prepares the output text that is written to the output blob.
    /// </summary>

    public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
    {
        string output = string.Empty;
        logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
        foreach (IListBlobItem listBlobItem in Bresult.Results)
        {
            CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
            if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
            {
                string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                logger.Write("input blob text: {0}", blobText);
                string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                var matchQuery = from word in source
                                 where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                 select word;
                int wordCount = matchQuery.Count();
                output += string.Format("{0} occurrences(s) of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
            }
        }
        return output;
    }
    ```

    O método GetFolderPath retorna o caminho para a pasta indicada pelo conjunto de dados e o método GetFileName retorna o nome do blob/arquivo para o qual o conjunto de dados aponta. Se você tiver um caminho de pasta que utiliza variáveis como {Year}, {Month}, {Day} etc., o método retorna a cadeia de caracteres como ela é, sem substituí-los com valores de tempo de execução. Confira a seção [Acessar propriedades estendidas](#access-extended-properties) para obter detalhes sobre como acessar SliceStart, SliceEnd, etc.

    ```JSON
    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "adftutorial/inputfolder/",
    ```

    O método Calculate calcula o número de instâncias da palavra-chave Microsoft nos arquivos de entrada (blobs na pasta). O termo de pesquisa ("Microsoft") é embutido no código.
10. Compile o projeto. Clique em **Compilar** no menu e clique em **Compilar Solução**.

    > [!IMPORTANT]
    > Defina a versão 4.5.2 do .NET Framework como o framework de destino para o seu projeto: clique com botão direito no projeto e, em seguida, clique em **Propriedades** para definir o framework de destino. O Data Factory não oferece suporte a atividades personalizadas compiladas em versões do .NET Framework posteriores a 4.5.2.

11. Inicie o **Windows Explorer** e navegue até a pasta **bin\debug** ou **bin\release**, dependendo do tipo do build.
12. Crie um arquivo zip **MyDotNetActivity.zip** contendo todos os binários na \<pasta do projeto\>\bin\Debug. Inclua o arquivo **MyDotNetActivity.pdb** para obter detalhes adicionais, como número de linha no código-fonte que causou o problema, se houver falha.

    > [!IMPORTANT]
    > Todos os arquivos no arquivo zip da atividade personalizada devem estar no **nível superior** , sem subpastas.

    ![Arquivos de saída binários](./media/data-factory-use-custom-activities/Binaries.png)
14. Crie um contêiner de blob chamado **customactivitycontainer** se ele ainda não existir.
15. Carregue MyDotNetActivity.zip como um blob para customactivitycontainer em um armazenamento de Blobs do Azure de **uso geral** (e não um armazenamento de Blobs de dinâmico/estático) que seja referenciado por AzureStorageLinkedService.

> [!IMPORTANT]
> Se você adicionar este projeto de atividade do .NET a uma solução no Visual Studio que contenha um projeto de Data Factory e adicionar uma referência ao projeto de atividade do .NET do projeto de aplicativo do Data Factory, não será necessário executar as duas últimas etapas de criar o arquivo zip e carregá-lo para o armazenamento de blobs do Azure de uso geral. Quando você publica entidades de Data Factory usando o Visual Studio, essas etapas são executadas automaticamente pelo processo de publicação. Para obter mais informações, consulte a seção [projeto de Data Factory no Visual Studio](#data-factory-project-in-visual-studio).

## <a name="create-a-pipeline-with-custom-activity"></a>Criar um pipeline com atividade personalizada
Você criou uma atividade personalizada e carregou o arquivo zip com binários em um contêiner de blobs em uma Conta de Armazenamento do Azure de **uso geral**. Nesta seção, você vai criar um Azure data factory com um pipeline que usa a atividade personalizada.

O conjunto de dados de entrada da atividade personalizada representa os blobs (arquivos) da pasta customactivityinput do contêiner adftutorial do armazenamento de blobs. O conjunto de dados de saída da atividade representa os blobs de saída da pasta customactivityinput do contêiner adftutorial do armazenamento de blobs.

Crie o arquivo **file.txt** com o conteúdo a seguir e carregue-o na pasta **customactivityinput** do contêiner **adftutorial**. Crie o contêiner adftutorial se ele ainda não existir.

```
test custom activity Microsoft test custom activity Microsoft
```

A pasta de entrada corresponde a uma fatia no Azure Data Factory, mesmo que a pasta tenha dois ou mais arquivos. Quando cada fatia é processada pelo pipeline, a atividade personalizada itera em todos os blobs na pasta de entrada dessa fatia.

Você vê um arquivo de saída na pasta adftutorial\customactivityoutput com um ou mais linhas (mesmo número de blobs na pasta de entrada):

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
```


Aqui estão as etapas que você executa nesta seção:

1. Criar uma **data factory**.
2. Crie **Serviços vinculados** para o pool de VMs do Lote do Azure em que a atividade personalizada é executada e o Armazenamento do Azure que contém os blobs de entrada/saída.
3. Crie **conjuntos de dados** de entrada e saída que representem a entrada e saída da atividade personalizada.
4. Crie um **pipeline** que usa a atividade personalizada.

> [!NOTE]
> Crie o **file.txt** e carregue-o em um contêiner de blobs se ainda não tiver feito isso. Consulte as instruções da seção anterior.

### <a name="step-1-create-the-data-factory"></a>Etapa 1: Criar o data factory
1. Depois de fazer logon no portal do Azure, execute as seguintes etapas:
   1. Clique em **Criar um recurso** no menu à esquerda.
   2. Clique em **Dados + Análise** na folha **Novo**.
   3. Clique em **Data Factory** na folha **Análise de dados**.

      ![Novo menu do Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-menu.png)
2. Na folha **Novo data factory**, insira **CustomActivityFactory** para o Nome. O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome de data factory "CustomActivityFactory" não está disponível**, altere o nome do data factory (por exemplo, **yournameCustomActivityFactory**) e tente criá-lo novamente.

    ![Nova folha do Azure Data Factory](media/data-factory-use-custom-activities/new-azure-data-factory-blade.png)
3. Clique em **NOME DO GRUPO DE RECURSOS**para selecionar um grupo de recursos existente ou criar um.
4. Depois de selecionar o grupo de recursos, verifique se que você está usando a **assinatura** correta e a **região** na qual deseja que o data factory seja criado.
5. Clique em **Criar** na folha **Novo data factory**.
6. Você vê o data factory sendo criado no **Painel** do portal do Azure.
7. Após a data factory ter sido criado com êxito, você vê a folha Data Factory, com a exibição do conteúdo.

    ![Folha Data Factory](media/data-factory-use-custom-activities/data-factory-blade.png)

### <a name="step-2-create-linked-services"></a>Etapa 2: Criar serviços vinculados
Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Nesta etapa, você vincula a conta de Armazenamento do Azure e a conta do Lote do Azure ao data factory.

#### <a name="create-azure-storage-linked-service"></a>Criar o serviço vinculado do armazenamento do Azure
1. Clique no bloco **Criar e implantar** na folha **DATA FACTORY** para **CustomActivityFactory**. Você vê o Data Factory Editor.
2. Clique em **Novo armazenamento de dados** na barra de comandos e escolha **Armazenamento do Azure**. Você deve ver o script JSON para criar um serviço de armazenamento vinculado do Azure no editor.

    ![Novo armazenamento de dados – Armazenamento do Azure](media/data-factory-use-custom-activities/new-data-store-menu.png)
3. Substitua o `<accountname>` pelo nome de sua conta de armazenamento do Azure e `<accountkey>` pela chave de acesso da conta de armazenamento do Azure. Para saber como obter sua chave de acesso de armazenamento, confira [Exibir, copiar e regenerar chaves de acesso de armazenamento](../../storage/common/storage-account-manage.md#access-keys).

    ![Serviço vinculado do Armazenamento do Azure](media/data-factory-use-custom-activities/azure-storage-linked-service.png)
4. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

#### <a name="create-azure-batch-linked-service"></a>Crie o serviço vinculado do Lote do Azure
1. No Editor de Data Factory, clique em **... Mais** na barra de comandos, clique em **Nova computação** e selecione **Lote do Azure** no menu.

    ![Nova computação – Lote do Azure](media/data-factory-use-custom-activities/new-azure-compute-batch.png)
2. Faça as alterações a seguir no script JSON:

   1. Especifique o nome da sua conta do Lote do Azure para a propriedade **accountName** . A **URL** da **folha da conta do Lote do Azure** está no seguinte formato: `http://accountname.region.batch.azure.com`. Para a propriedade **batchUri** em JSON, você precisará remover `accountname.` da URL e usar o `accountname` para a propriedade JSON `accountName`.
   2. Especifique a chave de conta do Lote do Azure para a propriedade **accessKey** .
   3. Especifique o nome do pool que você criou como parte dos pré-requisitos para a propriedade **poolName** . Você também pode especificar a ID do pool em vez do nome do pool.
   4. Especifique a URI do Lote do Azure para a propriedade **batchUri** . Exemplo: `https://westus.batch.azure.com`.
   5. Especifique **AzureStorageLinkedService** for the **linkedServiceName** .

        ```json
        {
          "name": "AzureBatchLinkedService",
          "properties": {
            "type": "AzureBatch",
            "typeProperties": {
              "accountName": "myazurebatchaccount",
              "batchUri": "https://westus.batch.azure.com",
              "accessKey": "<yourbatchaccountkey>",
              "poolName": "myazurebatchpool",
              "linkedServiceName": "AzureStorageLinkedService"
            }
          }
        }
        ```

       Para a propriedade **poolName** , você também pode especificar a ID do pool em vez do nome do pool.

### <a name="step-3-create-datasets"></a>Etapa 3: Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados para representar a entrada e saída de dados.

#### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
1. No **Editor** do Data Factory, clique em **... Mais** na barra de comandos, clique em **Novo conjunto de dados** e selecione **Armazenamento de Blobs do Azure** no menu suspenso.
2. Substitua o JSON no painel direito pelo snippet de código JSON a seguir:

    ```json
    {
        "name": "InputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/customactivityinput/",
                "format": {
                    "type": "TextFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```

   Você cria um pipeline posteriormente neste passo a passo com hora de início: 2016-11-16T00:00:00Z e hora de término: 2016-11-16T05:00:00Z. Ele fica agendado para gerar dados de hora em hora; portanto, há cinco fatias de entrada/saída (entre **00**:00:00 -> **05**:00:00).

   A **frequência** e o **intervalo** do conjunto de dados de entrada são definidos como **Hora** e **1**, o que significa que a fatia de entrada está disponível por hora. Neste exemplo, é o mesmo arquivo (file.txt) na intputfolder.

   Aqui estão as horas de início de cada fatia, representado pela variável de sistema SliceStart no snippet de código JSON acima.
3. Clique em **Implantar** na barra de ferramentas para implantar o **InputDataset**. Confirme que você vê a mensagem **TABELA CRIADA COM ÊXITO** na barra de título do Editor.

#### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
1. No **Editor de Data Factory**, clique em **... Mais** na barra de comandos, clique em **Novo conjunto de dados** e selecione **Armazenamento de Blobs do Azure**.
2. Substitua o script JSON no painel direito pelo script JSON a seguir:

    ```JSON
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "{slice}.txt",
                "folderPath": "adftutorial/customactivityoutput/",
                "partitionedBy": [
                    {
                        "name": "slice",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy-MM-dd-HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

     O local de saída é **adftutorial/customactivityoutput/** e o nome do arquivo de saída é aaaa-MM-dd-HH.txt onde aaaa-MM-dd-HH é o ano, mês, data e hora da fatia sendo produzida. Consulte a [Referência do Desenvolvedor][adf-developer-reference] para obter detalhes.

    Um blob/arquivo de saída é gerado para cada fatia de entrada. Aqui está como um arquivo de saída é chamado para cada fatia. Todos os arquivos de saída são gerados em uma pasta de saída: **adftutorial\customactivityoutput**.

   | Fatia | Hora de início | Arquivo de saída |
   |:--- |:--- |:--- |
   | 1 |2016-11-16T00:00:00 |2016-11-16-00.txt |
   | 2 |2016-11-16T01:00:00 |2016-11-16-01.txt |
   | 3 |2016-11-16T02:00:00 |2016-11-16-02.txt |
   | 4 |2016-11-16T03:00:00 |2016-11-16-03.txt |
   | 5 |2016-11-16T04:00:00 |2016-11-16-04.txt |

    Lembre-se de que todos os arquivos em uma pasta de entrada são parte de uma fatia com as horas de início mencionadas acima. Quando essa fatia é processada, a atividade personalizada examina cada arquivo e produz uma linha no arquivo de saída com o número de ocorrências do termo de pesquisa ("Microsoft"). Se houver três arquivos na inputfolder, haverá três linhas no arquivo de saída para cada fatia de hora: 2016-11-16-00.txt, 2016-11-16:01:00:00.txt etc.
3. Para implantar o **OutputDataset**, clique em **Implantar** na barra de comando.

### <a name="create-and-run-a-pipeline-that-uses-the-custom-activity"></a>Criar e executar um pipeline que usa a atividade personalizada
1. No Editor de Data Factory, clique em **... Mais**, e selecione **Novo pipeline** na barra de comandos.
2. Substitua o JSON no painel direito pelo script JSON a seguir:

    ```JSON
    {
      "name": "ADFTutorialPipelineCustom",
      "properties": {
        "description": "Use custom activity",
        "activities": [
          {
            "Name": "MyDotNetActivity",
            "Type": "DotNetActivity",
            "Inputs": [
              {
                "Name": "InputDataset"
              }
            ],
            "Outputs": [
              {
                "Name": "OutputDataset"
              }
            ],
            "LinkedServiceName": "AzureBatchLinkedService",
            "typeProperties": {
              "AssemblyName": "MyDotNetActivity.dll",
              "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
              "PackageLinkedService": "AzureStorageLinkedService",
              "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
              "extendedProperties": {
                "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
              }
            },
            "Policy": {
              "Concurrency": 2,
              "ExecutionPriorityOrder": "OldestFirst",
              "Retry": 3,
              "Timeout": "00:30:00",
              "Delay": "00:00:00"
            }
          }
        ],
        "start": "2016-11-16T00:00:00Z",
        "end": "2016-11-16T05:00:00Z",
        "isPaused": false
      }
    }
    ```

    Observe os seguintes pontos:

   * **Simultaneidade** é definido como **2** para que duas fatias sejam processadas em paralelo por 2 VMs no pool do Lote do Azure.
   * Há uma atividade na seção de atividades, que é do tipo: **DotNetActivity**.
   * **AssemblyName** é definido como o nome da DLL: **MyDotnetActivity.dll**.
   * **EntryPoint** é definido como **MyDotNetActivityNS.MyDotNetActivity**.
   * **PackageLinkedService** é definido como **AzureStorageLinkedService** que aponta para o armazenamento de blobs que contém o arquivo zip da atividade personalizada. Se você estiver usando diferentes contas de armazenamento do Azure para arquivos de entrada/saída e o arquivo zip da atividade personalizada, criará outro serviço vinculado do armazenamento do Azure. Este artigo pressupõe que você está usando a mesma conta de armazenamento do Azure.
   * **PackageFile** é definido como **customactivitycontainer/MyDotNetActivity.zip**. Ele está no formato: containerforthezip/nameofthezip.zip.
   * A atividade personalizada usa **InputDataset** como entrada e **OutputDataset** como saída.
   * A propriedade linkedServiceName da atividade personalizada aponta para o **AzureBatchLinkedService**, que informa ao Azure Data Factory que a atividade personalizada precisa ser executada em VMs do Lote do Azure.
   * A propriedade **isPaused** está definida para **false** por padrão. O pipeline é executado imediatamente neste exemplo porque a fatias começam no passado. Você pode definir essa propriedade como verdadeira para pausar o pipeline e defini-lo novamente como falsa para reiniciar.
   * As horas de **início** e **fim** estão distantes por **cinco** horas e as fatias são produzidas por hora, portanto, são produzidas cinco fatias pelo pipeline.
3. Para implantar o pipeline, clique em **Implantar** na barra de comando.

### <a name="monitor-the-pipeline"></a>Monitorar o Pipeline
1. Na folha Data Factory do portal do Azure, clique em **Diagrama**.

    ![Bloco do diagrama](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
2. Na exibição Diagrama, clique em OutputDataset.

    ![Modo de Exibição de Diagrama](./media/data-factory-use-custom-activities/diagram.png)
3. Você verá que as cinco fatias de saída estão no estado Pronto. Se não estiverem no estado Pronto, ainda não foram produzidas.

   ![Fatias de saída](./media/data-factory-use-custom-activities/OutputSlices.png)
4. Verifique se os arquivos de saída são gerados no armazenamento de blob no contêiner **adftutorial** .

   ![saída de atividade personalizada][image-data-factory-output-from-custom-activity]
5. Se você abrir o arquivo de saída, verá uma saída semelhante ao seguinte:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2016-11-16-00/file.txt.
    ```
6. Use o [Portal do Azure][azure-preview-portal] ou cmdlets do Azure PowerShell para monitorar seu data factory, pipelines e conjuntos de dados. Você pode ver as mensagens de **ActivityLogger** no código da atividade personalizada nos logs (especificamente, user-0.log) que você pode baixar do portal ou usando cmdlets.

   ![baixar logs de atividade personalizada][image-data-factory-download-logs-from-custom-activity]

Consulte [Monitorar e Gerenciar Pipelines](data-factory-monitor-manage-pipelines.md) para obter etapas detalhadas do monitoramento de conjuntos de dados e pipelines.

## <a name="data-factory-project-in-visual-studio"></a>Projeto de Data Factory no Visual Studio
Você pode criar e publicar entidades de Data Factory usando o Visual Studio, em vez de usar o portal do Azure. Para obter informações detalhadas de como criar e publicar as entidades de Data Factory usando o Visual Studio, consulte os artigos [Build your first pipeline using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) (Criar seu primeiro pipeline usando o Visual Studio) e [Copiar dados de Blob do Azure para o Azure SQL](data-factory-copy-activity-tutorial-using-visual-studio.md).

Execute as seguintes etapas adicionais se estiver criando um projeto de Data Factory no Visual Studio:

1. Adicione o projeto de Data Factory à solução do Visual Studio que contém o projeto de atividade personalizada.
2. Adicione uma referência ao projeto da atividade do .NET do projeto de Data Factory. Clique com o botão direito do mouse no projeto de Data Factory, aponte para **Adicionar** e, em seguida, clique em **Referência**.
3. Na caixa de diálogo **Adicionar Referência**, selecione o projeto **MyDotNetActivity** e clique em **OK**.
4. Crie e publique a solução.

    > [!IMPORTANT]
    > Quando você publica entidades de Data Factory, um arquivo zip é criado automaticamente para você e carregado para o contêiner de blob: customactivitycontainer. Se o contêiner de blob não existir, ele será automaticamente criado também.

## <a name="data-factory-and-batch-integration"></a>Integração de Data Factory e Lote
O serviço Data Factory cria um trabalho no Lote do Azure com o nome: **adf-poolname: job-xxx**. Clique em **Trabalhos** no menu à esquerda.

![Trabalhos de Azure Data Factory - Lote](media/data-factory-use-custom-activities/data-factory-batch-jobs.png)

Uma tarefa é criada para cada execução de atividade da fatia. Se houver cinco fatias prontas para serem processadas, cinco tarefas serão criadas neste trabalho. Se houver vários nós de computação no pool do Lote, duas ou mais fatias podem ser executadas em paralelo. Se o máximo de tarefas por nó de computação estiver definido como 1 >, você também pode ter mais de uma fatia em execução na mesma computação.

![Tarefas do trabalho de Azure Data Factory - Lote](media/data-factory-use-custom-activities/data-factory-batch-job-tasks.png)

O diagrama a seguir ilustra o relacionamento entre as tarefas do Azure Data Factory e o Lote.

![Data Factory e lote](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

## <a name="troubleshoot-failures"></a>Falhas na solução de problemas
A solução de problemas consiste em algumas técnicas básicas:

1. Se vir o erro a seguir, é provável que você esteja usando um armazenamento de blobs de blocos quente/frio em vez de usar um armazenamento de blobs do Azure de uso geral. Carregue o arquivo zip em uma **Conta de Armazenamento do Azure de uso geral**.

    ```
    Error in Activity: Job encountered scheduling error. Code: BlobDownloadMiscError Category: ServerError Message: Miscellaneous error encountered while downloading one of the specified Azure Blob(s).
    ```
2. Se vir o erro a seguir, confirme se o nome da classe no arquivo CS corresponde ao nome especificado para a propriedade **EntryPoint** no JSON do pipeline. No passo a passo, o nome da classe é: MyDotNetActivity e o EntryPoint no JSON é: MyDotNetActivityNS.**MyDotNetActivity**.

    ```
    MyDotNetActivity assembly does not exist or doesn't implement the type Microsoft.DataFactories.Runtime.IDotNetActivity properly
    ```

   Se os nomes forem correspondentes, confirme se todos os binários estão na **pasta raiz** do arquivo zip. Ou seja, ao abrir o arquivo zip, você deve ver todos os arquivos na pasta raiz, mas não em subpastas.
3. Se a fatia de entrada não estiver definida como **Pronto**, confirme se a estrutura da pasta de entrada está correta e se **file.txt** existe nas pastas de entrada.
3. No método **Execute** da atividade personalizada, use o objeto **IActivityLogger** para registrar informações que o ajudam a solucionar problemas. As mensagens registradas aparecem nos arquivos de log do usuário (um ou mais arquivos denominados: user-0.log, user-1.log, user-2.log, etc.).

   Na folha **OutputDataset**, clique na fatia para ver a folha **FATIA DE DADOS** dessa fatia. Você vê as **execuções de atividade** para essa fatia. Você deverá ver uma execução de atividade para a fatia. Se você clicar em Executar na barra de comandos, poderá iniciar outra execução de atividade para a mesma fatia.

   Quando você clicar na execução da atividade, verá a folha **DETALHES DE EXECUÇÃO DA ATIVIDADE** com uma lista de arquivos de log. Você vê mensagens registradas no arquivo user_0.log. Quando ocorrer um erro, você verá três execuções de atividade porque a contagem de repetições é definida como 3 no JSON do pipeline/atividade. Quando você clicar na execução da atividade, verá os arquivos de log que pode examinar para solucionar o erro.

   Na lista de arquivos de log, clique em **user-0.loo**. No painel à direita, estão os resultados do uso do método **IActivityLogger.Write** . Se não ver todas as mensagens, verifique se você terá mais arquivos de log chamados: user_1.log, user_2.log, etc. Caso contrário, o código pode ter falhado depois da última mensagem registrada.

   Verifique **system-0.log** para quaisquer mensagens de erro e exceções do sistema.
4. Inclua o arquivo **PDB** no arquivo zip para que os detalhes do erro tenham informações como **pilha de chamadas** quando ocorrer um erro.
5. Todos os arquivos no arquivo zip da atividade personalizada devem estar no **nível superior** , sem subpastas.
6. Verifique se **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) e **packageLinkedService** (devem apontar para o armazenamento de blobs do Azure de **uso geral** que contém o arquivo zip) estão definidos com os valores corretos.
7. Se você corrigir um erro e quiser reprocessar a fatia, clique com o botão direito do mouse na fatia na folha **OutputDataset** e clique em **Executar**.
8. Se você vir o erro a seguir, você está usando o pacote do armazenamento do Azure de versão > 4.3.0. O iniciador do serviço de Fábrica de Dados requer a versão 4.3 do WindowsAzure.Storage. Consulte a seção [Isolamento de Appdomain](#appdomain-isolation) para ver uma solução alternativa se você precisar usar a versão mais recente do assembly de armazenamento do Azure.

    ```
    Error in Activity: Unknown error in module: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.TypeLoadException: Could not load type 'Microsoft.WindowsAzure.Storage.Blob.CloudBlob' from assembly 'Microsoft.WindowsAzure.Storage, Version=4.3.0.0, Culture=neutral,
    ```

    Se você puder usar a versão 4.3.0 do pacote de Armazenamento do Azure, remova a referência existente ao pacote anterior à versão 4.3.0. Em seguida, execute o comando a seguir no Console do Gerenciador de Pacotes NuGet.

    ```powershell
    Install-Package WindowsAzure.Storage -Version 4.3.0
    ```

    Compile o projeto. Exclua o assembly Azure.Storage de versão > 4.3.0 da pasta bin\Debug. Crie um arquivo zip com binários e o arquivo PDB. Substitua o arquivo zip antigo por esse no contêiner de blob (customactivitycontainer). Execute novamente as fatias com falha (clique com o botão direito na fatia e clique em Executar).
8. A atividade personalizada não usa o arquivo **app.config** do seu pacote. Portanto, se seu código lê as cadeias de conexão do arquivo de configuração, ele não funciona no tempo de execução. A prática recomendada ao usar o Lote do Azure é armazenar os segredos em um **Azure KeyVault**, usar uma entidade de serviço com base em certificados para proteger o **keyvault** e distribuir o certificado para o pool do Lote do Azure. A atividade personalizada do .NET pode então acessar segredos no KeyVault no tempo de execução. Essa é uma solução genérica e pode ser dimensionada para qualquer tipo de segredo, não apenas a cadeia de conexão.

   Há uma solução alternativa mais fácil (mas não é uma prática recomendada): você pode criar um **serviço vinculado do SQL Azure** com configurações da cadeia de conexão, criar um conjunto de dados que usa o serviço vinculado e encadear o conjunto de dados como um conjunto de dados de entrada fictício para a atividade personalizada do .NET. Você pode então acessar a cadeia de conexão do serviço vinculado no código de atividade personalizada.

## <a name="update-custom-activity"></a>Atualize a atividade personalizada
Se você atualizar o código para a atividade personalizada, compile-o e carregue o arquivo zip que contém os novos binários para o armazenamento de blob.

## <a name="appdomain-isolation"></a>Isolamento de Appdomain
Confira [Exemplo de AppDomain Cruzado](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) , que mostra como criar uma atividade personalizada que não esteja restrita às versões de assembly usadas pelo iniciador do Data Factory (por exemplo: WindowsAzure.Storage v4.3.0, Newtonsoft.Json v6.0.x etc.).

## <a name="access-extended-properties"></a>Acessar propriedades estendidas
Você pode declarar propriedades estendidas na atividade JSON, conforme mostrado na amostra a seguir:

```JSON
"typeProperties": {
  "AssemblyName": "MyDotNetActivity.dll",
  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
  "PackageLinkedService": "AzureStorageLinkedService",
  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
  "extendedProperties": {
    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
    "DataFactoryName": "CustomActivityFactory"
  }
},
```

No exemplo, há duas propriedades estendidas: **SliceStart** e **DataFactoryName**. O valor de SliceStart baseia-se na variável de sistema SliceStart. Consulte [Variáveis de Sistema](data-factory-functions-variables.md) para obter uma lista das variáveis de sistema com suporte. O valor de DataFactoryName é embutido no código como CustomActivityFactory.

Para acessar essas propriedades estendidas no método **Execute**, use um código semelhante ao seguinte:

```csharp
// to get extended properties (for example: SliceStart)
DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

// to log all extended properties
IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
logger.Write("Logging extended properties if any...");
foreach (KeyValuePair<string, string> entry in extendedProperties)
{
    logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
}
```

## <a name="auto-scaling-of-azure-batch"></a>Dimensionamento automático do Lote do Azure
Você também pode criar um pool de Lotes do Azure com o recurso **autoscale** . Por exemplo, você poderia criar um pool do Lote do Azure sem nenhuma VM dedicada e uma fórmula de escala automática com base no número de tarefas pendentes.

A fórmula de exemplo aqui obtém o comportamento a seguir: Quando o pool é criado inicialmente, ele começa com uma VM. A métrica de $PendingTasks define o número de tarefas em execução + estado ativo (em fila).  A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e define TargetDedicated adequadamente. Isso garante que TargetDedicated nunca ultrapasse 25 VMs. Assim, o pool aumenta automaticamente conforme novas tarefas são enviadas e, conforme as tarefas são concluídas, as VMs se liberam uma a uma e são reduzidas pelo dimensionamento automático. startingNumberOfVMs e maxNumberofVMs podem ser ajustados às suas necessidades.

Fórmula de dimensionamento automático:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Consulte [Dimensionar automaticamente os nós de computação em um pool de Lotes do Azure](../../batch/batch-automatic-scaling.md) para obter detalhes.

Se o pool estiver usando o padrão [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), o serviço Lote poderá demorar de 15 a 30 minutos para preparar a VM antes de executar a atividade personalizada.  Se o pool estiver usando um autoScaleEvaluationInterval diferente, o serviço de lote pode levar autoScaleEvaluationInterval + 10 minutos.


## <a name="create-a-custom-activity-by-using-net-sdk"></a>Criar uma atividade personalizada usando o SDK do .NET
No passo a passo deste artigo, você pode criar um data factory com um pipeline que use a atividade personalizada usando o portal do Azure. O código a seguir mostra como criar o data factory usando o SDK do .NET em vez disso. Você pode encontrar mais detalhes sobre como usar o SDK para criar programaticamente pipelines no artigo [Criar um pipeline com Atividade de Cópia usando a API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md).

```csharp
using System;
using System.Configuration;
using System.Collections.ObjectModel;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.Azure;
using Microsoft.Azure.Management.DataFactories;
using Microsoft.Azure.Management.DataFactories.Models;
using Microsoft.Azure.Management.DataFactories.Common.Models;

using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Collections.Generic;

namespace DataFactoryAPITestApp
{
    class Program
    {
        static void Main(string[] args)
        {
            // create data factory management client

            // TODO: replace ADFTutorialResourceGroup with the name of your resource group.
            string resourceGroupName = "ADFTutorialResourceGroup";

            // TODO: replace APITutorialFactory with a name that is globally unique. For example: APITutorialFactory04212017
            string dataFactoryName = "APITutorialFactory";

            TokenCloudCredentials aadTokenCredentials = new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader().Result);

            Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

            DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

            Console.WriteLine("Creating a data factory");
            client.DataFactories.CreateOrUpdate(resourceGroupName,
                new DataFactoryCreateOrUpdateParameters()
                {
                    DataFactory = new DataFactory()
                    {
                        Name = dataFactoryName,
                        Location = "westus",
                        Properties = new DataFactoryProperties()
                    }
                }
            );

            // create a linked service for input data store: Azure Storage
            Console.WriteLine("Creating Azure Storage linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureStorageLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: Replace <accountname> and <accountkey> with name and key of your Azure Storage account.
                            new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>")
                        )
                    }
                }
            );

            // create a linked service for output data store: Azure SQL Database
            Console.WriteLine("Creating Azure Batch linked service");
            client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new LinkedServiceCreateOrUpdateParameters()
                {
                    LinkedService = new LinkedService()
                    {
                        Name = "AzureBatchLinkedService",
                        Properties = new LinkedServiceProperties
                        (
                            // TODO: replace <batchaccountname> and <yourbatchaccountkey> with name and key of your Azure Batch account
                            new AzureBatchLinkedService("<batchaccountname>", "https://westus.batch.azure.com", "<yourbatchaccountkey>", "myazurebatchpool", "AzureStorageLinkedService")
                        )
                    }
                }
            );

            // create input and output datasets
            Console.WriteLine("Creating input and output datasets");
            string Dataset_Source = "InputDataset";
            string Dataset_Destination = "OutputDataset";

            Console.WriteLine("Creating input dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Source,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FolderPath = "adftutorial/customactivityinput/",
                                Format = new TextFormat()
                            },
                            External = true,
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },

                            Policy = new Policy() { }
                        }
                    }
                });

            Console.WriteLine("Creating output dataset of type: Azure Blob");
            client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new DatasetCreateOrUpdateParameters()
                {
                    Dataset = new Dataset()
                    {
                        Name = Dataset_Destination,
                        Properties = new DatasetProperties()
                        {
                            LinkedServiceName = "AzureStorageLinkedService",
                            TypeProperties = new AzureBlobDataset()
                            {
                                FileName = "{slice}.txt",
                                FolderPath = "adftutorial/customactivityoutput/",
                                PartitionedBy = new List<Partition>()
                                {
                                    new Partition()
                                    {
                                        Name = "slice",
                                        Value = new DateTimePartitionValue()
                                        {
                                            Date = "SliceStart",
                                            Format = "yyyy-MM-dd-HH"
                                        }
                                    }
                                }
                            },
                            Availability = new Availability()
                            {
                                Frequency = SchedulePeriod.Hour,
                                Interval = 1,
                            },
                        }
                    }
                });

            Console.WriteLine("Creating a custom activity pipeline");
            DateTime PipelineActivePeriodStartTime = new DateTime(2017, 3, 9, 0, 0, 0, 0, DateTimeKind.Utc);
            DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
            string PipelineName = "ADFTutorialPipelineCustom";

            client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
                new PipelineCreateOrUpdateParameters()
                {
                    Pipeline = new Pipeline()
                    {
                        Name = PipelineName,
                        Properties = new PipelineProperties()
                        {
                            Description = "Use custom activity",

                            // Initial value for pipeline's active period. With this, you won't need to set slice status
                            Start = PipelineActivePeriodStartTime,
                            End = PipelineActivePeriodEndTime,
                            IsPaused = false,

                            Activities = new List<Activity>()
                            {
                                new Activity()
                                {
                                    Name = "MyDotNetActivity",
                                    Inputs = new List<ActivityInput>()
                                    {
                                        new ActivityInput() {
                                            Name = Dataset_Source
                                        }
                                    },
                                    Outputs = new List<ActivityOutput>()
                                    {
                                        new ActivityOutput()
                                        {
                                            Name = Dataset_Destination
                                        }
                                    },
                                    LinkedServiceName = "AzureBatchLinkedService",
                                    TypeProperties = new DotNetActivity()
                                    {
                                        AssemblyName = "MyDotNetActivity.dll",
                                        EntryPoint = "MyDotNetActivityNS.MyDotNetActivity",
                                        PackageLinkedService = "AzureStorageLinkedService",
                                        PackageFile = "customactivitycontainer/MyDotNetActivity.zip",
                                        ExtendedProperties = new Dictionary<string, string>()
                                        {
                                            { "SliceStart", "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"}
                                        }
                                    },
                                    Policy = new ActivityPolicy()
                                    {
                                        Concurrency = 2,
                                        ExecutionPriorityOrder = "OldestFirst",
                                        Retry = 3,
                                        Timeout = new TimeSpan(0,0,30,0),
                                        Delay = new TimeSpan()
                                    }
                                }
                            }
                        }
                    }
                });
        }

        public static async Task<string> GetAuthorizationHeader()
        {
            AuthenticationContext context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
            ClientCredential credential = new ClientCredential(
                ConfigurationManager.AppSettings["ApplicationId"],
                ConfigurationManager.AppSettings["Password"]);
            AuthenticationResult result = await context.AcquireTokenAsync(
                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                clientCredential: credential);

            if (result != null)
                return result.AccessToken;

            throw new InvalidOperationException("Failed to acquire token");
        }
    }
}
```

## <a name="debug-custom-activity-in-visual-studio"></a>Depurar atividade personalizada no Visual Studio
O exemplo do [Azure Data Factory – ambiente local](https://github.com/gbrueckl/Azure.DataFactory.LocalEnvironment) no GitHub inclui uma ferramenta que permite que depurar atividades personalizadas do .NET no Visual Studio.

## <a name="sample-custom-activities-on-github"></a>Exemplo de atividades personalizadas no GitHub
| Amostra | Qual atividade personalizada realiza |
| --- | --- |
| [HTTP Data Downloader](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/HttpDataDownloaderSample). |Baixa dados de um ponto de extremidade de HTTP para o Armazenamento de Blobs do Azure usando uma atividade de C# personalizada no Data Factory. |
| [Exemplo de análise de opinião no Twitter](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/TwitterAnalysisSample-CustomC%23Activity) |Invoca um modelo do Azure Machine Learning Studio e faz análise de sentimento, pontuação, previsão, etc. |
| [Executar Script R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample). |Invoca o script de R executando o RScript.exe no seu cluster do HDInsight que já tem o R instalado nele. |
| [Atividade cruzada do .NET no AppDomain](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/CrossAppDomainDotNetActivitySample) |Usa versões de assembly diferente daquelas usadas pelo iniciador do Data Factory |
| [Reprocessar um modelo no Azure Analysis Services](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/AzureAnalysisServicesProcessSample) |  Reprocessar um modelo no Azure Analysis Services. |

[batch-net-library]: ../../batch/batch-dotnet-get-started.md
[batch-create-account]: ../../batch/batch-account-create-portal.md
[batch-technical-overview]: ../../batch/batch-technical-overview.md
[batch-get-started]: ../../batch/batch-dotnet-get-started.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: https://go.microsoft.com/fwlink/?LinkId=517478
[adf-developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-output-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png
