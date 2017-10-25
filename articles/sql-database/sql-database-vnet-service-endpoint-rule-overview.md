---
title: "Pontos de extremidade e regras de serviço de rede virtual para o Banco de dados SQL do Azure | Microsoft Docs"
description: "Marque uma sub-rede como um ponto de extremidade de serviço de Rede virtual. Em seguida, o ponto de extremidade como uma regra de rede virtual para a ACL de seu banco de dados SQL do Azure. Seu Banco de dados SQL do Microsoft Azure então aceita a comunicação de todas as máquinas virtuais e outros nós na sub-rede."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: VNet Service endpoints
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 10/09/2017
ms.author: genemi
ms.openlocfilehash: f62184d97b18d72b91d63db0e449bbab6c20a179
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-sql-database"></a>Use pontos de extremidade e regras de serviço de rede virtual para o Banco de dados SQL do Azure

*Regras de rede Virtual* são um recurso de segurança de firewall que controla se o servidor do banco de dados SQL do Azure aceita comunicações que sejam enviadas de sub-redes particulares em redes virtuais. Este artigo explica por que o recurso de regra de rede virtual, às vezes, é a melhor opção para permitir a comunicação segura com seu banco de dados SQL do Azure.

Para criar uma regra de rede virtual, primeiro, é preciso que haja um [ponto de extremidade de serviço de rede virtual] [ vm-virtual-network-service-endpoints-overview-649d] para a regra de referência.

#### <a name="how-to-create-a-virtual-network-rule"></a>Como criar uma regra de rede virtual

Se você só criar uma regra de rede virtual, poderá pular para as etapas e explicação [posteriores neste artigo](#anchor-how-to-by-using-firewall-portal-59j).






<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Descrição e terminologia

**Rede virtual:** você pode ter redes virtuais associadas à sua assinatura do Azure.

**Sub-rede:** uma rede virtual contém **sub-redes**. Todas as máquinas virtuais do Azure (VMs) que você tem são atribuídas a sub-redes. Uma sub-rede pode conter várias VMs ou outros nós de computadores. Nós de computadores que estão fora da sua rede virtual não podem acessar sua rede virtual, a menos que você configure a segurança para permitir o acesso.

**Ponto de extremidade de serviço de rede virtual:** um [ponto de extremidade de serviço de uma rede Virtual][vm-virtual-network-service-endpoints-overview-649d] é uma sub-rede cujos valores de propriedade incluem um ou mais nomes de tipo formais de serviço do Azure. Neste artigo, estamos interessados no nome do tipo de **Microsoft.Sql**, que faz referência ao serviço do Azure chamado banco de dados SQL.

**Regra de rede virtual:** uma regra de rede virtual para o servidor de Banco de dados SQL do Microsoft Azure é uma sub-rede listada na lista de controle de acesso (ACL) do seu servidor de Banco de dados SQL do Microsoft Azure. Para estar na ACL do seu banco de dados SQL, a sub-rede deve conter o nome do tipo **Microsoft.Sql**.

Uma regra de rede virtual informa o servidor de Banco de dados SQL do Microsoft Azure para aceitar comunicações de cada nó que está na sub-rede.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Benefícios de uma regra de rede virtual

Até que você execute uma ação, as VMs em suas sub-redes não podem se comunicar com seu Banco de dados SQL do Microsoft Azure. A lógica para escolher a abordagem de regra de rede virtual para permitir a comunicação requer uma discussão de comparação e contraste que envolve as opções de segurança concorrentes oferecidas pelo firewall.

#### <a name="a-allow-access-to-azure-services"></a>R. Permitir o acesso aos serviços do Azure

O painel de firewall tem um botão de **ON/OFF** que é rotulado como **Permitir acesso aos serviços do Azure**. A configuração **ON** permite as comunicações de todos os endereços IP do Azure e todas as sub-redes do Azure. Esses IPs ou sub-redes do Azure não podem pertencer a você. Essa configuração **ON** é provavelmente mais aberta do que você deseja que seu Banco de dados SQL do Microsoft Azure seja. O recurso de regra de rede virtual oferece um maior controle granular.

#### <a name="b-ip-rules"></a>B. Regras de IP

O firewall do Bnco de dados SQL do Microsoft Azure permite que você especifique os intervalos de endereços IP dos quais as comunicações são aceitas no Banco de dados SQL do Microsoft Azure. Essa abordagem é adequada para endereços IP estáveis que estão fora da rede privada do Azure. Mas muitos nós dentro da rede privada do Azure estão configurados com endereços IP *dinâmicos*. Endereços IP dinâmicos podem mudar, como quando sua VM é reiniciada. Seria ilusório especificar um endereço IP dinâmico em uma regra de firewall, em um ambiente de produção.

Você pode recuperar a opção de IP obtendo um endereço IP *estático* para a VM. Para obter mais detalhes, consulte [Configurar endereços IP particulares para uma máquina virtual usando o Portal do Azure][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

No entanto, a abordagem de IP estático pode se tornar difícil de gerenciar e é cara quando realizada em escala. Regras de rede virtual são mais fáceis de estabelecer e gerenciar.

#### <a name="c-cannot-yet-have-sql-database-on-a-subnet"></a>C. Ainda não pode ter o Banco de dados SQL do Microsoft Azure em uma sub-rede

Se seu servidor de banco de dados SQL do Azure for um nó em uma sub-rede em sua rede virtual, todos os nós dentro da rede virtual podem se comunicar com seu banco de dados SQL. Nesse caso, suas VMs podem se comunicar com o Banco de dados SQL do Microsoft Azure sem a necessidade de nenhuma regra de rede virtual ou regra de IP.

No entanto a partir de setembro de 2017, o serviço de banco de dados SQL do Azure ainda não está entre os serviços que podem ser atribuídos a uma sub-rede.






<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Detalhes sobre as regras de rede virtual

Esta seção descreve vários detalhes sobre as regras de rede virtual.

#### <a name="only-one-geographic-region"></a>Apenas uma região geográfica

Cada ponto de extremidade de serviço de rede virtual se aplica a apenas uma região do Azure. O ponto de extremidade não permite que outras regiões aceitem a comunicação da sub-rede.

Qualquer regra de rede virtual é limitada à região à qual seu ponto de extremidade subjacente se aplica.

#### <a name="server-level-not-database-level"></a>Nível de servidor, não o nível de banco de dados

Cada regra de rede virtual se aplica a todo o seu servidor de banco de dados SQL do Azure, não apenas a um determinado banco de dados no servidor. Em outras palavras, a regra de rede virtual se aplica no nível de servidor, não no nível do banco de dados.

- Por outro lado, as regras de IP podem ser aplicadas em qualquer nível.

#### <a name="security-administration-roles"></a>Funções de administração de segurança

Há uma separação de funções de segurança na administração de pontos de extremidade de serviço de rede virtual. A ação é necessária em cada uma das seguintes funções:

- **Administrador de rede:** &nbsp; ativar o ponto de extremidade.
- **Administrador de banco de dados:** &nbsp; atualizar a lista de controle de acesso (ACL) para adicionar a sub-rede fornecida ao servidor de Banco de dados SQL do Microsoft Azure.

*Alternativa de RBAC:* 

As funções de Administrador de banco de dados e Administrador de rede têm mais recursos do que o necessário para gerenciar regras de rede virtual. É necessário apenas um subconjunto de seus recursos.

Você tem a opção de usar o [controle de acesso baseado em função (RBAC)][rbac-what-is-813s] no Azure para criar uma única função personalizada que tenha apenas o subconjunto necessário de recursos. A função personalizada pode ser usada em vez de envolver o Administrador de rede ou o Administrador de banco de dados. A área da superfície da sua exposição de segurança é menor, se você adicionar um usuário a uma função personalizada, em vez de adicionar o usuário às outras duas funções de administrador principal.






## <a name="limitations"></a>Limitações

Para o Banco de Dados SQL do Azure, o recurso de regras de rede virtual tem as seguintes limitações:

- No firewall do Banco de Dados SQL, cada regra de rede virtual faz referência a uma sub-rede. Todas essas sub-redes referenciadas devem ser hospedadas na mesma região geográfica que hospeda o Banco de Dados SQL.

- Cada servidor de banco de dados SQL do Azure pode ter até 128 entradas de ACL para qualquer rede virtual especificada.

- As regras de rede virtual se aplicam somente a redes virtuais do Azure Resource Manager; e não a redes do [modelo de implantação clássico][arm-deployment-model-568f].

- No firewall, os intervalos de endereços IP se aplicam aos seguintes itens de rede, mas as regras de rede virtual não:
    - [Rede privada virtual (VPN) de site a site (S2S)][vpn-gateway-indexmd-608y]
    - No local por meio de [ExpressRoute][expressroute-indexmd-744v]

#### <a name="expressroute"></a>ExpressRoute

Se sua rede estiver conectada à rede do Azure através do [ExpressRoute][expressroute-indexmd-744v], cada circuito é configurado com dois endereços IP públicos no Microsoft Edge. Os dois endereços IP são usados para se conectar aos serviços da Microsoft, como o Armazenamento do Azure, usando o emparelhamento público do Azure.

Para permitir a comunicação do seu circuito para o Banco de Dados SQL do Azure, você deve criar regras de rede IP para os endereços IP públicos dos seus circuitos. Para localizar os endereços IP públicos do seu circuito do ExpressRoute, abra um tique de suporte com o ExpressRoute por meio do Portal do Azure.


<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->





## <a name="errors-40914-and-40615"></a>Erros 40914 e 40615

O erro de conexão 40914 está relacionado a *regras de rede virtual*, conforme especificado no painel Firewall no Portal do Azure. O erro 40615 é semelhante, exceto pelo fato de que ele se relaciona com *regras de endereço IP* no Firewall.

#### <a name="error-40914"></a>Erro 40914

*Texto da mensagem:* não é possível abrir o servidor '*[nome-do-servidor]*' solicitado pelo logon. O cliente não tem permissão para acessar o servidor.

*Descrição do erro:* o cliente está em uma sub-rede que tem pontos de extremidade de servidor de rede virtual. Mas o servidor de Banco de Dados SQL do Azure não tem nenhuma regra de rede virtual que conceda à sub-rede o direito de se comunicar com o Banco de Dados SQL.

*Resolução de erro:* no painel Firewall do Portal do Azure, use o controle de regras de rede virtual para [adicionar uma regra de rede virtual](#anchor-how-to-by-using-firewall-portal-59j) à sub-rede.

#### <a name="error-40615"></a>Erro 40615

*Texto da mensagem:* não é possível abrir o servidor '{0}' solicitado pelo logon. O cliente com o endereço IP '{1}' não tem permissão para acessar o servidor.

*Descrição do erro:* o cliente está tentando conectar-se de um endereço IP que não está autorizado a se conectar ao servidor de Banco de Dados SQL do Azure. O firewall do servidor não tem nenhuma regra de endereço IP que permita que um cliente se comunique do endereço IP fornecido para o Banco de Dados SQL.

*Resolução de erro:* digite o endereço IP do cliente como uma regra de IP. Faça isso usando o painel Firewall no Portal do Azure.


Uma lista de várias mensagens de erro de Banco de Dados SQL está documentada [aqui][sql-database-develop-error-messages-419g].





<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>O Portal pode criar uma regra de rede virtual

Esta seção ilustra como você pode usar o [portal do Azure][http-azure-portal-link-ref-477t] para criar uma *regra de rede virtual* no banco de dados SQL do Azure. A regra informa ao Banco de dados SQL do Microsoft Azure para aceitar a comunicação de uma sub-rede específica que foi marcada como sendo um *ponto de extremidade de serviço de rede virtual*.

#### <a name="powershell-alternative"></a>Alternativa do PowerShell

Um script do PowerShell também pode criar regras de rede virtual. O cmdlet crucial **New-AzureRmSqlServerVirtualNetworkRule**. Se tiver interesse, consulte [PowerShell para criar uma regra e um ponto de extremidade do Serviço de rede virtual para o banco de dados SQL do Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

#### <a name="prerequisites"></a>Pré-requisitos

Você já deve ter uma sub-rede que esteja marcada com o ponto de extremidade de serviço de rede virtual específico *nome do tipo* relevante para o banco de dados SQL do Azure.

- O nome do tipo de ponto de extremidade relevante é **Microsoft.Sql**.
- Se a sua sub-rede não pode ser marcada com o nome do tipo, consulte [Verificar se sua sub-rede é um ponto de extremidade][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

### <a name="azure-portal-steps"></a>etapas do portal do Azure

1. Faça logon no [Portal do Azure][http-azure-portal-link-ref-477t].

2. Em seguida, navegue até o portal de **servidores SQL**&gt;**Firewall / Redes virtuais**.

3. Defina o controle **Permitir acesso aos serviços do Azure** como OFF.

    > [!IMPORTANT]
    > Se você deixar o controle definido como ON, o seu servidor de banco de dados SQL do Azure aceitará a comunicação de qualquer sub-rede, que pode ser acesso excessivo do ponto de vista da segurança. O recurso de ponto de extremidade de serviço de rede virtual do Microsoft Azure, em conjunto com o recurso de regra de rede virtual do Banco de dados SQL do Microsoft Azure, pode reduzir a área de superfície de segurança.

4. Clique no controle **+ Adicionar existente**, na seção **Redes virtuais**.

    ![Clique em Adicionar existente (ponto de extremidade de sub-rede, como uma regra SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. No novo painel **Criar/Atualizar**, preencha os controles com os nomes dos recursos do Azure.
 
    > [!TIP]
    > Você deve incluir o **prefixo de endereço** correto para a sua sub-rede. É possível encontrar o valor no portal. Navegue até **Todos os recursos** &gt; **Todos os tipos** &gt; **Redes virtuais**. O filtro exibe suas redes virtuais. Clique em sua rede virtual e, em seguida, clique em **Sub-redes**. A coluna **INTERVALO DE ENDEREÇOS** tem o prefixo de endereço de que você precisa.

    ![Preencha os campos para a nova regra.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Clique no botão **OK** perto da parte inferior do painel.

7.  Consulte a regra de rede virtual resultante no painel de firewall.

    ![Consulte a nova regra, no painel de firewall.][image-portal-firewall-vnet-result-rule-30-png]






<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Artigos relacionados

- [Usar o PowerShell para criar um ponto de extremidade de serviço de Rede virtual e, em seguida, uma regra de rede virtual para o banco de dados SQL do Azure][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Pontos de extremidade de serviço de rede virtual do Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Regras de firewall de nível do banco de dados e de nível do servidor de banco de dados SQL do Microsoft Azure][sql-db-firewall-rules-config-715d]

O recurso de pontos de extremidade de serviço de Rede virtual do Microsoft Azure e o recurso de regra de rede virtual para o banco de dados SQL do Azure, ambos se tornaram disponíveis em setembro de 2017.





<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png



<!-- Link references, to text, Within this same Github repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md#classic-deployment-characteristics

[expressroute-indexmd-744v]: ../expressroute/index.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.md



<!-- Link references, to text, Outside this Github repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/




<!-- ??2
#### Syntax related articles

- PowerShell cmdlets

- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->

