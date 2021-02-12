---
title: Excluir usando Aplicativo Azure ferramenta de instantâneo consistente para Azure NetApp Files | Microsoft Docs
description: Fornece um guia para executar o comando delete do Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 0e2e4beebedb93524da43c5a3fad750b0295f5cd
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632560"
---
# <a name="delete-using-azure-application-consistent-snapshot-tool-preview"></a>Excluir usando Aplicativo Azure ferramenta de instantâneo consistente (versão prévia)

Este artigo fornece um guia para executar o comando delete do Aplicativo Azure ferramenta de instantâneo consistente que você pode usar com Azure NetApp Files.

## <a name="introduction"></a>Introdução

É possível excluir instantâneos de volume e entradas de catálogo de banco de dados com o `azacsnap -c delete` comando.

> [!IMPORTANT]
> Os instantâneos criados menos de 10 minutos antes de executar esse comando não devem ser excluídos devido ao potencial de interferência com a replicação de instantâneo.

## <a name="command-options"></a>Opções de comando

O `-c delete` comando tem as seguintes opções:

- `--delete hana` Quando usado com as opções `--hanasid <SID>` e `--hanabackupid <HANA backup id>` excluirá as entradas do catálogo SAP Hana backup que correspondem aos critérios.

- `--delete storage` Quando usado com a opção, `--snapshot <snapshot name>` você excluirá o instantâneo do sistema de armazenamento de back-end.

- `--delete sync` Quando usado com opções `--hanasid <SID>` e `--hanabackupid <HANA backup id>` Obtém o nome do instantâneo de armazenamento do catálogo de backup do `<HANA backup id>` e exclui a entrada no catálogo de backup _e_ o instantâneo de qualquer um dos volumes que contêm o instantâneo nomeado.

- `--delete sync` Quando usado com o `--snapshot <snapshot name>` verificará se há entradas no catálogo de backup para o `<snapshot name>` , o Obtém a ID de backup SAP Hana e exclui a entrada no catálogo de backup _e_ o instantâneo de qualquer um dos volumes que contêm o instantâneo nomeado.

- `[--force]` adicional *Use com cuidado*.  Esta operação forçará a exclusão sem solicitar confirmação.

- `[--configfile <config filename>]` é um parâmetro opcional que permite nomes de arquivo de configuração personalizada.

### <a name="delete-a-snapshot-using-sync-option"></a>Excluir um instantâneo usando a `sync` opção '

```bash
azacsnap -c delete --delete sync --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Verifica se há entradas no catálogo de backup para a SAP HANA ID de backup 157979797979, obtém o nome do instantâneo de armazenamento e exclui a entrada no catálogo de backup e o instantâneo de todos os volumes que contêm o instantâneo nomeado.

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Verifica se há entradas no catálogo de backup para o instantâneo chamado hana_hourly .2020-01 -22 _2358, obtém a ID de backup SAP HANA e exclui a entrada no catálogo de backup e o instantâneo de qualquer um dos volumes que contenham o instantâneo nomeado.

### <a name="delete-a-snapshot-using-hana-option"></a>Excluir um instantâneo usando a `hana` opção '

```bash
azacsnap -c delete --delete hana --hanasid H80 --hanabackupid 157979797979
```

> [!NOTE]
> Exclui o SAP HANA ID de backup 157979797979 do catálogo de backup para SID H80.

### <a name="delete-a-snapshot-using-storage-option"></a>Excluir um instantâneo usando a `storage` opção '

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> [!NOTE]
> Exclui o instantâneo de qualquer volume que contenha o instantâneo chamado hana_hourly .2020-01 -22 _2358.

**Saída usando a `--delete storage` opção**

O usuário é solicitado a confirmar a exclusão.

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

É possível evitar a confirmação do usuário, usando o parâmetro opcional da `--force` seguinte maneira:

```bash
azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
```

```output
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## <a name="next-steps"></a>Próximas etapas

- [Obter detalhes do instantâneo](azacsnap-cmd-ref-details.md)
- [Fazer um backup](azacsnap-cmd-ref-backup.md)
