---
title: Práticas recomendadas para o Cache do Azure para Redis
description: Saiba como usar o Cache do Azure para Redis efetivamente seguindo estas práticas recomendadas.
services: cache
documentationcenter: na
author: joncole
manager: jhubbard
editor: tysonn
ms.assetid: 3e4905e3-89e3-47f7-8cfb-12caf1c6e50e
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 06/21/2019
ms.author: joncole
ms.openlocfilehash: bdc75033e0aa2e401a511789728feef3248d46ad
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452456"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Práticas recomendadas para o Cache do Azure para Redis 
Ao seguir essas práticas recomendadas, você pode ajudar a maximizar o desempenho e o uso econômico de seu Cache do Azure para instância do Redis.

## <a name="configuration-and-concepts"></a>Configuração e conceitos
 * **Use a camada Standard ou Premium para sistemas de produção.**  A camada básica é um sistema de nó único com nenhuma replicação de dados e não há SLA. Além disso, use pelo menos um cache C1.  Caches C0 servem para cenários de desenvolvimento/teste simples, pois eles têm um núcleo de CPU compartilhado, pouca memória e estão sujeitos a problemas de "vizinho barulhento".

 * **Lembre-se de que o Redis é um repositório de dados na memória.**  [Este artigo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) descreve alguns cenários onde pode ocorrer perda de dados.

 * **Desenvolva seu sistema, de modo que ele pode lidar com blips de conexão** [devido à aplicação de patch e failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

 * **Configurar seu [configuração maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para melhorar a capacidade de resposta do sistema** sob condições de pressão de memória.  Essa configuração é especialmente importante para cargas de trabalho pesadas de gravação ou se você estiver armazenando valores maiores (100 KB ou mais) no Redis.  Eu seria recomendável iniciar com 10% do tamanho do seu cache, em seguida, aumentar se você tiver cargas pesadas de gravação. Ver [algumas considerações](cache-how-to-troubleshoot.md#considerations-for-memory-reservations) ao selecionar um valor.

 * **O redis funciona melhor com valores menores**, portanto, considere talhar dados maiores em várias chaves.  Na [discussão do Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/), algumas considerações estão relacionadas que você deve considerar cuidadosamente.  Leia [este artigo](cache-how-to-troubleshoot.md#large-requestresponse-size) para obter um problema de exemplo que pode ser causado por valores grandes.

 * **Localize sua instância de cache e o seu aplicativo na mesma região.**  Conectar-se a um cache em uma região diferente significativamente pode aumentar a latência e reduzir a confiabilidade.  Embora você pode se conectar de fora do Azure, isso não recomendável *especialmente ao usar o Redis como um cache*.  Se você estiver usando o Redis como apenas um repositório de chave/valor, a latência pode não ser a principal preocupação. 

 * **Reutilizar conexões** -criando novas conexões é caro e aumenta a latência, portanto, reutilizar conexões tanto quanto possíveis. Se você optar por criar novas conexões, certifique-se de fechar as conexões antigas antes de liberá-los (mesmo em idiomas de memória gerenciada, como .NET ou Java).

 * **Configurar a biblioteca de cliente para usar um *tempo limite de conexão* de pelo menos 15 segundos**, dando a hora do sistema para conectar-se até mesmo sob condições de CPU mais alta.  Um valor de tempo limite de conexão pequeno não garante que a conexão foi estabelecida no intervalo de tempo.  Se algo dá errado (CPU de alto do cliente, da CPU do servidor de alta e assim por diante) e, em seguida, um valor de tempo limite de conexão curtas causará a falha da tentativa de conexão. Esse comportamento geralmente faz um pior situação ruim.  Em vez de ajudá-lo, tempos limite menor agravados o problema, forçando o sistema para reiniciar o processo de tentar se reconectar, que pode levar a um *conectar-se -> fail -> repetição* loop. Em geral, é recomendável que você deixe seu tempo limite de conexão a 15 segundos ou mais. É melhor deixar que a tentativa de conexão bem-sucedida após 15 ou 20 segundos do que para que ele falhe rapidamente apenas para tentar novamente. Um loop de repetição desse tipo pode causar sua interrupção durar mais do que se você permitir que o sistema simplesmente assumir mais inicialmente.  
     > [!NOTE]
     > Este guia é específico para o *tentativa de conexão* e não estão relacionados ao tempo em que você está disposto a esperar para uma *operação* , como GET ou SET para concluir.
 

 * **Evitar comandos caros** -alguns redis operações, como o [comando chaves](https://redis.io/commands/keys), são *muito* caro e deve ser evitado.  Para obter mais informações, consulte [algumas considerações para comandos caros](cache-how-to-troubleshoot.md#expensive-commands)


 
## <a name="memory-management"></a>Gerenciamento de memória
Há várias coisas relacionadas ao uso de memória dentro de sua instância de servidor do Redis que você talvez queira considerar.  Aqui estão alguns:

 * **Escolha uma [política de remoção](https://redis.io/topics/lru-cache) que funciona para seu aplicativo.**  É a política padrão do Redis do Azure *volatile-lru*, o que significa que somente as chaves com um TTL valor conjunto serão qualificada para remoção.  Se nenhuma chave tem um valor TTL, o sistema não remove todas as chaves.  Se você deseja que o sistema para permitir que qualquer chave a ser removido se estiver sob pressão de memória e, em seguida, você talvez queira considerar a *allkeys-lru* política.

 * **Defina um valor de expiração em suas chaves.**  Isso removerá as chaves de forma proativa, em vez de esperar até que haja pressão de memória.  Quando a remoção iniciado devido à pressão de memória, poderá causar uma carga adicional no seu servidor.  Para obter mais informações, consulte a documentação para o [expirar](https://redis.io/commands/expire) e [ExpireAt](https://redis.io/commands/expireat) comandos.
 
## <a name="client-library-specific-guidance"></a>Diretrizes específicas de biblioteca de cliente
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - qual cliente devo usar?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Lettuce (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Provedor de estado de sessão Asp.Net](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Quando é seguro tentar novamente?
Infelizmente, há uma resposta fácil.  Cada aplicativo precisa decidir quais operações podem ser repetidas e quais não podem.  Cada operação tem diferentes requisitos e dependências entre chaves.  Aqui estão algumas coisas que você pode considerar:

 * Você pode obter erros do lado do cliente, embora o Redis executou o comando solicitado que ele seja executado com êxito.  Por exemplo:
     - Tempos limite é um conceito do lado do cliente.  Se a operação atingiu o servidor, o servidor será executado o comando mesmo se o cliente desiste espera.  
     - Quando ocorre um erro sobre a conexão de soquete, não é possível saber se a operação foi realmente executado no servidor.  Por exemplo, o erro de conexão pode ocorrer depois que a solicitação é processada pelo servidor, mas antes que a resposta é recebida pelo cliente.
 *  Como o meu aplicativo reagir se eu acidentalmente executar a mesma operação duas vezes?  Por exemplo, e se eu incrementar um inteiro duas vezes em vez de apenas uma vez?  Meu aplicativo está gravando para a mesma chave de vários locais?  E se a minha lógica de repetição substitui um valor definido por alguma outra parte do meu aplicativo?

Se você quiser testar como seu código funciona em condições de erro, considere usar o [reinicializar recurso](cache-administration.md#reboot). Isso permite que você veja como blips de conexão afetam seu aplicativo.

## <a name="performance-testing"></a>Testes de desempenho
 * **Iniciar usando `redis-benchmark.exe`**  para ter uma ideia de taxa de transferência/latência possível antes de escrever seu próprio desempenho dos testes.  Documentação do redis-benchmark pode ser [encontradas aqui](http://redis.io/topics/benchmarks).  Observe que redis-benchmark não dá suporte a SSL, para que você precise [habilitar a porta não SSL por meio do Portal](cache-configure.md#access-ports) antes de executar o teste.  [Uma versão compatível do windows do redis benchmark.exe pode ser encontrada aqui](https://github.com/MSOpenTech/redis/releases)
 * O cliente usado para teste de VM deve estar **na mesma região** que sua instância do cache Redis.
 * **É recomendável usar séries de VM Dv2** para seu cliente pois ela tem hardware melhor e fornecerá os melhores resultados.
 * Verifique se o cliente tem de VM que você use **pelo menos largura de banda e a computação* como o cache está sendo testado. 
 * **Habilite o VRSS** no computador cliente, se você estiver usando o Windows.  [Consulte aqui para obter detalhes](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Exemplo de script do powershell:
     >PowerShell -ExecutionPolicy Unrestricted Enable-NetAdapterRSS -Name (    Get-NetAdapter).Name 
     
 * **Considere o uso de instâncias do Redis nível Premium**.  Esses tamanhos de cache terão melhor latência de rede e de taxa de transferência porque está em execução em hardware melhor para CPU e rede.
 
     > [!NOTE]
     > Nossos resultados de desempenho observados [publicados aqui](cache-faq.md#azure-cache-for-redis-performance) para sua referência.   Além disso, lembre-se de que o SSL/TLS adiciona alguma sobrecarga, para que você pode obter diferentes latências de e/ou a taxa de transferência, se você estiver usando a criptografia de transporte.
 
### <a name="redis-benchmark-examples"></a>Exemplos de redis-Benchmark
**Configuração de pré-teste**: Isso irá preparar a instância do cache com os dados necessários para a latência e taxa de transferência de testar os comandos listados abaixo.
> redis-benchmark.exe - h yourcache.redis.cache.windows.net - a Suachavedeacesso -t SET -n 10 - d 1024 

**Para testar a latência**: Isso irá testar solicitações GET que usem um conteúdo de 1K.
> redis-benchmark.exe - h yourcache.redis.cache.windows.net - a Suachavedeacesso -t GET -d 1024 - P 50 -c 4

**Para testar a taxa de transferência:** Isso usa solicitações GET de Pipelined com conteúdo de 1K.
> benchmark.exe redis - h yourcache.redis.cache.windows.net - Suachavedeacesso -t GET -n 1000000 - d 1024 - P 50 - c 50
