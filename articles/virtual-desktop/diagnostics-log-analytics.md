---
title: Log Analytics de diagnóstico de área de trabalho virtual do Windows – Azure
description: Como usar o log Analytics com o recurso de diagnóstico de área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5d8b696b175c4ef841eef1a51f1d357d1781cba7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018283"
---
# <a name="use-log-analytics-for-the-diagnostics-feature"></a>Usar Log Analytics para o recurso de diagnóstico

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica) sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/diagnostics-log-analytics-2019.md).

A área de trabalho virtual do Windows usa [Azure monitor](../azure-monitor/overview.md) para monitoramento e alertas como muitos outros serviços do Azure. Isso permite que os administradores identifiquem problemas por meio de uma interface. O serviço cria logs de atividade para ações administrativas e de usuário. Cada log de atividades se enquadra nas seguintes categorias:

- Atividades de gerenciamento:
    - Acompanhe se as tentativas de alterar objetos da área de trabalho virtual do Windows usando APIs ou PowerShell foram bem-sucedidas. Por exemplo, alguém pode criar um pool de hosts com êxito usando o PowerShell?
- Feed
    - Os usuários podem assinar com êxito os espaços de trabalho?
    - Os usuários veem todos os recursos publicados no cliente Área de Trabalho Remota?
- Conexões:
    - Quando os usuários iniciam e concluem conexões com o serviço.
- Registro do host:
    - O host da sessão foi registrado com êxito com o serviço na conexão?
- Errões:
    - Os usuários estão encontrando problemas com atividades específicas? Esse recurso pode gerar uma tabela que acompanha os dados da atividade, desde que as informações sejam unidas com as atividades.
- Pontos
    - Etapas específicas no tempo de vida de uma atividade que foram atingidas. Por exemplo, durante uma sessão, um usuário tinha um balanceamento de carga para um host específico, o usuário estava conectado durante uma conexão e assim por diante.

As conexões que não acessam a Área de Trabalho Virtual do Windows não são exibidas nos resultados do diagnóstico porque o próprio serviço da função de diagnóstico faz parte da Área de Trabalho Virtual do Windows. Problemas de conexão de área de trabalho virtual do Windows podem ocorrer quando o usuário está enfrentando problemas de conectividade de rede.

Azure Monitor permite analisar os dados da área de trabalho virtual do Windows e examinar os contadores de desempenho de máquina virtual (VM), tudo na mesma ferramenta. Este artigo informará mais sobre como habilitar o diagnóstico para seu ambiente de área de trabalho virtual do Windows.

>[!NOTE]
>Para saber como monitorar suas VMs no Azure, consulte [monitorando máquinas virtuais do Azure com Azure monitor](../azure-monitor/insights/monitor-vm-azure.md). Além disso, lembre-se de [examinar os limites do contador de desempenho](../virtual-desktop/virtual-desktop-fall-2019/deploy-diagnostics.md#windows-performance-counter-thresholds) para uma melhor compreensão da experiência do usuário no host da sessão.

## <a name="before-you-get-started"></a>Antes de começar

Antes de poder usar Log Analytics, você precisará criar um espaço de trabalho. Para fazer isso, siga as instruções em um dos dois artigos a seguir:

- Se preferir usar portal do Azure, consulte [criar um espaço de trabalho log Analytics no portal do Azure](../azure-monitor/learn/quick-create-workspace.md).
- Se preferir o PowerShell, consulte [criar um log Analytics espaço de trabalho com o PowerShell](../azure-monitor/platform/powershell-workspace-configuration.md).

Depois de criar seu espaço de trabalho, siga as instruções em [conectar computadores Windows a Azure monitor](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key) para obter as seguintes informações:

- A ID do espaço de trabalho
- A chave primária do seu espaço de trabalho

Você precisará dessas informações posteriormente no processo de instalação.

Certifique-se de examinar o gerenciamento de permissões para Azure Monitor habilitar o acesso a dados para aqueles que monitoram e mantêm seu ambiente de área de trabalho virtual do Windows. Para obter mais informações, consulte Introdução [às funções, permissões e segurança com Azure monitor](../azure-monitor/platform/roles-permissions-security.md).

## <a name="push-diagnostics-data-to-your-workspace"></a>Enviar dados de diagnóstico por push para seu espaço de trabalho

Você pode enviar por push dados de diagnóstico de seus objetos da área de trabalho virtual do Windows para o Log Analytics para seu espaço de trabalho. Você pode configurar esse recurso imediatamente ao criar seus objetos pela primeira vez.

Para configurar Log Analytics para um novo objeto:

1. Entre no portal do Azure e vá para a **área de trabalho virtual do Windows**.

2. Navegue até o objeto (como um pool de hosts, grupo de aplicativos ou espaço de trabalho) para o qual você deseja capturar logs e eventos.

3. Selecione **configurações de diagnóstico** no menu no lado esquerdo da tela.

4. Selecione **Adicionar configuração de diagnóstico** no menu que aparece no lado direito da tela.

    As opções mostradas na página Configurações de diagnóstico variam de acordo com o tipo de objeto que você está editando.

    Por exemplo, quando você estiver habilitando o diagnóstico para um grupo de aplicativos, verá opções para configurar pontos de verificação, erros e gerenciamento. Para espaços de trabalho, essas categorias configuram um feed para controlar quando os usuários assinam a lista de aplicativos. Para saber mais sobre as configurações de diagnóstico [, consulte Criar configuração de diagnóstico para coletar logs de recursos e métricas no Azure](../azure-monitor/platform/diagnostic-settings.md).

     >[!IMPORTANT]
     >Lembre-se de habilitar o diagnóstico para cada objeto de Azure Resource Manager que você deseja monitorar. Os dados estarão disponíveis para atividades após a habilitação do diagnóstico. Pode levar algumas horas após a primeira configuração.

5. Insira um nome para a configuração de configurações e, em seguida, selecione **Enviar para log Analytics**. O nome que você usa não deve ter espaços e deve estar em conformidade com as [convenções de nomenclatura do Azure](../azure-resource-manager/management/resource-name-rules.md). Como parte dos logs, você pode selecionar todas as opções que deseja adicionar à sua Log Analytics, como ponto de verificação, erro, gerenciamento e assim por diante.

6. Selecione **Salvar**.

>[!NOTE]
>Log Analytics oferece a opção de transmitir dados para os [hubs de eventos](../event-hubs/event-hubs-about.md) ou arquivá-los em uma conta de armazenamento. Para saber mais sobre esse recurso, confira [transmitir dados de monitoramento do Azure para um hub de eventos](../azure-monitor/platform/stream-monitoring-data-event-hubs.md) e [arquivar logs de recursos do Azure para a conta de armazenamento](../azure-monitor/platform/resource-logs.md#send-to-azure-storage).

## <a name="how-to-access-log-analytics"></a>Como acessar Log Analytics

Você pode acessar espaços de trabalho do Log Analytics no portal do Azure ou Azure Monitor.

### <a name="access-log-analytics-on-a-log-analytics-workspace"></a>Log Analytics de acesso em um espaço de trabalho Log Analytics

1. Entre no portal do Azure.

2. Pesquise **log Analytics espaço de trabalho**.

3. Em serviços, selecione **log Analytics espaços de trabalho**.

4. Na lista, selecione o espaço de trabalho que você configurou para o seu objeto de área de trabalho virtual do Windows.

5. Uma vez no espaço de trabalho, selecione **logs**. Você pode filtrar a lista de menus com a função de **pesquisa** .

### <a name="access-log-analytics-on-azure-monitor"></a>Log Analytics de acesso no Azure Monitor

1. Entrar no portal do Azure

2. Pesquise e selecione **Monitor**.

3. Selecione **Logs**.

4. Siga as instruções na página de registro para definir o escopo da consulta.

5. Você está pronto para consultar o diagnóstico. Todas as tabelas de diagnóstico têm um prefixo "WVD".

>[!NOTE]
>Para obter informações mais detalhadas sobre as tabelas armazenadas em logs de Azure Monitor, consulte a [Azure monitor a reisolamento de dados](/azure/azure-monitor/reference/). Todas as tabelas relacionadas à área de trabalho virtual do Windows são rotuladas como "WVD".

## <a name="cadence-for-sending-diagnostic-events"></a>Cadência para enviar eventos de diagnóstico

Os eventos de diagnóstico são enviados para Log Analytics quando concluídos.

Log Analytics apenas os relatórios nesses Estados intermediários para atividades de conexão:

- Iniciado: quando um usuário seleciona e se conecta a um aplicativo ou área de trabalho no cliente Área de Trabalho Remota.
- Conectado: quando o usuário se conecta com êxito à VM onde o aplicativo ou a área de trabalho está hospedada.
- Concluído: quando o usuário ou servidor desconecta a sessão, a atividade ocorreu.

## <a name="example-queries"></a>Consultas de exemplo

Acessar consultas de exemplo por meio da interface do usuário do Azure Monitor Log Analytics:
1. Vá para o espaço de trabalho do Log Analytics e, em seguida, selecione **logs**. A interface do usuário de consulta de exemplo é mostrada automaticamente.
1. Altere o filtro para **categoria**.
1. Selecione **área de trabalho virtual do Windows** para revisar as consultas disponíveis.
1. Selecione **executar** para executar a consulta selecionada.

Saiba mais sobre a interface de consulta de exemplo em [consultas salvas no Azure Monitor log Analytics](../azure-monitor/log-query/example-queries.md).

A lista de consulta a seguir permite que você revise as informações de conexão ou problemas para um único usuário. Você pode executar essas consultas no [Editor de consulta log Analytics](../azure-monitor/log-query/log-analytics-tutorial.md#write-a-query). Para cada consulta, substitua `userupn` pelo UPN do usuário que você deseja pesquisar.


Para localizar todas as conexões de um único usuário:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
```


Para localizar o número de vezes que um usuário se conectou por dia:

```kusto
WVDConnections
|where UserName == "userupn"
|take 100
|sort by TimeGenerated asc, CorrelationId
|summarize dcount(CorrelationId) by bin(TimeGenerated, 1d)
```

Para localizar a duração da sessão por usuário:

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId)
on CorrelationId
| project Duration = EndTime - StartTime, ResourceAlias
| sort by Duration asc
```

Para localizar erros para um usuário específico:

```kusto
WVDErrors
| where UserName == "userupn"
|take 100
```

Para descobrir se ocorreu um erro específico para outros usuários:

```kusto
WVDErrors
| where CodeSymbolic =="ErrorSymbolicCode"
| summarize count(UserName) by CodeSymbolic
```


>[!NOTE]
>- Quando um usuário abre a área de trabalho completa, o uso do aplicativo na sessão não é rastreado como pontos de verificação na tabela WVDCheckpoints.
>- A coluna ResourcesAlias na tabela WVDConnections mostra se um usuário se conectou a uma área de trabalho completa ou a um aplicativo publicado. A coluna mostra apenas o primeiro aplicativo aberto durante a conexão. Todos os aplicativos publicados que o usuário abre são acompanhados em WVDCheckpoints.
>- A tabela WVDErrors mostra erros de gerenciamento, problemas de registro de host e outros problemas que ocorrem enquanto o usuário assina uma lista de aplicativos ou áreas de trabalho.
>- O WVDErrors ajuda a identificar problemas que podem ser resolvidos por tarefas de administrador. O valor em não erro sempre diz "false" para esses tipos de problemas. Se o erro = "verdadeiro", você precisará escalar o problema para a Microsoft. Certifique-se de fornecer a CorrelationId para os erros que você escalonar.

## <a name="next-steps"></a>Próximas etapas

Para examinar os cenários de erro comuns que o recurso de diagnóstico pode identificar para você, consulte [identificar e diagnosticar problemas](diagnostics-role-service.md#common-error-scenarios).