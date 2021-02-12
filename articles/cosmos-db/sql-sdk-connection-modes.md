---
title: Azure Cosmos DB modos de conectividade do SDK do SQL
description: Saiba mais sobre os diferentes modos de conectividade disponíveis no Azure Cosmos DB SDKs do SQL.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/14/2020
ms.author: maquaran
ms.custom: devx-track-dotnet, contperf-fy21q2
ms.openlocfilehash: cf985999bac0cf45eec5d8f0f5f9e921b6f4591c
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934944"
---
# <a name="azure-cosmos-db-sql-sdk-connectivity-modes"></a>Azure Cosmos DB modos de conectividade do SDK do SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Como um cliente se conecta a Azure Cosmos DB tem implicações de desempenho importantes, especialmente para latência observada no lado do cliente. O Azure Cosmos DB oferece um modelo de programação RESTful simples e aberto por HTTPS chamado de modo Gateway. Além disso, ele oferece um protocolo TCP eficiente, que também é RESTful em seu modelo de comunicação e usa TLS para autenticação inicial e criptografia de tráfego, chamado de modo direto.

## <a name="available-connectivity-modes"></a>Modos de conectividade disponíveis

Os dois modos de conectividade disponíveis são:

  * Modo Gateway
      
    O modo de gateway tem suporte em todas as plataformas SDK. Se seu aplicativo for executado em uma rede corporativa com restrições de firewall estritas, o modo de gateway será a melhor opção porque ele usa a porta HTTPS padrão e um único ponto de extremidade DNS. No entanto, a compensação de desempenho é que o modo de gateway envolve um salto de rede adicional toda vez que os dados são lidos ou gravados em Azure Cosmos DB. Também recomendamos o modo de conexão de gateway quando você executa aplicativos em ambientes que têm um número limitado de conexões de soquete.

    Ao usar o SDK no Azure Functions, especialmente no plano de [consumo](../azure-functions/consumption-plan.md), esteja ciente dos limites atuais das [conexões](../azure-functions/manage-connections.md).

  * Modo direto

    O modo direto dá suporte à conectividade por meio do protocolo TCP e oferece melhor desempenho porque há menos saltos de rede. O aplicativo se conecta diretamente às réplicas de back-end. Atualmente, o modo direto só tem suporte em plataformas .NET e SDK Java.
     
:::image type="content" source="./media/performance-tips/connection-policy.png" alt-text="Os modos de conectividade de Azure Cosmos DB" border="false":::

Essencialmente, esses modos de conectividade condicionam a rota que as solicitações de plano de dados – leituras e gravações de documentos do computador cliente para partições no back-end de Azure Cosmos DB. O modo direto é a opção preferencial para o melhor desempenho: permite que o cliente abra conexões TCP diretamente em partições no Azure Cosmos DB back-end e solicitações de envio *diretas* ly sem intermediário. Por outro lado, no modo de gateway, as solicitações feitas pelo cliente são roteadas para um servidor chamado "gateway" no front-end de Azure Cosmos DB, que, por sua vez, circula suas solicitações para as partições apropriadas no back-end de Azure Cosmos DB.

## <a name="service-port-ranges"></a>Intervalos de porta de serviço

Ao usar o modo direto, além das portas de gateway, você precisa garantir que o intervalo de portas entre 10000 e 20000 esteja aberto porque o Azure Cosmos DB usa portas TCP dinâmicas. Ao usar o modo direto em [pontos de extremidade privados](./how-to-configure-private-endpoints.md), o intervalo completo de portas TCP-de 0 a 65535 deve ser aberto. Se essas portas não estiverem abertas e você tentar usar o protocolo TCP, você poderá receber um erro 503 Serviço indisponível.

A tabela a seguir mostra um resumo dos modos de conectividade disponíveis para várias APIs e as portas de serviço usadas para cada API:

|Modo da conexão  |Protocolo com Suporte  |SDKs com suporte  |Porta/serviço de API  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Todos os SDKs    |   SQL (443), MongoDB (10250, 10255, 10256), tabela (443), Cassandra (10350), grafo (443) <br> A porta 10250 é mapeada para uma API de Azure Cosmos DB padrão para a instância do MongoDB sem replicação geográfica. Enquanto as portas 10255 e 10256 são mapeadas para a instância que tem replicação geográfica.   |
|Direto    |     TCP    |  SDK Java do SDK do .NET    | Ao usar pontos de extremidade de serviço/público: portas no intervalo de 10000 a 20000<br>Ao usar pontos de extremidade privados: portas no intervalo de 0 a 65535 |

## <a name="next-steps"></a>Próximas etapas

Para otimizações de desempenho da plataforma SDK específica:

* [Dicas de desempenho do SDK do .NET v2](performance-tips.md)

* [Dicas de desempenho do SDK do .NET v3](performance-tips-dotnet-sdk-v3-sql.md)
 
* [Dicas de desempenho do SDK do Java v4](performance-tips-java-sdk-v4-sql.md)