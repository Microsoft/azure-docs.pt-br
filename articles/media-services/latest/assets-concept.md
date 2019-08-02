---
title: Ativos nos serviços de mídia do Azure | Microsoft Docs
description: Este artigo fornece uma explicação sobre a natureza dos ativos e como eles são usados pelos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d0a81d5d7ce8e7569b77007b6ad9c322cf626f16
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670693"
---
# <a name="assets"></a>Ativos

Nos serviços de mídia do Azure, uma [ativo](https://docs.microsoft.com/rest/api/media/assets) contém informações sobre arquivos digitais armazenados no armazenamento do Azure (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda oculta). 

Um ativo é mapeado para um contêiner de blob na [conta de Armazenamento do Microsoft Azure](storage-account-concept.md) e os arquivos no ativo são armazenados como blob de blocos nesse contêiner. Os Serviços de Mídia oferecem suporte a camadas de Blob quando a conta usa Armazenamento de uso geral v2 (GPv2). Com o GPv2, você pode mover arquivos para o [armazenamento esporádico ou para arquivar](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). O armazenamento **de Arquivamento** é adequado para arquivar arquivos fontes quando não forem mais necessário (por exemplo, após terem sido decodificados).

A camada de armazenamento de **Arquivamento** só é recomendada para arquivos de origem muito grandes que já tenham sido codificados e a saída do trabalho de codificação foi colocada em um contêiner de blobs de saída. Os blobs no contêiner de saída que você deseja associar a um ativo e usar para transmitir ou analisar seu conteúdo, deve existir em uma camada de armazenamento de **frequente** ou **esporádico**.

## <a name="upload-digital-files-into-assets"></a>Carregar os arquivos digitais em Ativos

Depois que os arquivos digitais são carregados no armazenamento e associados a um ativo, pode ser usados nos serviços de mídia, codificação, streaming, analisando os fluxos de trabalho de conteúdo. Um dos fluxos de trabalho dos Serviços de Mídia do Azure comuns é carregar, codificar e transmitir um arquivo. Esta seção descreve as etapas gerais.

> [!TIP]
> Antes de começar a desenvolver, examine [desenvolver com APIs dos serviços de mídia v3](media-services-apis-overview.md) (inclui informações sobre como acessar as APIs, as convenções de nomenclatura, etc.)

1. Use a API dos Serviços de Mídia do Azure v3 para criar um novo ativo de "entrada". Esta operação cria um contêiner na conta de armazenamento associada com sua conta de Serviços de Mídia do Azure. A API retorna o nome do contêiner (por exemplo, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).
   
    Se você já tiver um contêiner de blob que deseja associar a um ativo, você pode especificar o nome do contêiner ao criar o ativo. Os Serviços de Mídia do Azure atualmente suportam apenas blobs na raiz do contêiner e não com caminhos no nome do arquivo. Portanto, um contêiner com o nome do arquivo "input.mp4" funcionará. Porém, um contêiner com o nome do arquivo "videos/inputs/input.mp4", não funcionará.

    Você pode usar a CLI do Azure para carregar diretamente em qualquer conta de armazenamento e contêiner que você tiver direitos na sua assinatura. <br/>O nome do contêiner deve ser exclusivo e seguir as diretrizes de nomeação de armazenamento. O nome não precisa seguir a formatação do nome do contêiner de ativo dos Serviços de Mídia do Azure (GUID do ativo). 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Obter uma URL de SAS com permissões de leitura / gravação que serão usadas para carregar arquivos digitais em contêiner do ativo. Você pode usar a API de Serviços de Mídia do Azure [listará as URLs de contêiner do ativo](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Use as SDKs ou as APIs de Armazenamento do Azure (por exemplo, a [API REST de Armazenamento](../../storage/common/storage-rest-api-auth.md), [JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), ou [SDK do .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) para carregar arquivos no contêiner do Ativo. 
4. Use as APIs dos Serviços de Mídia v3 para criar uma transformação e um trabalho para processar seu ativo de "entrada". Para obter mais informações, consulte [Transformações e Trabalhos](transform-concept.md).
5. Transmitir o conteúdo do ativo de "saída".

Para obter um exemplo de .NET completo que mostra como: criar o ativo, obter uma URL SAS gravável para o contêiner do ativo no armazenamento, carregue o arquivo no contêiner no armazenamento usando a URL de SAS, consulte [Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Criar um novo ativo

> [!NOTE]
> As propriedades de ativos do tipo Datetime estão sempre no formato UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Para obter um exemplo REST, consulte [Criar um ativo com exemplo de REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples).

O exemplo mostra como criar o **Corpo da Solicitação** onde você pode especificar informações úteis, como descrição, nome do contêiner, conta de armazenamento e outras informações.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

Para obter um exemplo completo, consulte [Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md). Nos Serviços de Mídia do Azure v3, entrada de um trabalho também pode ser criada de URLs HTTPS (consulte [criar uma entrada de trabalho de uma URL HTTPS](job-input-from-http-how-to.md)).

## <a name="map-v3-asset-properties-to-v2"></a>Mapear propriedades de ativo v3 v2

A tabela a seguir mostra como o [ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)do propriedades na v3 são mapeadas para propriedades do ativo no v2.

|Propriedades de V3|Propriedades de v2|
|---|---|
|ID - (exclusivo) o caminho completo do Azure Resource Manager, veja exemplos na [ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|nome – (exclusivo) consulte [convenções de nomenclatura](media-services-apis-overview.md#naming-conventions) ||
|alternateId|AlternateId|
|assetId|ID - valor (exclusivo) começa com o `nb:cid:UUID:` prefixo.|
|criado|Criado|
|description|Nome|
|lastModified|LastModified|
|storageAccountName|StorageAccountName|
|storageEncryptionFormat| Opções (opções de criação)|
|type||

## <a name="storage-side-encryption"></a>Criptografia do armazenamento

Para proteger os Ativos em repouso, os ativos devem ser criptografados pela criptografia do armazenamento. A tabela a seguir mostra como a criptografia do armazenamento funciona nos Serviços de Mídia:

|Opção de criptografia|DESCRIÇÃO|Serviços de Mídia v2|Serviços de Mídia v3|
|---|---|---|---|
|Criptografia do Armazenamento dos Serviços de Mídia|Criptografia AES-256, chave gerenciada pelos Serviços de Mídia|Com suporte<sup>(1)</sup>|Sem suporte<sup>(2)</sup>|
|[Criptografia do Serviço de Armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Criptografia do servidor oferecida pelo Armazenamento do Microsoft Azure, chave gerenciada pelo Azure ou pelo cliente|Com suporte|Com suporte|
|[Criptografia do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Criptografia do cliente oferecida pelo armazenamento do Azure, chave gerenciada pelo cliente no Key Vault|Sem suporte|Sem suporte|

<sup>1</sup> Enquanto os Serviços de Mídia deem suporte para tratamento de conteúdo sem qualquer forma de criptografia/limpo, não é recomendável fazer isso.

<sup>2</sup> Nos Serviços de Mídia v3, a criptografia de armazenamento (criptografia AES-256) somente terá suporte para compatibilidade com versões anteriores quando os Ativos tiverem sido criados com os Serviços de Mídia v2. Isso significa que o v3 funciona com recursos criptografados de armazenamento existentes, mas não permite a criação de novos recursos.

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

Confira [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md).

## <a name="next-steps"></a>Próximas etapas

* [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
* [Usar um DVR de nuvem](live-event-cloud-dvr.md)
* [Diferenças entre os Serviços de Mídia do Azure v2 e v3](migrate-from-v2-to-v3.md)
