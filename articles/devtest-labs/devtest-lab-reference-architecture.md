---
title: Arquitetura de referência empresarial para Azure DevTest Labs
description: Este artigo fornece diretrizes de arquitetura de referência para Azure DevTest Labs em uma empresa.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 29f739c2fb9dd1cc58bf6c400eeee1bebb6243c2
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328837"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Arquitetura de referência de Azure DevTest Labs para empresas
Este artigo fornece uma arquitetura de referência para ajudá-lo a implantar uma solução com base em Azure DevTest Labs em uma empresa. Ele inclui o seguinte:
- Conectividade local por meio do Azure ExpressRoute
- Um gateway de área de trabalho remota para entrar remotamente em máquinas virtuais
- Conectividade com um repositório de artefatos para artefatos privados
- Outros serviços de PaaS que são usados em laboratórios

![Diagrama de arquitetura de referência](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Arquitetura
Estes são os principais elementos da arquitetura de referência:

- **Azure Active Directory (AD do Azure)**: o DevTest Labs usa o [serviço do Azure ad para gerenciamento de identidade](../active-directory/fundamentals/active-directory-whatis.md). Considere esses dois aspectos principais ao conceder aos usuários acesso a um ambiente baseado em DevTest Labs:
    - **Gerenciamento de recursos**: fornece acesso ao portal do Azure para gerenciar recursos (criar máquinas virtuais; criar ambientes; iniciar, parar, reiniciar, excluir e aplicar artefatos; e assim por diante). O gerenciamento de recursos é feito usando o controle de acesso baseado em função do Azure (RBAC do Azure). Você atribui funções a usuários e define permissões de nível de acesso e recursos.
    - **Máquinas virtuais (nível de rede)**: na configuração padrão, as máquinas virtuais usam uma conta de administrador local. Se houver um domínio disponível ([Azure AD Domain Services](../active-directory-domain-services/overview.md), um domínio local ou um domínio baseado em nuvem), as máquinas poderão ser unidas ao domínio. Os usuários podem usar suas identidades baseadas em domínio para se conectarem às VMs.
- **Conectividade local**: em nosso diagrama de arquitetura, o [ExpressRoute](../expressroute/expressroute-introduction.md) é usado. Mas você também pode usar uma [VPN site a site](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Embora o ExpressRoute não seja necessário para o DevTest Labs, ele é comumente usado em empresas. O ExpressRoute só será necessário se você precisar de acesso aos recursos corporativos. Os cenários comuns são:
    - Você tem dados locais que não podem ser movidos para a nuvem.
    - Você prefere unir as máquinas virtuais do laboratório ao domínio local.
    - Você deseja forçar todo o tráfego de rede dentro e fora do ambiente de nuvem por meio de um firewall local para segurança/conformidade.
- **Grupos de segurança de rede**: uma maneira comum de restringir o tráfego para o ambiente de nuvem (ou dentro do ambiente de nuvem) com base em endereços IP de origem e de destino é usar um [grupo de segurança de rede](../virtual-network/network-security-groups-overview.md). Por exemplo, você deseja permitir apenas o tráfego originado da rede corporativa nas redes do laboratório.
- **Gateway de área de trabalho remota**: as empresas normalmente bloqueiam conexões de área de trabalho remota de saída no firewall corporativo. Há várias opções para habilitar a conectividade com o ambiente baseado em nuvem no DevTest Labs, incluindo:
  - Use um [Gateway de área de trabalho remota](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)e permita o endereço IP estático do balanceador de carga do gateway.
  - [Direcione todo o tráfego RDP de entrada](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) na conexão VPN ExpressRoute/site a site. Essa funcionalidade é uma consideração comum quando as empresas planejam uma implantação do DevTest Labs.
- **Serviços de rede (redes virtuais, sub-redes)**: a topologia de [rede do Azure](../networking/networking-overview.md) é outro elemento-chave na arquitetura do DevTest Labs. Ele controla se os recursos do laboratório podem se comunicar e ter acesso ao local e à Internet. Nosso diagrama de arquitetura inclui as maneiras mais comuns que os clientes usam o DevTest Labs: todos os laboratórios se conectam por meio de [emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md) usando um [modelo hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) para a conexão VPN ExpressRoute/site a site para o local. Mas o DevTest Labs usa a rede virtual do Azure diretamente, portanto, não há restrições sobre como você configura a infraestrutura de rede.
- **DevTest Labs**: o DevTest Labs é uma parte fundamental da arquitetura geral. Para saber mais sobre o serviço, consulte [sobre o DevTest Labs](devtest-lab-overview.md).
- **Máquinas virtuais e outros recursos (SaaS, PaaS, IaaS)**: as máquinas virtuais são uma carga de trabalho importante que o DevTest Labs dá suporte juntamente com outros recursos do Azure. O DevTest Labs torna fácil e rápido para uma empresa fornecer acesso aos recursos do Azure (incluindo VMs e outros recursos do Azure). Saiba mais sobre o acesso ao Azure para [desenvolvedores](devtest-lab-developer-lab.md) e [testadores](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Considerações sobre escalabilidade
Embora o DevTest Labs não tenha cotas ou limites internos, outros recursos do Azure que são usados na operação típica de um laboratório têm [cotas de nível de assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md). Portanto, em uma implantação empresarial típica, você precisa de várias assinaturas do Azure para abranger uma grande implantação do DevTest Labs. As cotas que as empresas mais acessam mais normalmente são:

- **Grupos de recursos**: na configuração padrão, o DevTest Labs cria um grupo de recursos para cada nova máquina virtual ou o usuário cria um ambiente usando o serviço. As assinaturas podem conter [até 980 grupos de recursos](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). Portanto, esse é o limite de máquinas virtuais e ambientes em uma assinatura. Há duas outras configurações que você deve considerar:
    - **[Todas as máquinas virtuais vão para o mesmo grupo de recursos](resource-group-control.md)**: embora essa configuração ajude a atender ao limite do grupo de recursos, ela afeta o limite do grupo de recursos por recurso.
    - **Usando IPS públicos compartilhados**: todas as VMs do mesmo tamanho e região entram no mesmo grupo de recursos. Essa configuração é uma "base intermediária" entre as cotas do grupo de recursos e as cotas de tipo de recurso por recurso-grupo, se as máquinas virtuais tiverem permissão para ter endereços IP públicos.
- **Recursos por grupo de recursos por tipo de recurso**: o limite padrão para [recursos por grupo de recursos por tipo de recurso é 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  Quando você usar *todas as VMs, vá para a mesma* configuração de grupo de recursos, os usuários atingirão esse limite de assinatura muito mais cedo, especialmente se as VMs tiverem muitos discos extras.
- **Contas de armazenamento**: um laboratório do DevTest Labs vem com uma conta de armazenamento. A cota do Azure para o [número de contas de armazenamento por região por assinatura é de 250](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). O número máximo de laboratórios de DevTest na mesma região também é 250.
- **Atribuições de função**: uma atribuição de função é como você dá a um usuário ou acesso de entidade de segurança a um recurso (proprietário, recurso, nível de permissão). No Azure, há um [limite de 2.000 atribuições de função por assinatura](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-role-based-access-control-limits). Por padrão, o serviço do DevTest Labs cria um grupo de recursos para cada VM. O proprietário recebe permissão de *proprietário* para a VM do DevTest Labs e a permissão de *leitor* para o grupo de recursos. Dessa forma, cada nova VM que você cria usa duas atribuições de função, além das atribuições que são usadas quando você concede aos usuários a permissão para o laboratório.
- **Leituras/gravações da API**: há várias maneiras de automatizar o Azure e o DevTest Labs, incluindo APIs REST, PowerShell, CLI do Azure e SDK do Azure. Por meio da automação, você pode atingir outro limite nas solicitações de API: cada assinatura permite até [12.000 solicitações de leitura e 1.200 solicitações de gravação por hora](../azure-resource-manager/management/request-limits-and-throttling.md). Esteja atento a esse limite ao automatizar os laboratórios de DevTest.

## <a name="manageability-considerations"></a>Considerações sobre capacidade de gerenciamento
O DevTest Labs tem uma ótima interface de usuário administrativa para trabalhar com um único laboratório. Mas, em uma empresa, você provavelmente tem várias assinaturas do Azure e muitos laboratórios. Fazer alterações de forma consistente para todos os seus laboratórios requer scripts/automação. Aqui estão alguns exemplos e melhores práticas de gerenciamento para uma implantação do DevTest Labs:

- **Alterações nas configurações do laboratório**: um cenário comum é atualizar uma configuração de laboratório específica em todos os laboratórios na implantação. Por exemplo, um novo tamanho de instância de VM está disponível e todos os laboratórios devem ser atualizados para permitir. É melhor automatizar essas alterações usando scripts do PowerShell, a CLI ou as APIs REST.  
- **Token de acesso pessoal do repositório de artefatos**: normalmente, os tokens de acesso pessoal para um repositório git expiram em 90 dias, um ano ou dois anos. Para garantir a continuidade, é importante estender o token de acesso pessoal ou criar um novo. Em seguida, use a automação para aplicar o novo token de acesso pessoal a todos os laboratórios.
- **Restringir alterações a uma configuração de laboratório**: muitas vezes, uma configuração específica deve ser restrita (como permitir o uso de imagens do Marketplace). Você pode usar Azure Policy para evitar alterações em um tipo de recurso. Ou você pode criar uma função personalizada e conceder aos usuários essa função em vez da função de *proprietário* do laboratório. Você pode fazer isso para a maioria das configurações no laboratório (suporte interno, anúncio de laboratório, tamanhos de VM permitidos e assim por diante).
- **Exigir que as VMs sigam uma Convenção de nomenclatura**: normalmente, os gerentes desejam identificar facilmente as VMs que fazem parte de um ambiente de teste e desenvolvimento baseado em nuvem. Você pode fazer isso usando [Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

É importante observar que o DevTest Labs usa os recursos subjacentes do Azure que são gerenciados da mesma maneira: rede, discos, computação e assim por diante. Por exemplo, Azure Policy se aplica a máquinas virtuais que são criadas em um laboratório. A central de segurança do Azure pode relatar a conformidade da VM. E o serviço de backup do Azure pode fornecer backups regulares para as VMs no laboratório.

## <a name="security-considerations"></a>Considerações de segurança
Azure DevTest Labs usa recursos existentes no Azure (computação, rede e assim por diante). Portanto, ele se beneficia automaticamente com os recursos de segurança criados na plataforma. Por exemplo, para exigir que as conexões de área de trabalho remota de entrada sejam originadas somente da rede corporativa, basta adicionar um grupo de segurança de rede à rede virtual no gateway de área de trabalho remota. A única consideração de segurança adicional é o nível de permissões que você concede aos membros da equipe que usam os laboratórios diariamente. As permissões mais comuns são [ *proprietário* e *usuário*](devtest-lab-add-devtest-user.md). Para obter mais informações sobre essas funções, consulte [Adicionar proprietários e usuários no Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Próximas etapas
Veja o próximo artigo desta série: [escalar verticalmente sua infraestrutura de Azure DevTest Labs](devtest-lab-guidance-scale.md).