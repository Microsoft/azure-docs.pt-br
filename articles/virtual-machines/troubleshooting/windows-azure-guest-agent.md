---
title: Solucionando problemas do agente convidado do Windows Azure
description: Solucionar problemas do agente convidado do Windows Azure não está funcionando
services: virtual-machines-windows
ms.service: virtual-machines-windows
author: dkdiksha
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/15/2020
ms.author: genli
ms.openlocfilehash: 738c2a240ad6c88186357e69b02d33b40d366d7f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91977058"
---
# <a name="troubleshooting-windows-azure-guest-agent"></a>Solucionando problemas do agente convidado do Windows Azure

O agente convidado do Windows Azure é um agente de VM (máquina virtual). Ele permite que a VM se comunique com o controlador de malha (o servidor físico subjacente no qual a VM está hospedada) no endereço IP 168.63.129.16. Esse é um endereço IP público virtual que facilita a comunicação. Para obter mais informações, consulte [o que é o endereço IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

 A VM que é migrada para o Azure do local ou que é criada usando uma imagem personalizada não tem o agente convidado do Windows Azure instalado. Nesses cenários, você precisa instalar manualmente o agente de VM. Para obter mais informações sobre como instalar o agente de VM, consulte [visão geral do agente de máquina virtual do Azure](../extensions/agent-windows.md).

Depois que o agente convidado do Windows Azure for instalado com êxito, você poderá ver os seguintes serviços listados em Services. msc na VM:
 
- Serviço de agente convidado do Windows Azure
- Serviço de telemetria
- Serviço do agente RD

**Serviço de agente convidado do Windows Azure**: esse serviço é o serviço que é responsável por todo o log em WAppAgent. log. Esse serviço é responsável por configurar várias extensões e comunicação do convidado para o host. 

**Serviço de telemetria**: esse serviço é responsável por enviar os dados de telemetria da VM para o servidor de back-end.

**Serviço do agente RD**: esse serviço é responsável pela instalação do agente convidado. O instalador transparente também é um componente do agente de RD que ajuda a atualizar outros componentes e serviços do agente convidado. RDAgent também é responsável por enviar pulsações da VM convidada para o agente de host no servidor físico.

> [!NOTE]
> A partir da versão 2.7.41491.971 do agente convidado da VM, o componente telemetria está incluído no serviço RDAgent, portanto, você pode não ver esse serviço de telemetria listado em VMs recém-criadas.

## <a name="checking-agent-status-and-version"></a>Verificando o status e a versão do agente

Vá para a página Propriedades da VM em portal do Azure e verifique o **status do agente**. Se o agente convidado do Windows Azure estiver funcionando corretamente, o status mostrará **pronto**. Se o agente de VM estiver no status **não pronto** , as extensões e o **comando executar** no portal do Azure não funcionarão.

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Solucionando problemas do agente de VM que está no status não pronto

### <a name="step-1-check-whether-the-windows-azure-guest-agent-service-is-installed"></a>Etapa 1 verificar se o serviço do agente convidado do Windows Azure está instalado

- Verificar o pacote

    Localize a pasta C:\WindowsAzure. Se você vir a pasta GuestAgent que exibe o número de versão, isso significa que o agente convidado do Windows Azure foi instalado na VM. Você também pode procurar o pacote instalado.  Se o agente convidado do Windows Azure estiver instalado na VM, o pacote será salvo no seguinte local: `C:\windows\OEM\GuestAgent\VMAgentPackage.zip` .
    
    Você pode executar o seguinte comando do PowerShell para verificar se o agente de VM foi implantado na VM:
    
    `Get-AzVM -ResourceGroupName "RGNAME" -Name "VMNAME" -DisplayHint expand`
    
    Na saída, localize a propriedade **ProvisionVMAgent** e verifique se o valor está definido como **true**. Se for, isso significa que o agente está instalado na VM.
    
- Verificar os serviços e processos

   Vá para o console de serviços (Services. msc) e verifique o status dos seguintes serviços: serviço de agente convidado do Windows Azure, serviço RDAgent, serviço de telemetria do Windows Azure e serviço de agente de rede do Windows Azure.
 
    Você também pode verificar se esses serviços estão em execução examinando o Gerenciador de tarefas para os seguintes processos:
       
    - WindowsAzureGuestAgent.exe: serviço de agente convidado do Windows Azure
    - WaAppAgent.exe: serviço RDAgent
    - WindowsAzureNetAgent.exe: serviço de agente de rede do Windows Azure
    - WindowsAzureTelemetryService.exe: serviço de telemetria do Windows Azure

   Se você não encontrar esses processos e serviços, isso indica que você não tem o agente convidado do Windows Azure instalado.

- Verificar o programa e o recurso

    No painel de controle, vá para **programas e recursos** para determinar se o serviço do agente convidado do Windows Azure está instalado.

Se você não encontrar pacotes, serviços e processos em execução e nem mesmo ver o agente convidado do Windows Azure instalado em programas e recursos, tente [instalar o serviço de agente convidado do Windows Azure](../extensions/agent-windows.md). Se o agente convidado não for instalado corretamente, você poderá [instalar o agente de VM offline](./install-vm-agent-offline.md).

Se você puder ver os serviços e eles estiverem em execução, reinicie o serviço que veja se o problema foi resolvido. Se os serviços forem interrompidos, inicie-os e aguarde alguns minutos. Em seguida, verifique se o **status do agente** está relatando como **pronto**. Se você descobrir que esses serviços estão falhando, alguns processos de terceiros podem estar causando falhas nesses serviços. Para solucionar problemas desses problemas, entre em contato com [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="step-2-check-whether-auto-update-is-working"></a>Etapa 2 verificar se a atualização automática está funcionando

O agente convidado do Windows Azure tem um recurso de atualização automática. Ele verificará se há novas atualizações automaticamente e as instalará. Se o recurso de atualização automática não funcionar corretamente, tente desinstalar e instalar o agente convidado do Windows Azure usando as seguintes etapas:

1. Se o agente convidado do Windows Azure aparecer em **programas e recursos**, desinstale o agente convidado do Windows Azure.

2. Abra uma janela de prompt de comando que tenha privilégios de administrador.

3. Pare os serviços de agente convidado. Se os serviços não forem interrompidos, você deverá definir os serviços para **inicialização manual** e reiniciar a VM.
    ```
    net stop rdagent
    net stop WindowsAzureGuestAgent
    net stop WindowsAzureTelemetryService
    ```
1. Exclua os serviços de agente convidado:
    ```
    sc delete rdagent
    sc delete WindowsAzureGuestAgent
    sc delete WindowsAzureTelemetryService
    ```
1. Em `C:\WindowsAzure` criar uma pasta chamada Old.

1. Mova todas as pastas nomeadas pacotes ou GuestAgent para a pasta antiga.

1. Baixe e instale a versão mais recente do pacote de instalação do agente [aqui](https://go.microsoft.com/fwlink/?linkid=394789&clcid=0x409). Você precisa ter direitos de Administrador para concluir a instalação.

1. Instale o agente convidado usando o seguinte comando:

    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log
    ```
    Em seguida, verifique se os serviços de agente convidado são iniciados corretamente.
 
    Em casos raros em que o agente convidado não é instalado corretamente, você pode [instalar o agente de VM offline](./install-vm-agent-offline.md).
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Etapa 3 verificar se a VM pode se conectar ao controlador de malha

Use uma ferramenta como PsPing para testar se a VM pode se conectar ao 168.63.129.16 nas portas 80, 32526 e 443. Se a VM não se conectar conforme o esperado, verifique se a comunicação de saída pelas portas 80, 443 e 32526 está aberta no firewall local na VM. Se esse endereço IP estiver bloqueado, o agente de VM poderá exibir um comportamento inesperado em uma variedade de cenários.

## <a name="advanced-troubleshooting"></a>Solução de problemas avançada

Os eventos para solução de problemas do agente convidado do Windows Azure são registrados nos seguintes arquivos de log:

- C:\WindowsAzure\Logs\WaAppAgent.log
- C:\WindowsAzure\Logs\TransparentInstaller.log

Veja a seguir alguns cenários comuns em que o agente convidado do Windows Azure pode inserir o status **não pronto** ou parar de funcionar conforme o esperado.

### <a name="agent-stuck-on-starting"></a>Agente travado em "Iniciando"

No log do WaAppAgent, você pode ver que o agente está preso no processo inicial e não pode iniciar.

**Informações do log**
 
[00000007] [05/28/2019 12:58:50.90] [INFO] WindowsAzureGuestAgent iniciando. 2.7.41491.901 da versão

**Analisa**
 
A VM ainda está executando a versão mais antiga do agente convidado do Windows Azure. Na pasta C:\WindowsAzure, você pode observar que várias instâncias de agente convidado do Windows Azure estão instaladas, incluindo várias da mesma versão. Como várias instâncias de agente são instaladas, a VM não inicia a versão mais recente do agente convidado do Windows Azure.

**Solução**

Desinstale manualmente o agente convidado do Windows Azure e reinstale-o seguindo estas etapas:

1. Abra o painel de controle > programas e recursos e desinstale o agente convidado do Windows Azure.
1. Abra o Gerenciador de tarefas e pare os seguintes serviços: serviço de agente convidado do Windows Azure, serviço RDAgent, serviço de telemetria do Windows Azure e serviço de agente de rede do Windows Azure.
1. Em C:\WindowsAzure, crie uma pasta chamada OLD.
1. Mova todas as pastas nomeadas pacotes ou GuestAgent para a pasta antiga. Além disso, mova qualquer uma das pastas GuestAgent em C:\WindowsAzure\logs que comecem como GuestAgent_x. x. xxxxx para a pasta antiga.
1. Baixe e instale a versão mais recente do agente MSI. Você deve ter direitos de administrador para concluir a instalação.
1. Instale o agente convidado usando o seguinte comando MSI:
    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log`
    ```
1. Verifique se o RDAgent, o agente convidado do Windows Azure e os serviços de telemetria do Windows Azure agora estão em execução.
 
1. Verifique o WaAppAgent. log para certificar-se de que a versão mais recente do agente convidado do Windows Azure está em execução.
 
1. Exclua a pasta antiga em C:\WindowsAzure.
  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Não é possível conectar-se ao WireServer IP (IP do host) 

Você observa as seguintes entradas de erro em WaAppAgent. log e telemetria. log:

**Informações do log**

```Log sample
[ERROR] GetVersions() failed with exception: Microsoft.ServiceModel.Web.WebProtocolException: Server Error: Service Unavailable (ServiceUnavailable) ---> 
System.Net.WebException: The remote server returned an error: (503) Server Unavailable.
```
```Log sample
[00000011] [12/11/2018 06:27:55.66] [WARN]  (Ignoring) Exception while fetching supported versions from HostGAPlugin: System.Net.WebException: Unable to connect to the remote server 
---> System.Net.Sockets.SocketException: An attempt was made to access a socket in a way forbidden by its access permissions 168.63.129.16:32526
at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
at System.Net.ServicePoint.ConnectSocketInternal(Boolean connectFailure, Socket s4, Socket s6, Socket& socket, IPAddress& address, ConnectSocketState status, IAsyncResult asyncResult, Exception& exception)
--- End of inner exception stack trace ---
at System.Net.WebClient.DownloadDataInternal(Uri address, WebRequest& request)
at System.Net.WebClient.DownloadString(Uri address)
at Microsoft.GuestAgentHostPlugin.Client.GuestInformationServiceClient.GetVersions()
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.HostGAPluginUtility.UpdateHostGAPluginAvailability()`
```
**Analisa**

A VM não pode alcançar o servidor host wireserver.

**Solução**

1. Como o wireserver não está acessível, conecte-se à VM usando Área de Trabalho Remota e tente acessar a URL a seguir por meio de um navegador da Internet: http://168.63.129.16/?comp=versions 
1. Se você não conseguir acessar a URL da etapa 1, verifique o adaptador de rede para determinar se ele está definido como habilitado para DHCP e se tem DNS. Para verificar o status do DHCP, na interface de rede, execute o seguinte comando:  `netsh interface ip show config` .
1. Se o DHCP estiver desabilitado, execute o seguinte, certificando-se de alterar o valor em amarelo para o nome da sua interface: `netsh interface ip set address name="Name of the interface" source=dhcp` .
1. Verifique se há problemas que possam ser causados por um firewall, um proxy ou outra fonte que possa estar bloqueando o acesso ao endereço IP 168.63.129.16.
1. Verifique se o Firewall do Windows ou um firewall de terceiros está bloqueando o acesso às portas 80, 443 e 32526. Para obter mais informações sobre por que esse endereço não deve ser bloqueado, consulte [o que é o endereço IP 168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

### <a name="guest-agent-is-stuck-stopping"></a>O agente convidado está preso em "parando"  

Você observa as seguintes entradas de erro em WaAppAgent. log:

**Informações do log** 

```Log sample
[00000007] [07/18/2019 14:46:28.87] [WARN] WindowsAzureGuestAgent stopping.
[00000007] [07/18/2019 14:46:28.89] [INFO] Uninitializing StateExecutor with WaitForTerminalStateReachedOnEnd : True
[00000004] [07/18/2019 14:46:28.89] [WARN] WindowsAzureGuestAgent could not be stopped. Exception: System.NullReferenceException: Object reference not set to an instance of an object.
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.WaitForExtensionWorkflowComplete(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorForCloud.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentCore.Stop(Boolean waitForTerminalState)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.DoStopService()
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.<>c__DisplayClass2.<OnStopProcessing>b__1()
```
**Analisa**

O agente convidado do Windows Azure está preso no processo de interrupção.

**Solução**

1. Verifique se WaAppAgent.exe está em execução na VM. Se não estiver em execução, reinicie o serviço rdgagent e aguarde cinco minutos. Quando WaAppAgent.exe estiver em execução, encerre o processo de WindowsAzureGuest.exe.
2. Se a etapa 1 não resolver o problema, remova a versão instalada atualmente e instale a versão mais recente do agente manualmente.

### <a name="npcap-loopback-adapter"></a>Adaptador de loopback Npcap 

Você observa as seguintes entradas de erro em WaAppAgent. log:
 
```Log sample
[00000006] [06/20/2019 07:44:28.93] [INFO]  Attempting to discover fabric address on interface Npcap Loopback Adapter.
[00000024] [06/20/2019 07:44:28.93] [WARN]  Empty DHCP option data returned
[00000006] [06/20/2019 07:44:28.93] [ERROR] Did not discover fabric address on interface Npcap Loopback Adapter
```
**Analisa**

O adaptador de loopback Npcap é instalado na VM pelo Wireshark. O Wireshark é uma ferramenta de código aberto para a criação de perfil de tráfego de rede e análise de pacotes. Essa ferramenta é geralmente conhecida como um analisador de rede, analisador de protocolo de rede ou Sniffer.

**Solução**

O adaptador de loopback Npcap é provavelmente instalado pelo WireShark. Tente desabilitá-lo e verifique se o problema foi resolvido.

## <a name="next-steps"></a>Próximas etapas

Para solucionar ainda mais o problema "o agente convidado do Windows Azure não está funcionando", [entre em contato com o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).