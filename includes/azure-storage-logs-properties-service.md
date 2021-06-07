---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 9b18687c0a6f3e48d94431e88be8ae8137c9dcdb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011109"
---
| Propriedade | Descrição |
|:--- |:---|
|**accountName** | O nome da conta de armazenamento. Por exemplo: `mystorageaccount`.  |
|**requestUrl** | A URL que é solicitada. |
|**userAgentHeader** | O valor de cabeçalho de **User-Agent**, entre aspas. Por exemplo: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | O valor do cabeçalho de **Referrer**. Por exemplo: `http://contoso.com/about.html`.|
|**clientRequestId** | O valor do cabeçalho **x-ms-client-request-id** da solicitação. Por exemplo: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**etag** | O identificador ETag para o objeto retornado, entre aspas. Por exemplo: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | O tempo total, expressado em milissegundos para executar a operação solicitada. Esse valor não inclui a latência de rede (o tempo de leitura da solicitação de entrada e envio da resposta ao solicitante). Por exemplo: `22`. |
|**serviceType** | O serviço associado a essa solicitação. Por exemplo: `blob`, `table`, `files` ou `queue`. |
|**operationCount** | O número de cada operação registrada que está envolvida na solicitação. Essa contagem começa com um índice de `0`. Algumas solicitações exigem mais de uma operação. A maioria das solicitações executa apenas uma operação. Por exemplo: `1`. |
|**requestHeaderSize** | O tamanho do cabeçalho da solicitação, expresso em bytes. Por exemplo: `578`. <br>Se uma solicitação for malsucedida, esse valor poderá estar vazia. |
|**requestBodySize** | O tamanho dos pacotes de solicitação, expresso em bytes, que são lidos pelo serviço de armazenamento. <br> Por exemplo: `0`. <br>Se uma solicitação for malsucedida, esse valor poderá estar vazia.  |
|**responseHeaderSize** | O tamanho do cabeçalho da solicitação, expresso em bytes. Por exemplo: `216`. <br>Se uma solicitação for malsucedida, esse valor poderá estar vazia.  |
|**responseBodySize** | O tamanho dos pacotes de resposta gravados pelo serviço de armazenamento, em bytes. Se uma solicitação for malsucedida, esse valor poderá estar vazia. Por exemplo: `216`.  |
|**requestMd5** | O valor do cabeçalho **Content-MD5** ou do cabeçalho **x-ms-content-md5** na solicitação. O valor de hash MD5 especificado nesse campo representa o conteúdo na solicitação. Por exemplo: `788815fd0198be0d275ad329cafd1830`. <br>Este campo pode estar vazio.  |
|**serverMd5** | O valor de hash MD5 calculado pelo serviço de armazenamento. Por exemplo: `3228b3cf1069a5489b298446321f8521`. <br>Este campo pode estar vazio.  |
|**lastModifiedTime** | A LMT (Hora da Última Modificação) do objeto retornado.  Por exemplo: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Esse campo fica vazio para operações que podem retornar vários objetos. |
|**conditionsUsed** | Uma lista separada por ponto e vírgula de pares chave-valor que representam uma condição. As condições podem ser as seguintes: <li> If-Modified-Since <li> If-Unmodified-Since <li> If-Match <li> If-None-Match  <br> Por exemplo: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | O valor do cabeçalho Content-Length para a solicitação enviada ao serviço de armazenamento. Se a solicitação foi bem-sucedida, esse valor será igual a requestBodySize. Se uma solicitação for malsucedida, esse valor talvez não seja igual a requestBodySize ou pode estar vazio. |
|**tlsVersion** | A versão de TLS usada na conexão da solicitação. Por exemplo: `TLS 1.2`. |
|**smbTreeConnectID** | O **treeConnectId** de Server Message Block (SMB) estabelecido na hora de conexão da árvore. Por exemplo: `0x3` |
|**smbPersistentHandleID** | ID de identificador persistente de uma solicitação SMB2 CREATE que sobrevive a reconexões de rede.  Referenciado em [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 como **SMB2_FILEID.Persistent**. Por exemplo: `0x6003f` |
|**smbVolatileHandleID** | ID de identificador volátil de uma solicitação SMB2 CREATE que é reciclado em reconexões de rede.  Referenciado em [MS-SMB2](/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 como **SMB2_FILEID.Volatile**. Por exemplo: `0xFFFFFFFF00000065` |
|**smbMessageID** | A conexão relativa a **MessageId**. Por exemplo: `0x3b165` |
|**smbCreditsConsumed** | A entrada ou saída consumida pela solicitação, em unidades de 64K. Por exemplo: `0x3` |
|**smbCommandDetail** | Mais informações sobre essa solicitação específica, em vez do tipo geral de solicitação. Por exemplo: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | A **FileID** associada ao arquivo ou ao diretório.  Aproximadamente análogo a NTFS FileID. Por exemplo: `0x9223442405598953` |
|**smbSessionID** | O SMB2 **SessionId** estabelecido no momento da configuração da sessão. Por exemplo: `0x8530280128000049` |
|**smbCommandMajor  uint32** | Valor no **SMB2_HEADER.Command**. Atualmente, esse é um número entre 0 e 18, inclusive. Por exemplo: `0x6` |
|**smbCommandMinor** | A subclasse de **SmbCommandMajor**, quando apropriado. Por exemplo: `DirectoryCloseAndDelete` |