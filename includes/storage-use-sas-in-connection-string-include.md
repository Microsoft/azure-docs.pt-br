---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 2f27c50b1d016265c20102521a137bcbb0646115
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172140"
---
Se você possui uma URL de SAS (assinatura de acesso compartilhado) que concede acesso a recursos em uma conta de armazenamento, pode usar a SAS em uma cadeia de conexão. Como a SAS contém as informações necessárias para autenticar a solicitação, uma cadeia de conexão com uma SAS fornece o protocolo, o ponto de extremidade de serviço e as credenciais necessárias para acessar o recurso.

Para criar uma cadeia de conexão que inclui uma assinatura de acesso compartilhado, especifique a cadeia de caracteres no seguinte formato:

```
BlobEndpoint=myBlobEndpoint;
QueueEndpoint=myQueueEndpoint;
TableEndpoint=myTableEndpoint;
FileEndpoint=myFileEndpoint;
SharedAccessSignature=sasToken
```

Cada ponto de extremidade de serviço é opcional, embora a cadeia de conexão deve conter pelo menos um.

> [!NOTE]
> Usar HTTPS com uma SAS é uma prática recomendada.
>
> Se você estiver especificando uma SAS em uma cadeia de conexão em um arquivo de configuração, precisará codificar caracteres especiais na URL.
>
>

### <a name="service-sas-example"></a>Exemplo de SAS de serviço
Aqui está um exemplo de uma cadeia de conexão que inclui um serviço SAS para o Armazenamento de Blobs:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&sr=b&si=tutorial-policy-635959936145100803&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

E aqui está um exemplo da mesma cadeia de conexão com codificação de caracteres especiais:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
SharedAccessSignature=sv=2015-04-05&amp;sr=b&amp;si=tutorial-policy-635959936145100803&amp;sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D
```

### <a name="account-sas-example"></a>Exemplo de SAS de conta
Aqui está um exemplo de uma cadeia de conexão que inclui uma conta SAS para o Armazenamento de Blobs e de Arquivos: Observe que os pontos de extremidade para ambos os serviços são especificados:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&spr=https&st=2016-04-12T03%3A24%3A31Z&se=2016-04-13T03%3A29%3A31Z&srt=s&ss=bf&sp=rwl
```

E aqui está um exemplo da mesma cadeia de conexão com a codificação de URL:

```
BlobEndpoint=https://storagesample.blob.core.windows.net;
FileEndpoint=https://storagesample.file.core.windows.net;
SharedAccessSignature=sv=2015-07-08&amp;sig=iCvQmdZngZNW%2F4vw43j6%2BVz6fndHF5LI639QJba4r8o%3D&amp;spr=https&amp;st=2016-04-12T03%3A24%3A31Z&amp;se=2016-04-13T03%3A29%3A31Z&amp;srt=s&amp;ss=bf&amp;sp=rwl
```

