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

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="rest"></a>[REST](#tab/rest/)

Depois que você tiver [criado um ativo usando o Postman ou outro método REST e tiver obtido a URL SAS para o ativo](how-to-create-asset.md?tabs=rest), use os SDKs ou as APIs do Armazenamento do Microsoft Azure (por exemplo, a [API REST de armazenamento](../../storage/common/storage-rest-api-auth.md) ou o [SDK do .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

---
<!-- add these to the tabs when available -->
Para outros métodos, confira a [Documentação do Armazenamento do Microsoft Azure](../../storage/blobs/index.yml) para trabalhar com blobs em [.NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [Java](../../storage/blobs/storage-quickstart-blobs-java.md), [Python](../../storage/blobs/storage-quickstart-blobs-python.md) e [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md).

## <a name="next-steps"></a>Próximas etapas

> [Visão geral dos Serviços de Mídia](media-services-overview.md)