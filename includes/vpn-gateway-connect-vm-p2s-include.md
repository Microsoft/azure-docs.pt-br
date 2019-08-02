---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 35814de74fa03f9969cdd48882a5f672cfe306a1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171783"
---
Você pode se conectar a uma VM que é implantada em sua rede virtual criando uma Conexão de Área de trabalho remota para a sua VM. É a melhor maneira de verificar inicialmente se você pode se conectar à sua VM usando seu endereço IP privado, em vez do nome do computador. Dessa forma, você está testando para ver se você pode se conectar, não se a resolução de nomes está configurada corretamente.

1. Localize o endereço IP privado. Você pode encontrar o endereço IP privado de uma VM consultando as propriedades para a VM no portal do Azure ou usando o PowerShell.

   - Porta do Azure - Localize sua máquina virtual do Windows no portal do Azure. Exiba as propriedades para a VM. O endereço IP privado está listado.

   - PowerShell - Use o exemplo para exibir uma lista de VMs e endereços de IP privados dos seus grupos de recursos. Você não precisa modificar esse exemplo antes de usá-lo.

     ```powershell
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

2. Verifique se você está conectado à sua rede virtual usando a conexão VPN ponto a site.
3. Abra a **Conexão de Área de Trabalho Remota**, digitando "RDP" ou "Conexão de Área de Trabalho Remota" na caixa de pesquisa na barra de tarefas e, em seguida, selecione a Conexão de Área de Trabalho Remota. Você também pode abrir a Conexão de Área de Trabalho Remota usando o comando 'mstsc' no PowerShell. 
4. Na Conexão de Área de Trabalho Remota, insira o endereço IP privado da VM. Você pode clicar em "Mostrar opções" para ajustar as configurações adicionais, em seguida, conectar-se.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Para solucionar problemas de uma conexão de RDP para uma VM

Se você estiver tendo problemas para se conectar a uma máquina virtual em sua conexão VPN, verifique o seguinte:

- Verifique se a conexão VPN é estabelecida.
- Verifique se você está se conectando ao endereço IP privado para a VM.
- Use 'ipconfig' para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador do qual está se conectando. Se o endereço IP está dentro do intervalo de endereços da VNet a que você está se conectando ou dentro do intervalo de endereços de seu VPNClientAddressPool, isso é chamado de espaço de endereço sobreposto. Quando o espaço de endereço se sobrepõe dessa forma, o tráfego de rede não alcança o Azure; ele permanece na rede local.
- Se você puder se conectar à VM usando o endereço IP privado, mas não o nome do computador, verifique se você configurou o DNS corretamente. Para obter mais informações sobre como funciona a resolução de nome para VMs, confira [Resolução de nomes para VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Verifique se o pacote de configuração de cliente VPN foi gerado depois que os endereços IP do servidor DNS foram especificados para a rede virtual. Se você atualizou os endereços IP do servidor DNS, gere e instale um novo pacote de configuração de cliente VPN.
- Para obter mais informações sobre conexões RDP, confira [Solucionar problemas de conexões da Área de Trabalho Remota a uma VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).