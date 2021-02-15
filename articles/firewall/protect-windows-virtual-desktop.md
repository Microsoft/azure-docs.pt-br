---
title: Usar o Firewall do Azure para proteger a área de trabalho virtual do Windows
description: Saiba como usar o Firewall do Azure para proteger implantações de área de trabalho virtual do Windows
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 05/06/2020
ms.author: victorh
ms.openlocfilehash: ad4f42d0e33f6d70c75abfcd1daab4f5aa9a515f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94654976"
---
# <a name="use-azure-firewall-to-protect-window-virtual-desktop-deployments"></a>Usar o Firewall do Azure para proteger as implantações da Área de Trabalho Virtual do Windows

A área de trabalho virtual do Windows é um serviço de desktop e de virtualização de aplicativo que é executado no Azure. Quando um usuário final se conecta a um ambiente de área de trabalho virtual do Windows, sua sessão é executada por um pool de hosts. Um pool de hosts é uma coleção de máquinas virtuais do Azure que se registram na área de trabalho virtual do Windows como hosts de sessão. Essas máquinas virtuais são executadas em sua rede virtual e estão sujeitas aos controles de segurança de rede virtual. Eles precisam de acesso de Internet de saída ao serviço de área de trabalho virtual do Windows para operar corretamente e também podem precisar de acesso de saída à Internet para usuários finais. O Firewall do Azure pode ajudá-lo a bloquear seu ambiente e filtrar o tráfego de saída.

[Arquitetura de área de trabalho virtual do ![ ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) Windows ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Siga as diretrizes neste artigo para fornecer proteção adicional para o pool de hosts da área de trabalho virtual do Windows usando o Firewall do Azure.

## <a name="prerequisites"></a>Pré-requisitos


 - Um ambiente de área de trabalho virtual do Windows e pool de hosts implantados.

   Para obter mais informações, consulte [tutorial: criar um pool de hosts usando o Azure Marketplace](../virtual-desktop/create-host-pools-azure-marketplace.md) e [criar um pool de hosts com um modelo de Azure Resource Manager](../virtual-desktop/virtual-desktop-fall-2019/create-host-pools-arm-template.md).

Para saber mais sobre ambientes de área de trabalho virtual do Windows, consulte [ambiente de área de trabalho virtual do Windows](../virtual-desktop/environment-setup.md).

## <a name="host-pool-outbound-access-to-windows-virtual-desktop"></a>Acesso de saída do pool de hosts à área de trabalho virtual do Windows

As máquinas virtuais do Azure que você cria para a área de trabalho virtual do Windows devem ter acesso a vários FQDNs (nomes de domínio totalmente qualificados) para funcionar corretamente. O Firewall do Azure fornece uma marca de FQDN de área de trabalho virtual do Windows para simplificar essa configuração. Use as etapas a seguir para permitir o tráfego de saída da plataforma de área de trabalho virtual do Windows:

- Implante o Firewall do Azure e configure sua UDR (rota definida pelo usuário) da sub-rede do pool de hosts de área de trabalho virtual do Windows para rotear todo o tráfego pelo firewall do Azure. Sua rota padrão agora aponta para o firewall.
- Crie uma coleção de regras de aplicativo e adicione uma regra para habilitar a marca de FQDN *WindowsVirtualDesktop* . O intervalo de endereços IP de origem é a rede virtual do pool de hosts, o protocolo é **https** e o destino é **WindowsVirtualDesktop**.

- O conjunto de contas de armazenamento e barramento de serviço necessárias para o pool de hosts da área de trabalho virtual do Windows é específico da implantação, portanto, ele ainda não foi capturado na marca de FQDN WindowsVirtualDesktop. Você pode resolver isso de uma das seguintes maneiras:

   - Permitir acesso HTTPS de sua sub-rede do pool de hosts a * xt.blob.core.windows.net, * eh.servicebus.windows.net e * xt.table.core.windows.net. Esses FQDNs curinga habilitam o acesso necessário, mas são menos restritivos.
   - Use a seguinte consulta do log Analytics para listar os FQDNs exatos necessários e, em seguida, permiti-los explicitamente em suas regras de aplicativo de firewall:
   ```
   AzureDiagnostics
   | where Category == "AzureFirewallApplicationRule"
   | search "Deny"
   | search "gsm*eh.servicebus.windows.net" or "gsm*xt.blob.core.windows.net" or "gsm*xt.table.core.windows.net"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" *
   | project TimeGenerated,Protocol,FQDN
   ```

- Criar uma coleção de regras de rede Adicione as seguintes regras:

   - Permitir DNS – permitir tráfego de seu Adicionar endereço IP privado para * para portas TCP e UDP 53.
   - Permitir KMS – permitir o tráfego de suas máquinas virtuais da área de trabalho virtual do Windows para a porta TCP 1688 do serviço de ativação do Windows. Para obter mais informações sobre os endereços IP de destino, consulte [falha de ativação do Windows no cenário de túnel forçado](../virtual-machines/troubleshooting/custom-routes-enable-kms-activation.md#solution).

> [!NOTE]
> Algumas implantações podem não precisar de regras de DNS, por exemplo Azure Active Directory controladores de domínio encaminham consultas DNS para o DNS do Azure em 168.63.129.16.

## <a name="host-pool-outbound-access-to-the-internet"></a>Acesso de saída do pool de hosts à Internet

Dependendo das necessidades da sua organização, talvez você queira habilitar o acesso seguro à Internet de saída para seus usuários finais. Nos casos em que a lista de destinos permitidos é bem definida (por exemplo, [Microsoft 365 acesso](/microsoft-365/enterprise/microsoft-365-ip-web-service)), você pode usar o aplicativo de firewall do Azure e as regras de rede para configurar o acesso necessário. Isso roteia o tráfego do usuário final diretamente para a Internet para obter o melhor desempenho.

Se você quiser filtrar o tráfego de Internet do usuário de saída usando um gateway Web seguro local existente, poderá configurar navegadores da Web ou outros aplicativos em execução no pool de hosts da área de trabalho virtual do Windows com uma configuração de proxy explícita. Por exemplo, consulte [como usar as opções de linha de comando do Microsoft Edge para definir as configurações de proxy](/deployedge/edge-learnmore-cmdline-options-proxy-settings). Essas configurações de proxy influenciam apenas o acesso à Internet do usuário final, permitindo o tráfego de saída da plataforma de área de trabalho virtual do Windows diretamente por meio do firewall do Azure.

## <a name="additional-considerations"></a>Considerações adicionais

Talvez seja necessário configurar regras de firewall adicionais, dependendo dos seus requisitos:

- Acesso ao servidor NTP

   Por padrão, as máquinas virtuais que executam o Windows Connect para time.windows.com pela porta UDP 123 para sincronização de horário. Crie uma regra de rede para permitir esse acesso ou para um servidor de horário que você usa em seu ambiente.


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a área de trabalho virtual do Windows: [o que é área de trabalho virtual do Windows?](../virtual-desktop/overview.md)