---
title: 'Crie uma conexão entre VNets: clássico: portal do Azure'
description: Conecte redes virtuais do Azure entre si usando o PowerShell e o Portal do Azure.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 0d81e0474d898ffee7f128c0bcea61f077c3d758
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92103213"
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Configurar uma conexão entre redes virtuais (clássico)

Este artigo ajuda a criar uma conexão de gateway de VPN entre redes virtuais. As redes virtuais podem estar na mesma região ou em regiões diferentes, e com a mesma assinatura ou em assinaturas diferentes.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png" alt-text="Diagrama mostrando a arquitetura de VNet para VNet clássica":::

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

As etapas neste artigo se aplicam ao modelo de implantação clássico e o portal do Azure. Você também pode criar essa configuração usando uma ferramenta de implantação ou um modelo de implantação diferente, selecionando uma opção diferente na lista a seguir:

> [!div class="op_single_selector"]
> * [Clássico](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Resource Manager](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Conectar o VNets em diferentes modelos de implantação](vpn-gateway-connect-different-deployment-models-portal.md)
>
>

## <a name="about-vnet-to-vnet-connections"></a>Sobre conexões de rede virtual a rede virtual

Conectar uma rede virtual a outra rede virtual (VNet a VNet) no modelo de implantação clássico usando um gateway de VPN é semelhante a conectar uma rede virtual a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE.

As VNets que você conecta podem estar em regiões e assinaturas diferentes. Você pode combinar a comunicação de VNet a VNet usando configurações multissite. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual.

:::image type="content" source="./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png" alt-text="Diagrama mostrando conexões":::

### <a name="why-connect-virtual-networks"></a><a name="why"></a>Por que conectar redes virtuais?

Você talvez queira conectar redes virtuais pelos seguintes motivos:

* **Redundância geográfica entre regiões e presença geográfica**

  * Você pode configurar sua própria sincronização ou replicação geográfica com conectividade segura sem passar por pontos de extremidade voltados para a Internet.
  * Com o Azure Load Balancer e a tecnologia de clustering da Microsoft ou de terceiros, você pode configurar uma carga de trabalho altamente disponível com redundância geográfica entre várias regiões do Azure. Um exemplo importante é configurar o Always On do SQL com Grupos de Disponibilidade espalhados por várias regiões do Azure.
* **Aplicativos multicamadas regionais com limite de isolamento forte**

  * Na mesma região, você pode configurar aplicativos multicamadas com várias VNets conectadas com isolamento forte e comunicação entre camadas segura.
* **Comunicação entre organizações e assinaturas no Azure**

  * Se você tem várias assinaturas do Azure, agora é possível se conectar a cargas de trabalho de assinaturas diferentes com segurança entre redes virtuais.
  * Para empresas ou provedores de serviço, é possível habilitar a comunicação entre organizações usando a tecnologia VPN segura no Azure.

Para saber mais sobre conexões de Rede Virtual a Rede Virtual, confira as [Considerações sobre Rede Virtual para Rede Virtual](#faq) no final deste artigo.

## <a name="prerequisites"></a>Pré-requisitos

Podemos usar o portal para a maioria das etapas, mas você deve usar o PowerShell para criar conexões entre as redes virtuais. Você não pode criar as conexões usando o portal do Azure porque não há como especificar a chave compartilhada no Portal. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

## <a name="planning"></a><a name="planning"></a>Planejamento

É importante decidir os intervalos que você usará para configurar as redes virtuais. Para esta configuração, você deve garantir que nenhum dos intervalos de VNet se sobreponham entre si ou aos das redes locais às quais se conectam.

### <a name="vnets"></a><a name="vnet"></a>VNets

Para este exercício, usamos os seguintes valores de exemplo:

**Valores para TestVNet1**

Nome: TestVNet1<br>
Espaço de endereço: 10.11.0.0/16, 10.12.0.0/16 (opcional)<br>
Nome da sub-rede: padrão<br>
Intervalo de endereços da sub-rede: 10.11.0.0/24<br>
Grupo de recursos: ClassicRG<br>
Local: Leste dos EUA<br>
GatewaySubnet: 10.11.1.0/27

**Valores para TestVNet4**

Nome: TestVNet4<br>
Espaço de endereço: 10.41.0.0/16, 10.42.0.0/16 (opcional)<br>
Nome da sub-rede: padrão<br>
Intervalo de endereços da sub-rede: 10.41.0.0/24<br>
Grupo de recursos: ClassicRG<br>
Local: Oeste dos EUA<br>
GatewaySubnet: 10.41.1.0/27

### <a name="connections"></a><a name="plan"></a>conexões

A tabela a seguir mostra um exemplo de como você irá conectar seu VNets. Use os intervalos apenas como uma diretriz. Anote os intervalos para suas redes virtuais. Você precisa dessas informações para etapas posteriores.

Neste exemplo, TestVNet1 se conecta a um site de rede local que você cria com o nome ' VNet4Local '. As configurações de VNet4Local contêm os prefixos de endereço para TestVNet4.
O site local para cada VNet é a outra VNet. Os seguintes valores de exemplo são usados para nossa configuração:

**Exemplo**

| Rede Virtual | Espaço de endereço | Location | Conecta ao site de rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Leste dos EUA |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Oeste dos EUA |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="create-virtual-networks"></a><a name="vnetvalues"></a>Criar redes virtuais

Nesta etapa, você cria duas redes virtuais clássicas, TestVNet1 e TestVNet4. Se você estiver usando este artigo como um exercício, use os [valores de exemplo](#vnet).

**Ao criar suas redes virtuais, lembre-se das seguintes configurações:**

* **Espaços de Endereço de Rede Virtual** – na página Espaços de Endereço de Rede Virtual, especifique o intervalo de endereços que você deseja usar para sua rede virtual. Esses são os endereços IP dinâmicos que serão atribuídos às VMs e a outras instâncias de função que você implantar nessa rede virtual.<br>Os espaços de endereço selecionados não podem sobrepor os espaços de endereço de qualquer outra VNet ou os locais em que essa VNet se conectará.

* **Local** – Quando você cria uma rede virtual, associa-a a um local do Azure (região). Por exemplo, se você desejar que as VMs implantadas em sua rede virtual estejam localizadas fisicamente no oeste dos EUA, selecione esse local. Você não pode alterar o local associado à sua rede virtual depois de criá-la.

**Depois de criar suas VNets, você poderá adicionar as seguintes configurações:**

* **Espaço de endereço** – espaço de endereço adicional não é necessário para essa configuração, mas você pode incluir espaço de endereço adicional após a criação da VNet.

* **Sub-redes** – sub-redes adicionais não são necessárias para essa configuração, mas pode ser conveniente ter suas VMs em uma sub-rede separada das outras instâncias de função.

* **Servidores DNS** – insira o nome do servidor DNS e o endereço IP. Essa configuração não cria um servidor DNS. Ela permite que você especifique os servidores DNS que deseja usar para a resolução de nomes dessa rede virtual.

### <a name="to-create-a-classic-virtual-network"></a>Para criar uma rede virtual clássica

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-sites-and-gateways"></a><a name="localsite"></a>Configurar sites e gateways

O Azure usa as configurações especificadas em cada site de rede local para determinar como rotear o tráfego entre as VNets. Cada VNet deve apontar para a respectiva rede local à qual você deseja rotear o tráfego. Você determina o nome que deseja usar para se referir a cada site de rede local. É melhor usar algo descritivo.

Por exemplo, a TestVNet1 conecta-se a um site de rede local que você criou chamado "VNet4Local". As configurações de VNet4Local contêm os prefixos de endereço para TestVNet4.

Tenha em mente que o site local de cada VNet é a outra VNet.

| Rede Virtual | Espaço de endereço | Location | Conecta ao site de rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Leste dos EUA |SiteVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Oeste dos EUA |SiteVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

### <a name="to-configure-a-site"></a><a name="site"></a>Para configurar um site

O site local normalmente se refere ao seu site local. Ele contém o endereço IP do dispositivo VPN no qual você criará uma conexão e os intervalos de endereços IP que serão roteados através do gateway de VPN para o dispositivo VPN.

1. Na página de sua rede virtual, em **configurações**, selecione **conexões site a site**.
1. Na página conexões site a site, selecione **+ Adicionar**.
1. Na página **Configurar uma conexão VPN e um gateway** , para **tipo de conexão**, deixe **site a site** selecionado.

   * **Endereço IP do gateway de VPN:** esse é o endereço IP público do dispositivo VPN de sua rede local. Para este exercício, você pode colocar um endereço fictício, pois você ainda não tem o endereço IP para o gateway de VPN para o outro site. Por exemplo, 5.4.3.2. Posteriormente, depois de configurar o gateway para a outra VNet, você poderá ajustar esse valor.

   * **Espaço de endereço do cliente:** Liste os intervalos de endereços IP que você deseja roteados para a outra rede virtual por meio desse gateway. Você pode adicionar vários intervalos de espaço de endereço. Certifique-se de que os intervalos especificados aqui não se sobreponham aos intervalos de outras redes as quais sua rede virtual se conecta, ou com os intervalos de endereços da própria rede virtual.
1. Na parte inferior da página, não selecione revisar + criar. Em vez disso, selecione **Avançar: Gateway>**.

### <a name="to-configure-a-virtual-network-gateway"></a><a name="sku"></a>Para configurar um gateway de rede virtual

1. Na página **Gateway** , selecione os seguintes valores:

   * **Tamanho:** Esse é o SKU de gateway que você usa para criar seu gateway de rede virtual. Gateways VPN clássicos usam SKUs de gateway antigos (herdados). Para saber mais sobre os SKUs de gateway antigos, confira [Trabalhar com SKUs de gateway de rede virtual (SKUs antigos)](vpn-gateway-about-skus-legacy.md). Você pode selecionar **Standard** para este exercício.

   * **Tipo de roteamento:** Selecione o tipo de roteamento para o gateway. Isso é também conhecido como o tipo de VPN. É importante selecionar o tipo correto, pois não é possível converter o gateway de um tipo para outro. Seu dispositivo VPN deve ser compatível com o tipo de roteamento selecionado. Para obter mais informações sobre o tipo de roteamento, consulte [sobre as configurações de gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Você pode ver artigos sobre os tipos de VPN 'RouteBased' e 'PolicyBased'. 'Dinâmico' corresponde a 'RouteBased' e 'Estático' corresponde a 'PolicyBased'. Para essa configuração, selecione **dinâmico**.

   * **Sub-rede de gateway:** O tamanho da sub-rede de gateway que você especifica depende da configuração do gateway de VPN que você deseja criar. Embora seja possível criar uma sub-rede do gateway tão pequena quanto /29, recomendamos que você use /27 ou /28. Isso cria uma sub-rede maior que inclui mais endereços. Usar uma sub-rede de gateway maior permite endereços IP suficientes para acomodar as possíveis configurações futuras.

1. Selecione **examinar + criar** na parte inferior da página para validar suas configurações. Selecione **criar** para implantar. Pode levar até 45 minutos para criar um gateway de rede virtual, dependendo do SKU de gateway que você selecionou.
1. Você pode começar a prosseguir para a próxima etapa enquanto esse gateway está sendo criado.

### <a name="configure-testvnet4-settings"></a>Definir configurações de TestVNet4

Repita as etapas para [criar um site e um gateway](#localsite) para configurar o TestVNet4, substituindo os valores quando necessário. Se você estiver fazendo isso como um exercício, use os [valores de exemplo](#planning).

## <a name="update-local-sites"></a><a name="updatelocal"></a>Atualizar sites locais

Depois que os gateways de rede virtual tiverem sido criados para ambos os VNets, você deverá ajustar as propriedades do site local para o **endereço IP do gateway de VPN**.

|Nome da VNet|Site conectado|Endereço IP do gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Endereço IP do gateway de VPN para TestVNet4|
|TestVNet4|VNet1Local|Endereço IP do gateway de VPN para TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Parte 1 – Obter o endereço IP público do gateway de rede virtual

1. Navegue até a VNet acessando o **grupo de recursos** e selecionando a rede virtual.
1. Na página de sua rede virtual, no painel **Essentials** à direita, localize o **endereço IP do gateway** e copie para a área de transferência.

### <a name="part-2---modify-the-local-site-properties"></a>Parte 2-modificar as propriedades do site local

1. Em conexões site a site, selecione a conexão. Por exemplo, SiteVNet4.
1. Na página **Propriedades** da conexão site a site, selecione **Editar site local**.
1. No campo **endereço IP do gateway de VPN** , Cole o endereço IP do gateway de VPN que você copiou na seção anterior.
1. Selecione **OK**.
1. O campo é atualizado no sistema. Você também pode usar esse método para adicionar um endereço IP adicional que você deseja rotear para esse site.

### <a name="part-3---repeat-steps-for-the-other-vnet"></a>Parte 3 – Repita as etapas para a outra VNet

Repita as etapas para TestVNet4.

## <a name="retrieve-configuration-values"></a><a name="getvalues"></a>Recuperar valores de configuração

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-connections"></a><a name="createconnections"></a>Criar conexões

Quando todas as etapas anteriores tiverem sido concluídas, você poderá definir as chaves pré-compartilhadas IPsec/IKE e criar a conexão. Esse conjunto de etapas usa o PowerShell. As conexões de VNet para VNet para o modelo de implantação clássico não podem ser configuradas no portal do Azure porque a chave compartilhada não pode ser especificada no Portal.

Nos exemplos, observe que a chave compartilhada é exatamente a mesma. A chave compartilhada sempre deve corresponder. Certifique-se de substituir os valores nesses exemplos pelos nomes exatos de seus Sites de Rede Local e VNets.

1. Crie a conexão de TestVNet1 a TestVNet4. Certifique-se de alterar os valores.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
   -LocalNetworkSiteName 'value for _VNet4Local' -SharedKey A1b2C3D4
   ```
2. Crie a conexão de TestVNet4 a TestVNet1.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
   -LocalNetworkSiteName 'value for _VNet1Local' -SharedKey A1b2C3D4
   ```
3. Aguarde até que as conexões sejam inicializadas. Depois que o gateway tiver sido inicializado, o Status será 'Êxito'.

   ```
   Error          :
   HttpStatusCode : OK
   Id             :
   Status         : Successful
   RequestId      :
   StatusCode     : OK
   ```

## <a name="faq-and-considerations"></a><a name="faq"></a>Perguntas frequentes e considerações

Essas considerações se aplicam às redes virtuais clássicas e aos gateways de rede virtual clássicos.

* As redes virtuais podem estar na mesma assinatura ou em assinaturas diferentes.
* As redes virtuais podem estar na mesma região ou em regiões diferentes do Azure (locais).
* Um serviço de nuvem ou um ponto de extremidade de balanceamento de carga não pode se estender por redes virtuais, mesmo se estiverem conectados juntos.
* A conexão de várias redes virtuais entre si não exige nenhum dispositivo VPN.
* O recurso VNet a VNet dá suporte à conexão de Redes Virtuais do Azure. Ele não dá suporte à conexão de máquinas virtuais ou de serviços de nuvem que não estejam implantados em uma rede virtual.
* VNet a VNet requer gateways de roteamento dinâmico. Não há suporte para gateways de roteamento estático do Azure.
* A conectividade de rede virtual pode ser usada simultaneamente com VPNs de multissite. Há um máximo 10 túneis de VPN para um gateway de VPN de rede virtual conectado a outras redes virtuais ou a sites locais.
* Os espaços de endereço das redes virtuais e os sites de redes locais não devem se sobrepor. A sobreposição de espaços de endereço causará a falha da criação de redes virtuais ou do carregamento de arquivos de configuração netcfg.
* Não há suporte a túneis redundantes entre um par de redes virtuais.
* Todos os túneis de VPN para VNet, incluindo VPNs P2S, compartilham a largura de banda disponível no gateway de VPN e o mesmo SLA de tempo de ativação de gateway de VPN no Azure.
* O tráfego da rede virtual com rede virtual viaja pelo backbone do Azure.

## <a name="next-steps"></a>Próximas etapas

Verifique as conexões. Confira [Verificar uma conexão de Gateway de VPN](vpn-gateway-verify-connection-resource-manager.md).
