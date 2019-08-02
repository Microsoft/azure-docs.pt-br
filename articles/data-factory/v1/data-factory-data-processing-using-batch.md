---
title: Processar conjuntos de dados em larga escala usando o Data Factory e o Lote | Microsoft Docs
description: Descreve como processar volumes grandes de dados em um pipeline do Azure Data Factory usando a capacidade de processamento paralelo do Lote do Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 67829b6245fe4fea8da88c97fa8d5aeedccc90a0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446622"
---
# <a name="process-large-scale-datasets-by-using-data-factory-and-batch"></a>Processar conjuntos de dados em larga escala usando o Data Factory e o Lote
> [!NOTE]
> Este artigo se aplica à versão 1 do Azure Data Factory, que geralmente está disponível. Se você estiver usando a versão atual do serviço Data Factory, consulte [Atividades personalizadas no Data Factory](../transform-data-using-dotnet-custom-activity.md).

Este artigo descreve uma arquitetura de um exemplo de solução que move e processa os conjuntos de dados em larga escala de maneira automática e agendada. Ele também fornece um passo a passo completo para implementar a solução usando o Data Factory e o Lote do Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artigo é maior que um artigo típico porque contém um passo a passo de uma solução de exemplo inteira. Se estiver conhecendo o Lote e o Data Factory agora, saiba mais sobre esses serviços e como eles funcionam juntos. Se você conhece algo sobre os serviços e está criando/projetando uma solução, concentre-se na seção sobre arquitetura do artigo. Se estiver desenvolvendo um protótipo ou uma solução, você pode usar as instruções passo a passo. Seus comentários sobre esse conteúdo e como usá-lo são bem-vindos.

Primeiro, vamos observar como os serviços Data Factory e Lote podem ajudá-lo a processar conjuntos de dados grandes na nuvem.     


## <a name="why-azure-batch"></a>Por que usar o Lote do Azure?
 Use o Lote para executar aplicativos HPC (computação de alto desempenho) e paralelos em larga escala com eficiência na nuvem. É um serviço de plataforma que agenda trabalhos com uso intensivo de computação para execução em uma coleção gerenciada de VMs (máquinas virtuais). Pode dimensionar automaticamente os recursos de computação conforme as necessidades de seus trabalhos.

Com o serviço em Lotes, você define os recursos de computação do Azure para executar os aplicativos em paralelo e em escala. Execute trabalhos sob demanda ou agendados. Não é necessário criar, configurar e gerenciar manualmente um cluster HPC, VMs individuais, redes virtuais ou uma infraestrutura complexa de agendamento de tarefas e trabalhos.

 Caso não esteja familiarizado com o Lote, os seguintes artigos ajudarão você a entender a arquitetura/implementação da solução descrita neste artigo:   

* [Conceitos básicos do Lote](../../batch/batch-technical-overview.md)
* [Visão geral do recurso de Lote](../../batch/batch-api-basics.md)

Opcionalmente, para saber mais sobre o lote, consulte [documentação do lote](https://docs.microsoft.com/azure/batch/).

## <a name="why-azure-data-factory"></a>Por que usar o Azure Data Factory?
O Data Factory é um serviço de integração de dados baseado em nuvem que automatiza a movimentação e a transformação dos dados. Use o Data Factory para criar pipelines de dados gerenciados que movem dados dos armazenamentos de dados locais e na nuvem para um armazenamento de dados centralizado. Um exemplo é o armazenamento de Blobs do Azure. Use o Data Factory para o processar/transformar dados usando serviços como o Azure HDInsight e Azure Machine Learning. Também agende pipelines de dados para serem executados de maneira agendada (por exemplo, por hora, diariamente e semanalmente). Monitore e gerencie rapidamente os pipelines de dados para identificar problemas e agir.

  Caso não esteja familiarizado com o Data Factory, os seguintes artigos ajudarão você a entender a arquitetura/implementação da solução descrita neste artigo:  

* [Introdução ao Data Factory](data-factory-introduction.md)
* [Criar seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md)   

Opcionalmente, para saber mais sobre o Data Factory, consulte a [Documentação do Data Factory](https://docs.microsoft.com/rest/api/datafactory/v1/data-factory-data-factory).

## <a name="data-factory-and-batch-together"></a>Data Factory e Lote juntos
O Data Factory inclui atividades internas. Por exemplo, a atividade de Cópia é usada para copiar/mover dados de um armazenamento de dados de origem para um armazenamento de dados de destino. A atividade do Hive é usada para processar dados usando clusters Hadoop (HDInsight) no Azure. Para obter uma lista de atividades de transformação compatíveis, consulte [Atividades de transformação de dados](data-factory-data-transformation-activities.md).

Também crie atividades personalizadas do .NET para mover ou processar dados com sua própria lógica. Execute essas atividades em um cluster HDInsight ou em um pool do Lote de VMs. Ao usar o Lote, configure o pool para ser dimensionado automaticamente (adicionar ou remover VMs de acordo com a carga de trabalho) com base em uma fórmula fornecida.     

## <a name="architecture-of-a-sample-solution"></a>Arquitetura de uma solução de exemplo
  A arquitetura descrita neste artigo refere-se a uma solução simples. Também é relevante para cenários complexos, como modelagem de risco por serviços financeiros, processamento e renderização de imagem e análise de genoma.

O diagrama ilustra como o Data Factory orquestra a movimentação e o processamento de dados. Também mostra como o Lote processa os dados de forma paralela. Baixe e imprima o diagrama para fácil referência (297 x 420 mm ou tamanho A3). Para acessar o diagrama para que você possa imprimi-lo, consulte [Orquestração de dados e HPC usando o Data Factory e o Lote](https://go.microsoft.com/fwlink/?LinkId=717686).

[![Diagrama de processamento de dados em larga escala](./media/data-factory-data-processing-using-batch/image1.png)](https://go.microsoft.com/fwlink/?LinkId=717686)

A lista a seguir fornece as etapas básicas do processo. A solução inclui código e explicações para criar a solução de ponta a ponta.

* **Configure o Lote com um pool de nós de computação (VMs).** Especifique o número de nós e o tamanho de cada nó.

* **Crie uma instância do Data Factory** configurada com entidades que representam o armazenamento de blobs, o serviço de computação do Lote, os dados de entrada/saída e um fluxo de trabalho/pipeline com atividades que movem e transformam dados.

* **Crie uma atividade personalizada do .NET no pipeline do Data Factory.** A atividade é seu código de usuário executado no pool do Lote.

* **Armazene grandes quantidades de dados de entrada como blobs no Armazenamento do Azure.** Os dados são divididos em fatias lógicas (geralmente, por hora).

* **O Data Factory copia os dados que são processados em paralelo** para a localização secundária.

* **O Data Factory executa a atividade personalizada usando o pool alocado pelo Lote.** O Data Factory pode executar atividades simultaneamente. Cada atividade processa uma fatia de dados. Os resultados são armazenados no armazenamento.

* **O Data Factory move os resultados finais para um terceiro local** para distribuição por meio de um aplicativo ou para processamento adicional por outras ferramentas.

## <a name="implementation-of-the-sample-solution"></a>Implementação da solução de exemplo
A solução de exemplo é intencionalmente simples. Ela foi projetada para mostrar como usar o Data Factory e o Lote juntos para processar conjuntos de dados. A solução conta o número de ocorrências do termo de pesquisa “Microsoft” em arquivos de entrada organizados em uma série temporal. Em seguida, ele gera a contagem em arquivos de saída.

**Tempo:** se estiver familiarizado com os conceitos básicos do Azure, do Data Factory e do Lote e tiver concluído os pré-requisitos a seguir, essa solução levará de uma a duas horas para ser concluída.

### <a name="prerequisites"></a>Pré-requisitos
#### <a name="azure-subscription"></a>Assinatura do Azure
Se você não tem uma assinatura do Azure, crie uma conta de avaliação gratuita rapidamente. Para obter mais informações, consulte [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Conta de Armazenamento do Azure
Use uma conta de armazenamento para armazenar os dados deste tutorial. Se você não tem uma conta de armazenamento, consulte [Criar uma conta de armazenamento](../../storage/common/storage-quickstart-create-account.md). A solução de exemplo usa o armazenamento de blobs.

#### <a name="azure-batch-account"></a>Conta do Lote do Azure
Crie uma conta do Lote usando o [portal do Azure](https://portal.azure.com/). Para obter mais informações, consulte [Criar e gerenciar uma conta do Lote](../../batch/batch-account-create-portal.md). Anote a chave e o nome da conta do Lote. Você também pode usar o [New-AzBatchAccount](https://docs.microsoft.com/powershell/module/az.batch/new-azbatchaccount) para criar uma conta do lote. Para obter instruções sobre como usar esse cmdlet, consulte [Introdução aos cmdlets do PowerShell do Lote](../../batch/batch-powershell-cmdlets-get-started.md).

A solução de exemplo usa o Lote (indiretamente por meio de um pipeline do data factory) para processar dados de forma paralela em um pool de nós de computação (uma coleção gerenciada de VMs).

#### <a name="azure-batch-pool-of-virtual-machines"></a>Pool do Lote do Azure de máquinas virtuais
Crie um pool do Lote com pelo menos dois nós de computação.

1. No [portal do Azure](https://portal.azure.com), selecione **Procurar** no menu à esquerda e **Contas do Lote**.

1. Selecione sua conta do Lote para abrir a folha **Conta do Lote**.

1. Selecione o bloco **Pools**.

1. Na folha **Pools**, selecione o botão **Adicionar** na barra de ferramentas para adicionar um pool.

   a. Insira uma ID para o pool (**ID do Pool**). Anote a ID do pool. Você precisará dela quando criar a solução de data factory.

   b. Especifique **Windows Server 2012 R2** para a configuração **Família de Sistemas Operacionais**.

   c. Selecione um **camada de preços de nó**.

   d. Insira **2** como o valor para a configuração **Destino Dedicado**.

   e. Insira **2** como o valor para a configuração **Máximo de tarefas por nó**.

   f. Selecione **OK** para criar o pool.

#### <a name="azure-storage-explorer"></a>Gerenciador de Armazenamento do Azure
Use o [Gerenciador de Armazenamento do Azure 6](https://azurestorageexplorer.codeplex.com/) ou o [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer) (da ClumsyLeaf Software) para inspecionar e alterar os dados em seus projetos do Armazenamento. Também inspecione e altere os dados nos logs de seus aplicativos hospedados na nuvem.

1. Crie um contêiner chamado **mycontainer** com acesso particular (sem acesso anônimo).

1. Se você usar o CloudXplorer, crie pastas e subpastas com a seguinte estrutura:

   ![Estrutura de pastas e subpastas](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` e `outputfolder` são pastas de nível superior no `mycontainer`. A pasta `inputfolder` contém subpastas com carimbos de data/hora (AAAA-MM-DD-HH).

   Se você usar o Gerenciador de Armazenamento, na próxima etapa, você carregará arquivos com os seguintes nomes: `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt` e assim por diante. Essa etapa cria as pastas automaticamente.

1. Crie um arquivo de texto **file.txt** no computador com o conteúdo com a palavra-chave **Microsoft**. Um exemplo é “testar atividade personalizada Microsoft testar atividade personalizada Microsoft”.

1. Carregue o arquivo para as seguintes pastas de entrada no armazenamento de blobs:

   ![Pastas de entrada](./media/data-factory-data-processing-using-batch/image4.png)

   Se você usar o Gerenciador de Armazenamento, carregue o arquivo **file.txt** em **mycontainer**. Selecione **Copiar** na barra de ferramentas para criar uma cópia do blob. Na caixa de diálogo **Copiar Blob**, altere o **nome do blob de destino** para `inputfolder/2015-11-16-00/file.txt`. Repita essa etapa para criar `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt` e assim por diante. Essa ação cria as pastas automaticamente.

1. Crie outro contêiner chamado `customactivitycontainer`. Carregue o arquivo zip da atividade personalizada nesse contêiner.

#### <a name="visual-studio"></a>Visual Studio
Instale o Visual Studio 2012 ou posterior para criar a atividade personalizada do Lote a ser usada na solução de data factory.

### <a name="high-level-steps-to-create-the-solution"></a>Etapas de alto nível para criar a solução
1. Crie uma atividade personalizada que contenha a lógica de processamento de dados.

1. Crie um data factory que usa a atividade personalizada.

### <a name="create-the-custom-activity"></a>Criar a atividade personalizada
A atividade personalizada do data factory é o centro desta solução de exemplo. A solução de exemplo usa o Lote para executar a atividade personalizada. Para obter informações sobre como desenvolver atividades personalizadas e usá-las em pipelines do data factory, consulte [Usar atividades personalizadas em um pipeline do data factory](data-factory-use-custom-activities.md).

Para criar uma atividade personalizada do .NET que pode ser usada em um pipeline do data factory, crie um projeto da biblioteca de classes do .NET com uma classe que implemente a interface IDotNetActivity. Essa interface tem apenas um método: Execute. Veja a assinatura desse método:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

O método tem alguns componentes principais que você precisa entender:

* O método utiliza quatro parâmetros:

  * **linkedServices**. Esse parâmetro é uma lista enumerável de serviços vinculados que vinculam fontes de dados de entrada/saída (por exemplo: armazenamento de blobs) ao data factory. Nesta amostra, há apenas um serviço vinculado do tipo Armazenamento do Azure usado para entrada e saída.
  * **conjuntos de dados**. Esse parâmetro é uma lista enumerável de conjuntos de dados. Você pode usar esse parâmetro para obter os locais e esquemas definidos por conjuntos de dados de entrada e saída.
  * **atividade**. Esse parâmetro representa a entidade de computação atual. Nesse caso, esse é um serviço do Lote.
  * **logger**. Use o agente para escrever comentários de depuração exibidos como o log do “Usuário” no pipeline.
* O método retorna um dicionário que pode ser usado para unir atividades personalizadas no futuro. Esse recurso ainda não foi implementado e, portanto, basta retornar um dicionário vazio do método.

#### <a name="procedure-create-the-custom-activity"></a>Procedimento: Criar a atividade personalizada
1. Crie um projeto de biblioteca de classes .NET no Visual Studio.

   a. Inicie o Visual Studio 2012/2013/2015.

   b. Selecione **Arquivo** > **Novo** > **Projeto**.

   c. Expanda **Modelos** e selecione **Visual C\#** . Neste passo a passo, você pode usar C\#, mas pode usar qualquer linguagem .NET para desenvolver a atividade personalizada.

   d. Selecione **Biblioteca de Classes** na lista de tipos de projeto à direita.

   e. Insira **MyDotNetActivity** for the **Nome**.

   f. Selecione **C:\\ADF** para a **Localização**. Crie a pasta **ADF**, caso ela não exista.

   g. Selecione **OK** para criar o projeto.

1. Selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**.

1. No Console do Gerenciador de Pacotes, execute o seguinte comando para importar Microsoft.Azure.Management.DataFactories:

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
1. Importe o pacote NuGet do **Armazenamento do Azure** para o projeto. Você precisa desse pacote porque usa a API de Armazenamento de Blobs nesta amostra:

    ```powershell
    Install-Package Az.Storage
    ```
1. Adicione as seguintes diretivas using ao arquivo de origem no projeto:

    ```csharp
    using System.IO;
    using System.Globalization;
    using System.Diagnostics;
    using System.Linq;
    
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Runtime;
    
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```
1. Altere o nome do namespace para **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
1. Altere o nome da classe para **MyDotNetActivity** e derive-a da interface **IDotNetActivity**, conforme mostrado:

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
1. Implemente (adicione) o método **Execute** da interface **IDotNetActivity** na classe **MyDotNetActivity**. Copie o código de exemplo a seguir para o método. Para obter uma explicação da lógica usada nesse método, consulte a seção [Método Execute](#execute-method).

    ```csharp
    /// <summary>
    /// The Execute method is the only method of IDotNetActivity interface you must implement.
    /// In this sample, the method invokes the Calculate method to perform the core logic.  
    /// </summary>
    public IDictionary<string, string> Execute(
       IEnumerable<LinkedService> linkedServices,
       IEnumerable<Dataset> datasets,
       Activity activity,
       IActivityLogger logger)
    {
    
       // Declare types for the input and output data stores.
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // Use the First method instead of Single because we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
       string folderPath = GetFolderPath(inputDataset);
       string output = string.Empty; // for use later.
    
       // Create the storage client for input. Pass the connection string.
       CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
       CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();
    
       // Initialize the continuation token before using it in the do-while loop.
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
    
           // The Calculate method returns the number of occurrences of
           // the search term "Microsoft" in each blob associated
           // with the data slice.
           //
           // The definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // Create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // Write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
       logger.Write("output blob URI: {0}", outputBlobUri.ToString());
       // Create a blob and upload the output text.
       CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
       logger.Write("Writing {0} to the output blob", output);
       outputBlob.UploadText(output);
    
       // The dictionary can be used to chain custom activities together in the future.
       // This feature is not implemented yet, so just return an empty dictionary.
       return new Dictionary<string, string>();
    }
    ```
1. Adicione os seguintes métodos auxiliares à classe. Esses métodos são invocados pelo método **Execute** . O mais importante é que o método **Calculate** isola o código que itera por cada blob.

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
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
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
    
       AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
       if (blobDataset == null)
       {
           return null;
       }
    
       return blobDataset.FileName;
    }
    
    /// <summary>
    /// Iterates through each blob (file) in the folder, counts the number of instances of the search term in the file,
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
    O método GetFolderPath retorna o caminho para a pasta indicada pelo conjunto de dados e o método GetFileName retorna o nome do blob/arquivo para o qual o conjunto de dados aponta.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    O método Calculate calcula o número de instâncias da palavra-chave “Microsoft” nos arquivos de entrada (blobs na pasta). O termo de pesquisa “Microsoft” é embutido no código.

1. Compile o projeto. Selecione **Compilar** no menu e, em seguida, **Compilar Solução**.

1. Inicie o Windows Explorer e acesse a pasta **bin\\debug** ou **bin\\release**. A escolha da pasta depende do tipo de build.

1. Crie um arquivo zip **MyDotNetActivity.zip** que contém todos os binários na pasta **\\bin\\Debug**. Recomendamos incluir o arquivo MyDotNetActivity.**pdb** para obter detalhes adicionais, como o número de linha no código-fonte que causou o problema quando ocorre uma falha.

   ![A lista da pasta bin\Debug](./media/data-factory-data-processing-using-batch/image5.png)

1. Carregue **MyDotNetActivity.zip** como um blob no contêiner de blobs `customactivitycontainer`, no armazenamento de blobs utilizado pelo serviço vinculado StorageLinkedService no ADFTutorialDataFactory. Crie o contêiner de blobs `customactivitycontainer`, caso ele ainda não exista.

#### <a name="execute-method"></a>Método Execute
Esta seção fornece mais detalhes sobre o código no método Execute.

1. Os membros para iteração pela coleção de entrada são encontrados no namespace [Microsoft.WindowsAzure.Storage.Blob](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob) . Para iterar pela coleção de blobs, é necessário usar a classe **BlobContinuationToken**. Em essência, você deve usar um loop do-while com o token, como o mecanismo para saída do loop. Para obter mais informações, consulte [Usar o armazenamento de Blobs no .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Um loop básico é mostrado aqui:

    ```csharp
    // Initialize the continuation token.
    BlobContinuationToken continuationToken = null;
    do
    {
    // Get the list of input blobs from the input storage client object.
    BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    
                         true,
                                   BlobListingDetails.Metadata,
                                   null,
                                   continuationToken,
                                   null,
                                   null);
    // Return a string derived from parsing each blob.
    
     output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
    } while (continuationToken != null);

    ```
   Para obter mais informações, consulte a documentação do método [ListBlobsSegmented](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobssegmented).

1. O código para trabalhar com o conjunto de blobs logicamente fica dentro do loop do-while. No método **Execute**, o loop do-while passa a lista de blobs para um método chamado **Calculate**. O método retorna uma variável de cadeia de caracteres chamada **output** , que é o resultado da iteração nos blobs do segmento.

   Ele retorna o número de ocorrências do termo de pesquisa “Microsoft” no blob passado para o método **Calculate**.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
1. Depois que o método **Calculate** for concluído, ele precisará ser gravado em um novo blob. Para cada conjunto de blobs processado, um novo blob pode ser gravado com os resultados. Para gravar um novo blob, primeiro localize o conjunto de dados de saída.

    ```csharp
    // Get the output dataset by using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
1. O código também chama o método auxiliar **GetFolderPath** para recuperar o caminho da pasta (o nome do contêiner de armazenamento).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   O método GetFolderPath converte o objeto DataSet em um AzureBlobDataSet, que tem uma propriedade chamada FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
1. O código chama o método **GetFileName** para recuperar o nome do arquivo (nome do blob). O código é semelhante ao código anterior que foi usado para obter o caminho da pasta.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
1. O nome do arquivo é gravado, ao criar um objeto URI. O construtor de URI usa a propriedade **BlobEndpoint** propriedade para retornar o nome do contêiner. O nome do arquivo e caminho da pasta são adicionados para construir o URI do blob de saída.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
1. Depois de gravar o nome do arquivo, grave a cadeia de caracteres de saída do método **Calculate** em um novo blob:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Criar o data factory
Na seção [Criar a atividade personalizada](#create-the-custom-activity), você criou uma atividade personalizada e carregou o arquivo zip com binários e o arquivo PDB em um contêiner de blobs. Nesta seção, você cria um data factory com um pipeline que usa a atividade personalizada.

O conjunto de dados de entrada da atividade personalizada representa os blobs (arquivos) na pasta de entrada (`mycontainer\\inputfolder`) no armazenamento de blobs. O conjunto de dados de saída da atividade representa os blobs de saída na pasta de saída (`mycontainer\\outputfolder`) no armazenamento de blobs.

Solte um ou mais arquivos nas pastas de entrada:

```
mycontainer -\> inputfolder
    2015-11-16-00
    2015-11-16-01
    2015-11-16-02
    2015-11-16-03
    2015-11-16-04
```

Por exemplo, solte um arquivo (file.txt) com o seguinte conteúdo em cada uma das pastas:

```
test custom activity Microsoft test custom activity Microsoft
```

Casa pasta de entrada corresponde a uma fatia no data factory, mesmo que a pasta tenha dois ou mais arquivos. Quando cada fatia é processada pelo pipeline, a atividade personalizada itera em todos os blobs na pasta de entrada dessa fatia.

Você vê cinco arquivos de saída com o mesmo conteúdo. Por exemplo, o arquivo de saída do processamento do arquivo na pasta 2015-11-16-00 tem o seguinte conteúdo:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Se você soltar vários arquivos (file.txt, file2.txt e file3.txt) com o mesmo conteúdo na pasta de entrada, o conteúdo a seguir será exibido no arquivo de saída. Cada pasta (2015-11-16-00, etc.) corresponde a uma fatia neste exemplo, mesmo que a pasta tenha vários arquivos de entrada.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

O arquivo de saída tem três linhas agora, uma para cada arquivo de entrada (blob) na pasta associada à fatia (2015-11-16-00).

Uma tarefa é criada para cada execução de atividade. Neste exemplo, há apenas uma atividade no pipeline. Quando uma fatia é processada pelo pipeline, a atividade personalizada é executada no Lote para processar a fatia. Como há cinco fatias (cada fatia pode ter vários blobs ou arquivos), cinco tarefas são criadas no Lote. Quando uma tarefa é executada no Lote, é a atividade personalizada que está sendo executada.

A apresentação passo a passo a seguir dá os detalhes adicionais.

#### <a name="step-1-create-the-data-factory"></a>Etapa 1: Criar o data factory
1. Depois de entrar no [portal do Azure](https://portal.azure.com/), execute as seguintes etapas:

   a. Selecione **NOVO** no menu à esquerda.

   b. Selecione **Dados + Análise** na folha **Novo**.

   c. Selecione **Data Factory** na folha **Análise de dados**.

1. Na folha **Novo data factory**, insira **CustomActivityFactory** como o nome. O nome do data factory deve ser globalmente exclusivo. Se você receber o erro “O nome do data factory CustomActivityFactory não está disponível”, altere o nome do data factory. Por exemplo, use yournameCustomActivityFactory e crie o data factory novamente.

1. Selecione **NOME DO GRUPO DE RECURSOS** e um grupo de recursos existente ou crie um.

1. Verifique se a assinatura e a região nas quais deseja que o data factory seja criado estão corretas.

1. Selecione **Criar** na folha **Novo data factory**.

1. O data factory é criado no painel do portal.

1. Depois de criar o data factory com êxito, você verá a página **Data factory**, que mostra seu conteúdo.

   ![Página Data factory](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Etapa 2: Criar serviços vinculados
Os serviços vinculados vinculam armazenamentos de dados ou serviços de computação a um data factory. Nesta etapa, você vincula sua conta de armazenamento e sua conta do Lote ao data factory.

#### <a name="create-an-azure-storage-linked-service"></a>Criar um serviço vinculado do Armazenamento do Azure
1. Selecione o bloco **Criar e implantar** na folha **Data factory** de **CustomActivityFactory**. O Editor do Data Factory é exibido.

1. Selecione **Novo armazenamento de dados** na barra de comandos e escolha **Armazenamento do Azure.** O script JSON usado para criar um serviço vinculado do Armazenamento no editor é exibido.

   ![Novo repositório de dados](./media/data-factory-data-processing-using-batch/image7.png)

1. Substitua o **nome da conta** pelo nome da sua conta de armazenamento. Substitua **chave de conta** pela chave de acesso da conta de armazenamento. Para saber como obter sua chave de acesso de armazenamento, consulte [Exibir, copiar e regenerar chaves de acesso de armazenamento](../../storage/common/storage-account-manage.md#access-keys).

1. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

   ![Implantar](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-an-azure-batch-linked-service"></a>Criar um serviço vinculado do Lote do Azure
Nesta etapa, você cria um serviço vinculado para sua conta do Lote que é usado para executar a atividade personalizada do data factory.

1. Selecione **Novo computador** na barra de comandos e escolha **Lote do Azure.** O script JSON usado para criar um serviço vinculado do Lote no editor é exibido.

1. No script JSON:

   a. Substitua **nome da conta** pelo nome de sua conta do Lote.

   b. Substitua **chave de acesso** pela chave de acesso da conta do Lote.

   c. Insira a ID do pool para a propriedade **poolName**. Para essa propriedade, especifique o nome ou a ID do pool.

   d. Digite o URI do lote para a propriedade JSON **batchUri** .

      > [!IMPORTANT]
      > A URL da folha **Conta do Lote** está no seguinte formato: \<accountname\>.\<region\>.batch.azure.com. Para a propriedade **batchUri** no script JSON, você precisa remover a88"accountname."** da URL. Um exemplo é `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![Folha Conta do Lote](./media/data-factory-data-processing-using-batch/image9.png)

      Para a propriedade **poolName**, também especifique a ID do pool, em vez do nome do pool.

      > [!NOTE]
      > O serviço Data Factory não é compatível com uma opção sob demanda para o Lote como o faz para o HDInsight. Use apenas seu próprio pool do Lote em um data factory.
      >
      >
   
   e. Especifique **StorageLinkedService** for the **linkedServiceName** . Você criou esse serviço vinculado na etapa anterior. Esse armazenamento é usado como uma área de preparação para arquivos e logs.

1. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

#### <a name="step-3-create-datasets"></a>Etapa 3: Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados para representar a entrada e saída de dados.

#### <a name="create-the-input-dataset"></a>Como criar o conjunto de dados de entrada
1. No Editor do Data Factory, selecione o botão **Novo conjunto de dados** na barra de ferramentas. Selecione **Armazenamento de blobs do Azure** na lista suspensa.

1. Substitua o script JSON no painel direito pelo seguinte snippet de código JSON:

    ```json
    {
       "name": "InputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
               "format": {
                   "type": "TextFormat"
               },
               "partitionedBy": [
                   {
                       "name": "Year",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "yyyy"
                       }
                   },
                   {
                       "name": "Month",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "MM"
                       }
                   },
                   {
                       "name": "Day",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "dd"
                       }
                   },
                   {
                       "name": "Hour",
                       "value": {
                           "type": "DateTime",
                           "date": "SliceStart",
                           "format": "HH"
                       }
                   }
               ]
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

    Você cria um pipeline posteriormente neste passo a passo com a hora de início: 2015-11-16T00:00:00Z e a hora de término 2015-11-16T05:00:00Z. Ele é agendado para gerar dados de hora em hora; portanto, há cinco fatias de entrada/saída (entre **00**:00:00 -\> **05**:00:00).

    A **frequency** e o **interval** do conjunto de dados de entrada são definidos como **Hour** e **1**, o que significa que a fatia de entrada está disponível por hora.

    A hora de início de cada fatia é representada pela variável do sistema **SliceStart** no snippet de JSON anterior. Veja a seguir as horas de início de cada fatia.

    | **Fatia** | **Hora de início**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    O **folderPath** é calculado usando a parte de ano, mês, dia e hora da hora de início da fatia (**SliceStart**). É assim que uma pasta de entrada é mapeada para uma fatia.

    | **Fatia** | **Hora de início**          | **Pasta de entrada**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. Selecione **Implantar** na barra de ferramentas para criar e implantar a tabela **InputDataset**.

#### <a name="create-the-output-dataset"></a>Criar o conjunto de dados de saída
Nesta etapa, você cria outro conjunto de dados do tipo AzureBlob para representar os dados de saída.

1. No Editor do Data Factory, selecione o botão **Novo conjunto de dados** na barra de ferramentas. Selecione **Armazenamento de blobs do Azure** na lista suspensa.

1. Substitua o script JSON no painel direito pelo seguinte snippet de código JSON:

    ```json
    {
       "name": "OutputDataset",
       "properties": {
           "type": "AzureBlob",
           "linkedServiceName": "AzureStorageLinkedService",
           "typeProperties": {
               "fileName": "{slice}.txt",
               "folderPath": "mycontainer/outputfolder",
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

    Um blob/arquivo de saída é gerado para cada fatia de entrada. Aqui está como um arquivo de saída é chamado para cada fatia. Todos os arquivos de saída são gerados em uma pasta de saída, `mycontainer\\outputfolder`.

    | **Fatia** | **Hora de início**          | **Arquivo de saída**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Lembre-se de que todos os arquivos em uma pasta de entrada (por exemplo, 2015-11-16-00) fazem parte de uma fatia com a hora de início 2015-11-16-00. Quando essa fatia é processada, a atividade personalizada examina cada arquivo e produz uma linha no arquivo de saída com o número de ocorrências do termo de pesquisa “Microsoft”. Se houver três arquivos na pasta 2015-11-16-00, haverá três linhas no arquivo de saída 2015-11-16-00.txt.

1. Selecione **Implantar** na barra de ferramentas para criar e implantar **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-a-custom-activity"></a>Etapa 4: Criar e executar o pipeline com uma atividade personalizada
Nesta etapa, você cria um pipeline com uma atividade, a atividade personalizada criada anteriormente.

> [!IMPORTANT]
> Se você ainda não carregou o **file.txt** para as pastas de entrada no contêiner de blobs, faça isso antes de criar o pipeline. A propriedade **isPaused** é definida como falso no JSON do pipeline, de modo que o pipeline seja executado imediatamente, já que a data de **início** está no passado.
>
>

1. No Editor do Data Factory, selecione **Novo pipeline** na barra de comandos. Se o comando não for exibido, selecione o símbolo de reticências para exibi-lo.

1. Substitua o script JSON no painel direito pelo seguinte snippet de código JSON:

    ```json
    {
       "name": "PipelineCustom",
       "properties": {
           "description": "Use custom activity",
           "activities": [
               {
                   "type": "DotNetActivity",
                   "typeProperties": {
                       "assemblyName": "MyDotNetActivity.dll",
                       "entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                       "packageLinkedService": "AzureStorageLinkedService",
                       "packageFile": "customactivitycontainer/MyDotNetActivity.zip"
                   },
                   "inputs": [
                       {
                           "name": "InputDataset"
                       }
                   ],
                   "outputs": [
                       {
                           "name": "OutputDataset"
                       }
                   ],
                   "policy": {
                       "timeout": "00:30:00",
                       "concurrency": 5,
                       "retry": 3
                   },
                   "scheduler": {
                       "frequency": "Hour",
                       "interval": 1
                   },
                   "name": "MyDotNetActivity",
                   "linkedServiceName": "AzureBatchLinkedService"
               }
           ],
           "start": "2015-11-16T00:00:00Z",
           "end": "2015-11-16T05:00:00Z",
           "isPaused": false
      }
    }
    ```
   Observe os seguintes pontos:

   * Há apenas uma atividade no pipeline e ela é do tipo **DotNetActivity**.
   * **AssemblyName** é definido com o nome da DLL **MyDotNetActivity.dll**.
   * **EntryPoint** é definido como **MyDotNetActivityNS.MyDotNetActivity**. Ele é basicamente \<namespace\>.\<classname\> no código.
   * **PackageLinkedService** é definido como **StorageLinkedService**, que aponta para o armazenamento de blobs que contém o arquivo zip da atividade personalizada. Se você usar contas de armazenamento diferentes para arquivos de entrada/saída e o arquivo zip da atividade personalizada, precisará criar outro serviço vinculado do Armazenamento. Este artigo pressupõe que você use a mesma conta de armazenamento.
   * **PackageFile** é definido como **customactivitycontainer/MyDotNetActivity.zip**. Ele está no formato \<containerforthezip\>/\<nameofthezip.zip\>.
   * A atividade personalizada usa **InputDataset** como entrada e **OutputDataset** como saída.
   * A propriedade **linkedServiceName** da atividade personalizada aponta para **AzureBatchLinkedService**, que informa o Data Factory de que a atividade personalizada precisa ser executada no Lote.
   * A configuração **simultaneidade** é importante. Se você usar o valor padrão, que é 1, mesmo que tenha dois ou mais nós de computação no pool do Lote, as fatias serão processadas uma após a outra. Portanto, você não está aproveitando a capacidade de processamento paralelo do Lote. Se você definir **concurrency** com um valor mais alto, digamos 2, isso significa que duas fatias (corresponde a duas tarefas no Lote) podem ser processadas ao mesmo tempo. Nesse caso, ambas as VMs do pool do Lote são utilizadas. Defina a propriedade de simultaneidade adequadamente.
   * Apenas uma tarefa (fatia) é executada em uma VM a qualquer momento por padrão. Por padrão, a configuração **Máximo de tarefas por VM** é definida como 1 para um pool do Lote. Como parte dos pré-requisitos, você criou um pool com essa propriedade definida como 2. Portanto, duas fatias do data factory podem ser executadas em uma VM ao mesmo tempo.
     - A propriedade **isPaused** está definida como falso por padrão. O pipeline é executado imediatamente neste exemplo porque a fatias começam no passado. Defina essa propriedade como **true** para pausar o pipeline e defina-a novamente como **false** para reiniciá-lo.
     -   As horas de **início** e de **término** são separadas por cinco horas. As fatias são produzidas por hora; portanto, cinco fatias são produzidas pelo pipeline.

1. Clique em **Implantar** na barra de comandos para implantar o pipeline.

#### <a name="step-5-test-the-pipeline"></a>Etapa 5: Testar o pipeline
Nesta etapa, você testa o pipeline soltando arquivos nas pastas de entrada. Comece testando o pipeline com um arquivo para cada pasta de entrada.

1. Na folha **Data factory** no portal do Azure, selecione **Diagrama**.

   ![Diagrama](./media/data-factory-data-processing-using-batch/image10.png)

1. Na exibição **Diagrama**, clique duas vezes no conjunto de dados de entrada **InputDataset**.

   ![InputDataset](./media/data-factory-data-processing-using-batch/image11.png)

1. A folha **InputDataset** é exibida com todas as cinco fatias prontas. Observe a **HORA DE INÍCIO DA FATIA** e a **HORA DE TÉRMINO DA FATIA** de cada fatia.

   ![Horas de início e término da fatia de entrada](./media/data-factory-data-processing-using-batch/image12.png)

1. Na exibição **Diagrama**, selecione **OutputDataset**.

1. As cinco fatias de saída são exibidas no estado **Pronto** se foram produzidas.

   ![Horas de início e término da fatia de saída](./media/data-factory-data-processing-using-batch/image13.png)

1. Use o portal para exibir as tarefas associadas às fatias e veja em qual VM cada fatia foi executada. Para obter mais informações, consulte a seção [Integração entre o Data Factory e o Lote](#data-factory-and-batch-integration).

1. Os arquivos de saída são exibidos em `mycontainer` de `outputfolder` em seu armazenamento de blobs.

   ![Arquivos de saída no armazenamento](./media/data-factory-data-processing-using-batch/image15.png)

   Cinco arquivos de saída são listados, um para cada fatia de entrada. Cada um dos arquivos de saída tem um conteúdo semelhante à seguinte saída:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   O diagrama a seguir ilustra como as fatias do data factory são mapeadas para tarefas no Lote. Neste exemplo, uma fatia tem apenas uma execução.

   ![Diagrama de mapeamento de fatias](./media/data-factory-data-processing-using-batch/image16.png)

1. Agora, tente com vários arquivos em uma pasta. Crie os arquivos **file2.txt**, **file3.txt**, **file4.txt** e **file5.txt** com o mesmo conteúdo do file.txt na pasta **2015-11-06-01**.

1. Na pasta de saída, exclua o arquivo de saída **2015-11-16-01.txt**.

1. Na folha **OutputDataset**, clique com o botão direito do mouse na fatia com a **HORA DE INÍCIO DA FATIA** definida como **16/11/2015 1h00min00s**. Selecione **Executar** para executar novamente/reprocessar a fatia. Agora, a fatia tem cinco arquivos em vez de um.

    ![Executar](./media/data-factory-data-processing-using-batch/image17.png)

1. Depois que a fatia for executada e seu status for **Pronto**, verifique se no conteúdo do arquivo de saída existe esta fatia (**2015-11-16-01.txt**). O arquivo de saída é exibido em `mycontainer` de `outputfolder` em seu armazenamento de blobs. Deve haver uma linha para cada arquivo da fatia.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Se você não excluiu o arquivo de saída 2015-11-16-01.txt antes de tentar com cinco arquivos de entrada, serão exibidas uma linha da execução da fatia anterior e cinco linhas da execução da fatia atual. Por padrão, o conteúdo é acrescentado ao arquivo de saída, caso ele já exista.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integração de Data Factory e Lote
O serviço Data Factory cria um trabalho no Lote com o nome `adf-poolname:job-xxx`.

![Trabalhos em lotes](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Uma tarefa é criada no trabalho para cada execução de atividade de uma fatia. Se 10 fatias estão prontas para serem processadas, 10 tarefas são criadas no trabalho. Pode haver mais de uma fatia em execução em paralelo, se você tiver vários nós de computação no pool. Se o número máximo de tarefas por nó de computação for definido como maior que um, mais de uma fatia poderá ser executada no mesmo nó de computação.

Neste exemplo, há cinco fatias e, portanto, há cinco tarefas no Lote. Com **concurrency** definido como **5** no JSON do pipeline no data factory e **Máximo de tarefas por VM** definido como **2** no pool do Lote com **2** VMs, as tarefas são executadas com rapidez. (Verifique as horas de início e término das tarefas.)

Use o portal para exibir o trabalho em lotes e suas tarefas associadas às fatias e veja em qual VM cada fatia foi executada.

![Tarefas de trabalho em lotes](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Depurar o pipeline
A depuração consiste em algumas técnicas básicas.

1. Se a fatia de entrada não está definida como **Pronto**, confirme se a estrutura de pastas de entrada está correta e se file.txt existe nas pastas de entrada.

   ![Estrutura de pastas de entrada](./media/data-factory-data-processing-using-batch/image3.png)

1. No método **Execute** da atividade personalizada, use o objeto **IActivityLogger** para registrar informações que o ajudam a solucionar problemas. As mensagens registradas aparecerão no arquivo user\_0.log.

   Na folha **OutputDataset**, selecione a fatia para ver a folha **Fatia de dados** da fatia. Em **Execuções de atividade**, uma execução de atividade é exibida para a fatia. Se você selecionar **Executar** na barra de comandos, poderá iniciar outra execução de atividade para a mesma fatia.

   Quando você seleciona a execução de atividade, a folha **Detalhes de execução de atividade** é exibida com uma lista de arquivos de log. Veja as mensagens registradas no arquivo user\_0.log. Quando ocorrer um erro, você verá três execuções de atividade porque a contagem de repetições é definida como 3 no JSON do pipeline/atividade. Quando você seleciona a execução de atividade, os arquivos de log são exibidos, para que você possa examiná-los para solucionar o erro.

   ![Folhas OutputDataset e Fatia de dados](./media/data-factory-data-processing-using-batch/image18.png)

   Na lista de arquivos de log, selecione **user-0.log**. No painel direito, são exibidos os resultados do uso do método **IActivityLogger.Write**.

   ![Folha Detalhes da execução de atividade](./media/data-factory-data-processing-using-batch/image19.png)

   Verifique se há mensagens de erro e exceções do sistema em system-0.log.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
1. Inclua o arquivo **PDB** no arquivo zip para que os detalhes do erro tenham informações como uma pilha de chamadas quando ocorrer um erro.

1. Todos os arquivos do arquivo zip para a atividade personalizada precisam estar no nível superior sem subpastas.

   ![Lista de arquivos zip da atividade personalizada](./media/data-factory-data-processing-using-batch/image20.png)

1. Verifique se **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) e **packageLinkedService** (devem apontar para o armazenamento de blobs que contém o arquivo zip) estão definidos com os valores corretos.

1. Se você corrigir um erro e quiser reprocessar a fatia, clique com o botão direito do mouse na fatia na folha **OutputDataset** e selecione **Executar**.

   ![Opção Executar da folha OutputDataset](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Um contêiner chamado `adfjobs` está localizado no armazenamento de blobs. Esse contêiner não é excluído automaticamente, mas você pode excluí-lo com segurança depois de concluir o teste da solução. Da mesma forma, a solução de data factory cria um trabalho em lote chamado `adf-\<pool ID/name\>:job-0000000001`. Você pode excluir esse trabalho depois de testar a solução, se desejar.
   >
   >
1. A atividade personalizada não usa o arquivo **app.config** do pacote. Portanto, se o código lê cadeias de conexão do arquivo de configuração, ele não funciona em tempo de execução. A melhor prática para quando você usar o Lote é manter os segredos no Azure Key Vault. Em seguida, use uma entidade de serviço baseada em certificado para proteger o cofre de chaves e distribuir o certificado para o pool do Lote. A atividade personalizada do .NET pode acessar segredos no cofre de chaves em tempo de execução. Essa solução genérica pode ser dimensionada para qualquer tipo de segredo, não apenas para uma cadeia de conexão.

    Há uma solução alternativa mais fácil, mas não é uma melhor prática. Crie um serviço vinculado do banco de dados SQL com configurações da cadeia de conexão. Em seguida, crie um conjunto de dados que usa o serviço vinculado e encadeie o conjunto de dados como um conjunto de dados de entrada fictício com a atividade personalizada do .NET. Você pode então acessar a cadeia de conexão do serviço vinculado no código de atividade personalizada. Isso deve funcionar corretamente em tempo de execução.  

#### <a name="extend-the-sample"></a>Estender o exemplo
Estenda esta amostra para saber mais sobre os recursos do Data Factory e do Lote. Por exemplo, para processar fatias em um intervalo de tempo diferente, realize as seguintes etapas:

1. Adicione as seguintes subpastas em `inputfolder`: 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08 e 2015-11-16-09. Coloque os arquivos de entrada nessas pastas. Altere a hora de término do pipeline de `2015-11-16T05:00:00Z` para `2015-11-16T10:00:00Z`. Na exibição **Diagrama**, clique duas vezes em **InputDataset** e confirme se as fatias de entrada estão prontas. Clique duas vezes em **OutputDataset** para ver o estado das fatias de saída. Se estiverem no estado **Pronto**, verifique se os arquivos de saída estão na pasta de saída.

1. Aumente ou diminua a configuração **concurrency** para entender como ela afeta o desempenho de sua solução, especialmente o processamento que ocorre no Lote. Para obter mais informações sobre a configuração de **simultaneidade**, confira a “Etapa 4: Criar e executar o pipeline com uma atividade personalizada.”

1. Crie um pool com **Máximo de tarefas por VM**maior/menor. Para usar o novo pool criado, atualize o serviço vinculado do Lote na solução de data factory. Para obter mais informações sobre a configuração das **Tarefas máximas por VM**, confira a "Etapa 4: Criar e executar o pipeline com uma atividade personalizada.”

1. Crie um pool do Lote com o recurso **autoscale**. O dimensionamento automático de nós de computação em um pool do Lote é o ajuste dinâmico da potência de processamento usada pelo aplicativo. 

    A fórmula de exemplo aqui obtém o comportamento a seguir. Quando o pool é criado inicialmente, ele começa com uma VM. A métrica $PendingTasks define o número de tarefas nos estados em execução e ativo (colocadas em fila). A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e define TargetDedicated adequadamente. Isso garante que TargetDedicated nunca ultrapasse 25 VMs. Conforme novas tarefas são enviadas, o pool aumenta automaticamente. Conforme as tarefas são concluídas, as VMs são liberadas uma a uma e o dimensionamento automático reduz essas VMs. Ajuste startingNumberOfVMs e maxNumberofVMs conforme suas necessidades.
 
    Fórmula de dimensionamento automático:

    ``` 
    startingNumberOfVMs = 1;
    maxNumberofVMs = 25;
    pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
    pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
    $TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
    ```

   Para obter mais informações, consulte [Dimensionar automaticamente nós de computação em um pool do Lote](../../batch/batch-automatic-scaling.md).

   Se o pool usar o [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx) padrão, o serviço Lote poderá levar de 15 a 30 minutos para preparar a VM antes de executar a atividade personalizada. Se o pool usar um autoScaleEvaluationInterval diferente, o serviço Lote poderá levar autoScaleEvaluationInterval, mais 10 minutos.

1. Na solução de exemplo, o método **Execute** invoca o método **Calculate**, que processa uma fatia de dados de entrada para produzir uma fatia de dados de saída. Escreva seu próprio método para processar dados de entrada e substitua a chamada do método **Calculate** no método **Execute** por uma chamada ao seu método.

### <a name="next-steps-consume-the-data"></a>Próximas etapas: Consumir os dados
Depois de processar dados, consuma-os com ferramentas online como o Power BI. Aqui estão links para ajudá-lo a entender o Power BI e como usá-lo no Azure:

* [Explorar um conjunto de dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Atualizar dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure e Power BI: visão geral básica](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referências
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Introdução ao serviço Data Factory](data-factory-introduction.md)
  * [Introdução ao Data Factory](data-factory-build-your-first-pipeline.md)
  * [Usar atividades personalizadas em um pipeline do Data Factory](data-factory-use-custom-activities.md)
* [Lote do Azure](https://azure.microsoft.com/documentation/services/batch/)

  * [Conceitos básicos do Lote](../../batch/batch-technical-overview.md)
  * [Visão geral dos recursos do Lote](../../batch/batch-api-basics.md)
  * [Criar e gerenciar uma conta do Lote no portal do Azure](../../batch/batch-account-create-portal.md)
  * [Introdução à biblioteca de clientes do Lote para .NET](../../batch/quick-run-dotnet.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: https://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
