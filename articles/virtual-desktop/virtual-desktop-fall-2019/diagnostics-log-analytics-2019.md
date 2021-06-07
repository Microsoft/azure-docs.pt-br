---
title: Análise de logs de diagnóstico da Área de Trabalho Virtual do Windows (clássica) – Azure
description: Como usar o Log Analytics com o recurso de diagnóstico da Área de Trabalho Virtual do Windows (clássica).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9cfa50e13756692295c84b02d02dd71228b16eb9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444988"
---
# <a name="use-log-analytics-for-the-diagnostics-feature-in-windows-virtual-desktop-classic"></a>Usar o Log Analytics para o recurso de diagnóstico na Área de Trabalho Virtual do Windows (clássica)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager, confira [este artigo](../diagnostics-log-analytics.md).

A Área de Trabalho Virtual do Windows oferece um recurso de diagnóstico que permite ao administrador identificar problemas por meio de uma única interface. Esse recurso registra as informações de diagnóstico sempre que alguém que recebeu a função de Área de Trabalho Virtual do Windows usa o serviço. Cada log contém informações sobre qual função da Área de Trabalho Virtual do Windows estava envolvida na atividade, as mensagens de erro que aparecem durante a sessão, informações de locatário e informações do usuário. O recurso de diagnóstico cria logs de atividade para ações administrativas e de usuário. Cada log de atividades se enquadra em três categorias principais:

- Atividades de assinatura de feed: quando um usuário tenta se conectar ao feed por meio de aplicativos da Área de Trabalho Remota da Microsoft.
- Atividades de conexão: quando um usuário tenta se conectar a uma área de trabalho ou ao RemoteApp por meio de aplicativos da Área de Trabalho Remota da Microsoft.
- Atividades de gerenciamento: quando um administrador realiza operações de gerenciamento no sistema, como a criação de pools de hosts, a atribuição de usuários a grupos de aplicativos e a criação de atribuições de função.

As conexões que não acessam a Área de Trabalho Virtual do Windows não são exibidas nos resultados do diagnóstico porque o próprio serviço da função de diagnóstico faz parte da Área de Trabalho Virtual do Windows. Problemas de conexão da Área de Trabalho Virtual do Windows podem ocorrer quando o usuário final enfrenta problemas de conectividade de rede.

## <a name="why-you-should-use-log-analytics"></a>Por que você deve usar o Log Analytics

Recomendamos que você use o Log Analytics para analisar dados de diagnóstico no cliente do Azure que vão além da solução de problemas de usuário único. Como você pode incluir contadores de desempenho de VM no Log Analytics, você tem uma ferramenta para coletar informações para sua implantação.

## <a name="before-you-get-started"></a>Antes de começar

Para começar a usar o Log Analytics com o recurso de diagnóstico, [crie um workspace](../../azure-monitor/vm/quick-collect-windows-computer.md#create-a-workspace).

Depois de criar seu workspace, siga as instruções em [Conectar computadores Windows ao Azure Monitor](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key) para obter as seguintes informações:

- A ID do workspace
- A chave primária do seu workspace

Você precisará dessas informações posteriormente no processo de instalação.

## <a name="push-diagnostics-data-to-your-workspace"></a>Enviar dados de diagnóstico por push para seu workspace

Você pode enviar por push dados de diagnóstico do seu locatário da Área de Trabalho Virtual do Windows para o Log Analytics do seu workspace. Você pode configurar esse recurso imediatamente quando cria seu locatário pela primeira vez vinculando seu workspace ao seu locatário ou pode configurá-lo posteriormente com um locatário existente.

Para vincular seu locatário ao seu workspace do Log Analytics enquanto estiver configurando seu novo locatário, execute o seguinte cmdlet para entrar na Área de Trabalho Virtual do Windows com sua conta de usuário do TenantCreator:

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

Se você for vincular um locatário existente, em vez de um novo locatário, execute este cmdlet:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Você precisará executar esses cmdlets para todos os locatários que deseja vincular ao Log Analytics.

>[!NOTE]
>Se você não quiser vincular o workspace do Log Analytics ao criar um locatário, execute o cmdlet `New-RdsTenant`.

## <a name="cadence-for-sending-diagnostic-events"></a>Frequência de envio de eventos de diagnóstico

Os eventos de diagnóstico são enviados para o Log Analytics quando concluídos.

## <a name="example-queries"></a>Consultas de exemplo

As seguintes consultas de exemplo mostram como o recurso de diagnóstico gera um relatório para as atividades mais frequentes em seu sistema:

Este primeiro exemplo mostra as atividades de conexão iniciadas pelos usuários com clientes da área de trabalho remota com suporte:

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

Esta consulta de exemplo mostra as atividades de gerenciamento por administradores em locatários:

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

## <a name="stop-sending-data-to-log-analytics"></a>Interromper o envio de dados para o Log Analytics

Para interromper o envio de dados de um locatário existente para o Log Analytics, execute o seguinte cmdlet e defina cadeias de caracteres vazias:

```powershell
Set-RdsTenant -Name <TenantName> -AzureSubscriptionId <SubscriptionID> -LogAnalyticsWorkspaceId <String> -LogAnalyticsPrimaryKey <String>
```

Você precisará executar esse cmdlet para todos os locatários dos quais deseja interromper o envio de dados.

## <a name="next-steps"></a>Próximas etapas

Para examinar os cenários de erro comuns que o recurso de diagnóstico pode identificar para você, confira [Identificar e diagnosticar problemas](diagnostics-role-service-2019.md#common-error-scenarios).
