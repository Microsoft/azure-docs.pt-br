---
title: Migre seu aplicativo para usar o SDK do .NET Azure Cosmos DB 2,0 (Microsoft. Azure. Cosmos)
description: Saiba como atualizar seu aplicativo .NET existente do SDK v1 para a API do .NET SDK v2 para núcleo (SQL).
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/15/2020
ms.openlocfilehash: 88c40452da72ed4ab43d2d7613636136a5b78cfe
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94550044"
---
# <a name="migrate-your-application-to-use-the-azure-cosmos-db-net-sdk-v2"></a>Migrar seu aplicativo para usar o SDK do .NET Azure Cosmos DB v2

> [!IMPORTANT]
> É importante observar que a V3 do SDK do .NET está disponível no momento e um plano de migração de v2 para v3 está disponível [aqui](migrate-dotnet-v3.md). Para saber mais sobre o SDK do .NET Azure Cosmos DB v2, consulte as [notas de versão](sql-api-sdk-dotnet.md), o [repositório GitHub do .net](https://github.com/Azure/azure-cosmos-dotnet-v2), as dicas de [desempenho](performance-tips.md)do SDK do .net v2 e o guia de [solução de problemas](troubleshoot-dot-net-sdk.md).
>

Este artigo destaca algumas das considerações para atualizar seu aplicativo .NET v1 existente para Azure Cosmos DB API .NET SDK v2 para núcleo (SQL). Azure Cosmos DB SDK do .NET v2 corresponde ao `Microsoft.Azure.DocumentDB` namespace. Você pode usar as informações fornecidas neste documento se estiver migrando seu aplicativo de qualquer uma das seguintes Azure Cosmos DB plataformas .NET para usar o SDK v2 `Microsoft.Azure.Cosmos` :

* SDK do Azure Cosmos DB .NET Framework v1 para API do SQL
* Azure Cosmos DB SDK do .NET Core v1 para a API do SQL

## <a name="whats-available-in-the-net-v2-sdk"></a>O que está disponível no SDK do .NET v2

O SDK do v2 contém muitas melhorias de usabilidade e desempenho, incluindo:

* Suporte para o modo TCP direto para clientes não Windows
* Suporte para gravação de várias regiões
* Melhorias no desempenho da consulta
* Suporte para coleções geoespaciais/geométricas e indexação
* Melhorias aprimoradas para diagnóstico de transporte direto/TCP
* Atualizações na pilha de transporte TCP direta para reduzir o número de conexões estabelecidas
* Melhorias na redução de latência no RequestTimeout

A maior parte da lógica de repetição e dos níveis inferiores do SDK permanece muito inalterada.

## <a name="why-migrate-to-the-net-v2-sdk"></a>Por que migrar para o SDK do .NET v2

Além das inúmeras melhorias de desempenho, os novos investimentos em recursos feitos no SDK mais recente não serão portados de volta para as versões mais antigas.

Além disso, os SDKs mais antigos serão substituídos por versões mais recentes e o SDK v1 entrará em [modo de manutenção](sql-api-sdk-dotnet.md). Para obter a melhor experiência de desenvolvimento, é recomendável migrar seu aplicativo para uma versão mais recente do SDK.

## <a name="major-changes-from-v1-sdk-to-v2-sdk"></a>Principais alterações do SDK v1 para o SDK v2

### <a name="direct-mode--tcp"></a>Modo direto + TCP

O SDK do .NET v2 agora dá suporte ao modo direto e de gateway. O modo direto dá suporte à conectividade por meio do protocolo TCP e oferece melhor desempenho à medida que se conecta diretamente às réplicas de back-end com menos saltos de rede.

Para obter mais detalhes, leia o [guia modos de conectividade do SDK do SQL Azure Cosmos DB](sql-sdk-connection-modes.md).

### <a name="session-token-formatting"></a>Formatação do token de sessão

O SDK v2 não usa mais o formato de token de sessão *simples* , como usado em v1, em vez disso, o SDK usa a formatação de *vetor* . O formato deve ser convertido ao passar para o aplicativo cliente com versões diferentes, pois os formatos não são intercambiáveis.

Para obter mais informações, consulte [convertendo formatos de token de sessão no SDK do .net](how-to-convert-session-token.md).

### <a name="using-the-net-change-feed-processor-sdk"></a>Usando o SDK do processador do feed de alterações do .NET

A biblioteca do processador do feed de alterações do .NET 2.1. x requer o `Microsoft.Azure.DocumentDB` 2,0 ou posterior.

A biblioteca 2.1. x tem as seguintes alterações de chave:

* Aprimoramentos de estabilidade e diagnóstico
* Tratamento aprimorado de erros e exceções
* Suporte adicional para coleções de concessão particionadas
* Extensões avançadas para implementar a `ChangeFeedDocument` interface e a classe para rastreamento e tratamento de erros adicionais
* Suporte adicionado para usar um repositório personalizado para persistir tokens de continuação por partição

Para obter mais informações, consulte as [notas de versão](sql-api-sdk-dotnet-changefeed.md)da biblioteca do processador do feed de alterações.

### <a name="using-the-bulk-executor-library"></a>Usando a biblioteca de executores em massa

Atualmente, a biblioteca de executores em massa V2 tem uma dependência no SDK do .NET Azure Cosmos DB 2.5.1 ou posterior.

Para obter mais informações, consulte o [Azure Cosmos DB visão geral da biblioteca de executores em massa](bulk-executor-overview.md) e as [notas de versão](sql-api-sdk-bulk-executor-dot-net.md)da biblioteca de executores do .net.

## <a name="next-steps"></a>Próximas etapas

* Leia sobre [dicas de desempenho adicionais](sql-api-get-started.md) usando o Azure Cosmos DB para a API do SQL v2 para otimizar seu aplicativo para alcançar o desempenho máximo
* Saiba mais sobre [o que você pode fazer com o SDK v2](sql-api-dotnet-samples.md)