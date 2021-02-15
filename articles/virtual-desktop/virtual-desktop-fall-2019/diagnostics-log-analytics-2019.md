---
title: Log Analytics de diagnóstico da área de trabalho virtual (clássica) do Windows – Azure
description: Como usar o log Analytics com o recurso de diagnóstico da área de trabalho virtual do Windows (clássico).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 257656a29733972672570648d4317b7847fedff1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88997669"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-windows-virtual-desktop-classic"></a>Usar Log Analytics para o recurso de diagnóstico na área de trabalho virtual do Windows (clássico)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../diagnostics-log-analytics.md).

A Área de Trabalho Virtual do Windows oferece um recurso de diagnóstico que permite ao administrador identificar problemas por meio de uma única interface. Esse recurso registra as informações de diagnóstico sempre que alguém tiver atribuído a função de área de trabalho virtual do Windows usando o serviço. Cada log contém informações sobre qual função de área de trabalho virtual do Windows estava envolvida na atividade, todas as mensagens de erro que aparecem durante a sessão, informações de locatário e informações do usuário. O recurso de diagnóstico cria logs de atividade para ações administrativas e de usuário. Cada log de atividades cai em três categorias principais:

- Atividades de assinatura de feed: quando um usuário tenta se conectar ao feed por meio de aplicativos Área de Trabalho Remota da Microsoft.
- Atividades de conexão: quando um usuário tenta se conectar a um desktop ou RemoteApp por meio de aplicativos Área de Trabalho Remota da Microsoft.
- Atividades de gerenciamento: quando um administrador executa operações de gerenciamento no sistema, como a criação de pools de hosts, a atribuição de usuários a grupos de aplicativos e a criação de atribuições de função.

As conexões que não acessam a Área de Trabalho Virtual do Windows não são exibidas nos resultados do diagnóstico porque o próprio serviço da função de diagnóstico faz parte da Área de Trabalho Virtual do Windows. Problemas de conexão de área de trabalho virtual do Windows podem ocorrer quando o usuário está enfrentando problemas de conectividade de rede.

## <a name="why-you-should-use-log-analytics"></a>Por que você deve usar Log Analytics

Recomendamos que você use Log Analytics para analisar dados de diagnóstico no cliente do Azure que vão além da solução de problemas de usuário único. Como você pode efetuar pull em contadores de desempenho de VM em Log Analytics você tem uma ferramenta para coletar informações para sua implantação.

## <a name="before-you-get-started"></a>Antes de começar

Para poder usar Log Analytics com o recurso de diagnóstico, você precisará [criar um espaço de trabalho](../../azure-monitor/learn/quick-collect-windows-computer.md#create-a-workspace).

Depois de criar seu espaço de trabalho, siga as instruções em [conectar computadores Windows a Azure monitor](../../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key) para obter as seguintes informações:

- A ID do espaço de trabalho
- A chave primária do seu espaço de trabalho

Você precisará dessas informações posteriormente no processo de instalação.

## <a name="push-diagnostics-data-to-your-workspace"></a>Enviar dados de diagnóstico por push para seu espaço de trabalho

Você pode enviar por push dados de diagnóstico do seu locatário da área de trabalho virtual do Windows para o Log Analytics do seu espaço de trabalho. Você pode configurar esse recurso imediatamente quando você cria seu locatário pela primeira vez, vinculando seu espaço de trabalho ao seu locatário ou pode configurá-lo posteriormente com um locatário existente.

Para vincular seu locatário ao seu espaço de trabalho do Log Analytics enquanto estiver configurando seu novo locatário, execute o seguinte cmdlet para entrar na área de trabalho virtual do Windows com sua conta de usuário do TenantCreator:

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Se você for vincular um locatário existente, em vez de um novo locatário, execute este cmdlet:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Você precisará executar esses cmdlets para todos os locatários aos quais deseja vincular Log Analytics.

>[!NOTE]
>Se você não quiser vincular o espaço de trabalho Log Analytics ao criar um locatário, execute o `New-RdsTenant` cmdlet.

## <a name="cadence-for-sending-diagnostic-events"></a>Cadência para enviar eventos de diagnóstico

Os eventos de diagnóstico são enviados para Log Analytics quando concluídos.

## <a name="example-queries"></a>Consultas de exemplo

As consultas de exemplo a seguir mostram como o recurso de diagnóstico gera um relatório para as atividades mais frequentes em seu sistema:

Este primeiro exemplo mostra as atividades de conexão iniciadas pelos usuários com clientes de área de trabalho remota com suporte:

```powershell
WVDActivityV1_CL

| where Type_s == "Connection"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

Esta consulta de exemplo a seguir mostra as atividades de gerenciamento por administradores em locatários:

```powershell
WVDActivityV1_CL

| where Type_s == "Management"

| join kind=leftouter (

    WVDErrorV1_CL

    | summarize Errors = makelist(pack('Time', Time_t, 'Code', ErrorCode_s , 'CodeSymbolic', ErrorCodeSymbolic_s, 'Message', ErrorMessage_s, 'ReportedBy', ReportedBy_s , 'Internal', ErrorInternal_s )) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g 

| join  kind=leftouter (

    WVDCheckpointV1_CL

    | summarize Checkpoints = makelist(pack('Time', Time_t, 'ReportedBy', ReportedBy_s, 'Name', Name_s, 'Parameters', Parameters_s) ) by ActivityId_g

    ) on $left.Id_g  == $right.ActivityId_g

|project-away ActivityId_g, ActivityId_g1
```

## <a name="stop-sending-data-to-log-analytics"></a>Parar de enviar dados para Log Analytics

Para interromper o envio de dados de um locatário existente para Log Analytics, execute o seguinte cmdlet e defina cadeias de caracteres vazias:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Você precisará executar esse cmdlet para todos os locatários dos quais deseja interromper o envio de dados.

## <a name="next-steps"></a>Próximas etapas

Para examinar os cenários de erro comuns que o recurso de diagnóstico pode identificar para você, consulte [identificar e diagnosticar problemas](diagnostics-role-service-2019.md#common-error-scenarios).
