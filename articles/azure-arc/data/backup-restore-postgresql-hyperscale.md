---
title: Backup e restauração dos grupos de servidores de hiperescala do Banco de Dados do Azure para PostgreSQL
description: Backup e restauração dos grupos de servidores de hiperescala do Banco de Dados do Azure para PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 8b3304c673e8606667246a7d0df9ad8f3be11d9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101686692"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Fazer backup e restaurar grupos de servidores de hiperescala PostgreSQL habilitados para o Azure Arc

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

Quando você faz backup ou restaura seu grupo de servidores de hiperescala PostgreSQL habilitados para o Azure Arc, todo o conjunto de bancos de dados em todos os nós PostgreSQL do seu grupo de servidores é feito backup e/ou restaurado.

## <a name="take-a-manual-full-backup"></a>Faça um backup completo manual

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
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

Quando o backup for concluído, a ID, o nome, o tamanho, o estado e o carimbo de data/hora do backup serão retornados. Por exemplo:
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy` indica o fuso horário para a hora em que o backup foi feito. Neste exemplo, "+ 00:00" significa hora UTC (UTC + 00 hora 00 minutos).

> [!NOTE]
> Ainda não é possível:
> - Agendar backups automáticos
> - Mostrar o progresso de um backup enquanto ele está sendo realizado

## <a name="list-backups"></a>Listar backups

Para listar os backups que estão disponíveis para restauração, execute o seguinte comando:

```console
azdata arc postgres backup list --server-name <servergroup name>
```

Por exemplo:

```console
azdata arc postgres backup list --server-name postgres01
```

Retorna uma saída como:

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

A coluna timestamp indica o ponto no tempo UTC em que o backup foi feito.

## <a name="restore-a-backup"></a>Restaurar um backup
Nesta seção, mostraremos como fazer uma restauração completa ou uma restauração pontual. Ao restaurar um backup completo, você restaura todo o conteúdo do backup. Quando você faz uma restauração pontual, restaura até o ponto no tempo que você indica. Qualquer transação que tenha sido feita depois desse ponto no tempo não será restaurada.

### <a name="restore-a-full-backup"></a>Restaurar um backup completo
Para restaurar todo o conteúdo de um backup, execute o comando:
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

Em que:
- __backup-ID__ é a ID do backup mostrada no comando list backup mostrado acima.
Isso coordenará uma restauração completa distribuída em todos os nós que constituem seu grupo de servidores de hiperescala PostgreSQL habilitado para o Azure Arc. Em outras palavras, ele irá restaurar todos os dados em seus nós de operador e coordenador.

#### <a name="examples"></a>Exemplos:

__Restaurar o grupo de servidores postgres01 em si mesmo:__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

Esta operação só tem suporte para PostgreSQL versão 12 e superior.

__Restaure o grupo de servidores postgres01 para um grupo de servidores diferente postgres02:__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
Esta operação tem suporte para qualquer versão do PostgreSQL a partir da versão 11. O grupo de servidores de destino deve ser criado antes da operação de restauração, deve ser da mesma configuração e deve estar usando o mesmo PVC de backup que o grupo de servidores de origem.

Quando a operação de restauração for concluída, ela retornará uma saída como esta para a linha de comando:

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> Ainda não é possível:
> - Restaurar um backup indicando seu nome
> - Mostrar o progresso de uma operação de restauração


### <a name="do-a-point-in-time-restore"></a>Fazer uma restauração pontual

Para restaurar um grupo de servidores até uma hora de ponto específica, execute o comando:
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

Para ler o formato geral do comando Restore, execute: `azdata arc postgres backup restore --help` .

Em que `time` é o ponto no tempo para o qual restaurar. Forneça um carimbo de data/hora ou um número e um sufixo ( `m` por minutos, `h` para horas, `d` para dias ou `w` para semanas). Por exemplo, `1.5h` retorna 90 minutos.

#### <a name="examples"></a>Exemplos:
__Faça uma restauração pontual do grupo de servidores postgres01 em si mesmo:__

Ainda não é possível fazer uma restauração pontual de um grupo de servidores em si mesmo.

__Faça uma restauração pontual do grupo de servidores postgres01 em um grupo de servidores diferente postgres02 para um carimbo de data/hora específico:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

Este exemplo restaura no grupo de servidores postgres02 o estado em que o grupo de servidores postgres01 estava em 8 de dezembro de 2020 às 04:23:48.75 UTC. Observe que "+ 00" indica o fuso horário do ponto no tempo que você indica. Se você não indicar um fuso horário, o fuso horário do cliente do qual você executa a operação de restauração será usado.

Por exemplo:
- `2020-12-08 04:23:48.751326+00` é interpretado como `2020-12-08 04:23:48.751326` UTC
- Se você estiver no fuso horário padrão do Pacífico (PST = UTC + 08), `2020-12-08 04:23:48.751326` será interpretado como `2020-12-08 12:23:48.751326` UTC esta operação tem suporte para qualquer versão do PostgreSQL que inicia a versão 11. O grupo de servidores de destino deve ser criado antes da operação de restauração e deve estar usando o mesmo PVC de backup que o grupo de servidores de origem.


__Faça uma restauração pontual do grupo de servidores postgres01 para um grupo de servidores diferente postgres02 para um período de tempo específico no passado:__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

Este exemplo restaura no grupo de servidores postgres02 o estado em que o grupo de servidores postgres01 foi de 22 minutos atrás.
Esta operação tem suporte para qualquer versão do PostgreSQL a partir da versão 11. O grupo de servidores de destino deve ser criado antes da operação de restauração e deve estar usando o mesmo PVC de backup que o grupo de servidores de origem.

> [!NOTE]
> Ainda não é possível:
> - Mostrar o progresso de uma operação de restauração

## <a name="delete-backups"></a>Excluir backups

A retenção de backup não pode ser definida na visualização. No entanto, você pode excluir manualmente os backups que não são necessários.
O comando geral para excluir backups é:

```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```

em que:
- `--server-name` é o nome do grupo de servidores do qual o usuário deseja excluir um backup
- `--name` é o nome do backup a ser excluído
- `-id`é a ID do backup a ser excluído

> [!NOTE]
> `--name` e `-id` são mutuamente exclusivos.

Por exemplo:

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

Você pode recuperar o nome e a ID de seus backups executando o comando list backup, conforme explicado no parágrafo anterior.

Para obter mais detalhes sobre o comando de exclusão, execute:

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>Próximas etapas
- Leia sobre como [escalar horizontalmente (adicionando nós de trabalho)](scale-out-postgresql-hyperscale-server-group.md) seu grupo de servidores
- Leia sobre como escalar verticalmente [(aumentando/diminuindo a memória/vcores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) seu grupo de servidores
