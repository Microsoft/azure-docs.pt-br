---
title: Arquitetura de referência para o Azure DevTest Labs para uma empresa
description: Este artigo fornece uma orientação de arquitetura de referência para o Azure DevTest Labs em uma empresa.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: bcb154f7cffb92ef23fc2606e1f604bb12f8d1a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60561500"
---
# <a name="azure-devtest-labs---reference-architecture-for-an-enterprise"></a>O Azure DevTest Labs - arquitetura de referência para uma empresa
Este artigo fornece uma arquitetura de referência para a implantação de uma solução baseada no Azure DevTest Labs em uma empresa. Ele inclui a conectividade local por meio da rota expressa, um gateway de área de trabalho remota para entrar remotamente em máquinas virtuais, a conectividade a um repositório de artefato para artefatos particulares e outros serviços de PaaS usados em um laboratório.

![Arquitetura de referência](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Arquitetura
Os elementos principais na arquitetura de referência são:

- **O Azure Active Directory (AAD)**: O Azure DevTest Labs usa o [serviço do Azure Active Directory para gerenciamento de identidades](../active-directory/fundamentals/active-directory-whatis.md). Há duas coisas importantes a considerar no fornecimento de acesso a um ambiente com base no DevTest Labs para usuários:
    - **Gerenciamento de recursos**:  Ele fornece acesso ao portal do Azure para gerenciar os recursos (criar máquinas virtuais, criar ambientes, iniciar/parar/reiniciar/delete/applyartifacts e assim por diante). Isso é feito no Azure usando o controle de acesso baseado em Roble (RBAC) e aplicando uma atribuição de função para o usuário, o recurso de configuração e permissões em nível de acesso.
    - **Máquinas virtuais (nível de rede)**:  Na configuração padrão, máquinas virtuais usam uma conta de administrador local.  Se não houver um domínio ([serviços de domínio do AAD](../active-directory-domain-services/active-directory-ds-overview.md), um domínio local ou um domínio baseado em nuvem), máquinas podem ser unidas ao domínio. Depois de ingressar, usuários usem suas identidades baseados em domínio para se conectar às VMs.
- **Conectividade local**: No diagrama acima, arquitetura [Express Route](../expressroute/expressroute-introduction.md) i usado, mas você também pode usar um [VPN Site a Site](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Embora não seja necessário para os laboratórios de desenvolvimento/teste, ele é visto comumente em empresas. É necessário somente se há um motivo para se conectar aos recursos corporativos. Motivos comuns são: 
    - Dados locais que não podem ser movidos para a nuvem
    - Preferência para ingressar máquinas virtuais do laboratório no domínio local
    - Forçar todo o tráfego de rede que entra e sai do ambiente de nuvem por meio de um firewall local por motivos de segurança/conformidade
- **Grupos de segurança de rede**: Uma maneira comum para restringir o tráfego para o ambiente de nuvem (ou dentro do ambiente de nuvem) com base no código-fonte e endereços IP de destino é usar um [grupo de segurança de rede](../virtual-network/security-overview.md). Por exemplo, permitindo que somente o tráfego de rede proveniente da rede corporativa em redes do laboratório.
- **O gateway de área de trabalho remota**:  As empresas normalmente bloqueiam conexões da área de trabalho remotas de saída no firewall corporativo. Para habilitar a conectividade com o ambiente baseado em nuvem no DevTest Labs, há várias opções, como usar um [gateway de área de trabalho remota](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture) (lista de permissões de IP estático para o balanceador de carga de gateway) ou [direcionando todas as entrada O tráfego de RDP](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) sobre a conexão de VPN do Express Route/Site a Site. É uma consideração comum ao planejar uma implantação do DevTest Labs na empresa.
- **A rede do Azure (redes virtuais, sub-redes)**:  O [a rede do Azure](../networking/networking-overview.md) topologia é outro elemento essencial na arquitetura geral do DevTest Labs. Ele permite que os recursos de laboratórios para se comunicar (ou não), acesso ao local (ou não) e o acesso à internet (ou não). O diagrama da arquitetura inclui a maneira mais comum que os clientes estão usando o DevTest Labs (conectados por meio de todos os laboratórios [emparelhamento de VNet](../virtual-network/virtual-network-peering-overview.md) usando uma [modelo hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) para a conexão de VPN do Express Route/Site a Site para o local), mas, desde o DevTest Labs usa a rede do Azure diretamente não existem quaisquer restrições sobre como configurar a infraestrutura de rede.
- **DevTest Labs**:  DevTest Labs é uma parte fundamental da arquitetura geral. Para saber mais sobre o serviço, consulte [sobre o DevTest Labs](devtest-lab-overview.md).
- **Máquinas virtuais e outros recursos (SaaS, PaaS, IaaS)**:  Uma das principais cargas de trabalho com suporte pelo DevTest Labs são máquinas virtuais junto com outros recursos do Azure.  Laboratórios de desenvolvimento/teste torna fácil e rápida para uma empresa fornecer acesso aos recursos do Azure (incluindo máquinas virtuais e outros recursos do Azure).  Saiba mais sobre o acesso para o Azure para [desenvolvedores](devtest-lab-developer-lab.md) e [testadores](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Considerações sobre escalabilidade
Embora o DevTest Labs não tem internas cotas ou limites, outros recursos do Azure são usados em típico operações de um laboratório têm [cotas no nível de assinatura](../azure-subscription-service-limits.md). Como resultado, em uma implantação corporativa típica, você deve ter várias assinaturas do Azure para cobrir uma grande implantação do DevTest Labs. As cotas mais comumente alcançadas pelas empresas são:

- **Grupos de recursos**:  Na configuração padrão, o DevTest Labs cria um grupo de recursos para cada nova máquina virtual ou um ambiente que um usuário cria usando o serviço. As assinaturas podem conter um [máximo de grupos de recursos 980](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager), portanto, esse limite é o limite superior de máquinas virtuais e ambientes em uma assinatura. Há duas outras configurações que você deve considerar:
    - **[Todas as máquinas virtuais ir para o mesmo grupo de recursos](resource-group-control.md)**:  Embora ele ajuda você com o limite de grupo de recursos, ele afeta o tipo de recurso por limite de grupo de recursos.
    - **Usando compartilhados IPs públicos**:  Todas as VMs do mesmo tamanho e na mesma região entram no mesmo grupo de recursos. Ele é um 'intermediário' entre as cotas do grupo de recursos e o tipo de recurso por cotas de grupo de recursos se as máquinas virtuais podem ter endereços IP públicos. 
- **Grupo de recursos por recurso por recurso tipo**: O limite padrão para [recursos por grupo de recursos por tipo de recurso é 800](../azure-subscription-service-limits.md#resource-group-limits).  Quando usar todas as VMs para a mesma configuração de grupo de recursos, os usuários atingirá esse limite de assinatura muito antes, especialmente se as VMs têm muitos discos extras.
- **Contas de armazenamento**: Um laboratório no DevTest Labs é fornecido com uma conta de armazenamento e a cota do Azure para [número de contas de armazenamento por região e assinatura é de 250](../azure-subscription-service-limits.md#storage-limits). Isso significa que o limite superior para o número de laboratórios de desenvolvimento/teste na mesma região também é 250.
- **As atribuições de função**: Uma atribuição de função é como conceder acesso de usuário ou entidade de segurança a um recurso (proprietário, recursos, nível de permissão). No Azure, existe uma [limite de 2000 atribuições de função por assinatura](../azure-subscription-service-limits.md#role-based-access-control-limits). O serviço DevTest Labs (na configuração padrão) cria um grupo de recursos para cada VM e o proprietário será concedido o **proprietário** permissão para VM do DevTest Labs e **leitor** permissão para o grupo de recursos.  Dessa forma, cada nova VM criada usa duas atribuições de função adicionais para atribuições de função criadas quando permitirá que os usuários do laboratório.
- **API de leituras/gravações**: Há várias maneiras (APIs REST, PowerShell, CLI, SDK do Azure e assim por diante) para automatizar o Azure e laboratórios de desenvolvimento/teste e por meio da automação, é possível atingir o limite de outro em solicitações de API. Cada assinatura permite até [12000 ler solicitações por hora de gravação de solicitações e 1200](../azure-resource-manager/resource-manager-request-limits.md).  Ele é um limite a serem consideradas durante a automação do DevTest Labs.

## <a name="manageability-considerations"></a>Considerações sobre capacidade de gerenciamento
DevTest Labs tem uma interface de usuário administrativo excelente ao trabalhar com um único laboratório. Em uma empresa, há provavelmente várias assinaturas do Azure e muitos laboratórios. Isso significa que fazendo alterações em todos os laboratórios consistentemente requer scripting/automação.  Aqui estão alguns exemplos e melhores maneiras de gerenciar a implantação do DevTest Labs:

- **Alterações nas configurações de laboratório**: Um cenário comum é atualizar uma configuração de laboratório específico em todos os laboratórios na implantação. Por exemplo, um novo tamanho de instância VM está disponível e todos os laboratórios devem ser atualizados para permitir que ele.  É melhor automatizar essas alterações, usando scripts do PowerShell do Azure, CLI do Azure ou APIs REST.  
- **Token de acesso pessoal do repositório de artefato**:  Normalmente, os tokens de acesso pessoal para um repositório Git expirarem (90 dias, 1 ano, 2 anos). Para garantir a continuidade, é importante estender o token de acesso pessoal ou criar um novo e usar a automação para aplicar o novo token de acesso pessoal para todos os laboratórios.
- **Restringir alterações a uma configuração de laboratório**:  É geralmente o caso em que há uma configuração específica (por exemplo, permitindo que imagens do Marketplace a ser usado) deve ser restrita. Ele pode ser feito por meio da política do Azure (impedindo alterações para esse tipo de recurso) ou criando uma função personalizada e conceder a essa função em vez de 'proprietário' para o laboratório. Isso pode ser feito para a maioria das configurações no laboratório (suporte interno, comunicado do laboratório, tamanhos de VM permitidos e assim por diante)
- **Exigir que as VMs seguem uma convenção de nomenclatura**: É uma solicitação comum para identificar facilmente as VMs que fazem parte do ambiente de teste e desenvolvimento baseado em nuvem. Você pode fazer isso [com o Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

É importante observar que o DevTest Labs usa recursos subjacentes no Azure (sistema de rede, discos, computação e assim por diante) que são gerenciados da mesma maneira no Azure.  Por exemplo, a política do Azure se aplica às máquinas virtuais criadas em um laboratório. A Central de segurança do Azure pode relatar a conformidade da VM. O serviço de Backup do Azure pode fornecer backups regulares para as VMs no laboratório e assim por diante. 

## <a name="security-considerations"></a>Considerações de segurança
O Azure DevTest Labs usa os recursos existentes no Azure (computação, rede e assim por diante) e, portanto, automaticamente se beneficia de todos os recursos de segurança excelente internos para a plataforma. Por exemplo, para proteger qualquer conexões de entrada de área de trabalho remotas para serem originadas somente da rede corporativa, é tão simple quanto adicionar um grupo de segurança de rede para a rede virtual no gateway de área de trabalho remota. A consideração de segurança adicional apenas para o Azure DevTest Labs é o nível de permissões concedido aos membros da equipe que usarão os laboratórios em uma base diária.  São as permissões comuns dadas ["Proprietário" e "Usuário do DevTest Labs"](devtest-lab-add-devtest-user.md). Para obter mais informações sobre essas funções, consulte [adicionar usuários e proprietários aos Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Próximas etapas
Consulte o próximo artigo desta série: [Dimensionar sua infraestrutura do Azure DevTest Labs](devtest-lab-guidance-scale.md)
