---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5d631d8492ed1869bdc244e2cc90595183892822
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105719"
---
Você pode se conectar a uma VM que é implantada em sua rede virtual criando uma Conexão de Área de trabalho remota para a sua VM. É a melhor maneira de verificar inicialmente se você pode se conectar à sua VM usando seu endereço IP privado, em vez do nome do computador. Dessa forma, você está testando para ver se você pode se conectar, não se a resolução de nomes está configurada corretamente.

1. Localize o endereço IP privado. Você pode encontrar o endereço IP privado de uma VM consultando as propriedades para a VM no portal do Azure ou usando o PowerShell.

   * Porta do Azure - Localize sua máquina virtual do Windows no portal do Azure. Exiba as propriedades para a VM. O endereço IP privado está listado.

   * PowerShell - Use o exemplo para exibir uma lista de VMs e endereços de IP privados dos seus grupos de recursos. Você não precisa modificar esse exemplo antes de usá-lo.

     ```azurepowershell-interactive
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

1. Verifique se você está conectado à sua rede virtual usando a conexão VPN ponto a site.
1. Abra a **Conexão de Área de Trabalho Remota**, digitando "RDP" ou "Conexão de Área de Trabalho Remota" na caixa de pesquisa na barra de tarefas e, em seguida, selecione a Conexão de Área de Trabalho Remota. Você também pode abrir a Conexão de Área de Trabalho Remota usando o comando 'mstsc' no PowerShell. 
1. Na Conexão de Área de Trabalho Remota, insira o endereço IP privado da VM. Você pode clicar em "Mostrar opções" para ajustar as configurações adicionais, em seguida, conectar-se.

**Como solucionar problemas de conexão**

Se você estiver tendo problemas para se conectar a uma máquina virtual em sua conexão VPN, verifique o seguinte:

* Verifique se a conexão VPN é estabelecida.

* Verifique se você está se conectando ao endereço IP privado para a VM.

* Se você puder se conectar à VM usando o endereço IP privado, mas não o nome do computador, verifique se você configurou o DNS corretamente. Para obter mais informações sobre como funciona a resolução de nome para VMs, confira [Resolução de nomes para VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

* Para obter mais informações sobre conexões RDP, confira [Solucionar problemas de conexões da Área de Trabalho Remota a uma VM](/troubleshoot/azure/virtual-machines/troubleshoot-rdp-connection).