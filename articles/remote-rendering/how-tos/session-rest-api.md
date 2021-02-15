---
title: A API REST de gerenciamento de sessão
description: Descreve como gerenciar sessões
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: d957c5d6521010c7393e2297be16cd7bef41c35f
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724061"
---
# <a name="use-the-session-management-rest-api"></a>Usar a API REST de gerenciamento de sessão

Para usar a funcionalidade de renderização remota do Azure, você precisa criar uma *sessão*. Cada sessão corresponde a uma VM (máquina virtual) que está sendo alocada no Azure e aguardando a conexão de um dispositivo cliente. Quando um dispositivo se conecta, a VM renderiza os dados solicitados e serve o resultado como um fluxo de vídeo. Durante a criação da sessão, você escolheu em qual tipo de servidor deseja executar, o que determina os preços. Depois que a sessão não for mais necessária, ela deverá ser interrompida. Se não for interrompido manualmente, ele será desligado automaticamente quando o *tempo de concessão* da sessão expirar.

Fornecemos um script do PowerShell no [repositório de exemplos do arr](https://github.com/Azure/azure-remote-rendering) na pasta *Scripts* , chamada *RenderingSession.ps1*, que demonstra o uso de nosso serviço. O script e sua configuração são descritos aqui: [exemplos de scripts do PowerShell](../samples/powershell-example-scripts.md)

> [!TIP]
> Os comandos do PowerShell listados nesta página destinam-se a complementar um ao outro. Se você executar todos os scripts em sequência dentro do mesmo prompt de comando do PowerShell, eles serão criados em cima um do outro.

## <a name="regions"></a>Regiões

Consulte a [lista de regiões disponíveis](../reference/regions.md) para as URLs de base para as quais enviar as solicitações.

Para os scripts de exemplo abaixo, escolhemos a região *westus2*.

### <a name="example-script-choose-an-endpoint"></a>Script de exemplo: escolher um ponto de extremidade

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Contas

Se você não tiver uma conta de renderização remota, [crie uma](create-an-account.md). Cada recurso é identificado por uma *AccountId*, que é usada em todas as APIs de sessão.

### <a name="example-script-set-accountid-accountkey-and-account-domain"></a>Script de exemplo: Set AccountId, accountKey e domínio da conta

Domínio da conta é o local da conta de renderização remota. Neste exemplo, o local da conta é região *lesteus*.

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
$accountDomain = "eastus.mixedreality.azure.com"
```

## <a name="common-request-headers"></a>Cabeçalhos de solicitação comuns

* O cabeçalho *Authorization* deve ter o valor de " `Bearer TOKEN` ", onde " `TOKEN` " é o token de autenticação [retornado pelo serviço de token seguro](tokens.md).

### <a name="example-script-request-a-token"></a>Script de exemplo: solicitar um token

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.$accountDomain/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Cabeçalhos de resposta comuns

* O cabeçalho *MS-CV* pode ser usado pela equipe do produto para rastrear a chamada dentro do serviço.

## <a name="create-a-session"></a>Criar uma sessão

Este comando cria uma sessão. Ele retorna a ID da nova sessão. Você precisa da ID de sessão para todos os outros comandos.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*AccountId*/Sessions/Create | POST |

**Corpo da solicitação:**

* maxLeaseTime (TimeSpan): um valor de tempo limite quando a sessão será encerrada automaticamente
* modelos (matriz): URLs do contêiner de ativos a serem pré-carregados
* tamanho (cadeia de caracteres): o tamanho do servidor a ser configurado ([**"Standard"**](../reference/vm-sizes.md) ou [**"Premium"**](../reference/vm-sizes.md)). Consulte [limitações de tamanho](../reference/limits.md#overall-number-of-polygons)específicas.

**Response**

| Código de status | conteúdo JSON | Comentários |
|-----------|:-----------|:-----------|
| 202 | -sessionId: GUID | Êxito |

### <a name="example-script-create-a-session"></a>Script de exemplo: criar uma sessão

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

Saída de exemplo:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>Script de exemplo: Store sessionId

A resposta da solicitação acima inclui uma **SessionID**, que você precisa para todas as solicitações de acompanhamento.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="modify-and-query-session-properties"></a>Propriedades de modificar e consultar sessão

Há alguns comandos para consultar ou modificar os parâmetros de sessões existentes.

> [!CAUTION]
> Para todas as chamadas REST, o envio desses comandos com muita frequência fará com que o servidor seja limitado e retorne a falha eventualmente. Nesse caso, o código de status é 429 ("muitas solicitações"). Como regra geral, deve haver um atraso de **5 a 10 segundos entre as chamadas subsequentes**.

### <a name="update-session-parameters"></a>Atualizar parâmetros de sessão

Esse comando atualiza os parâmetros de uma sessão. No momento, você só pode estender o tempo de concessão de uma sessão.

> [!IMPORTANT]
> O tempo de concessão é sempre fornecido como um tempo total desde o início da sessão. Isso significa que se você criou uma sessão com um tempo de concessão de uma hora e deseja estender seu tempo de concessão por outra hora, você precisa atualizar seu maxLeaseTime para duas horas.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*AccountId*/Sessions/*SessionID* | PATCH |

**Corpo da solicitação:**

* maxLeaseTime (TimeSpan): um valor de tempo limite quando a sessão será encerrada automaticamente

**Response**

| Código de status | conteúdo JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | | Sucesso |

#### <a name="example-script-update-a-session"></a>Script de exemplo: atualizar uma sessão

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

Saída de exemplo:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

### <a name="get-active-sessions"></a>Obter sessões ativas

Esse comando retorna uma lista de sessões ativas.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*AccountId*/Sessions | GET |

**Response**

| Código de status | conteúdo JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | -Sessions: matriz de propriedades de sessão | consulte a seção "obter propriedades da sessão" para obter uma descrição das propriedades da sessão |

#### <a name="example-script-query-active-sessions"></a>Script de exemplo: consultar sessões ativas

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Saída de exemplo:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

### <a name="get-sessions-properties"></a>Propriedades de obter sessões

Esse comando retorna informações sobre uma sessão, como seu nome de host de VM.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*AccountId*/Sessions/*SessionID*/Properties | GET |

**Response**

| Código de status | conteúdo JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | -Message: cadeia de caracteres<br/>-sessionElapsedTime: TimeSpan<br/>-sessionHostname: cadeia de caracteres<br/>-sessionId: cadeia de caracteres<br/>-sessionMaxLeaseTime: TimeSpan<br/>-Sessions: enumeração<br/>-sessionStatus: enumeração | enum sessionStatus {iniciando, pronto, parando, parado, expirado, erro}<br/>Se o status for ' erro ' ou ' expirado ', a mensagem conterá mais informações |

#### <a name="example-script-get-session-properties"></a>Script de exemplo: obter propriedades de sessão

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Saída de exemplo:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>Parar uma sessão

Esse comando para uma sessão. A VM alocada será recuperada logo após.

| URI | Método |
|-----------|:-----------|
| /v1/accounts/*AccountId*/Sessions/*SessionID* | DELETE |

**Response**

| Código de status | conteúdo JSON | Comentários |
|-----------|:-----------|:-----------|
| 204 | | Êxito |

### <a name="example-script-stop-a-session"></a>Script de exemplo: parar uma sessão

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

Saída de exemplo:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>Próximas etapas

* [Scripts de exemplo do PowerShell](../samples/powershell-example-scripts.md)
