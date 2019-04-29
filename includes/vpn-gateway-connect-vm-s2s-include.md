---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 02/01/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 820a6a4da9f5c466e694f247d09393474d8464ee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60407479"
---
Você pode se conectar a uma VM que é implantada em sua rede virtual criando uma Conexão de Área de trabalho remota para a sua VM. É a melhor maneira de verificar inicialmente se você pode se conectar à sua VM usando seu endereço IP privado, em vez do nome do computador. Dessa forma, você está testando para ver se você pode se conectar, não se a resolução de nomes está configurada corretamente.

1. Localize o endereço IP privado. Você pode encontrar o endereço IP privado de uma VM de várias maneiras. A seguir, mostramos as etapas para o portal do Azure e para o PowerShell.

   - Porta do Azure - Localize sua máquina virtual do Windows no portal do Azure. Exiba as propriedades para a VM. O endereço IP privado está listado.

   - PowerShell - Use o exemplo para exibir uma lista de VMs e endereços de IP privados dos seus grupos de recursos. Você não precisa modificar esse exemplo antes de usá-lo.

     ```azurepowershell
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

2. Verifique se você está conectado à sua rede virtual usando a conexão VPN.
3. Abra a **Conexão de Área de Trabalho Remota**, digitando "RDP" ou "Conexão de Área de Trabalho Remota" na caixa de pesquisa na barra de tarefas e, em seguida, selecione a Conexão de Área de Trabalho Remota. Você também pode abrir a Conexão de Área de Trabalho Remota usando o comando 'mstsc' no PowerShell. 
4. Na Conexão de Área de Trabalho Remota, insira o endereço IP privado da VM. Você pode clicar em "Mostrar opções" para ajustar as configurações adicionais, em seguida, conectar-se.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Para solucionar problemas de uma conexão de RDP para uma VM

Se você estiver tendo problemas para se conectar a uma máquina virtual em sua conexão VPN, verifique o seguinte:

- Verifique se a conexão VPN é estabelecida.
- Verifique se você está se conectando ao endereço IP privado para a VM.
- Se você puder se conectar à VM usando o endereço IP privado, mas não o nome do computador, verifique se você configurou o DNS corretamente. Para obter mais informações sobre como funciona a resolução de nome para VMs, confira [Resolução de nomes para VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Para obter mais informações sobre conexões RDP, confira [Solucionar problemas de conexões da Área de Trabalho Remota a uma VM](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).
