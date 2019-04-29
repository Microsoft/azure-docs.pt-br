---
title: Introdução ao fornecimento de conteúdo sob demanda usando a REST | Microsoft Docs
description: Este tutorial orienta você pelas etapas de implementação de um aplicativo de fornecimento de conteúdo sob demanda com os Serviços de Mídia do Azure usando API REST.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 88194b59-e479-43ac-b179-af4f295e3780
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: fea9bae9fadc20622a6ca3d2e08db9cd3a92c800
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60639387"
---
# <a name="get-started-with-delivering-content-on-demand-using-rest"></a>Introdução ao fornecimento de conteúdo sob demanda usando a REST  

[!INCLUDE [media-services-selector-get-started](../../../includes/media-services-selector-get-started.md)]

Este início rápido orienta você pelas etapas de implementação de um aplicativo de entrega de conteúdo de vídeo sob demanda (VoD) com as APIs REST dos Serviços de Mídia do Azure (AMS).

O tutorial apresenta o fluxo de trabalho básico dos Serviços de Mídia e os objetos e as tarefas de programação mais comuns necessárias para o desenvolvimento dos Serviços de Mídia do Microsoft Azure. No final do tutorial, será possível transmitir ou fazer o download progressivamente de um arquivo de mídia de exemplo que você carregou, codificou e baixou.

A imagem a seguir mostra alguns dos objetos mais usados ao desenvolver aplicativos VoD em relação ao modelo de OData de Serviços de Mídia.

Clique na imagem para exibi-la em tamanho normal.  

<a href="./media/media-services-rest-get-started/media-services-overview-object-model.png" target="_blank"><img src="./media/media-services-rest-get-started/media-services-overview-object-model-small.png"></a> 

## <a name="prerequisites"></a>Pré-requisitos
Os seguintes pré-requisitos são necessários para começar a desenvolver com os serviços de mídia com APIs REST.

* Uma conta do Azure. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* Uma conta dos Serviços de Mídia. Para criar uma conta de Serviços de Mídia, consulte [Como criar uma conta de Serviços de Mídia](media-services-portal-create-account.md).
* Noções básicas sobre como desenvolver com API REST dos serviços de mídia. Para saber mais, consulte [Visão Geral da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).
* Um aplicativo de sua escolha que pode enviar solicitações e respostas HTTP. Este tutorial usa o [Fiddler](https://www.telerik.com/download/fiddler).

As tarefas a seguir são mostradas neste guia de início rápido.

1. Iniciar pontos de extremidade de streaming (usando o Portal do Azure).
2. Conectar à conta de serviços de mídia com a API REST.
3. Criar um novo ativo e carregar um arquivo de vídeo com a API REST.
4. Codificar o arquivo de origem em um conjunto de arquivos MP4 com taxa de bits adaptável com a API REST.
5. Publicar o ativo e obter URLs de download progressivo e streaming com API REST.
6. Reproduzir o conteúdo.

>[!NOTE]
>Há um limite de 1.000.000 políticas para diferentes políticas de AMS (por exemplo, para política de Localizador ou ContentKeyAuthorizationPolicy). Use a mesma ID de política, se estiver sempre usando os mesmos dias/permissões de acesso, por exemplo, políticas de localizadores que devem permanecer no local por um longo período (políticas de não upload). Para saber mais, confira [este artigo](media-services-dotnet-manage-entities.md#limit-access-policies).

Para obter detalhes sobre as entidades do REST do AMS usadas neste artigo, consulte [Referência de API REST dos Serviços de Mídia do Azure](https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference). Além disso, consulte [Conceitos dos Serviços de Mídia do Azure](media-services-concepts.md).

>[!NOTE]
>Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md).

## <a name="start-streaming-endpoints-using-the-azure-portal"></a>Iniciar pontos de extremidade de streaming usando o portal do Azure

Ao trabalhar com os Serviços de Mídia do Azure, um dos cenários mais comuns é o fornecimento de vídeo via streaming de taxa de bits adaptável. Os Serviços de Mídia fornecem um empacotamento dinâmico que permite a você enviar o conteúdo codificado para MP4 da taxa de bits adaptável nos formatos de transmissão suportados pelos Serviços de Mídia (MPEG DASH, HLS, Smooth Streaming) just-in-time, sem ter que armazenar as versões recolocadas de cada um dos formatos de transmissão.

>[!NOTE]
>Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado **Executando**.

Para iniciar o ponto de extremidade de streaming, faça o seguinte:

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Na janela Configurações, clique em Pontos de extremidade de streaming.
3. Clique no ponto de extremidade de streaming padrão.

    A janela DETALHES DO PONTO DE EXTREMIDADE DE STREAMING PADRÃO é exibida.

4. Clique no ícone Iniciar.
5. Clique no botão Salvar para salvar as alterações.

## <a id="connect"></a>Conectar-se à conta de Serviços de Mídia com a API REST

Para saber mais sobre como conectar-se à API do AMS, veja [Acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a id="upload"></a>Criar um novo ativo e carregar um arquivo de vídeo com a API REST

Nos serviços de mídia, você pode carregar seus arquivos digitais em um ativo. A entidade **Asset** pode conter vídeo, áudio, imagens, coleções de miniaturas, sequências de texto e arquivos de legendas (e os metadados sobre esses arquivos).  Depois que os arquivos são carregados no ativo, o conteúdo é armazenado com segurança na nuvem para processamento e transmissão adicionais.

Um dos valores que você precisa fornecer ao criar um ativo é opções de criação do ativo. A propriedade **Options** é um valor de enumeração que descreve as opções de criptografia em que um ativo pode ser criado. Um valor válido é um dos valores na lista abaixo, não uma combinação de valores desta lista:

* **Nenhuma** = **0** - nenhuma criptografia é usada. Ao usar essa opção, seu conteúdo não é protegido quando está em trânsito ou em repouso no armazenamento.
    Se você pretende enviar um MP4 usando o download progressivo, use essa opção.
* **StorageEncrypted** = **1** - criptografa o conteúdo limpo localmente usando a criptografia AES de 256 bits e, em seguida, carrega-o para o armazenamento do Azure, onde ele é armazenado, criptografado em rest. Ativos protegidos pela criptografia de armazenamento são descriptografados automaticamente e posicionados em um sistema de arquivos criptografado antes da codificação, então opcionalmente criptografados novamente antes do carregamento como um novo ativo de saída. O caso de uso primário para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada de alta qualidade com criptografia forte em repouso no disco.
* **CommonEncryptionProtected** = **2** — use esta opção se você estiver carregando conteúdo que já foi criptografado e protegido com criptografia comum ou DRM PlayReady (por exemplo, Smooth Streaming protegido com DRM PlayReady).
* **EnvelopeEncryptionProtected** = **4** – use esta opção se você estiver carregando HLS criptografado com AES. Os arquivos devem ter sido codificados e criptografados pelo Gerenciador de Transformação.

### <a name="create-an-asset"></a>Criar um ativo
Um ativo é um contêiner para múltiplos tipos ou conjuntos de objetos nos serviços de mídia, incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda codificada. Na API REST, criar um ativo requer enviar solicitação POST para serviços de mídia e colocar qualquer informação de propriedade sobre seus ativos no corpo da solicitação.

O exemplo a seguir mostra como criar um ativo.

**Solicitação HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 45

    {"Name":"BigBuckBunny.mp4", "Options":"0"}


**Resposta HTTP**

Se for bem-sucedido, será retornado o seguinte:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 452
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
    request-id: e98be122-ae09-473a-8072-0ccd234a0657
    x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny2.mp4",
       "Options":0,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

### <a name="create-an-assetfile"></a>Criar um AssetFile
A entidade [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) representa um arquivo de áudio ou vídeo que é armazenado em um contêiner de blob. Um arquivo de ativo está sempre associado a um ativo, e um ativo pode conter um ou vários AssetFiles. A tarefa do Codificador dos serviços de mídia falha se um objeto de arquivo de ativo não estiver associado um arquivo digital em um contêiner de blob.

Depois de carregar o arquivo de mídia digital em um contêiner de blob, você usará a solicitação **MESCLAR** HTTP para atualizar o AssetFile com as informações sobre o arquivo de mídia (como mostrado posteriormente no tópico).

**Solicitação HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 164

    {  
       "IsEncrypted":"false",
       "IsPrimary":"false",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Resposta HTTP**

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 535
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
    Server: Microsoft-IIS/8.5
    request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 00:34:07 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "Name":"BigBuckBunny.mp4",
       "ContentFileSize":"0",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "EncryptionVersion":null,
       "EncryptionScheme":null,
       "IsEncrypted":false,
       "EncryptionKeyId":null,
       "InitializationVector":null,
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }


### <a name="creating-the-accesspolicy-with-write-permission"></a>Criando o AccessPolicy com permissão de gravação
Antes de carregar todos os arquivos no armazenamento de blobs, defina os direitos de política de acesso para gravar em um ativo. Para fazer isso, POSTE uma solicitação HTTP para o conjunto de entidade AccessPolicies. Defina um valor de DurationInMinutes durante a criação ou você receberá uma mensagem de erro de servidor interno 500 em resposta. Para saber mais sobre AccessPolicies, consulte [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

O exemplo a seguir mostra como criar um AccessPolicy:

**Solicitação HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 74

    {"Name":"NewUploadPolicy", "DurationInMinutes":"440", "Permissions":"2"}

**Resposta HTTP**

Se for bem-sucedido, será retornada a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 312
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae')
    Server: Microsoft-IIS/8.5
    request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    x-ms-request-id: 74c74545-7e0a-4cd6-a440-c1c48074a970
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:18:06 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#AccessPolicies/@Element",
       "Id":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "Created":"2015-01-18T22:18:06.6370575Z",
       "LastModified":"2015-01-18T22:18:06.6370575Z",
       "Name":"NewUploadPolicy",
       "DurationInMinutes":440.0,
       "Permissions":2
    }

### <a name="get-the-upload-url"></a>Obter a URL de carregamento

Para receber a URL de carregamento real, crie um localizador de SAS. Os localizadores definem a hora de início e o tipo de ponto de extremidade de conexão para clientes que desejam acessar arquivos em um ativo. Você pode criar várias entidades de localizador para um determinado par de AccessPolicy e ativos para manipular solicitações e necessidades de clientes diferentes. Cada um destes localizadores usa o valor StartTime mais o valor de DurationInMinutes do AccessPolicy para determinar quanto tempo uma URL pode ser usada. Para saber mais, consulte [Localizador](https://docs.microsoft.com/rest/api/media/operations/locator).

Uma URL SAS tem o seguinte formato:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

Algumas considerações se aplicam:

* Você não pode ter mais do que cinco localizadores exclusivos associados a um determinado ativo ao mesmo tempo. 
* Se você precisar carregar os arquivos imediatamente, você deve definir o valor StartTime como cinco minutos antes da hora atual. Isso ocorre porque pode haver uma defasagem horária entre o computador do cliente e os serviços de mídia. Além disso, seu valor de StartTime deve estar no seguinte formato de data e hora: AAAA-MM-DDTHH:mm:ssZ (por exemplo, “2014-05-23T17:53:50Z”).    
* Pode haver um 30 a 40 segundos de atraso após a criação de um localizador quando ele está disponível para uso. Esse problema se aplica a [SAS URL](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) e localizadores de origem.

O exemplo a seguir mostra como criar um localizador URL SAS, conforme definido pela propriedade Type no corpo da solicitação ("1" para um localizador SAS e "2" para um localizador de origem sob demanda). A propriedade **Path** retornada contém a URL que você deve usar para carregar seu arquivo.

**Solicitação HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Locators HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 178

    {  
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Type":1
    }


**Resposta HTTP**

Se for bem-sucedido, será retornada a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 949
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54')
    Server: Microsoft-IIS/8.5
    request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    x-ms-request-id: 2adeb1f8-89c5-4cc8-aa4f-08cdfef33ae0
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 03:01:29 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Locators/@Element",
       "Id":"nb:lid:UUID:af57bdd8-6751-4e84-b403-f3c140444b54",
       "ExpirationDateTime":"2015-02-19T00:05:53",
       "Type":1,
       "Path":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "BaseUri":"https://storagetestaccount001.blob.core.windows.net/asset-f438649c-313c-46e2-8d68-7d2550288247",
       "ContentAccessComponent":"?sv=2012-02-12&sr=c&si=af57bdd8-6751-4e84-b403-f3c140444b54&sig=fE4btwEfZtVQFeC0Wh3Kwks2OFPQfzl5qTMW5YytiuY%3D&st=2015-02-18T16%3A45%3A53Z&se=2015-02-19T00%3A05%3A53Z",
       "AccessPolicyId":"nb:pid:UUID:be0ac48d-af7d-4877-9d60-1805d68bffae",
       "AssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StartTime":"2015-02-18T16:45:53",
       "Name":null
    }

### <a name="upload-a-file-into-a-blob-storage-container"></a>Carregar um arquivo em um contêiner de armazenamento de blobs
Depois de definir AccessPolicy e Localizador, o arquivo real é carregado em um contêiner de armazenamento de blobs do Azure usando as APIs REST do Armazenamento do Azure. Você deve carregar os arquivos como blobs de blocos. Os blobs de páginas não são compatíveis com os Serviços de Mídia do Azure.  

> [!NOTE]
> Você deve adicionar o nome do arquivo para o arquivo que você deseja carregar no valor **Path** do Localizador recebido na seção anterior. Por exemplo, `https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4?`.
>
>

Para saber mais sobre como trabalhar com blobs de armazenamento do Azure, consulte [API REST do serviço Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

### <a name="update-the-assetfile"></a>Atualizar o AssetFile
Agora que você carregou o arquivo, atualize as informações de tamanho do FileAsset (e outros). Por exemplo: 

    MERGE https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5') HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

    {  
       "ContentFileSize":"1186540",
       "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
       "MimeType":"video/mp4",
       "Name":"BigBuckBunny.mp4",
       "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
    }


**Resposta HTTP**

Se for bem-sucedido, será retornado o seguinte:

    HTTP/1.1 204 No Content
    ...

## <a name="delete-the-locator-and-accesspolicy"></a>Excluir o AccessPolicy e localizador
**Solicitação HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/Locators('nb%3Alid%3AUUID%3Aaf57bdd8-6751-4e84-b403-f3c140444b54') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Resposta HTTP**

Se for bem-sucedido, será retornado o seguinte:

    HTTP/1.1 204 No Content
    ...

**Solicitação HTTP**

    DELETE https://wamsbayclus001rest-hs.cloudapp.net/api/AccessPolicies('nb%3Apid%3AUUID%3Abe0ac48d-af7d-4877-9d60-1805d68bffae') HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net

**Resposta HTTP**

Se for bem-sucedido, será retornado o seguinte:

    HTTP/1.1 204 No Content
    ...

## <a id="encode"></a>Codificar o arquivo de origem em um conjunto de arquivos MP4 com taxa de bits adaptável

Após a inserção de Ativos nos Serviços de Mídia, a mídia poderá ser codificada, transmultiplexada, marcada com marca d'água e assim por diante, antes que seja entregue aos clientes. Essas atividades são agendadas e executadas em contraste com várias instâncias de função de plano de fundo para garantir a disponibilidade e desempenho elevados. Essas atividades são chamadas de Trabalhos, e cada Trabalho é composto por Tarefas atômicas, que fazem o trabalho real no arquivo do Ativo. (Para saber mais, consulte as descrições de [Trabalho](https://docs.microsoft.com/rest/api/media/operations/job), [Tarefa](https://docs.microsoft.com/rest/api/media/operations/task)).

Como foi mencionado anteriormente, ao trabalhar com os Serviços de Mídia do Azure, um dos cenários mais comuns é fornecer streaming com uma taxa de bits adaptável aos clientes dos Serviços de Mídia do Azure. Os Serviços de Mídia podem empacotar dinamicamente um conjunto de arquivos MP4 com taxa de bits adaptável em um dos seguintes formatos: HLS (HTTP Live Streaming), Smooth Streaming e MPEG-DASH.

A seção a seguir mostra como criar um trabalho que contém uma tarefa de codificação. A tarefa especifica a transcodificação do arquivo de mezanino em um conjunto de MP4s de taxa de bits adaptável usando o **Codificador de Mídia Padrão**. A seção também mostra como monitorar o progresso de processamento de trabalho. Quando o trabalho for concluído, você poderá criar localizadores, que serão necessários para acessar seus ativos.

### <a name="get-a-media-processor"></a>Obter um processador de mídia
Nos Serviços de Mídia, um processador de mídia é um componente que manipula uma tarefa de processamento específica, como codificação, conversão de formato, criptografia ou descriptografia de conteúdo de mídia. Para a tarefa de codificação mostrada neste tutorial, usaremos o Codificador de Mídia Padrão.

O código a seguir solicita a ID do codificador.

**Solicitação HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/MediaProcessors()?$filter=Name%20eq%20'Media%20Encoder%20Standard' HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Resposta HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 273
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    x-ms-request-id: 6beb04b4-55a7-480d-8aa8-e5c5d59ffa1f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 07:54:09 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#MediaProcessors",
       "value":[  
          {  
             "Id":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "Description":"Media Encoder Standard",
             "Name":"Media Encoder Standard",
             "Sku":"",
             "Vendor":"Microsoft",
             "Version":"1.1"
          }
       ]
    }

### <a name="create-a-job"></a>Criar um trabalho
Cada trabalho pode ter uma ou mais tarefas dependendo do tipo de processamento que você deseja realizar. Por meio da API REST, você pode criar trabalhos e as tarefas relacionadas em uma das duas maneiras: As tarefas podem ser definidas embutidas por meio da propriedade de navegação de tarefas nas entidades de trabalho, ou através do processamento em lotes do OData. O SDK dos Serviços de Mídia usa o processamento em lotes. No entanto, para fins de legibilidade dos exemplos de código neste artigo, as tarefas serão definidas em linha. Para obter informações sobre o processamento em lotes, consulte [Processamento em lote do protocolo OData (Open Data)](https://www.odata.org/documentation/odata-version-3-0/batch-processing/).

O exemplo a seguir mostra como criar e publicar um trabalho com uma tarefa definida para codificar um vídeo em uma determinada resolução e qualidade. A seção de documentação a seguir contém a lista de todas as [predefinições de tarefa](https://msdn.microsoft.com/library/mt269960) compatíveis com o processador do Codificador de Mídia Padrão.  

**Solicitação HTTP**

    POST https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net
    Content-Length: 482

    {  
       "Name":"NewTestJob",
       "InputMediaAssets":[  
          {  
             "__metadata":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')"
             }
          }
       ],
       "Tasks":[  
          {  
             "Configuration":"Adaptive Streaming",
             "MediaProcessorId":"nb:mpid:UUID:ff4df607-d419-42f0-bc17-a481b1331e56",
             "TaskBody":"<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset>
                <outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
          }
       ]
    }

**Resposta HTTP**

Se for bem-sucedido, será retornada a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1215
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')
    Server: Microsoft-IIS/8.5
    request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    x-ms-request-id: 532ac1ec-a475-4dce-b2d5-7c8ce94ac87c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:04:35 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Job"
          },
          "Tasks":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Tasks"
             }
          },
          "OutputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets"
             }
          },
          "InputMediaAssets":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')/InputMediaAssets"
             }
          },
          "Id":"nb:jid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "Name":"NewTestJob",
          "Created":"2015-01-19T08:04:34.3287057Z",
          "LastModified":"2015-01-19T08:04:34.3287057Z",
          "EndTime":null,
          "Priority":0,
          "RunningDuration":0,
          "StartTime":null,
          "State":0,
          "TemplateId":null,
          "JobNotificationSubscriptions":{  
             "__metadata":{  
                "type":"Collection(Microsoft.Cloud.Media.Vod.Rest.Data.Models.JobNotificationSubscription)"
             },
             "results":[  

             ]
          }
       }
    }


Há algumas coisas importantes a observar em qualquer solicitação de trabalho:

* As propriedades TaskBody DEVEM usar XML literal para definir o número de entradas ou os ativos de saída que serão usados pela Tarefa. O artigo da tarefa contém a Definição de Esquema XML para o XML.
* Na definição de TaskBody, cada valor interno para `<inputAsset>` e `<outputAsset>` deve ser definido como JobInputAsset(value) ou JobOutputAsset(value).
* Uma tarefa pode ter vários ativos de saída. Um JobOutputAsset(x) só pode ser usado uma vez como uma saída de uma tarefa em um trabalho.
* Você pode especificar JobInputAsset ou JobOutputAsset como um ativo de entrada de uma tarefa.
* As tarefas não devem formar um ciclo.
* O parâmetro de valor passado para JobInputAsset ou JobOutputAsset representa o valor de índice para um ativo. Os ativos reais são definidos nas propriedades de navegação InputMediaAssets e OutputMediaAssets na definição de entidade de tarefa.

> [!NOTE]
> Como os serviços de mídia são baseados no OData v3, os ativos individuais nas coleções de propriedade de navegação InputMediaAssets e OutputMediaAssets são referenciados por meio de um par nome-valor "__metadata : uri".
>
>

* Os InputMediaAssets mapeiam para um ou mais ativos que você criou nos serviços de mídia. Os OutputMediaAssets são criados pelo sistema. Eles não fazem referência a um ativo existente.
* Os OutputMediaAssets podem ser nomeados usando o atributo assetName. Se esse atributo não estiver presente, então o nome do OutputMediaAsset será tudo o que é o valor de texto interno do elemento `<outputAsset>` com um sufixo do valor do nome do trabalho ou o valor da ID de trabalho (no caso em que a propriedade Nome não esteja definida). Por exemplo, se você definir um valor de assetName como "Amostra", a propriedade Nome de OutputMediaAsset deve ser definida como "Amostra". No entanto, se você não definiu um valor para assetName, mas definiu o nome do trabalho como "NewJob", o nome do OutputMediaAsset poderia ser "JobOutputAsset(value)_NewJob".

    O exemplo a seguir mostra como definir o atributo assetName:

        "<?xml version=\"1.0\" encoding=\"utf-8\"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName=\"CustomOutputAssetName\">JobOutputAsset(0)</outputAsset></taskBody>"
* Para habilitar o encadeamento de tarefas:

  * Um trabalho deve ter, pelo menos, duas tarefas
  * Deve haver pelo menos uma tarefa cujas entradas são a saída de outra tarefa no trabalho.

Para saber mais, consulte [Criar um trabalho de codificação com a API REST dos Serviços de Mídia.](media-services-rest-encode-asset.md)

### <a name="monitor-processing-progress"></a>Monitorar o progresso de processamento
Você pode recuperar o status do trabalho, usando a propriedade State, conforme mostrado no exemplo a seguir:

**Solicitação HTTP**

    GET https://wamsbayclus001rest-hs.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/State HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 0


**Resposta HTTP**

Se for bem-sucedido, será retornada a seguinte resposta:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 17
    Content-Type: application/json;odata=verbose;charset=utf-8
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 01324d81-18e2-4493-a3e5-c6186209f0c8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Sun, 13 May 2012 05:16:53 GMT

    {"d":{"State":2}}


### <a name="cancel-a-job"></a>Cancelar um trabalho
Os serviços de mídia permitem cancelar trabalhos em execução com a função CancelJob. Esta chamada retornará um código de erro 400 se você tentar cancelar um Trabalho quando seu estado estiver definido como Cancelado, Cancelando, Erro ou Concluído.

O exemplo a seguir mostra como chamar a função CancelJob.

**Solicitação HTTP**

    GET https://wamsbayclus001rest-hs.net/API/CancelJob?jobid='nb%3ajid%3aUUID%3a71d2dd33-efdf-ec43-8ea1-136a110bd42c' HTTP/1.1
    Content-Type: application/json;odata=verbose
    Accept: application/json;odata=verbose
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.2
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net


Se tiver êxito, uma resposta de código 204 é retornada sem corpo de mensagem.

> [!NOTE]
> Você deve codificar a URL da ID do trabalho (normalmente nb:jid:UUID: algumvalor) ao passá-la como um parâmetro para CancelJob.
>
>

### <a name="get-the-output-asset"></a>Obtenha o ativo de saída
O código a seguir mostra como solicitar a ID do ativo de saída.

**Solicitação HTTP**

    GET https://wamsbayclus001rest-hs.cloudapp.net/api/Jobs('nb%3Ajid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/OutputMediaAssets() HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: wamsbayclus001rest-hs.cloudapp.net


**Resposta HTTP**

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 445
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    x-ms-request-id: 73cd605d-066a-46f1-8358-f4bd25a9220a
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Mon, 19 Jan 2015 08:28:13 GMT

    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets",
       "value":[  
          {  
             "Id":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "State":0,
             "Created":"2015-01-19T07:52:15.603",
             "LastModified":"2015-01-19T07:52:15.603",
             "AlternateId":null,
             "Name":"Multibitrate MP4s",
             "Options":0,
             "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c",
             "StorageAccountName":"storagetestaccount001"
          }
       ]
    }

## <a id="publish_get_urls"></a>Publicar o ativo e obter URLs de download progressivo e streaming com API REST

Para transmitir ou baixar um ativo, primeiro você precisa "publicá-lo" criando um localizador. Os localizadores fornecem acesso aos arquivos contidos no ativo. Os Serviços de Mídia oferecem suporte a dois tipos de localizadores: OnDemandOrigin, usado para transmitir mídia por streaming (por exemplo, MPEG DASH, HLS ou Smooth Streaming) e localizadores de Assinatura de Acesso (SAS), usados para fazer o download de arquivos de mídia. 

Depois de criar os localizadores, você pode criar as URLs usadas para transmitir ou baixar os arquivos.

>[!NOTE]
>Quando sua conta AMS é criada, um ponto de extremidade de streaming **padrão** é adicionado à sua conta em estado **Parado**. Para iniciar seu conteúdo de streaming e tirar proveito do empacotamento dinâmico e da criptografia dinâmica, o ponto de extremidade de streaming do qual você deseja transmitir o conteúdo deve estar em estado **Executando**.

Uma URL de streaming para Smooth Streaming tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Uma URL de streaming para HLS tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Uma URL de streaming para MPEG DASH tem o seguinte formato:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Uma URL SAS usada para baixar arquivos tem o seguinte formato:

    {blob container name}/{asset name}/{file name}/{SAS signature}

Esta seção mostra como executar as seguintes tarefas necessárias para "publicar" seus ativos.  

* Criando o AccessPolicy com permissão de leitura
* Criando uma URL SAS para download de conteúdo
* Criando uma URL de origem para conteúdo de streaming

### <a name="creating-the-accesspolicy-with-read-permission"></a>Criando o AccessPolicy com permissão de leitura
Antes de baixar ou realizar streaming de qualquer conteúdo de mídia primeiro defina um AccessPolicy com permissões de leitura e crie a entidade do localizador apropriada que especifica o tipo de mecanismo de entrega que você deseja habilitar para seus clientes. Para saber mais sobre as propriedades disponíveis, consulte [Propriedades da entidade AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy#accesspolicy_properties).

O exemplo a seguir mostra como especificar o AccessPolicy para permissões de leitura para um determinado ativo.

    POST https://wamsbayclus001rest-hs.net/API/AccessPolicies HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 74
    Expect: 100-continue

    {"Name": "DownloadPolicy", "DurationInMinutes" : "300", "Permissions" : 1}

Se tiver êxito, um código de sucesso 201 é retornado descrevendo a entidade AccessPolicy que você criou. Em seguida, você usará a ID do AccessPolicy com a ID do Ativo que contém o arquivo que você deseja fornecer (como um ativo de saída) para criar a entidade do Localizador.

> [!NOTE]
> Esse fluxo de trabalho básico é o mesmo utilizado para carregar um arquivo ao ingerir um ativo (como foi discutido neste tópico). Além disso, como o carregamento de arquivos, se você (ou seus clientes) precisarem acessar os arquivos imediatamente, defina o valor StartTime para cinco minutos antes da hora atual Essa ação é necessária porque pode haver uma defasagem horária entre o cliente e os serviços de mídia. O valor de StartTime deve estar no seguinte formato de data e hora: AAAA-MM-DDTHH:mm:ssZ (por exemplo, “2014-05-23T17:53:50Z”).
>
>

### <a name="creating-a-sas-url-for-downloading-content"></a>Criando uma URL SAS para download de conteúdo
O código a seguir mostra como obter uma URL que pode ser usada para baixar um arquivo de mídia criado e carregado anteriormente. O AccessPolicy tem o conjunto de permissões de leitura e o caminho do localizador se refere a uma URL de download SAS.

    POST https://wamsbayclus001rest-hs.net/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs.net
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c", "StartTime" : "2014-05-17T16:45:53", "Type":1}

Se for bem-sucedido, será retornada a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 1150
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 8cfd8556-3064-416a-97f2-67d9e35f0911
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:32 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A8e5a821d-2194-4d00-8884-adf979856874')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A71d2dd33-efdf-ec43-8ea1-136a110bd42c')/Asset"
             }
          },
          "Id":"nb:lid:UUID:8e5a821d-2194-4d00-8884-adf979856874",
          "ExpirationDateTime":"\/Date(1337049393000)\/",
          "Type":1,
          "Path":"https://storagetestaccount001.blob.core.windows.net/asset-71d2dd33-efdf-ec43-8ea1-136a110bd42c?st=2012-05-14T21%3A36%3A33Z&se=2012-05-15T02%3A36%3A33Z&sr=c&si=8e5a821d-2194-4d00-8884-adf979856874&sig=y75dViDpC5V8WutrXM%2B%2FGpR3uOtqmlISiNlHU1YUBOg%3D",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:71d2dd33-efdf-ec43-8ea1-136a110bd42c",
          "StartTime":"\/Date(1337031393000)\/"
       }
    }

A propriedade **Path** retornada contém a URL de SAS.

> [!NOTE]
> Se você baixar o conteúdo de armazenamento criptografado, deverá manualmente descriptografá-lo antes de renderizá-lo ou usar o MediaProcessor de descriptografia de armazenamento em uma tarefa de processamento para arquivos processados de saída de modo transparente para um OutputAsset e, em seguida, fazer o download deste ativo. Para saber mais sobre processamento, consulte Criar um trabalho de codificação com a API REST dos serviços de mídia. Além disso, os localizadores URL SAS não podem ser atualizados depois que eles foram criados. Por exemplo, você não pode reutilizar o mesmo localizador com um valor StartTime atualizado. Isso é devido ao modo como as URLs SAS são criadas. Se você quiser acessar um ativo para baixar após um localizador ter expirado, você deve criar um novo com um novo StartTime.
>
>

### <a name="download-files"></a>Baixar arquivos
Depois de definir AccessPolicy e localizador, você pode baixar arquivos usando as APIs de REST do armazenamento do Azure.  

> [!NOTE]
> Você deve adicionar o nome do arquivo para o arquivo que você deseja carregar no valor de **Path** do Localizador recebido na seção anterior. Por exemplo, https://storagetestaccount001.blob.core.windows.net/asset-e7b02da4-5a69-40e7-a8db-e8f4f697aac0/BigBuckBunny.mp4? . . .

Para saber mais sobre como trabalhar com blobs de armazenamento do Azure, consulte [API REST do serviço Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Como resultado do trabalho de codificação que você executou anteriormente (codificação no conjunto de MP4 adaptável), você tem vários arquivos MP4 que pode baixar progressivo. Por exemplo:     

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

### <a name="creating-a-streaming-url-for-streaming-content"></a>Criando uma URL de streaming para conteúdo de streaming
O código a seguir mostra como criar um localizador de URL de streaming:

    POST https://wamsbayclus001rest-hs/API/Locators HTTP/1.1
    Content-Type: application/json
    Accept: application/json
    DataServiceVersion: 3.0
    MaxDataServiceVersion: 3.0
    x-ms-version: 2.17
    Authorization: Bearer <ENCODED JWT TOKEN>
    Host: wamsbayclus001rest-hs
    Content-Length: 182
    Expect: 100-continue

    {"AccessPolicyId": "nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8", "AssetId" : "nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3", "StartTime" : "2014-05-17T16:45:53",, "Type":2}

Se for bem-sucedido, será retornada a seguinte resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 981
    Content-Type: application/json;odata=verbose;charset=utf-8
    Location: https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')
    Server: Microsoft-IIS/7.5
    x-ms-request-id: 2eac4158-fc78-4fa2-81ee-c9f582dc385f
    X-Content-Type-Options: nosniff
    DataServiceVersion: 1.0;
    X-AspNet-Version: 4.0.30319
    X-Powered-By: ASP.NET
    Date: Mon, 14 May 2012 21:41:39 GMT

    {  
       "d":{  
          "__metadata":{  
             "id":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')",
             "type":"Microsoft.Cloud.Media.Vod.Rest.Data.Models.Locator"
          },
          "AccessPolicy":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/AccessPolicy"
             }
          },
          "Asset":{  
             "__deferred":{  
                "uri":"https://wamsbayclus001rest-hs.net/api/Locators('nb%3Alid%3AUUID%3A52034bf6-dfae-4d83-aad3-3bd87dcb1a5d')/Asset"
             }
          },
          "Id":"nb:lid:UUID:52034bf6-dfae-4d83-aad3-3bd87dcb1a5d",
          "ExpirationDateTime":"\/Date(1337049395000)\/",
          "Type":2,
          "Path":"http://wamsbayclus001rest-hs.net/52034bf6-dfae-4d83-aad3-3bd87dcb1a5d/",
          "AccessPolicyId":"nb:pid:UUID:38c71dd0-44c5-4c5f-8418-08bb6fbf7bf8",
          "AssetId":"nb:cid:UUID:eb5540a2-116e-4d36-b084-7e9958f7f3c3",
          "StartTime":"\/Date(1337031395000)\/"
       }
    }

Para transmitir uma URL de origem de Smooth Streaming em um reprodutor de mídia de streaming, você deve acrescentar a propriedade do Caminho com o nome do arquivo manifesto do Smooth Streaming, seguido de "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

Para transmitir HLS, anexe (format=m3u8-aapl) após o "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

Para transmitir MPEG DASH, anexe (format=mpd-time-csf) após o "/manifest".

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)


## <a id="play"></a>Reproduzir o conteúdo
Para o fluxo de vídeo, use [Player dos Serviços de Mídia do Azure](https://amsplayer.azurewebsites.net/azuremediaplayer.html).

Para testar o download progressivo, cole uma URL em um navegador (por exemplo, IE, Chrome, Safari).

## <a name="next-steps-media-services-learning-paths"></a>Próximas etapas: Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
