---
title: Configurar, otimizar e solucionar problemas do AzCopy com o armazenamento do Azure | Microsoft Docs
description: Configurar, otimizar e solucionar problemas do AzCopy com o armazenamento do Azure. Altere o local ou remova o plano e os arquivos de log. Altere o nível de log padrão.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 244012f0945f467fe79e95d652ba22e3b62a1b7a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596948"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>Configurar, otimizar e solucionar problemas do AzCopy

AzCopy é um utilitário de linha de comando que você pode usar para copiar blobs ou arquivos de ou para uma conta de armazenamento. Este artigo ajuda a executar tarefas de configuração avançadas e a solucionar problemas que podem surgir ao usar o AzCopy.

> [!NOTE]
> Se você estiver procurando conteúdo para ajudá-lo a começar a usar o AzCopy, consulte qualquer um dos seguintes artigos:
> - [Introdução ao AzCopy](storage-use-azcopy-v10.md)
> - [Transferir dados com o AzCopy e o Armazenamento de Blobs](./storage-use-azcopy-v10.md#transfer-data)
> - [Transferir dados com o AzCopy e o Armazenamento de Arquivos](storage-use-azcopy-files.md)
> - [Transferir dados com o AzCopy e os buckets do Amazon S3](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Definir configurações de proxy

Para definir as configurações de proxy para AzCopy, defina a `HTTPS_PROXY` variável de ambiente. Se você executar o AzCopy no Windows, o AzCopy detectará automaticamente as configurações de proxy, de modo que você não precisará usar essa configuração no Windows. Se você optar por usar essa configuração no Windows, ela substituirá a detecção automática.

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | Em um prompt de comando, use: `set HTTPS_PROXY=<proxy IP>:<proxy port>`<br> No PowerShell, use: `$env:HTTPS_PROXY="<proxy IP>:<proxy port>"`|
| **Linux** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |
| **macOS** | `export HTTPS_PROXY=<proxy IP>:<proxy port>` |

Atualmente, o AzCopy não dá suporte a proxies que exigem autenticação com NTLM ou Kerberos.

### <a name="bypassing-a-proxy"></a>Ignorando um proxy ###

Se você estiver executando o AzCopy no Windows e quiser dizer que ele _não usa nenhum_ proxy (em vez de detectar automaticamente as configurações), use estes comandos. Com essas configurações, o AzCopy não pesquisará nem tentará usar nenhum proxy.

| Sistema operacional | Ambiente | Comandos  |
|--------|-----------|----------|
| **Windows** | Prompt de comando (CMD) | `set HTTPS_PROXY=dummy.invalid` <br>`set NO_PROXY=*`|
| **Windows** | PowerShell | `$env:HTTPS_PROXY="dummy.invalid"` <br>`$env:NO_PROXY="*"`<br>|

Em outros sistemas operacionais, basta deixar a variável de HTTPS_PROXY desdefinida se você quiser usar nenhum proxy.

## <a name="optimize-performance"></a>Otimizar desempenho

Você pode obter o desempenho do benchmark e, em seguida, usar comandos e variáveis de ambiente para encontrar uma compensação ideal entre o consumo de recursos e o desempenho.

Esta seção ajuda você a executar essas tarefas de otimização:

> [!div class="checklist"]
> * Executar testes de benchmark
> * Otimizar a taxa de transferência
> * Otimizar o uso de memória 
> * Otimizar a sincronização de arquivos

### <a name="run-benchmark-tests"></a>Executar testes de benchmark

Você pode executar um teste de benchmark de desempenho em contêineres de BLOB ou compartilhamentos de arquivos específicos para exibir estatísticas gerais de desempenho e para identificar afunilamentos de desempenho. Você pode executar o teste carregando ou baixando dados de teste gerados. 

Use o comando a seguir para executar um teste de benchmark de desempenho.

|    |     |
|--------|-----------|
| **Sintaxe** | `azcopy benchmark 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Exemplo** | `azcopy benchmark 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> Este exemplo inclui argumentos de caminho com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

Esse comando executa um parâmetro de comparação de desempenho carregando dados de teste para um destino especificado. Os dados de teste são gerados na memória, carregados no destino e, em seguida, excluídos do destino após a conclusão do teste. Você pode especificar quantos arquivos serão gerados e qual tamanho você gostaria que eles estivéssemos usando parâmetros de comando opcionais.

Se você preferir executar esse teste baixando dados, defina o `mode` parâmetro como `download` . Para obter documentos de referência detalhados, consulte [benchmark azcopy](storage-ref-azcopy-bench.md). 

### <a name="optimize-throughput"></a>Otimizar a taxa de transferência

Você pode usar o `cap-mbps` sinalizador em seus comandos para inserir um teto na taxa de dados de produtividade. Por exemplo, o comando a seguir retoma um trabalho e a taxa de transferência de Caps para `10` megabits (MB) por segundo. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

A taxa de transferência pode diminuir ao transferir arquivos pequenos. Você pode aumentar a taxa de transferência definindo a `AZCOPY_CONCURRENCY_VALUE` variável de ambiente. Essa variável especifica o número de solicitações simultâneas que podem ocorrer.  

Se o computador tiver menos de 5 CPUs, o valor dessa variável será definido como `32` . Caso contrário, o valor padrão é igual a 16 multiplicado pelo número de CPUs. O valor padrão máximo dessa variável é `3000` , mas você pode definir manualmente esse valor como maior ou menor. 

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **macOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Use o `azcopy env` para verificar o valor atual dessa variável. Se o valor estiver em branco, você poderá ler qual valor está sendo usado observando o início de qualquer arquivo de log do AzCopy. O valor selecionado e o motivo pelo qual ele foi selecionado são relatados lá.

Antes de definir essa variável, recomendamos que você execute um teste de parâmetro de comparação. O processo de teste de benchmark relatará o valor de simultaneidade recomendado. Como alternativa, se as suas condições de rede e suas cargas variam, defina essa variável como a palavra `AUTO` em vez de um número específico. Isso fará com que o AzCopy sempre execute o mesmo processo de ajuste automático que ele usa nos testes de parâmetro de comparação.

### <a name="optimize-memory-use"></a>Otimizar o uso de memória

Defina a `AZCOPY_BUFFER_GB` variável de ambiente para especificar a quantidade máxima de memória do sistema que você deseja que o AzCopy use ao baixar e carregar arquivos.
Expresse esse valor em gigabytes (GB).

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **macOS** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>Otimizar a sincronização de arquivos

O comando [Sync](storage-ref-azcopy-sync.md) identifica todos os arquivos no destino e compara os nomes de arquivo e os últimos carimbos de data/hora antes do início da operação de sincronização. Se você tiver um grande número de arquivos, poderá melhorar o desempenho eliminando esse processamento antecipado. 

Para fazer isso, use o comando [azcopy Copy](storage-ref-azcopy-copy.md) , em vez disso, e defina o `--overwrite` sinalizador como `ifSourceNewer` . O AzCopy comparará os arquivos conforme eles forem copiados sem executar verificações e comparações iniciais. Isso fornece uma margem de desempenho em casos em que há um grande número de arquivos para comparar.

O comando [azcopy Copy](storage-ref-azcopy-copy.md) não exclui arquivos do destino, portanto, se você quiser excluir arquivos no destino quando eles não existirem na origem, use o comando de [sincronização azcopy](storage-ref-azcopy-sync.md) com o `--delete-destination` sinalizador definido como um valor de `true` ou `prompt` . 

## <a name="troubleshoot-issues"></a>Solucionar problemas

O AzCopy cria arquivos de log e de plano para cada trabalho. Você pode usar os logs para investigar e solucionar problemas potenciais. 

Os logs conterão o status de falha ( `UPLOADFAILED` , `COPYFAILED` , e `DOWNLOADFAILED` ), o caminho completo e o motivo da falha.

Por padrão, os arquivos de log e de plano estão localizados no `%USERPROFILE%\.azcopy` diretório no Windows ou `$HOME$\.azcopy` no diretório no Mac e no Linux, mas você pode alterar esse local, se desejar.

O erro relevante não é necessariamente o primeiro erro que aparece no arquivo. Para erros como erros de rede, tempos limite e erros de servidor ocupado, o AzCopy tentará novamente até 20 vezes e, geralmente, o processo de repetição será executado com sucesso.  O primeiro erro que você vê pode ser algo inofensivo que foi repetido com êxito.  Então, em vez de olhar o primeiro erro no arquivo, procure os erros que estão próximos `UPLOADFAILED` , `COPYFAILED` ou `DOWNLOADFAILED` . 

> [!IMPORTANT]
> Ao enviar uma solicitação para Suporte da Microsoft (ou solucionar o problema que envolve terceiros), compartilhe a versão redação do comando que você deseja executar. Isso garante que a SAS não seja compartilhada acidentalmente com ninguém. Você pode encontrar a versão editada no início do arquivo de log.

### <a name="review-the-logs-for-errors"></a>Examinar os logs em busca de erros

O comando a seguir receberá todos os erros com `UPLOADFAILED` o status do `04dc9ca9-158f-7945-5933-564021086c79` log:

**Windows (PowerShell)**

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

Use o comando a seguir para retomar um trabalho com falha/cancelado. Esse comando usa seu identificador junto com o token SAS, pois ele não é persistente por motivos de segurança:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Coloque argumentos de caminho, como o token SAS com aspas simples (' '). Use aspas simples em todos os shells de comando, exceto pelo shell de comando do Windows (cmd.exe). Se você estiver usando um shell de comando do Windows (cmd.exe), coloque os argumentos de caminho com aspas duplas ("") em vez de aspas simples (' ').

Quando você reinicia um trabalho, o AzCopy examina o arquivo de plano de trabalho. O arquivo de plano lista todos os arquivos que foram identificados para processamento quando o trabalho foi criado pela primeira vez. Quando você retomar um trabalho, o AzCopy tentará transferir todos os arquivos listados no arquivo de plano que ainda não foram transferidos.

## <a name="change-the-location-of-the-plan-and-log-files"></a>Alterar o local do plano e dos arquivos de log

Por padrão, os arquivos de plano e de log estão localizados no `%USERPROFILE%\.azcopy` diretório no Windows ou no `$HOME/.azcopy` diretório no Mac e no Linux. Você pode alterar esse local.

### <a name="change-the-location-of-plan-files"></a>Alterar o local dos arquivos de plano

Use qualquer um desses comandos.

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | PowerShell`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> Em um prompt de comando, use:: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Use o `azcopy env` para verificar o valor atual dessa variável. Se o valor estiver em branco, os arquivos de plano serão gravados no local padrão.

### <a name="change-the-location-of-log-files"></a>Alterar o local dos arquivos de log

Use qualquer um desses comandos.

| Sistema operacional | Comando  |
|--------|-----------|
| **Windows** | PowerShell`$env:AZCOPY_LOG_LOCATION="<value>"` <br> Em um prompt de comando, use:: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Use o `azcopy env` para verificar o valor atual dessa variável. Se o valor estiver em branco, os logs serão gravados no local padrão.

## <a name="change-the-default-log-level"></a>Alterar o nível de log padrão

Por padrão, o nível de log AzCopy é definido como `INFO` . Se você quiser reduzir o detalhamento de log para economizar espaço em disco, substitua essa configuração usando a ``--log-level`` opção. 

Os níveis de log disponíveis são: `NONE` , `DEBUG` , `INFO` ,, `WARNING` `ERROR` , `PANIC` e `FATAL` .

## <a name="remove-plan-and-log-files"></a>Remover arquivos de plano e de log

Se você quiser remover todos os arquivos de plano e de log do computador local para economizar espaço em disco, use o `azcopy jobs clean` comando.

Para remover o plano e os arquivos de log associados a apenas um trabalho, use `azcopy jobs rm <job-id>` . Substitua o `<job-id>` espaço reservado neste exemplo pela ID do trabalho.
