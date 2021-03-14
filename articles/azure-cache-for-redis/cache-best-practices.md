---
title: Melhores práticas para o Cache do Azure para Redis
description: Saiba como usar o cache do Azure para Redis com eficiência seguindo estas práticas recomendadas.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 84a6bba390b0f6b101bd8243cf47b79af9618999
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521638"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Melhores práticas para o Cache do Azure para Redis 
Ao seguir essas práticas recomendadas, você pode ajudar a maximizar o desempenho e o uso econômico de seu cache do Azure para a instância Redis.

## <a name="configuration-and-concepts"></a>Configuração e conceitos
 * **Use a camada Standard ou Premium para sistemas de produção.**  A camada básica é um sistema de nó único sem replicação de dados e nenhum SLA. Além disso, use pelo menos um cache C1.  Os caches C0 são destinados a cenários de desenvolvimento/teste simples, pois eles têm um núcleo de CPU compartilhado, pouca memória e estão sujeitos a problemas de "vizinho ruidosa".

 * **Lembre-se de que Redis é um armazenamento de dados na memória.**  [Este artigo](cache-troubleshoot-data-loss.md) descreve alguns cenários em que podem ocorrer perda de dados.

 * **Desenvolva seu sistema de modo que ele possa lidar com o blips de conexão devido a** [patches e failover](cache-failover.md).

 * **Configure sua [configuração de MaxMemory-reservado](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) para melhorar a capacidade de resposta do sistema** em condições de pressão de memória.  Uma configuração de reserva suficiente é especialmente importante para cargas de trabalho pesadas de gravação ou se você estiver armazenando valores maiores (100 KB ou mais) em Redis. Você deve começar com 10% do tamanho do seu cache e aumentar essa porcentagem se houver cargas pesadas de gravação.

 * O **Redis funciona melhor com valores menores**, portanto, considere a possibilidade de aumentar dados maiores em várias chaves.  Nesta [discussão de Redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/), são listadas algumas considerações que você deve considerar com cuidado.  Leia [este artigo](cache-troubleshoot-client.md#large-request-or-response-size) para obter um problema de exemplo que pode ser causado por valores grandes.

 * **Localize sua instância de cache e seu aplicativo na mesma região.**  Conectar-se a um cache em uma região diferente pode aumentar significativamente a latência e reduzir a confiabilidade.  Embora você possa se conectar de fora do Azure, isso não é recomendado *especialmente ao usar Redis como um cache*.  Se você estiver usando Redis como apenas um repositório de chave/valor, a latência poderá não ser a principal preocupação. 

 * **Reutilizar conexões.**  A criação de novas conexões é cara e aumenta a latência. portanto, reutilize as conexões o máximo possível. Se você optar por criar novas conexões, certifique-se de fechar as conexões antigas antes de liberá-las (mesmo em linguagens de memória gerenciada como .NET ou Java).

* **Use o pipeline.**  Tente escolher um cliente Redis que dê suporte ao [pipeline Redis](https://redis.io/topics/pipelining) para fazer uso mais eficiente da rede para obter a melhor taxa de transferência possível.

 * **Configure sua biblioteca de cliente para usar um *tempo limite de conexão* de, pelo menos, 15 segundos**, dando à hora do sistema a conexão, mesmo sob condições de CPU mais altas.  Um valor de tempo limite de conexão pequeno não garante que a conexão seja estabelecida nesse período de tempo.  Se algo der errado (alta CPU de cliente, alta CPU de servidor e assim por diante), um valor de tempo limite de conexão curto causará falha na tentativa de conexão. Esse comportamento geralmente resulta em uma situação pior.  Em vez de ajudar, tempos limite mais curtos aggravatem o problema ao forçar o sistema a reiniciar o processo de tentativa de reconexão, o que pode levar a um loop de *repetição de falha > de > conexão* . Geralmente, é recomendável deixar o tempo limite de conexão em 15 segundos ou superior. É melhor permitir que sua tentativa de conexão seja bem-sucedida após 15 ou 20 segundos do que fazer com que ela falhe rapidamente apenas para tentar novamente. Esse loop de repetição pode fazer com que a interrupção seja mais longa do que se você deixar que o sistema simplesmente demore mais tempo inicialmente.  
     > [!NOTE]
     > Essa diretriz é específica para a *tentativa de conexão* e não está relacionada ao tempo que você deseja aguardar uma *operação* como Get ou Set como concluída.

 * **Evite operações caras** – algumas operações Redis, como o comando [Keys](https://redis.io/commands/keys) , são *muito* caras e devem ser evitadas.  Para obter mais informações, consulte algumas considerações sobre [comandos de longa execução](cache-troubleshoot-server.md#long-running-commands)

 * **Usar criptografia TLS** -o cache do Azure para Redis requer comunicações criptografadas TLS por padrão.  No momento, há suporte para as versões 1,0, 1,1 e 1,2 do TLS.  No entanto, o TLS 1,0 e o 1,1 estão em um caminho para a substituição em todo o setor, portanto, use o TLS 1,2, se possível.  Se a sua biblioteca ou ferramenta de cliente não oferecer suporte a TLS, a habilitação de conexões não criptografadas poderá ser feita [por meio das APIs de](cache-configure.md#access-ports) [Gerenciamento](/rest/api/redis/redis/update)ou portal do Azure.  Nesses casos em que as conexões criptografadas não são possíveis, colocar o cache e o aplicativo cliente em uma rede virtual seria recomendado.  Para obter mais informações sobre quais portas são usadas no cenário de cache de rede virtual, consulte esta [tabela](cache-how-to-premium-vnet.md#outbound-port-requirements).

 * **Tempo limite de ociosidade** -o Azure Redis atualmente tem 10 minutos de tempo limite de ociosidade para conexões, portanto, isso deve ser definido para menos de 10 minutos.

## <a name="memory-management"></a>Gerenciamento de memória
Há várias coisas relacionadas ao uso de memória em sua instância do servidor Redis que você talvez queira considerar.  Aqui estão algumas:

 * **Escolha uma [política de remoção](https://redis.io/topics/lru-cache) que funcione para seu aplicativo.**  A política padrão para o Azure Redis é *volátil-LRU*, o que significa que somente as chaves que têm um valor de TTL definido serão elegíveis para remoção.  Se nenhuma chave tiver um valor TTL, o sistema não removerá nenhuma chave.  Se você quiser que o sistema permita que qualquer chave seja removida se estiver sob pressão de memória, convém considerar a política *AllKeys-LRU* .

 * **Defina um valor de expiração em suas chaves.**  Uma expiração removerá as chaves proativamente, em vez de aguardar até que haja pressão de memória.  Quando a remoção é acionada devido à pressão de memória, ela pode causar carga adicional no servidor.  Para obter mais informações, consulte a documentação para os comandos [expire](https://redis.io/commands/expire) e [EXPIREAT](https://redis.io/commands/expireat) .

## <a name="client-library-specific-guidance"></a>Diretrizes específicas da biblioteca de cliente
 * [StackExchange. Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java-qual cliente devo usar?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Lettuce (Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Provedor de estado de sessão ASP.NET](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Quando é seguro tentar novamente?
Infelizmente, não há uma resposta fácil.  Cada aplicativo precisa decidir quais operações podem ser repetidas e quais não podem.  Cada operação tem requisitos e dependências entre chaves diferentes.  Aqui estão algumas coisas que você pode considerar:

 * Você pode obter erros do lado do cliente, embora Redis tenha executado com êxito o comando que você solicitou que ele fosse executado.  Por exemplo:
    - Os tempos limite são um conceito do lado do cliente.  Se a operação tiver atingido o servidor, o servidor executará o comando mesmo se o cliente desistir de espera.  
    - Quando ocorre um erro na conexão de soquete, não é possível saber se a operação realmente foi executada no servidor.  Por exemplo, o erro de conexão pode ocorrer depois que o servidor processou a solicitação, mas antes de o cliente receber a resposta.
 * Como meu aplicativo reage se eu executar acidentalmente a mesma operação duas vezes?  Por exemplo, e se eu incrementar um inteiro duas vezes em vez de uma vez?  Meu aplicativo está gravando na mesma chave de vários locais?  E se minha lógica de repetição substituir um valor definido por alguma outra parte do meu aplicativo?

Se você quiser testar como o código funciona em condições de erro, considere usar o [recurso de reinicialização](cache-administration.md#reboot). A reinicialização permite que você veja como a conexão blips afeta seu aplicativo.

## <a name="performance-testing"></a>Testes de desempenho
 * **Comece usando `redis-benchmark.exe`** para ter uma ideia de taxa de transferência/latência possível antes de escrever seus próprios testes de desempenho.  A documentação do Redis-benchmark pode ser [encontrada aqui](https://redis.io/topics/benchmarks).  Observe que Redis-benchmark não dá suporte a TLS, portanto, você precisará [habilitar a porta não TLS por meio do portal](cache-configure.md#access-ports) antes de executar o teste.  [Uma versão compatível com Windows do redis-benchmark.exe pode ser encontrada aqui](https://github.com/MSOpenTech/redis/releases)
 * A VM do cliente usada para teste deve estar **na mesma região** que a instância do cache Redis.
 * **É recomendável usar a série de VMs Dv2** para seu cliente, pois eles têm um hardware melhor e fornecerão os melhores resultados.
 * Verifique se a VM do cliente que você usa tem **pelo menos tanto de computação quanto de largura de banda* quanto o cache que está sendo testado. 
 * **Teste sob condições de failover** em seu cache. É importante garantir que você não teste o desempenho do seu cache somente sob condições de estado estável. Além disso, teste em condições de failover e meça a carga de CPU/servidor no cache durante esse tempo. Você pode iniciar um failover [reiniciando o nó primário](cache-administration.md#reboot). Isso permitirá que você veja como seu aplicativo se comporta em termos de taxa de transferência e latência durante as condições de failover (ocorre durante as atualizações e pode ocorrer durante um evento não planejado). Idealmente, você don't't deseja ver o pico de carga de CPU/servidor para mais do que dizer 80% mesmo durante um failover, pois isso pode afetar o desempenho.
 * **Alguns tamanhos de cache** são hospedados em VMs com 4 ou mais núcleos. Isso é útil para distribuir a criptografia/descriptografia TLS, bem como cargas de trabalho de conexão/desconexão TLS em vários núcleos para reduzir o uso geral da CPU nas VMs do cache.  [Consulte aqui para obter detalhes sobre tamanhos de VM e núcleos](cache-planning-faq.md#azure-cache-for-redis-performance)
 * **Habilite o VRSS** no computador cliente se você estiver no Windows.  [Consulte aqui para obter detalhes](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).  Exemplo de script do PowerShell:
     >PowerShell-ExecutionPolicy irrestrito Enable-NetAdapterRSS-Name (Get-netadapter). Nomes 

 * **Considere o uso de instâncias de Redis da camada Premium**.  Esses tamanhos de cache terão melhor latência de rede e taxa de transferência, pois estão em execução em hardware melhor para CPU e rede.

     > [!NOTE]
     > Nossos resultados de desempenho observados são [publicados aqui](cache-planning-faq.md#azure-cache-for-redis-performance) para sua referência.   Além disso, lembre-se de que o SSL/TLS adiciona alguma sobrecarga, de modo que você poderá obter latências e/ou taxa de transferência diferentes se estiver usando a criptografia de transporte.

### <a name="redis-benchmark-examples"></a>Exemplos de Redis-Benchmark
**Configuração de pré-teste**: Prepare a instância de cache com os dados necessários para os comandos de teste de latência e taxa de transferência listados abaixo.
> Redis-benchmark-h yourcache.redis.cache.windows.net-a Suachavedeacesso-t SET-n 10-d 1024 

**Para testar a latência**: teste as solicitações Get usando uma carga de 1K.
> Redis-benchmark-h yourcache.redis.cache.windows.net-a Suachavedeacesso-t GET-d 1024-P 50-c 4

**Para testar a taxa de transferência:** Solicitações GET em pipeline com carga de 1K.
> Redis-benchmark-h yourcache.redis.cache.windows.net-a Suachavedeacesso-t GET-n 1 milhão-d 1024-P 50-c 50
