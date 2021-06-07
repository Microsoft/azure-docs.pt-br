---
title: Visão geral de rede-banco de dados do Azure para MySQL servidor flexível
description: Saiba mais sobre as opções de conectividade e rede na opção de implantação de servidor flexível para o banco de dados do Azure para MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: ec835073a1fe447490f6965fe41478319a47f503
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105106829"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Conceitos de conectividade e rede para o banco de dados do Azure para MySQL – servidor flexível (versão prévia)

Este artigo descreve as opções de conectividade pública e privada para seu servidor. Você aprenderá detalhadamente os conceitos de rede para o banco de dados do Azure para MySQL servidor flexível para criar um servidor com segurança no Azure.

> [!IMPORTANT]
> Banco de dados do Azure para MySQL-o servidor flexível está em versão prévia.

## <a name="choosing-a-networking-option"></a>Escolhendo uma opção de rede
Você tem duas opções de rede para o servidor flexível do banco de dados do Azure para MySQL. As opções são **acesso privado (integração de VNet)** e **acesso público (endereços IP permitidos)** . Na criação do servidor, você deve escolher uma opção. 

> [!NOTE]
> A opção de rede não pode ser alterada depois que o servidor é criado. 

* **Acesso privado (integração de VNet)** – você pode implantar seu servidor flexível em sua [Rede Virtual do Azure](../../virtual-network/virtual-networks-overview.md). As redes virtuais do Azure fornecem comunicação de rede privada e segura. Os recursos em uma rede virtual podem se comunicar por meio de endereços IP privados.

   Escolha a opção de Integração de VNet se desejar as seguintes funcionalidades:
   * Conecte-se dos recursos do Azure na mesma rede virtual ou [rede virtual emparelhada](../../virtual-network/virtual-network-peering-overview.md) ao seu servidor flexível
   * Usar a VPN ou o ExpressRoute para se conectar de recursos que não são do Azure com seu servidor flexível
   * Nenhum ponto de extremidade público

* **Acesso público (endereços IP permitidos)** – seu servidor flexível é acessado por meio de um ponto de extremidade público. O ponto de extremidade público é um endereço DNS que poderia ser resolvido publicamente. A frase "endereços IP permitidos" refere-se a um intervalo de IPs que você escolhe para conceder permissão para acessar o servidor. Essas permissões são chamadas **regras de firewall**. 

   Escolha o método de acesso público se desejar os seguintes recursos:
   * Conectar-se de recursos do Azure que não dão suporte a redes virtuais
   * Conectar-se de recursos fora de um Azure que não estão conectados por VPN ou ExpressRoute 
   * O servidor flexível tem um ponto de extremidade público

As seguintes características se aplicam se você optar por usar o acesso privado ou a opção de acesso público:
* Conexões de endereços IP permitidos precisam ser autenticadas no servidor MySQL com credenciais válidas
* A [criptografia de conexão](#tls-and-ssl) está disponível para o tráfego de rede
* O servidor tem um FQDN (nome de domínio totalmente qualificado). Para a propriedade hostname em cadeias de conexão, é recomendável usar o FQDN em vez de um endereço IP.
* Ambas as opções controlam o acesso no nível do servidor, não no nível do banco de dados ou da tabela. Você usaria as propriedades de funções do MySQL para controlar o banco de dados, a tabela e o acesso a outros objetos.


## <a name="private-access-vnet-integration"></a>Acesso privado (Integração VNet)
O acesso privado com integração de rede virtual (vnet) fornece comunicação privada e segura para o servidor flexível do MySQL.

### <a name="virtual-network-concepts"></a>Conceitos de rede virtual
Aqui estão alguns conceitos que devem estar familiarizados ao usar redes virtuais com servidores flexíveis MySQL.

* **Rede virtual** – uma VNet (rede virtual) do Azure contém um espaço de endereço IP privado configurado para seu uso. Visite a [visão geral da rede virtual do Azure](../../virtual-network/virtual-networks-overview.md) para saber mais sobre a rede virtual do Azure.

    Sua rede virtual deve estar na mesma região do Azure que o seu servidor flexível.

* **Sub-rede delegada** – uma rede virtual contém sub-redes (sub-redes). As sub-redes permitem segmentar sua rede virtual em espaços de endereço menores. Os recursos do Azure são implantados em sub-redes específicas em uma rede virtual. 

   O servidor flexível do MySQL deve estar em uma sub-rede **delegada** somente para uso do servidor flexível do MySQL. Essa delegação significa que somente os servidores flexíveis do banco de dados do Azure para MySQL podem usar essa sub-rede. Nenhum outro tipo de recurso do Azure pode estar na sub-rede delegada. Você delega uma sub-rede atribuindo sua propriedade de delegação como Microsoft. DBforMySQL/flexibleServers.

* **NSG (grupos de segurança de rede)** As regras de segurança nos grupos de segurança de rede permitem filtrar o tipo de tráfego de rede que pode fluir para dentro e para fora das sub-redes e interfaces de rede da rede virtual. Examine a [visão geral do grupo de segurança de rede](../../virtual-network/network-security-groups-overview.md) para obter mais informações.

* **Emparelhamento de rede virtual** O emparelhamento de rede virtual permite que você conecte diretamente duas ou mais redes virtuais no Azure. As redes virtuais emparelhadas aparecem como uma para fins de conectividade. O tráfego entre máquinas virtuais em redes virtuais emparelhadas usa a infraestrutura de backbone da Microsoft. O tráfego entre o aplicativo cliente e o servidor flexível no VNets emparelhado é roteado somente pela rede privada da Microsoft e é isolado somente nessa rede.

O servidor flexível dá suporte ao emparelhamento de rede virtual na mesma região do Azure. **Não há suporte para** o emparelhamento de VNets entre regiões. Examine os [conceitos de emparelhamento de rede virtual](../../virtual-network/virtual-network-peering-overview.md) para obter mais informações.

### <a name="connecting-from-peered-vnets-in-same-azure-region"></a>Conectando de VNets emparelhadas na mesma região do Azure
Se o aplicativo cliente que está tentando se conectar ao servidor flexível estiver na rede virtual emparelhada, ele poderá não ser capaz de se conectar usando o ServerName do servidor flexível, pois ele não pode resolver o nome DNS para o servidor flexível da VNet emparelhada. Há duas opções para resolver isso:
* Usar o endereço IP privado (recomendado para o cenário de desenvolvimento/teste) – essa opção pode ser usada para fins de teste ou testes. Você pode usar o nslookup para reverter a pesquisa do endereço IP privado para seu ServerName flexível (nome de domínio totalmente qualificado) e usar o endereço IP privado para se conectar do aplicativo cliente. O uso do endereço IP privado para conexão com o servidor flexível não é recomendado para uso em produção, pois pode ser alterado durante o evento planejado ou não planejado.
* Usar zona de DNS privado (recomendado para produção)-essa opção é adequada para fins de produção. Você provisiona uma [zona DNS privada](../../dns/private-dns-getstarted-portal.md) e a vincula à rede virtual do cliente. Na zona DNS privada, você adiciona um [registro a](../../dns/dns-zones-records.md#record-types) para seu servidor flexível usando seu endereço IP privado. Em seguida, você pode usar o registro a para se conectar do aplicativo cliente na rede virtual emparelhada ao servidor flexível.

### <a name="connecting-from-on-premises-to-flexible-server-in-virtual-network-using-expressroute-or-vpn"></a>Conectando de local para servidor flexível na rede virtual usando ExpressRoute ou VPN
Para cargas de trabalho que exigem acesso ao servidor flexível na rede virtual da rede local, você precisará do [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute/) ou [VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn/) e da rede virtual [conectada ao local](/azure/architecture/reference-architectures/hybrid-networking/). Com essa configuração em vigor, você precisará de um encaminhador DNS para resolver o nome do arquivo flexível se quiser se conectar do aplicativo cliente (como o MySQL Workbench) em execução na rede virtual local. Esse encaminhador DNS é responsável por resolver todas as consultas DNS por meio de um encaminhador de nível de servidor para o serviço DNS fornecido pelo Azure [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md).

Para configurar corretamente, você precisa dos seguintes recursos:

- Rede local
- Servidor flexível do MySQL provisionado com acesso privado (integração VNet)
- Rede virtual [conectada ao local](/azure/architecture/reference-architectures/hybrid-networking/)
- Usar o encaminhador DNS [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) implantado no Azure

Você pode usar o nome do servidor flexível (FQDN) para se conectar do aplicativo cliente na rede virtual emparelhada ou na rede local para o servidor flexível.

### <a name="unsupported-virtual-network-scenarios"></a>Cenários de rede virtual sem suporte
* Ponto de extremidade público (ou IP público ou DNS)-um servidor flexível implantado em uma rede virtual não pode ter um ponto de extremidade público
* Depois que o servidor flexível for implantado em uma rede virtual e sub-rede, você não poderá movê-lo para outra rede virtual ou sub-rede. Não é possível mover a rede virtual para outro grupo de recursos ou assinatura.
* O tamanho da sub-rede (espaços de endereço) não pode ser aumentado após a existência de recursos na sub-rede
* Não há suporte para o emparelhamento de VNets entre regiões

Saiba como habilitar o acesso privado (integração vnet) usando o [portal do Azure](how-to-manage-virtual-network-portal.md) ou [CLI do Azure](how-to-manage-virtual-network-cli.md).

> [!NOTE]
> Se você estiver usando o servidor DNS personalizado, deverá usar um encaminhador DNS para resolver o FQDN do servidor do banco de dados do Azure para MySQL-flexível. Consulte a [resolução de nomes que usa seu próprio servidor DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) para saber mais.

## <a name="public-access-allowed-ip-addresses"></a>Acesso público (endereços IP permitidos)
As características do método de acesso público incluem:
* Somente os endereços IP que você permitir terão permissão para acessar o servidor flexível do MySQL. Por padrão, nenhum endereço IP é permitido. Você pode adicionar endereços IP durante a criação do servidor ou depois.
* O servidor MySQL tem um nome DNS que possa ser resolvido publicamente
* Seu servidor flexível não está em uma de suas redes virtuais do Azure
* O tráfego de rede de e para o servidor não passa por uma rede privada. O tráfego usa os caminhos gerais da Internet.

### <a name="firewall-rules"></a>Regras de firewall
A concessão de permissão a um endereço IP é chamada de regra de firewall. Se uma tentativa de conexão vier de um endereço IP que você não tenha permitido, o cliente de origem verá um erro.


### <a name="allowing-all-azure-ip-addresses"></a>Permitindo todos os endereços IP do Azure
Se um endereço IP de saída fixo não estiver disponível para seu serviço do Azure, você poderá considerar a possibilidade de habilitar conexões de todos os endereços IP do datacenter do Azure.

> [!IMPORTANT]
> A opção **permitir acesso público dos serviços e recursos do Azure no Azure** configura o firewall para permitir todas as conexões do Azure, incluindo conexões das assinaturas de outros clientes. Ao selecionar essa opção, verifique se as permissões de logon e de usuário limitam o acesso somente a usuários autorizados.

Saiba como habilitar e gerenciar o acesso público (endereços IP permitidos) usando o [portal do Azure](how-to-manage-firewall-portal.md) ou [CLI do Azure](how-to-manage-firewall-cli.md).


### <a name="troubleshooting-public-access-issues"></a>Solucionando problemas de acesso público
Considere os seguintes pontos quando o acesso ao banco de dados do Microsoft Azure para o serviço do servidor MySQL não se comportar conforme o esperado:

* **As alterações à lista de permissões ainda não entraram em vigor:** pode ocorrer um atraso de cinco minutos para que as alterações na configuração do firewall do Banco de Dados do Azure para servidor MySQL entrem em vigor.

* **Falha na autenticação:** Se um usuário não tiver permissões no servidor do banco de dados do Azure para MySQL ou se a senha usada estiver incorreta, a conexão com o banco de dados do Azure para servidor MySQL será negada. A criação de uma configuração de firewall apenas fornece aos clientes uma oportunidade de tentar se conectar ao servidor. Cada cliente ainda deve fornecer as credenciais de segurança necessárias.

* **Endereço IP do cliente dinâmico:** Se você tiver uma conexão com a Internet com endereçamento IP dinâmico e estiver tendo problemas para passar pelo firewall, poderá tentar uma das seguintes soluções:

   * Pergunte ao seu provedor de serviços de Internet (ISP) o intervalo de endereços IP atribuído aos computadores cliente que acessam o servidor do banco de dados do Azure para MySQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.
   * Obtenha o endereçamento IP estático para os computadores cliente e adicione os endereços IP estáticos como uma regra de firewall.
  
* **A regra de firewall não está disponível para o formato IPv6:** As regras de firewall devem estar no formato IPv4. Se você especificar regras de firewall no formato IPv6, ele mostrará o erro de validação.


## <a name="hostname"></a>Nome do host
Independentemente da opção de rede que você escolher, é recomendável usar sempre um FQDN (nome de domínio totalmente qualificado) como nome do host ao se conectar ao seu servidor flexível. Não há garantia de que o endereço IP do servidor permaneça estático. Usar o FQDN ajudará você a evitar fazer alterações na cadeia de conexão. 

Exemplo
* Aconselhável `hostname = servername.mysql.database.azure.com`
* Sempre que possível, evite usar `hostname = 10.0.0.4` (um endereço privado) ou `hostname = 40.2.45.67` (um IP público)


## <a name="tls-and-ssl"></a>TLS e SSL
O servidor flexível do banco de dados do Azure para MySQL oferece suporte à conexão de aplicativos cliente com o serviço MySQL usando TLS (Transport Layer Security). O TLS é um protocolo padrão do setor que garante conexões de rede criptografadas entre o servidor de banco de dados e os aplicativos cliente. O TLS é um protocolo atualizado de protocolo SSL (SSL).

O banco de dados do Azure para MySQL servidor flexível só dá suporte a conexões criptografadas usando o protocolo TLS 1,2. Todas as conexões de entrada com o TLS 1.0 e o TLS 1.1 serão negadas. Não é possível desabilitar ou alterar a versão do TLS para conectar-se ao banco de dados do Azure para MySQL servidor flexível. Examine como [se conectar usando SSL/TLS](how-to-connect-tls-ssl.md) para saber mais. 


## <a name="next-steps"></a>Próximas etapas
* Saiba como habilitar o acesso privado (integração vnet) usando o [portal do Azure](how-to-manage-virtual-network-portal.md) ou a [CLI do Azure](how-to-manage-virtual-network-cli.md)
* Saiba como habilitar o acesso público (endereços IP permitidos) usando o [portal do Azure](how-to-manage-firewall-portal.md) ou a [CLI do Azure](how-to-manage-firewall-cli.md)
* Saiba como [usar o TLS](how-to-connect-tls-ssl.md)
