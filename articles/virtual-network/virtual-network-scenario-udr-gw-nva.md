---
title: Conexão híbrida com o aplicativo de 2 camadas | Microsoft Docs
description: Aprenda a implantar dispositivos virtuais e UDR para criar um ambiente de aplicativo de várias camadas no Azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: carmonm
editor: tysonn
ms.assetid: 1f509bec-bdd1-470d-8aa4-3cf2bb7f6134
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2016
ms.author: kumud
ms.openlocfilehash: c959ee3bea24955e3281feb9db66e4e0cadc8bf9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61034111"
---
# <a name="virtual-appliance-scenario"></a>Cenário de dispositivo virtual
Um cenário comum entre os maiores clientes do Azure é a necessidade de fornecer um aplicativo de duas camadas exposto à Internet, permitindo acesso para a camada traseira de um datacenter local. Este documento explica um cenário usando UDR (Rotas Definidas pelo Usuário), um Gateway de VPN e dispositivos virtuais de rede para implantar um ambiente de duas camadas que atende aos seguintes requisitos:

* O aplicativo Web deve ser acessível somente pela Internet pública.
* O servidor Web que hospeda o aplicativo deve ser capaz de acessar um servidor de aplicativos back-end.
* Todo o tráfego da Internet para o aplicativo Web deve passar por um dispositivo virtual de firewall. Este dispositivo virtual será usado apenas para o tráfego de Internet.
* Todo o tráfego indo para o servidor de aplicativos deve passar por um dispositivo virtual de firewall. Este dispositivo virtual será usado para acesso ao servidor de back-end e para acesso proveniente da rede local por meio de um Gateway de VPN.
* Os administradores devem ser capazes de gerenciar os dispositivos virtuais de firewall em seus computadores locais usando um terceiro dispositivo virtual de firewall usado exclusivamente para fins de gerenciamento.

Esse é um cenário de DMZ padrão com um DMZ e uma rede protegida. Esse cenário pode ser criado no Azure usando NSGs, dispositivos virtuais de firewall ou uma combinação de ambos. A tabela a seguir mostra alguns dos prós e contras entre NSGs e dispositivos virtuais de firewall.

|  | Prós | Contras |
| --- | --- | --- |
| NSG |Sem custo. <br/>Integrado ao RBAC do Azure. <br/>As regras podem ser criadas em modelos Aa ARM. |A complexidade pode variar em ambientes maiores. |
| Firewall |Controle total sobre o plano de dados. <br/>Gerenciamento central por meio do console do firewall. |Custo do dispositivo de firewall. <br/>Não é integrado ao RBAC do Azure. |

A solução a seguir usa dispositivos virtuais do firewall para implementar um cenário de rede DMZ/protegido.

## <a name="considerations"></a>Considerações
Você pode implantar o ambiente explicado anteriormente no Azure usando da seguinte maneira diferentes recursos disponíveis hoje.

* **Rede virtual (VNet)**. Uma VNet do Azure funciona de maneira semelhante a uma rede local e pode ser segmentada em uma ou mais sub-redes para fornecer isolamento de tráfego e separação de preocupações.
* **Dispositivo virtual**. Vários parceiros fornecem dispositivos virtuais no Azure Marketplace que podem ser usados para os três firewalls descritos acima. 
* **UDR (Rotas Definidas pelo Usuário)**. As tabelas de rotas podem conter UDRs usadas pela rede do Azure para controlar o fluxo de pacotes em uma VNet. Essas tabelas de rotas podem ser aplicadas a sub-redes. Um dos recursos mais recentes no Azure é a capacidade de aplicar uma tabela de rotas para o GatewaySubnet, fornecendo a capacidade de encaminhar todo o tráfego de entrada da VNet do Azure de uma conexão híbrida para um dispositivo virtual.
* **Encaminhamento IP**. Por padrão, o mecanismo de rede do Azure encaminhará pacotes a NICs (placas de interface de rede virtual) somente se o endereço IP de destino do pacote corresponder ao endereço IP da NIC. Portanto, se uma UDR definir que um pacote deverá ser enviado para um determinado dispositivo virtual, o mecanismo de rede do Azure deverá remover esse pacote. Para garantir que o pacote seja entregue a uma VM (no caso, um dispositivo virtual) que não seja o destino real do pacote, você precisará habilitar o encaminhamento IP para o dispositivo virtual.
* **NSGs (Grupos de Segurança de Rede)**. O exemplo a seguir não faz uso de NSGs, mas você pode usar os NSGs aplicados às sub-redes e/ou NICs nesta solução para filtrar ainda mais o tráfego de entrada e saída dessas NICs e sub-redes.

![Conectividade IPv6](./media/virtual-network-scenario-udr-gw-nva/figure01.png)

Neste exemplo, há uma assinatura que contém o seguinte:

* 2 grupos de recursos, não mostrados no diagrama. 
  * **ONPREMRG**. Contém todos os recursos necessários para simular uma rede local.
  * **AZURERG**. Contém todos os recursos necessários para o ambiente de rede virtual do Azure. 
* Uma VNet denominada **onpremvnet** usada para imitar um datacenter local segmentado como listado abaixo.
  * **onpremsn1**. A sub-rede que contém uma VM (máquina virtual) executando o Ubuntu para simular um servidor local.
  * **onpremsn2**. A sub-rede que contém uma VM executando o Ubuntu para simular um computador local usado por um administrador.
* Há um dispositivo virtual de firewall denominado **OPFW** em **onpremvnet** usado para manter um túnel para **azurevnet**.
* Uma VNet denominada **azurevnet** segmentada como listado abaixo.
  * **azsn1**. A sub-rede de firewall externo usada exclusivamente para o firewall externo. Todo o tráfego da Internet será proveniente desta sub-rede. Essa sub-rede contém apenas uma NIC associada ao firewall externo.
  * **azsn2**. A sub-rede de front-end hospeda uma VM em execução como um servidor Web que será acessado da Internet.
  * **azsn3**. A sub-rede de back-end hospeda uma VM que executa um servidor de aplicativos de back-end que será acessado pelo servidor Web de front-end.
  * **azsn4**. A sub-rede de gerenciamento usada exclusivamente para fornecer acesso de gerenciamento para todos os dispositivos virtuais do firewall. Essa sub-rede contém apenas uma NIC para cada dispositivo virtual de firewall usado na solução.
  * **GatewaySubnet**. A sub-rede de conexão híbrida do Azure necessária para o Gateway de VPN e o ExpressRoute para fornecer conectividade entre VNets do Azure e outras redes. 
* Há 3 dispositivos virtuais de firewall na rede **azurevnet** . 
  * **AZF1**. Firewall externo exposto à Internet pública usando um recurso de endereço IP público do Azure. Você precisa garantir que tem um modelo do Marketplace, ou diretamente do seu fornecedor de dispositivo, que provisiona um dispositivo virtual de 3 NICs.
  * **AZF2**. Firewall interno usado para controlar o tráfego entre **azsn2** e **azsn3**. Isso também é um dispositivo virtual de 3 NICs.
  * **AZF3**. O firewall de gerenciamento acessível aos administradores do datacenter local e conectado a uma sub-rede de gerenciamento usada para gerenciar todos os dispositivos de firewall. Você pode encontrar modelos de dispositivo virtual de 2 NICs no Marketplace ou solicitar uma diretamente do seu fornecedor de dispositivo.

## <a name="user-defined-routing-udr"></a>Roteamento definido pelo usuário (UDR)
Cada sub-rede no Azure pode ser vinculada a uma tabela UDR usada para definir como o tráfego iniciado na sub-rede é roteado. Se nenhum UDR for definido, o Azure usará as rotas padrão para permitir que o tráfego flua de uma sub-rede para outra. Para entender melhor os UDRs, visite [O que são Rotas Definidas pelo Usuário e Encaminhamento de IP](virtual-networks-udr-overview.md).

Para garantir a comunicação seja feita por meio do dispositivo de firewall correto, com base no último requisito acima, você precisará criar a seguinte tabela de rotas contendo UDRs em **azurevnet**.

### <a name="azgwudr"></a>azgwudr
Nesse cenário, o único tráfego fluindo do local para o Azure será usado para gerenciar os firewalls se conectando ao **AZF3** e esse tráfego deve passar pelo firewall interno, **AZF2**. Portanto, é necessária apenas uma rota no **GatewaySubnet** conforme mostrado abaixo.

| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.4.0/24 |10.0.3.11 |Permite que tráfego local alcance o gerenciamento de firewall **AZF3** |

### <a name="azsn2udr"></a>azsn2udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.3.0/24 |10.0.2.11 |Permite tráfego para a sub-rede de back-end que hospeda o servidor de aplicativos por meio do **AZF2** |
| 0.0.0.0/0 |10.0.2.10 |Permite que todos os outros tráfegos sejam roteados por meio do **AZF1** |

### <a name="azsn3udr"></a>azsn3udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.2.0/24 |10.0.3.10 |Permite que o tráfego **azsn2** ao flua do servidor de aplicativos para o servidor Web por meio do **AZF2** |

Você também precisa criar tabelas de rotas para as sub-redes em **onpremvnet** simularem o datacenter local.

### <a name="onpremsn1udr"></a>onpremsn1udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 192.168.2.0/24 |192.168.1.4 |Permite o tráfego para **onpremsn2** por meio do **OPFW** |

### <a name="onpremsn2udr"></a>onpremsn2udr
| Destino | Próximo salto | Explicação |
| --- | --- | --- |
| 10.0.3.0/24 |192.168.2.4 |Permite o tráfego para a sub-rede com suporte no Azure por meio de **OPFW** |
| 192.168.1.0/24 |192.168.2.4 |Permite o tráfego para **onpremsn1** por meio do **OPFW** |

## <a name="ip-forwarding"></a>encaminhamento IP
UDR e encaminhamento IP são recursos que podem ser usados em combinação para permitir que dispositivos virtuais sejam usados para controlar o fluxo de tráfego em uma rede virtual do Azure.  Um dispositivo virtual é nada mais do que uma VM que executa um aplicativo usado para lidar com o tráfego de rede de alguma forma, como um firewall ou um dispositivo NAT.

Essa VM de dispositivo virtual deve ser capaz de receber o tráfego de entrada não endereçado a si mesma. Para permitir que uma VM receba o tráfego endereçado a outros destinos, você deve habilitar o Encaminhamento IP para a VM. Esta é uma configuração do Azure, não uma configuração no sistema operacional convidado. O dispositivo virtual ainda precisa executar algum tipo de aplicativo para tratar o tráfego de entrada e roteá-la adequadamente.

Para saber mais sobre Encaminhamento de IP, visite [O que são Rotas Definidas pelo Usuário e Encaminhamento de IP?](virtual-networks-udr-overview.md).

Por exemplo, imagine que você tenha a seguinte configuração em uma VNet do Azure:

* A sub-rede **onpremsn1** contém uma VM denominada **onpremvm1**.
* A sub-rede **onpremsn2** contém uma VM chamada **onpremvm2**.
* Um dispositivo virtual denominado **OPFW** é conectado a **onpremsn1** e **onpremsn2**.
* Uma rota definida pelo usuário vinculado a **onpremsn1** especifica que todo o tráfego para **onpremsn2** devem ser enviado ao **OPFW**.

Nesse ponto, se **onpremvm1** tentar estabelecer uma conexão com **onpremvm2**, será usado o UDR e o tráfego será enviado para **OPFW** como o próximo salto. Tenha em mente que o destino do pacote real não está sendo alterado, ainda diz **onpremvm2** no destino. 

Sem o encaminhamento IP habilitado para **OPFW**, a lógica de rede virtual do Azure removerá os pacotes, já que só permitirá que os pacotes sejam enviados a uma VM se o endereço IP da VM for o destino para o pacote.

Com o encaminhamento de IP, a lógica de rede virtual do Azure encaminhará os pacotes para OPFW, sem alterar seu endereço de destino original. **OPFW** deve lidar com os pacotes e determinar o que fazer com eles.

Para o cenário acima para funcionar, você deve habilitar o encaminhamento de IP em NICs de **OPFW**, **AZF1**, **AZF2**, e **AZF3** que são usadas para rotear (todas as NICs exceto aquelas vinculadas à subrede de gerenciamento). 

## <a name="firewall-rules"></a>Regras de firewall
Conforme descrito acima, somente encaminhamento de IP garante que os pacotes sejam enviados para os dispositivos virtuais. O dispositivo ainda precisa decidir o que fazer com esses pacotes. No cenário acima, você precisa criar as seguintes regras em seus dispositivos:

### <a name="opfw"></a>OPFW
OPFW representa um dispositivo local que contém as seguintes regras:

* **rota**: Todo o tráfego para 10.0.0.0/16 (**azurevnet**) deve ser enviado pelo túnel **ONPREMAZURE**.
* **Política**: Permitir todo o tráfego bidirecional entre **port2** e **ONPREMAZURE**.

### <a name="azf1"></a>AZF1
O AZF1 representa um dispositivo virtual do Azure que contém as seguintes regras:

* **Política**: Permitir todo o tráfego bidirecional entre **port1** e **port2**.

### <a name="azf2"></a>AZF2
O AZF2 representa um dispositivo virtual do Azure que contém as seguintes regras:

* **rota**: Todo o tráfego para 10.0.0.0/16 (**onpremvnet**) deve ser enviada para o gateway do Azure endereço IP (ou seja, 10.0.0.1) por meio de **port1**.
* **Política**: Permitir todo o tráfego bidirecional entre **port1** e **port2**.

## <a name="network-security-groups-nsgs"></a>Grupos de segurança de rede (NSG)
Nesse cenário, os NSGs não estão sendo usados. No entanto, você pode aplicar NSGs a cada sub-rede para restringir o tráfego de entrada e saído. Por exemplo, você pode aplicar as seguintes regras de NSG à sub-rede FW externa.

**Entrada**

* Permita todo o tráfego TCP da Internet para a porta 80 em qualquer VM na sub-rede.
* Negue todos os outros tráfegos da Internet.

**Saída**

* Negue todo tráfego para a Internet.

## <a name="high-level-steps"></a>Etapas de alto nível
Para implantar este cenário, siga as etapas de alto nível abaixo.

1. Faça logon na sua Assinatura do Azure.
2. Se você desejar implantar uma VNet para imitar a rede local, provisione os recursos que fazem parte do **ONPREMRG**.
3. Provisione os recursos que fazem parte do **AZURERG**.
4. Provisionar o túnel de **onpremvnet** para **azurevnet**.
5. Depois que todos os recursos forem provisionados, faça logon em **onpremvm2** e faça ping do 10.0.3.101 para testar a conectividade entre **onpremsn2** e **azsn3**.

