---
title: Usar o Redis-CLI com o cache do Azure para Redis
description: Saiba como usar *redis-cli.exe* como uma ferramenta de linha de comando para interagir com um cache do Azure para Redis como um cliente
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 3a1e1cdb7706cb284da57e10e35afe3420deaf75
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375299"
---
# <a name="use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Use a ferramenta de linha de comando Redis com o cache do Azure para Redis

O *redis-cli.exe* é uma ferramenta de linha de comando popular para interagir com um Cache do Azure para Redis como um cliente. Essa ferramenta também está disponível para uso com Cache do Azure para Redis.

A ferramenta está disponível para as plataformas Windows baixando as [Ferramentas de linha de comando Redis para Windows](https://github.com/MSOpenTech/redis/releases/). 

Se você quiser executar a ferramenta de linha de comando em outra plataforma, baixe o cache do Azure para Redis de [https://redis.io/download](https://redis.io/download) .

## <a name="gather-cache-access-information"></a>Coletar informações de acesso do cache

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode coletar as informações necessárias para acessar o cache usando três métodos:

1. Usando a CLI do Azure [az redis list-keys](/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Com o Azure PowerShell usando [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Usando o portal do Azure.

Nesta seção, você vai recuperar as chaves do Portal do Azure.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Habilitar o acesso para redis-cli.exe

Com o cache do Azure para Redis, somente a porta TLS (6380) é habilitada por padrão. A `redis-cli.exe` ferramenta de linha de comando não dá suporte a TLS. Você tem duas opções de configuração para usá-lo:

1. [Habilitar a porta não TLS (6379)](cache-configure.md#access-ports)  -  **Essa configuração não é recomendada** porque, nessa configuração, as chaves de acesso são enviadas via TCP em texto não criptografado. Essa alteração pode comprometer o acesso ao seu cache. O único cenário em que você pode considerar essa configuração é quando você está acessando apenas um cache de teste.

2. Baixar e instalar o [stunnel](https://www.stunnel.org/downloads.html).

    Execute **stunnel GUI Start** para iniciar o servidor.

    Clique com o botão direito no ícone da barra de tarefas para o servidor stunnel e clique em **Mostrar janela de log**.

    No menu da janela de log do stunnel, clique em **configuração**  >  **Editar configuração** para abrir o arquivo de configuração atual.

    Adicione a seguinte entrada para *redis-cli.exe* sob a seção **Definições de serviço**. Insira o nome real do cache em vez de `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Salve e feche o arquivo de configuração. 
  
    No menu da janela de log do stunnel, clique em **configuração**  >  **recarregar configuração**.


## <a name="connect-using-the-redis-command-line-tool"></a>Conecte-se usando a ferramentas de linha de comando do Redis.

Ao usar stunnel, execute *redis-cli.exe* e passe apenas sua *porta* e *chave de acesso* (primário ou secundário) para conectar-se ao cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![Captura de tela que mostra que a conexão com o cache foi bem-sucedida.](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Se você estiver usando um cache de teste com a porta não TLS não **segura** , execute `redis-cli.exe` e passe seu *nome de host*, *porta* e *chave de acesso* (primário ou secundário) para se conectar ao cache de teste.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel com redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como usar o [Console Redis](cache-configure.md#redis-console) para emitir comandos.