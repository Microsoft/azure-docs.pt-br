---
title: incluir arquivo
description: incluir arquivo
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4fc14083b0912cf18f98c229adcb297b89bc8971
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95563943"
---
Criar uma Conexão de Área de Trabalho Remota para conectar-se a uma VM implantada na sua VNet. A melhor maneira de verificar se você pode se conectar à sua VM é usando seu endereço IP privado em vez do nome do computador. Dessa forma, você está testando para ver se pode conectar-se e não se a resolução de nomes está configurada corretamente. 

1. Localize o endereço IP privado para sua VM. Para encontrar o endereço IP privado de uma VM, exiba as propriedades da VM no portal do Azure ou use o PowerShell.
2. Verifique se você está conectado à sua VNet usando a conexão VPN ponto a site. 
3. Para abrir a Conexão de Área de Trabalho Remota, digite *RDP* ou *Conexão de Área de Trabalho Remota* na caixa de pesquisa na barra de tarefas e, em seguida, selecione a **Conexão de Área de Trabalho Remota**. Também é possível abri-la usando o comando **mstsc** no PowerShell. 
3. Em **conexão de área de trabalho remota**, insira o endereço IP privado da VM. Se necessário, selecione **Mostrar opções** para ajustar as configurações adicionais e, em seguida, conectar-se.

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>Para solucionar problemas de uma conexão de RDP para uma VM

Se houver problemas para se conectar a uma máquina virtual em sua conexão VPN, há algumas coisas que você pode verificar. 

- Verifique se a conexão VPN é estabelecida.
- Verifique se você está se conectando ao endereço IP privado da VM.
- Insira **ipconfig** para verificar o endereço IPv4 atribuído ao adaptador Ethernet no computador do qual está se conectando. Se o endereço IP estiver dentro do intervalo de endereços da VNet a que você está se conectando ou dentro do intervalo de endereços de seu VPNClientAddressPool, isso será chamado de espaço de endereço sobreposto. Quando o espaço de endereço se sobrepõe dessa forma, o tráfego de rede não alcança o Azure; ele permanece na rede local.
- Se você puder se conectar à VM usando o endereço IP privado, mas não o nome do computador, verifique se você configurou o DNS corretamente. Para obter mais informações sobre como funciona a resolução de nome para VMs, confira [Resolução de nomes para VMs](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
- Verifique se o pacote de configuração de cliente VPN foi gerado depois que os endereços IP do servidor DNS foram especificados para a VNet. Se você atualizar os endereços IP do servidor DNS, gere e instale um novo pacote de configuração de cliente VPN.

Para obter mais informações sobre solução de problemas, confira [Solucionar problemas de conexões da Área de Trabalho Remota a uma VM](../articles/virtual-machines/troubleshooting/troubleshoot-rdp-connection.md).