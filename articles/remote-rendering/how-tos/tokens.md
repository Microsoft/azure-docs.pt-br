---
title: Obter tokens de acesso de serviço
description: Descreve como criar tokens para acessar as APIs REST do ARR
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9721685fc3ccd2c1c80b55e9118d6d347cc97a9c
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/31/2020
ms.locfileid: "97830693"
---
# <a name="get-service-access-tokens"></a>Obter tokens de acesso de serviço

O acesso às APIs REST do ARR só é concedido para usuários autorizados. Para provar sua autorização, você deve enviar um *token de acesso* junto com as solicitações REST. Esses tokens são emitidos pelo *serviço de token de segurança* (STS) no Exchange para uma chave de conta. Os tokens têm um **tempo de vida de 24 horas** e, portanto, podem ser emitidos para os usuários sem conceder acesso completo ao serviço.

Este artigo descreve como criar esse token de acesso.

## <a name="prerequisites"></a>Pré-requisitos

[Crie uma conta do arr](create-an-account.md), se você ainda não tiver uma.

## <a name="token-service-rest-api"></a>API REST do serviço de token

Para criar tokens de acesso, o *Secure token Service* fornece uma única API REST. A URL para o serviço STS depende do domínio de conta da conta de renderização remota. Ele está no formato https://sts . [ domínio de conta], por exemplo, `https://sts.southcentralus.mixedreality.azure.com`

### <a name="get-token-request"></a>Solicitação ' Get token '

| URI | Método |
|-----------|:-----------|
| /accounts/**AccountId**/token | GET |

| Cabeçalho | Valor |
|--------|:------|
| Autorização | " **AccountId**:**accountKey**" de portador |

Substitua *AccountId* e *accountKey* por seus respectivos dados.

### <a name="get-token-response"></a>Resposta de ' Get token '

| Código de status | conteúdo JSON | Comentários |
|-----------|:-----------|:-----------|
| 200 | AccessToken: cadeia de caracteres | Êxito |

| Cabeçalho | Finalidade |
|--------|:------|
| MS-CV | Esse valor pode ser usado para rastrear a chamada dentro do serviço |

## <a name="getting-a-token-using-powershell"></a>Obtendo um token usando o PowerShell

O código do PowerShell abaixo demonstra como enviar a solicitação REST necessária para o STS. Em seguida, ele imprime o token no prompt do PowerShell.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"
$accountDomain = "<account_domain_from_portal>

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.$accountDomain/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

O script apenas imprime o token na saída, de onde você pode copiar & colá-lo. Para um projeto real, você deve automatizar esse processo.

## <a name="next-steps"></a>Próximas etapas

* [Scripts de exemplo do PowerShell](../samples/powershell-example-scripts.md)
* [APIs de front-end do Azure](../how-tos/frontend-apis.md)
* [API REST de gerenciamento de sessão](../how-tos/session-rest-api.md)
