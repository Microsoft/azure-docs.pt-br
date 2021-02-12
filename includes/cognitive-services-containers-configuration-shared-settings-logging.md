---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 2bfb700ac5c220b780c05c8d415a4506c7a2f871
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001131"
---
As configurações `Logging` gerenciam o suporte ao registro em log do ASP.NET Core para o contêiner. Você pode usar as mesmas definições de configuração e valores para o contêiner usado em um aplicativo ASP.NET Core. 

Os provedores de registro em log a seguir são suportados pelo contêiner:

|Provedor|Finalidade|
|--|--|
|[Console](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#console-provider)|O ASP.NET Core `Console` provedor de log. Todas as definições de configuração do ASP.NET Core e os valores padrão desse provedor de log são suportados.|
|[Depurar](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#debug-provider)|O ASP.NET Core `Debug` provedor de log. Todas as definições de configuração do ASP.NET Core e os valores padrão desse provedor de log são suportados.|
|[Disco](#disk-logging)|O provedor de log do JSON. Esse provedor de log grava dados de log na montagem de saída.|

Este comando de contêiner armazena informações de registro em log no formato JSON para a montagem de saída:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
--mount type=bind,src=/home/azureuser/output,target=/output \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Disk:Format=json
```

Este comando de contêiner mostra informações de depuração prefixadas com `dbug` enquanto o contêiner está em execução:

```bash
docker run --rm -it -p 5000:5000 \
--memory 2g --cpus 1 \
<registry-location>/<image-name> \
Eula=accept \
Billing=<endpoint> \
ApiKey=<api-key> \
Logging:Console:LogLevel:Default=Debug
```

### <a name="disk-logging"></a>Registro em log do Disk

O provedor de registro `Disk` suporta as seguintes configurações:

| Name | Tipo de dados | Descrição |
|------|-----------|-------------|
| `Format` | String | O formato de saída para arquivos de log.<br/> **Nota:** Esse valor deve ser definido como `json` para ativar o provedor de registro. Se esse valor for especificado sem especificar também uma montagem de saída durante a instanciação de um contêiner, ocorrerá um erro. |
| `MaxFileSize` | Integer | O tamanho máximo, em megabytes (MB), de um arquivo de log. Quando o tamanho do arquivo de log atual atende ou excede esse valor, um novo arquivo de log é iniciado pelo provedor de log. Se -1 for especificado, o tamanho do arquivo de log será limitado apenas pelo tamanho máximo do arquivo, se houver, para a montagem de saída. O valor padrão é 1. |

Para obter mais informações sobre como configurar o suporte ao registro em log do ASP.NET Core, consulte [Configuração do arquivo de configurações](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1).