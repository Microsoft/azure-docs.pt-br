---
title: Obter informações de chave de criptografia
titleSuffix: Azure Cognitive Search
description: Recupere o nome da chave de criptografia e a versão usada em um mapa de índice ou sinônimo para que você possa gerenciar a chave em Azure Key Vault.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/01/2020
ms.openlocfilehash: 37ff94608e9756142f70a4f3c64d0a6f7eeea685
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88932892"
---
# <a name="get-customer-managed-key-information-from-indexes-and-synonym-maps"></a>Obter informações de chave gerenciadas pelo cliente de índices e mapas de sinônimos

No Azure Pesquisa Cognitiva, as chaves de criptografia gerenciadas pelo cliente são criadas, armazenadas e gerenciadas no Azure Key Vault. Se você precisar determinar se um objeto está criptografado ou qual nome ou versão da chave foi usada, use a API REST ou um SDK para recuperar a propriedade **encryptionKey** de uma definição de mapa de índice ou sinônimo. 

É recomendável [habilitar o log](../key-vault/general/logging.md) em Key Vault para que você possa monitorar o uso da chave.

## <a name="get-the-admin-api-key"></a>Obter a chave de API de administração

Para obter as definições de objeto de um serviço de pesquisa, você precisará autenticar com direitos de administrador. A maneira mais fácil de obter a chave de API de administração é por meio do Portal.

1. Entre no [portal do Azure](https://portal.azure.com/) e abra a página Visão geral do serviço de pesquisa.

1. No lado esquerdo, clique em **chaves** e copie uma API de administração. Uma chave de administração é necessária para recuperação de mapa de índice e sinônimo.

Para as etapas restantes, alterne para o PowerShell e a API REST. O portal não mostra mapas de sinônimo nem as propriedades de chave de criptografia de índices.

## <a name="use-powershell-and-rest"></a>Usar o PowerShell e o REST

Execute os comandos a seguir para configurar as variáveis e obter as definições de objeto.

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe qual chave de criptografia e versão é usada, você pode gerenciar a chave em Azure Key Vault ou verificar outras definições de configuração.

+ [Início Rápido: definir e recuperar um segredo do Azure Key Vault usando o PowerShell](../key-vault/secrets/quick-create-powershell.md)

+ [Configurar chaves gerenciadas pelo cliente para criptografia de dados no Azure Pesquisa Cognitiva](search-security-manage-encryption-keys.md)