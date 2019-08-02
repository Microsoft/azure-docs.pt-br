---
title: Solução de problemas de SSH detalhada para uma VM do Azure | Microsoft Docs
description: Etapas da solução de problemas de SSH mais detalhada para problemas de conexão para uma máquina virtual do Azure
keywords: conexão ssh recusada, erro de ssh, ssh do azure, falha de conexão SSH
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 153117488cf94eb304eeb63ba6dca92a6c6ff27d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67696221"
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>Etapas detalhadas de solução de problemas de SSH para problemas ao se conectar a uma VM do Linux no Azure
Há muitas razões possíveis para que o cliente de SSH não possa acessar o serviço SSH na VM. Se tiver seguido as [etapas gerais de solução de problemas de SSH](troubleshoot-ssh-connection.md), será necessário solucionar o problema de conexão. Este artigo guiará você pelas etapas detalhadas de solução de problemas para determinar onde a conexão SSH está falhando e como resolver isso.

## <a name="take-preliminary-steps"></a>Realizar etapas preliminares
O diagrama a seguir mostra os componentes que estão envolvidos.

![Diagrama que mostra os componentes de serviço SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

As etapas a seguir ajudarão você a isolar a origem da falha e encontrar soluções ou soluções alternativas.

1. Verifique o status da VM no portal.
   No [Portal do Azure](https://portal.azure.com), selecione **Máquinas virtuais** > *Nome da VM*.

   O painel de status da VM deve mostrar **Executando**. Role para baixo para mostrar a atividade recente dos recursos de computação, armazenamento e rede.

2. Selecione **Configurações** para examinar os pontos de extremidade, os endereços IP, os grupos de segurança de rede e outras configurações.

   A VM deve ter um ponto de extremidade definido para o tráfego SSH que você pode exibir nos **Pontos de extremidade** ou no  **[Grupo de segurança de rede](../../virtual-network/security-overview.md)** . Os pontos de extremidade nas VMs que foram criados usando o Resource Manager são armazenados em um grupo de segurança de rede. Verifique se as regras foram aplicadas ao grupo de segurança de rede e se são referenciadas na sub-rede.

Para verificar a conectividade de rede, verifique os pontos de extremidade configurados e se você pode se conectar à VM por meio de outro protocolo, como HTTP ou outro serviço.

Após essas etapas, tente estabelecer novamente a conexão SSH.

## <a name="find-the-source-of-the-issue"></a>Descobrir a origem do problema
O cliente SSH no computador poderá não conseguir se conectar ao serviço SSH na VM do Azure devido aos problemas ou às configurações incorretas nas seguintes áreas:

* [Computador cliente de SSH](#source-1-ssh-client-computer)
* [Dispositivo de borda da organização](#source-2-organization-edge-device)
* [Ponto de extremidade de serviço de nuvem e ACL (lista de controle de acesso)](#source-3-cloud-service-endpoint-and-acl)
* [Grupos de segurança de rede](#source-4-network-security-groups)
* [VM baseada em Linux no Azure](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Fonte 1: Computador cliente de SSH
Para que o seu computador deixe de ser a fonte da falha, verifique se ele pode estabelecer conexões SSH com outro computador local baseado em Linux.

![Diagrama que realça os componentes do computador cliente SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Se a conexão falhar, verifique se os seguintes problemas ocorreram em seu computador:

* Uma configuração de firewall local que está bloqueando o tráfego SSH de entrada ou de saída (TCP 22)
* Um software de proxy cliente instalado localmente que está impedindo conexões de SSH
* Um software de monitoramento de rede instalado localmente que está impedindo conexões SSH
* Outros tipos de software de segurança que monitoram o tráfego ou que permitem/não permitem tipos específicos de tráfego

Se uma das seguintes condições se aplicar, desabilite temporariamente o software e tente novamente uma conexão SSH em um computador local para descobrir o motivo pelo qual a conexão está sendo bloqueada no seu computador. Em seguida, trabalhe com o administrador de rede para corrigir as configurações do software e permitir as conexões SSH.

Se você estiver usando autenticação de certificado, verifique se tem essas permissões para a pasta .ssh em seu diretório base:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*.pub
* Chmod 600 ~/.ssh/id_rsa (ou quaisquer outros arquivos que têm suas chaves privadas armazenadas)
* Chmod 644 ~/.ssh/known_hosts (contém os hosts aos quais você se conectou via SSH)

## <a name="source-2-organization-edge-device"></a>Fonte 2: Dispositivo de borda da organização
Para que o dispositivo de borda de sua organização deixe de ser a fonte da falha, verifique se um computador conectado diretamente à Internet pode estabelecer conexões SSH a sua VM do Azure. Se você estiver acessando a VM usando uma VPN de site a site ou uma conexão de ExpressRoute do Azure, vá para [fonte 4: Grupos de segurança de rede](#nsg).

![Diagrama que realça o dispositivo de borda da organização](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Se não tiver um computador que esteja diretamente conectado à Internet, crie uma nova VM do Azure em seu próprio grupo de recursos ou serviço de nuvem e use essa nova VM. Para saber mais, consulte [Criar uma máquina virtual que execute o Linux no Azure](../linux/quick-create-cli.md). Exclua o grupo de recursos ou a VM e o serviço de nuvem ao concluir o teste.

Se você puder criar uma conexão SSH com um computador conectado diretamente à Internet, verifique se há o seguinte no dispositivo de borda da organização:

* Um firewall interno que está bloqueando o tráfego SSH com a Internet
* Um servidor proxy que está impedindo conexões SSH
* Software de detecção de invasão ou de monitoramento de rede em execução em dispositivos em sua rede de borda que está impedindo conexões SSH

Trabalhe com o administrador da rede para corrigir as configurações dos dispositivos de borda de sua organização a fim de permitir o tráfego SSH com a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Fonte 3: Ponto de extremidade de serviço de nuvem e ACL
> [!NOTE]
> Essa fonte aplica-se somente às VMs que foram criadas usando o modelo de implantação clássico. Para VMs que foram criadas por meio do Resource Manager, vá para a [fonte 4: Grupos de segurança de rede](#nsg).

Para que o ponto de extremidade de serviço de nuvem e uma ACL deixem de ser a fonte da falha, verifique se outra VM do Azure na mesma rede virtual pode se conectar usando SSH.

![Diagrama que realça a ACL e o ponto de extremidade do serviço de nuvem](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Se não houver outra VM na mesma rede virtual, você poderá criar facilmente uma nova. Para saber mais, veja [Criar uma VM do Linux no Azure usando a CLI](../linux/quick-create-cli.md). Exclua a VM extra ao concluir o teste.

Se for possível criar uma conexão SSH com uma VM na mesma rede virtual, verifique as seguintes áreas:

* **A configuração do ponto de extremidade para o tráfego SSH na VM de destino.** A porta TCP particular do ponto de extremidade deve corresponder à porta TCP na qual o serviço SSH na VM está escutando. (A porta padrão é 22). Verifique o número da porta TCP do SSH no Portal do Azure selecionando **Máquinas virtuais** > *Nome da VM* > **Configurações** > **Pontos de extremidade**.
* **A ACL para o ponto de extremidade de tráfego de SSH na máquina virtual de destino.** Uma ACL permite que você especifique tráfego de entrada permitido ou negado da Internet com base em seu endereço IP de origem. ACLs configuradas incorretamente podem impedir o tráfego de SSH para o ponto de extremidade. Verifique suas ACLs para assegurar que o tráfego de entrada dos endereços IP públicos de seu proxy ou de outro servidor de borda é permitido. Para obter mais informações, veja [Sobre ACLs (listas de controle de acesso) de rede](../../virtual-network/virtual-networks-acl.md).

Para que o ponto de extremidade deixe de ser a fonte do problema, remova o ponto de extremidade atual, crie um novo e especifique o nome do SSH (porta TCP 22 como o número da porta pública e privada). Para obter mais informações, consulte [Configurar pontos de extremidade em uma máquina virtual no Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Fonte 4: Grupos de segurança de rede
Os grupos de segurança de rede proporcionam um controle mais granular do tráfego de entrada e de saída permitido. Você pode criar regras que abrangem sub-redes e serviços de nuvem em uma rede virtual do Azure. Examine as regras do grupo de segurança de rede para verificar se o tráfego SSH de entrada e saída da Internet é permitido.
Para saber mais, confira [Sobre grupos de segurança de rede](../../virtual-network/security-overview.md).

Você também pode usar a Verificação de IP para validar a configuração do NSG. Para saber mais, veja [Visão geral do monitoramento de rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Fonte 5: Máquina virtual baseada no Linux
A última fonte possível de problemas é a própria máquina virtual do Azure.

![Diagrama que realça uma VM do Azure baseada em Linux](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Se você ainda não fez isso, siga as instruções [para redefinir uma senha para máquinas virtuais baseadas em Linux](../linux/reset-password.md).

Tente se conectar novamente do seu computador. Caso ainda não consiga, veja a seguir alguns dos possíveis problemas:

* O serviço SSH não está em execução na máquina virtual de destino.
* O serviço SSH não está escutando na porta TCP 22. Para testar, instale um cliente telnet no computador local e execute "telnet *cloudServiceName*.cloudapp.net 22". Essa etapa determina se a máquina virtual permite a comunicação de entrada e de saída para o ponto de extremidade do SSH.
* O firewall local na máquina virtual de destino tem regras que estão impedindo o tráfego SSH de entrada ou de saída.
* Um software de detecção de invasão ou de monitoramento de rede em execução na máquina virtual do Azure está impedindo conexões de SSH.

## <a name="additional-resources"></a>Recursos adicionais
Para obter mais informações sobre como solucionar problemas de acesso do aplicativo, consulte [Solucionar problemas de acesso a um aplicativo executado em uma máquina virtual do Azure](../linux/troubleshoot-app-connection.md)
