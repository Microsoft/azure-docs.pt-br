---
title: Solução de problemas detalhada da área de trabalho remota no Azure | Microsoft Docs
description: Examinar as etapas detalhadas para solução de problemas para erros da área de trabalho remota, em que não é possível usar a área de trabalho remota para conectar às máquinas virtuais do Windows no Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: não é possível conectar à área de trabalho remota, solucionar problemas da área de trabalho remota, a área de trabalho remota não pode conectar, erros da área de trabalho remota, solução de problemas da área de trabalho remota, problemas da área de trabalho remota
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 56fa1363e9737a2ce06ba5f0215235a84ee70ce6
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709906"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Etapas detalhadas de solução de problemas para problemas de conexão de área de trabalho remota para VMs do Windows no Azure
Este artigo fornece etapas detalhadas de solução de problemas a fim de diagnosticar e corrigir erros complexos de Área de Trabalho Remota para máquinas virtuais do Azure baseadas no Windows.

> [!IMPORTANT]
> Para eliminar os erros mais comuns da Área de Trabalho Remota, leia [o artigo de solução de problemas básicos da Área de Trabalho Remota](troubleshoot-rdp-connection.md) antes de continuar.

Você pode encontrar uma mensagem de erro da Área de Trabalho Remota que não se parece com nenhuma das mensagens de erro específicas tratadas no [Guia básico de solução de problemas da Área de Trabalho Remota](troubleshoot-rdp-connection.md). Siga estas etapas para determinar por que o cliente RDP (área de trabalho remota) não é capaz de se conectar ao serviço RDP na VM do Azure.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Se você precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure MSDN e Excedente de Pilha](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Vá para o [site de Suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter Suporte**. Para saber mais sobre como usar o suporte do Azure, leia as [Perguntas frequentes do Suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Componentes de uma conexão da Área de Trabalho Remota
Estes são os componentes envolvidos em uma conexão RDP:

![](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Antes de continuar, talvez seja útil revisar mentalmente o que mudou desde a última conexão bem-sucedida da Área de Trabalho Remota com a VM. Por exemplo:

* O endereço IP público da VM ou do serviço de nuvem que contém a máquina virtual (também chamado de [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address), endereço IP virtual) foi alterado. A falha RDP pode ter ocorrido porque o cache do cliente DNS ainda tem o *endereço IP antigo* registrado para o nome DNS. Libere o cache do cliente DNS e tente conectar-se novamente à VM. Ou tente conectar-se diretamente com o novo VIP.
* Você está usando um aplicativo de terceiros para gerenciar suas conexões de Área de Trabalho Remota em vez de usar a conexão gerada pelo portal do Azure. Verifique se a configuração do aplicativo inclui a porta TCP correta para o tráfego de Área de Trabalho Remota. Você pode verificar essa porta para ter uma máquina virtual clássica no [portal do Azure](https://portal.azure.com), clicando em Configurações da VM > Pontos de Extremidade.

## <a name="preliminary-steps"></a>Etapas preliminares
Antes de prosseguir para a solução de problemas detalhada,

* verifique o status da máquina virtual no portal do Azure em busca de problemas óbvios.
* Siga as [etapas de correção rápida para os erros comuns de RDP no guia de solução de problemas básicos](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* Para imagens personalizadas, verifique se o VHD está corretamente preparado antes de carregá-lo. Para obter mais informações, consulte [Preparar um VHD ou VHDX do Windows para carregar no Azure](../windows/prepare-for-upload-vhd-image.md).


Tente reconectar-se à VM por meio da Área de Trabalho Remota após estas etapas.

## <a name="detailed-troubleshooting-steps"></a>Etapas detalhadas de solução de problemas
Talvez o cliente da Área de Trabalho Remota não consiga acessar o serviço da Área de Trabalho Remota na VM do Azure devido a problemas nas seguintes fontes:

* [Computador cliente de Área de Trabalho Remota](#source-1-remote-desktop-client-computer)
* [Dispositivo de borda de intranet da organização](#source-2-organization-intranet-edge-device)
* [Ponto de extremidade de serviço de nuvem e ACL (lista de controle de acesso)](#source-3-cloud-service-endpoint-and-acl)
* [Grupos de segurança de rede](#source-4-network-security-groups)
* [VM do Azure Baseada em Windows](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Fonte 1: Computador cliente de Área de Trabalho Remota
Verifique se o computador pode estabelecer conexões de Área de Trabalho Remota com outro computador local baseado em Windows.

![](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Se isso não for possível, verifique as seguintes configurações em seu computador:

* Uma configuração de firewall local que está bloqueando o tráfego de Área de Trabalho Remota.
* Um software de proxy cliente instalado localmente que está impedindo conexões de Área de Trabalho Remota.
* Um software de monitoramento de rede instalado localmente que está impedindo conexões de Área de Trabalho Remota.
* Outros tipos de software de segurança que monitoram o tráfego ou permitem/não permitem tipos específicos de tráfego e que estão impedindo conexões de Área de Trabalho Remota.

Em todos esses casos, desabilite temporariamente o software e tente estabelecer uma conexão com um computador local por meio da Área de Trabalho Remota. Se você puder encontrar a causa real dessa forma, trabalhe com o administrador de rede para corrigir as configurações do software a fim de permitir as conexões de Área de Trabalho Remota.

## <a name="source-2-organization-intranet-edge-device"></a>Fonte 2: Dispositivo de borda de intranet da organização
Verifique se um computador conectado diretamente à Internet pode estabelecer conexões de Área de Trabalho Remota com sua máquina virtual do Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Se não tiver um computador que esteja diretamente conectado à Internet, crie e teste com uma nova máquina virtual do Azure em um grupo de recursos ou serviço de nuvem. Para obter mais informações, consulte [Criar uma máquina virtual que executa o Windows no Azure](../virtual-machines-windows-hero-tutorial.md). Após o teste, você pode excluir a máquina virtual e o grupo de recursos ou o serviço de nuvem.

Se você puder criar uma conexão de Área de Trabalho Remota com um computador conectado diretamente à Internet, verifique se há o seguinte no dispositivo de borda de intranet da organização:

* Um firewall interno bloqueando conexões HTTPS com a Internet.
* Um servidor proxy impedindo conexões de Área de Trabalho Remota.
* Software de detecção de intrusão ou monitoramento de rede executado em dispositivos em sua rede de borda que está impedindo conexões de Área de Trabalho Remota.

Trabalhe com o administrador da rede para corrigir as configurações do dispositivo de borda de intranet da organização para permitir as conexões de Área de Trabalho Remota baseadas em HTTPS com a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Fonte 3: Ponto de extremidade de serviço de nuvem e ACL
Para VMs criadas usando o modelo de implantação clássico, verifique se outra VM do Azure que está no mesmo serviço de nuvem ou rede virtual pode estabelecer conexões da Área de Trabalho Remota com sua VM do Azure.

![](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> Para máquinas virtuais criadas no Gerenciador de recursos, vá para a [fonte 4: Grupos de segurança de rede](#source-4-network-security-groups).

Se não houver outra máquina virtual no mesmo serviço de nuvem ou rede virtual, você poderá criar uma. Siga as etapas em [Criar uma máquina virtual que executa o Windows no Azure](../virtual-machines-windows-hero-tutorial.md). Exclua a máquina virtual de teste após esse teste ser concluído.

Se você puder se conectar a uma máquina virtual por meio da Área de Trabalho Remota no mesmo serviço de nuvem ou rede virtual, verifique as seguintes configurações:

* A configuração de ponto de extremidade para o tráfego de área de trabalho remota na VM de destino: A porta TCP privada do ponto de extremidade deve corresponder à porta TCP na qual o serviço de área de trabalho remota da VM está escutando (o padrão é 3389).
* A ACL para o ponto de extremidade do tráfego de área de trabalho remota na VM de destino: As ACLs permitem que você especifique permitido ou negado o tráfego de entrada da Internet com base em seu endereço IP de origem. ACLs configuradas incorretamente podem impedir o tráfego de Área de Trabalho Remota para o ponto de extremidade. Verifique suas ACLs para verificar se o tráfego de entrada dos endereços IP públicos de seu proxy ou outro servidor de borda é permitido. Para obter mais informações, veja [O que é uma ACL (Lista de Controle de Acesso) de Rede?](../../virtual-network/virtual-networks-acl.md)

Para verificar se o ponto de extremidade é a fonte do problema, remova o ponto de extremidade atual e crie um novo, escolhendo uma porta aleatória no intervalo 49152-65535 para o número da porta externa. Para obter mais informações, consulte [Como configurar pontos de extremidade para uma máquina virtual](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="source-4-network-security-groups"></a>Fonte 4: Grupos de segurança de rede
Os grupos de segurança de rede proporcionam um controle mais granular do tráfego de entrada e de saída permitido. Você pode criar regras que abrangem sub-redes e serviços de nuvem em uma rede virtual do Azure.

Use a [verificação de fluxo de IP](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md) para confirmar se uma regra em um Grupo de Segurança de Rede está bloqueando o tráfego de ou para uma máquina virtual. Você também pode examinar as regras de grupo de segurança efetivas para garantir que a regra "Permitir" NSG existe e é priorizada para a porta RDP(padrão 3389). Para saber mais, confira [Usar regras de segurança efetivas para solucionar problemas de fluxo de tráfego de VM](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>Fonte 5: VM do Azure baseada no Windows
![](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Siga as instruções [deste artigo](../windows/reset-rdp.md). Este artigo redefine o serviço de Área de Trabalho Remota na máquina virtual:

* Habilitar a regra de firewall padrão do Windows de "Área de Trabalho Remota" (porta TCP 3389).
* Habilitar conexões de Área de Trabalho Remota definindo o valor do Registro HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections como 0.

Tente estabelecer a conexão de seu computador novamente. Se você ainda não conseguir conectar-se via Área de Trabalho Remota, verifique os possíveis problemas a seguir:

* O serviço de Área de Trabalho Remota não está em execução na VM de destino.
* O serviço de Área de Trabalho Remota não está escutando na porta TCP 3389.
* O Firewall do Windows ou outro firewall local tem uma regra de saída que está impedindo o tráfego de Área de Trabalho Remota.
* Um software de detecção de intrusão ou monitoramento de rede em execução na máquina virtual do Azure está impedindo conexões de Área de Trabalho Remota.

Para VMs criadas usando o modelo de implantação clássico, você pode usar uma sessão remota do Azure PowerShell para a máquina virtual do Azure. Primeiro, você precisa instalar um certificado para o serviço de nuvem de hospedagem da máquina virtual. Acesse [Configurar Acesso Seguro Remoto do PowerShell às Máquinas Virtuais do Azure](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) e baixe o arquivo de script **InstallWinRMCertAzureVM.ps1** para o computador local.

Em seguida, instale o Azure PowerShell, se ainda não tiver feito isso. Consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

Em seguida, abra um prompt de comando do Azure PowerShell e altere a pasta atual para o local do arquivo de script **InstallWinRMCertAzureVM.ps1** . Para executar um script do Azure PowerShell, é preciso definir a política de execução correta. Execute o comando **Get-ExecutionPolicy** para determinar o nível de política atual. Para obter informações sobre como definir o nível apropriado, consulte [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Em seguida, preencha o nome de sua assinatura do Azure, o nome do serviço de nuvem e o nome da máquina virtual (removendo os caracteres < e >), depois execute estes comandos.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Você pode obter o nome da assinatura correto na propriedade *SubscriptionName* da exibição do comando **Get-AzureSubscription**. Você pode obter o nome do serviço de nuvem para a máquina virtual na coluna *ServiceName* na exibição do comando **Get-AzureVM**.

Verifique se você tem o novo certificado. Abra um snap-in de Certificados para o usuário atual e examine a pasta **Autoridades\Certificados de Certificação da Raiz Confiáveis**. Você deve ver um certificado com o nome DNS de seu serviço de nuvem na coluna Emitido para (exemplo: cloudservice4testing.cloudapp.net).

Em seguida, inicie uma sessão remota do Azure PowerShell usando estes comandos.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

Depois de inserir credenciais de administrador válidas, você verá algo semelhante ao prompt do Azure PowerShell a seguir:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

A primeira parte desse prompt é seu nome de serviço de nuvem que contém a VM de destino, que pode ser diferente de "cloudservice4testing.cloudapp.net". Agora é possível emitir comandos do Azure PowerShell para esse serviço de nuvem para investigar os problemas mencionados e corrigir a configuração.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Para corrigir manualmente os Serviços de Área de Trabalho Remota escutando a porta TCP
No prompt de sessão remota do Azure PowerShell, execute este comando.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

A propriedade PortNumber mostra o número de porta atual. Se necessário, altere o número de porta da Área de Trabalho Remota de volta para seu valor padrão (3389) usando este comando.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Verifique se a porta foi alterada para 3389 usando este comando.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Saia da sessão remota do Azure PowerShell usando este comando.

```powershell
Exit-PSSession
```

Verifique se o ponto de extremidade da Área de Trabalho Remota para a VM do Azure também está usando a porta TCP 3398 como sua porta interna. Reinicie a VM do Azure e tente novamente a conexão de Área de Trabalho Remota.

## <a name="additional-resources"></a>Recursos adicionais
[Como redefinir uma senha ou o serviço da Área de Trabalho Remota para as máquinas virtuais do Windows](../windows/reset-rdp.md)

[Como instalar e configurar o Azure PowerShell](/powershell/azure/overview)

[Solucionar problemas de conexões SSH (Secure Shell) para uma máquina virtual do Azure baseada em Linux](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

