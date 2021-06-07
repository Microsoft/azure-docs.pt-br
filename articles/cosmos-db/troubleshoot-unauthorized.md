---
title: Solucionar problemas Azure Cosmos DB exceções não autorizadas
description: Saiba como diagnosticar e corrigir exceções não autorizadas.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: aa9bf1fd706ccf6064893f1141be5e5b2f185ff3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94411160"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-unauthorized-exceptions"></a>Diagnosticar e solucionar problemas Azure Cosmos DB exceções não autorizadas
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 401: a assinatura MAC encontrada na solicitação HTTP não é a mesma que a assinatura computada.
Se você recebeu a mensagem de erro 401 "a assinatura MAC encontrada na solicitação HTTP não é igual à assinatura computada", pode ser causada pelos cenários a seguir.

Para SDKs mais antigos, a exceção pode aparecer como uma exceção JSON inválida em vez da exceção correta não autorizada 401. Os SDKs mais recentes lidam corretamente com esse cenário e apresentam uma mensagem de erro válida.

## <a name="troubleshooting-steps"></a>Etapas para solucionar problemas
A lista a seguir contém causas conhecidas e soluções para exceções não autorizadas.

### <a name="the-key-wasnt-properly-rotated-is-the-most-common-scenario"></a>A chave não foi girada corretamente é o cenário mais comum
A assinatura MAC 401 é vista logo após uma rotação de chave e eventualmente é interrompida sem nenhuma alteração. 

#### <a name="solution"></a>Solução:
A chave foi girada e não segue as [práticas recomendadas](secure-access-to-data.md#key-rotation). A rotação de chave de conta Azure Cosmos DB pode levar de alguns segundos a possivelmente dias, dependendo do tamanho da conta de Azure Cosmos DB.

### <a name="the-key-is-misconfigured"></a>A chave está configurada incorretamente 
O problema de assinatura de MAC 401 será consistente e acontecerá para todas as chamadas que usam essa chave.

#### <a name="solution"></a>Solução:
A chave está configurada incorretamente no aplicativo e está usando a chave incorreta para a conta, ou a chave inteira não foi copiada.

### <a name="the-application-is-using-the-read-only-keys-for-write-operations"></a>O aplicativo está usando as chaves somente leitura para operações de gravação
O problema de assinatura de MAC 401 só ocorre para operações de gravação como criar ou substituir, mas solicitações de leitura são realizadas com sucesso.

#### <a name="solution"></a>Solução:
Alterne o aplicativo para usar uma chave de leitura/gravação para permitir que as operações sejam concluídas com êxito.

### <a name="race-condition-with-create-container"></a>Condição de corrida com criar contêiner
O problema de assinatura de MAC 401 é visto logo após a criação de um contêiner. Esse problema ocorre somente até que a criação do contêiner seja concluída.

#### <a name="solution"></a>Solução:
Há uma condição de corrida com a criação de contêiner. Uma instância do aplicativo está tentando acessar o contêiner antes que a criação do contêiner seja concluída. O cenário mais comum para essa condição de corrida é se o aplicativo estiver em execução e o contêiner for excluído e recriado com o mesmo nome. O SDK tenta usar o novo contêiner, mas a criação do contêiner ainda está em andamento para que ele não tenha as chaves.

## <a name="next-steps"></a>Próximas etapas
* [Diagnostique e solucione](troubleshoot-dot-net-sdk.md) problemas ao usar o SDK do .net Azure Cosmos DB.
* Saiba mais sobre as diretrizes de desempenho para o [.net v3](performance-tips-dotnet-sdk-v3-sql.md) e o [.net v2](performance-tips.md).
* [Diagnostique e solucione](troubleshoot-java-sdk-v4-sql.md) problemas ao usar o SDK do Azure Cosmos DB Java v4.
* Saiba mais sobre as diretrizes de desempenho para o [SDK do Java v4](performance-tips-java-sdk-v4-sql.md).