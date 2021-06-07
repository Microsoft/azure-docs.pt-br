---
title: Como criar um laboratório com um recurso compartilhado | Azure Lab Services
description: Saiba como criar um laboratório que exige um recurso compartilhado entre os alunos.
author: emaher
ms.topic: article
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: d4bf4127dc163bb5f034e077b84664828374ba87
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647963"
---
# <a name="how-to-create-a-lab-with-a-shared-resource-in-azure-lab-services"></a>Como criar um laboratório com um recurso compartilhado no Azure Lab Services

Às vezes, ao criar um laboratório de sala de aula, pode haver alguns recursos que precisam ser compartilhados entre todos os alunos em um laboratório.  Por exemplo, você tem um servidor de licenciamento ou SQL Server para uma classe de banco de dados.  Este artigo abordará as etapas para habilitar o recurso compartilhado para um laboratório.  Também falaremos sobre como limitar o acesso a esse recurso compartilhado.

## <a name="architecture"></a>Arquitetura

Conforme mostrado no diagrama a seguir, teremos uma conta de laboratório com um laboratório.  A conta de laboratório terá as configurações de emparelhamento vnet para que a rede virtual do laboratório esteja conectada à rede do recurso compartilhado.  No diagrama abaixo, há duas redes virtuais com intervalos de IP não sobrepostos.  Esses intervalos de IP são apenas intervalos de exemplo.  Observe também que a rede virtual de recursos compartilhados está na mesma assinatura que a conta do laboratório.

![Serviços de laboratório com arquitetura de recursos compartilhados](./media/how-to-create-a-lab-with-shared-resource/shared-resource-architecture.png)

## <a name="setup-shared-resource"></a>Configurar recurso compartilhado

A rede virtual para o recurso compartilhado deve ser criada antes de o laboratório ser criado.  Para obter mais informações sobre como criar uma rede virtual, consulte [criar uma rede virtual](../virtual-network/quick-create-portal.md).  O planejamento de intervalos de rede virtual para que eles não se sobreponham ao endereço IP das máquinas de laboratório é importante.  Para obter mais informações sobre como planejar sua rede, consulte o artigo [planejar redes virtuais](../virtual-network/virtual-network-vnet-plan-design-arm.md) . Em nosso exemplo, o recurso compartilhado está em uma rede virtual com o intervalo de 10.2.0.0/16.  Se ainda não tiver feito isso, [crie uma sub-rede](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) para manter o recurso compartilhado.  No exemplo, usamos o intervalo 10.2.0.0/24, mas seu intervalo pode ser diferente dependendo das necessidades da sua rede.

O recurso compartilhado pode ser o software em execução em uma máquina virtual ou um serviço fornecido pelo Azure. O recurso compartilhado deve estar disponível por meio do endereço IP privado.  Ao disponibilizar o recurso compartilhado somente por meio de IP privado, você limita o acesso a esse recurso compartilhado.

O diagrama também mostra um NSG (grupo de segurança de rede) que pode ser usado para restringir o tráfego proveniente da VM do aluno.  Por exemplo, você pode escrever uma regra de segurança que declara que o tráfego dos endereços IP da VM do aluno só pode acessar um recurso compartilhado e nada mais.  Para obter mais informações sobre como definir regras de segurança, consulte [gerenciar grupo de segurança de rede](../virtual-network/manage-network-security-group.md#work-with-security-rules). Se você quiser restringir o acesso a um recurso compartilhado para um laboratório específico, obtenha o endereço IP do laboratório nas [configurações do laboratório da conta do laboratório](manage-labs.md#view-labs-in-a-lab-account) e defina uma regra de entrada para permitir o acesso somente desse endereço IP.  Não se esqueça de permitir as portas 49152 a 65535 para esse endereço IP.  Opcionalmente, você pode encontrar o endereço IP privado das VMs dos alunos usando a [página pool de máquinas virtuais](how-to-set-virtual-machine-passwords.md).

Se o recurso compartilhado for uma máquina virtual do Azure que executa o software necessário, talvez seja necessário modificar as regras de firewall padrão para a máquina virtual.

### <a name="tips-for-shared-resources---license-server"></a>Dicas para recursos compartilhados-servidor de licenças
Um dos recursos compartilhados mais comuns é um servidor de licença, aqui estão algumas dicas sobre como obter êxito com a configuração de um.
#### <a name="server-region"></a>Região do servidor
O servidor de licença precisará estar conectado à rede virtual que está emparelhada com o laboratório, portanto, o servidor de licença precisa estar localizado na mesma região que a conta do laboratório.

#### <a name="static-private-ip-and-mac-address"></a>IP privado estático e endereço MAC
Por padrão, as máquinas virtuais têm um IP privado dinâmico, [antes de configurar qualquer software para definir o IP privado como estático](../virtual-network/virtual-networks-static-private-ip-arm-pportal.md). Isso define o IP privado e o endereço MAC como estático.  

#### <a name="control-access"></a>Controlar Acesso
O controle do acesso ao servidor de licença é fundamental.  Depois que a VM for configurada, o acesso ainda será necessário para manutenção, solução de problemas e atualização.  Aqui estão algumas maneiras diferentes de fazer isso.
- [Configuração do acesso JIT (just in time) na central de segurança do Azure.](../security-center/security-center-just-in-time.md?tabs=jit-config-asc%252cjit-request-asc)
- [Configurar um grupo de segurança de rede para restringir o acesso.](../virtual-network/network-security-groups-overview.md)
- [Configuração de bastiões para permitir acesso seguro ao servidor de licença.](https://azure.microsoft.com/services/azure-bastion/)

## <a name="lab-account"></a>Conta de laboratório

Para usar um recurso compartilhado, a conta de laboratório deve ser configurada para usar uma [rede virtual emparelhada](how-to-connect-peer-virtual-network.md).  Nesse caso, iremos emparelhar para a rede virtual que contém o recurso compartilhado.

>[!WARNING]
>O laboratório da sua classe deve ser criado **depois** que a conta de laboratório estiver emparelhada com a rede virtual de recursos compartilhados.  
Computador de modelo

Depois que sua conta de laboratório estiver emparelhada com a rede virtual, o computador de modelo agora deverá ter acesso ao recurso compartilhado.  Talvez seja necessário atualizar as regras de firewall, dependendo do recurso compartilhado que está sendo acessado.