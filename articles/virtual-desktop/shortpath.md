---
title: Shortpath RDP do Windows Virtual Desktop (versão prévia)
titleSuffix: Azure
description: Como configurar o RDP Shortpath (visualização) para a área de trabalho virtual do Windows.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 295a46f6d1074ddf8422233ea3ccfa4d65c28fd8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100571589"
---
# <a name="windows-virtual-desktop-rdp-shortpath-preview"></a>Shortpath RDP do Windows Virtual Desktop (versão prévia)

> [!IMPORTANT]
> O RDP Shortpath está atualmente em visualização pública.
> Essa visualização é fornecida sem um contrato de nível de serviço e não é recomendável usá-la para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O RDP Shortpath é um recurso da área de trabalho virtual do Windows que estabelece um transporte baseado em UDP direto entre Área de Trabalho Remota host de sessão e cliente. O RDP usa esse transporte para fornecer Área de Trabalho Remota e o RemoteApp, oferecendo melhor confiabilidade e latência consistente.

## <a name="key-benefits"></a>Principais benefícios

* O transporte Shortpath do RDP é baseado na parte superior do  [URCP (protocolo de controle de taxa universal)](https://www.microsoft.com/en-us/research/publication/urcp-universal-rate-control-protocol-for-real-time-communication-applications/)altamente eficiente. O URCP aprimora o UDP com o monitoramento ativo das condições de rede e fornece a utilização de links justa e completo. O URCP opera em níveis baixos de atraso e perda conforme necessário pelo Área de Trabalho Remota. O URCP alcança o melhor desempenho, aprendendo dinamicamente os parâmetros de rede e fornecendo o protocolo com um mecanismo de controle de taxa.
* O RDP Shortpath estabelece a conectividade direta entre Área de Trabalho Remota host de sessão e cliente. A conectividade direta reduz a dependência dos gateways de área de trabalho virtual do Windows, melhora a confiabilidade da conexão e aumenta a largura de banda disponível para cada sessão de usuário.
* A remoção de retransmissão adicional reduz o tempo de ida e volta, o que melhora a experiência do usuário com aplicativos sensíveis à latência e métodos de entrada.
* O RDP Shortpath oferece suporte para configurar a prioridade de [QoS (qualidade de serviço)](./rdp-quality-of-service-qos.md) para conexões RDP por meio de uma marca de DSCP (ponto de código serviços diferenciados)
* O transporte Shortpath RDP permite [limitar o tráfego de rede de saída](./rdp-bandwidth.md#limit-network-bandwidth-use-with-throttle-rate) especificando uma taxa de aceleração para cada sessão.

## <a name="connection-security"></a>Segurança da conexão

O RDP Shortpath está estendendo os recursos de multitransporte RDP. Ele não substitui o transporte de conexão reversa, mas o complementa. Todo o agente de sessão inicial é gerenciado por meio da infraestrutura de área de trabalho virtual do Windows.

A porta UDP 3390 é usada somente para o tráfego Shortpath de entrada que é autenticado pelo transporte de conexão reversa. O ouvinte RDP Shortpath ignora todas as tentativas de conexão para o ouvinte, a menos que correspondam à sessão de conexão inversa.

O RDP Shortpath usa uma conexão TLS entre o cliente e o host da sessão usando os certificados do host da sessão. Por padrão, o certificado usado para criptografia de RDP é gerado automaticamente pelo sistema operacional durante a implantação. Se desejar, os clientes podem implantar certificados gerenciados centralmente emitidos pela autoridade de certificação corporativa. Para obter mais informações sobre configurações de certificado, consulte a [documentação do Windows Server](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="rdp-shortpath-connection-sequence"></a>Sequência de conexão Shortpath RDP

Depois de instalar o [transporte de conexão reversa](./network-connectivity.md), o cliente e o host de sessão estabelecem a conexão RDP e negociam os recursos de transporte múltiplo. Etapas adicionais descritas abaixo:

1. O host de sessão envia a lista de seus endereços IPv4 e IPv6 públicos e privados para o cliente.
2. O cliente inicia o thread em segundo plano para estabelecer um transporte baseado em UDP paralelo diretamente a um dos endereços IP do host.
3. Embora o cliente esteja investigando os endereços IP fornecidos, ele continua o estabelecimento da conexão inicial sobre o transporte de conexão reversa para garantir que não haja atraso na conexão do usuário.
4. Se o cliente tiver uma linha de visão direta e a configuração do firewall estiver correta, o cliente estabelecerá uma conexão TLS segura com o host de sessão.
5. Depois de estabelecer o transporte Shortpath, o RDP move todos os DVCs (canais virtuais dinâmicos), incluindo gráficos remotos, entrada e redirecionamento de dispositivo para o novo transporte.
6. Se uma topologia de rede ou firewall impedir que o cliente estabeleça a conectividade UDP direta, o RDP continuará com um transporte de conexão reversa.

O diagrama a seguir fornece uma visão geral de alto nível da conexão de rede RDP Shortpath.

:::image type="content" source="media/rdp-shortpath-connections.svg" alt-text="Diagrama de conexões de rede Shortpath de RDP" lightbox="media/rdp-shortpath-connections.svg":::

## <a name="requirements"></a>Requisitos

Para dar suporte a Shortpath RDP, o cliente de área de trabalho virtual do Windows precisa de uma linha direta de visão para o host da sessão. Você pode obter uma linha de visão direta usando uma das seguintes tecnologias:

* [Emparelhamento privado do ExpressRoute](../expressroute/expressroute-circuit-peerings.md)
* [VPN site a site (baseada em IPsec)](../vpn-gateway/tutorial-site-to-site-portal.md)
* [VPN ponto a site (baseada em IPsec)](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [Atribuição de endereço IP público](../virtual-network/virtual-network-public-ip-address.md)

Se você estiver usando outros tipos de VPN para se conectar à rede virtual do Azure, recomendamos usar a VPN baseada em UDP para obter os melhores resultados. Embora a maioria das soluções VPN baseadas em TCP encapsulasse todos os pacotes IP, incluindo UDP, eles adicionam sobrecarga herdada do controle de congestionamento TCP que tornaria o desempenho de RDP lento.

A linha de visão direta significa que os firewalls não estão bloqueando a porta UDP 3390 e o cliente pode se conectar diretamente ao host da sessão.

## <a name="enabling-rdp-shortpath-preview"></a>Habilitando a visualização do RDP Shortpath

Para participar da visualização do RDP Shortpath, você precisa habilitar o ouvinte de Shortpath do RDP no host da sessão. Você pode habilitar o RDP Shortpath em qualquer número de hosts de sessão usados em seu ambiente. Não há nenhum requisito para habilitar o Shortpath de RDP em todos os hosts no pool.
Para habilitar o ouvinte Shortpath, você precisa configurar os seguintes valores de registro:

> [!WARNING]
> Problemas sérios podem ocorrer se você modificar o registro incorretamente usando o editor do registro ou usando outro método. Esses problemas podem exigir que você reinstale o sistema operacional. A Microsoft não garante que esses problemas possam ser solucionados. Modifique o Registro por conta própria.

1. No host da sessão, inicie Regedit.exe e, em seguida, navegue até o seguinte local:

    ```cmd
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations
    ```

2. Crie um novo valor **DWORD** chamado **fUseUdpPortRedirector** e defina-o como **1** (Decimal)
3. Crie um novo valor **DWORD** chamado **UdpPortNumber** e defina-o como **3390** (Decimal)
4. Feche o Editor do Registro.
5. Reiniciar host de sessão

Você também pode executar os seguintes cmdlets em uma janela do PowerShell com privilégios elevados para definir esses valores de registro:

```powershell
$WinstationsKey = 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations'
New-ItemProperty -Path $WinstationsKey -Name 'fUseUdpPortRedirector' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 1 -Force
New-ItemProperty -Path $WinstationsKey -Name 'UdpPortNumber' -ErrorAction:SilentlyContinue -PropertyType:dword -Value 3390 -Force
```

Você também pode usar o PowerShell para configurar a política de grupo

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'fUseUdpPortRedirector' -Value 1 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
Set-GPPrefRegistryValue -Key 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations' -ValueName 'UdpPortNumber' -Value 3390 -Type:DWord  -Action:Create -Context:Computer -Name $policyName -Domain $domainName
```

## <a name="configure-windows-defender-firewall-with-advanced-security"></a>Configurar o Windows Defender firewall com segurança avançada

Para permitir o tráfego de rede de entrada para Shortpath RDP, use o nó Windows Defender firewall com segurança avançada no snap-in do MMC de gerenciamento de Política de Grupo para criar regras de firewall.

1. Abra o Console de Gerenciamento de Política de Grupo para o [Windows Defender firewall com segurança avançada](/windows/security/threat-protection/windows-firewall/open-the-group-policy-management-console-to-windows-firewall-with-advanced-security).
2. No painel de navegação, selecione **regras de entrada**.
3. Selecione **ação** e, em seguida, selecione **nova regra**.
4. Na página **tipo de regra** do assistente de nova regra de entrada, selecione **personalizado** e, em seguida, selecione **Avançar**.
5. Na página **programa** , selecione **o caminho do programa** e digite "% SystemRoot% \system32\svchost.exe" e, em seguida, selecione **Avançar**.
6. Na página **protocolo e portas** , selecione o tipo de protocolo UDP. Na **porta local**, selecione "portas específicas" e digite 3390.
7. Na página **Escopo**, você pode especificar se a regra deverá ser aplicada somente ao tráfego de rede enviado ou recebido pelos endereços IP indicados nesta página. Configure conforme apropriado para seu design e, em seguida, selecione **Avançar**.
8. Na página **ação** , selecione **permitir a conexão** e, em seguida, selecione **Avançar**.
9. Na página **perfil** , selecione os tipos de local de rede aos quais essa regra se aplica e, em seguida, selecione **Avançar**.
10. Na página **nome** , digite um nome e uma descrição para a regra e, em seguida, selecione **concluir**.

Você pode verificar se a nova regra corresponde às capturas de tela abaixo: :::image type="content" source="media/rdp-shortpath-firewall-general-tab.png" alt-text="captura de tela da guia geral para configuração de firewall para conexões de rede Shortpath de RDP" lightbox="media/rdp-shortpath-firewall-general-tab.png":::

:::image type="content" source="media/rdp-shortpath-firewall-service-settings.png" alt-text="Captura de tela da guia programas e serviços para configuração de firewall para conexões de rede Shortpath de RDP" lightbox="media/rdp-shortpath-firewall-service-settings.png":::

:::image type="content" source="media/rdp-shortpath-firewall-protocol-and-ports.png" alt-text="Captura de tela da guia protocolos e portas para configuração de firewall para conexões de rede Shortpath de RDP" lightbox="media/rdp-shortpath-firewall-protocol-and-ports.png":::

Você também pode usar o PowerShell para configurar o Firewall do Windows:

```powershell
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP'  -PolicyStore PersistentStore -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True
```

### <a name="using-powershell-to-configure-windows-defender-firewall"></a>Usando o PowerShell para configurar o Windows Defender firewall

Você também pode usar o PowerShell para configurar a política de grupo

```powershell
# Replace $domainName value with the name of your Active Directory domain
# Replace $policyName value with the name of existing Group Policy Object
$domainName = "contoso.com"
$policyName = "RDP Shortpath Policy"
$gpoSession = Open-NetGPO -PolicyStore "$domainName\$policyName"
New-NetFirewallRule -DisplayName 'Remote Desktop - Shortpath (UDP-In)'  -Action Allow -Description 'Inbound rule for the Remote Desktop service to allow RDP traffic. [UDP 3390]' -Group '@FirewallAPI.dll,-28752' -Name 'RemoteDesktop-UserMode-In-Shortpath-UDP' -Profile Domain, Private -Service TermService -Protocol udp -LocalPort 3390 -Program '%SystemRoot%\system32\svchost.exe' -Enabled:True -GPOSession $gpoSession
Save-NetGPO -GPOSession $gpoSession
```

## <a name="configuring-azure-network-security-group"></a>Configurando o grupo de segurança de rede do Azure

Para permitir o acesso ao ouvinte de Shortpath de RDP entre limites de segurança de rede, você precisa configurar o grupo de segurança de rede do Azure para permitir a porta UDP de entrada 3390.
Siga a [documentação do grupo de segurança de rede](../virtual-machines/windows/nsg-quickstart-portal.md) para criar uma regra de segurança de entrada que permita o tráfego com os seguintes parâmetros:

* **Origem**  -  do **Qualquer um** ou o intervalo de IP no qual os clientes estão residentes
* **Intervalos de portas de origem** - **\***
* **Destino**  -  **Qualquer**
* Intervalos de portas de **destino**  -  **3390**
* **Protocolo**  -  do **UDP**
* **Ação**  -  do **Permitir**
* Opcionalmente, altere a **prioridade**. A prioridade afeta a ordem na qual as regras são aplicadas: quanto menor for o valor numérico, mais cedo a regra será aplicada.
* **Nome** -- **Shortpath RDP**

### <a name="disabling-rdp-shortpath-for-a-specific-subnet"></a>Desabilitando Shortpath RDP para uma sub-rede específica

Se você precisar bloquear sub-redes específicas usando o transporte Shortpath do RDP, poderá configurar grupos de segurança de rede adicionais especificando os intervalos de IP de origem.

## <a name="verifying-the-connectivity"></a>Verificando a conectividade

### <a name="using-connection-information-dialog"></a>Usando a caixa de diálogo informações de conexão

Para verificar se as conexões estão usando o RDP Shortpath, abra a caixa de diálogo "informações de conexão" clicando no ícone de antena na barra de ferramentas de conexão.

:::image type="content" source="media/rdp-shortpath-connection-bar.png" alt-text="Imagem da barra de Conexão de Área de Trabalho Remota":::

:::image type="content" source="media/rdp-shortpath-connection-info.png" alt-text="Imagem da caixa de diálogo de informações do Conexão de Área de Trabalho Remota":::

### <a name="using-event-logs"></a>Usando logs de eventos

Para verificar se a sessão está usando o transporte Shortpath do RDP:

1. Conecte-se à área de trabalho da VM usando o cliente da Área de Trabalho Virtual do Windows.
2. Inicie o Visualizador de Eventos e navegue até o seguinte nó: **logs de aplicativos e serviços > microsoft > Windows > RemoteDesktopServices-RdpCoreCDV > Microsoft-Windows-RemoteDesktopServices-RdpCoreCDV/Operational**
3. Para determinar se o transporte Shortpath de RDP é usado, procure a ID de evento 131.

### <a name="using-log-analytics-to-verify-shortpath-connectivity"></a>Usando Log Analytics para verificar a conectividade Shortpath

Se você estiver usando o [Azure log Analytics](./diagnostics-log-analytics.md), poderá monitorar conexões consultando a [tabela WVDConnections](/azure/azure-monitor/reference/tables/wvdconnections). Uma coluna denominada UdpUse, indica se A pilha RDP do Windows Virtual Desktop usa o protocolo UDP na conexão do usuário atual.
Os valores possíveis são:

* **0** -a conexão do usuário não está usando o RDP Shortpath
* **1** -a conexão do usuário está usando o RDP Shortpath
  
A lista de consulta a seguir permite que você revise as informações de conexão. Você pode executar essa consulta no [Editor de consulta log Analytics](../azure-monitor/logs/log-analytics-tutorial.md#write-a-query). Para cada consulta, substitua `userupn` pelo UPN do usuário que você deseja pesquisar.

```kusto
let Events = WVDConnections | where UserName == "userupn" ;
Events
| where State == "Connected"
| project CorrelationId , UserName, ResourceAlias , StartTime=TimeGenerated, UdpUse, SessionHostName, SessionHostSxSStackVersion
| join (Events
| where State == "Completed"
| project EndTime=TimeGenerated, CorrelationId, UdpUse)
on CorrelationId
| project StartTime, Duration = EndTime - StartTime, ResourceAlias, UdpUse,  SessionHostName, SessionHostSxSStackVersion
| sort by StartTime asc
```

## <a name="troubleshooting"></a>Solução de problemas

### <a name="verify-shortpath-listener"></a>Verificar ouvinte Shortpath

Para verificar se o ouvinte UDP está habilitado, use o seguinte comando do PowerShell no host da sessão:

```powershell
Get-NetUDPEndpoint -OwningProcess ((Get-WmiObject win32_service -Filter "name = 'TermService'").ProcessId)  -LocalPort 3390
```

Se habilitada, você verá a saída semelhante à seguinte

```dos
LocalAddress                             LocalPort
------------                             ---------
::                                       3390
0.0.0.0                                  3390
```

Se houver um conflito, você poderá identificar o processo que ocupa a porta usando o comando a seguir

```powershell
Get-Process -id (Get-NetUDPEndpoint  -LocalPort 3390 -LocalAddress 0.0.0.0).OwningProcess
```

## <a name="disabling-rdp-shortpath"></a>Desabilitando Shortpath RDP

Em alguns casos, talvez seja necessário desabilitar o transporte Shortpath do RDP. Você pode desabilitar o Shortpath RDP usando a política de grupo.

### <a name="disabling-rdp-shortpath-on-the-client"></a>Desabilitando Shortpath RDP no cliente

Para desabilitar o Shortpath de RDP para um cliente específico, você pode usar os seguintes Política de Grupo para desabilitar o suporte a UDP:

1. No cliente, execute **gpedit. msc**.
2. Navegue até **configuração do computador > modelos de administração > componentes do Windows > Serviços de Área de Trabalho Remota > conexão de área de trabalho remota cliente**.
3. Defina a configuração **"desligar UDP no cliente"** como **habilitado**

### <a name="disabling-rdp-shortpath-on-the-session-host"></a>Desabilitando Shortpath RDP no host de sessão

Para desabilitar o Shortpath RDP para um host de sessão específico, você pode usar os seguintes Política de Grupo para desabilitar o suporte a UDP:

1. No host da sessão, execute **gpedit. msc**.
2. Navegue até **configuração do computador > modelos de administração > componentes do Windows > Serviços de Área de Trabalho Remota > conexão de área de trabalho remota Host > conexões**.
3. Defina a configuração **"selecionar protocolos de transporte RDP"** como **somente TCP**

## <a name="public-preview-feedback"></a>Comentários da visualização pública

Gostaríamos de conhecer suas experiências com esta visualização pública!
* Para perguntas, solicitações, comentários e outros comentários, [Use este formulário de comentários](https://aka.ms/RDPShortpathFeedback).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre conectividade de rede de área de trabalho virtual do Windows, consulte [noções básicas sobre conectividade de rede de área de trabalho virtual](network-connectivity.md)
* Para começar a usar a qualidade de serviço (QoS) para área de trabalho virtual do Windows, consulte [implementar a QoS (qualidade de serviço) para a área de trabalho virtual do Windows](rdp-quality-of-service-qos.md).