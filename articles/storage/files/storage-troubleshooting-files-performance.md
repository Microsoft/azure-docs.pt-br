---
title: Guia de solução de problemas de desempenho de arquivos do Azure
description: Problemas de desempenho com compartilhamentos de arquivos do Azure e soluções alternativas associadas conhecidos.
services: storage
author: gunjanj
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 8c35501f3afbeed519fb5304229f25be1cbd5f9b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445666"
---
# <a name="troubleshoot-azure-files-performance-issues"></a>Solucionar problemas de desempenho do arquivos do Azure

Este artigo lista alguns problemas comuns relacionados aos compartilhamentos de arquivos do Azure. Ele fornece possíveis causas e soluções alternativas quando esses problemas são encontrados.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Problemas de desempenho geral de alta latência e baixa taxa de transferência

### <a name="cause-1-share-experiencing-throttling"></a>Causa 1: Compartilhar a limitação está enfrentando

A cota padrão em um compartilhamento de premium é 100 GiB, que fornece a linha de base de 100 IOPS (com um potencial de intermitente até 300 por uma hora). Para obter mais informações sobre provisionamento e sua relação com a IOPS, consulte o [provisionado compartilhamentos](storage-files-planning.md#provisioned-shares) seção do guia de planejamento.

Para confirmar se o compartilhamento está sendo limitado, você pode aproveitar as métricas do Azure no portal.

1. Entre no [Portal do Azure](https://portal.azure.com).

1. Selecione **todos os serviços** e, em seguida, pesquise por **métricas**.

1. Selecione **Métricas**.

1. Selecione sua conta de armazenamento que o recurso.

1. Selecione **arquivo** como o namespace da métrica.

1. Selecione **transações** como a métrica.

1. Adicione um filtro para **ResponseType** e verifique se todas as solicitações têm um código de resposta **SuccessWithThrottling** (para o protocolo SMB) ou **ClientThrottlingError** (por REST).

![Opções de métricas para compartilhamentos de arquivos do premium](media/storage-troubleshooting-premium-fileshares/metrics.png)

### <a name="solution"></a>Solução

- Aumento compartilhar capacidade provisionada, especificando uma cota maior em seu compartilhamento.

### <a name="cause-2-metadatanamespace-heavy-workload"></a>Causa 2: A carga de trabalho pesada/namespace de metadados

Se a maioria de suas solicitações é metadados centrado em (como o createfile/openfile/closefile/queryinfo/querydirectory), a latência será pior quando comparado com operações de leitura/gravação.

Para confirmar se a maioria das suas solicitações são metadados centralizado no, você pode usar as mesmas etapas acima. Exceto em vez de adicionar um filtro para **ResponseType**, adicione um filtro para **nome da API**.

![Filtrar por nome da API em suas métricas](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Solução alternativa

- Verifique se o aplicativo pode ser modificado para reduzir o número de operações de metadados.

### <a name="cause-3-single-threaded-application"></a>Causa 3: Aplicativo single-threaded

Se o aplicativo que está sendo usado pelo cliente é single-threaded, isso pode resultar em IOPS/taxa de transferência significativamente menor do que o máximo possível com base em seu tamanho do compartilhamento provisionado.

### <a name="solution"></a>Solução

- Aumente o paralelismo de aplicativo, aumentando o número de threads.
- Alternar para aplicativos em que o paralelismo é possível. Por exemplo, para operações de cópia, os clientes pode usar o AzCopy ou RoboCopy de clientes do Windows ou o **paralela** comando nos clientes Linux.

## <a name="very-high-latency-for-requests"></a>Latência muito alta de solicitações

### <a name="cause"></a>Causa

A VM do cliente pode estar localizado em uma região diferente que o compartilhamento de arquivos.

### <a name="solution"></a>Solução

- Execute o aplicativo de uma VM que está localizada na mesma região que o compartilhamento de arquivos.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Não é possível obter a taxa de transferência máxima com suporte pela rede do cliente

Uma causa potencial disso é a falta fo SMB suporte a vários canais. Atualmente, compartilhamentos de arquivos do Azure suportam apenas canal único, para que haja apenas uma conexão do cliente VM para o servidor. Essa conexão única vinculada a um único núcleo na VM cliente, para que a taxa de transferência máxima que pode ser obtida de uma VM é associada por um único núcleo.

### <a name="workaround"></a>Solução alternativa

- Obtenção de uma VM com um núcleo maior pode ajudar a melhorar a taxa de transferência.
- Executar o aplicativo cliente de várias VMs aumentará a taxa de transferência.
- Use APIs REST, sempre que possível.

## <a name="throughput-on-linux-clients-is-significantly-lower-when-compared-to-windows-clients"></a>Taxa de transferência de clientes Linux é significativamente menor em comparação com os clientes do Windows.

### <a name="cause"></a>Causa

Esse é um problema conhecido com a implementação do cliente SMB no Linux.

### <a name="workaround"></a>Solução alternativa

- Distribuir a carga entre várias VMs
- Na mesma VM, use vários pontos de montagem com **nosharesock** opção e spread como a carga entre esses pontos de montagem.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Latências altas para cargas de trabalho pesadas metadados que envolva operações de abertura/fechamento extensivas.

### <a name="cause"></a>Causa

Falta de suporte para concessões de diretório.

### <a name="workaround"></a>Solução alternativa

- Se possível, evite o identificador de abertura/fechamento excessiva no mesmo diretório em um curto período de tempo.
- Para VMs do Linux, aumentar o tempo de limite de cache de entrada de diretório, especificando **actimeo =<sec>**  como uma opção de montagem. Por padrão, ele é um segundo, para que um valor maior, como três ou cinco pode ajudar.
- Para VMs Linux, atualize o kernel para 4.20 ou superior.

## <a name="low-iops-on-centosrhel"></a>IOPS de baixa no RHEL/CentOS

### <a name="cause"></a>Causa

Não há suporte para a profundidade de e/s maior que um no CentOS/RHEL.

### <a name="workaround"></a>Solução alternativa

- Atualizar para o 8 de CentOS / RHEL 8.
- Altere para o Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-files-in-linux"></a>Cópia de arquivos bidirecional lenta dos Arquivos do Azure no Linux

Se você estiver tendo a cópia de arquivo lenta para e dos arquivos do Azure, dê uma olhada a [lenta a cópia de arquivos do Azure no Linux arquivos](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux) guia de seção de solução de problemas do Linux.

## <a name="jitterysaw-tooth-pattern-for-iops"></a>Padrão de trêmulo/saw-tooth para IOPS

### <a name="cause"></a>Causa

Aplicativo cliente excede consistentemente o IOPS de linha de base. Atualmente, não há nenhum lado de serviço suavização da carga de solicitação, portanto, se o cliente exceder a linha de base de IOPS, haverá restrição pelo serviço. Essa limitação pode resultar no cliente que apresenta um padrão IOPS trêmulo/saw-tooth. Nesse caso, o IOPS médio obtido pelo cliente pode ser menor do que a linha de base IOPS.

### <a name="workaround"></a>Solução alternativa

- Reduza a carga de solicitação do aplicativo cliente, para que o compartilhamento não será limitado.
- Aumente a cota do compartilhamento para que o compartilhamento não será limitado.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Chamadas de DirectoryOpen/DirectoryClose excessivas

### <a name="cause"></a>Causa

Se o número de chamadas DirectoryOpen/DirectoryClose estiver entre as chamadas de API principais e você não espera que o cliente a fazer que o número de chamadas, ele pode ser um problema com o antivírus instalado na VM cliente do Azure.

### <a name="workaround"></a>Solução alternativa

- Para corrigir esse problema está disponível na [abril atualização de plataforma para Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>Criação do arquivo é mais lenta do que o esperado

### <a name="cause"></a>Causa

Cargas de trabalho que se baseiam na criação de um grande número de arquivos não verá uma diferença significativa entre o desempenho premium de compartilhamentos de arquivos e compartilhamentos de arquivos padrão.

### <a name="workaround"></a>Solução alternativa

- Nenhuma.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Lentidão no desempenho do Windows 8.1 ou Server 2012 R2

### <a name="cause"></a>Causa

Maior do que esperado latência ao acessar arquivos do Azure para cargas de trabalho intensivas de e/s.

### <a name="workaround"></a>Solução alternativa

- Instalar o disponíveis [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1).