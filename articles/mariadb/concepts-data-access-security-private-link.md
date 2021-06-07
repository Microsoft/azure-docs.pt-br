---
title: Link privado-banco de dados do Azure para MariaDB
description: Saiba como o link privado funciona para o banco de dados do Azure para MariaDB.
author: mksuni
ms.author: sumuth
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: a1b97586eeeab1d5d1917f2d1cec9e0f71e9e329
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867450"
---
# <a name="private-link-for-azure-database-for-mariadb"></a>Link privado para o banco de dados do Azure para MariaDB

O link privado permite criar pontos de extremidade privados para o banco de dados do Azure para MariaDB e, portanto, traz os serviços do Azure dentro de sua VNet (rede virtual privada). O ponto de extremidade privado expõe um IP privado que você pode usar para se conectar ao seu banco de dados do Azure para o servidor de banco de dados MariaDB, assim como qualquer outro recurso na VNet.

Para obter uma lista dos serviços de PaaS que dão suporte à funcionalidade de link privado, examine a [documentação](../private-link/index.yml)do link privado. Um ponto de extremidade privado é um endereço IP privado em uma [VNET](../virtual-network/virtual-networks-overview.md) e sub-rede específicas.

> [!NOTE]
> O recurso de link privado só está disponível para o banco de dados do Azure para servidores MariaDB nos tipos de preço Uso Geral ou com otimização de memória. Verifique se o servidor de banco de dados está em um desses tipos de preço.

## <a name="data-exfiltration-prevention"></a>Prevenção contra exportação de dados

Data ex-filtragem no banco de dados do Azure para MariaDB é quando um usuário autorizado, como um administrador de banco de dados, é capaz de extrair dados de um sistema e movê-los para outro local ou sistema fora da organização. Por exemplo, o usuário move os dados para uma conta de armazenamento pertencente a terceiros.

Considere um cenário com um usuário executando o MariaDB Workbench dentro de uma VM do Azure conectando-se a uma instância do banco de dados do Azure para MariaDB. Essa instância do MariaDB está no data center do oeste dos EUA. O exemplo a seguir mostra como limitar o acesso com pontos de extremidade públicos no banco de dados do Azure para MariaDB usando controles de acesso à rede.

* Desabilite todo o tráfego de serviço do Azure para o banco de dados do Azure para MariaDB por meio do ponto de extremidade público, configurando permitir serviços do Azure para Certifique-se de que nenhum endereço IP ou intervalo tenha permissão para acessar o servidor por meio de [regras de firewall](concepts-firewall-rules.md) ou pontos de extremidade de [serviço de rede virtual](concepts-data-access-security-vnet.md).

* Permita o tráfego somente para o banco de dados do Azure para MariaDB usando o endereço IP privado da VM. Para obter mais informações, confira os artigos sobre o [ponto de extremidade de serviço](concepts-data-access-security-vnet.md) e as [regras de firewall da VNET](howto-manage-vnet-portal.md).

* Na VM do Azure, restrinja o escopo da conexão de saída usando NSGs (grupos de segurança de rede) e marcas de serviço da seguinte maneira:

    * Especifique uma regra de NSG para permitir o tráfego para a marca de serviço = SQL. Westus-permitindo somente a conexão com o banco de dados do Azure para MariaDB no oeste dos EUA
    * Especifique uma regra de NSG (com prioridade mais alta) para negar o tráfego para a marca de serviço = SQL negando conexões com o banco de dados MariaDB em todas as regiões</br></br>

No final desta configuração, a VM do Azure pode se conectar somente ao banco de dados do Azure para MariaDB na região oeste dos EUA. No entanto, a conectividade não é restrita a um único banco de dados do Azure para MariaDB. A VM ainda pode se conectar a qualquer banco de dados do Azure para MariaDB na região oeste dos EUA, incluindo os bancos que não fazem parte da assinatura. Embora tenhamos reduzido o escopo da exportação de dados no cenário acima a uma região específica, não o eliminamos por completo.</br>

Com o link privado, agora você pode configurar controles de acesso à rede como NSGs para restringir o acesso ao ponto de extremidade privado. Os recursos de PaaS individuais do Azure são então mapeados para pontos de extremidade privados específicos. Um insider mal-intencionado só pode acessar o recurso de PaaS mapeado (por exemplo, um banco de dados do Azure para MariaDB) e nenhum outro recurso.

## <a name="on-premises-connectivity-over-private-peering"></a>Conectividade local no emparelhamento privado

Quando você se conecta ao ponto de extremidade público de computadores locais, seu endereço IP precisa ser adicionado ao firewall baseado em IP usando uma regra de firewall de nível de servidor. Embora esse modelo funcione bem para permitir o acesso a computadores individuais para cargas de trabalho de desenvolvimento ou teste, é difícil gerenciá-lo em um ambiente de produção.

Com o link privado, você pode habilitar o acesso entre instalações ao ponto de extremidade privado usando o er ( [rota expressa](https://azure.microsoft.com/services/expressroute/) ), o emparelhamento privado ou o [túnel VPN](../vpn-gateway/index.yml). Em seguida, eles podem desabilitar todo o acesso por meio do ponto de extremidade público e não usar o firewall baseado em IP.

> [!NOTE]
> Em alguns casos, o Banco de Dados do Azure para MariaDB e a sub-rede da VNet estão em assinaturas diferentes. Nesses casos, você deve garantir as seguintes configurações:
> - Certifique-se de que a assinatura tenha o provedor de recursos **Microsoft. DBforMariaDB** registrado. Para obter mais informações, confira [resource-manager-registration][resource-manager-portal]

## <a name="configure-private-link-for-azure-database-for-mariadb"></a>Configurar o link privado para o banco de dados do Azure para MariaDB

### <a name="creation-process"></a>Processo de criação

Pontos de extremidade privados são necessários para habilitar o link privado. Isso pode ser feito usando os seguintes guias de instruções.

* [Azure portal](howto-configure-privatelink-portal.md)
* [CLI](howto-configure-privatelink-cli.md)

### <a name="approval-process"></a>Processo de aprovação

Depois que o administrador de rede cria o PE (ponto de extremidade particular), o administrador pode gerenciar o PEC (conexão de ponto de extremidade particular) para o banco de dados do Azure para MariaDB. Essa separação de tarefas entre o administrador de rede e o DBA é útil para o gerenciamento do banco de dados do Azure para conectividade MariaDB. 

* Navegue até o recurso de servidor do banco de dados do Azure para MariaDB no portal do Azure. 
    * Selecione as conexões de ponto de extremidade privado no painel esquerdo
    * Mostra uma lista de todas as conexões de ponto de extremidade privado (PECs)
    * Ponto de extremidade particular correspondente (PE) criado

![Selecione o portal de ponto de extremidade privado](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Selecione uma PEC individual na lista selecionando-a.

![Selecione a aprovação de ponto de extremidade particular pendente](media/concepts-data-access-and-security-private-link/select-private-link.png)

* O administrador do servidor MariaDB pode optar por aprovar ou rejeitar um PEC e, opcionalmente, adicionar uma resposta de texto curto.

![Selecione a mensagem de ponto de extremidade particular](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Após a aprovação ou rejeição, a lista refletirá o estado apropriado junto com o texto de resposta

![Selecione o estado final do ponto de extremidade privado](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mariadb"></a>Casos de uso de link privado para o banco de dados do Azure para MariaDB


Os clientes podem se conectar ao ponto de extremidade privado da mesma VNet, [rede virtual emparelhada](../virtual-network/virtual-network-peering-overview.md) na mesma região ou entre regiões, ou via [conexão vnet a vnet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) entre regiões. Além disso, os clientes podem se conectar localmente usando o ExpressRoute, o emparelhamento privado ou o túnel de VPN. Veja abaixo um diagrama simplificado que mostra os casos de uso comuns.

![Selecione a visão geral do ponto de extremidade privado](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Como se conectar por meio de uma VM do Azure na VNET (Rede Virtual) emparelhada
Configure o [emparelhamento VNet](../virtual-network/tutorial-connect-virtual-networks-powershell.md) para estabelecer a conectividade com o banco de dados do Azure para MariaDB de uma VM do Azure em uma VNet emparelhada.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Como se conectar por meio de uma VM do Azure no ambiente VNET a VNET
Configure a [conexão de gateway VPN de vnet para vnet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) para estabelecer a conectividade com um banco de dados do Azure para MariaDB de uma VM do Azure em uma região ou assinatura diferente.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Como se conectar por meio de um ambiente local pela VPN
Para estabelecer a conectividade de um ambiente local para o banco de dados do Azure para MariaDB, escolha e implemente uma das opções:

* [Conexão ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Conexão VPN site a site](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Circuito do ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

## <a name="private-link-combined-with-firewall-rules"></a>Link Privado combinado com regras de firewall

As seguintes situações e resultados são possíveis quando você usa o Link Privado em combinação com regras de firewall:

* Se você não configurar nenhuma regra de firewall, por padrão, nenhum tráfego será capaz de acessar o banco de dados do Azure para MariaDB.

* Se você configurar o tráfego público ou um ponto de extremidade de serviço e criar pontos de extremidade privados, os tipos diferentes de tráfego de entrada serão autorizados pelo tipo correspondente de regra de firewall.

* Se você não configurar nenhum tráfego público ou ponto de extremidade de serviço e criar pontos de extremidades privados, o banco de dados do Azure para MariaDB será acessível somente por meio dos pontos de extremidade privados. Se você não configurar o tráfego público ou um ponto de extremidade de serviço, depois que todos os pontos de extremidades particulares aprovados forem rejeitados ou excluídos, nenhum tráfego poderá acessar o banco de dados do Azure para MariaDB.

## <a name="deny-public-access-for-azure-database-for-mariadb"></a>Negar acesso público para o banco de dados do Azure para MariaDB

Se você quiser contar completamente apenas com pontos de extremidade privados para acessar o banco de dados do Azure para MariaDB, você pode desabilitar a configuração de todos os pontos de extremidade públicos ([regras de firewall](concepts-firewall-rules.md) e pontos de extremidade de [serviço de VNet](concepts-data-access-security-vnet.md)) definindo a configuração de **acesso de rede pública Deny** no servidor de banco de dados. 

Quando essa configuração é definida como *Sim*, somente as conexões por meio de pontos de extremidade privados são permitidas para o banco de dados do Azure para MariaDB. Quando essa configuração é definida como *não*, os clientes podem se conectar ao banco de dados do Azure para MariaDB com base em suas configurações de firewall ou ponto de extremidade de serviço de VNet. Além disso, quando o valor do acesso à rede privada é definido, os clientes não podem adicionar e/ou atualizar as existentes ' regras de firewall ' e ' regras de ponto de extremidade de serviço de VNet '.

> [!Note]
> Esse recurso está disponível em todas as regiões do Azure em que o banco de dados do Azure para PostgreSQL-o servidor único dá suporte a tipos de preço Uso Geral e com otimização de memória.
>
> Essa configuração não tem nenhum impacto sobre as configurações de SSL e TLS para o banco de dados do Azure para MariaDB.

Para saber como definir o **acesso de rede pública Deny** para seu banco de dados do Azure para MariaDB do portal do Azure, consulte [como configurar o acesso de rede pública Deny](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os recursos de segurança do banco de dados do Azure para MariaDB, consulte os seguintes artigos:

* Para configurar um firewall para o banco de dados do Azure para MariaDB, consulte [suporte a firewall](concepts-firewall-rules.md).

* Para saber como configurar um ponto de extremidade de serviço de rede virtual para o banco de dados do Azure para MariaDB, consulte [Configurar o acesso de redes virtuais](concepts-data-access-security-vnet.md).

* Para obter uma visão geral do banco de dados do Azure para conectividade MariaDB, consulte [arquitetura de conectividade do banco de dados do Azure para MariaDB](concepts-connectivity-architecture.md)

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
