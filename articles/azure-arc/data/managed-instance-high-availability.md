---
title: Alta disponibilidade da Instância Gerenciada habilitada para o Azure Arc
titleSuffix: Deploy Azure Arc enabled Managed Instance with high availability
description: Saiba como implantar o Azure Arc habilitado Instância Gerenciada com alta disponibilidade.
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: conceptual
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 92f5c900238fc5d40e22870e2f00f8adeb5d335f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102032187"
---
# <a name="azure-arc-enabled-managed-instance-high-availability"></a>Alta disponibilidade da Instância Gerenciada habilitada para o Azure Arc

O Instância Gerenciada habilitado para Arc do Azure é implantado em kubernetes como um aplicativo em contêiner e usa construções kubernetes como conjuntos com estado e armazenamento persistente para fornecer monitoramento de integridade interno, detecção de falha e mecanismos de failover para manter a integridade do serviço. Para maior confiabilidade, você também pode configurar o Instância Gerenciada habilitado para o Azure ARC para implantar com réplicas extras em uma configuração de alta disponibilidade. O monitoramento, a detecção de falhas e o failover automático são gerenciados pelo controlador de dados do Arc Data Services. Esse serviço é fornecido sem a intervenção do usuário – tudo, desde a configuração do grupo de disponibilidade e da configuração dos pontos de extremidade de espelhamento de banco de dados, à adição do bancos de dados ao grupo de disponibilidade ou à coordenação de failover e atualização. Este documento explora os dois tipos de alta disponibilidade.

## <a name="built-in-high-availability"></a>Alta disponibilidade interna 

A alta disponibilidade interna é fornecida pelo kubernetes quando o armazenamento persistente remoto é configurado e compartilhado com nós usados pela implantação do serviço de dados Arc. Nessa configuração, o Kubernetes desempenha a função do orquestrador de cluster. Quando a instância gerenciada em um contêiner ou o nó subjacente falha, o Orchestrator Inicializa outra instância do contêiner e é anexado ao mesmo armazenamento persistente. Esse tipo é habilitado por padrão quando você implanta Instância Gerenciada habilitadas para o Azure Arc.

### <a name="verify-built-in-high-availability"></a>Verificar a alta disponibilidade interna

Esta seção, você verifica a alta disponibilidade interna fornecida pelo kubernetes. Ao seguir as etapas para testar essa funcionalidade, você exclui o pod de uma instância gerenciada existente e verifica se o kubernetes se recupera dessa ação. 

### <a name="prerequisites"></a>Pré-requisitos

- O cluster kubernetes deve ter [Armazenamento remoto compartilhado](storage-configuration.md#factors-to-consider-when-choosing-your-storage-configuration) 
- Um arco do Azure habilitado Instância Gerenciada implantado com uma réplica (padrão)

1. Exibir o pods. 

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

2. Exclua o pod de instância gerenciada.

   ```console
   kubectl delete pod <name of managed instance>-0 -n <namespace of data controller>
   ```

   Por exemplo,

   ```output
   user@pc:/# kubectl delete pod sql1-0 -n arc
   pod "sql1-0" deleted
   ```

3. Exiba o pods para verificar se a instância gerenciada está sendo recuperada.

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

   Por exemplo,

   ```output
   user@pc:/# kubectl get pods -n arc
   NAME                 READY   STATUS    RESTARTS   AGE
   sql1-0               2/3     Running   0          22s
   ```

Depois que todos os contêineres dentro do pod tiverem sido recuperados, você poderá se conectar à instância gerenciada.

## <a name="deploy-with-always-on-availability-groups"></a>Implantar com grupos de disponibilidade Always On

Para maior confiabilidade, você pode configurar Instância Gerenciada habilitadas para o Azure ARC para implantar com réplicas extras em uma configuração de alta disponibilidade. 

Recursos que os grupos de disponibilidade habilitam:

- Quando implantado com várias réplicas, um único grupo de disponibilidade chamado `containedag` é criado. Por padrão, `containedag` tem três réplicas, incluindo a primária. Todas as operações CRUD para o grupo de disponibilidade são gerenciadas internamente, incluindo a criação do grupo de disponibilidade ou a adição de réplicas ao grupo de disponibilidade criado. Grupos de disponibilidade adicionais não podem ser criados no Instância Gerenciada habilitado para arco do Azure.

- Todos os bancos de dados são adicionados automaticamente ao grupo de disponibilidade, incluindo todos os bancos de dados do usuário e do sistema, como `master` e `msdb`. Essa funcionalidade fornece um modo de exibição do sistema único entre as réplicas do grupo de disponibilidade. Observe os `containedag_master` dois `containedag_msdb` bancos de dados e se você se conectar diretamente à instância do. Os bancos de dados do `containedag_*` representam o `master` e o `msdb` dentro do grupo de disponibilidade.

- Um ponto de extremidade externo é provisionado automaticamente para conectar-se a bancos de dados dentro do grupo de disponibilidade. Esse ponto de extremidade `<managed_instance_name>-svc-external` desempenha a função do ouvinte do grupo de disponibilidade.

### <a name="deploy"></a>Implantar

Para implantar uma instância gerenciada com grupos de disponibilidade, execute o comando a seguir.

```console
azdata arc sql mi create -n <name of instance> --replicas 3
```

### <a name="check-status"></a>Verificar o status
Depois que a instância tiver sido implantada, execute os seguintes comandos para verificar o status da sua instância:

```console
azdata arc sql mi list
azdata arc sql mi show -n <name of instance>
```

Saída de exemplo:

```output
user@pc:/# azdata arc sql mi list
ExternalEndpoint    Name    Replicas    State
------------------  ------  ----------  -------
20.131.31.58,1433   sql2    3/3         Ready

user@pc:/#  azdata arc sql mi show -n sql2
{
...
  "status": {
    "AGStatus": "Healthy",
    "externalEndpoint": "20.131.31.58,1433",
    "logSearchDashboard": "link to logs dashboard",
    "metricsDashboard": "link to metrics dashboard",
    "readyReplicas": "3/3",
    "state": "Ready"
  }
}
```

Observe o número adicional de `Replicas` e o `AGstatus` campo que indica a integridade do grupo de disponibilidade. Se todas as réplicas estiverem ativas e sincronizadas, esse valor será `healthy` . 

### <a name="restore-a-database"></a>Restaurar um banco de dados 
Etapas adicionais são necessárias para restaurar um banco de dados em um grupo de disponibilidade. As etapas a seguir demonstram como restaurar um banco de dados em uma instância gerenciada e adicioná-lo a um grupo de disponibilidade. 

1. Expor o ponto de extremidade externo da instância primária criando um novo serviço kubernetes.

    Determine o Pod que hospeda a réplica primária conectando-se à instância gerenciada e execute:

    ```sql
    SELECT @@SERVERNAME
    ```
    Crie o serviço kubernetes para a instância primária executando o comando a seguir se o cluster kubernetes usar os serviços nodePort. Substitua `podName` pelo nome do servidor retornado na etapa anterior, `serviceName` com o nome preferencial para o serviço kubernetes criado.

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=NodePort
    ```

    Para um serviço Balancer, execute o mesmo comando, exceto que o tipo de serviço criado é `LoadBalancer` . Por exemplo: 

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=LoadBalancer
    ```

    Aqui está um exemplo desse comando executado no serviço kubernetes do Azure, onde o Pod que hospeda o primário é `sql2-0` :

    ```bash
    kubectl -n arc-cluster expose pod sql2-0 --port=1533  --name=sql2-0-p --type=LoadBalancer
    ```

    Obtenha o IP do serviço de Kubernetes criado:

    ```bash
    kubectl get services -n <namespaceName>
    ```
2. Restaure o banco de dados para o ponto de extremidade da instância primária.

    Adicione o arquivo de backup do banco de dados ao contêiner da instância primária.

    ```console
    kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>
    ```

    Exemplo

    ```console
    kubectl cp /home/WideWorldImporters-Full.bak sql2-1:var/opt/mssql/data/WideWorldImporters-Full.bak -c arc-sqlmi -n arc
    ```

    Restaure o arquivo de backup do banco de dados executando o comando a seguir.

    ```sql 
    RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
    WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
    ,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
    ,RECOVERY, REPLACE, STATS = 5;  
    GO
    ```
    
    Exemplo

    ```sql
    RESTORE Database WideWorldImporters
    FROM DISK = '/var/opt/mssql/data/WideWorldImporters-Full.BAK'
    WITH
    MOVE 'WWI_Primary' TO '/var/opt/mssql/data/WideWorldImporters.mdf',
    MOVE 'WWI_UserData' TO '/var/opt/mssql/data/WideWorldImporters_UserData.ndf',
    MOVE 'WWI_Log' TO '/var/opt/mssql/data/WideWorldImporters.ldf',
    MOVE 'WWI_InMemory_Data_1' TO '/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1',
    RECOVERY, REPLACE, STATS = 5;  
    GO
    ```

3. Adicione um banco de dados ao grupo de disponibilidade.

    Para que o banco de dados seja adicionado ao AG, ele deve ser executado no modo de recuperação completa e um backup de log precisa ser feito. Execute as instruções TSQL abaixo para adicionar o banco de dados restaurado ao grupo de disponibilidade.

    ```sql
    ALTER DATABASE <databaseName> SET RECOVERY FULL;
    BACKUP DATABASE <databaseName> TO DISK='<filePath>'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE <databaseName>
    ```

    O exemplo a seguir adiciona um banco de dados chamado `WideWorldImporters` que foi restaurado na instância:

    ```sql
    ALTER DATABASE WideWorldImporters SET RECOVERY FULL;
    BACKUP DATABASE WideWorldImporters TO DISK='/var/opt/mssql/data/WideWorldImporters.bak'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE WideWorldImporters
    ```

> [!IMPORTANT]
> Como melhor prática, limpe excluindo o serviço de Kubernetes criado acima executando este comando:
>
>```bash
>kubectl delete svc sql2-0-p -n arc
>```

### <a name="limitations"></a>Limitações

O Azure Arc habilitado Instância Gerenciada grupos de disponibilidade têm as mesmas [limitações que os grupos de disponibilidade de cluster de Big Data. Clique aqui para saber mais.](/sql/big-data-cluster/deployment-high-availability#known-limitations)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [Recursos e funcionalidades da Instância Gerenciada de SQL habilitada para Azure Arc](managed-instance-features.md)
