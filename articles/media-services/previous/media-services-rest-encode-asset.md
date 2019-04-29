---
title: Como codificar um ativo do Azure usando o Media Encoder Standard | Microsoft Docs
description: Saiba como usar o Media Encoder Standard para codificar conteúdo de mídia nos Serviços de Mídia do Azure. Exemplos de código usam a API REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2a7273c6-8a22-4f82-9bfe-4509ff32d4a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 8db9e60e9ce99eaf2621821825620966b8b8b4ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640065"
---
# <a name="how-to-encode-an-asset-by-using-media-encoder-standard"></a>Como codificar um ativo usando o Media Encoder Standard
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
> * [REST](media-services-rest-encode-asset.md)
> * [Portal](media-services-portal-encode.md)
>
>

## <a name="overview"></a>Visão geral

Para fornecer vídeo digital pela Internet, é necessário compactar a mídia. Os arquivos de vídeo digital são muito grandes e podem ser muito grandes para serem fornecidos pela Internet ou exibidos corretamente nos dispositivos dos clientes. A codificação é o processo de compactação de vídeo e áudio para que seus clientes possam exibir sua mídia.

Os trabalhos de codificação são uma das operações de processamento mais comuns nos Serviços de Mídia do Azure. Você cria trabalhos de codificação para converter arquivos de mídia de uma codificação para outra. Ao codificar, é possível usar o codificador interno dos Serviços de Mídia (Codificador de Mídia Padrão). Você também pode usar um codificador fornecido por um parceiro dos Serviços de Mídia. Codificadores de terceiros estão disponíveis por meio do Azure Marketplace. Você pode especificar os detalhes das tarefas de codificação usando cadeias de caracteres predefinidas para seu codificador ou usando arquivos de configuração predefinidos. Para ver os tipos de predefinições disponíveis, confira [Predefinições de tarefa para o Media Encoder Standard](https://msdn.microsoft.com/library/mt269960).

Cada trabalho pode ter uma ou mais tarefas, dependendo do tipo de processamento que você deseja realizar. Por meio da API REST, é possível criar trabalhos e as tarefas relacionadas de uma destas duas maneiras:

* As tarefas podem ser definidas de forma embutida por meio da propriedade de navegação das Tarefas nas entidades de Trabalho.
* Use o processamento em lotes OData.

Recomendamos sempre codificar os arquivos de origem em um conjunto de MP4 de taxa de bits adaptável e, em seguida, converter o conjunto no formato desejado usando o [empacotamento dinâmico](media-services-dynamic-packaging-overview.md).

Se o ativo de saída tiver o armazenamento criptografado, você deverá configurar a política de entrega de ativos. Para obter mais informações, consulte [Configurando a política de entrega de ativos](media-services-rest-configure-asset-delivery-policy.md).

## <a name="considerations"></a>Considerações

Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).

Antes de começar a fazer referência a processadores de mídia, verifique se você tem a ID do processador de mídia correta. Para obter mais informações, consulte [Obter processadores de mídia](media-services-rest-get-media-processor.md).

## <a name="connect-to-media-services"></a>Conectar-se aos Serviços de Mídia

Para saber mais sobre como conectar-se à API do AMS, veja [Acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="create-a-job-with-a-single-encoding-task"></a>Criar um trabalho com uma única tarefa de codificação

> [!NOTE]
> Ao trabalhar com a API REST dos Serviços de Mídia, as seguintes considerações se aplicam:
>
> Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).
>
> Quando usar JSON e especificar o uso da palavra-chave **Metadata** na solicitação (por exemplo, para referenciar um objeto vinculado), você deve definir o cabeçalho **Accept** para o [ formato JSON Detalhado](https://www.odata.org/documentation/odata-version-3-0/json-verbose-format/): Accept: application/json;odata=verbose.
>
>

O exemplo a seguir mostra como criar e postar um trabalho com uma tarefa definida para codificar um vídeo em uma resolução e qualidade específicas. Quando estiver codificando com o Media Encoder Standard, você poderá usar as predefinições de configuração de tarefa especificadas [aqui](https://msdn.microsoft.com/library/mt269960).

Solicitação:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
        Authorization: Bearer <ENCODED JWT TOKEN> 
        x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
        Host: media.windows.net

    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "Adaptive Streaming", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",  "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Resposta:

    HTTP/1.1 201 Created

    . . .

### <a name="set-the-output-assets-name"></a>Definir nome do ativo de saída
O exemplo a seguir mostra como definir o atributo assetName:

    { "TaskBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"}

## <a name="considerations"></a>Considerações
* As propriedades TaskBody devem usar o XML literal para definir o número de entradas ou os ativos de saída que são usados pela tarefa. O artigo da tarefa contém a Definição de Esquema XML para o XML.
* Na definição de TaskBody, cada valor interno para `<inputAsset>` e `<outputAsset>` deve ser definido como JobInputAsset(value) ou JobOutputAsset(value).
* Uma tarefa pode ter vários ativos de saída. Um JobOutputAsset(x) só pode ser usado uma vez como uma saída de uma tarefa em um trabalho.
* Você pode especificar JobInputAsset ou JobOutputAsset como um ativo de entrada de uma tarefa.
* As tarefas não devem formar um ciclo.
* O parâmetro de valor passado para JobInputAsset ou JobOutputAsset representa o valor de índice de um ativo. Os ativos reais são definidos nas propriedades de navegação InputMediaAssets e OutputMediaAssets na definição de entidade do trabalho.
* Como os Serviços de Mídia se baseiam no OData v3, os ativos individuais nas coleções de propriedades de navegação InputMediaAssets e OutputMediaAssets são referenciados por meio de um par nome-valor “__metadata: uri”.
* InputMediaAssets são mapeados para um ou mais ativos criados nos Serviços de Mídia. Os OutputMediaAssets são criados pelo sistema. Eles não referenciam um ativo existente.
* OutputMediaAssets podem ser nomeados usando o atributo assetName. Se esse atributo não estiver presente, então o nome do OutputMediaAsset será tudo o que é o valor de texto interno do elemento `<outputAsset>` com um sufixo do valor do nome do trabalho ou o valor da ID de trabalho (no caso em que a propriedade Nome não esteja definida). Por exemplo, se você definir um valor para assetName como “Sample”, a propriedade Name de OutputMediaAsset deverá ser definida como “Sample”. No entanto, se você não definiu um valor para assetName, mas definiu o nome do trabalho como “NewJob”, o Nome do OutputMediaAsset poderá ser “JobOutputAsset(value)_NewJob”.

## <a name="create-a-job-with-chained-tasks"></a>Criar um trabalho com tarefas encadeadas
Em muitos cenários de aplicativo, os desenvolvedores querem criar uma série de tarefas de processamento. Nos serviços de mídia, você pode criar uma série de tarefas encadeadas. Cada tarefa executa etapas de processamento diferentes e pode usar diferentes processadores de mídia. As tarefas encadeadas podem entregar um ativo de uma tarefa para outra, desempenhando uma sequência linear de tarefas no ativo. No entanto, as tarefas executadas em um trabalho não precisam estar em uma sequência. Quando você cria uma tarefa encadeada, os objetos **ITask** são criados em um único objeto **IJob**.

> [!NOTE]
> Atualmente, há um limite de 30 tarefas por trabalho. Se você precisa encadear mais de 30 tarefas, crie mais de um trabalho para conter as tarefas.
>
>

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          },
          {  
             "Configuration":"H264 Smooth Streaming 720p",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-16\"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
          }
       ]
    }


### <a name="considerations"></a>Considerações
Para habilitar o encadeamento de tarefas:

* Um trabalho deve ter, pelo menos, duas tarefas.
* Deve haver, pelo menos, uma tarefa cuja entrada é a saída de outra tarefa no trabalho.

## <a name="use-odata-batch-processing"></a>Usar o processamento em lotes OData
O exemplo a seguir mostra como usar o processamento em lotes OData para criar um trabalho e tarefas. Para obter informações sobre o processamento em lotes, consulte [Processamento em lote do protocolo OData (Open Data)](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

    POST https://media.windows.net/api/$batch HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: multipart/mixed; boundary=batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Accept: multipart/mixed
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
    Host: media.windows.net


    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e
    Content-Type: multipart/mixed; boundary=changeset_122fb0a4-cd80-4958-820f-346309967e4d

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/Jobs HTTP/1.1
    Content-ID: 1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {"Name" : "NewTestJob", "InputMediaAssets@odata.bind":["https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A2a22445d-1500-80c6-4b34-f1e5190d33c6')"]}

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d
    Content-Type: application/http
    Content-Transfer-Encoding: binary

    POST https://media.windows.net/api/$1/Tasks HTTP/1.1
    Content-ID: 2
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

    {  
       "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
       "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
       "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"Custom output name\">JobOutputAsset(0)</outputAsset></taskBody>"
    }

    --changeset_122fb0a4-cd80-4958-820f-346309967e4d--
    --batch_a01a5ec4-ba0f-4536-84b5-66c5a5a6d34e--



## <a name="create-a-job-by-using-a-jobtemplate"></a>Criar um trabalho usando um JobTemplate
Ao processar vários ativos usando um conjunto comum de tarefas, use um JobTemplate para especificar as predefinições de tarefa padrão ou para definir a ordem das tarefas.

O exemplo a seguir mostra como criar um JobTemplate com um TaskTemplate definido de forma embutida. O TaskTemplate usa o Media Encoder Standard como o MediaProcessor para codificar o arquivo de ativo. No entanto, outros MediaProcessors também podem ser usados.

    POST https://media.windows.net/API/JobTemplates HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version=\"1.0\" encoding=\"utf-8\"?><jobTemplate><taskBody taskTemplateId=\"nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789\"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }


> [!NOTE]
> Ao contrário de outras entidades de Serviços de Mídia, você deve definir um novo identificador GUID para cada TaskTemplate e colocá-lo na propriedade taskTemplateId e na propriedade ID no corpo da solicitação. O esquema de identificação de conteúdo deve seguir o esquema descrito em Identificar Entidades de Serviços de Mídia do Azure. Além disso, os JobTemplates não podem ser atualizados. Em vez disso, você deve criar um novo com as suas alterações atualizadas.
>
>

Se for bem-sucedido, será retornada a seguinte resposta:

    HTTP/1.1 201 Created

    . . .


O seguinte exemplo mostra como criar um trabalho que referencia uma ID de JobTemplate:

    POST https://media.windows.net/API/Jobs HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN> 
    Host: media.windows.net


    {"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}


Se for bem-sucedido, será retornada a seguinte resposta:

    HTTP/1.1 201 Created

    . . .


## <a name="advanced-encoding-features-to-explore"></a>Recursos de Codificação Avançada para explorar
* [Como gerar miniaturas](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Gerar miniaturas durante a codificação](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Cortar vídeos durante a codificação](media-services-crop-video.md)
* [Personalizar as predefinições de codificação](media-services-custom-mes-presets-with-dotnet.md)
* [Sobrepor ou colocar uma imagem de marca d'água em um vídeo](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você sabe como criar um trabalho para codificar um ativo, consulte [Como verificar o andamento do trabalho com os Serviços de Mídia](media-services-rest-check-job-progress.md).

## <a name="see-also"></a>Consulte também
[Obter processadores de mídia](media-services-rest-get-media-processor.md)
