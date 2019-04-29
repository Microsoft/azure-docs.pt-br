---
title: Carregar arquivos em uma conta dos Serviços de Mídia do Azure usando o REST | Microsoft Docs
description: Saiba como obter o conteúdo de mídia nos serviços de mídia ao criar e carregar ativos.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: f63087d107b9db30e2af6273afde7f51f1c72404
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817613"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Carregar arquivos em uma conta dos Serviços de Mídia usando o REST  
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> * [Portal](media-services-portal-upload-files.md)
> 

Nos serviços de mídia, você pode carregar seus arquivos digitais em um ativo. A entidade [Asset](https://docs.microsoft.com/rest/api/media/operations/asset) pode conter vídeo, áudio, imagens, coleções de miniaturas, sequências de texto e arquivos de legendas (e os metadados sobre esses arquivos).  Depois que os arquivos são carregados no ativo, o conteúdo é armazenado com segurança na nuvem para processamento e transmissão adicionais. 

Neste tutorial, aprenda a carregar um arquivo e outra operação associada a ele:

> [!div class="checklist"]
> * Configurar o Postman para todas as operações de carregamento
> * Conectar-se aos Serviços de Mídia 
> * Criar uma política de acesso com permissão de gravação
> * Criar um ativo
> * Criar um localizador SAS e a URL de carregamento
> * Carregar um arquivo para o armazenamento de blob usando a URL de carregamento
> * Criar um metadados no ativo para o arquivo de mídia carregado

## <a name="prerequisites"></a>Pré-requisitos

- Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- [Crie uma conta dos Serviços de Mídia do Azure usando o Portal do Azure](media-services-portal-create-account.md).
- Verifique o artigo [Acessar a API dos Serviços de Mídia do Azure com a visão geral de autenticação do AAD](media-services-use-aad-auth-to-access-ams-api.md).
- Configurar o **Postman** conforme descrito em [Configurar Postman para chamadas de API de REST dos Serviços de Mídia](media-rest-apis-with-postman.md).

## <a name="considerations"></a>Considerações

As seguintes considerações se aplicam ao usar a API de REST de Serviços de Mídia:
 
* Ao acessar entidades nos Serviços de Mídia do REST API, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md). <br/>A coleção do Postman usada neste tutorial se encarrega da configuração de todos os cabeçalhos necessários.
* Os serviços de mídia usam o valor da propriedade IAssetFile.Name ao construir URLs para o conteúdo de streaming (por exemplo, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) Por esse motivo, não é permitida a codificação por porcentagem. O valor da propriedade **Name** não pode ter quaisquer dos seguintes [caracteres reservados para codificação de percentual](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Além disso, pode haver somente um '.' para a extensão de nome de arquivo.
* O comprimento do nome não deve ser maior do que 260 caracteres.
* Há um limite no tamanho máximo de arquivo com suporte para o processamento nos Serviços de Mídia. Confira [este](media-services-quotas-and-limitations.md) artigo para obter detalhes sobre a limitação de tamanho do arquivo.

## <a name="set-up-postman"></a>Configurar o Postman

Para obter etapas sobre como configurar o Postman para este tutorial, consulte [Configurar o Postman](media-rest-apis-with-postman.md).

## <a name="connect-to-media-services"></a>Conectar-se aos Serviços de Mídia

1. Adicione valores de conexão ao seu ambiente. 

    Algumas variáveis que fazem parte do **ambiente** do [MediaServices](postman-environment.md) precisam ser definidas manualmente antes de iniciar a execução de operações definidas na [coleção](postman-collection.md).

    Para obter valores para as primeiras cinco variáveis, consulte [Acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    ![Carregar um arquivo](./media/media-services-rest-upload-files/postman-import-env.png)
2. Especifique o valor para a variável de ambiente **NomeDoArquivoDeMídia**.

    Especifique o nome do arquivo de mídia que você pretende carregar. Neste exemplo, vamos carregar o arquivo BigBuckBunny.mp4. 
3. Navegue e selecione o arquivo **AzureMediaServices.postman_environment.json**. Você verá que quase todas as operações na coleção executam um script de "teste". Os scripts levam alguns valores retornados pela resposta e definem variáveis de ambiente apropriadas.

    Por exemplo, a primeira operação obtém um token de acesso e define-o na variável de ambiente **AccessToken** que é usada em todas as outras operações.

    ```    
    "listen": "test",
    "script": {
        "type": "text/javascript",
        "exec": [
            "var json = JSON.parse(responseBody);",
            "postman.setEnvironmentVariable(\"AccessToken\", json.access_token);"
        ]
    }
    ```
4. À esquerda da janela do **Postman**, clique em **1. Obtenha o token de autenticação do AAD** -> **Obter Token do Azure AD para Entidade de serviço**.

    A parte da URL é preenchida com a variável de ambiente **AzureADSTSEndpoint** (anteriormente no tutorial, você definiu os valores de variáveis de ambiente que oferecem suporte à coleção).

    ![Carregar um arquivo](./media/media-services-rest-upload-files/postment-get-token.png)

5. Pressione **Enviar**.

    Você pode ver a resposta que contém "access_token". O script de "teste" usa esse valor e define a variável de ambiente do **AccessToken** (como descrito acima). Se você examinar suas variáveis de ambiente, você verá que essa variável agora contém o valor do token de acesso (token de portador) que é usado no restante das operações. 

    Se o token expirar vá novamente para a etapa "Obter Token do Azure AD para Entidade de serviço". 

## <a name="create-an-access-policy-with-write-permission"></a>Criar uma política de acesso com permissão de gravação

### <a name="overview"></a>Visão geral 

>[!NOTE]
>Há um limite de 1.000.000 políticas para diferentes políticas de AMS (por exemplo, para política de Localizador ou ContentKeyAuthorizationPolicy). Use a mesma ID de política, se você estiver sempre usando os mesmos dias/permissões de acesso, por exemplo, políticas de localizadores que devem permanecer no local por um longo período (políticas de não carregamento). Para saber mais, confira [este artigo](media-services-dotnet-manage-entities.md#limit-access-policies).

Antes de carregar todos os arquivos no armazenamento de blobs, defina os direitos de política de acesso para gravar em um ativo. Para fazer isso, POSTE uma solicitação HTTP para o conjunto de entidade AccessPolicies. Defina um valor de DurationInMinutes durante a criação ou você receberá uma mensagem de erro de servidor interno 500 em resposta. Para saber mais sobre AccessPolicies, consulte [AccessPolicy](https://docs.microsoft.com/rest/api/media/operations/accesspolicy).

### <a name="create-an-access-policy"></a>Crie uma política de acesso

1. Selecione **AccessPolicy** -> **Criar AccessPolicy para Carregamento**.
2. Pressione **Enviar**.

    ![Carregar um arquivo](./media/media-services-rest-upload-files/postman-access-policy.png)

    O script de "teste" obtém a Id de AccessPolicy e define a variável de ambiente apropriada.

## <a name="create-an-asset"></a>Criar um ativo

### <a name="overview"></a>Visão geral

Um [ativo](https://docs.microsoft.com/rest/api/media/operations/asset) é um contêiner para vários tipos ou conjuntos de objetos nos Serviços de Mídia, incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda. Na API REST, criar um ativo requer enviar solicitação POST para serviços de mídia e colocar qualquer informação de propriedade sobre seus ativos no corpo da solicitação.

Uma das propriedades que você pode adicionar ao criar um ativo é **Opções**. As opções de criptografia a seguir poderão ser especificadas: **Nenhuma** (padrão, nenhuma criptografia é usada), **CriptografiaDeArmazenamento** (para o conteúdo que foi previamente criptografado com criptografia de armazenamento no lado do cliente), **CriptografiaComumProtegida**, ou **CriptografiaEnvelopeProtegida**. Quando você tiver um ativo criptografado, você precisa configurar uma política de entrega. Para obter mais informações, confira a seção [Configurando as políticas de entrega de ativos](media-services-rest-configure-asset-delivery-policy.md).

Se seu ativo for criptografado, você deve criar um **ContentKey** e vinculá-lo a seu ativo, conforme descrito no seguinte artigo: [Como criar um ContentKey](media-services-rest-create-contentkey.md). Após carregar os arquivos no ativo, você precisa atualizar as propriedades de criptografia na entidade **AssetFile** com os valores obtidos durante a criptografia dos **Ativos**. Faça isso usando a solicitação HTTP **MERGE** . 

Neste exemplo, estamos criando um ativo não criptografado. 

### <a name="create-an-asset"></a>Criar um ativo

1. Selecione **Ativos** -> **Criar ativo**.
2. Pressione **Enviar**.

    ![Carregar um arquivo](./media/media-services-rest-upload-files/postman-create-asset.png)

    O script de "teste" obtém a Id do ativo e define a variável de ambiente apropriada.

## <a name="create-a-sas-locator-and-create-the-upload-url"></a>Crie um localizador SAS e a URL de carregamento.

### <a name="overview"></a>Visão geral

Depois de definir AccessPolicy e localizador, o arquivo real é carregado como um contêiner de armazenamento de blobs do Azure usando as APIs de REST do armazenamento do Azure. Você deve carregar os arquivos como blobs de blocos. Os blobs de páginas não são compatíveis com os Serviços de Mídia do Azure.  

Para saber mais sobre como trabalhar com blobs de armazenamento do Azure, consulte [API REST do serviço Blob](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API).

Para receber a URL de carregamento real, crie um localizador de SAS (mostrado abaixo). Os localizadores definem a hora de início e o tipo de ponto de extremidade de conexão para clientes que desejam acessar arquivos em um ativo. Você pode criar várias entidades de localizador para um determinado par de AccessPolicy e ativos para manipular solicitações e necessidades de clientes diferentes. Cada um destes localizadores usa o valor StartTime mais o valor de DurationInMinutes do AccessPolicy para determinar quanto tempo uma URL pode ser usada. Para saber mais, consulte [Localizador](https://docs.microsoft.com/rest/api/media/operations/locator).

Uma URL SAS tem o seguinte formato:

    {https://myaccount.blob.core.windows.net}/{asset name}/{video file name}?{SAS signature}

### <a name="considerations"></a>Considerações

Algumas considerações se aplicam:

* Você não pode ter mais do que cinco localizadores exclusivos associados a um determinado ativo ao mesmo tempo. Para saber mais, consulte Localizador.
* Se você precisar carregar os arquivos imediatamente, você deve definir o valor StartTime como cinco minutos antes da hora atual. Isso ocorre porque pode haver uma defasagem horária entre o computador do cliente e os serviços de mídia. Além disso, seu valor de StartTime deve estar no seguinte formato de data e hora: AAAA-MM-DDTHH:mm:ssZ (por exemplo, “2014-05-23T17:53:50Z”).    
* Pode haver um 30 a 40 segundos de atraso após a criação de um localizador quando ele está disponível para uso.

### <a name="create-a-sas-locator"></a>Criar um localizador SAS.

1. Selecione **Localizador** -> **Criar localizador SAS**.
2. Pressione **Enviar**.

    O script de "teste" cria a "URL de carregamento" com base no nome do arquivo de mídia especificado as informações de localizador SAS e define a variável de ambiente apropriada.

    ![Carregar um arquivo](./media/media-services-rest-upload-files/postman-create-sas-locator.png)

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Carregar um arquivo para o armazenamento de blob usando a URL de carregamento

### <a name="overview"></a>Visão geral

Agora que você tem a URL de carregamento, você precisa gravar um código usando as APIs de blob do Azure diretamente para carregar seu arquivo para o contêiner SAS. Para obter mais informações, consulte os seguintes artigos:

- [Usando o REST API do Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-rest-api-auth?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [COLOCAR Blob](https://docs.microsoft.com/rest/api/storageservices/put-blob)
- [Carregar blobs o para armazenamento de Blob](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage)

### <a name="upload-a-file-with-postman"></a>Carregar um arquivo com o Postman

Como exemplo, usamos o Postman para carregar um arquivo. mp4 pequeno. Pode haver um limite de tamanho de arquivo no carregamento de binários pelo Postman.

A solicitação de carregamento não é parte da coleção do **AzureMedia**. 

Criar e configurar uma nova solicitação:
1. Pressione **+** para criar uma nova guia de solicitação.
2. Selecione **COLOCAR** operação e cole **{{UploadURL}}** na URL.
2. Deixe a guia **Autorização** como está (não a defina como **Token de portador**).
3. Na guia **Cabeçalhos**, especifique: **Chave**: “x-ms-blob-type” e **Valor**: “BlockBlob”.
2. Na guia **Corpo**, clique em **binário**.
4. Escolha o arquivo com o nome que você especificou na variável de ambiente **NomeDoArquivoDeMídia**.
5. Pressione **Enviar**.

    ![Carregar um arquivo](./media/media-services-rest-upload-files/postman-upload-file.png)

##  <a name="create-a-metadata-in-the-asset"></a>Criar um metadados no ativo

Depois que o arquivo foi carregado, você precisa criar um metadados no ativo para o arquivo de mídia carregado no armazenamento de blob associado a seu ativo.

1. Selecione **ArquivosdeAtivos** -> **CriarInformaçõesDeArquivos**.
2. Pressione **Enviar**.

    ![Carregar um arquivo](./media/media-services-rest-upload-files/postman-create-file-info.png)

O arquivo deve ser carregado e seus metadados definidos.

## <a name="validate"></a>Validar

Para validar que o arquivo foi carregado com êxito, convém consultar o [ArquivoDeAtivo](https://docs.microsoft.com/rest/api/media/operations/assetfile) e comparar o **TamanhoDoConteúdoDeArquivo** (ou outros detalhes) com o que você espera encontrar no novo ativo. 

Por exemplo, a seguinte operação**OBTER** coloca os dados de arquivo para o arquivo de ativo (no nosso caso, o arquivo BigBuckBunny.mp4). A consulta está usando as [variáveis de ambiente](postman-environment.md) que você definiu anteriormente.

    {{RESTAPIEndpoint}}/Assets('{{LastAssetId}}')/Files

A resposta conterá o tamanho, o nome e outras informações.

    "Id": "nb:cid:UUID:69e72ede-2886-4f2a-8d36-80a59da09913",
    "Name": "BigBuckBunny.mp4",
    "ContentFileSize": "3186542",
    "ParentAssetId": "nb:cid:UUID:0b8f3b04-72fb-4f38-8e7b-d7dd78888938",
            
## <a name="next-steps"></a>Próximas etapas

Agora você pode codificar seus ativos carregados. Para saber mais, veja [Codificar ativos](media-services-portal-encode.md).

Você também pode usar as Azure Functions para disparar um trabalho de codificação baseado em um arquivo que chega no contêiner configurado. Para obter mais informações, confira [este exemplo](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/ ).

