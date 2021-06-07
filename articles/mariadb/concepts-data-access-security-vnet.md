---
title: Pontos de extremidade de serviço de VNet-banco de dados do Azure para MariaDB
description: Descreve como os pontos de extremidade de serviço de VNet funcionam no servidor do Banco de Dados do Azure para MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/17/2020
ms.openlocfilehash: bd7d08e4f65612b9a76b63e8153603d043209ad3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98662671"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-mariadb"></a>Usar regras e pontos de extremidade de serviço de Rede Virtual para Banco de Dados do Azure para MariaDB

As *regras da rede virtual* são um recurso de segurança de firewall que controla se o servidor do Banco de Dados do Azure para MariaDB aceita comunicações que sejam enviadas de sub-redes particulares em redes virtuais. Este artigo explica por que o recurso de regra da rede virtual é, às vezes, a melhor opção para permitir a comunicação segura com seu servidor do Banco de Dados do Azure para MariaDB.

Para criar uma regra da rede virtual, deve haver primeiro uma VNet ([rede virtual][vm-virtual-network-overview]) e um [ponto de extremidade de serviço de rede virtual][vm-virtual-network-service-endpoints-overview-649d] para a regra a ser referenciada. A figura a seguir ilustra como um ponto de extremidade de serviço de rede Virtual funciona com o Banco de Dados do Azure para MariaDB:

![Exemplo de como funciona um ponto de extremidade de serviço de VNet](media/concepts-data-access-security-vnet/vnet-concept.png)

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure nas quais o Banco de Dados do Azure para MariaDB é implantado para servidores de Uso Geral e Otimizado por Memória.

Você também pode considerar o uso de [link privado](concepts-data-access-security-private-link.md) para conexões. O link privado fornece um endereço IP privado em sua VNet para o banco de dados do Azure para o servidor MariaDB.

<a name="anch-terminology-and-description-82f"></a>

## <a name="terminology-and-description"></a>Descrição e terminologia

**Rede virtual:** você pode ter redes virtuais associadas à sua assinatura do Azure.

**Sub-rede:** uma rede virtual contém **sub-redes**. Todas as máquinas virtuais do Azure (VMs) que você tem são atribuídas a sub-redes. Uma sub-rede pode conter várias VMs ou outros nós de computadores. Nós de computadores que estão fora da sua rede virtual não podem acessar sua rede virtual, a menos que você configure a segurança para permitir o acesso.

**Ponto de extremidade de serviço de Rede Virtual:** Um [ponto de extremidade de serviço de Rede Virtual][vm-virtual-network-service-endpoints-overview-649d] é uma sub-rede cujos valores de propriedade incluem um ou mais nomes formais de tipo de serviço do Azure. Neste artigo, estamos interessados no nome do tipo de **Microsoft.Sql**, que faz referência ao serviço do Azure chamado banco de dados SQL. Essa marca de serviço também aplica-se aos serviços do Banco de Dados do Azure para MariaDB, MySQL e PostgreSQL. É importante observar que, ao aplicar a marca de serviço **Microsoft.Sql** a um ponto de extremidade de serviço de VNet, ela configurará o tráfego do ponto de extremidade para todos os servidores do Banco de Dados SQL do Azure, Banco de Dados do Azure para MariaDB, Banco de Dados do Azure para MySQL e Banco de Dados do Azure para PostgreSQL na sub-rede.

**Regra da rede virtual:** uma regra da rede virtual para seu servidor do Banco de Dados do Azure para MariaDB é uma sub-rede listada no ACL (lista de controle de acesso) do seu servidor do Banco de Dados do Azure para MariaDB. Para estar no ACL do seu servidor do Banco de Dados do Azure para MariaDB, a sub-rede deve conter o nome do tipo **Microsoft.Sql**.

Uma regra da rede virtual instrui o servidor do Banco de Dados do Azure para MariaDB a aceitar comunicações de cada nó na sub-rede.







<a name="anch-benefits-of-a-vnet-rule-68b"></a>

## <a name="benefits-of-a-virtual-network-rule"></a>Benefícios de uma regra da rede virtual

Até que você execute uma ação, as VMs em suas sub-redes não podem se comunicar com seu servidor do Banco de Dados do Azure para MariaDB. Uma ação que estabelece a comunicação é a criação de uma regra da rede virtual. A lógica para escolher a abordagem de regra da VNet requer uma discussão de comparação e contraste que envolve as opções de segurança concorrentes oferecidas pelo firewall.

### <a name="a-allow-access-to-azure-services"></a>a. Permitir acesso aos serviços do Azure

O painel de segurança de conexão tem um botão de **ON/OFF** rotulado como **Permitir acesso aos serviços do Azure**. A configuração **ON** permite as comunicações de todos os endereços IP do Azure e todas as sub-redes do Azure. Esses IPs ou sub-redes do Azure não podem pertencer a você. Essa configuração **ON** é provavelmente mais aberta do que você deseja que seu Banco de Dados do Azure para MariaDB seja. O recurso de regra da rede virtual oferece um maior controle granular.

### <a name="b-ip-rules"></a>B. Regras de IP

O firewall do Banco de Dados do Azure para MariaDB permite que você especifique intervalos de endereços IP dos quais as comunicações são aceitas no Banco de Dados do Azure para MariaDB. Essa abordagem é adequada para endereços IP estáveis que estão fora da rede privada do Azure. Mas muitos nós dentro da rede privada do Azure estão configurados com endereços IP *dinâmicos*. Endereços IP dinâmicos podem mudar, como quando sua VM é reiniciada. Seria ilusório especificar um endereço IP dinâmico em uma regra de firewall, em um ambiente de produção.

Você pode recuperar a opção de IP obtendo um endereço IP *estático* para a VM. Para obter mais detalhes, consulte [Configurar endereços IP particulares para uma máquina virtual usando o Portal do Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

No entanto, a abordagem de IP estático pode se tornar difícil de gerenciar e é cara quando realizada em escala. Regras da rede virtual são mais fáceis de estabelecer e gerenciar.


<a name="anch-details-about-vnet-rules-38q"></a>

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre as regras da rede virtual

Esta seção descreve vários detalhes sobre as regras da rede virtual.

### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto de extremidade de serviço de rede virtual se aplica a apenas uma região do Azure. O ponto de extremidade não permite que outras regiões aceitem a comunicação da sub-rede.

Qualquer regra da rede virtual é limitada à região à qual seu ponto de extremidade subjacente se aplica.

### <a name="server-level-not-database-level"></a>Nível de servidor, não o nível de banco de dados

Cada regra da rede virtual aplica-se a todo o seu servidor do Banco de Dados do Azure para MariaDB, não apenas a um determinado banco de dados no servidor. Em outras palavras, a regra da rede virtual se aplica no nível de servidor, não no nível do banco de dados.

### <a name="security-administration-roles"></a>Funções de administração de segurança

Há uma separação de funções de segurança na administração de pontos de extremidade de serviço de rede virtual. A ação é necessária em cada uma das seguintes funções:

- **Administrador de rede:** &nbsp; ativar o ponto de extremidade.
- **Administrador de banco de dados:** &nbsp; atualize a ACL (lista de controle de acesso) para adicionar a sub-rede fornecida ao servidor do Banco de Dados do Azure para MariaDB.

*Alternativa do RBAC do Azure:*

As funções de Administrador de banco de dados e Administrador de rede têm mais recursos do que o necessário para gerenciar regras da rede virtual. É necessário apenas um subconjunto de seus recursos.

Você tem a opção de usar o [controle de acesso baseado em função do Azure (RBAC do Azure)][rbac-what-is-813s] no Azure para criar uma única função personalizada que tem apenas o subconjunto necessário de recursos. A função personalizada pode ser usada em vez de envolver o administrador de rede ou o administrador de banco de dados. A área da superfície de sua exposição de segurança será menor se você adicionar um usuário a uma função personalizada, em vez de adicionar o usuário às outras duas principais funções de administrador.

> [!NOTE]
> Em alguns casos, o Banco de Dados do Azure para MariaDB e a sub-rede da VNet estão em assinaturas diferentes. Nesses casos, você deve garantir as seguintes configurações:
> - Ambas as assinaturas devem estar associadas ao mesmo locatário do Azure Active Directory.
> - O usuário tem as permissões necessárias para iniciar operações, como habilitar pontos de extremidade de serviço e adicionar uma sub-rede da VNet ao servidor.
> - Certifique-se de que a assinatura tenha o provedor de recursos **Microsoft. SQL** e **Microsoft. DBforMariaDB** registrado. Para obter mais informações, confira [resource-manager-registration][resource-manager-portal]

## <a name="limitations"></a>Limitações

Para o Banco de Dados do Azure para MariaDB, o recurso de regras da rede virtual tem as seguintes limitações:

- Um aplicativo Web pode ser mapeado para um IP privado em uma sub-rede/rede virtual. Mesmo se os pontos de extremidade de serviço são ativados por meio da rede virtual/sub-rede determinada, as conexões do aplicativo Web para o servidor terão uma fonte IP pública Azure, não uma fonte de sub-rede/rede virtual. Para habilitar a conectividade de um aplicativo Web para um servidor com regras de firewall de VNET, você precisa Permitir que os serviços do Azure acessem o servidor no servidor.

- No firewall do Banco de Dados do Azure para MariaDB, cada regra da rede virtual referencia uma sub-rede. Todas essas sub-redes referenciadas devem ser hospedadas na mesma região geográfica que hospeda o Banco de Dados do Azure para MariaDB.

- Cada servidor do Banco de Dados do Azure para MariaDB pode ter até 128 entradas de ACL para qualquer rede virtual especificada.

- As regras da rede virtual se aplicam somente a redes virtuais do Azure Resource Manager; e não a redes do [modelo de implantação clássico][resource-manager-deployment-model-568f].

- Ativar pontos de extremidade de serviço de rede virtual no banco de dados do Azure para MariaDB usando a marca de serviço **Microsoft. SQL** também habilita os pontos de extremidade para todos os serviços de banco de dados do Azure: banco de dados do Azure para MariaDB, banco de dados do Azure para MySQL, banco de dados do Azure para PostgreSQL, banco de dados SQL do Azure e Azure Synapse

- O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.

- Se **o Microsoft. SQL** estiver habilitado em uma sub-rede, isso indica que você só deseja usar regras de VNet para se conectar. [Regras de firewall não VNet](concepts-firewall-rules.md) de recursos nessa sub-rede não funcionarão.

- No firewall, os intervalos de endereços IP se aplicam aos seguintes itens de rede, mas as regras da rede virtual não:
    - [Rede privada virtual (VPN) de site a site (S2S)][vpn-gateway-indexmd-608y]
    - Local via [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Se sua rede estiver conectada à rede do Azure através do [ExpressRoute][expressroute-indexmd-744v], cada circuito é configurado com dois endereços IP públicos no Microsoft Edge. Os dois endereços IP são usados para se conectar aos serviços da Microsoft, como o Armazenamento do Azure, usando o emparelhamento público do Azure.

Para permitir a comunicação do seu circuito com o Banco de Dados do Azure para MariaDB, é necessário criar regras de rede IP para os endereços IP públicos dos seus circuitos. Para localizar os endereços IP públicos do seu circuito do ExpressRoute, abra um tique de suporte com o ExpressRoute por meio do Portal do Azure.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Adicionando uma regra de firewall de VNET ao seu servidor sem ativar os pontos de extremidade de serviço de VNET

Simplesmente definir uma regra de firewall de VNet não ajuda a proteger o servidor para a VNet. Você também deve **ativar** os pontos de extremidade de serviço da VNet para que a segurança entre em vigor. Quando você **ativa** endpoints de serviço, sua sub-rede VNet passa por um período de inatividade até concluir a transição de **Desativado** para **Ativado**. Isso é especialmente verdadeiro no contexto de VNETs grandes. Use o sinalizador **IgnoreMissingServiceEndpoint** para reduzir ou eliminar o tempo de inatividade durante a transição.

Você pode definir o sinalizador **IgnoreMissingServiceEndpoint** usando a CLI do Azure ou o portal.

## <a name="related-articles"></a>Artigos relacionados
- [Redes virtuais do Azure][vm-virtual-network-overview]
- [Pontos de extremidade de serviço de rede virtual do Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Próximas etapas
Para obter artigos sobre como criar regras de VNet, consulte:
- [Criar e gerenciar regras de VNet do Banco de Dados do Azure para MariaDB usando o portal do Azure](howto-manage-vnet-portal.md)
 
<!--
- [Create and manage Azure Database for MariaDB VNet rules using Azure CLI](howto-manage-vnet-using-cli.md)
-->

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../role-based-access-control/overview.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md