---
title: Parâmetros e cabeçalhos comuns
description: Os parâmetros e cabeçalhos comuns a todas as operações que você pode executar relacionadas aos recursos do Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d1d93bcd84fd9460e658b221089a4b24d46b0429
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "83005810"
---
# <a name="common-parameters-and-headers"></a>Parâmetros e cabeçalhos comuns

As informações a seguir são comuns a todas as operações que você pode executar relacionadas aos recursos do Key Vault:

- O `Host` cabeçalho HTTP deve estar sempre presente e deve especificar o nome do host do cofre. Exemplo: `Host: contoso.vault.azure.net`. Observe que a maioria das tecnologias de cliente preenche o `Host` cabeçalho do URI. Por exemplo, `GET https://contoso.vault.azure.net/secrets/mysecret{...}` definirá as `Host` `contoso.vault.azure.net` . Isso significa que, se você acessar Key Vault usando um endereço IP bruto como `GET https://10.0.0.23/secrets/mysecret{...}` , o valor automático do `Host` cabeçalho ficará incorreto e você terá que verificar manualmente se o `Host` cabeçalho contém o nome do host do cofre.
- Substitua `{api-version}` pela versão da api no URI.
- Substitua `{subscription-id}` pelo identificador de assinatura no URI
- Substitua `{resource-group-name}` pelo grupo de recursos. Para saber mais, consulte Usar os grupos de recursos para gerenciar seus recursos do Azure.
- Substitua `{vault-name}` pelo nome de seu cofre de chaves no URI.
- Defina o cabeçalho Content-Type como application/json.
- Defina o cabeçalho de autorização para um token Web JSON obtido do Azure Active Directory (AAD). Para obter mais informações, consulte [Autenticando](authentication-requests-and-responses.md) solicitações de Azure Resource Manager.

## <a name="common-error-response"></a>Resposta de erro comum
O serviço usará códigos de status HTTP para indicar êxito ou falha. Além disso, as falhas contêm uma resposta no seguinte formato:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Nome do elemento | Type | Descrição |
|---|---|---|
| code | string | O tipo de erro que ocorreu.|
| message | string | Uma descrição do que causou o erro. |



## <a name="see-also"></a>Consulte Também
 [Referência da API REST do Azure Key Vault](/rest/api/keyvault/)
