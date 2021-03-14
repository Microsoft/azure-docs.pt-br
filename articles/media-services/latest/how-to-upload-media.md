---
Título: Carregar mídia: Descrição dos Serviços de Mídia do Azure: Saiba como carregar mídia para streaming ou codificação.
services: media-services documentationcenter: '' author: IngridAtMicrosoft manager: femila editor: ''

ms.service: media-services ms.workload: ms.topic: how-to ms.date: 08/31/2020 ms.author: inhenkel
---

# <a name="upload-media-for-streaming-or-encoding"></a>Carregar mídia para streaming ou codificação

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Nos Serviços de Mídia, você pode carregar seus arquivos digitais (mídia) em um contêiner de blob associado a um ativo. A entidade [Asset](/rest/api/media/operations/asset) pode conter vídeo, áudio, imagens, coleções de miniaturas, sequências de texto e arquivos de legendas (e os metadados sobre esses arquivos). Depois que os arquivos são carregados no contêiner do ativo, o conteúdo é armazenado com segurança na nuvem para processamento e transmissão adicionais.

No entanto, antes de começar, você precisará coletar ou pensar em alguns valores.

1. O caminho do arquivo local até o arquivo que você deseja carregar
1. A ID do ativo para o ativo (contêiner)
1. O nome que você deseja usar para o arquivo carregado, incluindo a extensão
1. O nome da conta de armazenamento que você está usando
1. A chave de acesso da sua conta de armazenamento

## <a name="portal"></a>[Portal](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Upload files with the CLI](./includes/task-upload-file-to-asset-cli.md)]

## <a name="python"></a>[Python](#tab/python)

Supondo que o código já tenha estabelecido a autenticação e você já tenha criado um ativo de entrada, use o seguinte snippet de código para carregar arquivos locais nesse ativo (in_container).

```python
#The storage objects
from azure.storage.blob import BlobServiceClient, BlobClient

#Establish storage variables
storage_account_name = '<your storage account name'
storage_account_key = '<your storage account key'
storage_blob_url = 'https://<your storage account name>.blob.core.windows.net/'

in_container = 'asset-' + inputAsset.asset_id

#The file path of local file you want to upload
source_file = "ignite.mp4"

# Use the Storage SDK to upload the video
blob_service_client = BlobServiceClient(account_url=storage_blob_url, credential=storage_account_key)
blob_client = blob_service_client.get_blob_client(in_container,source_file)

# Upload the video to storage as a block blob
with open(source_file, "rb") as data:
    blob_client.upload_blob(data, blob_type="BlockBlob")
```

---
<!-- add these to the tabs when available -->
Para outros métodos, confira a [Documentação do Armazenamento do Microsoft Azure](../../storage/blobs/index.yml) para trabalhar com blobs em [.NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [Java](../../storage/blobs/storage-quickstart-blobs-java.md), [Python](../../storage/blobs/storage-quickstart-blobs-python.md) e [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md).

## <a name="next-steps"></a>Próximas etapas

> [Visão geral dos Serviços de Mídia](media-services-overview.md)
