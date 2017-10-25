---
title: "Criar uma conexão entre redes virtuais: clássico: portal do Azure | Microsoft Docs"
description: "Como conectar redes virtuais do Azure entre si usando o PowerShell e o Portal Clássico do Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/02/2017
ms.author: cherylmc
ms.openlocfilehash: 77097d59077cd8e199acdb5dc0d8427369565eea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Configurar uma conexão entre redes virtuais (clássico)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

Este artigo mostra como criar uma conexão de gateway de VPN entre redes virtuais. As redes virtuais podem estar na mesma região ou em regiões diferentes, e com a mesma assinatura ou em assinaturas diferentes. As etapas neste artigo se aplicam ao modelo de implantação clássico e o portal do Azure. Você também pode criar essa configuração usando uma ferramenta de implantação ou um modelo de implantação diferente, selecionando uma opção diferente na lista a seguir:

> [!div class="op_single_selector"]
> * [Portal do Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI do Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Conectar modelos de implantação diferentes – portal do Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Conectar modelos de implantação diferentes – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagrama de conectividade VNet a VNet](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

## <a name="about-vnet-to-vnet-connections"></a>Sobre conexões de rede virtual a rede virtual

Conectar uma rede virtual a outra rede virtual (VNet a VNet) no modelo de implantação clássico usando um gateway de VPN é semelhante a conectar uma rede virtual a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE.

As VNets que você conecta podem estar em regiões e assinaturas diferentes. Você pode combinar a comunicação de VNet a VNet usando configurações multissite. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual.

![Conexão VNet a VNet](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>Por que conectar redes virtuais?

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

### <a name="before-you-begin"></a>Antes de começar

Antes de iniciar este exercício, baixe e instale a versão mais recente dos cmdlets do PowerShell do Azure Service Management (SM). Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview): Podemos usar o portal para a maioria das etapas, mas você deve usar o PowerShell para criar conexões entre as redes virtuais. Você não pode criar as conexões usando o portal do Azure.

## <a name="plan"></a>Etapa 1 – Planejar os intervalos de endereços IP

É importante decidir os intervalos que você usará para configurar as redes virtuais. Para esta configuração, você deve garantir que nenhum dos intervalos de VNet se sobreponham entre si ou aos das redes locais às quais se conectam.

A tabela a seguir mostra um exemplo de como definir as VNets. Use os intervalos apenas como uma diretriz. Anote os intervalos para suas redes virtuais. Você precisa dessas informações para etapas posteriores.

**Exemplo**

| Rede Virtual | Espaço de endereço | Região | Conecta ao site de rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Leste dos EUA |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Oeste dos EUA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>Etapa 2 – Criar as redes virtuais

Crie duas redes virtuais no [portal do Azure](https://portal.azure.com). Para ver as etapas para criar redes virtuais clássicas, confira [Criar uma rede virtual clássica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Ao usar o portal para criar uma rede virtual clássica, navegue até a folha de rede virtual usando as etapas a seguir, caso contrário, a opção para criar uma rede virtual clássica não aparecerá:

1. Clique no sinal “+” para abrir a folha “Novo”.
2. No campo “Pesquisar no Marketplace”, digite “Rede Virtual”. Se você selecionar, em vez disso, Rede -> Rede Virtual, você não terá a opção de criar uma VNet clássica.
3. Localize “Rede Virtual” na lista retornada e clique para abrir a folha Rede Virtual. 
4. Na folha da rede virtual, selecione “Clássica” para criar uma VNet clássica. 

Se estiver usando este artigo como um exercício, você poderá usar os seguintes valores de exemplo:

**Valores para TestVNet1**

Nome: TestVNet1<br>
Espaço de endereço: 10.11.0.0/16, 10.12.0.0/16 (opcional)<br>
Nome da sub-rede: padrão<br>
Intervalo de endereços da sub-rede: 10.11.0.1/24<br>
Grupo de recursos: ClassicRG<br>
Local: Leste dos EUA<br>
GatewaySubnet: 10.11.1.0/27

**Valores para TestVNet4**

Nome: TestVNet4<br>
Espaço de endereço: 10.41.0.0/16, 10.42.0.0/16 (opcional)<br>
Nome da sub-rede: padrão<br>
Intervalo de endereços da sub-rede: 10.41.0.1/24<br>
Grupo de recursos: ClassicRG<br>
Local: Oeste dos EUA<br>
GatewaySubnet: 10.41.1.0/27

**Ao criar suas redes virtuais, lembre-se das seguintes configurações:**

* **Espaços de Endereço de Rede Virtual** – na página Espaços de Endereço de Rede Virtual, especifique o intervalo de endereços que você deseja usar para sua rede virtual. Esses são os endereços IP dinâmicos que serão atribuídos às VMs e a outras instâncias de função que você implantar nessa rede virtual.<br>Os espaços de endereço selecionados não podem sobrepor os espaços de endereço de qualquer outra VNet ou os locais em que essa VNet se conectará.

* **Local** – ao criar uma rede virtual, você a associa a um local do Azure (região). Por exemplo, se você desejar que as VMs implantadas em sua rede virtual estejam localizadas fisicamente no oeste dos EUA, selecione esse local. Você não pode alterar o local associado à sua rede virtual depois de criá-la.

**Depois de criar suas VNets, você poderá adicionar as seguintes configurações:**

* **Espaço de endereço** – espaço de endereço adicional não é necessário para essa configuração, mas você pode incluir espaço de endereço adicional após a criação da VNet.

* **Sub-redes** – sub-redes adicionais não são necessárias para essa configuração, mas pode ser conveniente ter suas VMs em uma sub-rede separada das outras instâncias de função.

* **Servidores DNS** – insira o nome do servidor DNS e o endereço IP. Essa configuração não cria um servidor DNS. Ela permite que você especifique os servidores DNS que deseja usar para a resolução de nomes dessa rede virtual.

Nesta seção, você configura o tipo de conexão, o site local e cria o gateway.

## <a name="localsite"></a>Etapa 3 – Configurar o site local

O Azure usa as configurações especificadas em cada site de rede local para determinar como rotear o tráfego entre as VNets. Cada VNet deve apontar para a respectiva rede local à qual você deseja rotear o tráfego. Você determina o nome que deseja usar para se referir a cada site de rede local. É melhor usar algo descritivo.

Por exemplo, a TestVNet1 conecta-se a um site de rede local que você criou chamado "VNet4Local". As configurações de VNet4Local contêm os prefixos de endereço para TestVNet4.

O site local para cada VNet é a outra VNet. Os seguintes valores de exemplo são usados para nossa configuração:

| Rede Virtual | Espaço de endereço | Região | Conecta ao site de rede local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Leste dos EUA |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Oeste dos EUA |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Localize TestVNet1 no portal do Azure. Na seção **Conexões VPN** da folha, clique em **Gateway**.

    ![Sem gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. Na página **Nova Conexão VPN**, selecione **Site a Site**.
3. Clique em **Site local** para abrir a página Site local e defina as configurações.
4. Na página **Site local**, nomeie o site local. Em nosso exemplo, demos ao site local o nome de "VNet4Local".
5. Para **Endereço IP do gateway de VPN**, você pode usar qualquer endereço IP desejado, desde que ele esteja em um formato válido. Normalmente, você usaria o endereço IP externo real para um dispositivo VPN. Mas, para uma configuração clássica de VNet a VNet, você usa o endereço IP público que é atribuído ao gateway da sua VNet. Considerando que você ainda não criou o gateway de rede virtual, especifique qualquer endereço IP público válido como um espaço reservado.<br>Não deixe em branco. Não é opcional para essa configuração. Em uma etapa posterior, você voltará para essas configurações e as definirá com os endereços IP de gateway de rede virtual correspondentes assim que forem gerados pelo Azure.
6. Para **Espaço de Endereço do Cliente**, use o espaço de endereço da outra VNet. Consulte seu exemplo de planejamento. Clique em **OK** para salvar as configurações e retornar para a folha **Nova Conexão VPN**.

    ![site local](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>Etapa 4 – Criar o gateway de rede virtual

Cada rede virtual deve ter um gateway de rede virtual. O gateway de rede virtual roteia e criptografa o tráfego.

1. Na folha **Nova Conexão VPN**, marque a caixa de seleção **Criar gateway imediatamente**.
2. Clique em **Sub-rede, tamanho e tipo de roteamento**. Na folha **Configuração de gateway**, clique em **Sub-rede**.
3. O nome da sub-rede de gateway é preenchido automaticamente com o nome 'GatewaySubnet' exigido. O **Intervalo de endereços** contém os endereços IP que estão alocados para os serviços de gateway de VPN. Algumas configurações permitem uma sub-rede de gateway de /29, mas é melhor usar uma de /28 ou /27 para acomodar futuras configurações que possam exigir mais endereços IP para os serviços de gateway. Em nossas configurações de exemplo, usamos 10.11.1.0/27. Ajuste o espaço de endereço e clique em **OK**.
4. Configure o **Tamanho do Gateway**. Essa configuração refere-se ao [SKU do Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Configurar o **Tipo de Roteamento**. O tipo de roteamento para essa configuração deve ser **Dinâmico**. Não é possível alterar o tipo de roteamento, a menos que você destrua o gateway e crie um novo.
6. Clique em **OK**.
7. Na folha **Nova Conexão VPN**, clique em **OK** para começar a criar o gateway de rede virtual. Criar um gateway pode levar 45 minutos ou mais, dependendo do SKU de gateway selecionado.

## <a name="vnet4settings"></a>Etapa 5 – Definir as configurações de TestVNet4

Repita as etapas para [Criar um site local](#localsite) e [Criar o gateway de rede virtual](#gw) a fim de configurar TestVNet4, substituindo os valores quando necessário. Se você estiver fazendo isso como um exercício, use os [valores de exemplo](#vnetvalues).

## <a name="updatelocal"></a>Etapa 6 – Atualizar os sites locais

Depois que os gateways de rede virtual tiverem sido criados para ambas as VNets, você deverá ajustar os valores do **endereço IP do gateway de VPN** dos sites locais.

|Nome da VNet|Site conectado|Endereço IP do gateway|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Endereço IP do gateway de VPN para TestVNet4|
|TestVNet4|VNet1Local|Endereço IP do gateway de VPN para TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Parte 1 – Obter o endereço IP público do gateway de rede virtual

1. Localize a rede virtual no portal do Azure.
2. Clique para abrir a folha **Visão geral** da VNet. Na folha, em **Conexões VPN**, você pode exibir o endereço IP do seu gateway de rede virtual.

  ![IP público](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Copie o endereço IP. Você o usará na próxima seção.
4. Repita essas etapas para TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Parte 2 – Modificar os sites locais

1. Localize a rede virtual no portal do Azure.
2. Na folha **Visão geral** da VNet, clique no site local.

  ![Site local criado](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. Na folha **Conexões VPN Site a Site**, clique no nome do site local que você quer modificar.

  ![Abrir site local](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Clique no **Site local** que quer modificar.

  ![modificar site](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Atualize o **endereço IP do gateway de VPN** e clique em **OK** para salvar as configurações.

  ![IP do gateway](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Feche as outras folhas.
7. Repita essas etapas para TestVNet4.

## <a name="getvalues"></a>Etapa 7 – Recuperar valores do arquivo de configuração de rede

Quando você cria VNets clássicas no portal do Azure, o nome que você vê não é o nome completo usado para o PowerShell. Por exemplo, uma VNet que parece ter o nome **TestVNet1** no portal, pode ter um nome muito mais longo no arquivo de configuração de rede. O nome pode ser algo parecido com: **Grupo ClassicRG TestVNet1**. Ao criar suas conexões, é importante usar os valores que você vê no arquivo de configuração de rede.

Nas etapas a seguir, você vai se conectar à sua conta do Azure, bem como baixar e exibir o arquivo de configuração de rede para obter os valores que são necessários para as conexões.

1. Baixe e instale a versão mais recente dos cmdlets do PowerShell do SM (Gerenciamento de Serviços) do Azure. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview):

2. Abra o console do PowerShell com direitos elevados e conecte-se à sua conta. Use o exemplo a seguir para ajudar a se conectar:

  ```powershell
  Login-AzureRmAccount
  ```

  Verificar as assinaturas da conta.

  ```powershell
  Get-AzureRmSubscription
  ```

  Se você tiver mais de uma assinatura, selecione a assinatura que deseja usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

  Use o cmdlet a seguir para adicionar sua assinatura do Azure ao PowerShell para o modelo de implantação clássico.

  ```powershell
  Add-AzureAccount
  ```
3. Exporte e exiba o arquivo de configuração de rede. Crie um diretório em seu computador e exporte o arquivo de configuração de rede para o diretório. Neste exemplo, o arquivo de configuração de rede é exportado para **C:\AzureNet**.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
4. Abra o arquivo com um editor de texto e exiba os nomes dos sites e VNets. Esses serão os nomes usados na criação de suas conexões.<br>Os nomes de VNet são listados como **VirtualNetworkSite name =**<br>Os nomes de site são listados como **LocalNetworkSiteRef name =**

## <a name="createconnections"></a>Etapa 8 – Criar as conexões do gateway de VPN

Quando todas as etapas anteriores tiverem sido concluídas, você poderá definir as chaves pré-compartilhadas IPsec/IKE e criar a conexão. Esse conjunto de etapas usa o PowerShell. As conexões de VNet a VNet para o modelo de implantação clássico não podem ser configurados no portal do Azure.

Nos exemplos, observe que a chave compartilhada é exatamente a mesma. A chave compartilhada sempre deve corresponder. Certifique-se de substituir os valores nesses exemplos pelos nomes exatos de seus Sites de Rede Local e VNets.

1. Crie a conexão de TestVNet1 a TestVNet4.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
  -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
  ```
2. Crie a conexão de TestVNet4 a TestVNet1.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
  -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
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

## <a name="faq"></a>Considerações de VNet a VNet para VNets clássicas
* As redes virtuais podem estar na mesma assinatura ou em assinaturas diferentes.
* As redes virtuais podem estar na mesma região ou em regiões diferentes do Azure (locais).
* Um serviço de nuvem ou um ponto de extremidade de balanceamento de carga não pode abranger redes virtuais, mesmo que elas estejam conectadas entre si.
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
