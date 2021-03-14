---
title: Solucionar problemas com o agente do Log Analytics para Windows
description: Descreva os sintomas, as causas e a resolução dos problemas mais comuns com o agente de Log Analytics para Windows no Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 4e2531d511193586ef4605cc3732968b6db28d9f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100604952"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-windows"></a>Como solucionar problemas com o agente do Log Analytics para Windows 

Este artigo fornece ajuda para solucionar erros que podem ocorrer com o Log Analytics Agent para Windows no Azure Monitor e sugere possíveis soluções para resolvê-los.

Se nenhuma dessas etapas funcionar para você, os seguintes canais de suporte também estarão disponíveis:

* Os clientes com benefícios de suporte Premier podem abrir uma solicitação de suporte com [Premier](https://premier.microsoft.com/).
* Os clientes com contratos de suporte do Azure podem abrir uma solicitação de suporte [no portal do Azure](https://manage.windowsazure.com/?getsupport=true).
* Visite a página de comentários Log Analytics para revisar ideias e bugs enviados [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) ou um novo arquivo. 

## <a name="log-analytics-troubleshooting-tool"></a>Ferramenta de solução de problemas Log Analytics

O agente do Log Analytics ferramenta de solução de problemas do Windows é uma coleção de scripts do PowerShell projetadas para ajudar a localizar e diagnosticar problemas com o agente de Log Analytics. Ele é incluído automaticamente com o agente na instalação. A execução da ferramenta deve ser a primeira etapa no diagnóstico de um problema.

### <a name="how-to-use"></a>Como usar
1. Abra o prompt do PowerShell como administrador no computador em que o agente do Log Analytics está instalado.
1. Navegue até o diretório onde a ferramenta está localizada.
   * `cd "C:\Program Files\Microsoft Monitoring Agent\Agent\Troubleshooter"`
1. Execute o script principal usando este comando:
   * `.\GetAgentInfo.ps1`
1. Selecione um cenário de solução de problemas.
1. Siga as instruções no console do. (Observação: as etapas de logs de rastreamento exigem intervenção manual para parar a coleta de log. Com base na reprodução do problema, aguarde o tempo de duração e pressione ' s ' para parar a coleta de logs e prosseguir para a próxima etapa).

   Os locais do arquivo de resultados são registrados após a conclusão e uma nova janela do Explorer realçando-o é aberto.

### <a name="installation"></a>Instalação
A ferramenta de solução de problemas é incluída automaticamente na instalação do agente de Log Analytics Build 10.20.18053.0 e em diante.

### <a name="scenarios-covered"></a>Cenários cobertos
Abaixo está uma lista de cenários verificados pela ferramenta de solução de problemas:

- Agente não relatando dados ou dados de pulsação ausentes
- Falha na implantação da extensão do agente
- Falha do agente
- Agente consumindo alta CPU/memória
- Falhas de instalação/desinstalação
- Problema de logs personalizados
- Problema do gateway do OMS
- Problema de contadores de desempenho
- Coletar todos os logs

>[!NOTE]
>Execute a ferramenta de solução de problemas quando enfrentar um problema. Ao abrir um tíquete, ter os logs inicialmente ajudará muito a nossa equipe de suporte a solucionar o problema mais rapidamente.

## <a name="important-troubleshooting-sources"></a>Fontes de solução de problemas importantes

 Para auxiliar na solução de problemas relacionados ao agente do Log Analytics para Windows, o agente registra eventos no log de eventos do Windows, especificamente em *Application and Services\Operations Manager*.  

## <a name="connectivity-issues"></a>Problemas de conectividade

Se o agente estiver se comunicando por meio de um servidor proxy ou firewall, pode haver restrições no local, impedindo a comunicação do computador de origem e do serviço de Azure Monitor. Caso a comunicação seja bloqueada, devido à configuração incorreta, o registro com um espaço de trabalho pode falhar ao tentar instalar o agente ou configurar o agente post-setup para relatar para um espaço de trabalho adicional. A comunicação do agente pode falhar após o registro bem-sucedido. Esta seção descreve os métodos para solucionar esse tipo de problema com o agente do Windows.

Verifique se o firewall ou o proxy está configurado para permitir as seguintes portas e URLs descritas na tabela a seguir. Também confirme se a inspeção HTTP não está habilitada para o tráfego da Web, pois ela pode impedir um canal TLS seguro entre o agente e o Azure Monitor.  

|Recurso de agente|Portas |Direção |Ignorar a inspeção de HTTPS|
|------|---------|--------|--------|   
|*.ods.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.oms.opinsights.azure.com |Porta 443 |Saída|Sim |  
|*.blob.core.windows.net |Porta 443 |Saída|Sim |  
|*. agentsvc.azure-automation.net |Porta 443 |Saída|Sim |  

Para obter informações de firewall necessárias para o Azure Governamental, confira [Gerenciamento do Azure Governamental](../../azure-government/compare-azure-government-global-azure.md#azure-monitor). Se você planeja usar o Hybrid Runbook Worker da Automação do Azure para conectar e se registrar no serviço de automação para usar runbooks ou soluções de gerenciamento em seu ambiente, é necessário ter acesso ao número da porta e as URLs descritas em [Configurar sua rede para o Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md#network-planning). 

Há várias maneiras de verificar se o agente está se comunicando com êxito com Azure Monitor.

- Habilite a [avaliação de integridade do agente de log Analytics do Azure](../insights/solution-agenthealth.md) no espaço de trabalho. No painel Integridade do Agente, exiba a coluna **contagem de agentes sem resposta** para ver rapidamente se o agente está listado.  

- Execute a consulta a seguir para confirmar se o agente está enviando uma pulsação para o espaço de trabalho para o qual ele está configurado para relatar. Substituir `<ComputerName>` pelo nome real da máquina.

    ```
    Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer 
    ```

    Se o computador estiver se comunicando com êxito com o serviço, a consulta deverá retornar um resultado. Se a consulta não retornou um resultado, primeiro verifique se o agente está configurado para relatar para o espaço de trabalho correto. Se estiver configurado corretamente, vá para a etapa 3 e pesquise o log de eventos do Windows para identificar se o agente está registrando em log Qual problema pode estar impedindo a comunicação com o Azure Monitor.

- Outro método para identificar um problema de conectividade é executar a ferramenta **TestCloudConnectivity** . A ferramenta é instalada por padrão com o agente na pasta *%systemroot%\Program Comuns\microsoft Monitoring Agent\Agent*. Em um prompt de comando com privilégios elevados, navegue até a pasta e execute a ferramenta. A ferramenta retorna os resultados e os destaques em que o teste falhou (por exemplo, se ele estava relacionado a uma porta/URL específica que foi bloqueada). 

    ![Resultados da execução da ferramenta TestCloudConnection](./media/agent-windows-troubleshoot/output-testcloudconnection-tool-01.png)

- Filtre o log de eventos *Operations Manager* por **fontes de evento**  -  *serviço de integridade módulos*, *HealthService* e *conector de serviço* e filtre por *aviso* de **nível de evento** e *erro* para confirmar se há eventos gravados da tabela a seguir. Se estiverem, examine as etapas de resolução incluídas para cada evento possível.

    |ID do evento |Fonte |Descrição |Resolução |
    |---------|-------|------------|-----------|
    |2133 & 2129 |Serviço de Integridade |Falha na conexão com o serviço do agente |Esse erro pode ocorrer quando o agente não pode se comunicar diretamente ou por meio de um servidor de firewall/proxy para o serviço de Azure Monitor. Verifique as configurações de proxy do agente ou se o firewall de rede/proxy permite o tráfego TCP do computador para o serviço.|
    |2138 |Módulos do Serviço de Integridade |Proxy exige autenticação |Defina as configurações de proxy do agente e especifique o nome de usuário/senha necessários para autenticar com o servidor proxy. |
    |2129 |Módulos do Serviço de Integridade |Falha na conexão/falha na negociação de TLS |Verifique as configurações de TCP/IP do adaptador de rede e as configurações de proxy do agente.|
    |2127 |Módulos do Serviço de Integridade |Falha ao enviar dados-código de erro recebido |Se isso ocorrer apenas periodicamente durante o dia, poderia ser uma anomalia aleatória que pode ser ignorada. Monitor para entender com que frequência isso acontece. Se ocorrer muitas vezes ao longo do dia, verifique primeiro as configurações de rede e de proxy. Se a descrição incluir o código de erro HTTP 404 e for a primeira vez que o agente tentar enviar dados para o serviço, ele incluirá um erro 500 com um código de erro de 404 interno. 404 significa não encontrado, que indica que a área de armazenamento do novo espaço de trabalho ainda está sendo provisionada. Na próxima tentativa, os dados serão gravados com êxito no espaço de trabalho conforme o esperado. Um erro HTTP 403 pode indicar uma permissão ou um problema de credenciais. Há mais informações incluídas com o erro 403 para ajudar a solucionar o problema.|
    |4000 |Conector de serviço |Falha na resolução do nome DNS |O computador não pôde resolver o endereço da Internet usado ao enviar dados para o serviço. Isso pode ser as configurações do resolvedor de DNS em seu computador, configurações de proxy incorretas ou talvez um problema de DNS temporário com seu provedor. Se ocorrer periodicamente, isso pode ser causado por um problema transitório relacionado à rede.|
    |4001 |Conector de serviço |Falha na conexão com o serviço. |Esse erro pode ocorrer quando o agente não pode se comunicar diretamente ou por meio de um servidor de firewall/proxy para o serviço de Azure Monitor. Verifique as configurações de proxy do agente ou se o firewall de rede/proxy permite o tráfego TCP do computador para o serviço.|
    |4002 |Conector de serviço |O serviço retornou o código de status HTTP 403 em resposta a uma consulta. Verifique com o administrador de serviços a integridade do serviço. A consulta será repetida mais tarde. |Esse erro é gravado durante a fase de registro inicial do agente e você verá uma URL semelhante à seguinte: *https:// \<workspaceID> . OMS.opinsights.Azure.com/AgentService.svc/AgentTopologyRequest*. Um código de erro 403 significa proibido e pode ser causado por uma ID ou chave de espaço de trabalho digitada incorretamente, ou os dados e a hora estão incorretos no computador. Se a hora for +/-15 minutos da hora atual, a integração falhará. Para corrigir isso, atualize a data e/ou o fuso horário do computador com Windows.|

## <a name="data-collection-issues"></a>Problemas de coleta de dados

Depois que o agente é instalado e é reportado para seu espaço de trabalho ou espaços de trabalho configurados, ele pode parar de receber a configuração, coletar ou encaminhar o desempenho, os logs ou outros dados para o serviço, dependendo do que está habilitado e direcionando o computador. É necessário determinar se:

- É um tipo de dados específico ou todos os dados que não estão disponíveis no espaço de trabalho?
- O tipo de dados é especificado por uma solução ou é especificado como parte da configuração da coleta de dados do espaço de trabalho?
- Quantos computadores são afetados? É um único ou vários computadores que se reportam ao espaço de trabalho?
- Ele estava funcionando e parou em uma hora específica do dia, ou ele nunca foi coletado? 
- A consulta de pesquisa de log está sendo usada sintaticamente correta? 
- O agente já recebeu sua configuração de Azure Monitor?

A primeira etapa na solução de problemas é determinar se o computador está enviando um evento de pulsação.

```
Heartbeat 
    | where Computer like "<ComputerName>"
    | summarize arg_max(TimeGenerated, * ) by Computer
```

Se a consulta retornar resultados, você precisará determinar se um determinado tipo de dados não é coletado e encaminhado para o serviço. Isso pode ser causado pelo agente não recebendo a configuração atualizada do serviço ou por algum outro sintoma impedindo que o agente opere normalmente. Execute as etapas a seguir para solucionar problemas adicionais.

1. Abra um prompt de comando com privilégios elevados no computador e reinicie o serviço do Agent digitando `net stop healthservice && net start healthservice` .
2. Abra o log de eventos *Operations Manager* e procure por **IDs de evento** *7023, 7024, 7025, 7028* e *1210* na **origem do evento** *HealthService*.  Esses eventos indicam que o agente está recebendo a configuração com êxito do Azure Monitor e está monitorando o computador ativamente. A descrição do evento para a ID de evento 1210 também especificará na última linha todas as soluções e informações incluídas no escopo do monitoramento no agente.  

    ![Descrição do evento ID 1210](./media/agent-windows-troubleshoot/event-id-1210-healthservice-01.png)

3. Se, após vários minutos, você não vir os dados esperados nos resultados da consulta ou na visualização, dependendo de se você estiver exibindo os dados de uma solução ou insight, no log de eventos *Operations Manager* , procure por **fontes de eventos** *HealthService* e *serviço de integridade módulos* e filtre por *aviso* de **nível de evento** e *erro* para confirmar se há eventos gravados da tabela a seguir.

    |ID do evento |Fonte |Descrição |Resolução |
    |---------|-------|------------|
    |8000 |HealthService |Esse evento especificará se um fluxo de trabalho relacionado a desempenho, evento ou outro tipo de dados coletado não puder encaminhar para o serviço para ingestão no espaço de trabalho. | A ID de evento 2136 da fonte HealthService é escrita junto com esse evento e pode indicar que o agente não pode se comunicar com o serviço, possivelmente devido a uma configuração incorreta do proxy e das configurações de autenticação, interrupção de rede ou o firewall/proxy de rede não permite o tráfego TCP do computador para o serviço.| 
    |10102 e 10103 |Módulos do Serviço de Integridade |O fluxo de trabalho não pôde resolver a fonte de dados. |Isso pode ocorrer se o contador de desempenho ou a instância especificada não existir no computador ou se estiver definido incorretamente nas configurações de dados do espaço de trabalho. Se esse for um [contador de desempenho](data-sources-performance-counters.md#configuring-performance-counters)especificado pelo usuário, verifique se as informações especificadas estão seguindo o formato correto e se existem nos computadores de destino. |
    |26002 |Módulos do Serviço de Integridade |O fluxo de trabalho não pôde resolver a fonte de dados. |Isso pode ocorrer se o log de eventos do Windows especificado não existir no computador. Esse erro pode ser ignorado com segurança se o computador não tiver esse log de eventos registrado; caso contrário, se esse for um [log de eventos](data-sources-windows-events.md#configuring-windows-event-logs)especificado pelo usuário, verifique se as informações especificadas estão corretas. |
