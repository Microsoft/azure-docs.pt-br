---
title: Como criptografar seu conteúdo com a criptografia de armazenamento usando a API REST do AMS
description: Saiba como criptografar seu conteúdo com criptografia de armazenamento usando as APIs REST do AMS.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: a26388de85ff6293985fe23adac8ca4d04d0de61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60952261"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Criptografia do seu conteúdo com criptografia de armazenamento 

Recomendamos que você criptografe seu conteúdo localmente usando a criptografia AES de 256 bits e, em seguida, o carregue no Armazenamento do Microsoft Azure no qual ele está armazenado e criptografado em repouso.

Este artigo fornece uma visão geral da criptografia de armazenamento do AMS e mostra como carregar o conteúdo de armazenamento criptografado:

* Crie uma chave de conteúdo.
* Crie um ativo. Defina o AssetCreationOption como StorageEncryption ao criar o ativo.
  
     Os ativos criptografados são associados a chaves de conteúdo.
* Vincular a chave de conteúdo ao ativo.  
* Defina os parâmetros relacionados à criptografia nas entidades AssetFile.

## <a name="considerations"></a>Considerações 

Se você quiser entregar um ativo de armazenamento criptografado, configure a política de entrega do ativo. Antes que seu ativo possa ser transmitido, o servidor de streaming remove a criptografia de armazenamento e transmite o conteúdo usando a política de entrega especificada. Para obter mais informações, confira a seção [Configuring Asset Delivery Policies](media-services-rest-configure-asset-delivery-policy.md)(Configurando as Políticas de Entrega de Ativos).

Ao acessar entidades nos serviços de mídia, você deve definir valores e campos de cabeçalho específicos nas suas solicitações HTTP. Para obter mais informações, consulte [Configuração para desenvolvimento da API REST dos Serviços de Mídia](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Criptografia do armazenamento

|Opção de criptografia|DESCRIÇÃO|Serviços de Mídia v2|Serviços de Mídia v3|
|---|---|---|---|
|Criptografia do Armazenamento dos Serviços de Mídia|Criptografia AES-256, chave gerenciada pelos Serviços de Mídia|Com suporte<sup>(1)</sup>|Sem suporte<sup>(2)</sup>|
|[Criptografia do Serviço de Armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Criptografia do servidor oferecida pelo Armazenamento do Microsoft Azure, chave gerenciada pelo Azure ou pelo cliente|Com suporte|Com suporte|
|[Criptografia do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Criptografia do cliente oferecida pelo armazenamento do Azure, chave gerenciada pelo cliente no Key Vault|Sem suporte|Sem suporte|

<sup>1</sup> Enquanto os Serviços de Mídia deem suporte para tratamento de conteúdo sem qualquer forma de criptografia/limpo, não é recomendável fazer isso.

<sup>2</sup> Nos Serviços de Mídia v3, a criptografia de armazenamento (criptografia AES-256) somente terá suporte para compatibilidade com versões anteriores quando os Ativos tiverem sido criados com os Serviços de Mídia v2. Isso significa que o v3 funciona com recursos criptografados de armazenamento existentes, mas não permite a criação de novos recursos.

## <a name="connect-to-media-services"></a>Conectar-se aos Serviços de Mídia

Para saber mais sobre como conectar-se à API do AMS, veja [Acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Visão geral da criptografia de armazenamento
A criptografia de armazenamento do AMS aplica a criptografia do modo **AES-CTR** no arquivo inteiro.  O modo AES-CTR é uma codificação de bloco que pode criptografar dados de comprimento arbitrário sem necessidade de preenchimento. Ela funciona criptografando um bloco de contador com o algoritmo AES e aplicando XOR à saída do AES com os dados para criptografar ou descriptografar.  O bloco de contador usado é construído copiando o valor do InitializationVector para bytes de 0 a 7 do valor do contador e 8 a 15 do valor do contador são definidos como zero. Do bloco de contador de 16 bytes, os bytes de 8 a 15 (ou seja, os bytes menos significativos) são usados como um inteiro sem sinal de 64 bits simples que é incrementado em um para cada bloco subsequente de dados processados e é mantido em ordem de byte da rede. Se esse número inteiro alcançar o valor máximo (0xFFFFFFFFFFFFFFFF), então, incrementá-lo redefinirá o contador de bloco para zero (bytes 8 a 15) sem afetar os outros 64 bits do contador (ou seja, bytes de 0 a 7).   Para manter a segurança de criptografia do modo AES-CTR, o valor do InitializationVector de um determinado Identificador Chave para cada conteúdo deve ser exclusivo para cada arquivo e os arquivos devem ser menores do que 2^64 blocos em comprimento.  Esse valor exclusivo é para garantir que um valor do contador nunca seja reutilizado com uma determinada chave. Para obter mais informações sobre o modo CTR, confira [esta página wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (o artigo da wiki usa o termo "Nonce" em vez de "InitializationVector").

Use a **Criptografia de Armazenamento** para criptografar seu conteúdo limpo localmente usando a criptografia AES de 256 bits e, em seguida, carregue-a no Armazenamento do Azure no qual ela é armazenada e criptografada em repouso. Ativos protegidos pela criptografia de armazenamento são descriptografados automaticamente e posicionados em um sistema de arquivos criptografado antes da codificação, então opcionalmente criptografados novamente antes do carregamento como um novo ativo de saída. O caso de uso principal para criptografia de armazenamento é quando você deseja proteger seus arquivos de mídia de entrada com criptografia forte de alta qualidade em repouso no disco.

Para entregar um ativo de armazenamento criptografado, você deve configurar a política de entrega do ativo para que o Serviços de Mídia saiba como você deseja distribuir seu conteúdo. Antes que seu ativo possa ser transmitido, o servidor de streaming remove a criptografia de armazenamento e transmite o conteúdo usando a política de distribuição especificada (por exemplo, AES, criptografia comum ou sem criptografia).

## <a name="create-contentkeys-used-for-encryption"></a>Criar ContentKeys usadas para criptografia
Os ativos criptografados são associados às chaves de Criptografia de Armazenamento. Crie a chave de conteúdo a ser usada para criptografia antes de criar os arquivos de ativo. Este artigo descreve como criar uma chave de conteúdo.

A seguir, estão as etapas gerais para gerar chaves de conteúdo que você associará aos ativos que deseja que sejam criptografados. 

1. Na criptografia de armazenamento, gere uma chave AES de 32 bytes aleatoriamente. 
   
    A chave AES de 32 bytes é a chave de conteúdo do ativo, o que significa que todos os arquivos associados a esse ativo precisarão usar a mesma chave de conteúdo durante a descriptografia. 
2. Chame os métodos [GetProtectionKeyId](https://docs.microsoft.com/rest/api/media/operations/rest-api-functions#getprotectionkeyid) e [GetProtectionKey](https://msdn.microsoft.com/library/azure/jj683097.aspx#getprotectionkey) para obter o Certificado X.509 correto que deve ser usado para criptografar sua chave de conteúdo.
3. Criptografe a chave de conteúdo com a chave pública do certificado X.509. 
   
   O SDK do .NET dos serviços de mídia usa RSA com OAEP ao fazer a criptografia.  Você pode ver um exemplo do .NET na [função EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Crie um valor de soma de verificação calculado usando o identificador de chave e a chave de conteúdo. O exemplo de .NET a seguir calcula a soma de verificação usando a parte GUID do identificador de chave e a chave de conteúdo limpa.

    ```csharp
            public static string CalculateChecksum(byte[] contentKey, Guid keyId)
            {
                const int ChecksumLength = 8;
                const int KeyIdLength = 16;

                byte[] encryptedKeyId = null;

                // Checksum is computed by AES-ECB encrypting the KID
                // with the content key.
                using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
                {
                    rijndael.Mode = CipherMode.ECB;
                    rijndael.Key = contentKey;
                    rijndael.Padding = PaddingMode.None;

                    ICryptoTransform encryptor = rijndael.CreateEncryptor();
                    encryptedKeyId = new byte[KeyIdLength];
                    encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
                }

                byte[] retVal = new byte[ChecksumLength];
                Array.Copy(encryptedKeyId, retVal, ChecksumLength);

                return Convert.ToBase64String(retVal);
            }
    ```

5. Crie a chave de conteúdo com os valores **EncryptedContentKey** (convertido em cadeia de caracteres codificada em base64), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType** e **Checksum** que você recebeu nas etapas anteriores.

    Na criptografia de armazenamento, as propriedades a seguir devem ser incluídas no corpo da solicitação.

    Propriedade do corpo da solicitação    | DESCRIÇÃO
    ---|---
    ID | A ID de ContentKey é gerada usando o seguinte formato, "NB:\<nova GUID >".
    ContentKeyType | O tipo de chave de conteúdo é um inteiro que define a chave. Para o formato de criptografia de armazenamento, o valor é 1.
    EncryptedContentKey | Criamos um novo valor de chave de conteúdo, que é um valor de 256 bits (32 bytes). A chave é criptografada usando o certificado X.509 de criptografia de armazenamento que recuperamos dos Serviços de Mídia do Microsoft Azure por meio da execução de uma solicitação HTTP GET para os métodos GetProtectionKeyId e GetProtectionKey. Como um exemplo, confira o seguinte código do .NET: o método **EncryptSymmetricKeyData** definido [aqui](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | Essa é a ID da chave de proteção para o certificado X.509 de criptografia de armazenamento usado para criptografar nossa chave de conteúdo.
    ProtectionKeyType | Esse é o tipo de criptografia para a chave de proteção usada para criptografar a chave de conteúdo. Em nosso exemplo, este valor será StorageEncryption(1).
    Soma de verificação |A soma de verificação calculada por MD5 para a chave de conteúdo. Ela é calculada pela criptografia da ID de conteúdo com a chave de conteúdo. O exemplo de código demonstra como calcular a soma de verificação.


### <a name="retrieve-the-protectionkeyid"></a>Recuperação de ProtectionKeyId
O exemplo a seguir mostra como recuperar o ProtectionKeyId, uma impressão digital de certificado, para o certificado que você deve usar ao criptografar a chave de conteúdo. Conclua esta etapa para certificar-se de que você já tem o certificado apropriado em seu computador.

Solicitação:

    GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

Resposta:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 139
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:42:52 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Recuperar ProtectionKey para o ProtectionKeyId
O exemplo a seguir mostra como recuperar o certificado X.509 usando o ProtectionKeyId recebido na etapa anterior.

Solicitação:

    GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN> 
    x-ms-version: 2.17
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    Host: media.windows.net

Resposta:

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Content-Length: 1227
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Server: Microsoft-IIS/8.5
    x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
    request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Thu, 05 Feb 2015 07:52:30 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
    "value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}

### <a name="create-the-content-key"></a>Criar a chave de conteúdo
Depois de recuperar o certificado X.509 e usar sua chave pública para criptografar a chave de conteúdo, crie uma entidade **ContentKey** e defina seus valores de propriedade adequadamente.

Um dos valores que você deve definir quando criar o conteúdo chave é o tipo. Ao usar criptografia de armazenamento, o valor deve ser definido como '1'. 

O exemplo a seguir mostra como criar um **ContentKey** com um **ContentKeyType** definido para criptografia de armazenamento ("1") e o **ProtectionKeyType** definido como "0" para indicar que a ID da chave de proteção é a impressão digital do certificado X.509.  

Solicitação

    POST https://media.windows.net/api/ContentKeys HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    User-Agent: Microsoft ADO.NET Data Services
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net
    {
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
    "ContentKeyType":"1", 
    "ProtectionKeyType":"0",
    "EncryptedContentKey":"your encrypted content key",
    "Checksum":"calculated checksum"
    }

Resposta:

    HTTP/1.1 201 Created
    Cache-Control: no-cache
    Content-Length: 777
    Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
    Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
    Server: Microsoft-IIS/8.5
    request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
    X-Content-Type-Options: nosniff
    DataServiceVersion: 3.0;
    X-Powered-By: ASP.NET
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Wed, 04 Feb 2015 02:37:46 GMT

    {"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
    "Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
    "LastModified":"2015-02-04T02:37:46.9684379Z",
    "ContentKeyType":1,
    "EncryptedContentKey":"your encrypted content key",
    "Name":"ContentKey",
    "ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
    "ProtectionKeyType":0,
    "Checksum":"calculated checksum"}

## <a name="create-an-asset"></a>Criar um ativo
O exemplo a seguir mostra como criar um ativo.

**Solicitação HTTP**

    POST https://media.windows.net/api/Assets HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

    {"Name":"BigBuckBunny" "Options":1}

**Resposta HTTP**

Se for bem-sucedido, será retornada a seguinte resposta:

    HTP/1.1 201 Created
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
    Strict-Transport-Security: max-age=31536000; includeSubDomains
    Date: Sun, 18 Jan 2015 22:06:40 GMT
    {  
       "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
       "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "State":0,
       "Created":"2015-01-18T22:06:40.6010903Z",
       "LastModified":"2015-01-18T22:06:40.6010903Z",
       "AlternateId":null,
       "Name":"BigBuckBunny.mp4",
       "Options":1,
       "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
       "StorageAccountName":"storagetestaccount001"
    }

## <a name="associate-the-contentkey-with-an-asset"></a>Associar o ContentKey com um ativo
Depois de criar o ContentKey, associe-o ao seu ativo usando a operação $links, conforme mostrado no exemplo a seguir:

Solicitação:

    POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Content-Type: application/json
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net

    {"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}

Resposta:

    HTTP/1.1 204 No Content 

## <a name="create-an-assetfile"></a>Criar um AssetFile
A entidade [AssetFile](https://docs.microsoft.com/rest/api/media/operations/assetfile) representa um arquivo de áudio ou vídeo que é armazenado em um contêiner de blob. Um arquivo de ativo está sempre associado a um ativo e um ativo pode conter um ou vários arquivos de ativo. A tarefa do Codificador dos serviços de mídia falha se um objeto de arquivo de ativo não estiver associado um arquivo digital em um contêiner de blob.

A instância de **AssetFile** e o arquivo de mídia real são dois objetos diferentes. A instância de AssetFile contém metadados sobre o arquivo de mídia, enquanto o arquivo de mídia contém o conteúdo de mídia real.

Depois de carregar seu arquivo de mídia digital em um contêiner de blobs, você usará a solicitação HTTP **MERGE** para atualizar o AssetFile com informações sobre o arquivo de mídia (não mostrado neste artigo). 

**Solicitação HTTP**

    POST https://media.windows.net/api/Files HTTP/1.1
    Content-Type: application/json
    DataServiceVersion: 1.0;NetFx
    MaxDataServiceVersion: 3.0;NetFx
    Accept: application/json
    Accept-Charset: UTF-8
    Authorization: Bearer <ENCODED JWT TOKEN>
    x-ms-version: 2.17
    Host: media.windows.net
    Content-Length: 164

    {  
       "IsEncrypted":"true",
       "EncryptionScheme" : "StorageEncryption", 
       "EncryptionVersion" : "1.0",       
       "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector" : "397304628502661816</d:InitializationVector",
       "Options":0,
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
       "EncryptionVersion": "1.0",
       "EncryptionScheme": "StorageEncryption",
       "IsEncrypted":true,
       "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
       "InitializationVector":"397304628502661816</d:InitializationVector",
       "IsPrimary":false,
       "LastModified":"2015-01-19T00:34:08.1934137Z",
       "Created":"2015-01-19T00:34:08.1934137Z",
       "MimeType":"video/mp4",
       "ContentChecksum":null
    }
