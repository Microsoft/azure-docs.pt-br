---
title: Exemplos do Cache do Azure para Redis | Microsoft Docs
description: Saiba como usar o Cache Redis do Azure
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: multiple
ms.topic: article
origin.date: 01/23/2017
ms.date: 02/27/2019
ms.author: v-junlch
ms.openlocfilehash: 73c771ab18d1cc2944298818c1cab90eb2f277ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829796"
---
# <a name="azure-cache-for-redis-samples"></a>Exemplos do Cache Redis do Azure
Este tópico fornece uma lista de exemplos do Cache do Azure para Redis que abrangem cenários como conexão a um cache, leitura e gravação de dados para e de um cache e uso dos provedores do Cache do Azure para Redis do ASP.NET. Alguns dos exemplos são projetos para download, e outros fornecem orientação passo a passo e incluem snippets de código, mas não se vinculam a um projeto baixável.

## <a name="hello-world-samples"></a>Exemplos Hello world
Os exemplos nesta seção mostram as noções básicas da conexão a uma instância do Cache do Azure para Redis e da leitura e gravação de dados no cache usando diversas linguagens e clientes Redis.

O exemplo [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) mostra como executar várias operações de cache usando o cliente .NET [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis).

Este exemplo mostra como:

- Usar várias opções de conexão
- Ler e gravar objetos em cache usando operações síncronas e assíncronas
- Usar comandos MGET/MSET do Redis para retornar valores de chaves especificadas
- Executar operações transacionais do Redis
- Trabalhar com listas do Redis e conjuntos classificados
- Armazenar objetos .NET usando serializadores JsonConvert
- Usar conjuntos do Redis para implementar a marcação
- Trabalhar com o Cluster Redis

Para obter mais informações, consulte a documentação do [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) no GitHub e para ver mais cenários de uso, consulte os testes da unidade [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests).

[Como usar o Cache do Azure para Redis com Python](cache-python-get-started.md) mostra como começar com o Cache do Azure par Redis usando Python e o cliente [redis-py](https://github.com/andymccurdy/redis-py).

[Trabalhar com objetos .NET no cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) mostra uma maneira de serializar objetos .NET para que você possa gravá-los para e lê-los de uma instância do Cache do Azure para Redis. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Usar o Cache do Azure para Redis como um backplane de expansão para o ASP.NET SignalR
O exemplo [Usar o Cache do Azure para Redis como um Backplane de expansão para o ASP.NET SignalR](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) demonstra como você pode usar o Cache do Azure para Redis como um backplane SignalR. Para obter mais informações sobre o backplane, consulte [SignalR Scaleout com Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Exemplo de consulta do cliente do Cache do Azure para Redis
Esse exemplo demonstra a comparação de desempenho entre o acesso a dados de um cache e o acesso a dados do armazenamento de persistência. Esse exemplo tem dois projetos.

- [Demonstração de como o Cache do Azure para Redis pode melhorar o desempenho armazenando dados em cache](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
- [Propagar o banco de dados e o cache para a demonstração](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Estado de sessão do ASP.NET e cache de saída
O exemplo [Usar o Cache do Azure para Redis para armazenar SessionState e OutputCache do ASP.NET](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) demonstra como usar o Cache do Azure para Redis para armazenar a Sessão do ASP.NET e o Cache de Saída usando os provedores SessionState e OutputCache para Redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Gerenciar o Cache do Azure para Redis com MAML
O exemplo [Gerenciar o Cache do Azure para Redis usando Bibliotecas de Gerenciamento do Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) demonstra como você pode usar Bibliotecas de Gerenciamento do Azure para gerenciar – (criar/atualizar/excluir) o cache. 

## <a name="custom-monitoring-sample"></a>Exemplo de monitoramento personalizado
O exemplo [Acessar dados de monitoramento do Cache do Azure para Redis](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) demonstra como você pode acessar os dados de monitoramento do Cache do Azure para Redis fora do Portal do Azure.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Um clone no estilo do Twitter escrito usando PHP e Redis
O exemplo [Retwis](https://github.com/SyntaxC4-MSFT/retwis) é o Redis Hello World. É um clone de rede social mínimo no estilo do Twitter escrito usando Redis e PHP usando o cliente [Predis](https://github.com/nrk/predis) . O código-fonte é projetado para ser muito simples e, ao mesmo tempo, mostrar diferentes estruturas de dados do Redis.

## <a name="bandwidth-monitor"></a>Monitor de largura de banda
O exemplo de [Monitor de largura de banda](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) permite que você monitore a largura de banda usada no cliente. Para medir a largura de banda, execute o exemplo no computador cliente de cache, faça chamadas ao cache e observe a largura de banda relatada pelo exemplo de monitor de largura de banda.

<!-- Update_Description: update metedata properties -->

