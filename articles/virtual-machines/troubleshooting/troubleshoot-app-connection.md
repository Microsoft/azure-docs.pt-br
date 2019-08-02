---
title: Solucionar problemas de acesso ao aplicativo da VM no Azure | Microsoft Docs
description: Use essas etapas detalhadas de solução de problemas para isolar problemas ao conectar-se aos aplicativos executados em máquinas virtuais no Azure.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: não é possível iniciar o aplicativo, programa não abre, porta de escuta bloqueada, não é possível iniciar o programa, porta de escuta bloqueada
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 9bc528cdd098a2e355c542c3ca8f9bcb0287f339
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67710516"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Solucionar problemas de conectividade do aplicativo em máquinas virtuais no Azure

Há vários motivos pelos quais não é possível iniciar ou conectar-se a um aplicativo em execução em uma VM (máquina virtual) do Azure. Entre eles estão o aplicativo não estar em execução ou não estar escutando nas portas esperadas, a porta de escuta estar bloqueada ou as regras de rede não passarem corretamente o tráfego para o aplicativo. Este artigo descreve uma abordagem metódica para localizar e corrigir o problema.

Se você estiver tendo problemas para se conectar à sua VM usando RDP ou SSH, veja um dos seguintes artigos primeiro:

* [Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure baseada no Windows](troubleshoot-rdp-connection.md)
* [Solucionar problemas de conexões SSH (Secure Shell) para uma máquina virtual do Azure baseada em Linux](troubleshoot-ssh-connection.md).

Se você precisar de mais ajuda em qualquer momento neste artigo, você pode contatar os especialistas do Azure nos [fóruns do Azure MSDN e Excedente de Pilha](https://azure.microsoft.com/support/forums/). Como alternativa, você também pode registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.

## <a name="quick-start-troubleshooting-steps"></a>Etapas de solução de problemas de início rápido
Se você tiver problemas para se conectar a um aplicativo, experimente as etapas de solução de problemas gerais a seguir. Após cada etapa, tente se conectar ao seu aplicativo novamente:

* Reiniciar a máquina virtual
* Recrie o ponto de extremidade/as regras de firewall /as regras de NSG (grupo de segurança de rede)
  * [Modelo do Resource Manager: gerenciar Grupos de Segurança de Rede](../../virtual-network/manage-network-security-group.md)
  * [Modelo clássico: gerenciar pontos de extremidade dos Serviços de Nuvem](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Conectar-se de um local diferente, como uma rede virtual do Azure diferente
* Reimplantar a máquina virtual
  * [Reimplantar VM do Windows](redeploy-to-new-node-windows.md)
  * [Reimplantar VM Linux](redeploy-to-new-node-linux.md)
* Recriar a máquina virtual

Para obter mais informações, consulte [Solução de problemas de conectividade de ponto de extremidade (RDP/SSH/HTTP, falhas etc.)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).

## <a name="detailed-troubleshooting-overview"></a>Visão geral detalhada da solução de problemas
Há quatro áreas principais nas quais é possível solucionar problemas de acesso de um aplicativo que está sendo executado em uma máquina virtual do Azure.

![solucionar o problema de incapacidade de iniciar o aplicativo](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. O aplicativo em execução na máquina virtual do Azure.
   * O próprio aplicativo está sendo executado corretamente?
2. A máquina virtual do Azure.
   * É a própria máquina virtual que está sendo executada corretamente e respondendo às solicitações?
3. Pontos de extremidade de rede do Azure.
   * Pontos de extremidade de serviço de nuvem para máquinas virtuais no modelo de implantação clássico.
   * Grupos de segurança de rede e regras NAT de entrada para máquinas virtuais no modelo de implantação do Resource Manager.
   * O tráfego pode fluir dos usuários para a VM/aplicativo tráfego fluxo dos usuários para o aplicativo/VMs nas portas esperados?
4. Seu dispositivo de borda da Internet.
   * As regras de firewall estão impedindo o tráfego de fluir corretamente?

Para computadores cliente que acessam o aplicativo em uma conexão VPN site a site ou do ExpressRoute, as principais áreas que podem causar problemas são o aplicativo e a máquina virtual do Azure.

Para determinar a origem do problema e sua correção, siga estas etapas.

## <a name="step-1-access-application-from-target-vm"></a>Etapa 1: Aplicativo de acesso da VM de destino
Tente acessar o aplicativo com o programa cliente apropriado na VM em que ele está sendo executado. Use o nome de host local, o endereço IP local ou o endereço de loopback (127.0.0.1).

![iniciar o aplicativo diretamente da VM](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Por exemplo, se o aplicativo for um servidor Web, abra um navegador na VM e tente acessar uma página da Web hospedada na VM.

Se você conseguir acessar o aplicativo, vá para a [Etapa 2](#step2).

Se não conseguir acessar o aplicativo, verifique as seguintes configurações:

* O aplicativo está em execução na máquina virtual de destino.
* O aplicativo está escutando nas portas TCP e UDP esperadas.

Em máquinas virtuais baseadas em Linux e Windows, use o comando **netstat -a** para mostrar as portas de escuta ativas. Examine a saída para as portas esperadas no qual seu aplicativo deve estar escutando. Reinicie o aplicativo ou configure-o para usar as portas esperadas, como necessário e tente acessar o aplicativo localmente outra vez.

## <a id="step2"></a>Etapa 2: Aplicativo de acesso de outra VM na mesma rede virtual
Tente acessar o aplicativo de uma VM diferente, mas na mesma rede virtual, usando o nome de host da VM ou seu endereço IP público, privado ou do provedor atribuído ao Azure. Em máquinas virtuais criadas usando o modelo de implantação clássica, não use o endereço IP público do serviço de nuvem.

![Iniciar o aplicativo de uma VM diferente](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Por exemplo, se o aplicativo for um servidor Web, tente acessar uma página da Web em um navegador em outra VM na mesma rede virtual.

Se você conseguir acessar o aplicativo, vá para a [Etapa 3](#step3).

Se não conseguir acessar o aplicativo, verifique as seguintes configurações:

* O firewall do host na VM de destino está permitindo o tráfego de solicitação de entrada e de resposta de saída.
* O software de detecção de invasão ou de monitoramento de rede em execução na VM de destino está permitindo o tráfego.
* Os pontos de extremidade dos Serviços de Nuvem ou os Grupos de Segurança de Rede estão permitindo o tráfego:
  * [Modelo clássico: gerenciar pontos de extremidade dos Serviços de Nuvem](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Modelo do Resource Manager: gerenciar Grupos de Segurança de Rede](../../virtual-network/manage-network-security-group.md)
* Um componente separado em execução na VM, no caminho entre a VM de teste e a sua VM, como um balanceador de carga ou firewall, está permitindo o tráfego.

Em uma máquina virtual baseada no Windows, use o Firewall do Windows com Segurança avançada para determinar se as regras de firewall excluem o tráfego de entrada e de saída do seu aplicativo.

## <a id="step3"></a>Etapa 3: Aplicativo de acesso de fora da rede virtual
Tente acessar o aplicativo em um computador fora da rede virtual em que está a VM na qual o aplicativo está sendo executado. Use uma rede diferente do computador cliente original.

![Iniciar o aplicativo em um computador fora da rede virtual](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Por exemplo, se o aplicativo for um servidor Web, tente acessar uma página da Web em um navegador em execução em um computador que não está na rede virtual.

Se não conseguir acessar o aplicativo, verifique as seguintes configurações:

* Para VMs criadas com o modelo de implantação clássico:
  
  * Verifique se a configuração do ponto de extremidade para a VM está permitindo o tráfego de entrada, especialmente o protocolo (TCP ou UDP) e os números de porta pública e privada.
  * Verifique se essas ACLs (listas de controle de acesso) no ponto de extremidade não estão impedindo o tráfego vindo da Internet.
  * Para saber mais, consulte [Como configurar pontos de extremidade para uma máquina virtual](../windows/classic/setup-endpoints.md).
* Para VMs criadas com o modelo de implantação do Resource Manager:
  
  * Verifique se a configuração da regra NAT de entrada para a VM está permitindo o tráfego de entrada, especialmente o protocolo (TCP ou UDP) e os números de porta pública e privada.
  * Verifique se os Grupos de Segurança de Rede permitem o tráfego de saída de respostar e de entrada de solicitações.
  * Para obter mais informações, confira [O que é um grupo de segurança de rede?](../../virtual-network/security-overview.md)

Se a máquina virtual ou ponto de extremidade for um membro de um conjunto com balanceamento de carga:

* Verifique se o protocolo de teste (TCP ou UDP) e o número da porta estão corretos.
* Se a porta e protocolo de teste forem diferentes do protocolo e da porta configurados com balanceamento de carga:
  * Verifique se o aplicativo está escutando no protocolo de investigação (TCP ou UDP) e no número da porta (use **netstat –a** na VM de destino).
  * Verifique se o firewall do host na VM de destino está permitindo o tráfego de solicitação de investigação de entrada e de resposta de investigação de saída.

Se você puder acessar o aplicativo, certifique-se de que seu dispositivo de borda de Internet esteja permitindo:

* O tráfego de solicitação de saída do aplicativo no computador cliente para a máquina virtual do Azure.
* O tráfego de resposta do aplicativo de entrada da máquina virtual do Azure.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Etapa 4 Se você não puder acessar o aplicativo, use o IP Verify para verificar as configurações. 

Para saber mais, veja [Visão geral do monitoramento de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="additional-resources"></a>Recursos adicionais
[Solucionar problemas de conexões de Área de Trabalho Remota para uma máquina virtual do Azure baseada no Windows](troubleshoot-rdp-connection.md)

[Solucionar problemas de conexões SSH (Secure Shell) para uma máquina virtual do Azure baseada em Linux](troubleshoot-ssh-connection.md)


