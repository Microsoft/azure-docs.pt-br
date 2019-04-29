---
title: Visão geral de ponto de extremidade de serviço vnet do servidor do Banco de Dados do Azure para PostgreSQL
description: Saiba como os pontos de extremidade de serviço VNET funcionam para seu servidor do Banco de Dados do Azure para PostgreSQL.
author: WenJason
ms.author: v-jay
ms.service: postgresql
ms.topic: conceptual
origin.date: 08/20/2018
ms.date: 04/22/2019
ms.openlocfilehash: c6549ad170a0fc3b4387d5bc5163ca0548b92119
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60560106"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-postgresql"></a>Use pontos de extremidade de serviço e regras de Rede virtual para o Banco de Dados do Azure para PostgreSQL

As *regras de rede virtual* são um recurso de segurança de firewall que controla se o servidor do Banco de Dados do Azure para PostgreSQL aceita comunicações que sejam enviadas de sub-redes particulares em redes virtuais. Este artigo explica por que o recurso de regra da rede virtual é, às vezes, a melhor opção para permitir a comunicação segura com seu servidor do Banco de Dados do Azure para PostgreSQL.

Para criar uma regra da rede virtual, deve haver primeiro uma VNet ([rede virtual][vm-virtual-network-overview]) e um [ponto de extremidade de serviço de rede virtual][vm-virtual-network-service-endpoints-overview-649d] para a regra a ser referenciada. A figura a seguir ilustra como um ponto de extremidade de serviço de Rede virtual funciona com o Banco de Dados do Azure para PostgreSQL:

![Exemplo de como funciona um ponto de extremidade de serviço de VNet](media/concepts-data-access-and-security-vnet/vnet-concept.png)

> [!NOTE]
> Esse recurso está disponível em todas as regiões da nuvem pública do Azure nas quais o Banco de Dados do Azure para PostgreSQL é implantado para servidores de Finalidade Geral e Otimizado por Memória.
> No caso de emparelhamento de VNet, se o tráfego estiver fluindo através de um Gateway VNet comum com pontos de extremidade de serviço e deve fluir para o par, crie uma regra ACL/VNet para permitir que Máquinas Virtuais do Azure acessem o Banco de Dados do Azure para servidor PostgreMySQL.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Descrição e terminologia

**Rede virtual:** Você pode associar redes virtuais à sua assinatura do Azure.

**Sub-rede:** Uma rede virtual contém **sub-redes**. Todas as máquinas virtuais do Azure (VMs) que você tem são atribuídas a sub-redes. Uma sub-rede pode conter várias VMs ou outros nós de computadores. Nós de computadores que estão fora da sua rede virtual não podem acessar sua rede virtual, a menos que você configure a segurança para permitir o acesso.

**Ponto de extremidade de serviço de Rede Virtual:** Um [ponto de extremidade de serviço de Rede Virtual][vm-virtual-network-service-endpoints-overview-649d] é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço do Azure. Neste artigo, estamos interessados no nome do tipo de **Microsoft.Sql**, que faz referência ao serviço do Azure chamado banco de dados SQL. Essa marcação de serviço também aplica-se aos serviços MySQL e Banco de Dados do Azure para PostgreSQL. É importante observar que, ao aplicar a marcação de serviço **Microsoft.Sql** a um ponto de extremidade de serviço de VNet, ela configurará o tráfego do ponto de extremidade de serviço para todos os servidores do Banco de Dados SQL do Azure, Banco de Dados do Azure para PostgreSQL e Banco de Dados do Azure para MySQL na sub-rede. 

**Regra de rede virtual:** Uma regra da rede virtual para seu servidor do Banco de Dados do Azure para PostgreSQL é uma sub-rede listada no ACL (lista de controle de acesso) do seu servidor do Banco de Dados do Azure para PostgreSQL. Para estar no ACL do seu servidor do Banco de Dados do Azure para PostgreSQL, a sub-rede deve conter o nome do tipo **Microsoft.Sql**.

Uma regra da rede virtual instrui o servidor do Banco de Dados do Azure para PostgreSQL a aceitar comunicações de cada nó na sub-rede.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Benefícios de uma regra da rede virtual

Até que você execute uma ação, as VMs em suas sub-redes não podem se comunicar com seu servidor do Banco de Dados do Azure para PostgreSQL. Uma ação que estabelece a comunicação é a criação de uma regra da rede virtual. A lógica para escolher a abordagem de regra da VNet requer uma discussão de comparação e contraste que envolve as opções de segurança concorrentes oferecidas pelo firewall.

### <a name="a-allow-access-to-azure-services"></a>a. Permitir o acesso aos serviços do Azure

O painel de segurança de conexão tem um botão de **ON/OFF** rotulado como **Permitir acesso aos serviços do Azure**. A configuração **ON** permite as comunicações de todos os endereços IP do Azure e todas as sub-redes do Azure. Esses IPs ou sub-redes do Azure não podem pertencer a você. Essa configuração **ON** é provavelmente mais aberta do que você deseja que seu Banco de Dados do Azure para PostgreSQL seja. O recurso de regra da rede virtual oferece um maior controle granular.

### <a name="b-ip-rules"></a>B. Regras de IP

O firewall do Banco de Dados do Azure para PostgreSQL permite que você especifique intervalos de endereços IP dos quais as comunicações são aceitas no Banco de Dados do Azure para PostgreSQL. Essa abordagem é adequada para endereços IP estáveis que estão fora da rede privada do Azure. Mas muitos nós dentro da rede privada do Azure estão configurados com endereços IP *dinâmicos*. Endereços IP dinâmicos podem mudar, como quando sua VM é reiniciada. Seria ilusório especificar um endereço IP dinâmico em uma regra de firewall, em um ambiente de produção.

Você pode recuperar a opção de IP obtendo um endereço IP *estático* para a VM. Para obter mais detalhes, consulte [Configurar endereços IP particulares para uma máquina virtual usando o Portal do Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

No entanto, a abordagem de IP estático pode se tornar difícil de gerenciar e é cara quando realizada em escala. Regras da rede virtual são mais fáceis de estabelecer e gerenciar.

### <a name="c-cannot-yet-have-azure-database-for-postgresql-on-a-subnet-without-defining-a-service-endpoint"></a>C. Ainda não é possível ter o Banco de Dados do Azure para PostgreSQL em uma sub-rede sem definir um ponto de extremidade de serviço

Se o servidor **Microsoft.Sql** fosse um nó em uma sub-rede em sua rede virtual, todos os nós dentro da rede virtual poderiam se comunicar com seu servidor do Banco de Dados do Azure para PostgreSQL. Nesse caso, suas VMs podem se comunicar com o Banco de Dados do Azure para PostgreSQL sem a necessidade de nenhuma regra da rede virtual ou regras de IP.

No entanto, a partir de agosto de 2018, o serviço Banco de Dados do Azure para PostgreSQL ainda não está entre os serviços que podem ser atribuídos diretamente a uma sub-rede.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre as regras da rede virtual

Esta seção descreve vários detalhes sobre as regras da rede virtual.

### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto de extremidade de serviço de rede virtual se aplica a apenas uma região do Azure. O ponto de extremidade não permite que outras regiões aceitem a comunicação da sub-rede.

Qualquer regra da rede virtual é limitada à região à qual seu ponto de extremidade subjacente se aplica.

### <a name="server-level-not-database-level"></a>Nível de servidor, não o nível de banco de dados

Cada regra da rede virtual aplica-se a todo o seu servidor do Banco de Dados do Azure para PostgreSQL, não apenas a um determinado banco de dados no servidor. Em outras palavras, a regra da rede virtual se aplica no nível de servidor, não no nível do banco de dados.

#### <a name="security-administration-roles"></a>Funções de administração de segurança

Há uma separação de funções de segurança na administração de pontos de extremidade de serviço de rede virtual. A ação é necessária em cada uma das seguintes funções:

- **Administrador de Rede:** &nbsp; Ative o ponto de extremidade.
- **Administrador do banco de dados:** &nbsp; atualize a ACL (lista de controle de acesso) para adicionar a sub-rede fornecida ao servidor do Banco de Dados do Azure para PostgreSQL.

*Alternativa de RBAC:*

As funções de Administrador de banco de dados e Administrador de rede têm mais recursos do que o necessário para gerenciar regras da rede virtual. É necessário apenas um subconjunto de seus recursos.

Você tem a opção de usar o [controle de acesso baseado em função (RBAC)][rbac-what-is-813s] no Azure para criar uma única função personalizada que tenha apenas o subconjunto necessário de recursos. A função personalizada pode ser usada em vez de envolver o Administrador de rede ou o Administrador de banco de dados. A área da superfície da sua exposição de segurança é menor, se você adicionar um usuário a uma função personalizada, em vez de adicionar o usuário às outras duas funções de administrador principal.

> [!NOTE]
> Em alguns casos, o Banco de Dados do Azure para PostgreSQL e a sub-rede da VNet estão em assinaturas diferentes. Nesses casos, você deve garantir as seguintes configurações:
> - Ambas as assinaturas devem estar associadas ao mesmo locatário do Azure Active Directory.
> - O usuário tem as permissões necessárias para iniciar operações, como habilitar pontos de extremidade de serviço e adicionar uma sub-rede da VNet ao servidor.

## <a name="limitations"></a>Limitações

Para o Banco de Dados do Azure para PostgreSQL, o recurso de regras da rede virtual tem as seguintes limitações:

- Um aplicativo Web pode ser mapeado para um IP privado em uma sub-rede/rede virtual. Mesmo se os pontos de extremidade de serviço são ativados por meio da rede virtual/sub-rede determinada, as conexões do aplicativo Web para o servidor terão uma fonte IP pública Azure, não uma fonte de sub-rede/rede virtual. Para habilitar a conectividade de um aplicativo Web para um servidor que tem regras de firewall de rede virtual, você deve permitir Azure services para acessar o servidor no servidor.

- No firewall do Banco de Dados do Azure para PostgreSQL, cada regra da rede virtual referencia uma sub-rede. Todas essas sub-redes referenciadas devem ser hospedadas na mesma região geográfica que hospeda o Banco de Dados do Azure para PostgreSQL.

- Cada servidor do Banco de Dados do Azure para PostgreSQL pode ter até 128 entradas de ACL para qualquer rede virtual especificada.

- As regras da rede virtual se aplicam somente a redes virtuais do Azure Resource Manager; e não a redes do [modelo de implantação clássico][arm-deployment-model-568f].

- A ativação dos pontos de extremidade de serviço de rede virtual no Banco de Dados do Azure para PostgreSQL usando a marca de serviço **Microsoft.Sql** também habilita os pontos de extremidade para todos os serviços de Banco de Dados do Azure: Banco de Dados do Azure para MySQL, Banco de Dados do Azure para PostgreSQL, Banco de Dados SQL do Azure e SQL Data Warehouse do Azure.

- O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.

- No firewall, os intervalos de endereços IP se aplicam aos seguintes itens de rede, mas as regras da rede virtual não:
    - [Rede privada virtual (VPN) de site a site (S2S)][vpn-gateway-indexmd-608y]
    - No local por meio de [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Se sua rede estiver conectada à rede do Azure através do [ExpressRoute][expressroute-indexmd-744v], cada circuito é configurado com dois endereços IP públicos no Microsoft Edge. Os dois endereços IP são usados para se conectar aos serviços da Microsoft, como o Armazenamento do Azure, usando o emparelhamento público do Azure.

Para permitir a comunicação do seu circuito com o Banco de Dados do Azure para PostgreSQL, é necessário criar regras de rede IP para os endereços IP públicos dos seus circuitos. Para localizar os endereços IP públicos do seu circuito do ExpressRoute, abra um tique de suporte com o ExpressRoute por meio do Portal do Azure.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Adicionando uma regra de Firewall da VNET ao servidor sem a ativação de pontos de extremidade de serviço da VNET

Simplesmente definir uma regra de firewall não ajuda a proteger o servidor para a VNet. Você também deve **ativar** os pontos de extremidade de serviço da VNet para que a segurança entre em vigor. Quando você **ativa** endpoints de serviço, sua sub-rede VNet passa por um período de inatividade até concluir a transição de **Desativado** para **Ativado**. Isso é especialmente verdadeiro no contexto de VNETs grandes. Use o sinalizador **IgnoreMissingServiceEndpoint** para reduzir ou eliminar o tempo de inatividade durante a transição.

Você pode definir o sinalizador **IgnoreMissingServiceEndpoint** usando a CLI do Azure ou o portal.

## <a name="related-articles"></a>Artigos relacionados
- [Redes virtuais do Azure][vm-virtual-network-overview]
- [Pontos de extremidade de serviço de rede virtual do Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Próximos passos
Para obter artigos sobre como criar regras de VNet, consulte:
- [Criar e gerenciar regras de VNet do Banco de Dados do Azure para PostgreSQL usando o portal do Azure](howto-manage-vnet-using-portal.md)
- [Criar e gerenciar regras de VNet do Banco de Dados do Azure para PostgreSQL usando a CLI do Azure](howto-manage-vnet-using-cli.md)


<!-- Link references, to text, Within this same GitHub repo. -->
[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml