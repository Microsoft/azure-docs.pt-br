---
title: Usar marcas de índice de BLOB para gerenciar e localizar dados no armazenamento de BLOBs do Azure
description: Consulte exemplos de como usar marcas de índice de BLOB para categorizar, gerenciar e consultar objetos de BLOB.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/19/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.custom: devx-track-csharp
ms.openlocfilehash: 411815ca2f947c47b8dfb0d2e5d61f8ea18f3545
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95541242"
---
# <a name="use-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>Usar marcas de índice de BLOB (versão prévia) para gerenciar e localizar dados no armazenamento de BLOBs do Azure

Marcas de índice de blob categorizam dados em sua conta de armazenamento usando atributos de marca de chave-valor. Essas marcas são indexadas e expostas automaticamente como um índice multidimensional pesquisável para localizar dados facilmente. Este artigo mostra como definir, obter e localizar dados usando marcas de índice de blob.

> [!NOTE]
> O índice de blob está em visualização pública e está disponível nas regiões do **Canadá central**, **leste do Canadá**, **França central** e **França sul** . Para saber mais sobre esse recurso juntamente com limitações e problemas conhecidos, consulte [gerenciar e localizar dados de blob do Azure com marcas de índice de BLOB (versão prévia)](storage-manage-find-blobs.md).

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)

- Uma assinatura do Azure registrada e aprovada para acesso à visualização do índice de BLOB
- Acesso ao [portal do Azure](https://portal.azure.com/)

# <a name="net"></a>[.NET](#tab/net)

Como o índice de blob está em versão prévia, o pacote de armazenamento do .NET é liberado no feed do NuGet de visualização. Esta biblioteca está sujeita a alterações durante o período de visualização.

1. Configure seu projeto do Visual Studio para começar a usar a biblioteca de cliente do armazenamento de BLOBs do Azure V12 para .NET. Para saber mais, consulte [início rápido do .net](storage-quickstart-blobs-dotnet.md)

2. No Gerenciador de pacotes NuGet, localize o pacote **Azure. Storage. BLOBs** e instale a versão **12.7.0-Preview. 1** ou mais recente em seu projeto. Você também pode executar o comando do PowerShell: `Install-Package Azure.Storage.Blobs -Version 12.7.0-preview.1`

   Para saber como, confira [Localizar e instalar um pacote](/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package).

3. Adicione o seguinte usando as instruções na parte superior do seu arquivo de código.

    ```csharp
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    using Azure.Storage.Blobs.Specialized;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    ```

---

## <a name="upload-a-new-blob-with-index-tags"></a>Carregar um novo blob com marcas de índice

Essa tarefa pode ser executada por um [proprietário de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) ou uma entidade de segurança que tenha recebido permissão para a `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [operação do provedor de recursos do Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) por meio de uma função personalizada do Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://portal.azure.com/), selecione a conta de armazenamento 

2. Navegue até a opção **contêineres** em **serviço blob**, selecione seu contêiner

3. Selecione o botão **carregar** e procure o sistema de arquivos local para localizar um arquivo a ser carregado como um blob de blocos.

4. Expanda o menu suspenso **Avançado** e vá para a seção **Marcas do Índice de Blob**

5. Insira as marcas do índice de blob de chave/valor que você deseja aplicar aos seus dados

6. Selecione o botão **Carregar** para carregar o blob

:::image type="content" source="media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png" alt-text="Captura de tela do portal do Azure mostrando como carregar um blob com marcas de índice.":::

# <a name="net"></a>[.NET](#tab/net)

O exemplo a seguir mostra como criar um blob de acréscimo com marcas definidas durante a criação.

```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");

          // Blob index tags to upload
          AppendBlobCreateOptions appendOptions = new AppendBlobCreateOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>Obter, definir e atualizar marcas de índice de BLOB

A obtenção de marcas de índice de blob pode ser executada por um [proprietário de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) ou uma entidade de segurança que tenha recebido permissão para a operação do provedor de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` [recursos do Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) por meio de uma função personalizada do Azure.

A configuração e a atualização de marcas de índice de blob podem ser executadas por um [proprietário de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) ou uma entidade de segurança que tenha recebido permissão para a operação do provedor de `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` [recursos do Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) por meio de uma função personalizada do Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://portal.azure.com/), selecione a conta de armazenamento 

2. Navegue até a opção **Contêineres** em **Serviço Blob** e selecione seu contêiner

3. Selecione o blob na lista de BLOBs dentro do contêiner selecionado

4. A guia visão geral do blob exibirá as propriedades do blob, inclusive quaisquer **Marcas do Índice de Blob**

5. Você pode obter, definir, modificar ou excluir qualquer uma das marcas de índice de chave/valor para seu blob

6. Selecione o botão **Salvar** para confirmar as atualizações do seu blob

:::image type="content" source="media/storage-blob-index-concepts/blob-index-get-set-tags.png" alt-text="Captura de tela do portal do Azure mostrando como obter, definir, atualizar e excluir marcas de índice em BLOBs.":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or update blob index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get blob index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List blobs with all options returned including blob index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing blob index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>Filtrar e localizar dados com marcas de índice de BLOB

Essa tarefa pode ser executada por um [proprietário de dados de blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) ou uma entidade de segurança que tenha recebido permissão para a `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` [operação do provedor de recursos do Azure](../../role-based-access-control/resource-provider-operations.md#microsoftstorage) por meio de uma função personalizada do Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Dentro do portal do Azure, o filtro de marcas de índice de blob aplica automaticamente o `@container` parâmetro ao escopo do contêiner selecionado. Se você quiser filtrar e localizar dados marcados em toda a conta de armazenamento, use nossa API REST, SDKs ou ferramentas.

1. No [portal do Azure](https://portal.azure.com/), selecione a conta de armazenamento. 

2. Navegue até a opção **contêineres** em **serviço blob**, selecione seu contêiner

3. Selecione o botão **Filtrar marcas do Índice de Blob** para filtrar dentro do contêiner selecionado

4. Inserir uma chave de marca de índice de BLOB e um valor de marca

5. Selecione o botão **Filtrar marcas do Índice de Blob** para adicionar filtros de marca adicionais (até 10)

:::image type="content" source="media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png" alt-text="Captura de tela do portal do Azure mostrando como filtrar e localizar BLOBs marcados usando marcas de índice":::

# <a name="net"></a>[.NET](#tab/net)

```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<TaggedBlobItem> blobs = new List<TaggedBlobItem>();
          await foreach (TaggedBlobItem taggedBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(taggedBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>Gerenciamento do ciclo de vida com filtros de marca de índice de blob

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://portal.azure.com/), selecione a conta de armazenamento. 

2. Navegue até a opção de **Gerenciamento do Ciclo de Vida** em **Serviço de blob**

3. Selecione *Adicionar regra* e preencha os campos de formulário do conjunto de Ações

4. Selecione conjunto de **filtros** para Adicionar filtro opcional para correspondência de prefixo e correspondência de índice de BLOB

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png" alt-text="Captura de tela do portal do Azure mostrando como adicionar marcas de índice para o gerenciamento do ciclo de vida.":::

5. Selecione **examinar + adicionar** para revisar as configurações de regra

  :::image type="content" source="media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png" alt-text="Captura de tela da portal do Azure mostrando uma regra de gerenciamento de ciclo de vida com marcas de índice de blob exemplo de filtro":::

6. Selecione **Adicionar** para aplicar a nova regra à política de gerenciamento do ciclo de vida

# <a name="net"></a>[.NET](#tab/net)

As políticas de [Gerenciamento de ciclo de vida](storage-lifecycle-management-concepts.md) são aplicadas para cada conta de armazenamento no nível do plano de controle. Para o .NET, instale o [Microsoft Azure biblioteca de armazenamento de gerenciamento](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) versão 16.0.0 ou superior.

---

## <a name="next-steps"></a>Próximas etapas

 - Saiba mais sobre marcas de índice de BLOB, consulte [gerenciar e localizar dados de blob do Azure com marcas de índice de BLOB (versão prévia)](storage-manage-find-blobs.md )
 - Saiba mais sobre o gerenciamento do ciclo de vida, consulte [gerenciar o ciclo de vida do armazenamento de BLOBs](storage-lifecycle-management-concepts.md)