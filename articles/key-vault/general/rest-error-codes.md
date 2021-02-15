---
title: Códigos de erro da API REST – Azure Key Vault
description: Esses códigos de erro podem ser retornados por uma operação em um serviço Web Azure Key Vault.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 30b7e34f2a791cfd8dec1a6d8e81d706fa07939f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631215"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Azure Key Vault códigos de erro da API REST
 
Os códigos de erro a seguir podem ser retornados por uma operação em um serviço Web Azure Key Vault.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: solicitação não autenticada

401 significa que a solicitação não está autenticada para Key Vault. 

Uma solicitação será autenticada se:

- O cofre de chaves conhece a identidade do chamador; e
- O chamador tem permissão para tentar acessar recursos de Key Vault. 

Há várias razões diferentes pelas quais uma solicitação pode retornar 401.

### <a name="no-authentication-token-attached-to-the-request"></a>Nenhum token de autenticação anexado à solicitação. 

Aqui está um exemplo de solicitação PUT, definindo o valor de um segredo:

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

O cabeçalho "Authorization" é o token de acesso necessário com cada chamada para a Key Vault para operações de plano de dados. Se o cabeçalho estiver ausente, a resposta deverá ser 401.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>O token não tem o recurso correto associado a ele. 

Ao solicitar um token de acesso do ponto de extremidade OAUTH do Azure, um parâmetro chamado "Resource" é obrigatório. O valor é importante para o provedor de token porque ele faz o escopo do token para seu uso pretendido. O recurso para **todos os** tokens para acessar uma Key Vault é *https: \/ /Vault.keyvault.net* (sem barra à direita).

### <a name="the-token-is-expired"></a>O token expirou

Os tokens são codificados em Base64 e os valores podem ser decodificados em sites como [http://jwt.calebb.net](http://jwt.calebb.net) . Aqui está o token acima decodificado:

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

Podemos ver muitas partes importantes nesse token:

- AUD (Audience): o recurso do token. Observe que isso é `https://vault.azure.net` . Esse token não funcionará para nenhum recurso que não corresponda explicitamente a esse valor, como grafo.
- IAT (emitido em): o número de tiques desde o início da época em que o token foi emitido.
- NBF (não antes): o número de tiques desde o início da época em que esse token se torna válido.
- exp (expiração): o número de tiques desde o início da época em que esse token expira.
- AppID (ID do aplicativo): o GUID para a ID do aplicativo que faz essa solicitação.
- tid (ID do locatário): o GUID da ID de locatário da entidade de segurança que faz esta solicitação

É importante que todos os valores sejam corretamente identificados no token para que a solicitação funcione. Se tudo estiver correto, a solicitação não resultará em 401.

### <a name="troubleshooting-401"></a>Solução de problemas 401

401s deve ser investigado a partir do ponto de geração de token, antes que a solicitação seja feita ao cofre de chaves. Geralmente, o código está sendo usado para solicitar o token. Depois que o token é recebido, ele é passado para a solicitação de Key Vault. Se o código estiver sendo executado localmente, você poderá usar o Fiddler para capturar a solicitação/resposta para `https://login.microsoftonline.com` . Uma solicitação é parecida com esta:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

As informações fornecidas pelo usuário a seguir devem estar corretas:

- A ID do locatário do cofre de chaves
- Valor do recurso definido como https %3 A %2 F %2 F Vault. Azure. net (codificação de URL)
- ID do Cliente
- Segredo do cliente

Verifique se o restante da solicitação é quase idêntico.

Se você só puder obter o token de acesso de resposta, poderá decodificá-lo (conforme mostrado acima) para garantir a ID do locatário, a ID do cliente (ID do aplicativo) e o recurso.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: permissões insuficientes

O HTTP 403 significa que a solicitação foi autenticada (ela conhece a identidade solicitada), mas a identidade não tem permissão para acessar o recurso solicitado. Há duas causas:

- Não há nenhuma política de acesso para a identidade.
- O endereço IP do recurso solicitante não é aprovado nas configurações de firewall do cofre de chaves.

O HTTP 403 geralmente ocorre quando o aplicativo do cliente não está usando a ID do cliente que o cliente pensa. Isso geralmente significa que as políticas de acesso não estão configuradas corretamente para a identidade de chamada real.

### <a name="troubleshooting-403"></a>Solução de problemas 403

Primeiro, ative o registro em log. Para obter instruções sobre como fazer isso, consulte [Azure Key Vault log](logging.md).

Depois que o registro em log estiver ativado, você poderá determinar se o 403 é devido à política de acesso ou à política de firewall.

#### <a name="error-due-to-firewall-policy"></a>Erro devido à política de firewall

"O endereço do cliente (00.00.00.00) não é autorizado e o chamador não é um serviço confiável"

Há uma lista limitada de "serviços confiáveis do Azure". Os sites do Azure **não** são um serviço do Azure confiável. Para obter mais informações, consulte a postagem no blog [Key Vault acesso ao firewall por Azure app Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services).

Você deve adicionar o endereço IP do site do Azure ao Key Vault para que ele funcione.

Se devido à política de acesso: Localize a ID de objeto para a solicitação e verifique se a ID do objeto corresponde ao objeto ao qual o usuário está tentando atribuir a política de acesso. Em geral, haverá vários objetos no AAD que têm o mesmo nome, portanto, escolher o correto é muito importante. Ao excluir e adicionar novamente a política de acesso, é possível ver se existem vários objetos com o mesmo nome.

Além disso, a maioria das políticas de acesso não exige o uso do "aplicativo autorizado", conforme mostrado no Portal. O aplicativo autorizado é usado para cenários de autenticação "em nome de", que são raros. 


## <a name="http-429-too-many-requests"></a>HTTP 429: muitas solicitações

A limitação ocorre quando o número de solicitações excede o máximo declarado para o período de tempo. Se a limitação ocorrer, a resposta do Key Vault será HTTP 429. Há máximos declarados para tipos de solicitações feitas. Por exemplo: a criação de uma chave HSM de 2048 bits é de 5 solicitações por 10 segundos, mas todas as outras transações HSM têm um limite de solicitação 1000/10 segundos. Portanto, é importante entender quais tipos de chamadas estão sendo feitas ao determinar a causa da limitação.
Em geral, as solicitações para os Key Vault são limitadas a 2000 solicitações/10 segundos. As exceções são operações importantes, conforme documentado em [limites de serviço Key Vault](service-limits.md)

### <a name="troubleshooting-429"></a>Solução de problemas 429
A limitação é contornada usando estas técnicas:

- Reduza o número de solicitações feitas ao Key Vault determinando se há padrões para um recurso solicitado e tentando armazená-los em cache no aplicativo de chamada. 

- Quando ocorre Key Vault limitação, adapte o código solicitante para usar uma retirada exponencial para tentar novamente. O algoritmo é explicado aqui: [como restringir seu aplicativo](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Se o número de solicitações não puder ser reduzido pelo cache e a retirada cronometrada não funcionar, considere dividir as chaves em vários cofres de chaves. O limite de serviço para uma única assinatura é cinco vezes o limite individual de Key Vault. Se estiver usando mais de 5 cofres de chaves, deve-se considerar o uso de várias assinaturas. 

Diretrizes detalhadas, incluindo solicitação para aumentar os limites, podem ser encontradas aqui: [diretrizes de limitação de Key Vault](overview-throttling.md)
