---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 72e61a36b58c0bc666f3e19b71fb1abe842208f5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171767"
---
1. Entre no portal do Azure e selecione **Criar um recurso**. A página **Novo** é aberta.

2. No campo **Pesquisar no marketplace**, insira *gateway de rede virtual* e selecione **Gateway de rede virtual** na lista de busca. 

3. Na página **Gateway da rede virtual**, selecione **Criar** para abrir a página **Criar gateway da rede virtual**.

   ![Criar campos na página do gateway de rede virtual](./media/vpn-gateway-add-gw-rm-portal-include/gw.png "Criar campos na página do gateway de rede virtual")

4. Na página **Criar gateway de rede virtual**, preencha os valores para seu gateway de rede virtual:

   - **Nome**: Insira um nome para o objeto de gateway que você está criando. Esse nome é diferente do nome da sub-rede de gateway. 

   - **Tipo de gateway**: Selecione **VPN** para gateways de VPN. 

   - **Tipo de VPN**: Selecione o Tipo de VPN especificado para sua configuração. A maioria das configurações exige **um tipo de VPN baseado** em rota.

   - **SKU**: Selecione o SKU do gateway no menu suspenso. As SKUs listadas na lista suspensa dependem do tipo de VPN selecionado. Para saber mais sobre os SKUs de gateway, consulte [SKUs de Gateway](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku).

      Selecione apenas **Ativar o modo ativo-ativo** se estiver criando uma configuração de gateway ativa-ativa. Caso contrário, deixe essa configuração não selecionado.
  
   - **Localização**: Talvez seja necessário rolar para ver o **Local**. Defina **Localização** para a localização onde se encontra sua rede virtual. Por exemplo, **Oeste dos EUA**. Se você não definir a localização para a região onde se encontra sua rede virtual, ela não aparecerá na lista suspensa quando você selecionar uma rede virtual.

   - **Rede virtual**: Escolha a rede virtual à qual você deseja adicionar este gateway. Selecione **Rede virtual** para abrir a página **Escolher uma rede virtual** e selecione a VNet. Se você não vir a sua VNet, verifique se o campo **Localização** está apontando para a região na qual sua rede virtual está localizada.

   - **Intervalo de endereços da Sub-rede do Gateway:** Você só verá essa configuração se anteriormente não tiver criado uma sub-rede de gateway para sua rede virtual. Se você criou uma sub-rede de gateway válida previamente, essa configuração não será exibida.

   - **Endereço IP público**: Essa configuração especifica o objeto de endereço IP público associado ao gateway da VPN. O endereço IP público é atribuído dinamicamente a esse objeto quando o gateway de VPN é criado. O gateway de VPN atualmente suporta apenas alocação de endereços IP público *Dinâmico*. No entanto,a alocação dinâmica não significa que o endereço IP muda após ter sido atribuído ao seu gateway de VPN. A única vez em que o endereço IP Público é alterado é quando o gateway é excluído e recriado. Isso não altera o redimensionamento, a redefinição ou outras manutenções/atualizações internas do seu gateway de VPN.
    
      - Deixe **criar novo** selecionado.

      - Na caixa de texto, digite um nome para seu endereço IP público.

   - **Configurar BGP ASN**: Deixe essa configuração desmarcada, a menos que sua configuração especificamente exija. Se você precisar dessa configuração, o ASN padrão será *65515* embora isso possa ser alterado.
     
5. Verifique as configurações e selecione **Criar** para começar a criar o gateway de VPN. As configurações são validadas, e você verá o bloco **Implantar gateway de rede virtual** no painel. A criação de um gateway pode levar até 45 minutos. Talvez seja necessário atualizar a página do portal para ver o status concluído.

6. Depois de criar o gateway, verifique se o endereço IP foi atribuído a ele ao visualizar a rede virtual no portal. O gateway aparecerá como um dispositivo conectado. Você pode selecionar o dispositivo conectado (seu gateway de rede virtual) para exibir mais informações.