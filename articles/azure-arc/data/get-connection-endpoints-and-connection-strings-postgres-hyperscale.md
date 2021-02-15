---
title: Obter pontos de extremidade de conexão e formar cadeias de conexão para seu grupo de servidores de hiperescala PostgreSQL habilitado para Arc
titleSuffix: Azure Arc enabled data services
description: Obter pontos de extremidade de conexão e formar cadeias de conexão para seu grupo de servidores de hiperescala PostgreSQL habilitado para Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4087d618209ab4db46f89ef4e6db7ac87ca4cf57
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331005"
---
# <a name="get-connection-endpoints-and-form-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>Obter pontos de extremidade de conexão e formar cadeias de conexão para seu grupo de servidores de hiperescala PostgreSQL habilitado para Arc

Este artigo explica como você pode recuperar os pontos de extremidade de conexão para seu grupo de servidores e como você forma as cadeias de conexão que serão usadas com seus aplicativos e/ou ferramentas.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Obter pontos de extremidade de conexão:

### <a name="from-cli-with-azdata"></a>Da CLI com azdata
#### <a name="1-connect-to-your-arc-data-controller"></a>1. Conecte-se ao controlador de dados Arc:
- Se você já tiver uma sessão aberta no host do controlador de dados Arc: execute o seguinte comando:
```console
azdata login
```

- Se você não tiver uma sessão aberta no host do controlador de dados Arc: execute o comando a seguir 
```console
azdata login --endpoint https://<external IP address of host/data controller>:30080
```

#### <a name="2-show-the-connection-endpoints"></a>2. mostrar os pontos de extremidade de conexão
Execute o comando a seguir:
```console
azdata arc postgres endpoint list -n <server group name>
```
Ele retorna uma saída como:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```
Use estes pontos de extremidade para:
- Formate suas cadeias de conexão e conecte-se com suas ferramentas ou aplicativos de cliente
- Acessar os painéis Grafana e Kibana do seu navegador

Por exemplo, você pode usar o ponto de extremidade chamado _instância PostgreSQL_ para se conectar com psql ao seu grupo de servidores. Por exemplo:
```console
psql postgresql://postgres:MyPassworkd@12.345.123.456:1234
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - A senha do usuário _postgres_ indicado no ponto de extremidade chamado "_instância do PostgreSQL_" é a senha que você escolheu ao implantar o grupo de servidores.
> - Sobre o azdata: a concessão associada à sua conexão dura cerca de 10 horas. Depois disso, você precisa se reconectar. Se sua concessão tiver expirado, você receberá a seguinte mensagem de erro ao tentar executar um comando com azdata (diferente de azdata logon): _erro: (401)_ 
>  _motivo:_ 
>  _cabeçalhos de resposta http não autorizados: HTTPHeaderDict ({' Date ': ' Sun, 06 setembro de 2020 16:58:38 GMT ', ' Content-length ': ' 0 ', ' www-Authenticate ': '_ 
>  _Basic realment = "_ credenciais de logon necessárias", erro de portador = "INVALID_TOKEN", error_description = "o token está expirado" '}) _ quando isso acontece, você precisa se reconectar com azdata conforme explicado acima.

## <a name="from-cli-with-kubectl"></a>Da CLI com kubectl
- Se o seu grupo de servidores for do postgres versão 12 (padrão), o seguinte comando:
```console
kubectl get postgresql-12/<server group name>
```
- Se o seu grupo de servidores for do postgres versão 11, o seguinte comando:
```console
kubectl get postgresql-11/<server group name>
```

Esses comandos produzirão uma saída como a mostrada abaixo. Você pode usar essas informações para formar suas cadeias de conexão:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 


## <a name="form-connection-strings"></a>Cadeias de conexão do formulário:
Use a tabela abaixo de modelos de cadeias de conexão para o grupo de servidores. Em seguida, você pode copiar/colar e personalizá-los conforme necessário:

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

### <a name="web-app"></a>Aplicativo Web

```webapp
Database=postgres; Data Source=192.168.1.121; User Id=postgres; Password={your_password_here}
```

## <a name="next-steps"></a>Próximas etapas
- Leia sobre como [escalar horizontalmente (adicionando nós de trabalho)](scale-out-postgresql-hyperscale-server-group.md) seu grupo de servidores
- Leia sobre como escalar verticalmente [(aumentando/diminuindo a memória/vcores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) seu grupo de servidores


