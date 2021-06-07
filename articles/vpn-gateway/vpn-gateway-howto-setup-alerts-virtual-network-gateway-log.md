---
title: 'Gateway de VPN do Azure: configurar alertas em eventos de log de recursos de diagnóstico'
description: Saiba como configurar alertas com base em eventos de log de recursos do gateway de VPN do Azure usando Azure Monitor Log Analytics.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: alzam
ms.openlocfilehash: 70cac6ef566ef52409cd9667708c2fc297e046f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656607"
---
# <a name="set-up-alerts-on-resource-log-events-from-vpn-gateway"></a>Configurar alertas em eventos de log de recursos do gateway de VPN

Este artigo ajuda você a configurar alertas com base em eventos de log de recursos do gateway de VPN do Azure usando Azure Monitor Log Analytics. 

Os seguintes logs de recursos estão disponíveis no Azure:

|***Nome** _ | _ *_Descrição_** |
|--- | --- |
|GatewayDiagnosticLog | Contém logs de recursos para eventos de configuração do gateway, alterações principais e eventos de manutenção |
|TunnelDiagnosticLog | Contém eventos de alteração de estado de túnel. Eventos Connect/Disconnect de túnel têm um motivo resumido para a alteração de estado, se aplicável |
|RouteDiagnosticLog | Registra alterações em rotas estáticas e eventos BGP que ocorrem no gateway |
|IKEDiagnosticLog | Registra mensagens de controle de IKE e eventos no gateway |
|P2SDiagnosticLog | Registra mensagens de controle de ponto a site e eventos no gateway. As informações da fonte de conexão são fornecidas somente para conexões IKEv2 e OpenVPN |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Configurar alertas no Portal do Azure

As etapas de exemplo a seguir criam um alerta para um evento de desconexão que envolve um túnel VPN site a site:


1. Na portal do Azure, procure **log Analytics** em **todos os serviços** e selecione **log Analytics espaços de trabalho**.

   ![Seleções para ir para Log Analytics espaços de trabalho](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Criar")

2. Selecione **criar** na página **log Analytics** .

   ![Página Log Analytics com botão criar](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Selecionar")

3. Selecione **criar novo** e preencha os detalhes.

   ![Detalhes para a criação de um espaço de trabalho Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Selecionar")

4. Localize o gateway de VPN na   >  folha **configurações de diagnóstico** do monitor.

   ![Seleções para localizar o gateway de VPN nas configurações de diagnóstico](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Selecionar")

5. Para ativar o diagnóstico, clique duas vezes no gateway e, em seguida, selecione **Ativar diagnóstico**.

   ![Seleções para ativar o diagnóstico](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Selecionar")

6. Preencha os detalhes e verifique se **Enviar para log Analytics** e **TunnelDiagnosticLog** estão selecionados. Escolha o espaço de trabalho Log Analytics que você criou na etapa 3.

   ![Caixas de seleção selecionadas](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Selecionar")

   > [!NOTE]
   > Pode levar algumas horas para que os dados sejam exibidos inicialmente.

7. Vá para a visão geral do recurso de gateway de rede virtual e selecione **alertas** na guia **monitoramento** . Em seguida, crie uma nova regra de alerta ou edite uma regra de alerta existente.

   ![Seleções para criar uma nova regra de alerta](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecionar")

   ![ponto a site](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Selecionar")
8. Selecione o espaço de trabalho Log Analytics e o recurso.

   ![Seleções de espaço de trabalho e recurso](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Selecionar")

9. Selecione **pesquisa de logs personalizada** como a lógica de sinal em **Adicionar condição**.

   ![Seleções para uma pesquisa de logs personalizada](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Selecionar")

10. Insira a consulta a seguir na caixa de texto **consulta de pesquisa** . Substitua os valores em <> e TimeGenerated conforme apropriado.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Defina o valor do limite como 0 e selecione **concluído**.

    ![Inserindo uma consulta e selecionando um limite](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Selecionar")

11. Na página **criar regra** , selecione **criar novo** na seção **grupos de ação** . Preencha os detalhes e selecione **OK**.

    ![Detalhes de um novo grupo de ação](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Selecionar")

12. Na página **criar regra** , preencha os detalhes de **Personalizar ações** e verifique se o nome correto aparece na seção **nome do grupo de ações** . Selecione **criar regra de alerta** para criar a regra.

    ![Seleções para criar uma regra](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Selecionar")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Configurar alertas usando o PowerShell

As etapas de exemplo a seguir criam um alerta para um evento de desconexão que envolve um túnel VPN site a site.

1. Criar um espaço de trabalho Log Analytics:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. Ative o diagnóstico para o gateway de VPN:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Crie um grupo de ação.

   Esse código cria um grupo de ação que envia uma notificação por email quando um alerta é disparado:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Criar uma regra de alerta com base em uma pesquisa de logs personalizada:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Próximas etapas

Para configurar alertas em métricas de túnel, consulte [configurar alertas em métricas de gateway de VPN](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
