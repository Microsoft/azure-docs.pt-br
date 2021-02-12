---
title: Como converter formatos de token de sessão no SDK do .NET-Azure Cosmos DB
description: Saiba como converter formatos de token de sessão para garantir as compatibilidades entre diferentes versões do SDK do .NET
author: vinhms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/30/2020
ms.author: vitrinh
ms.custom: devx-track-csharp
ms.openlocfilehash: 7ed06eeb7330989b4a251dc77e516eb8ac578bff
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342038"
---
# <a name="convert-session-token-formats-in-net-sdk"></a>Converter formatos de token de sessão no SDK do .NET
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Este artigo explica como converter entre diferentes formatos de token de sessão para garantir a compatibilidade entre as versões do SDK.

> [!NOTE]
> Por padrão, o SDK mantém o controle do token de sessão automaticamente e usará o token de sessão mais recente.  Para obter mais informações, consulte [utilizar tokens de sessão](how-to-manage-consistency.md#utilize-session-tokens). As instruções neste artigo se aplicam somente às seguintes condições:
> * Sua conta de Azure Cosmos DB usa a consistência da sessão.
> * Você está gerenciando os tokens de sessão manualmente.
> * Você está usando várias versões do SDK ao mesmo tempo.

## <a name="session-token-formats"></a>Formatos de token de sessão

Há dois formatos de token de sessão: **simples** e **vetor**.  Esses dois formatos não são intercambiáveis, portanto, o formato deve ser convertido ao passar para o aplicativo cliente com versões diferentes.
- O formato de token de sessão **simples** é usado pelo SDK do .net V1 (Microsoft.Azure.DocumentDB-Version 1. x)
- O formato do token de sessão de **vetor** é usado pelo SDK do .net V2 (Microsoft.Azure.DocumentDB-Version 2. x)

### <a name="simple-session-token"></a>Token de sessão simples

Um token de sessão simples tem este formato: `{pkrangeid}:{globalLSN}`

### <a name="vector-session-token"></a>Token de sessão de vetor

Um token de sessão de vetor tem o seguinte formato: `{pkrangeid}:{Version}#{GlobalLSN}#{RegionId1}={LocalLsn1}#{RegionId2}={LocalLsn2}....#{RegionIdN}={LocalLsnN}`

## <a name="convert-to-simple-session-token"></a>Converter em token de sessão simples

Para passar um token de sessão para o cliente usando o SDK do .NET v1, use um formato de token de sessão **simples** .  Por exemplo, use o código de exemplo a seguir para convertê-lo.

```csharp
private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SessionTokenHelpers.SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInSimpleFormat;

if (sessionTokenSegments.Length == 1)
{
    // returning the same token since it already has the correct format
    sessionTokenInSimpleFormat = sessionTokenToConvert;
}
else
{
    long version = 0;
    long globalLSN = 0;

    if (!long.TryParse(sessionTokenSegments[0], out version)
        || !long.TryParse(sessionTokenSegments[1], out globalLSN))
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }

    sessionTokenInSimpleFormat = string.Format("{0}:{1}", items[0], globalLSN);
}
```

## <a name="convert-to-vector-session-token"></a>Converter em token de sessão de vetor

Para passar um token de sessão para o cliente usando o SDK do .NET v2, use o formato de token de sessão de **vetor** .  Por exemplo, use o código de exemplo a seguir para convertê-lo.

```csharp

private static readonly char[] SegmentSeparator = (new[] { '#' });
private static readonly char[] PkRangeSeparator = (new[] { ':' });

// sessionTokenToConvert = session token from previous response
string[] items = sessionTokenToConvert.Split(PkRangeSeparator, StringSplitOptions.RemoveEmptyEntries);
string[] sessionTokenSegments = items[1].Split(SegmentSeparator, StringSplitOptions.RemoveEmptyEntries);

string sessionTokenInVectorFormat;

if (sessionTokenSegments.Length == 1)
{
    long globalLSN = 0;
    if (long.TryParse(sessionTokenSegments[0], out globalLSN))
    {
        sessionTokenInVectorFormat = string.Format("{0}:-2#{1}", items[0], globalLSN);
    }
    else
    {
        throw new ArgumentException("Invalid session token format", sessionTokenToConvert);
    }
}
else
{
    // returning the same token since it already has the correct format
    sessionTokenInVectorFormat = sessionTokenToConvert;
}
```

## <a name="next-steps"></a>Próximas etapas

Leia os seguintes artigos:

* [Usar tokens de sessão para gerenciar a consistência no Azure Cosmos DB](how-to-manage-consistency.md#utilize-session-tokens)
* [Escolher o nível de consistência correto no Azure Cosmos DB](./consistency-levels.md)
* [Compensações de consistência, disponibilidade e desempenho no Azure Cosmos DB](./consistency-levels.md)
* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](./consistency-levels.md)