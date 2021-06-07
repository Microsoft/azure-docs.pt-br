---
title: Escalar e reduzir verticalmente um grupo de servidores de hiperescala do banco de dados do Azure para PostgreSQL usando a CLI (azdata ou kubectl)
description: Escalar e reduzir verticalmente um grupo de servidores de hiperescala do banco de dados do Azure para PostgreSQL usando a CLI (azdata ou kubectl)
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 4461fb6904d51ee8d740b633a2d0028658ac2ced
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687542"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-azdata-or-kubectl"></a>Escalar e reduzir verticalmente um grupo de servidores de hiperescala do banco de dados do Azure para PostgreSQL usando a CLI (azdata ou kubectl)



Há ocasiões em que talvez seja necessário alterar as características ou a definição de um grupo de servidores. Por exemplo:

- Escalar ou reduzir verticalmente o número de vCores que cada um dos nós de coordenador ou de trabalho usa
- Escalar ou reduzir verticalmente a memória que cada um dos nós coordenador ou de trabalho usa

Este guia explica como dimensionar vCore e/ou memória.

Escalar ou reduzir verticalmente as configurações de memória e vCore do seu grupo de servidores significa que você tem a possibilidade de definir um mínimo e/ou um máximo para cada uma das configurações vCore e de memória. Se você quiser configurar o grupo de servidores para usar um número específico de vCore ou uma quantidade específica de memória, defina as configurações mín. igual às configurações máximas.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>Mostrar a definição atual do grupo de servidores

Para mostrar a definição atual do seu grupo de servidores e ver quais são as configurações de vCore e memória atuais, execute um dos seguintes comandos:

### <a name="cli-with-azdata"></a>CLI com azdata

```console
azdata arc postgres server show -n <server group name>
```
### <a name="cli-with-kubectl"></a>CLI com kubectl

```console
kubectl describe postgresql-12/<server group name> [-n <namespace name>]
```
> [!NOTE]
> Se você criou um grupo de servidores do PostgreSQL versão 11, execute `kubectl describe postgresql-11/<server group name>` em vez disso.

Ele retorna a configuração do seu grupo de servidores. Se você tiver criado o grupo de servidores com as configurações padrão, deverá ver a definição da seguinte maneira:

```console
"scheduling": {
      "default": {
        "resources": {
          "requests": {
            "memory": "256Mi"
          }
        }
      }
    },
```

## <a name="interpret-the-definition-of-the-server-group"></a>Interpretar a definição do grupo de servidores

Na definição de um grupo de servidores, a seção que transporta as configurações de vCore mínimo/máximo por nó e mín/máx de memória por nó é a seção **"agendamento"** . Nessa seção, as configurações máximas serão persistidas em uma subseção chamada **"Limits"** e as configurações min serão mantidas na subseção chamada **"requests"**.

Se você definir configurações min que sejam diferentes das configurações máximas, a configuração garantirá que o grupo de servidores tenha alocado os recursos solicitados, se necessário. Ele não excederá os limites definidos.

Os recursos (vCores e Memory) que serão realmente usados pelo seu grupo de servidores são até as configurações máximas e dependem das cargas de trabalho e dos recursos disponíveis no cluster. Se você não limitar as configurações com um máximo, o grupo de servidores poderá usar até todos os recursos que o cluster kubernetes aloca para os nós kubernetes em que o grupo de servidores está agendado.

Essas configurações de vCore e de memória se aplicam a cada um dos nós de hiperescala do PostgreSQL (nó de coordenador e nós de trabalho). Ainda não há suporte para definir as definições do nó de coordenador e os nós de trabalho separadamente.

Em uma configuração padrão, somente a memória mínima é definida como 256Mi, pois é a quantidade mínima de memória recomendada para executar a hiperescala do PostgreSQL.

> [!NOTE]
> Definir um mínimo não significa que o grupo de servidores usará, necessariamente, esse mínimo. Isso significa que, se o grupo de servidores precisar dele, será garantido que seja alocado pelo menos esse mínimo. Por exemplo, vamos considerar que definimos `--minCpu 2` . Isso não significa que o grupo de servidores usará pelo menos 2 vCores em todos os momentos. Em vez disso, isso significa que o grupo de servidores pode começar a usar menos de 2 vCores se ele não precisar muito e for garantido que seja alocado pelo menos 2 vCores se precisar deles posteriormente. Isso significa que o cluster kubernetes aloca recursos para outras cargas de trabalho de forma que ele possa reservar 2 vCores para o grupo de servidores, se ele precisar.

>[!NOTE]
>Antes de modificar a configuração do seu sistema, certifique-se de se familiarizar com o modelo de recursos kubernetes [aqui](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

## <a name="scale-up-the-server-group"></a>Escalar verticalmente o grupo de servidores

As configurações que você está prestes a definir devem ser consideradas dentro da configuração que você definiu para o cluster kubernetes. Verifique se você não está definindo valores que o cluster kubernetes não será capaz de atender. Isso pode levar a erros ou comportamento imprevisível. Por exemplo, se o status do seu grupo de servidores permanecer na _atualização_ de status por um longo tempo depois que você alterar a configuração, poderá ser uma indicação de que você definiu os parâmetros abaixo para os valores que o cluster kubernetes não pode atender. Se esse for o caso, reverta a alteração ou leia a _troubleshooting_section.

Como exemplo, vamos supor que você deseja escalar verticalmente a definição do grupo de servidores para:

- VCore mínimo = 2
- VCore máximo = 4
- Memória mínima = 512Mb
- Memória máxima = 1Gb

Você usaria uma das seguintes abordagens:

### <a name="cli-with-azdata"></a>CLI com azdata

```console
azdata arc postgres server edit -n <name of your server group> --cores-request <# core-request>  --cores-limit <# core-limit>  --memory-request <# memory-request>Mi  --memory-limit <# memory-limit>Mi
```

> [!CAUTION]
> Veja abaixo um exemplo fornecido para ilustrar como você pode usar o comando. Antes de executar um comando de edição, certifique-se de definir os parâmetros para valores que o cluster kubernetes pode honrar.

```console
azdata arc postgres server edit -n <name of your server group> --cores-request 2  --cores-limit 4  --memory-request 512Mi  --memory-limit 1024Mi
```

O comando é executado com êxito quando ele mostra:

```console
<name of your server group> is Ready
```

> [!NOTE]
> Para obter detalhes sobre esses parâmetros, execute `azdata arc postgres server edit --help` .

### <a name="cli-with-kubectl"></a>CLI com kubectl

```console
kubectl edit postgresql-12/<server group name> [-n <namespace name>]
```

Isso o levará no editor de vi, no qual você pode navegar e alterar a configuração. Use o seguinte para mapear a configuração desejada para o nome do campo na especificação:

> [!CAUTION]
> Veja abaixo um exemplo fornecido para ilustrar como você pode editar a configuração. Antes de atualizar a configuração, certifique-se de definir os parâmetros para valores que o cluster kubernetes pode honrar.

Por exemplo:
- VCore mínimo = 2-> scheduling\default\resources\requests\cpu
- VCore máximo = 4-> scheduling\default\resources\limits\cpu
- Memória mínima = 512Mb-> scheduling\default\resources\requests\cpu
- Memória máxima = 1Gb-> scheduling\default\resources\limits\cpu

Se você não estiver familiarizado com o editor vi, consulte uma descrição dos comandos que podem ser necessários [aqui](https://www.computerhope.com/unix/uvi.htm):
- modo de edição: `i`
- mover-se com setas
- _stop edição: `esc`
- _exit sem salvar: `:qa!`
- _exit depois de salvar: `:qw!`


## <a name="show-the-scaled-up-definition-of-the-server-group"></a>Mostrar a definição de escala vertical do grupo de servidores

Execute novamente o comando para exibir a definição do grupo de servidores e verifique se ele está definido como desejado:

### <a name="cli-with-azdata"></a>CLI com azdata

```console
azdata arc postgres server show -n <the name of your server group>
```
### <a name="cli-with-kubectl"></a>CLI com kubectl

```console
kubectl describe postgresql-12/<server group name>  [-n <namespace name>]
```
> [!NOTE]
> Se você criou um grupo de servidores do PostgreSQL versão 11, execute `kubectl describe postgresql-11/<server group name>` em vez disso.


Ele mostrará a nova definição do grupo de servidores:

```console
"scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "2",
            "memory": "512Mi"
          }
        }
      }
    },
```

## <a name="scale-down-the-server-group"></a>Reduzir verticalmente o grupo de servidores

Para reduzir verticalmente o grupo de servidores, execute o mesmo comando, mas defina valores menores para as configurações que você deseja reduzir verticalmente. Para remover as solicitações e/ou os limites, especifique seu valor como cadeia de caracteres vazia.

## <a name="reset-to-default-values"></a>Redefinir para valores padrão
Para redefinir os parâmetros de limites/solicitações de núcleo/memória para seus valores padrão, edite-os e passe uma cadeia de caracteres vazia em vez de um valor real. Por exemplo, se você quiser redefinir o parâmetro de limite de núcleo (CL), execute os seguintes comandos:
- em um cliente Linux:

```console
    azdata arc postgres server edit -n <servergroup name> -cl ""
```

- em um cliente do Windows: 
 
```console
    azdata arc postgres server edit -n <servergroup name> -cl '""'
```


## <a name="next-steps"></a>Próximas etapas

- [Escalar horizontalmente o grupo de servidores de Hiperescala do Banco de Dados do Azure para PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)
- [Configuração de armazenamento e conceitos de armazenamento kubernetes](storage-configuration.md)
- [Modelo de recurso kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
