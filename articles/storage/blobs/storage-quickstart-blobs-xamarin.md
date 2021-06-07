---
title: 'Início rápido: Biblioteca do Armazenamento de Blobs do Azure v12 – Xamarin'
description: Neste início rápido, você aprenderá a usar a biblioteca de clientes do Armazenamento de Blobs do Azure versão 12 com o Xamarin para criar um contêiner e um blob no Armazenamento de Blobs (objeto). Em seguida, você aprenderá a baixar o blob para seu dispositivo móvel e a listar todos os blobs em um contêiner.
author: codemillmatt
ms.author: masoucou
ms.date: 10/09/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: c6b502bbc56d6c9521ec736150fbc0046ff78125
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642476"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>Início rápido: Biblioteca de clientes do Armazenamento de Blobs do Azure v12 com o Xamarin

Introdução à biblioteca de clientes do Armazenamento de Blobs do Azure v12 com o Xamarin. O Armazenamento de Blobs do Azure é uma solução de armazenamento de objetos da Microsoft para a nuvem. Siga as etapas para instalar o pacote e testar o código de exemplo para tarefas básicas. O Armazenamento de Blobs é otimizado para armazenar grandes quantidades de dados não estruturados.

Use a biblioteca de clientes do Armazenamento de Blobs do Azure v12 com o Xamarin para:

* Criar um contêiner
* Carregar um blob para o Armazenamento do Azure
* Listar todos os blobs em um contêiner
* Baixar o blob para seu dispositivo
* Excluir um contêiner

Links de referência:

* [Documentação de referência da API](/dotnet/api/azure.storage.blobs)
* [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [Pacote (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [Amostra](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
* Conta de armazenamento do Azure – [criar uma conta de armazenamento](../common/storage-account-create.md)
* Visual Studio com o [Desenvolvimento Móvel para carga de trabalho do .NET](/xamarin/get-started/installation/?pivots=windows) instalado ou [Visual Studio para Mac](/visualstudio/mac/installation?view=vsmac-2019&preserve-view=true)

## <a name="setting-up"></a>Configurando
    
Esta seção fornece instruções sobre como preparar um projeto para funcionar com a biblioteca de clientes do Armazenamento de Blobs do Azure v12 com o Xamarin.
    
### <a name="create-the-project"></a>Criar o projeto

1. Abra o Visual Studio e crie um Aplicativo de Formatos em Branco.
1. Dê a ele o nome: BlobQuickstartV12

### <a name="install-the-package"></a>Instalar o pacote

1. Clique com o botão direito no painel do Gerenciador de Soluções e selecione **Gerenciar Pacotes NuGet para a Solução**.
1. Pesquise **Azure.Storage.Blobs** e instale a versão estável mais recente em todos os projetos em sua solução.

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório **BlobQuickstartV12**:

1. Abra o arquivo *MainPage.xaml* em seu editor
1. Remova tudo entre os elementos de `<ContentPage></ContentPage>` e substitua pelo seguinte:

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

## <a name="object-model"></a>Modelo de objeto

O Armazenamento de Blobs do Azure é otimizado para armazenar grandes quantidades de dados não estruturados. Dados não estruturados são dados que não estão de acordo com uma definição ou um modelo de dados específico, como texto ou dados binários. O Armazenamento de Blobs oferece três tipos de recursos:

* A conta de armazenamento
* Um contêiner na conta de armazenamento
* Um blob no contêiner

O diagrama a seguir mostra a relação entre esses recursos.

![Diagrama da arquitetura de Armazenamento de Blobs](./media/storage-blobs-introduction/blob1.png)

Use as seguintes classes .NET para interagir com esses recursos:

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): a classe `BlobServiceClient` permite manipular os recursos do Armazenamento do Azure e os contêineres do blob.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): a classe `BlobContainerClient` permite manipular os contêineres do Armazenamento do Azure e seus blobs.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): a classe `BlobClient` permite manipular os blobs do Armazenamento do Azure.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): a classe `BlobDownloadInfo` representa as propriedades e o conteúdo retornados do download de um blob.

## <a name="code-examples"></a>Exemplos de código

Esses snippets de código de exemplo mostram como executar as seguintes tarefas com a biblioteca de clientes do Armazenamento de Blobs do Azure para .NET em um aplicativo Xamarin.Forms:

* [Criar variáveis de nível de classe](#create-class-level-variables)
* [Criar um contêiner](#create-a-container)
* [Carregar blobs em um contêiner](#upload-blobs-to-a-container)
* [Listar os blobs em um contêiner](#list-the-blobs-in-a-container)
* [Baixar blobs](#download-blobs)
* [Excluir um contêiner](#delete-a-container)

### <a name="create-class-level-variables"></a>Criar variáveis de nível de classe

O código abaixo declara muitas variáveis de nível de classe. Elas precisavam se comunicar com o Armazenamento de Blobs do Azure em todo o restante desse exemplo.

Eles são adicionais à cadeia de conexão da conta de armazenamento definida na seção [Configurar sua cadeia de conexão de armazenamento](#configure-your-storage-connection-string).

Adicione este código como variáveis de nível de classe dentro do arquivo *MainPage.xaml.cs*:

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>Criar um contêiner

Escolha um nome para o novo contêiner. O código abaixo anexa um valor GUID ao nome do contêiner para garantir que ele seja exclusivo.

> [!IMPORTANT]
> Os nomes de contêiner devem estar em minúsculas. Para saber mais sobre como nomear contêineres e blobs, veja [Nomenclatura e referência de contêineres, blobs e metadados](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Crie uma instância da classe [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient). Em seguida, chame o método [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) para criar o contêiner em sua conta de armazenamento.

Adicione esse código ao arquivo *MainPage.xaml.cs*:

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>Carregar blobs em um contêiner

O trecho de código a seguir:

1. Cria um `MemoryStream` de texto.
1. Carrega o texto em um blob chamando a função [UploadAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) da classe [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient), passando-a no nome do arquivo e no `MemoryStream` do texto. Esse método criará o blob se ele ainda não existir e o substituirá se já existir.

Adicione esse código ao arquivo *MainPage.xaml.cs*:

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>Listar os blobs em um contêiner

Liste os blobs no contêiner chamando o método [GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync). Nesse caso, apenas um blob foi adicionado ao contêiner, portanto, a operação de listagem retorna apenas esse blob.

Adicione esse código ao arquivo *MainPage.xaml.cs*:

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>Baixar blobs

Baixe o blob criado anteriormente chamando o método [DownloadToAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadtoasync). O código de exemplo copia a representação `Stream` do blob primeiro para um `MemoryStream` e, em seguida, para um `StreamReader` para que o texto possa ser exibido.

Adicione esse código ao arquivo *MainPage.xaml.cs*:

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>Excluir um contêiner

O código a seguir limpa os recursos que o aplicativo criou ao excluir todo o contêiner usando [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync).

Primeiro, o aplicativo solicita a confirmação antes de excluir o blob e o contêiner. Essa é uma boa oportunidade de verificar se os recursos foram criados corretamente antes de serem excluídos.

Adicione esse código ao arquivo *MainPage.xaml.cs*:

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>Executar o código

Quando o aplicativo for iniciado, primeiro ele criará o contêiner como ele aparece. Em seguida, você precisará clicar nos botões para carregar, listar, baixar os blobs e excluir o contêiner.

Para executar o aplicativo no Windows, pressione F5. Para executar o aplicativo no Mac, pressione Cmd + Enter.

O aplicativo é gravado na tela após cada operação. A saída do aplicativo é semelhante ao exemplo abaixo:

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

Antes de começar o processo de limpeza, verifique se a saída do conteúdo do blob na tela corresponde ao valor que foi carregado.

Após verificar os valores, confirme o prompt para excluir o contêiner e concluir a demonstração.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a carregar, baixar e listar blobs usando a biblioteca de clientes do Armazenamento de Blobs do Azure v12 com o Xamarin.

Para ver os aplicativos de exemplo de armazenamento de blobs, acesse:

> [!div class="nextstepaction"]
> [Exemplo do SDK do Armazenamento de Blobs do Azure v12 Xamarin](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* Para acessar tutoriais, exemplos, inícios rápidos e outras documentações, visite [Azure para desenvolvedores de dispositivos móveis](/azure/mobile-apps).
* Para saber mais sobre o Xamarin, confira [Introdução ao Xamarin](/xamarin/get-started/).
