---
title: 'Conectar a sua rede local a uma rede virtual do Azure: VPN Site a Site (clássico): portal | Microsoft Docs'
description: Criar uma conexão IPsec de sua rede local para uma rede virtual clássica do Azure pela Internet pública.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 4ad05281f13885327c855a261a3101388f38af83
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98878046"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Criar uma conexão Site a Site usando o portal do Azure (clássico)

Este artigo mostra como usar o portal do Azure para criar uma conexão de gateway de VPN Site a Site de sua rede local para a rede virtual. As etapas neste artigo se aplicam ao modelo de implantação clássico e não se aplicam ao modelo de implantação atual, Resource Manager. Você também pode criar essa configuração usando uma ferramenta de implantação ou um modelo de implantação diferente, selecionando uma opção diferente na lista a seguir:

> [!div class="op_single_selector"]
> * [Azure portal](./tutorial-site-to-site-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal do Azure (clássico)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 

Uma conexão de gateway de VPN Site a Site é usada para conectar a rede local a uma rede virtual do Azure por um túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão exige um dispositivo VPN localizado no local que tenha um endereço IP público voltado para o exterior atribuído a ele. Para saber mais sobre os gateways de VPN, veja [Sobre o gateway de VPN](vpn-gateway-about-vpngateways.md).

![Diagrama de conexão Site a Site de Gateway de VPN entre locais](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Antes de começar

Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

* Verifique se você deseja trabalhar no modelo de implantação clássico. Se você deseja trabalhar no modelo de implantação do Gerenciador de Recursos, confira [Criar uma conexão Site a Site (Gerenciador de Recursos)](./tutorial-site-to-site-portal.md). Recomendamos que você use o modelo de implantação do Gerenciador de recursos, pois o modelo clássico é herdado.
* Verifique se você possui um dispositivo VPN compatível e alguém que possa configurá-lo. Para obter mais informações sobre dispositivos VPN compatíveis e a configuração de dispositivo, confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md).
* Verifique se você possui um endereço IPv4 público voltado para o exterior para seu dispositivo VPN.
* Se não estiver familiarizado com os intervalos de endereços IP localizados na configuração de rede local, você precisará trabalhar em conjunto com alguém que possa lhe fornecer os detalhes. Ao criar essa configuração, você deve especificar os prefixos de intervalo de endereços IP que o Azure roteará para seu local. Nenhuma das sub-redes da rede local podem se sobrepor às sub-redes de rede virtual às quais você deseja se conectar.
* O PowerShell é necessário para especificar a chave compartilhada e criar a conexão de gateway de VPN. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Exemplo de valores de configuração para este exercício

Os exemplos neste artigo usam os seguintes valores. Você pode usar esses valores para criar um ambiente de teste ou consultá-los para compreender melhor os exemplos neste artigo. Normalmente, ao trabalhar com valores de endereço IP para o espaço de endereço, você deseja coordenar com o administrador da rede para evitar sobreposição de espaços de endereço, o que pode afetar o roteamento. Nesse caso, substitua os valores de endereço IP pelos seus próprios se desejar criar uma conexão de trabalho.

* **Grupo de recursos:** TestRG1
* **Nome da VNet:** TestVNet1
* **Espaço de endereço:** 10.11.0.0/16
* **Nome da sub-rede:** FrontEnd
* **Intervalo de endereços da sub-rede:** 10.11.0.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **Região:** (EUA) Leste dos EUA
* **Nome do site local:** Site2
* **Espaço de endereço do cliente:** o espaço de endereço localizado no site local.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Criar uma rede virtual

Ao criar uma rede virtual a ser usada para uma conexão de S2S, você precisa certificar-se de que os espaços de endereço especificados não coincidam com um dos espaços de endereço de cliente dos sites locais aos quais você deseja se conectar. Se você tiver uma sobreposição de sub-redes, a conexão não funcionará corretamente.

* Se você já tiver uma rede virtual, verifique se as configurações são compatíveis com seu design de gateway de VPN. Preste atenção especial em todas as sub-redes que possam se sobrepor a outras redes.

* Se você ainda não tiver uma rede virtual, crie uma. Capturas de tela são fornecidas como exemplos. Substitua os valores pelos seus próprios.

### <a name="to-create-a-virtual-network"></a>Para criar uma rede virtual

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="configure-the-site-and-gateway"></a><a name="localsite"></a>Configurar o site e o gateway

### <a name="to-configure-the-site"></a>Para configurar o site

O site local normalmente se refere ao seu site local. Ele contém o endereço IP do dispositivo VPN no qual você criará uma conexão e os intervalos de endereços IP que serão roteados através do gateway de VPN para o dispositivo VPN.

1. Na página de sua rede virtual, em **configurações**, selecione **conexões site a site**.
1. Na página conexões site a site, selecione **+ Adicionar**.
1. Na página **Configurar uma conexão VPN e um gateway** , para **tipo de conexão**, deixe **site a site** selecionado. Para este exercício, você precisará usar uma combinação dos valores de [exemplo](#values) e seus próprios valores.

   * **Endereço IP do gateway de VPN:** esse é o endereço IP público do dispositivo VPN de sua rede local. O dispositivo VPN exige um endereço IP IPv4 público. Especifique um endereço IP público válido para o dispositivo VPN ao qual você deseja se conectar. Ele deve ser acessível pelo Azure. Se você não souber o endereço IP de seu dispositivo VPN, coloque-o em um valor de espaço reservado (desde que esteja no formato de um endereço IP público válido) e depois altere-o.

   * **Espaço de endereço do cliente:** lista os intervalos de endereços IP que você deseja rotear até a rede local por meio deste gateway. Você pode adicionar vários intervalos de espaço de endereço. Certifique-se de que os intervalos especificados aqui não se sobreponham aos intervalos de outras redes as quais sua rede virtual se conecta, ou com os intervalos de endereços da própria rede virtual.
1. Na parte inferior da página, não selecione revisar + criar. Em vez disso, selecione **Avançar: Gateway>**.

### <a name="to-configure-the-virtual-network-gateway"></a><a name="sku"></a>Para configurar o gateway de rede virtual

1. Na página **Gateway** , selecione os seguintes valores:

   * **Tamanho:** Esse é o SKU de gateway que você usa para criar seu gateway de rede virtual. Gateways VPN clássicos usam SKUs de gateway antigos (herdados). Para saber mais sobre os SKUs de gateway antigos, confira [Trabalhar com SKUs de gateway de rede virtual (SKUs antigos)](vpn-gateway-about-skus-legacy.md). Você pode selecionar **Standard** para este exercício.

   * **Tipo de roteamento:** Selecione o tipo de roteamento para o gateway. Isso é também conhecido como o tipo de VPN. É importante selecionar o tipo correto, pois não é possível converter o gateway de um tipo para outro. Seu dispositivo VPN deve ser compatível com o tipo de roteamento selecionado. Para obter mais informações sobre o tipo de roteamento, consulte [sobre as configurações de gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype). Você pode ver artigos sobre os tipos de VPN 'RouteBased' e 'PolicyBased'. 'Dinâmico' corresponde a 'RouteBased' e 'Estático' corresponde a 'PolicyBased'. Normalmente, você deseja o roteamento dinâmico.

   * **Sub-rede de gateway:** O tamanho da sub-rede de gateway que você especifica depende da configuração do gateway de VPN que você deseja criar. Embora seja possível criar uma sub-rede do gateway tão pequena quanto /29, recomendamos que você use /27 ou /28. Isso cria uma sub-rede maior que inclui mais endereços. Usar uma sub-rede de gateway maior permite endereços IP suficientes para acomodar as possíveis configurações futuras.

1. Selecione **examinar + criar** na parte inferior da página para validar suas configurações. Selecione **criar** para implantar. Pode levar até 45 minutos para criar um gateway de rede virtual, dependendo do SKU de gateway que você selecionou.

## <a name="configure-your-vpn-device"></a><a name="vpndevice"></a>Configurar o dispositivo VPN

As conexões Site a Site para uma rede local exigem um dispositivo VPN. Nesta etapa, você deve configurar seu dispositivo VPN. Ao configurar seu dispositivo VPN, você precisará dos seguintes valores:

* Uma chave compartilhada. Essa é a mesma chave compartilhada especificada ao criar a conexão VPN Site a Site. Em nossos exemplos, usamos uma chave compartilhada básica. Recomendamos gerar uma chave mais complexa para uso.
* O endereço IP público do seu gateway de rede virtual. Você pode exibir o endereço IP público usando o portal do Azure, o PowerShell ou a CLI.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="retrieve-values"></a><a name="getvalues"></a>Recuperar valores

[!INCLUDE [retrieve values](../../includes/vpn-gateway-values-classic.md)]

## <a name="create-the-connection"></a><a name="CreateConnection"></a>Criar a conexão

> [!NOTE]
> Para o modelo de implantação clássico, essa etapa não está disponível no portal do Azure ou via Azure Cloud Shell. Você deve usar a versão do SM (gerenciamento de serviços) dos cmdlets Azure PowerShell localmente da sua área de trabalho.
>

Nesta etapa, usando os valores das etapas anteriores, você define a chave compartilhada e cria a conexão. A chave que você define deve ser a mesma chave usada na configuração do dispositivo VPN.

1. Defina a chave compartilhada e crie a conexão.

   * Altere o valor-VNetName e o valor-LocalNetworkSiteName. Ao especificar um nome que contenha espaços, use aspas simples ao redor do valor.
   * O '-SharedKey ' é um valor que você gera e, em seguida, especifica. No exemplo, usamos ' abc123 ', mas você pode (e deve) gerar algo mais complexo. O importante é que o valor especificado aqui deve ser o mesmo valor especificado ao configurar seu dispositivo VPN.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName '6C74F6E6_Site2' -SharedKey abc123
   ```

1. Quando a conexão é criada, o resultado é: **Status: Bem-sucedida**.

## <a name="verify-your-connection"></a><a name="verify"></a>Verificar a conexão

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

Se você estiver tendo problemas para se conectar, consulte a seção **Solucionar problemas** do sumário no painel esquerdo.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Como redefinir um gateway de VPN

Redefinir um gateway de VPN do Azure é útil se você perde a conectividade VPN entre locais em um ou mais túneis de VPN site a site. Nessa situação, os dispositivos VPN locais estão funcionando corretamente, mas não são capazes de estabelecer túneis IPsec com os gateways de VPN do Azure. Para obter as etapas, consulte [Redefinir um gateway de VPN](./reset-gateway.md#resetclassic).

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Como alterar um SKU de gateway

Para obter as etapas para alterar um SKU de gateway, consulte [redimensionar um SKU de gateway](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>Próximas etapas

* Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](../index.yml).
* Para obter informações sobre o túnel forçado, consulte [sobre o túnel forçado](vpn-gateway-about-forced-tunneling.md).