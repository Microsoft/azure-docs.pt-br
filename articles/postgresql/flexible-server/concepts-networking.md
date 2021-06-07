---
title: Visão geral de rede – banco de dados do Azure para PostgreSQL – servidor flexível
description: Saiba mais sobre as opções de conectividade e rede na opção de implantação de servidor flexível para o banco de dados do Azure para PostgreSQL
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/21/2021
ms.openlocfilehash: a6f049670a6860bbc195b92458945d1a53029b4f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732795"
---
# <a name="networking-overview---azure-database-for-postgresql---flexible-server"></a>Visão geral de rede – banco de dados do Azure para PostgreSQL – servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Este artigo descreve os conceitos de conectividade e rede para o banco de dados do Azure para PostgreSQL-servidor flexível. 

## <a name="choosing-a-networking-option"></a>Escolhendo uma opção de rede
Você tem duas opções de rede para o servidor do banco de dados do Azure para PostgreSQL-flexível. As opções são **acesso privado (integração de VNet)** e **acesso público (endereços IP permitidos)** . Na criação do servidor, você deve escolher uma opção. 

> [!NOTE]
> A opção de rede não pode ser alterada depois que o servidor é criado. 

* **Acesso privado (integração de VNet)** – você pode implantar seu servidor flexível em sua [Rede Virtual do Azure](../../virtual-network/virtual-networks-overview.md). As redes virtuais do Azure fornecem comunicação de rede privada e segura. Os recursos em uma rede virtual podem se comunicar por meio de endereços IP privados.

   Escolha a opção de Integração de VNet se desejar as seguintes funcionalidades:
   * Conectar-se de recursos do Azure na mesma rede virtual com seu servidor flexível usando endereços IP privados
   * Usar a VPN ou o ExpressRoute para se conectar de recursos que não são do Azure com seu servidor flexível
   * O servidor flexível não tem nenhum ponto de extremidade público

* **Acesso público (endereços IP permitidos)** – seu servidor flexível é acessado por meio de um ponto de extremidade público. O ponto de extremidade público é um endereço DNS que poderia ser resolvido publicamente. A frase "endereços IP permitidos" refere-se a um intervalo de IPs que você escolhe para conceder permissão para acessar o servidor. Essas permissões são chamadas **regras de firewall**. 

   Escolha o método de acesso público se desejar os seguintes recursos:
   * Conectar-se de recursos do Azure que não dão suporte a redes virtuais
   * Conectar-se de recursos fora de um Azure que não estão conectados por VPN ou ExpressRoute 
   * O servidor flexível tem um ponto de extremidade público

As seguintes características se aplicam se você optar por usar o acesso privado ou a opção de acesso público:
* Conexões de endereços IP permitidos precisam ser autenticadas no servidor PostgreSQL com credenciais válidas
* A [criptografia de conexão](#tls-and-ssl) está disponível para o tráfego de rede
* O servidor tem um FQDN (nome de domínio totalmente qualificado). Para a propriedade hostname em cadeias de conexão, é recomendável usar o FQDN em vez de um endereço IP.
* Ambas as opções controlam o acesso no nível do servidor, não no nível do banco de dados ou da tabela. Você usaria as propriedades de funções do PostgreSQL para controlar o banco de dados, a tabela e o acesso a outros objetos.


## <a name="private-access-vnet-integration"></a>Acesso privado (Integração VNet)
O acesso privado com integração de rede virtual (vnet) fornece comunicação privada e segura para seu servidor flexível do PostgreSQL.

### <a name="virtual-network-concepts"></a>Conceitos de rede virtual
Aqui estão alguns conceitos a serem familiarizados com o uso de redes virtuais com servidores flexíveis PostgreSQL.

* **Rede virtual** – uma VNet (rede virtual) do Azure contém um espaço de endereço IP privado configurado para seu uso. Visite a [visão geral da rede virtual do Azure](../../virtual-network/virtual-networks-overview.md) para saber mais sobre a rede virtual do Azure.

    Sua rede virtual deve estar na mesma região do Azure que o seu servidor flexível.


* **Sub-rede delegada** – uma rede virtual contém sub-redes (sub-redes). As sub-redes permitem segmentar sua rede virtual em espaços de endereço menores. Os recursos do Azure são implantados em sub-redes específicas em uma rede virtual. 

   Seu servidor flexível PostgreSQL deve estar em uma sub-rede que é **delegada** somente para uso do servidor flexível PostgreSQL. Essa delegação significa que somente os servidores flexíveis do banco de dados do Azure para PostgreSQL podem usar essa sub-rede. Nenhum outro tipo de recurso do Azure pode estar na sub-rede delegada. Você delega uma sub-rede atribuindo sua propriedade de delegação como Microsoft. DBforPostgreSQL/flexibleServers.

   Adicione `Microsoft.Storage` ao ponto de extremidade de serviço para a sub-rede delegada a servidores flexíveis. 

* **NSG (grupos de segurança de rede)** As regras de segurança nos grupos de segurança de rede permitem filtrar o tipo de tráfego de rede que pode fluir para dentro e para fora das sub-redes e interfaces de rede da rede virtual. Examine a [visão geral do grupo de segurança de rede](../../virtual-network/network-security-groups-overview.md) para obter mais informações.


### <a name="unsupported-virtual-network-scenarios"></a>Cenários de rede virtual sem suporte
* Ponto de extremidade público (ou IP público ou DNS)-um servidor flexível implantado em uma rede virtual não pode ter um ponto de extremidade público
* Depois que o servidor flexível for implantado em uma rede virtual e sub-rede, você não poderá movê-lo para outra rede virtual ou sub-rede. Não é possível mover a rede virtual para outro grupo de recursos ou assinatura.
* O tamanho da sub-rede (espaços de endereço) não pode ser aumentado após a existência de recursos na sub-rede
* Não há suporte para o emparelhamento de VNets entre regiões

Saiba como criar um servidor flexível com acesso privado (integração VNet) no [portal do Azure](how-to-manage-virtual-network-portal.md) ou [no CLI do Azure](how-to-manage-virtual-network-cli.md).

> [!NOTE]
> Se você estiver usando o servidor DNS personalizado, deverá usar um encaminhador DNS para resolver o FQDN do servidor do banco de dados do Azure para PostgreSQL-flexível. Consulte a [resolução de nomes que usa seu próprio servidor DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) para saber mais.

## <a name="public-access-allowed-ip-addresses"></a>Acesso público (endereços IP permitidos)
As características do método de acesso público incluem:
* Somente os endereços IP que você permitir terão permissão para acessar seu servidor flexível PostgreSQL. Por padrão, nenhum endereço IP é permitido. Você pode adicionar endereços IP durante a criação do servidor ou depois.
* O servidor PostgreSQL tem um nome DNS que possa ser resolvido publicamente
* Seu servidor flexível não está em uma de suas redes virtuais do Azure
* O tráfego de rede de e para o servidor não passa por uma rede privada. O tráfego usa os caminhos gerais da Internet.

### <a name="firewall-rules"></a>Regras de firewall
A concessão de permissão a um endereço IP é chamada de regra de firewall. Se uma tentativa de conexão vier de um endereço IP que você não tenha permitido, o cliente de origem verá um erro.

Saiba como criar um servidor flexível com acesso público (endereços IP permitidos) no [portal do Azure](how-to-manage-firewall-portal.md) ou [no CLI do Azure](how-to-manage-firewall-cli.md).


### <a name="allowing-all-azure-ip-addresses"></a>Permitindo todos os endereços IP do Azure
Se um endereço IP de saída fixo não estiver disponível para seu serviço do Azure, você poderá considerar a possibilidade de habilitar conexões de todos os endereços IP do datacenter do Azure.

> [!IMPORTANT]
> A opção **permitir acesso público dos serviços e recursos do Azure no Azure** configura o firewall para permitir todas as conexões do Azure, incluindo conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.

### <a name="troubleshooting-public-access-issues"></a>Solucionando problemas de acesso público
Considere os seguintes pontos quando o acesso ao Banco de Dados do Microsoft Azure para o serviço de servidor PostgreSQL não se comportar conforme o esperado:

* **As alterações à lista de permissões ainda não entraram em vigor:** pode ocorrer um atraso de cinco minutos para que as alterações na configuração do firewall do Banco de Dados do Azure para servidor PostgreSQL entrem em vigor.

* **Falha na autenticação:** Se um usuário não tiver permissões no servidor do banco de dados do Azure para PostgreSQL ou se a senha usada estiver incorreta, a conexão com o banco de dados do Azure para servidor PostgreSQL será negada. A criação de uma configuração de firewall apenas fornece aos clientes uma oportunidade de tentar se conectar ao servidor. Cada cliente ainda deve fornecer as credenciais de segurança necessárias.

* **Endereço IP do cliente dinâmico:** Se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver tendo problemas para passar pelo firewall, poderá tentar uma das seguintes soluções:

   * Peça ao seu Provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos computadores clientes que acessarão o servidor de Banco de Dados do Azure para servidor PostgreSQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.
   * Obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP estáticos como uma regra de firewall.

* **A regra de firewall não está disponível para o formato IPv6:** As regras de firewall devem estar no formato IPv4. Se você especificar regras de firewall no formato IPv6, ele mostrará o erro de validação.

## <a name="hostname"></a>Nome do host
Independentemente da opção de rede que você escolher, é recomendável usar sempre um FQDN (nome de domínio totalmente qualificado) como nome do host ao se conectar ao seu servidor flexível. Não há garantia de que o endereço IP do servidor permaneça estático. Usar o FQDN ajudará você a evitar fazer alterações na cadeia de conexão. 

Exemplo
* Aconselhável `hostname = servername.postgres.database.azure.com`
* Sempre que possível, evite usar `hostname = 10.0.0.4` (um endereço privado) ou `hostname = 40.2.45.67` (um endereço público)


## <a name="tls-and-ssl"></a>TLS e SSL
Banco de dados do Azure para PostgreSQL-o servidor flexível dá suporte à conexão de aplicativos cliente ao serviço PostgreSQL usando TLS (Transport Layer Security). O TLS é um protocolo padrão do setor que garante conexões de rede criptografadas entre o servidor de banco de dados e os aplicativos cliente. O TLS é um protocolo atualizado do SSL (protocolo SSL).

Banco de dados do Azure para PostgreSQL-o servidor flexível só dá suporte a conexões criptografadas usando a segurança da camada de transporte. Todas as conexões de entrada com TLS 1,0 e TLS 1,1 serão negadas. 

## <a name="next-steps"></a>Próximas etapas
* Saiba como criar um servidor flexível com **acesso privado (integração VNet)** no [portal do Azure](how-to-manage-virtual-network-portal.md) ou [no CLI do Azure](how-to-manage-virtual-network-cli.md).
* Saiba como criar um servidor flexível com **acesso público (endereços IP permitidos)** no [portal do Azure](how-to-manage-firewall-portal.md) ou [no CLI do Azure](how-to-manage-firewall-cli.md).
