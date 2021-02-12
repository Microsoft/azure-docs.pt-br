---
title: Monitorar a Sincronização de Arquivos do Azure | Microsoft Docs
description: Examine como monitorar sua implantação de Sincronização de Arquivos do Azure usando o Azure Monitor, o serviço de sincronização de armazenamento e o Windows Server.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/28/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1cc2cd1a7c5c16b1f9d1542e3f2d14dc030bb090
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586543"
---
# <a name="monitor-azure-file-sync"></a>Monitorar a Sincronização de Arquivos do Azure

Use a Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em Arquivos do Azure enquanto mantém a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. A Sincronização de arquivos do Azure transforma o Windows Server em um cache rápido do compartilhamento de arquivos do Azure. Use qualquer protocolo disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter tantos caches quantos precisar em todo o mundo.

Este artigo descreve como monitorar sua implantação do Sincronização de Arquivos do Azure usando Azure Monitor, o serviço de sincronização de armazenamento e o Windows Server.

Os cenários a seguir são abordados neste guia: 
- Exiba Sincronização de Arquivos do Azure métricas no Azure Monitor.
- Crie alertas no Azure Monitor para notificá-lo proativamente sobre condições críticas.
- Exiba a integridade de sua implantação do Sincronização de Arquivos do Azure usando o portal do Azure.
- Como usar os logs de eventos e os contadores de desempenho em seus servidores Windows para monitorar a integridade de sua implantação de Sincronização de Arquivos do Azure. 

## <a name="azure-monitor"></a>Azure Monitor

Use [Azure monitor](../../azure-monitor/overview.md) para exibir as métricas e configurar alertas para sincronização, camadas de nuvem e conectividade de servidor.  

### <a name="metrics"></a>Métricas

As métricas para a Sincronização de Arquivos do Azure são habilitadas por padrão e são enviadas para o Azure Monitor a cada 15 minutos.

**Como exibir métricas de Sincronização de Arquivos do Azure no Azure Monitor**
1. Vá para o **serviço de sincronização de armazenamento** no **portal do Azure** e clique em **métricas**.
2. Clique na lista suspensa **métrica** e selecione a métrica que você deseja exibir.

![Captura de tela de métricas de Sincronização de Arquivos do Azure](media/storage-sync-files-troubleshoot/file-sync-metrics.png)

As métricas a seguir para a Sincronização de Arquivos do Azure estão disponíveis no Azure Monitor:

| Nome da métrica | Descrição |
|-|-|
| Bytes sincronizados | Tamanho dos dados transferidos (upload e download).<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensões aplicáveis: nome do ponto de extremidade do servidor, direção de sincronização, nome do grupo de sincronização |
| Recall da camada de nuvem | Tamanho dos dados em recall.<br><br>**Observação**: essa métrica será removida no futuro. Use a métrica de tamanho de recuperação de camadas de nuvem para monitorar o tamanho dos dados recuperados.<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensão aplicável: nome do servidor |
| Tamanho de recall da camada de nuvem | Tamanho dos dados em recall.<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensões aplicáveis: nome do servidor, nome do grupo de sincronização |
| Tamanho de recall da camada de nuvem por aplicativo | Tamanho dos dados recuperados pelo aplicativo.<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensões aplicáveis: nome do aplicativo, nome do servidor, nome do grupo de sincronização |
| Taxa de transferência de recall da camada de nuvem | Tamanho da taxa de transferência de recall de dados.<br><br>Unidade: Bytes<br>Tipo de agregação: Sum<br>Dimensões aplicáveis: nome do servidor, nome do grupo de sincronização |
| Arquivos não sincronizando | Contagem de arquivos que estão falhando em sincronizar.<br><br>Unidade: Contagem<br>Tipos de agregação: média, soma<br>Dimensões aplicáveis: nome do ponto de extremidade do servidor, direção de sincronização, nome do grupo de sincronização |
| Arquivos sincronizados | Contagem dos arquivos transferidos (upload e download).<br><br>Unidade: Contagem<br>Tipo de agregação: Sum<br>Dimensões aplicáveis: nome do ponto de extremidade do servidor, direção de sincronização, nome do grupo de sincronização |
| Status online do servidor | Contagem de pulsações recebidas do servidor.<br><br>Unidade: Contagem<br>Tipo de agregação: Máximo<br>Dimensão aplicável: nome do servidor |
| Resultado da sessão de sincronização | Resultado da sessão de sincronização (1 = sessão de sincronização bem-sucedida; 0 = sessão de sincronização com falha)<br><br>Unidade: Contagem<br>Tipos de agregação: máximo<br>Dimensões aplicáveis: nome do ponto de extremidade do servidor, direção de sincronização, nome do grupo de sincronização |

### <a name="alerts"></a>Alertas

Os alertas trabalham de forma proativa, mandando notificações quando encontram condições importante em seus dados de monitoramento. Para saber mais sobre como configurar alertas no Azure Monitor, consulte [visão geral de alertas no Microsoft Azure](../../azure-monitor/platform/alerts-overview.md).

**Como criar alertas para Sincronização de Arquivos do Azure**

1. Vá para o **serviço de sincronização de armazenamento** no **portal do Azure**. 
2. Clique em **alertas** na seção monitoramento e, em seguida, clique em **+ nova regra de alerta**.
3. Clique em **Selecionar condição** e forneça as seguintes informações para o alerta: 
    - **Métrica**
    - **Nome da dimensão**
    - **Lógica de alerta**
4. Clique em **selecionar grupo de ações** e adicione um grupo de ações (email, SMS, etc.) ao alerta selecionando um grupo de ações existente ou criando um novo grupo de ação.
5. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, **Descrição** e **severidade**.
6. Clique em **criar regra de alerta** para criar o alerta.  

A tabela a seguir lista alguns cenários de exemplo para monitorar e a métrica apropriada a ser usada para o alerta:

| Cenário | Métrica a ser usada para alerta |
|-|-|
| A integridade do ponto de extremidade do servidor mostra um erro no portal | Resultado da sessão de sincronização |
| Os arquivos estão falhando ao sincronizar com um ponto de extremidade de servidor ou de nuvem | Arquivos não sincronizando |
| O servidor registrado não está conseguindo se comunicar com o serviço de sincronização de armazenamento | Status online do servidor |
| O tamanho de recall em camadas de nuvem excedeu 500GiB em um dia  | Tamanho de recall da camada de nuvem |

Para obter instruções sobre como criar alertas para esses cenários, consulte a seção [exemplos de alertas](#alert-examples) .

## <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento

Para exibir a integridade de sua implantação de Sincronização de Arquivos do Azure no **portal do Azure**, navegue até o **serviço de sincronização de armazenamento** e as seguintes informações estão disponíveis:

- Integridade do servidor registrado
- Integridade do ponto de extremidade do servidor
    - Arquivos não sincronizando
    - Atividade de sincronização
    - Eficiência de camadas de nuvem
    - Arquivos sem camadas
    - Erros de recuperação
- Métricas

### <a name="registered-server-health"></a>Integridade do servidor registrado

Para exibir a **integridade do servidor registrado** no portal, navegue até a seção **servidores registrados** do **serviço de sincronização de armazenamento**.

![Captura de tela de integridade de servidores registrados](media/storage-sync-files-troubleshoot/file-sync-registered-servers.png)

- Se o estado do **servidor registrado** estiver **online**, o servidor estará se comunicando com êxito com o serviço.
- Se o estado do **servidor registrado** for **exibido offline**, o processo do monitor de sincronização de armazenamento (AzureStorageSyncMonitor.exe) não estará em execução ou o servidor não poderá acessar o serviço de sincronização de arquivos do Azure. Consulte a [documentação de solução de problemas](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) para obter diretrizes.

### <a name="server-endpoint-health"></a>Integridade do ponto de extremidade do servidor

Para exibir a integridade de um **ponto de extremidade do servidor** no portal, navegue até a seção **grupos de sincronização** do serviço de **sincronização de armazenamento** e selecione um **grupo de sincronização**.

![Captura de tela da integridade do ponto de extremidade do servidor](media/storage-sync-files-troubleshoot/file-sync-server-endpoint-health.png)

- A **atividade de sincronização** e integridade do ponto de **extremidade do servidor** no portal baseia-se nos eventos de sincronização que são registrados no log de eventos de telemetria no servidor (ID 9102 e 9302). Se uma sessão de sincronização falhar devido a um erro transitório, como o erro cancelado, o ponto de extremidade do servidor ainda será mostrado como **íntegro** no portal, desde que a sessão de sincronização atual esteja progredindo (os arquivos são aplicados). A ID de evento 9302 é o evento de progresso de sincronização e a ID de evento 9102 é registrada quando uma sessão de sincronização é concluída.  Para obter mais informações, consulte [sincronizar integridade](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) e [sincronizar andamento](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session). Se a integridade do ponto de extremidade do servidor mostrar um **erro** ou **nenhuma atividade**, consulte a [documentação de solução de problemas](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#common-sync-errors) para obter diretrizes.
- A contagem de **arquivos não sincronizados** no portal é baseada na ID de evento 9121 registrada no log de eventos de telemetria no servidor. Esse evento é registrado para cada erro por item quando a sessão de sincronização é concluída. Para resolver erros por item, consulte [como fazer ver se há arquivos ou pastas específicas que não estão sincronizando?](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).
- Para exibir a **eficiência de camadas de nuvem** no portal, vá para as **Propriedades de ponto de extremidade do servidor** e navegue até a seção camadas de **nuvem** . Os dados fornecidos para a eficiência de camadas de nuvem baseiam-se na ID de evento 9071 que é registrada no log de eventos de telemetria no servidor. Para saber mais, consulte [Visão geral da camada de nuvem](./storage-sync-cloud-tiering.md).
- Para exibir **arquivos que não** são em camadas e **recuperar erros** no portal, vá para as **Propriedades de ponto de extremidade do servidor** e navegue até a seção camadas de **nuvem** . **Os arquivos que não são camadas** baseiam-se na ID de evento 9003 que é registrada no log de eventos de telemetria no servidor e os **erros de recuperação** se baseiam na ID de evento 9006. Para investigar os arquivos que estão falhando na camada ou na recuperação, consulte [como solucionar problemas de arquivos que falham na camada](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-tier) e [como solucionar problemas de arquivos que não puderam ser recuperados](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#how-to-troubleshoot-files-that-fail-to-be-recalled).

### <a name="metric-charts"></a>Gráficos de métricas

- Os gráficos de métrica a seguir são visíveis no portal do serviço de sincronização de armazenamento:

  | Nome da métrica | Descrição | Nome da folha |
  |-|-|-|
  | Bytes sincronizados | Tamanho dos dados transferidos (upload e download) | Grupo de sincronização, ponto de extremidade do servidor |
  | Recall da camada de nuvem | Tamanho dos dados em recall | Servidores registrados |
  | Arquivos não sincronizando | Contagem de arquivos que estão falhando ao sincronizar | Ponto de extremidade do servidor |
  | Arquivos sincronizados | Contagem dos arquivos transferidos (upload e download) | Grupo de sincronização, ponto de extremidade do servidor |
  | Status online do servidor | Contagem de pulsações recebidas do servidor | Servidores registrados |

- Para saber mais, consulte [Azure monitor](#azure-monitor).

  > [!Note]  
  > Os gráficos no portal do Serviço de Sincronização de Armazenamento possuem um intervalo de tempo de 24 horas. Para exibir os diferentes intervalos de tempo ou dimensões, use o Azure Monitor.

## <a name="windows-server"></a>Windows Server

No **Windows Server** que tem o agente de sincronização de arquivos do Azure instalado, você pode exibir a integridade dos pontos de extremidade do servidor no servidor usando os **logs de eventos** e os **contadores de desempenho**.

### <a name="event-logs"></a>Logs de eventos

Use o log de eventos de telemetria no servidor para monitorar a integridade do servidor registrado, da sincronização e das camadas de nuvem. O log de eventos de telemetria está localizado em Visualizador de Eventos em *Applications e Services\Microsoft\FileSync\Agent*.

Integridade da sincronização

- A ID do evento 9102 é registrada quando uma sessão de sincronização é concluída. Use esse evento para determinar se as sessões de sincronização são bem-sucedidas (**HRESULT = 0**) e se há erros de sincronização por item (**PerItemErrorCount**). Para obter mais informações, consulte a documentação [sincronizar integridade](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#broken-sync) e  [erros por item](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) .

  > [!Note]  
  > Às vezes, as sessões de sincronização falham em geral ou têm um PerItemErrorCount diferente de zero. No entanto, eles ainda encaminham o progresso e alguns arquivos são sincronizados com êxito. Você pode ver isso nos campos aplicados, como AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes. Esses campos informam quanto da sessão foi bem-sucedida. Se você vir a falha de várias sessões de sincronização em uma linha e elas tiverem uma contagem aplicada crescente, forneça o tempo de sincronização para tentar novamente antes de abrir um tíquete de suporte.

- A ID de evento 9121 é registrada para cada erro por item quando a sessão de sincronização é concluída. Use esse evento para determinar o número de arquivos que estão falhando na sincronização com esse erro (**PersistentCount** e **TransientCount**). Os erros persistentes por item devem ser investigados, consulte [como fazer ver se há arquivos ou pastas específicas que não estão sincronizando?](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

- A ID do evento 9302 é registrada a cada 5 a 10 minutos, se há uma sessão de sincronização ativa. Use esse evento para determinar quantos itens devem ser sincronizados (**TotalItemCount**), o número de itens que foram sincronizados até o momento (**AppliedItemCount**) e o número de itens que falharam na sincronização devido a um erro por item (**PerItemErrorCount**). Se a sincronização não estiver fazendo o andamento (**AppliedItemCount = 0**), a sessão de sincronização eventualmente falhará e uma ID de evento 9102 será registrada com o erro. Para obter mais informações, consulte a [documentação progresso da sincronização](./storage-sync-files-troubleshoot.md?tabs=server%252cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Integridade do servidor registrado

- A ID do evento 9301 é registrada a cada 30 segundos quando um servidor consulta o serviço em busca de trabalhos. Se GetNextJob for concluído com **status = 0**, o servidor será capaz de se comunicar com o serviço. Se GetNextJob for concluído com um erro, consulte a [documentação de solução de problemas](./storage-sync-files-troubleshoot.md?tabs=portal1%252cazure-portal#server-endpoint-noactivity) para obter diretrizes.

Integridade de camadas de nuvem

- Para monitorar a atividade de camadas em um servidor, use a ID de evento 9003, 9016 e 9029 no log de eventos de telemetria, que está localizado em Visualizador de Eventos em *Applications e Services\Microsoft\FileSync\Agent*.

  - A identificação de evento 9003 fornece distribuição de erro para um terminal do servidor. Por exemplo: contagem de erros total e ErrorCode. Um evento é registrado por código de erro.
  - A identificação de evento 9016 fornece resultados de fantasma para um volume. Por exemplo: a porcentagem de espaço livre é, o número de arquivos fantasmas na sessão e o número de arquivos com falha no fantasma.
  - A ID do evento 9029 fornece informações de sessão de conversão em fantasma para um ponto de extremidade de servidor. Por exemplo: o número de arquivos tentados na sessão, o número de arquivos em camadas na sessão e o número de arquivos já em camadas.

- Para monitorar a atividade de recuperação em um servidor, use a ID de evento 9005, 9006, 9009, 9059 e 9071 no log de eventos de telemetria, localizado em Visualizador de Eventos em *Applications and Services\Microsoft\FileSync\Agent*.

  - A ID de evento 9005 fornece confiabilidade de recall para um ponto de extremidade do servidor. Por exemplo: total de arquivos exclusivos acessados e total de arquivos exclusivos com falha de acesso.
  - ID do evento 9006 fornece Lembre-se a distribuição de erro para um ponto de extremidade do servidor. Por exemplo: total de solicitações com falha e ErrorCode. Um evento é registrado por código de erro.
  - A ID do evento 9009 fornece informações de sessão de recall para um ponto de extremidade de servidor. Por exemplo: DurationSeconds, CountFilesRecallSucceeded e CountFilesRecallFailed.
  - A ID do evento 9059 fornece distribuição de recall do aplicativo para um ponto de extremidade de servidor. Por exemplo: Shareid, nome do aplicativo e TotalEgressNetworkBytes.
  - A ID de evento 9071 fornece eficiência de camadas de nuvem para um ponto de extremidade do servidor. Por exemplo: TotalDistinctFileCountCacheHit, TotalDistinctFileCountCacheMiss, TotalCacheHitBytes e TotalCacheMissBytes.

### <a name="performance-counters"></a>Contadores de desempenho

Use os contadores de desempenho da Sincronização de Arquivos do Azure no servidor para monitorar a atividade de sincronização.

Para exibir Sincronização de Arquivos do Azure contadores de desempenho no servidor, abra o monitor de desempenho (Perfmon.exe). Você pode encontrar os contadores nos objetos de **operações de sincronização** de **AFS bytes transferidos** e AFS.

Os seguintes contadores de desempenho para a Sincronização de Arquivos do Azure estão disponíveis no Monitor de Desempenho:

| Objeto de Desempenho\Nome do Contador | Descrição |
|-|-|
| Bytes de AFS Transferidos\Bytes Baixados/s | Número de bytes baixados por segundo. |
| Bytes de AFS Transferidos\Bytes Carregados/s | Número de bytes carregados por segundo. |
| Bytes de AFS Transferidos\Total de Bytes/s | Total de bytes por segundo (upload e download). |
| Operações de Sincronização de AFS\Arquivos de Sincronização Baixados/s | Número de arquivos baixados por segundo. |
| Operações de Sincronização de AFS\Arquivos de Sincronização Carregados/s | Número de arquivos carregados por segundo. |
| Operações de Sincronização de AFS\Total de Operações de Arquivo de Sincronização/s | Número total de arquivos sincronizados (upload e download). |

## <a name="alert-examples"></a>Exemplos de alertas
Esta seção fornece alguns alertas de exemplo para Sincronização de Arquivos do Azure.

  > [!Note]  
  > Se você criar um alerta e ele estiver muito ruidosa, ajuste o valor de limite e a lógica de alerta.

### <a name="how-to-create-an-alert-if-the-server-endpoint-health-shows-an-error-in-the-portal"></a>Como criar um alerta se a integridade do ponto de extremidade do servidor mostrar um erro no portal

1. No **portal do Azure**, navegue até o respectivo **serviço de sincronização de armazenamento**. 
2. Vá para a seção **monitoramento** e clique em **alertas**. 
3. Clique em **+ nova regra de alerta** para criar uma nova regra de alerta. 
4. Configure a condição clicando em **Selecionar condição**.
5. Na folha **Configurar lógica de sinal** , clique em **sincronizar sessão resultado** em nome do sinal.  
6. Selecione a seguinte configuração de dimensão: 
     - Nome da dimensão: **nome do ponto de extremidade do servidor**  
     - Operador **=** 
     - Valores de dimensão: **todos os valores atuais e futuros**  
7. Navegue até a **lógica de alerta** e conclua o seguinte: 
     - Limite definido como **estático** 
     - Operador: **menor que** 
     - Tipo de agregação: **máximo**  
     - Valor do limite: **1** 
     - Avaliado com base em: granularidade de agregação = **24 horas** | Frequência de avaliação = a **cada hora** 
     - Clique em **concluído.** 
8. Clique em **selecionar grupo de ações** para adicionar um grupo de ações (email, SMS, etc.) ao alerta, seja selecionando um grupo de ações existente ou criando um novo grupo de ação.
9. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, **Descrição** e **severidade**.
10. Clique em **Criar regra de alerta**. 

### <a name="how-to-create-an-alert-if-files-are-failing-to-sync-to-a-server-or-cloud-endpoint"></a>Como criar um alerta se os arquivos estiverem falhando na sincronização com um servidor ou ponto de extremidade de nuvem

1. No **portal do Azure**, navegue até o respectivo **serviço de sincronização de armazenamento**. 
2. Vá para a seção **monitoramento** e clique em **alertas**. 
3. Clique em **+ nova regra de alerta** para criar uma nova regra de alerta. 
4. Configure a condição clicando em **Selecionar condição**.
5. Na folha **Configurar lógica de sinal** , clique em **arquivos não sincronizando** sob o nome do sinal.  
6. Selecione a seguinte configuração de dimensão: 
     - Nome da dimensão: **nome do ponto de extremidade do servidor**  
     - Operador **=** 
     - Valores de dimensão: **todos os valores atuais e futuros**  
7. Navegue até a **lógica de alerta** e conclua o seguinte: 
     - Limite definido como **estático** 
     - Operador: **maior que** 
     - Tipo de agregação: **média**  
     - Valor do limite: **100** 
     - Avaliado com base em: granularidade de agregação = **5 minutos** | Frequência de avaliação = a **cada 5 minutos** 
     - Clique em **concluído.** 
8. Clique em **selecionar grupo de ações** para adicionar um grupo de ações (email, SMS, etc.) ao alerta, seja selecionando um grupo de ações existente ou criando um novo grupo de ação.
9. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, **Descrição** e **severidade**.
10. Clique em **Criar regra de alerta**. 

### <a name="how-to-create-an-alert-if-a-registered-server-is-failing-to-communicate-with-the-storage-sync-service"></a>Como criar um alerta se um servidor registrado estiver falhando ao se comunicar com o serviço de sincronização de armazenamento

1. No **portal do Azure**, navegue até o respectivo **serviço de sincronização de armazenamento**. 
2. Vá para a seção **monitoramento** e clique em **alertas**. 
3. Clique em **+ nova regra de alerta** para criar uma nova regra de alerta. 
4. Configure a condição clicando em **Selecionar condição**.
5. Na folha **Configurar lógica de sinal** , clique em **status do servidor online** em nome do sinal.  
6. Selecione a seguinte configuração de dimensão: 
     - Nome da dimensão: **nome do servidor**  
     - Operador **=** 
     - Valores de dimensão: **todos os valores atuais e futuros**  
7. Navegue até a **lógica de alerta** e conclua o seguinte: 
     - Limite definido como **estático** 
     - Operador: **menor que** 
     - Tipo de agregação: **máximo**  
     - Valor do limite (em bytes): **1** 
     - Avaliado com base em: granularidade de agregação = **1 hora** | Frequência de avaliação = a **cada 30 minutos** 
         - Observe que as métricas são enviadas para Azure Monitor a cada 15 a 20 minutos. Não defina a **frequência de avaliação** para menos de 30 minutos (irá gerar falsos alertas).
     - Clique em **concluído.** 
8. Clique em **selecionar grupo de ações** para adicionar um grupo de ações (email, SMS, etc.) ao alerta, seja selecionando um grupo de ações existente ou criando um novo grupo de ação.
9. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, **Descrição** e **severidade**.
10. Clique em **Criar regra de alerta**. 

### <a name="how-to-create-an-alert-if-the-cloud-tiering-recall-size-has-exceeded-500gib-in-a-day"></a>Como criar um alerta se o tamanho de recall em camadas de nuvem tiver excedido 500GiB em um dia

1. No **portal do Azure**, navegue até o respectivo **serviço de sincronização de armazenamento**. 
2. Vá para a seção **monitoramento** e clique em **alertas**. 
3. Clique em **+ nova regra de alerta** para criar uma nova regra de alerta. 
4. Configure a condição clicando em **Selecionar condição**.
5. Na folha **Configurar lógica de sinal** , clique em **camada de recall de camadas de nuvem** em nome do sinal.  
6. Selecione a seguinte configuração de dimensão: 
     - Nome da dimensão: **nome do servidor**  
     - Operador **=** 
     - Valores de dimensão: **todos os valores atuais e futuros**  
7. Navegue até a **lógica de alerta** e conclua o seguinte: 
     - Limite definido como **estático** 
     - Operador: **maior que** 
     - Tipo de agregação: **total**  
     - Valor do limite (em bytes): **67108864000** 
     - Avaliado com base em: granularidade de agregação = **24 horas** | Frequência de avaliação = a **cada hora** 
     - Clique em **concluído.** 
8. Clique em **selecionar grupo de ações** para adicionar um grupo de ações (email, SMS, etc.) ao alerta, seja selecionando um grupo de ações existente ou criando um novo grupo de ação.
9. Preencha os **detalhes do alerta** , como nome da **regra de alerta**, **Descrição** e **severidade**.
10. Clique em **Criar regra de alerta**. 

## <a name="next-steps"></a>Próximas etapas
- [Planejando uma implantação da Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
- [Considere as configurações de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
- [Implantar a Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
- [Solucionar problemas da Sincronização de Arquivos do Azure](storage-sync-files-troubleshoot.md)
- [Perguntas frequentes da Arquivos do Azure](storage-files-faq.md)
