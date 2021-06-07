---
title: Noções básicas sobre o acesso à máquina virtual just-in-time na central de segurança do Azure
description: Este documento explica como o acesso just-in-time à VM na central de segurança do Azure ajuda você a controlar o acesso às máquinas virtuais do Azure
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 9a52596aa0dd5fa7b9a7226d2ae57259dab08d37
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93285725"
---
# <a name="understanding-just-in-time-jit-vm-access"></a>Noções básicas sobre acesso à VM JIT (just-in-time)

Esta página explica os princípios por trás do recurso de acesso à VM JIT (just-in-time) da central de segurança do Azure e a lógica por trás da recomendação.

Para saber como aplicar o JIT às suas VMs usando o portal do Azure (a central de segurança ou as máquinas virtuais do Azure) ou programaticamente, consulte [como proteger suas portas de gerenciamento com o JIT](security-center-just-in-time.md).


## <a name="the-risk-of-open-management-ports-on-a-virtual-machine"></a>O risco de abrir portas de gerenciamento em uma máquina virtual

Os atores de ameaças buscam ativamente computadores acessíveis com portas de gerenciamento abertas, como RDP ou SSH. Todas as suas máquinas virtuais são alvos potenciais de um ataque. Quando uma VM é comprometida com êxito, ela é usada como o ponto de entrada para atacar outros recursos no seu ambiente.



## <a name="why-jit-vm-access-is-the-solution"></a>Por que o acesso à VM JIT é a solução 

Assim como acontece com todas as técnicas de prevenção de segurança cibernética, seu objetivo deve ser reduzir a superfície de ataque. Nesse caso, isso significa ter menos portas abertas, especialmente portas de gerenciamento.

Os usuários legítimos também usam essas portas, portanto, não é prático mantê-las fechadas.

Para resolver esse dilema, a central de segurança do Azure oferece JIT. Com o JIT, você pode bloquear o tráfego de entrada para suas VMs, reduzindo a exposição a ataques e, ao mesmo tempo, fornecendo acesso fácil para se conectar às VMs quando necessário.



## <a name="how-jit-operates-with-network-security-groups-and-azure-firewall"></a>Como o JIT opera com grupos de segurança de rede e o Firewall do Azure

Ao habilitar o acesso just-in-time à VM, você pode selecionar as portas na VM para a qual o tráfego de entrada será bloqueado. A central de segurança garante que as regras "negar todo o tráfego de entrada" existem para as portas selecionadas no [grupo de segurança de rede](../virtual-network/network-security-groups-overview.md#security-rules) (NSG) e nas regras de [Firewall do Azure](../firewall/rule-processing.md). Essas regras restringem o acesso às portas de gerenciamento das VMs do Azure e as defendem contra ataques. 

Se outras regras já existirem para as portas selecionadas, essas regras existentes têm prioridade sobre as novas regras "negar todo o tráfego de entrada". Se não houver nenhuma regra existente nas portas selecionadas, as novas regras têm a prioridade mais alta no NSG e no firewall do Azure.

Quando um usuário solicita acesso a uma VM, a central de segurança verifica se o usuário tem permissões do Azure [RBAC (controle de acesso baseado em função)](../role-based-access-control/role-assignments-portal.md) para essa VM. Se a solicitação for aprovada, a central de segurança configurará o NSGs e o Firewall do Azure para permitir o tráfego de entrada para as portas selecionadas do endereço IP (ou intervalo) relevante, para o período de tempo especificado. Depois que o tempo expirar, a Central de Segurança restaura os NSGs aos seus estados anteriores. As conexões que já estão estabelecidas não são interrompidas.

> [!NOTE]
> O JIT não oferece suporte a VMs protegidas por firewalls do Azure controlados pelo [Gerenciador de firewall do Azure](../firewall-manager/overview.md).




## <a name="how-security-center-identifies-which-vms-should-have-jit-applied"></a>Como a central de segurança identifica quais VMs devem ter o JIT aplicado

O diagrama a seguir mostra a lógica que a central de segurança aplica ao decidir como categorizar suas VMs com suporte: 

[![Fluxo lógico de máquina virtual (VM) just-in-time (JIT)](media/just-in-time-explained/jit-logic-flow.png)](media/just-in-time-explained/jit-logic-flow.png#lightbox)

Quando a central de segurança encontra um computador que pode se beneficiar do JIT, ela adiciona esse computador à guia de **recursos não íntegros** da recomendação. 

![Recomendação de acesso de máquina virtual (VM) just-in-time (JIT)](./media/just-in-time-explained/unhealthy-resources.png)


## <a name="faq---questions-about-just-in-time-virtual-machine-access"></a>Perguntas frequentes-perguntas sobre o acesso à máquina virtual just in time

### <a name="what-permissions-are-needed-to-configure-and-use-jit"></a>Quais permissões são necessárias para configurar e usar o JIT?

O JIT requer que o [Azure defender para servidores](defender-for-servers-introduction.md) seja habilitado na assinatura. 

As funções **leitor** e **SecurityReader** podem exibir o status e os parâmetros do JIT.

Se você quiser criar funções personalizadas que possam funcionar com o JIT, precisará dos detalhes da tabela a seguir.

> [!TIP]
> Para criar uma função com privilégios mínimos para usuários que precisam solicitar acesso JIT a uma VM e não executar outras operações JIT, use o [script set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) das páginas da Comunidade do GitHub da central de segurança.

| Para permitir que um usuário: | Permissões a serem definidas|
| --- | --- |
|Configurar ou editar uma política JIT para uma VM | *Atribua essas ações à função:*  <ul><li>No escopo de uma assinatura ou grupo de recursos associado à VM:<br/> `Microsoft.Security/locations/jitNetworkAccessPolicies/write` </li><li> No escopo de uma assinatura ou grupo de recursos da VM: <br/>`Microsoft.Compute/virtualMachines/write`</li></ul> | 
|Solicitar acesso JIT a uma VM | *Atribua essas ações ao usuário:*  <ul><li>No escopo de uma assinatura ou grupo de recursos associado à VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action` </li><li>No escopo de uma assinatura ou grupo de recursos associado à VM:<br/>  `Microsoft.Security/locations/jitNetworkAccessPolicies/*/read` </li><li>  No escopo de uma assinatura ou grupo de recursos ou VM:<br/> `Microsoft.Compute/virtualMachines/read` </li><li>  No escopo de uma assinatura ou grupo de recursos ou VM:<br/> `Microsoft.Network/networkInterfaces/*/read` </li></ul>|
|Ler políticas JIT| *Atribua essas ações ao usuário:*  <ul><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/read`</li><li>`Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action`</li><li>`Microsoft.Security/policies/read`</li><li>`Microsoft.Security/pricings/read`</li><li>`Microsoft.Compute/virtualMachines/read`</li><li>`Microsoft.Network/*/read`</li>|
|||





## <a name="next-steps"></a>Próximas etapas

Esta página explicou o _motivo pelo qual_ o acesso à VM (just-in-time) (máquina virtual) JIT deve ser usado. 

Avance para o artigo de instruções para saber mais sobre como habilitar o JIT e solicitar acesso às suas VMs habilitadas para o JIT:

> [!div class="nextstepaction"]
> [Como proteger suas portas de gerenciamento com o JIT](security-center-just-in-time.md)