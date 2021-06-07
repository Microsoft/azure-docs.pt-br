---
title: API REST de configuração de Azure App-consistência
description: Páginas de referência para garantir a consistência em tempo real usando a API REST de configuração de Azure App
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b324d23ce9abc1eb3893f316365aff828de2063d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932618"
---
# <a name="real-time-consistency"></a>Consistência em tempo real

Devido à natureza de alguns sistemas distribuídos, a consistência em tempo real entre as solicitações é difícil de impor implicitamente. Uma solução é permitir o suporte de protocolo na forma de vários tokens de sincronização. Os tokens de sincronização são opcionais.

## <a name="initial-request"></a>Solicitação inicial

Para garantir a consistência em tempo real entre diferentes instâncias de cliente e solicitações, use `Sync-Token` cabeçalhos de solicitação e resposta opcionais.

Sintaxe:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Parâmetro|Descrição|
|--|--|
| `<id>` | ID do token (opaco) |
| `<value>` | Valor do token (opaco). Permite cadeia de caracteres codificada em base64. |
| `<sn>` | Número de sequência de token (versão). Superior significa uma versão mais recente do mesmo token. Permite uma melhor simultaneidade e cache de cliente. O cliente pode optar por usar apenas a última versão do token, pois as versões de token são inclusivas. Esse parâmetro não é necessário para solicitações. |

## <a name="response"></a>Resposta

O serviço fornece um `Sync-Token` cabeçalho com cada resposta.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Solicitações subsequentes

Qualquer solicitação subsequente é garantida a resposta consistente em tempo real em relação ao fornecido `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Se você omitir o `Sync-Token` cabeçalho da solicitação, é possível que o serviço responda com os dados armazenados em cache durante um curto período de tempo (até alguns segundos), antes que ele seja liquidado internamente. Esse comportamento pode causar leituras inconsistentes caso as alterações tenham ocorrido imediatamente antes da leitura.

## <a name="multiple-sync-tokens"></a>Vários tokens de sincronização

O servidor pode responder com vários tokens de sincronização para uma única solicitação. Para manter a consistência em tempo real para a próxima solicitação, o cliente deve responder com todos os tokens de sincronização recebidos. Vários valores de cabeçalho devem ser separados por vírgulas.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
