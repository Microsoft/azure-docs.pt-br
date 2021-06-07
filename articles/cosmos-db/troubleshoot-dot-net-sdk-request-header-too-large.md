---
title: Solucionar problemas de uma mensagem "cabeçalho de solicitação muito grande" ou 400 solicitação inadequada no Azure Cosmos DB
description: Saiba como diagnosticar e corrigir a exceção de cabeçalho de solicitação muito grande.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: a14503bc56777563b6360143efaa86a136b22278
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340508"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Diagnosticar e solucionar problemas Azure Cosmos DB mensagem "cabeçalho de solicitação muito grande"
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A mensagem "cabeçalho de solicitação muito grande" é lançada com um código de erro HTTP 400. Esse erro ocorre se o tamanho do cabeçalho da solicitação cresceu tão grande que ele excede o tamanho máximo permitido. Recomendamos que você use a versão mais recente do SDK. Use pelo menos a versão 3. x ou 2. x, pois essas versões adicionam o rastreamento de tamanho de cabeçalho à mensagem de exceção.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A mensagem "cabeçalho de solicitação muito grande" ocorrerá se a sessão ou o token de continuação for muito grande. As seções a seguir descrevem a causa do problema e sua solução em cada categoria.

### <a name="session-token-is-too-large"></a>O token de sessão é muito grande

#### <a name="cause"></a>Causa:
Uma solicitação inadequada 400 provavelmente ocorre porque o token de sessão é muito grande. Se as instruções a seguir forem verdadeiras, o token de sessão será muito grande:

* O erro ocorre em operações de ponto como criar, ler e atualizar onde não há um token de continuação.
* A exceção foi iniciada sem fazer nenhuma alteração no aplicativo. O token de sessão aumenta conforme o número de partições aumenta no contêiner. O número de partições aumenta conforme a quantidade de dados aumenta ou se a taxa de transferência for aumentada.

#### <a name="temporary-mitigation"></a>Mitigação temporária: 
Reinicie o aplicativo cliente para redefinir todos os tokens de sessão. Eventualmente, o token de sessão aumentará de volta para o tamanho anterior que causou o problema. Para evitar esse problema completamente, use a solução na próxima seção.

#### <a name="solution"></a>Solução:
1. Siga as orientações nos artigos de dicas de desempenho do .NET [v3](performance-tips-dotnet-sdk-v3-sql.md) ou [.net v2](performance-tips.md) . Converta o aplicativo para usar o modo de conexão direta com o protocolo TCP. O modo de conexão direta com o protocolo TCP não tem a restrição de tamanho de cabeçalho como o protocolo HTTP e, portanto, evita esse problema. Certifique-se de usar a versão mais recente do SDK, que tem uma correção para operações de consulta quando a interoperabilidade do serviço não está disponível.
1. Se o modo de conexão direta com o protocolo TCP não for uma opção para sua carga de trabalho, atenue-o alterando o [nível de consistência do cliente](how-to-manage-consistency.md). O token de sessão é usado somente para consistência de sessão, que é o nível de consistência padrão para Azure Cosmos DB. Outros níveis de consistência não usam o token de sessão.

### <a name="continuation-token-is-too-large"></a>O token de continuação é muito grande

#### <a name="cause"></a>Causa:
A solicitação inadequada 400 ocorre em operações de consulta em que o token de continuação é usado se o token de continuação ficou muito grande ou se consultas diferentes têm tamanhos de token de continuação diferentes.
    
#### <a name="solution"></a>Solução:
1. Siga as orientações nos artigos de dicas de desempenho do .NET [v3](performance-tips-dotnet-sdk-v3-sql.md) ou [.net v2](performance-tips.md) . Converta o aplicativo para usar o modo de conexão direta com o protocolo TCP. O modo de conexão direta com o protocolo TCP não tem a restrição de tamanho de cabeçalho como o protocolo HTTP e, portanto, evita esse problema. 
1. Se o modo de conexão direta com o protocolo TCP não for uma opção para sua carga de trabalho, defina a `ResponseContinuationTokenLimitInKb` opção. Você pode encontrar essa opção no `FeedOptions` em v2 ou `QueryRequestOptions` em v3.

## <a name="next-steps"></a>Próximas etapas
* [Diagnostique e solucione](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB.
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md).
