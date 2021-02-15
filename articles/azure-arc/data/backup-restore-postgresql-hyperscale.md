---
title: Backup e restauração dos grupos de servidores de hiperescala do Banco de Dados do Azure para PostgreSQL
description: Backup e restauração dos grupos de servidores de hiperescala do Banco de Dados do Azure para PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d27537f017707e937303dd0c08a589db28aac6ef
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071431"
---
# <a name="backup-and-restore-for-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Backup e restauração para grupos de servidores de hiperescala PostgreSQL habilitados para o Azure Arc

Você pode fazer backup/restauração completo de seu grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc. Quando você faz isso, todo o conjunto de bancos de dados em todos os nós de seu grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc é feito backup e/ou restaurado.
Para fazer um backup e restaurá-lo, você precisa certificar-se de que uma classe de armazenamento de backup esteja configurada para seu grupo de servidores. Por enquanto, você precisa indicar uma classe de armazenamento de backup no momento em que cria o grupo de servidores. Ainda não é possível configurar o grupo de servidores para usar uma classe de armazenamento de backup depois que ela tiver sido criada.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!CAUTION]
> A visualização não oferece suporte a backup/restauração para a versão 11 do mecanismo do Postgres. Ele só dá suporte a backup/restauração para postgres versão 12.

## <a name="verify-configuration"></a>Verificar a configuração

Primeiro, verifique se o grupo de servidores existente foi configurado para usar a classe de armazenamento de backup.

Execute o seguinte comando depois de definir o nome do seu grupo de servidores:
```console
 azdata arc postgres server show -n postgres01
```
Examine a seção armazenamento da saída:
```console
...
"storage": {
      "backups": {
        "className": "local-storage"
      },
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
...
```
Se você vir o nome de uma classe de armazenamento indicada na seção "backups" da saída desse comando, significa que o grupo de servidores foi configurado para usar uma classe de armazenamento de backup e está pronto para fazer backups e fazer restaurações. Se você não vir uma seção "backups", será necessário excluir e recriar o grupo de servidores para configurar a classe de armazenamento de backup. Neste ponto, ainda não é possível configurar uma classe de armazenamento de backup depois que o grupo de servidores tiver sido criado.

>[!IMPORTANT]
>Se o grupo de servidores já estiver configurado para usar uma classe de armazenamento de backup, pule a próxima etapa e vá diretamente para a etapa "fazer backup completo manual".

## <a name="create-a-server-group"></a>Criar um grupo de servidor 

Em seguida, crie um grupo de servidores configurado para backup/restauração.

Para poder fazer backups e restaurá-los, você precisa criar um servidor configurado com uma classe de armazenamento.

Para obter uma lista das classes de armazenamento disponíveis no cluster kubernetes, execute o seguinte comando:

```console
kubectl get sc
```

<!--The general format of create server group command is documented [here](create-postgresql-instances.md)-->

```console
azdata arc postgres server create -n <name> --workers 2 --storage-class-backups <storage class name> [--storage-class-data <storage class name>] [--storage-class-logs <storage class name>]
```

Por exemplo, se você tiver criado um ambiente simples baseado em kubeadm:
```console
azdata arc postgres server create -n postgres01 --workers 2 --storage-class-backups local-storage
```

## <a name="take-manual-full-backup"></a>Fazer backup completo manual


Em seguida, faça um backup completo manual.

> [!CAUTION]
> **Somente para usuários do Azure kubernetes Service (AKs):** estamos cientes de um problema com a realização de backups de um grupo de servidores hospedado no AKs (serviço kubernetes do Azure). Já estamos trabalhando para corrigi-lo. Até que a atualização seja implantada em uma versão ou atualização futura, antes de fazer um backup, você precisará excluir os pods de seus grupos de servidores. Para cada um dos pods do seu grupo de servidores (você lista os pods executando **kubectl Get pods-n \<namespace name> **), exclua-os executando **kubectl Delete Pod \<server group pod name> -n \<namespace name> **. Não exclua pods que não fazem parte do seu grupo de servidores. A exclusão de pods não coloca seus dados em risco. Aguarde até que todos os pods fiquem online novamente e em STATUS = em execução antes de fazer um backup. O status do pod é fornecido na saída do comando kubectl Get pods acima.


Para fazer um backup completo de todos os dados e pastas de log do seu grupo de servidores, execute o seguinte comando:

```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
Em que:
- __nome__ indica o nome de um backup
- __nome do servidor__ indica um grupo de servidores
- __sem espera__ indica que a linha de comando não aguardará a conclusão do backup para que você possa continuar a usar esta janela de linha de comando

Esse comando coordenará um backup completo distribuído em todos os nós que constituem seu grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc. Em outras palavras, ele fará backup de todos os dados em seus nós coordenador e de trabalho.

Por exemplo:
```console
azdata arc postgres backup create --name MyBackup_Aug31_0730amPST --server-name postgres01
```

Quando o backup for concluído, a ID, o nome e o estado do backup serão retornados. Por exemplo:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "name": "MyBackup_Aug31_0730amPS",
  "state": "Done"
}
```

> [!NOTE]
> Ainda não é possível:
> - Agendar backups automáticos
> - Mostrar o progresso de um backup enquanto ele está sendo realizado

## <a name="list-backups"></a>Listar backups

Liste os backups que estão disponíveis para restauração.

Para listar os backups que estão disponíveis para restauração, execute o seguinte comando:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Por exemplo:
```console
azdata arc postgres backup list --server-name postgres01
```

Retornará uma saída como:
```console
ID                                Name                      State    Timestamp
--------------------------------  ------------------------  -------  ------------------------------
d134f51aa87f4044b5fb07cf95cf797f  MyBackup_Aug31_0730amPST  Done     2020-08-31 14:30:00:00+00:00
```

Timestamp indica o ponto no tempo em que o backup foi feito.

## <a name="restore-a-backup"></a>Restaurar um backup

Para restaurar o backup de um grupo de servidores inteiro, execute o comando:

```console
azdata arc postgres backup restore --server-name <server group name> --backup-id <backup id>
```

Em que:
- __backup-ID__ é a ID do backup mostrada no comando list backup (consulte a etapa 3).
Isso coordenará uma restauração completa distribuída em todos os nós que constituem seu grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc. Em outras palavras, ele irá restaurar todos os dados em seus nós de operador e coordenador.

Por exemplo:
```console
azdata arc postgres backup restore --server-name postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Quando a operação de restauração for concluída, ela retornará uma saída como esta para a linha de comando:
```console
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```
> [!NOTE]
> Ainda não é possível:
> - Restaurar um backup indicando seu nome
> - Restaurar um grupo de servidores com um nome diferente ou em um grupo de servidores diferente
> - Mostrar o progresso de uma operação de restauração

## <a name="delete-backups"></a>Excluir backups
A retenção de backup não pode ser definida na visualização. No entanto, você pode excluir manualmente os backups que não são necessários.
O comando geral para excluir backups é:
```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```
onde:
- `--server-name` é o nome do grupo de servidores do qual o usuário deseja excluir um backup
- `--name` é o nome do backup a ser excluído
- `-id`é a ID do backup a ser excluído

> [!NOTE]
> `--name` e `-id` são mutuamente exclusivos.

Você pode recuperar o nome e a ID de seus backups executando o comando list backup, conforme explicado no parágrafo anterior.

Por exemplo, considere que você tem os seguintes backups listados:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
5b0481dfc1c94b4cac79dd56a1bb21f4  MyBackup091720200110am  Done
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```
e você deseja excluir o primeiro deles, você executaria o seguinte comando:
```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```
Se você for listar os backups nesse ponto, obteria a seguinte saída:
```console
azdata arc postgres backup list -sn postgres01
ID                                Name                    State
--------------------------------  ----------------------  -------
0cf39f1e92344e6db4cfa285d36c7b14  MyBackup091720200111am  Done
```

Para obter mais detalhes sobre o comando de exclusão, execute:
```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Próximas etapas
- Leia sobre como [escalar horizontalmente (adicionando nós de trabalho)](scale-out-postgresql-hyperscale-server-group.md) seu grupo de servidores
- Leia sobre como escalar verticalmente [(aumentando/diminuindo a memória/vcores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) seu grupo de servidores
