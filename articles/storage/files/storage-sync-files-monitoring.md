---
title: Monitorar a Sincronização de Arquivos do Azure | Microsoft Docs
description: Como monitorar a Sincronização de Arquivos do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 86c4bf328430bbc623d8e493eec5db520d50ef82
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485986"
---
# <a name="monitor-azure-file-sync"></a>Monitorar a Sincronização de Arquivos do Azure

Use a Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em Arquivos do Azure enquanto mantém a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. A Sincronização de arquivos do Azure transforma o Windows Server em um cache rápido do compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo descreve como monitorar a implantação da sincronização de arquivos do Azure usando o Azure Monitor, o serviço de sincronização de armazenamento e o Windows Server.

As seguintes opções de monitoramento estão disponíveis no momento.

## <a name="azure-monitor"></a>Azure Monitor

Use [do Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) para exibir as métricas e configurar alertas para a sincronização, disposição em camadas e conectividade do servidor nuvem.  

### <a name="metrics"></a>metrics

As métricas para a Sincronização de Arquivos do Azure são habilitadas por padrão e são enviadas para o Azure Monitor a cada 15 minutos.

Para exibir as métricas de sincronização de arquivos do Azure no Azure Monitor, selecione a **serviços de sincronização de armazenamento** tipo de recurso.

As métricas a seguir para a Sincronização de Arquivos do Azure estão disponíveis no Azure Monitor:

| Nome da métrica | DESCRIÇÃO |
|-|-|
| Bytes sincronizados | Tamanho dos dados transferidos (upload e download).<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensões aplicáveis: Nome do ponto de extremidade do servidor, direção da sincronização, nome de grupo de sincronização |
| Recall da camada de nuvem | Tamanho dos dados em recall.<br><br>**Observação**: Essa métrica será removida no futuro. Use a métrica de tamanho de recall em camadas da nuvem para monitorar o tamanho dos dados recuperados.<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensão aplicável: Nome do Servidor |
| Tamanho de recall em camadas na nuvem | Tamanho dos dados em recall.<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensão aplicável: Nome do nome de servidor, grupo de sincronização |
| Tamanho de recall em camadas na nuvem por aplicativo | Tamanho dos dados recuperados pelo aplicativo.<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensão aplicável: Nome do aplicativo nome, servidor nome, grupo de sincronização |
| Taxa de transferência de recall em camadas na nuvem | Tamanho da taxa de transferência de recolhimento de dados.<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensão aplicável: Nome do nome de servidor, grupo de sincronização |
| Arquivos não sincronizando | Contagem de arquivos que estão falhando em sincronizar.<br><br>Unidade: Count<br>Tipo de agregação: Sum<br>Dimensões aplicáveis: Nome do ponto de extremidade do servidor, direção da sincronização, nome de grupo de sincronização |
| Arquivos sincronizados | Contagem dos arquivos transferidos (upload e download).<br><br>Unidade: Count<br>Tipo de agregação: Sum<br>Dimensões aplicáveis: Nome do ponto de extremidade do servidor, direção da sincronização, nome de grupo de sincronização |
| Status online do servidor | Contagem de pulsações recebidas do servidor.<br><br>Unidade: Count<br>Tipo de agregação: Máximo<br>Dimensão aplicável: Nome do Servidor |
| Resultado da sessão de sincronização | Resultado da sessão de sincronização (1 = sessão de sincronização bem-sucedida; 0 = sessão de sincronização com falha)<br><br>Unidade: Contagem<br>Tipos de agregação: Máximo<br>Dimensões aplicáveis: Nome do ponto de extremidade do servidor, direção da sincronização, nome de grupo de sincronização |

### <a name="alerts"></a>Alertas

Para configurar alertas no Azure Monitor, selecione o serviço de sincronização de armazenamento e, em seguida, selecione a [métrica de sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) a ser usado para o alerta.  

A tabela a seguir lista alguns cenários de exemplo para monitorar e a métrica apropriada a ser usado para o alerta:

| Cenário | Métrica a ser usada para alerta |
|-|-|
| Integridade do ponto de extremidade do servidor no portal do = erro | Resultado da sessão de sincronização |
| Arquivos estão falhando para um servidor de sincronização ou ponto de extremidade de nuvem | Arquivos não sincronizando |
| Falha de comunicação com o serviço de sincronização de armazenamento do servidor registrado | Status online do servidor |
| Tamanho de recall em camadas de nuvem excedeu 500GiB em um dia  | Tamanho de recall em camadas na nuvem |

Para saber mais sobre como configurar alertas no Azure Monitor, consulte [visão geral dos alertas no Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento

Para exibir a integridade de servidor registrado, integridade do ponto de extremidade de servidor e métricas, vá para o serviço de sincronização de armazenamento no portal do Azure. Você pode exibir a integridade do servidor registrado na **servidores registrados** folha e o servidor de integridade do ponto de extremidade na **grupos de sincronização** folha.

### <a name="registered-server-health"></a>Integridade do servidor registrado

- Se o **servidor registrado** o estado é **Online**, o servidor está se comunicando com o serviço.
- Se o **servidor registrado** o estado é **aparece Offline**, verifique se o processo de Monitor de sincronização de armazenamento (AzureStorageSyncMonitor.exe) no servidor está em execução. Se o servidor estiver atrás de um firewall ou proxy, consulte [deste artigo](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) para configurar o firewall e proxy.

### <a name="server-endpoint-health"></a>Integridade do ponto de extremidade de servidor

- A integridade do ponto de extremidade do servidor no portal baseia-se nos eventos de sincronização registrados no log de eventos de telemetria no servidor (ID 9102 e 9302). Se uma sessão de sincronização falhar devido a um erro transitório, como erro cancelada, sincronização ainda poderá aparecer íntegra no portal enquanto a sessão de sincronização atual está progredindo. ID do evento 9302 é usado para determinar se os arquivos estão sendo aplicados. Para obter mais informações, consulte [integridade de sincronização](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [sincronizar progresso](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Se o portal mostra um erro de sincronização porque a sincronização não está progredindo, consulte o [documentação de solução de problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para obter orientação.

### <a name="metric-charts"></a>Gráficos de métricas

- Os gráficos de métricas a seguir são visíveis no portal do serviço de sincronização de armazenamento:

  | Nome da métrica | DESCRIÇÃO | Nome da folha |
  |-|-|-|
  | Bytes sincronizados | Tamanho dos dados transferidos (upload e download) | Grupo de sincronização, ponto de extremidade do servidor |
  | Recall da camada de nuvem | Tamanho dos dados em recall | Servidores registrados |
  | Arquivos não sincronizando | Contagem de arquivos que estão falhando ao sincronizar | Ponto de extremidade do servidor |
  | Arquivos sincronizados | Contagem dos arquivos transferidos (upload e download) | Grupo de sincronização, ponto de extremidade do servidor |
  | Status Online do Servidor | Contagem de pulsações recebidas do servidor | Servidores registrados |

- Para obter mais informações, consulte [do Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Os gráficos no portal do Serviço de Sincronização de Armazenamento possuem um intervalo de tempo de 24 horas. Para exibir os diferentes intervalos de tempo ou dimensões, use o Azure Monitor.

## <a name="windows-server"></a>Windows Server

No Windows Server, você pode exibir as camadas, de nuvem servidor registrado e integridade de sincronização.

### <a name="event-logs"></a>Logs de eventos

Use o log de eventos de telemetria no servidor para monitorar a integridade do servidor registrado, da sincronização e das camadas de nuvem. O log de eventos de telemetria está localizado no Visualizador de eventos sob *aplicativos e services\microsoft\filesync\agent.* .

Integridade de sincronização:

- ID do evento 9102 é registrado após a conclusão de uma sessão de sincronização. Use esse evento para determinar se as sessões de sincronização são bem-sucedidas (**HResult = 0**) erros de sincronização e se há por item. Para obter mais informações, consulte o [integridade de sincronização](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) e [erros por item](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) documentação.

  > [!Note]  
  > Às vezes, as sessões de sincronização falharem em geral ou tem um PerItemErrorCount diferente de zero. No entanto, eles ainda progridam e alguns arquivos de sincronização com êxito. Você pode ver isso nos campos, como AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes aplicados. Esses campos informam quanto da sessão foi bem-sucedida. Se você ver várias sessões de sincronização falhar em uma linha, e eles têm uma contagem aplicada a cada vez maior, ao tempo de sincronização para tentar novamente antes de abrir um tíquete de suporte.

- A ID do evento 9302 é registrada a cada 5 a 10 minutos, se há uma sessão de sincronização ativa. Use esse evento para determinar se a sessão de sincronização atual está progredindo (**AppliedItemCount > 0**). Se a sincronização não está progredindo, a sessão de sincronização deve falhar, eventualmente, e um 9102 de ID de evento será registrado com o erro. Para obter mais informações, consulte o [documentação do progresso de sincronização](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Integridade do servidor registrado:

- A ID do evento 9301 é registrada a cada 30 segundos quando um servidor consulta o serviço em busca de trabalhos. Se GetNextJob termina com **status = 0**, o servidor é capaz de se comunicar com o serviço. Verifique se GetNextJob termina com um erro, o [documentação de solução de problemas](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) para obter orientação.

Integridade de camadas de nuvem:

- Para monitorar atividade em camadas em um servidor, use o evento ID 9003, 9016 e 9029 no log de eventos de telemetria, que está localizado no Visualizador de eventos em *aplicativos e services\microsoft\filesync\agent.* .

  - A identificação de evento 9003 fornece distribuição de erro para um terminal do servidor. Por exemplo:  Contagem total de erros e o código de erro. Um evento é registrado por código de erro.
  - A identificação de evento 9016 fornece resultados de fantasma para um volume. Por exemplo: Porcentagem de espaço livre é, número de arquivos fantasma na sessão, e o número de arquivos não puderam ser fantasmas.
  - A ID do evento 9029 fornece informações de sessão de conversão em fantasma para um ponto de extremidade de servidor. Por exemplo:  Número de arquivos tentada na sessão, o número de arquivos em camadas na sessão e o número de arquivos já em camadas.
  
- Para monitorar a atividade de recuperação em um servidor, use o evento ID 9005, 9006, 9009 e 9059 no log de eventos de telemetria, que está localizado no Visualizador de eventos em *aplicativos e services\microsoft\filesync\agent.* .

  - A ID de evento 9005 fornece confiabilidade de recall para um ponto de extremidade do servidor. Por exemplo: Total de arquivos exclusivos acessados e Total de arquivos exclusivos com falhas de acesso.
  - ID do evento 9006 fornece Lembre-se a distribuição de erro para um ponto de extremidade do servidor. Por exemplo: Total de solicitações com falha e o código de erro. Um evento é registrado por código de erro.
  - A ID do evento 9009 fornece informações de sessão de recall para um ponto de extremidade de servidor. Por exemplo: DurationSeconds, CountFilesRecallSucceeded e CountFilesRecallFailed.
  - A ID do evento 9059 fornece distribuição de recall do aplicativo para um ponto de extremidade de servidor. Por exemplo:  ShareId, nome do aplicativo e TotalEgressNetworkBytes.

### <a name="performance-counters"></a>contadores de desempenho

Use os contadores de desempenho da Sincronização de Arquivos do Azure no servidor para monitorar a atividade de sincronização.

Para exibir os contadores de desempenho de sincronização de arquivos do Azure no servidor, abra o Monitor de desempenho (Perfmon.exe). Você pode encontrar os contadores na **AFS Bytes transferidos** e **operações de sincronização AFS** objetos.

Os seguintes contadores de desempenho para a Sincronização de Arquivos do Azure estão disponíveis no Monitor de Desempenho:

| Objeto de Desempenho\Nome do Contador | DESCRIÇÃO |
|-|-|
| Bytes de AFS Transferidos\Bytes Baixados/s | Número de bytes baixados por segundo. |
| Bytes de AFS Transferidos\Bytes Carregados/s | Número de bytes carregados por segundo. |
| Bytes de AFS Transferidos\Total de Bytes/s | Total de bytes por segundo (upload e download). |
| Operações de Sincronização de AFS\Arquivos de Sincronização Baixados/s | Número de arquivos baixados por segundo. |
| Operações de Sincronização de AFS\Arquivos de Sincronização Carregados/s | Número de arquivos carregados por segundo. |
| Operações de Sincronização de AFS\Total de Operações de Arquivo de Sincronização/s | Número total de arquivos sincronizados (upload e download). |

## <a name="next-steps"></a>Próximas etapas
- [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Considere as configurações de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
- [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
- [Solucionar problemas da Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md)
- [Perguntas frequentes da Arquivos do Azure](storage-files-faq.md)
