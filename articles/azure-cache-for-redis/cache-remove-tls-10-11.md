---
title: Remover o TLS 1.0 e 1.1 do uso com o Cache do Azure para Redis
description: Saiba como remover o TLS 1.0 e 1.1 do seu aplicativo ao se comunicar com o Cache do Azure para Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: fd0e6f893d152259c46ff06e9ec20af54395c5e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994376"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Remover o TLS 1.0 e 1.1 do uso com o Cache do Azure para Redis

Há uma tendência em todo o setor para o uso exclusivo do protocolo TLS versão 1.2 ou posterior. As versões 1.0 e 1.1 do TLS são conhecidas como suscetíveis a ataques como BEAST e POODLE e têm pontos fracos de CVE (vulnerabilidades e exposições comuns). Eles também não dão suporte aos métodos de criptografia modernos e conjuntos de codificação recomendados pelos padrões de conformidade do PCI (setor de cartão de pagamento). Este [blog de segurança do TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) explica algumas dessas vulnerabilidades mais detalhadamente.

Como parte desse esforço, faremos as seguintes alterações no Cache do Azure para Redis:

* **Fase 1:** Configuraremos a versão padrão mínima do TLS como 1,2 para instâncias de cache recém-criadas (anteriormente, era TLS 1,0). As instâncias de cache existentes não serão atualizadas por enquanto. Você ainda pode usar o portal do Azure ou outras APIs de gerenciamento para [alterar a versão mínima do TLS](cache-configure.md#access-ports) para 1,0 ou 1,1 para compatibilidade com versões anteriores, se necessário.
* **Fase 2:** Vamos parar de dar suporte a TLS 1,1 e a TLS 1,0. Após essa alteração, seu aplicativo deve usar o TLS 1,2 ou posterior para se comunicar com o cache. Espera-se que o serviço de cache do Azure para Redis esteja disponível enquanto os migramos para dar suporte apenas a TLS 1,2 ou posterior.

  > [!NOTE]
  > A fase 2 está temporariamente planejada para começar não antes de 31 de dezembro de 2020. No entanto, é altamente recomendável que você comece a planejar essa alteração agora e atualize proativamente os clientes para dar suporte a TLS 1,2 ou posterior. 
  >

Como parte dessa alteração, também removeremos o suporte para conjuntos de criptografia mais antigos que não são seguros. Nossos pacotes criptografia com suporte serão restritos aos seguintes pacotes quando o cache for configurado com um mínimo de TLS 1,2:

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Este artigo fornece diretrizes gerais sobre como detectar dependências dessas versões anteriores do TLS e removê-las do seu aplicativo.

As datas em que essas alterações entram em vigor são:

| Nuvem                | Data de início da fase 1 | Data de início da fase 2         |
|----------------------|--------------------|----------------------------|
| Azure (global)       |  13 de janeiro de 2020  | Adiado devido a COVID-19  |
| Azure Government     |  13 de março de 2020    | Adiado devido a COVID-19  |
| Azure Alemanha        |  13 de março de 2020    | Adiado devido a COVID-19  |
| Azure China 21Vianet |  13 de março de 2020    | Adiado devido a COVID-19  |

> [!NOTE]
> A fase 2 está temporariamente planejada para começar não antes de 31 de dezembro de 2020. Este artigo será atualizado quando datas específicas forem definidas.
>

## <a name="check-whether-your-application-is-already-compliant"></a>Verificar se o seu aplicativo já está em conformidade

A maneira mais fácil de descobrir se seu aplicativo funcionará com o TLS 1,2 é definir o valor de **versão mínima do TLS** como TLS 1,2 em um cache de teste ou de preparo e, em seguida, executar testes. A configuração **Versão mínima do TLS** está nas [Configurações avançadas](cache-configure.md#advanced-settings) da instância de cache no portal do Azure.  Se o aplicativo continuar funcionando conforme o esperado após essa alteração, provavelmente ele está em conformidade. Talvez seja necessário configurar a biblioteca de cliente do Redis usada pelo seu aplicativo para habilitar o TLS 1,2 para se conectar ao cache do Azure para Redis.

## <a name="configure-your-application-to-use-tls-12"></a>Configurar seu aplicativo para usar o TLS 1.2

A maioria dos aplicativos, para lidar com a comunicação com os respectivos caches, usa bibliotecas de clientes do Redis. Aqui estão as instruções para configurar algumas das bibliotecas de clientes populares, em várias linguagens de programação e estruturas, para usar o TLS 1.2.

### <a name="net-framework"></a>.NET Framework

Os clientes do .NET do Redis usam a versão mais antiga do TLS por padrão no .NET Framework 4.5.2 ou anterior e usam a versão mais recente do TLS no .NET Framework 4.6 ou posterior. Se você estiver usando uma versão mais antiga do .NET Framework, poderá habilitar o TLS 1.2 manualmente:

* **StackExchange.Redis:** Defina `ssl=true` e `sslprotocols=tls12` na cadeia de conexão.
* **ServiceStack.Redis:** Siga as instruções de [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) e exija a versão 5.6 ou posterior do ServiceStack.Redis.

### <a name="net-core"></a>.NET Core

Os clientes Redis .NET Core assumem como padrão a versão TLS padrão do sistema operacional que, obviamente, depende do sistema operacional propriamente dito. 

Dependendo da versão do sistema operacional e de todos os patches que foram aplicados, a versão efetiva padrão do TLS pode variar. Embora haja uma fonte de informações sobre isso, [aqui](/dotnet/framework/network-programming/tls#support-for-tls-12) está um artigo para o Windows. 

No entanto, se você estiver usando um sistema operacional antigo ou apenas quiser ter certeza, é recomendável configurar a versão de TLS preferencial manualmente por meio do cliente.


### <a name="java"></a>Java

Os clientes do Java do Redis usam o TLS 1.0 na versão 6 ou anterior do Java. Jedis, Lettuce e Redisson não poderão se conectar ao Cache do Azure para Redis se o TLS 1.0 estiver desabilitado no cache. Atualize sua estrutura Java para usar novas versões do TLS.

Para o Java 7, os clientes Redis não usam o TLS 1.2 por padrão, mas podem ser configurados para ele. O Jedis permite que você especifique as configurações de TLS subjacentes com o seguinte snippet de código:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

Os clientes Lettuce e Redisson ainda não dão suporte à especificação da versão do TLS, portanto, eles serão interrompidos se o cache aceitar somente conexões pelo TLS 1.2. As correções para esses clientes estão sendo examinadas, portanto, verifique com esses pacotes uma versão atualizada que tenha esse suporte.

No Java 8, o TLS 1.2 é usado por padrão e não deve exigir atualizações para a configuração do cliente na maioria dos casos. Para maior segurança, teste seu aplicativo.

### <a name="nodejs"></a>Node.js

O Node Redis e o IORedis usam TLS 1.2 por padrão.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* Versões anteriores ao PHP 7: O Predis dá suporte apenas ao TLS 1.0. Essas versões não funcionam com o TLS 1.2, exigindo atualização para que seja possível usá-lo.
 
* PHP 7.0 a PHP 7.2.1: O Predis usa apenas o TLS 1.0 ou 1.1 por padrão. Você pode usar a solução alternativa a seguir para usar o TLS 1.2. Especifique o TLS 1.2 ao criar a instância do cliente:

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7.3 e versões posteriores: O Predis usa a versão mais recente do TLS.

#### <a name="phpredis"></a>PhpRedis

O PhpRedis não é compatível com TLS em nenhuma versão do PHP.

### <a name="python"></a>Python

O Redis-py usa o TLS 1.2 por padrão.

### <a name="go"></a>GO

O Redigo usa o TLS 1.2 por padrão.

## <a name="additional-information"></a>Informações adicionais

- [Como configurar o Cache do Azure para Redis](cache-configure.md)