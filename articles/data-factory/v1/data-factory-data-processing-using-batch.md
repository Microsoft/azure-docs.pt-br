---
title: Processar conjuntos de dados em larga escala usando o Data Factory e o Lote | Microsoft Docs
description: Descreve como processar grandes volumes de dados em um pipeline do Data Factory do Azure usando o recurso de processamento paralelo do Lote do Azure.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 688b964b-51d0-4faa-91a7-26c7e3150868
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 75213a4d0297c96ec32200158d8b60db4b8b2da4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="process-large-scale-datasets-using-data-factory-and-batch"></a>Processar conjuntos de dados em larga escala usando o Data Factory e o Lote
Este artigo descreve uma arquitetura de um exemplo de solução que move e processa os conjuntos de dados em larga escala de maneira automática e agendada. Ele também fornece um passo a passo completo para implementar a solução usando Azure Data Factory e o Lote do Azure.

Este artigo é maior que nossos artigos comuns porque contém um passo a passo de um exemplo de solução inteira. Se não tiver experiência com o Lote e o Data Factory, você pode aprender mais sobre esses serviços e como eles funcionam juntos. Se já tem algum conhecimento sobre os serviços e estiver projetando/arquitetando uma solução, você pode se concentrar apenas na [seção de arquitetura](#architecture-of-sample-solution) do artigo e, se estiver desenvolvendo um protótipo ou uma solução, também pode querer testar nossas instruções no [passo a passo](#implementation-of-sample-solution). Seus comentários sobre esse conteúdo e como usá-lo são bem-vindos.

Em primeiro lugar, vamos observar como os serviços Data Factory e Lote podem ajudar com o processamento de conjuntos de dados grandes na nuvem.     

## <a name="why-azure-batch"></a>Por que usar o Lote do Azure?
O Lote do Azure o habilita a executar aplicativos paralelos em larga escala e de HPC (computação de alto desempenho) na nuvem. É um serviço de plataforma que agenda o trabalho de computação intensiva para executar em uma coleção gerenciada de máquinas virtuais e que pode dimensionar automaticamente os recursos de computação para atender às necessidades dos trabalhos.

Com o serviço em Lotes, você define os recursos de computação do Azure para executar os aplicativos em paralelo e em escala. Você pode executar trabalhos sob demanda ou agendados e não precisa criar, configurar e gerenciar manualmente um cluster HPC, máquinas virtuais individuais, redes virtuais ou uma infraestrutura complexa de agendamento de trabalhos e tarefas.

Se não estiver familiarizado com o Lote do Azure, veja os artigos a seguir, pois eles ajudam a entender a arquitetura/implementação da solução descrita neste artigo.   

* [Noções básicas de Lote do Azure](../../batch/batch-technical-overview.md)
* [Visão geral do recurso de Lote](../../batch/batch-api-basics.md)

(opcional) Para saber mais sobre o Lote do Azure, confira o [Caminho de aprendizagem do Lote do Azure](https://azure.microsoft.com/documentation/learning-paths/batch/).

## <a name="why-azure-data-factory"></a>Por que usar o Azure Data Factory?
O Data Factory é um serviço de integração de dados baseado em nuvem que automatiza a movimentação e a transformação dos dados. Com o serviço Data Factory, você pode criar pipelines de dados gerenciados que movem dados de repositórios de dados locais e na nuvem para um repositório de dados centralizado (por exemplo: Armazenamento de Blobs do Azure), bem como processar/transformar dados usando serviços como o Azure HDInsight e o Azure Machine Learning. Também é possível agendar pipelines de dados para execução de maneira agendada (de hora em hora, diariamente, semanalmente etc.), além de monitorá-los e gerenciá-los rapidamente a fim de identificar problemas e tomar providências.

Se não estiver familiarizado com o Azure Data Factory, veja os artigos a seguir, pois eles ajudam a entender a arquitetura/implementação da solução descrita neste artigo.  

* [Introdução ao Azure Data Factory](data-factory-introduction.md)
* [Criar seu primeiro pipeline de dados](data-factory-build-your-first-pipeline.md)   

(opcional) Para saber mais sobre o Azure Data Factory, confira o [Caminho de aprendizagem do Azure Data Factory](https://azure.microsoft.com/documentation/learning-paths/data-factory/).

## <a name="data-factory-and-batch-together"></a>Data Factory e Lote juntos
O Data Factory inclui atividades internas como Atividade de Cópia para copiar/mover dados de um repositório de dados de origem para um repositório de dados de destino, bem como a Atividade Hive para processar dados usando clusters Hadoop (HDInsight) no Azure. Confira [Atividades de transformação de dados](data-factory-data-transformation-activities.md) para obter uma lista de atividades de transformação permitidas.

Ele também permite criar atividades do .NET personalizadas para mover ou processar dados com sua própria lógica e executar essas atividades em um cluster do Azure HDInsight ou em um pool de VMs do Azure Batch. Ao usar o Lote do Azure, é possível configurar o pool para dimensionar automaticamente (adicionar ou remover VMs com base na carga de trabalho) com base em uma fórmula que você fornece.     

## <a name="architecture-of-sample-solution"></a>Arquitetura do exemplo de solução
Embora a arquitetura descrita neste artigo seja para uma solução simples, ela é relevante para cenários complexos, como modelagem de risco por serviços financeiros, processamento e renderização de imagem, e análise genômica.

O diagrama ilustra 1) como o Data Factory orquestra movimentação e processamento de dados e 2) como o Lote do Azure processa os dados de forma paralela. Baixe e imprima o diagrama para consulta fácil (27,94 x 43,18 cm ou tamanho A3): [HPC and data orchestration using Azure Batch and Data Factory](http://go.microsoft.com/fwlink/?LinkId=717686) (HPC e orquestração de dados usando o Azure Batch e o Data Factory).

[![Diagrama de processamento de dados em larga escala](./media/data-factory-data-processing-using-batch/image1.png)](http://go.microsoft.com/fwlink/?LinkId=717686)

A lista a seguir fornece as etapas básicas do processo. A solução inclui código e explicações para criar a solução de ponta a ponta.

1. **Configure o Lote do Azure com um pool de nós de computação (VMs)**. Você pode especificar o número de nós e o tamanho de cada nó.
2. **Crie uma instância do Azure Data Factory** que está configurada com entidades que representam o armazenamento de blobs do Azure, serviço de computação do Lote do Azure, dados de entrada/saída e um fluxo de trabalho/pipeline com atividades que movem e transformam dados.
3. **Crie uma atividade .NET personalizada no pipeline do Data Factory**. A atividade é seu código de usuário que é executado no pool do Azure Batch.
4. **Armazene grandes quantidades de dados de entrada como blobs no armazenamento do Azure**. Os dados são divididos em fatias lógicas (geralmente, por hora).
5. **O Data Factory copia os dados que são processados em paralelo** para a localização secundária.
6. **O Data Factory executa a atividade personalizada usando o pool alocado pelo Lote**. O Data Factory pode executar atividades simultaneamente. Cada atividade processa uma fatia de dados. Os resultados são armazenados no armazenamento do Azure.
7. **O Data Factory move os resultados finais para um terceiro local**para distribuição por meio de um aplicativo ou para processamento adicional por outras ferramentas.

## <a name="implementation-of-sample-solution"></a>Implementação da solução de exemplo
A solução de exemplo é intencionalmente simples e serve para mostrar como usar o Data Factory e o Lote juntos no processamento de conjuntos de dados. A solução conta apenas com o número de ocorrências de um termo de pesquisa ("Microsoft") em arquivos de entrada organizados em uma série temporal. Ele produz a contagem para arquivos de saída.

**Tempo**: se você estiver familiarizado com as noções básicas do Azure, Data Factory e Batch e tiver atendido aos pré-requisitos listados abaixo, estimamos que essa solução levará de 1 a 2 horas para ser concluída.

### <a name="prerequisites"></a>Pré-requisitos
#### <a name="azure-subscription"></a>Assinatura do Azure
Se você não tiver uma assinatura do Azure, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Veja [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

#### <a name="azure-storage-account"></a>Conta de Armazenamento do Azure
Você usará uma conta de armazenamento do Azure para armazenar os dados neste tutorial. Se você não tiver uma conta de armazenamento do Azure, consulte o artigo [Criar uma conta de armazenamento](../../storage/common/storage-create-storage-account.md#create-a-storage-account). A solução de exemplo usa o armazenamento de blobs.

#### <a name="azure-batch-account"></a>Conta do Lote do Azure
Crie uma conta do Azure Batch usando o [Portal do Azure](http://manage.windowsazure.com/). Consulte [Criar e gerenciar uma conta do Lote do Azure](../../batch/batch-account-create-portal.md). Anote a chave e o nome da conta do Lote do Azure. Você também pode usar o cmdlet [New-AzureRmBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx) para criar uma conta do Lote do Azure. Consulte [Introdução aos cmdlets do PowerShell do Lote do Azure](../../batch/batch-powershell-cmdlets-get-started.md) para obter instruções detalhadas sobre como usar esse cmdlet.

A solução de exemplo usa Azure Batch (indiretamente por meio de um pipeline do Azure Data Factory) para processar dados de forma paralela em um pool de nós de computação (uma coleção gerenciada de máquinas virtuais).

#### <a name="azure-batch-pool-of-virtual-machines-vms"></a>Pool de VMs (máquinas virtuais) do Lote do Azure
Crie um **pool de Lote do Azure** com pelo menos dois nós de computação.

1. No [Portal do Azure](https://portal.azure.com), clique em **Procurar** no menu à esquerda e clique em **Contas do Batch**.
2. Selecione sua a conta do Lote do Azure para abrir a folha **Conta do Batch** .
3. Clique no bloco **Pools** .
4. Na folha **Pools** , clique no botão Adicionar na barra de ferramentas para adicionar um pool.
   1. Insira uma ID para o pool (**ID do Pool**). Observe a **ID do pool**; você precisa dela ao criar a solução Data Factory.
   2. Especifique **Windows Server 2012 R2** para a configuração da família do sistema operacional.
   3. Selecione um **camada de preços de nó**.
   4. Digite **2** como valor para a configuração **Destino Dedicado**.
   5. Digite **2** como valor para a configuração **Máximo de tarefas por nó**.
   6. Clique em **OK** para criar o pool.

#### <a name="azure-storage-explorer"></a>Gerenciador de Armazenamento do Azure
[Azure Storage Explorer 6 (ferramenta)](https://azurestorageexplorer.codeplex.com/) ou [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (da ClumsyLeaf Software). Você usa essas ferramentas para inspecionar e alterar os dados em seus projetos do Azure Storage, incluindo os logs dos aplicativos hospedados na nuvem.

1. Crie um contêiner chamado **mycontainer** com acesso privado (sem acesso anônimo)
2. Se você estiver usando **CloudXplorer**, crie pastas e subpastas com a seguinte estrutura:

   ![](./media/data-factory-data-processing-using-batch/image3.png)

   `Inputfolder` e `outputfolder` são pastas de nível superior no `mycontainer`. A `inputfolder` contém subpastas com carimbos de data/hora (AAAA-MM-DD-HH).

   Se você estiver usando o **Gerenciador de Armazenamento do Azure**, na próxima etapa, precisará carregar arquivos com os nomes `inputfolder/2015-11-16-00/file.txt`, `inputfolder/2015-11-16-01/file.txt` e assim por diante. Essa etapa cria as pastas automaticamente.
3. Crie um arquivo de texto **file.txt** no computador com o conteúdo com a palavra-chave **Microsoft**. Por exemplo: "testar a atividade personalizada Microsoft testar atividade personalizada Microsoft".
4. Carregue o arquivo para as seguintes pastas de entrada no armazenamento de blobs do Azure.

   ![](./media/data-factory-data-processing-using-batch/image4.png)

   Se você estiver usando o **Azure Storage Explorer**, carregue o arquivo **file.txt** para **mycontainer**. Clique em **Copiar** na barra de ferramentas para criar uma cópia do blob. Na caixa de diálogo **Copiar Blob**, altere o **nome do blob de destino** para `inputfolder/2015-11-16-00/file.txt`. Repita essa etapa para criar `inputfolder/2015-11-16-01/file.txt`, `inputfolder/2015-11-16-02/file.txt`, `inputfolder/2015-11-16-03/file.txt`, `inputfolder/2015-11-16-04/file.txt` e assim por diante. Essa ação cria as pastas automaticamente.
5. Crie outro contêiner chamado `customactivitycontainer`. Você carrega o arquivo zip da atividade personalizada para esse contêiner.

#### <a name="visual-studio"></a>Visual Studio
Microsoft Visual Studio 2012 ou posterior para criar a atividade personalizada do Lote a ser usada na solução de Data Factory.

### <a name="high-level-steps-to-create-the-solution"></a>Etapas de alto nível para criar a solução
1. Crie uma atividade personalizada que contenha a lógica de processamento de dados.
2. Crie um Azure Data Factory que usa a atividade personalizada:

### <a name="create-the-custom-activity"></a>Criar a atividade personalizada
A atividade personalizada do Data Factory é o cerne desta solução de exemplo. A solução de exemplo usa o Lote do Azure para executar a atividade personalizada. Consulte [Usar atividades personalizadas em um pipeline do Azure Data Factory](data-factory-use-custom-activities.md) para obter as informações básicas para desenvolver atividades personalizadas e usá-las em pipelines do Azure Data Factory.

Para criar uma atividade personalizada do .NET que possa ser usada em um pipeline do Azure Data Factory, você precisa criar um projeto da **Biblioteca de Classes do .NET** com uma classe que implemente a interface **IDotNetActivity**. Essa interface tem apenas um método: **Execute**. Veja a assinatura desse método:

```csharp
public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
```

O método tem alguns componentes principais que você precisa entender.

* O método utiliza quatro parâmetros:

  1. **linkedServices**. Uma lista enumerável de serviços vinculados que vinculam fontes de dados de entrada/saída (por exemplo: armazenamento de blobs do Azure) no data factory. Neste exemplo, há apenas um serviço vinculado do tipo armazenamento do Azure usado para entrada e saída.
  2. **conjuntos de dados**. É uma lista enumerável de conjuntos de dados. Você pode usar esse parâmetro para obter os locais e esquemas definidos por conjuntos de dados de entrada e saída.
  3. **atividade**. Esse parâmetro representa a entidade de computação atual, nesse caso, um serviço de Lote do Azure.
  4. **logger**. O logger permite que você escreva comentários de depuração que são exibidos como o log do “usuário" no pipeline.
* O método retorna um dicionário que pode ser usado para unir atividades personalizadas no futuro. Este recurso ainda não está implementado, portanto, retorne um dicionário vazio do método.

#### <a name="procedure-create-the-custom-activity"></a>Procedimento: criar a atividade personalizada
1. Crie um projeto de Biblioteca de Classes .NET no Visual Studio.

   1. Inicie o **Visual Studio 2012**/**2013/2015**.
   2. Clique em **Arquivo**, aponte para **Novo** e clique em **Projeto**.
   3. Expanda **Modelos** e selecione **Visual C\#**. Neste passo a passo, você pode usar C\#, mas pode usar qualquer linguagem .NET para desenvolver a atividade personalizada.
   4. Selecione **Biblioteca de Classes** na lista de tipos de projeto à direita.
   5. Insira **MyDotNetActivity** for the **Nome**.
   6. Selecione **C:\\ADF** para a **Localização**. Crie a pasta **ADF** se ela não existir.
   7. Clique em **OK** para criar o projeto.
2. Clique em **Ferramentas**, aponte para **Gerenciador de Pacotes NuGet** e clique em **Console do Gerenciador de Pacotes**.
3. No **Console do Gerenciador de Pacotes**, execute o comando a seguir para importar **Microsoft.Azure.Management.DataFactories**.

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactories
    ```
4. Importe o pacote NuGet do **Armazenamento do Azure** para o projeto. Você precisa desse pacote porque usa a API de Armazenamento de Blobs neste exemplo.

    ```powershell
    Install-Package Azure.Storage
    ```
5. Adicione as diretivas **using** a seguir ao arquivo de origem no projeto.

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
6. Altere o nome do **namespace** para **MyDotNetActivityNS**.

    ```csharp
    namespace MyDotNetActivityNS
    ```
7. Altere o nome da classe para **MyDotNetActivity** e derive-a da interface **IDotNetActivity**, conforme mostrado abaixo.

    ```csharp
    public class MyDotNetActivity : IDotNetActivity
    ```
8. Implemente (Adicione) o método **Execute** da interface **IDotNetActivity** à classe **MyDotNetActivity** e copie o seguinte código de exemplo para o método. Consulte a seção [Método Execute](#execute-method) para obter uma explicação para a lógica usada nesse método.

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
    
       // declare types for input and output data stores
       AzureStorageLinkedService inputLinkedService;
    
       Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
    
       foreach (LinkedService ls in linkedServices)
           logger.Write("linkedService.Name {0}", ls.Name);
    
       // using First method instead of Single since we are using the same
       // Azure Storage linked service for input and output.
       inputLinkedService = linkedServices.First(
           linkedService =>
           linkedService.Name ==
           inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
           as AzureStorageLinkedService;
    
       string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
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
           // with the data slice.
           //
           // definition of the method is shown in the next step.
           output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
    
       } while (continuationToken != null);
    
       // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
       Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    
       folderPath = GetFolderPath(outputDataset);
    
       logger.Write("Writing blob to the folder: {0}", folderPath);
    
       // create a storage object for the output blob.
       CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
       // write the name of the file.
       Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    
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
9. Adicione os seguintes métodos auxiliares à classe. Esses métodos são invocados pelo método **Execute** . Mais importante, o método **Calculate** isola o código que itera através de cada blob.

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
    O método **GetFolderPath** retorna o caminho para a pasta indicada pelo conjunto de dados e o método **GetFileName** retorna o nome do blob/arquivo para o qual o conjunto de dados aponta.

    ```csharp

    "name": "InputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "file.txt",
            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
    ```

    O método **Calculate** calcula o número de instâncias da palavra-chave **Microsoft** nos arquivos de entrada (blobs na pasta). O termo de pesquisa ("Microsoft") é embutido no código.

1. Compile o projeto. Clique em **Compilar** no menu e clique em **Compilar Solução**.
2. Inicie o **Windows Explorer** e navegue até a pasta **bin\\debug** ou **bin\\release** dependendo do tipo da compilação.
3. Crie um arquivo zip **MyDotNetActivity.zip** que contém todos os binários na pasta **\\bin\\Debug**. Inclua o arquivo MyDotNetActivity.**pdb** para obter detalhes adicionais, como número de linha no código fonte que causou o problema quando ocorrer uma falha.

   ![](./media/data-factory-data-processing-using-batch/image5.png)
4. Carregue **MyDotNetActivity.zip** como um blob no contêiner de blobs `customactivitycontainer`, no armazenamento de blobs do Azure utilizado pelo serviço vinculado **StorageLinkedService** no **ADFTutorialDataFactory**. Crie o contêiner de blobs `customactivitycontainer` se ele ainda não existir.

#### <a name="execute-method"></a>Método Execute
Esta seção fornece mais detalhes e observações sobre o código no método Execute.

1. Os membros para iteração pela coleção de entrada são encontrados no namespace [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx) . A iteração através da coleção de blobs requer o uso da classe **BlobContinuationToken** . Em essência, você deve usar um loop do-while com o token, como o mecanismo para saída do loop. Para obter mais informações, consulte o artigo sobre [Como usar o armazenamento de blobs do .NET](../../storage/blobs/storage-dotnet-how-to-use-blobs.md). Um loop básico é mostrado aqui:

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
   Consulte a documentação do método [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx) para obter detalhes.
2. O código para trabalhar com o conjunto de blobs logicamente fica dentro do loop do-while. No método **Execute**, o loop do-while passa a lista de blobs para um método chamado **Calculate**. O método retorna uma variável de cadeia de caracteres chamada **output** , que é o resultado da iteração nos blobs do segmento.

   Ele retorna o número de ocorrências do termo de pesquisa (**Microsoft**) no blob passado para o método **Calculate**.

    ```csharp
    output += string.Format("{0} occurrences of the search term \"{1}\" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
    ```
3. Quando o método **Calculate** tiver feito o trabalho, ele deverá ser gravado em um novo blob. Para cada conjunto de blobs processado, um novo blob pode ser gravado nos resultados. Para gravar um novo blob, primeiro localize o conjunto de dados de saída.

    ```csharp
    // Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
    Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
    ```
4. O código também chama um método auxiliar: **GetFolderPath** para recuperar o caminho da pasta (o nome do contêiner de armazenamento).

    ```csharp
    folderPath = GetFolderPath(outputDataset);
    ```
   O **GetFolderPath** converte o objeto DataSet em um AzureBlobDataSet, que tem uma propriedade chamada FolderPath.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FolderPath;
    ```
5. O código chama o método **GetFileName** para recuperar o nome do arquivo (nome do blob). O código é semelhante ao código acima para obter o caminho da pasta.

    ```csharp
    AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
    
    return blobDataset.FileName;
    ```
6. O nome do arquivo é gravado, ao criar um objeto URI. O construtor de URI usa a propriedade **BlobEndpoint** propriedade para retornar o nome do contêiner. O nome do arquivo e caminho da pasta são adicionados para construir o URI do blob de saída.  

    ```csharp
    // Write the name of the file.
    Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));
    ```
7. O nome do arquivo foi escrito e agora você pode gravar a cadeia de caracteres de saída do método **Calculate** em um novo blob:

    ```csharp
    // Create a blob and upload the output text.
    CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
    logger.Write("Writing {0} to the output blob", output);
    outputBlob.UploadText(output);
    ```

### <a name="create-the-data-factory"></a>Criar o data factory
Na seção [Criar atividade personalizada](#create-the-custom-activity) , você criou uma atividade personalizada e carregou o arquivo zip com binários e o arquivo PDB em um contêiner de blob do Azure. Nesta seção, você cria um Azure **data factory** com um **pipeline** que usa **atividade personalizada**.

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

Por exemplo, solte um arquivo (file.txt) com o seguinte conteúdo em cada uma das pastas.

```
test custom activity Microsoft test custom activity Microsoft
```

A pasta de entrada corresponde a uma fatia no Azure Data Factory, mesmo que a pasta tenha dois ou mais arquivos. Quando cada fatia é processada pelo pipeline, a atividade personalizada itera em todos os blobs na pasta de entrada dessa fatia.

Você vê cinco arquivos de saída com o mesmo conteúdo. Por exemplo, o arquivo de saída do processamento do arquivo na pasta 2015-11-16-00 tem o seguinte conteúdo:

```
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
```

Se você soltar vários arquivos (file.txt, file2.txt, file3.txt) com o mesmo conteúdo na pasta de entrada, verá o seguinte conteúdo no arquivo de saída. Cada pasta (2015-11-16-00, etc.) corresponde a uma fatia neste exemplo, mesmo que a pasta tenha vários arquivos de entrada.

```csharp
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.
```

O arquivo de saída tem três linhas agora, uma para cada arquivo de entrada (blob) na pasta associada à fatia (2015-11-16-00).

Uma tarefa é criada para cada execução de atividade. Neste exemplo, há apenas uma atividade no pipeline. Quando uma fatia é processada pelo pipeline, a atividade personalizada é executada no Lote do Azure para processar a fatia. Uma vez que há cinco intervalos (cada fatia pode ter vários blobs ou arquivos), há cinco tarefas criadas no Azure Batch. Quando uma tarefa é executada em lote, é, na verdade, a atividade personalizada que está sendo executada.

A apresentação passo a passo a seguir dá os detalhes adicionais.

#### <a name="step-1-create-the-data-factory"></a>Etapa 1: Criar o data factory
1. Depois de fazer logon no [Portal do Azure](https://portal.azure.com/), execute as seguintes etapas:

   1. Clique em **NOVO** no menu à esquerda.
   2. Clique em **Dados + Análise** na folha **Novo**.
   3. Clique em **Data Factory** na folha **Análise de dados**.
2. Na folha **Novo data factory**, insira **CustomActivityFactory** para o Nome. O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome de data factory “CustomActivityFactory” não está disponível**, altere o nome (por exemplo, **yournameCustomActivityFactory**) e tente criá-lo novamente.
3. Clique em **NOME DO GRUPO DE RECURSOS**para selecionar um grupo de recursos existente ou criar um.
4. Depois de selecionar o grupo de recursos, verifique se que você está usando a assinatura correta e a região na qual deseja que o data factory seja criado.
5. Clique em **Criar** na folha **Novo data factory**.
6. Você vê o data factory sendo criado no **Painel** do portal do Azure.
7. Após o data factory ter sido criado com êxito, você verá a página do data factory, que exibe seu conteúdo.

   ![](./media/data-factory-data-processing-using-batch/image6.png)

#### <a name="step-2-create-linked-services"></a>Etapa 2: Criar serviços vinculados
Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Nesta etapa, você vincula a conta do **Azure Storage** e a conta do **Azure Batch** ao data factory.

#### <a name="create-azure-storage-linked-service"></a>Criar o serviço vinculado do armazenamento do Azure
1. Clique no bloco **Criar e implantar** na folha **DATA FACTORY** para **CustomActivityFactory**. Você vê o Data Factory Editor.
2. Clique em **Novo armazenamento de dados** na barra de comandos e escolha **Azure Storage**. Você deve ver o script JSON para criar um serviço de armazenamento vinculado do Azure no editor.

   ![](./media/data-factory-data-processing-using-batch/image7.png)

3. Substitua o **nome da conta** pelo nome da conta do Armazenamento do Azure e a **chave de conta** pela chave de acesso da sua conta do Armazenamento do Azure. Para saber como obter sua chave de acesso de armazenamento, confira [Exibir, copiar e regenerar chaves de acesso de armazenamento](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

4. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

   ![](./media/data-factory-data-processing-using-batch/image8.png)

#### <a name="create-azure-batch-linked-service"></a>Crie o serviço vinculado do Lote do Azure
Nesta etapa, você cria um serviço vinculado para a sua conta do **Azure Batch** que é usado para executar a atividade personalizada do Data Factory.

1. Clique em **Novo computador** na barra de comandos e escolha **Azure Batch**. Você deve ver o script JSON para criar um serviço vinculado do Lote do Azure no editor.
2. No script JSON:

   1. Substitua **nome da conta** pelo nome da sua conta do Lote do Batch.
   2. Substitua **chave de acesso** pela chave de acesso da conta do Lote do Azure.
   3. Insira a ID do pool para a propriedade **poolName****.** Para essa propriedade, você pode especificar o nome do pool ou o ID do pool.
   4. Digite o URI do lote para a propriedade JSON **batchUri** .

      > [!IMPORTANT]
      > A **URL** da **folha da conta do Azure Batch** está no seguinte formato: \<accountname\>.\<region\>.batch.azure.com. Para a propriedade **batchUri** em JSON, você precisará **remover "accountname."** da URL. Exemplo: `"batchUri": "https://eastus.batch.azure.com"`.
      >
      >

      ![](./media/data-factory-data-processing-using-batch/image9.png)

      Para a propriedade **poolName** , você também pode especificar a ID do pool em vez do nome do pool.

      > [!NOTE]
      > O serviço de Data Factory não suporta uma opção sob demanda para o Azure Batch como o faz para o HDInsight. Você só pode usar seu próprio pool do Azure Batch em um Azure Data Factory.
      >
      >
   5. Especifique **StorageLinkedService** for the **linkedServiceName** . Você criou esse serviço vinculado na etapa anterior. Esse armazenamento é usado como uma área de preparação para arquivos e logs.
3. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

#### <a name="step-3-create-datasets"></a>Etapa 3: Criar conjuntos de dados
Nesta etapa, você cria conjuntos de dados para representar a entrada e saída de dados.

#### <a name="create-input-dataset"></a>Criar conjunto de dados de entrada
1. No **Editor** do Data Factory, clique no botão **Novo conjunto de dados** na barra de ferramentas e clique em **Armazenamento de Blobs do Azure** no menu suspenso.
2. Substitua o JSON no painel direito pelo trecho de código JSON a seguir:

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

    Você cria um pipeline posteriormente neste passo a passo com hora de início: 2015-11-16T00:00:00Z e final: 2015-11-16T05:00:00Z. Ele é agendado para gerar dados **de hora em hora**, então há cinco fatias de entrada/saída (**00**:00:00 –\> **05**:00:00).

    A **frequência** e o **intervalo** do conjunto de dados de entrada são definidos como **Hora** e **1**, o que significa que a fatia de entrada está disponível por hora.

    Aqui estão as horas de início de cada fatia, representado pela variável de sistema **SliceStart** no trecho de código JSON acima.

    | **Fatia** | **Hora de início**          |
    |-----------|-------------------------|
    | 1         | 2015-11-16T**00**:00:00 |
    | 2         | 2015-11-16T**01**:00:00 |
    | 3         | 2015-11-16T**02**:00:00 |
    | 4         | 2015-11-16T**03**:00:00 |
    | 5         | 2015-11-16T**04**:00:00 |

    O **folderPath** é calculado usando a parte de ano, mês, dia e hora da hora de início da fatia (**SliceStart**). Portanto, é assim que uma pasta de entrada é mapeada para uma fatia.

    | **Fatia** | **Hora de início**          | **Pasta de entrada**  |
    |-----------|-------------------------|-------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

1. Clique em **Implantar** na barra de ferramentas para criar e implantar a tabela **InputDataset**.

#### <a name="create-output-dataset"></a>Criar conjunto de dados de saída
Nesta etapa, você cria outro conjunto de dados do tipo AzureBlob para representar os dados de saída.

1. No **Editor** do Data Factory, clique no botão **Novo conjunto de dados** na barra de ferramentas e clique em **Armazenamento de Blobs do Azure** no menu suspenso.
2. Substitua o JSON no painel direito pelo trecho de código JSON a seguir:

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

    Um blob/arquivo de saída é gerado para cada fatia de entrada. Aqui está como um arquivo de saída é chamado para cada fatia. Todos os arquivos de saída são gerados em uma pasta de saída: `mycontainer\\outputfolder`.

    | **Fatia** | **Hora de início**          | **Arquivo de saída**       |
    |-----------|-------------------------|-----------------------|
    | 1         | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
    | 2         | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
    | 3         | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
    | 4         | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
    | 5         | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

    Lembre-se de que todos os arquivos em uma pasta de entrada (por exemplo, 2015-11-16-00) fazem parte de uma fatia com as horas de início mencionadas acima: 2015-11-16-00. Quando essa fatia é processada, a atividade personalizada examina cada arquivo e produz uma linha no arquivo de saída com o número de ocorrências do termo de pesquisa ("Microsoft"). Se houver três arquivos na pasta 2015-11-16-00, haverá três linhas no arquivo de saída: 2015-11-16-00.txt.

1. Clique em **Implantar** na barra de ferramentas para criar e implantar **OutputDataset**.

#### <a name="step-4-create-and-run-the-pipeline-with-custom-activity"></a>Etapa 4: criar e executar o pipeline com atividade personalizada
Nesta etapa, você cria um pipeline com uma atividade, a atividade personalizada criada anteriormente.

> [!IMPORTANT]
> Se você ainda não tiver carregado o **file.txt** para as pastas de entrada no contêiner de blob, faça isso antes de criar o pipeline. A propriedade **isPaused** é definida como false no JSON do pipeline, de modo que o pipeline é executado imediatamente, já que a data **inicial** está no passado.
>
>

1. No Editor Data Factory, clique em **Novo pipeline** na barra de comandos. Se você não vir o comando, clique em **... (Reticências)** para vê-lo.
2. Substitua o JSON no painel direito pelo script JSON a seguir:

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

   * Há apenas uma atividade no pipeline, e ela é do tipo: **DotNetActivity**.
   * **AssemblyName** é definido para o nome da DLL: **MyDotnetActivity.dll**.
   * **EntryPoint** é definido como **MyDotNetActivityNS.MyDotNetActivity**. Ele é basicamente \<namespace\>.\<classname\> em seu código.
   * **PackageLinkedService** é definido como **StorageLinkedService**, que aponta para o armazenamento de blobs que contém o arquivo zip da atividade personalizada. Se você estiver usando contas do Azure Storage diferentes para arquivos de entrada/saída e o arquivo zip da atividade personalizada, precisa de criar outro serviço vinculado do Azure Storage. Este artigo pressupõe que você está usando a mesma conta de armazenamento do Azure.
   * **PackageFile** é definido como **customactivitycontainer/MyDotNetActivity.zip**. Ele está no formato: \<containerforthezip\>/\<nameofthezip.zip\>.
   * A atividade personalizada usa **InputDataset** como entrada e **OutputDataset** como saída.
   * A propriedade **linkedServiceName** da atividade personalizada aponta para o **AzureBatchLinkedService**, que informa ao Azure Data Factory que a atividade personalizada precisa ser executada em um cluster do Azure Batch.
   * A configuração **simultaneidade** é importante. Se você usar o valor padrão, que é 1, mesmo que tenha dois ou mais nós de computação no pool de Lote do Azure, as fatias serão processadas uma após a outra. Portanto, você não está aproveitando a capacidade de processamento paralelo de Lote do Azure. Se você definir **simultaneidade** para um valor mais alto, digamos 2, isso significará que duas fatias (correspondendo a duas tarefas no Azure Batch) poderão ser processadas ao mesmo tempo, nesse caso, ambas as VMs no pool do Azure Batch serão utilizadas. Portanto, defina a propriedade de simultaneidade adequadamente.
   * Apenas uma tarefa (fatia) é executada em uma VM a qualquer momento por padrão. Isso ocorre porque, por padrão, o **Máximo de tarefas por VM** é definido como 1 para um pool do Azure Batch. Como parte dos pré-requisitos, você criou um pool com essa propriedade definida para 2, de modo que as fatias do Data Factory podem ser executadas em uma VM ao mesmo tempo.

    -   **isPaused** está definida para falso por padrão. O pipeline é executado imediatamente neste exemplo porque a fatias começam no passado. Você pode definir essa propriedade como verdadeira para pausar o pipeline e defini-lo novamente como falsa para reiniciar.

    -   As horas de **início** e **fim** são distantes cinco horas e as fatias são produzidas por hora, portanto, são produzidas cinco fatias pelo pipeline.

1. Clique em **Implantar** na barra de comandos para implantar o pipeline.

#### <a name="step-5-test-the-pipeline"></a>Etapa 5: testar o pipeline
Nesta etapa, você testa o pipeline soltando arquivos nas pastas de entrada. Vamos começar testando o pipeline com um arquivo por uma pasta de entrada.

1. Na folha Data Factory do portal do Azure, clique em **Diagrama**.

   ![](./media/data-factory-data-processing-using-batch/image10.png)
2. Na exibição de diagrama, clique duas vezes no conjunto de dados de entrada: **InputDataset**.

   ![](./media/data-factory-data-processing-using-batch/image11.png)
3. Você deve ver a folha **InputDataset** com todas as cinco fatias prontas. Observe a **HORA DE INÍCIO DA FATIA** e a **HORA DE TÉRMINO DA FATIA** de cada fatia.

   ![](./media/data-factory-data-processing-using-batch/image12.png)
4. Na **Exibição de Diagrama**, clique em **OutputDataset**.
5. Você deve ver que as cinco fatias de saída estarão no estado Pronto, se já tiverem sido produzidas.

   ![](./media/data-factory-data-processing-using-batch/image13.png)
6. Use o Portal do Azure para exibir as **tarefas** associadas às **fatias** e veja em qual VM cada fatia foi executada. Confira a seção [Integração de Data Factory e Lote](#data-factory-and-batch-integration) para obter detalhes.
7. Você deverá ver os arquivos de saída na `outputfolder` do `mycontainer` no armazenamento de blobs do Azure.

   ![](./media/data-factory-data-processing-using-batch/image15.png)

   Você deve ver cinco arquivos de saída, um para cada fatia de entrada. Cada arquivo de saída deve ter conteúdo semelhante à seguinte saída:

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
    ```
   O diagrama a seguir ilustra como as fatias do Data Factory são mapeadas para tarefas em Lote do Azure. Neste exemplo, uma fatia tem apenas uma execução.

   ![](./media/data-factory-data-processing-using-batch/image16.png)
8. Agora, vamos tentar com vários arquivos em uma pasta. Crie arquivos: **file2.txt**, **file3.txt**, **file4.txt** e **file5.txt** com o mesmo conteúdo que o file.txt na pasta: **2015-11-06-01**.
9. Na pasta de saída, **exclua** o arquivo de saída: **2015-11-16-01.txt**.
10. Agora, na folha **OutputDataset**, clique com o botão direito do mouse na fatia com **HORA DE INÍCIO DA FATIA** definida como **11/16/2015 01:00:00 AM (16/11/2015 01:00:00)** e clique em **Executar** para executar/processar novamente a fatia. Agora, a fatia tem cinco arquivos em vez de um.

    ![](./media/data-factory-data-processing-using-batch/image17.png)
11. Depois que a fatia for executada e seu status for **Pronto**, verifique se no conteúdo do arquivo de saída existe esta fatia (**2015-11-16-01.txt**) na `outputfolder` do `mycontainer` no armazenamento de blobs. Deve haver uma linha para cada arquivo da fatia.

    ```
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
    2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.
    ```

> [!NOTE]
> Se você não tiver excluído o arquivo de saída 2015-11-16-01.txt antes de tentar com arquivos de entrada, uma linha da execução da fatia anterior e cinco linhas da execução da fatia atual serão exibidas. Por padrão, o conteúdo é anexado ao arquivo de saída, se ele já existir.
>
>

#### <a name="data-factory-and-batch-integration"></a>Integração de Data Factory e Lote
O serviço Data Factory cria um trabalho no Lote do Azure com o nome `adf-poolname:job-xxx`.

![Trabalhos de Azure Data Factory - Lote](media/data-factory-data-processing-using-batch/data-factory-batch-jobs.png)

Uma tarefa é criada no trabalho para cada execução de atividade de uma fatia. Se houver 10 fatias prontas para serem processadas, 10 tarefas serão criadas no trabalho. Pode haver mais de uma fatia em execução em paralelo, se você tiver vários nós de computação no pool. Se o máximo de tarefas por nó de computação for definido como > 1, pode haver mais de uma fatia em execução na mesma computação.

Neste exemplo, há cinco fatias, então cinco tarefas no Azure Batch. Com a **simultaneidade** definida como **5** no JSON do pipeline no Azure Data Factory e **Máximo de tarefas por VM** definido como **2** no pool do Azure Batch com **2** VMs, as tarefas são executadas rapidamente (verifique os horários de início e de término das tarefas).

Use o portal para exibir o trabalho do Lote e suas tarefas associadas às **fatias** e veja em qual VM cada fatia foi executada.

![Tarefas do trabalho de Azure Data Factory - Lote](media/data-factory-data-processing-using-batch/data-factory-batch-job-tasks.png)

### <a name="debug-the-pipeline"></a>Depurar o pipeline
A depuração consiste em algumas técnicas básicas:

1. Se a fatia de entrada não estiver definida como **Pronto**, confirme se a estrutura da pasta de entrada está correta e se file.txt existe nas pastas de entrada.

   ![](./media/data-factory-data-processing-using-batch/image3.png)
2. No método **Execute** da atividade personalizada, use o objeto **IActivityLogger** para registrar informações que o ajudam a solucionar problemas. As mensagens registradas aparecerão no arquivo user\_0.log.

   Na folha **OutputDataset**, clique na fatia para ver a folha **FATIA DE DADOS** dessa fatia. Você vê as **execuções de atividade** para essa fatia. Você deverá ver uma execução de atividade para a fatia. Se você clicar em **Executar** na barra de comandos, poderá iniciar outra execução de atividade para a mesma fatia.

   Quando você clicar na execução da atividade, verá a folha **DETALHES DE EXECUÇÃO DA ATIVIDADE** com uma lista de arquivos de log. Você vê mensagens registradas no arquivo **user\_0.log**. Quando ocorrer um erro, você verá três execuções de atividade porque a contagem de repetições é definida como 3 no JSON do pipeline/atividade. Quando você clicar na execução da atividade, verá os arquivos de log que pode examinar para solucionar o erro.

   ![](./media/data-factory-data-processing-using-batch/image18.png)

   Na lista de arquivos de log, clique em **user-0.loo**. No painel à direita, estão os resultados do uso do método **IActivityLogger.Write** .

   ![](./media/data-factory-data-processing-using-batch/image19.png)

   Verifique o system-0.log para quaisquer mensagens de erro e exceções do sistema.

    ```
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...
    
    Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module
    
    Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully
    ```
3. Inclua o arquivo **PDB** no arquivo zip para que os detalhes do erro tenham informações como **pilha de chamadas** quando ocorrer um erro.
4. Todos os arquivos no arquivo zip para a atividade personalizada devem estar no **nível superior** sem subpastas.

   ![](./media/data-factory-data-processing-using-batch/image20.png)
5. Verifique se **assemblyName** (MyDotNetActivity.dll), **entryPoint** (MyDotNetActivityNS.MyDotNetActivity), **packageFile** (customactivitycontainer/MyDotNetActivity.zip) e **packageLinkedService** (devem apontar para o armazenamento de blobs do Azure que contém o arquivo zip) estão definidos com os valores corretos.
6. Se você corrigir um erro e quiser reprocessar a fatia, clique com o botão direito do mouse na fatia na folha **OutputDataset** e clique em **Executar**.

   ![](./media/data-factory-data-processing-using-batch/image21.png)

   > [!NOTE]
   > Você verá um **contêiner** no Armazenamento de blobs do Azure chamado `adfjobs`. Esse contêiner não é automaticamente excluído, mas você pode excluí-lo com segurança depois de concluir o teste da solução. Da mesma forma, a solução Data Factory cria um **trabalho** do Lote do Azure chamado `adf-\<pool ID/name\>:job-0000000001`. Você pode excluir esse trabalho depois de testar a solução, se desejar.
   >
   >
7. A atividade personalizada não usa o arquivo **app.config** do seu pacote. Portanto, se seu código lê as cadeias de conexão do arquivo de configuração, ele não funciona no tempo de execução. A prática recomendada ao usar o Azure Batch é armazenar os segredos em um **Azure KeyVault**, usar uma entidade de serviço com base em certificados para proteger o keyvault e distribuir o certificado para o pool do Azure Batch. A atividade personalizada do .NET pode então acessar segredos no KeyVault no tempo de execução. Essa é uma solução genérica e pode ser dimensionada para qualquer tipo de segredo, não apenas a cadeia de conexão.

    Há uma solução alternativa mais fácil (mas não é uma prática recomendada): você pode criar um **serviço vinculado do SQL Azure** com configurações da cadeia de conexão, criar um conjunto de dados que usa o serviço vinculado e encadear o conjunto de dados como um conjunto de dados de entrada fictício para a atividade personalizada do .NET. Você pode então acessar a cadeia de conexão do serviço vinculado no código de atividade personalizada e isso deve funcionar bem no tempo de execução.  

#### <a name="extend-the-sample"></a>Estender o exemplo
Você pode estender este exemplo para saber mais sobre os recursos de Data Factory do Azure e Lote do Azure. Por exemplo, para processar fatias em um intervalo de tempo diferente, realize as seguintes etapas:

1. Adicione as seguintes subpastas a `inputfolder`: 2015-11-16-05, 2015-11-16-06, 201-11-16-07, 2011-11-16-08, 2015-11-16-09. Depois, coloque os arquivos de entrada nessas pastas. Altere a hora de término do pipeline de `2015-11-16T05:00:00Z` para `2015-11-16T10:00:00Z`. Na **Exibição de Diagrama**, clique duas vezes em **InputDataset** e confirme se as fatias de entrada estão prontas. Clique duas vezes em **OuptutDataset** para ver o estado das fatias de saída. Se estiverem no estado Pronto, verifique se os arquivos de saída estão na pasta de saída.
2. Aumente ou diminua a configuração de **simultaneidade** para entender como ela afeta o desempenho da sua solução, especialmente o processamento que ocorre no Lote do Azure. (Consulte a etapa 4: criar e executar o pipeline para obter mais informações sobre a configuração **simultaneidade** .)
3. Crie um pool com **Máximo de tarefas por VM**maior/menor. Para usar o novo pool criado, atualize o serviço vinculado do Azure Batch na solução do Data Factory. (Consulte a etapa 4: criar e executar o pipeline para obter mais informações sobre a configuração **Máximo de tarefas por VM** .)
4. Crie um pool do Lote do Azure com o recurso **dimensionar automaticamente** . O dimensionamento automático de nós de computação em um pool do Lote do Azure é o ajuste dinâmico da potência de processamento usada pelo seu aplicativo. 

    A fórmula de exemplo alcança o seguinte comportamento: quando o pool é inicialmente criado, ele começa com uma VM. A métrica de $PendingTasks define o número de tarefas em execução + estado ativo (em fila).  A fórmula localiza o número médio de tarefas pendentes nos últimos 180 segundos e define TargetDedicated adequadamente. Isso garante que TargetDedicated nunca ultrapasse 25 VMs. Assim, o pool aumenta automaticamente conforme novas tarefas são enviadas e, conforme as tarefas são concluídas, as VMs se liberam uma a uma e são reduzidas pelo dimensionamento automático. startingNumberOfVMs e maxNumberofVMs podem ser ajustados às suas necessidades.
 
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
5. Na solução de exemplo, o método **Execute** invoca o método **Calculate**, que processa uma fatia de dados de entrada para produzir uma fatia de dados de saída. Você pode escrever seu próprio método para processar dados de entrada e substituir a chamada do método Calculate no método Execute por uma chamada para o seu método.

### <a name="next-steps-consume-the-data"></a>Próximas etapas: consumir os dados
Depois de processar dados, é possível consumi-lo com ferramentas online como o **Microsoft Power BI**. Aqui estão links para ajudá-lo a entender o Power BI e como usá-lo no Azure:

* [Explorar um conjunto de dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-data/)
* [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/)
* [Atualizar dados no Power BI](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/)
* [Azure e Power BI - visão geral básica](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

## <a name="references"></a>Referências
* [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

  * [Introdução ao serviço do Azure Data Factory](data-factory-introduction.md)
  * [Introdução ao Data Factory do Azure](data-factory-build-your-first-pipeline.md)
  * [Usar atividades personalizadas em um pipeline do Data Factory do Azure](data-factory-use-custom-activities.md)
* [Lote do Azure](https://azure.microsoft.com/documentation/services/batch/)

  * [Noções básicas de Lote do Azure](../../batch/batch-technical-overview.md)
  * [Visão geral dos recursos do Lote do Azure](../../batch/batch-api-basics.md)
  * [Criar e gerenciar uma conta do Azure Batch no Portal do Azure](../../batch/batch-account-create-portal.md)
  * [Introdução ao .NET da Biblioteca de Lote do Azure](../../batch/batch-dotnet-get-started.md)

[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
