---
title: 'WAN virtual do Azure: criar uma solução de virtualização de rede (NVA) no Hub'
description: Saiba como implantar uma solução de virtualização de rede no Hub WAN virtual.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a Network Virtual Appliance (NVA) in my Virtual WAN hub.
ms.openlocfilehash: 1f80da4014d4b6b27155db17d32800e54b51f40d
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258058"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Como criar uma solução de virtualização de rede em um hub de WAN virtual do Azure (versão prévia)

Este artigo mostra como usar a WAN virtual para se conectar aos seus recursos no Azure por meio de uma NVA ( **solução de virtualização de rede** ) no Azure. Esse tipo de conexão exige um dispositivo VPN localizado no local que tenha um endereço IP público voltado para o exterior atribuído a ele. Para obter mais informações sobre WAN virtual, consulte o [que é WAN virtual?](virtual-wan-about.md).

As etapas neste artigo o ajudarão a criar uma solução de virtualização de rede de **Wan CloudGen Barracuda** no Hub de WAN virtual. Para concluir este exercício, você deve ter um dispositivo de nuvem Barracuda (CPE) e uma licença para o dispositivo de WAN Barracuda CloudGen que você implanta no hub antes de começar.

Para obter a documentação de implantação do **Cisco SD-Wan** na WAN virtual do Azure, confira [Cisco Cloud enrampa para várias nuvens](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701). 

## <a name="prerequisites"></a>Pré-requisitos

Verifique se você atende aos seguintes critérios antes de iniciar a configuração:

* Obtenha uma licença para o gateway de WAN do Barracuda CloudGen. Para saber mais sobre como fazer isso, consulte a [documentação do Barracuda CLOUDGEN Wan](https://www.barracuda.com/products/cloudgenwan)

* Você tem uma rede virtual à qual deseja se conectar. Verifique se nenhuma das sub-redes das redes locais se sobrepõe às redes virtuais às quais você deseja se conectar. Para criar uma rede virtual no portal do Azure, consulte o [Início Rápido](../virtual-network/quick-create-portal.md).

* Sua rede virtual não tem gateways de rede virtual. Se sua rede virtual tem um gateway (VPN ou ExpressRoute), remova todos os gateways. Essa configuração requer que as redes virtuais sejam conectadas ao gateway do hub da WAN Virtual.

* Obtenha um intervalo de endereços IP para sua região de hub. O hub é uma rede virtual criada e usada pela WAN Virtual. O intervalo de endereços especificado para o hub não pode se sobrepor a nenhuma das redes virtuais existentes às quais você se conecta. Ele também não pode se sobrepor aos intervalos de endereços aos quais você se conecta localmente. Se não estiver familiarizado com os intervalos de endereços IP da sua configuração de rede local, trabalhe com alguém que possa lhe fornecer esses detalhes.

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Criar uma WAN Virtual

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Criar um hub

Um hub é uma rede virtual que pode conter gateways para a funcionalidade site a site, ExpressRoute, ponto a site ou solução de virtualização de rede. Depois que o hub é criado, você será cobrado por ele, mesmo se não anexar sites. Se você optar por criar um gateway de VPN site a site, levará 30 minutos para criar o gateway de VPN site a site no Hub virtual. Ao contrário do site a site, do ExpressRoute ou ponto a site, o hub deve ser criado primeiro para que você possa implantar um dispositivo de rede virtual na VNet do Hub.

1. Localize o WAN Virtual que você criou. Na página **Wan virtual** , na seção **conectividade** , selecione **hubs**.
1. Na página **hubs** , selecione + novo hub para abrir a página **criar Hub virtual** .

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="Noções básicas":::
1. Na página **Criar Hub Virtual**, na guia **Básico**, preencha os seguintes campos:

   **Detalhes do projeto**

   * Região (anteriormente conhecida como Local)
   * Nome
   * Espaço de endereço privado do hub. O espaço de endereço mínimo é /24 para criar um hub, o que significa que qualquer intervalo entre /25 e /32 produzirá um erro durante a criação. A WAN virtual do Azure, sendo um serviço gerenciado pela Microsoft, cria as sub-redes apropriadas no Hub virtual para os diferentes gateways/serviços. (Por exemplo: soluções de virtualização de rede, gateways de VPN, gateways de ExpressRoute, gateways de ponto a site e VPN de usuário, firewall, roteamento, etc.). Não há necessidade de o usuário planejar explicitamente o espaço de endereço de sub-rede para os serviços no Hub virtual porque a Microsoft faz isso como parte do serviço.
1. Selecione **Examinar + Criar** para validar.
1. Selecione **Criar** para criar o hub.

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>Criar a solução de virtualização de rede no Hub

Nesta etapa, você criará uma solução de virtualização de rede no Hub. O procedimento para cada NVA será diferente para cada produto do parceiro NVA. Para este exemplo, estamos criando um gateway de WAN Barracuda CloudGen.

1. Localize o Hub de WAN virtual que você criou na etapa anterior e abra-o.

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="Hub virtual":::
1. Localize o bloco dispositivos de rede virtual e selecione o link **criar** .
1. Na folha da **solução de virtualização de rede** , selecione **Barracuda CloudGen Wan** e, em seguida, selecione o botão **criar** .

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="Selecionar NVA":::
1. Isso levará você para a oferta do Azure Marketplace para o gateway de WAN do Barracuda CloudGen. Leia os termos e, em seguida, selecione o botão **criar** quando estiver pronto.

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="Noções básicas do Barracuda NVA":::
1. Na página **noções básicas** , você precisará fornecer as seguintes informações:

   * **Assinatura** – escolha a assinatura que você usou para implantar a WAN virtual e o Hub.
   * **Grupo de recursos** -escolha o mesmo grupo de recursos usado para implantar a WAN e o Hub virtuais.
   * **Região** -escolha a mesma região em que o recurso de Hub virtual está localizado.
   * **Nome do aplicativo** – o Barracuda NextGen Wan é um aplicativo gerenciado. Escolha um nome que facilite a identificação desse recurso, pois é isso que ele será chamado quando aparecer em sua assinatura.
   * **Grupo de recursos gerenciados** – esse é o nome do grupo de recursos gerenciado no qual o Barracuda implantará os recursos que são gerenciados por eles. O nome deve ser preenchido previamente para isso.
1. Selecione o botão **Avançar: CLOUDGEN Wan gateway** .

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="Gateway de WAN CloudGen":::
1. Forneça as seguintes informações aqui:

   * **Hub de WAN virtual** -o Hub de WAN virtual no qual você deseja implantar esse NVA.
   * **Unidades de infraestrutura NVA** -indique o número de unidades de infraestrutura NVA com as quais você deseja implantar esse NVA. Escolha a quantidade de capacidade de largura de banda agregada que você deseja fornecer em todos os sites de ramificação que serão conectados a esse Hub por meio desse NVA.
   * O **token** -Barracuda requer que você forneça um token de autenticação aqui para se identificar como um usuário registrado deste produto. Você precisará obtê-lo do Barracuda.
1. Selecione o botão **revisar e criar** para continuar.
1. Nesta página, você será solicitado a aceitar os termos do contrato de acesso de Co-Admin. Isso é padrão com aplicativos gerenciados em que o Publicador terá acesso a alguns recursos nessa implantação. Marque a caixa **concordo com os termos e condições acima** e, em seguida, selecione **criar**.

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Conectar a VNet ao hub

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a WAN virtual, consulte a página [o que é WAN virtual?](virtual-wan-about.md) .
* Para saber mais sobre o NVAs em um hub de WAN virtual, consulte [sobre a solução de virtualização de rede no Hub de WAN virtual (versão prévia)](about-nva-hub.md).
