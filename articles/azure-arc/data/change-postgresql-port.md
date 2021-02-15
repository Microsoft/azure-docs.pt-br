---
title: Alterar a porta PostgreSQL
description: Altere a porta na qual o grupo de servidores de hiperescala do PostgreSQL habilitado para o Azure Arc está escutando.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/02/2020
ms.topic: how-to
ms.openlocfilehash: 45424408c790e4921be48464818c55fe74313fd3
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93328674"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>Alterar a porta na qual o grupo de servidores está ouvindo 

A alteração da porta é uma operação de edição padrão do grupo de servidores. Para alterar a porta, execute o seguinte comando:
```console
 azdata arc postgres server edit -n <server group name> --port <desired port number>
```

Por exemplo, vamos supor que o nome do seu grupo de servidores seja _postgres01_ e você gostaria que ele escuta na porta _866_. Você executaria o seguinte comando:
```console
 azdata arc postgres server edit -n postgres01 --port 866
```

## <a name="verify-that-the-port-was-changed"></a>Verifique se a porta foi alterada

Para verificar se a porta foi alterada, execute o seguinte comando para mostrar a configuração do seu grupo de servidores:
```console
azdata arc postgres server show -n <server group name>
```

Na saída desse comando, examine o número da porta exibida para o item "porta" na seção "serviço" das especificações do seu grupo de servidores.
Como alternativa, você pode verificar no item externalEndpoint da seção status das especificações do seu grupo de servidores que o endereço IP é seguido pelo número da porta que você configurou.

Como ilustração, se continuarmos com o exemplo acima, você executaria o comando:
```console
azdata arc postgres server show -n postgres01
```

e você verá a porta 866 mencionada aqui:

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
e aqui

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>Próximas etapas
- Leia sobre [como se conectar ao seu grupo de servidores](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).
- Leia sobre como você pode configurar outros aspectos do seu grupo de servidores na seção How-to\Manage\Configure & seção escala da documentação.
