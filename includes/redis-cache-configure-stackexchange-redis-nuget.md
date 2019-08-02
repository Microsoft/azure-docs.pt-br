---
author: yegu-ms
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: yegu
ms.openlocfilehash: 61e93e3700b9a396d2ac4fdcbb51fc5c874cf9cb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286221"
---
Os aplicativos .NET podem usar o cliente de cache **StackExchange.Redis** , que pode ser configurado no Visual Studio usando um pacote NuGet que simplifica a configuração dos aplicativos do cliente de cache. 

> [!NOTE]
> Para obter mais informações, consulte a página do GitHub [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) e a [documentação do cliente de cache StackExchange.Azure](https://github.com/StackExchange/StackExchange.Redis#documentation).
>
>

Para configurar um aplicativo de cliente no Visual Studio utilizando o pacote NuGet StackExchange.Redis, clique com o botão direito no projeto em **Gerenciador de Soluções** e escolha **Gerenciar pacotes NuGet**. 

![Gerenciar pacotes NuGet](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Digite **StackExchange.Redis** ou **StackExchange.Redis.StrongName** na caixa de texto de pesquisa, selecione a versão desejada nos resultados e clique em **Instalar**.

> [!NOTE]
> Se preferir utilizar uma versão de nome forte da biblioteca do cliente de **StackExchange.Redis**, escolha **StackExchange.Redis.StrongName**; caso contrário escolha **StackExchange.Redis**.
>
>

![Pacote NuGet StackExchange.Redis](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Os downloads de pacote NuGet acrescentam as referências de assembly necessárias para o seu aplicativo de cliente para acessar o Cache do Azure para Redis com o cliente de Cache StackExchange.Azure para Redis.

> [!NOTE]
> Se você já tiver configurado seu projeto para usar o StackExchange.Redis, poderá verificar atualizações ao pacote por meio do **Gerenciador de Pacotes NuGet**. Para verificar e instalar as versões atualizadas do pacote NuGet StackExchange.Redis, clique em **Atualizações** na janela **Gerenciador de Pacotes NuGet**. Se uma atualização ao pacote NuGet StackExchange.Redis estiver disponível, você poderá atualizar seu projeto para usar a versão atualizada.
>
>

Você também pode instalar o pacote do NuGet StackExchange.Redis clicando no **Gerenciador de Pacotes NuGet**, **Console do Gerenciador de Pacotes** no menu **Ferramentas** e executando o seguinte comando na janela **Console do Gerenciador de Pacotes**.

```
Install-Package StackExchange.Redis
```
