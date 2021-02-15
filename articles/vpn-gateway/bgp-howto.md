---
title: 'Configurar BGP para gateway de VPN: Portal'
titleSuffix: Azure VPN Gateway
description: Este artigo orienta você pelas etapas para configurar o BGP com o gateway de VPN do Azure usando o PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/18/2020
ms.author: yushwang
ms.openlocfilehash: db19b1ae017fa7981747b0e7b4c82e97efc61ed3
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878877"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways"></a>Como configurar o BGP em gateways de VPN do Azure

Este artigo orienta você pelas etapas para habilitar o BGP em uma conexão VPN S2S (site a site) entre locais e uma conexão VNet a VNet usando o portal do Azure.

## <a name="about-bgp"></a><a name="about"></a>Sobre o BGP

O BGP é o protocolo de roteamento padrão usado na Internet para a troca de informações de roteamento e acessibilidade entre duas ou mais redes. O BGP habilita os gateways de VPN do Azure e seus dispositivos VPN locais, chamados de vizinhos ou pares de BGP, para trocar "rotas" que informarão os dois gateways sobre a disponibilidade e a acessibilidade para que esses prefixos passem pelos gateways ou roteadores envolvidos. O BGP também pode habilitar o roteamento de trânsito entre várias redes propagando rotas que um gateway BGP obtém de um par no nível de protocolo BGP para todos os outros pares no nível de protocolo BGP.

Para obter mais informações sobre os benefícios do BGP e para entender os requisitos técnicos e as considerações de como usar o BGP, consulte [visão geral do BGP com gateways de VPN do Azure](vpn-gateway-bgp-overview.md).

## <a name="getting-started"></a>Introdução

Cada parte deste artigo ajuda você a formar um bloco de construção básico para habilitar o BGP em sua conectividade de rede. Se você concluir todas as três partes, crie a topologia conforme mostrado no diagrama 1.

**Diagrama 1**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Diagrama mostrando a arquitetura e as configurações de rede" border="false":::

Você pode combinar essas partes para criar uma rede de trânsito mais complexa, com saltos múltiplos e que atenda às suas necessidades.

### <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

## <a name="part-1-configure-bgp-on-the-virtual-network-gateway"></a><a name ="config"></a>Parte 1: configurar o BGP no gateway de rede virtual

Nesta seção, você criará e configurará uma rede virtual, criará e configurará um gateway de rede virtual com parâmetros BGP e obterá o endereço IP do par de BGP do Azure. O diagrama 2 mostra as definições de configuração a serem usadas ao trabalhar com as etapas nesta seção.

**Diagrama 2**

:::image type="content" source="./media/bgp-howto/bgp-gateway.png" alt-text="Diagrama mostrando as configurações do gateway de rede virtual" border="false":::

### <a name="1-create-and-configure-testvnet1"></a>1. criar e configurar o TestVNet1

Nesta etapa, você criará e configurará o TestVNet1. Use as etapas no [tutorial criar um gateway](./tutorial-create-gateway-portal.md) para criar e configurar sua rede virtual do Azure e o gateway de VPN. Use as configurações de referência nas capturas de tela abaixo.

* Rede virtual:

   :::image type="content" source="./media/bgp-howto/testvnet-1.png" alt-text="TestVNet1 com prefixos de endereço correspondentes":::

* Sub-redes:

   :::image type="content" source="./media/bgp-howto/testvnet-1-subnets.png" alt-text="Sub-redes TestVNet1":::

### <a name="2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>2. criar o gateway de VPN para TestVNet1 com parâmetros BGP

Nesta etapa, você cria um gateway de VPN com os parâmetros BGP correspondentes.

1. No portal do Azure, navegue até o recurso de **Gateway de rede virtual** no Marketplace e selecione **criar**.

1. Preencha os parâmetros conforme mostrado abaixo:

   :::image type="content" source="./media/bgp-howto/create-gateway-1.png" alt-text="Criar VNG1":::

1. Na seção **Configurar BGP** na página realçada, defina as seguintes configurações:

   :::image type="content" source="./media/bgp-howto/create-gateway-1-bgp.png" alt-text="Configurar BGP":::

   * Selecione **Configurar BGP**  -  **habilitado** para mostrar a seção de configuração de BGP.

   * Preencha seu ASN (número do sistema autônomo).

   * O campo **endereço IP do BGP APIPA do Azure** é opcional. Se os dispositivos VPN locais usarem o endereço APIPA para BGP, você deverá selecionar um endereço do intervalo de endereços APIPA reservado do Azure para VPN, que é de **169.254.21.0** para **169.254.22.255**. Este exemplo usa 169.254.21.11.

   * Se você estiver criando um gateway de VPN ativo-ativo, a seção BGP mostrará um **segundo endereço IP APIPA do Azure personalizado** adicional. Especifique um endereço diferente do intervalo APIPA permitido (**169.254.21.0** para **169.254.22.255**).

   > [!IMPORTANT]
   >
   > * Por padrão, o Azure atribui um endereço IP privado do intervalo de prefixo GatewaySubnet automaticamente como o endereço IP do BGP do Azure no gateway de VPN do Azure. O endereço BGP do APIPA do Azure personalizado é necessário quando os dispositivos VPN locais usam um endereço APIPA (169.254.0.1 a 169.254.255.254) como o IP do BGP. O gateway de VPN do Azure escolherá o endereço APIPA personalizado se o recurso de gateway de rede local correspondente (rede no local) tiver um endereço APIPA como o IP do par de BGP. Se o gateway de rede local usar um endereço IP regular (não APIPA), o gateway de VPN do Azure será revertido para o endereço IP privado do intervalo de GatewaySubnet.
   >
   > * Os endereços BGP do APIPA não devem se sobrepor entre os dispositivos VPN locais e todos os gateways de VPN do Azure conectados.
   >

1. Selecione **Examinar + criar** para executar a validação. Depois que a validação for aprovada, selecione **Criar** para implantar o Gateway de VPN. Um gateway pode levar até 45 minutos para ser criado e implantado por completo. Você poderá ver o status de implantação na página de Visão Geral do gateway.

### <a name="3-obtain-the-azure-bgp-peer-ip-addresses"></a>3. obter os endereços IP do par de BGP do Azure

Depois que o gateway for criado, você poderá obter os endereços IP do par de BGP no gateway de VPN do Azure. Esses endereços são necessários para configurar seus dispositivos VPN locais para estabelecer sessões BGP com o gateway de VPN do Azure.

1. Navegue até o recurso de gateway de rede virtual e selecione a página **configuração** para ver as informações de configuração de BGP, conforme mostrado na captura de tela a seguir. Nessa página, você pode exibir todas as informações de configuração do BGP no gateway de VPN do Azure: ASN, endereço IP público e endereços IP do par de BGP correspondentes no lado do Azure (padrão e APIPA).

   :::image type="content" source="./media/bgp-howto/vnet-1-gw-bgp.png" alt-text="Gateway BGP":::

1. Na página **configuração** , você pode fazer as seguintes alterações de configuração:

   * Você pode atualizar a ASN ou o endereço IP do protocolo APIPA, se necessário.
   * Se você tiver um gateway de VPN ativo-ativo, essa página mostrará o endereço IP público, o padrão e os endereços IP de BGP do APIPA da segunda instância do gateway de VPN do Azure.

1. Se você fez alterações, selecione **salvar** para confirmar as alterações no gateway de VPN do Azure.

## <a name="part-2-configure-bgp-on-cross-premises-s2s-connections"></a><a name ="crosspremises"></a>Parte 2: configurar o BGP em conexões S2S entre locais

Para estabelecer uma conexão entre locais, você precisa criar um gateway de *rede local* para representar o dispositivo VPN local e uma *conexão* para conectar o gateway de VPN com o gateway de rede local, conforme explicado em [criar conexão site a site](./tutorial-site-to-site-portal.md). Este artigo contém as propriedades adicionais necessárias para especificar os parâmetros de configuração do BGP.

**Diagrama 3**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises.png" alt-text="Diagrama mostrando IPsec" border="false":::

### <a name="1-configure-bgp-on-the-local-network-gateway"></a>1. configurar o BGP no gateway de rede local

Nesta etapa, você configura o BGP no gateway de rede local. Use a captura de tela a seguir como exemplo. A captura de tela mostra o site5 (gateway de rede local) com os parâmetros especificados no diagrama 3.

:::image type="content" source="./media/bgp-howto/create-local-bgp.png" alt-text="Configurar o BGP para o gateway de rede local":::

#### <a name="important-configuration-considerations"></a>Considerações importantes sobre configuração

* O endereço IP do par de BGP e do ASN deve corresponder à configuração do roteador VPN local.
* Você pode deixar o **espaço de endereço** vazio somente se estiver usando o BGP para se conectar a essa rede. O gateway de VPN do Azure adicionará internamente uma rota de seu endereço IP de par de BGP ao túnel IPsec correspondente. Se você **não** estiver usando o BGP entre o gateway de VPN do Azure e essa rede específica, **deverá** fornecer uma lista de prefixos de endereço válidos para o **espaço de endereço**.
* Opcionalmente, você pode usar um **endereço IP APIPA** (169.254. x. x) como seu IP de par BGP local, se necessário. Mas você também precisará especificar um endereço IP APIPA, conforme descrito anteriormente neste artigo para o gateway de VPN do Azure, caso contrário, a sessão BGP não poderá estabelecer para essa conexão.
* Você pode inserir as informações de configuração de BGP durante a criação do gateway de rede local ou pode adicionar ou alterar a configuração de BGP na página de **configuração** do recurso de gateway de rede local.

**Exemplo**

Este exemplo usa um endereço APIPA (169.254.100.1) como o endereço IP do par de BGP local:

:::image type="content" source="./media/bgp-howto/local-apipa.png" alt-text="Gateway de rede local APIPA e BGP":::

### <a name="2-configure-a-s2s-connection-with-bgp-enabled"></a>2. configurar uma conexão S2S com BGP habilitado

Nesta etapa, você cria uma nova conexão que tem o BGP habilitado. Se você já tiver uma conexão e quiser habilitar o BGP nela, poderá [atualizar uma conexão existente](#update).

#### <a name="to-create-a-connection-with-bgp-enabled"></a>Para criar uma conexão com BGP habilitado

Para criar uma nova conexão com o BGP habilitado, na página **Adicionar conexão** , preencha os valores e, em seguida, marque a opção **habilitar BGP** para habilitar o BGP nesta conexão. Selecione **OK** para criar a conexão.

:::image type="content" source="./media/bgp-howto/ipsec-connection-bgp.png" alt-text="Conexão IPsec entre locais com BGP":::

#### <a name="to-update-an-existing-connection"></a><a name ="update"></a>Para atualizar uma conexão existente

Se você quiser alterar a opção de BGP em uma conexão, navegue até a página de **configuração** do recurso de conexão e, em seguida, alterne a opção **BGP** conforme realçado no exemplo a seguir. Selecione **Salvar** para salvar as alterações.

:::image type="content" source="./media/bgp-howto/update-bgp.png" alt-text="Atualizar o BGP para uma conexão":::

## <a name="part-3-configure-bgp-on-vnet-to-vnet-connections"></a><a name ="v2v"></a>Parte 3: configurar o BGP em conexões de VNet para VNet

As etapas para habilitar ou desabilitar o BGP em uma conexão VNet para VNet são as mesmas que as etapas S2S na [parte 2](#crosspremises). Você pode habilitar o BGP ao criar a conexão ou atualizar a configuração em uma conexão de VNet para VNet existente.

>[!NOTE] 
>Uma conexão VNet a VNet sem BGP limitará a comunicação apenas com as duas VNets conectadas. Habilite o BGP para permitir o recurso de roteamento de trânsito para outras conexões S2S ou VNet para VNet desses dois VNets.
>

Para o contexto, referindo-se ao **diagrama 4**, se o BGP fosse desabilitado entre TestVNet2 e TestVNet1, TestVNet2 não aprenderia as rotas para a rede local, site5 e, portanto, não podia se comunicar com o site 5. Depois de habilitar o BGP, conforme mostrado no diagrama 4, todas as três redes poderão se comunicar pelas conexões de IPsec e VNet para VNet.

**Diagrama 4**

:::image type="content" source="./media/bgp-howto/bgp-crosspremises-v2v.png" alt-text="Diagrama mostrando a rede completa" border="false":::

## <a name="next-steps"></a>Próximas etapas

Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma máquina virtual](../virtual-machines/windows/quick-create-portal.md) para obter as etapas.