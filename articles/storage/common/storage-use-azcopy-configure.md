---
title: Configurar, otimizar e solucionar problemas de AzCopy com o armazenamento do Azure | Microsoft Docs
description: Configurar, otimizar e solucionar problemas de AzCopy.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 05/14/2019
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: 1a67846889b43d582a7a7d477a33f0e2168fd760
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147864"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurar, otimizar e solucionar problemas de AzCopy

O AzCopy é um utilitário de linha de comando que você pode usar para copiar blobs ou arquivos de ou para uma conta de armazenamento. Este artigo ajuda você a executar tarefas de configuração avançada e ajuda você a solucionar problemas que podem surgir como usar o AzCopy.

> [!NOTE]
> Se você estiver procurando o conteúdo para ajudar você a se familiarizar com o AzCopy, consulte um dos artigos a seguir:
> - [Introdução ao AzCopy](storage-use-azcopy-v10.md)
> - [Transferir dados com o AzCopy e o Armazenamento de Blobs](storage-use-azcopy-blobs.md)
> - [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
> - [Transferir dados com o AzCopy e os buckets do Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Definir configurações de proxy

Para configurar as configurações de proxy para o AzCopy, defina o `https_proxy` variável de ambiente.

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | Em um prompt de comando, use: `set https_proxy=<proxy IP>:<proxy port>`<br> No PowerShell, use: `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Atualmente, o AzCopy não dá suporte a proxies que exigem a autenticação com NTLM ou Kerberos.

## <a name="optimize-throughput"></a>Otimizar a taxa de transferência

Defina o `AZCOPY_CONCURRENCY_VALUE` variável de ambiente para configurar o número de solicitações simultâneas e para controlar o consumo de recursos e desempenho de taxa de transferência. Se seu computador tiver menos de 5 CPUs e, em seguida, o valor dessa variável é definido como `32`. Caso contrário, o valor padrão é igual a 16, multiplicados pelo número de CPUs. O valor padrão máximo dessa variável é `300`, mas você pode definir manualmente esse valor maior ou menor.

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Use o `azcopy env` para verificar o valor atual dessa variável.  Se o valor é em branco, o `AZCOPY_CONCURRENCY_VALUE` variável é definida como o valor padrão de `300`.

## <a name="change-the-location-of-the-log-files"></a>Alterar a localização dos arquivos de log

Por padrão, os arquivos de log estão localizados na `%USERPROFILE\\.azcopy` diretório no Windows ou no `$HOME\\.azcopy` diretório no Mac e Linux. Você pode alterar esse local se você precisar usando estes comandos.

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Use o `azcopy env` para verificar o valor atual dessa variável. Se o valor estiver em branco, os logs são gravados no local padrão.

## <a name="change-the-default-log-level"></a>Alterar o nível de log padrão

Por padrão, o nível de log do AzCopy é definido como `INFO`. Se você deseja reduzir o detalhamento do log para economizar espaço em disco, substituir essa configuração usando o ``--log-level`` opção. 

Níveis de log disponíveis são: `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC`, e `FATAL`.

## <a name="troubleshoot-issues"></a>Solucionar problemas

O AzCopy cria arquivos de log e o plano para cada trabalho. Você pode usar os logs para investigar e solucionar problemas potenciais. 

Os logs conterão o status de falha (`UPLOADFAILED`, `COPYFAILED`, e `DOWNLOADFAILED`), o caminho completo e o motivo da falha.

Por padrão, os arquivos de log e o plano estão localizados na `%USERPROFILE\\.azcopy` do Windows ou `$HOME\\.azcopy` diretório no Mac e Linux.

> [!IMPORTANT]
> Ao enviar uma solicitação para o Microsoft Support (ou solucionar o problema que envolvem qualquer terceiro), compartilhar a versão em uma versão editada do comando que você deseja executar. Isso garante que a SAS não sejam acidentalmente compartilhada com qualquer pessoa. Você pode encontrar a versão editada no início do arquivo de log.

### <a name="review-the-logs-for-errors"></a>Examinar os logs em busca de erros

O comando a seguir obterá todos os erros com `UPLOADFAILED` status do `04dc9ca9-158f-7945-5933-564021086c79` log:

**Windows**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Exibir e retomar trabalhos

Cada operação de transferência criará um trabalho do AzCopy. Use o seguinte comando para exibir o histórico de trabalhos:

```
azcopy jobs list
```

Para exibir as estatísticas de trabalho, use o seguinte comando:

```
azcopy jobs show <job-id>
```

Para filtrar as transferências por status, use o seguinte comando:

```
azcopy jobs show <job-id> --with-status=Failed
```

Use o comando a seguir para retomar um trabalho com falha/cancelado. Esse comando usa seu identificador juntamente com o token SAS, pois não é persistente por motivos de segurança:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

Quando você retomar um trabalho, o AzCopy examina o arquivo de plano de trabalho. O arquivo plano lista todos os arquivos que foram identificados para o processamento quando o trabalho foi criado. Quando você retomar um trabalho, o AzCopy tentará transferir todos os arquivos que estão listados no arquivo de plano que já não foram transferido.