---
title: Mostrar a configuração de um grupo de servidores de hiperescala PostgreSQL habilitado para Arc
titleSuffix: Azure Arc enabled data services
description: Mostrar a configuração de um grupo de servidores de hiperescala PostgreSQL habilitado para Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a268cd6b2fa3da6846554e3d1b170298abec7f18
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2020
ms.locfileid: "93279394"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>Mostrar a configuração de um grupo de servidores de hiperescala PostgreSQL habilitado para Arc

Este artigo explica como exibir a configuração de seus grupos de servidores. Ele faz isso prevendo algumas perguntas que você pode estar pedindo e responde a elas. Às vezes, pode haver várias respostas válidas. Este artigo tem como densidade os mais comuns ou úteis. Ele agrupa essas perguntas por tema:

- de um ponto de vista kubernetes
- de um ponto de vista dos serviços de dados habilitados para o Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>De um ponto de vista kubernetes

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>Quantos pods são usados pela hiperescala PostgreSQL habilitada para Arc do Azure?

Liste os recursos de kubernetes do tipo Postgres. Execute o comando:

```console
kubectl get postgresqls [-n <namespace name>]
```

A saída desse comando mostra a lista de grupos de servidores criados. Para cada um, indica o número de pods. Por exemplo:

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

Este exemplo mostra que 2 grupos de servidores são criados e cada um é executado em 3 pods (1 coordenador + 2 trabalhadores). Isso significa que os grupos de servidores criados neste controlador de dados Arc do Azure usam 6 pods.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Quais pods são usados por grupos de servidores de hiperescala PostgreSQL habilitados para Arc do Azure?

Execute:

```console
kubectl get pods [-n <namespace name>]
```

Isso retorna a lista de pods. Você verá os pods usados por seus grupos de servidores com base nos nomes que você forneceu a esses grupos de servidores. Por exemplo:

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

Neste exemplo, os seis pods que hospedam os dois grupos de servidores criados são:
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>Qual Pod do grupo de servidores é usado para qual função o grupo de servidores?

Qualquer nome de Pod com sufixo `-0` representa um nó de coordenador. Qualquer nome de nó sufixado por `-x` onde é 1 ou maior é o nó de trabalho. No exemplo acima:
- Os coordenadores são: `postgres01-0` , `postgres02-0`
- Os trabalhadores são: `postgres01-2` , `postgres01-2` , `postgres02-1` , `postgres02-2`

### <a name="what-is-the-status-of-the-pods"></a>Qual é o status do pods?

Executar `kubectl get pods` e examinar a coluna `STATUS`

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>Quais PVCs (declarações de volume persistentes) estão sendo usadas? 

Para entender quais PVCs são usados, bem como quais são usados para dados, logs e backups, execute: 

```console
kubectl get pvc [-n <namespace name>]
```

Por padrão, o prefixo do nome de um PVC indica seu uso:

- `backups-`...: é um PVC usado para backups
- `data-`...: o PVC é usado para arquivos de dados
- `logs-`...: é um PVC usado para logs de transações/arquivos WAL

Por exemplo:

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>De um ponto de vista dos serviços de dados habilitados para o Azure Arc:

* Quantos grupos de servidores são criados em um controlador de dados de arco?
* Quais são seus nomes?
* Quantos nós de trabalho eles usam?
* Qual versão do postgres é executada?

Use um dos comandos a seguir.
- **Com kubectl:**

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   Por exemplo, ele produz a saída abaixo em que cada linha é um `servergroup` . A estrutura do nome na exibição abaixo é formada como:

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   A saída acima mostra 2 grupos de servidores que são do postgres versão 12. Se a versão foi postgres 11, o nome do CRD seria postgresql-11.arcdata.microsoft.com em vez disso.

   Cada um deles é executado em 3 nós/pods: 1 coordenador e 2 trabalhadores.

- **Com azdata:**

Execute o comando a seguir. A saída mostra informações semelhantes ao que o kubectl mostra:

   ```console
   azdata arc postgres server list

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>Quanta memória e vCores estão sendo usadas e quais extensões foram criadas para um grupo?

Execute um dos comandos a seguir

**Com Kubectl:**

Use kubectl para descrever os recursos do Postgres. Para fazer isso, você precisa de seu tipo (nome do recurso kubernetes (CRD) para a versão postgres correspondente, conforme mostrado acima) e o nome do grupo de servidores.
O formato geral deste comando é:

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

Por exemplo:

```console
kubectl describe postgresql-12/postgres02
```

Esses comandos mostram a configuração do grupo de servidores:

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Workers:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

>[!NOTE]
>Antes da versão de outubro de 2020, `Workers` estava `Shards` no exemplo anterior. Consulte [notas de versão – serviços de dados habilitados para o Azure Arc (versão prévia)](release-notes.md) para obter mais informações.

Vamos destacar alguns pontos de interesse específicos na descrição dos `servergroup` mostrados acima. O que ele nos diz sobre este grupo de servidores?

- É da versão 12 do postgres: 
   > ```json
   > Kind:         `postgresql-12`
   > ```
- Ele foi criado durante o mês de agosto de 2020:
   > ```json
   > Creation Timestamp:  `2020-08-31T21:01:07Z`
   > ```
- Duas extensões postgres foram criadas neste grupo de servidores: `citus` e `pg_stat_statements`
   > ```json
   > Engine:
   >    Extensions:
   >      Name:  `citus`
   >      Name:  `pg_stat_statements`
   > ```
- Ele usa dois nós de trabalho
   > ```json
   > Scale:
   >    Workers:  `2`
   > ```
- É garantido usar 1 CPU/vCore e 512MB de RAM por nó. Ele usará mais de 4 CPU/vCores e 1024MB de memória:
   > ```json
   > Scheduling:
   >    Default: 
   >      Resources:
   >        Limits:
   >          Cpu:     4
   >          Memory:  1024Mi
   >        Requests:
   >          Cpu:     1
   >          Memory:  512Mi
   > ```
 - Ele está disponível para consultas e não tem nenhum problema. Todos os nós estão em execução:
   > ```json
   > Status:
   >  ...
   >  Ready Pods:         3/3
   >  State:              Ready
   > ```

**Com azdata:**

O formato geral do comando é:

```console
azdata arc postgres server show -n <server group name>
```

Por exemplo:

```console
azdata arc postgres server show -n postgres02
```

Retorna a saída abaixo em um formato e conteúdo muito semelhante ao retornado por kubectl.

```console
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "workers": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>Próximas etapas
- [Leia sobre os conceitos de hiperescala do PostgreSQL habilitado para Arc do Azure](concepts-distributed-postgres-hyperscale.md)
- [Leia sobre como escalar horizontalmente (adicionar nós de trabalho) um grupo de servidores](scale-out-postgresql-hyperscale-server-group.md)
- [Leia sobre como escalar verticalmente (aumentar ou reduzir a memória e/ou vCores) um grupo de servidores](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [Leia sobre a configuração de armazenamento](storage-configuration.md)
- [Leia como monitorar uma instância de banco de dados](monitor-grafana-kibana.md)
- [Usar extensões PostgreSQL em seu grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Configurar a segurança para seu grupo de servidores de PostgreSQL de Hiperescala habilitado para o Azure Arc](configure-security-postgres-hyperscale.md)
