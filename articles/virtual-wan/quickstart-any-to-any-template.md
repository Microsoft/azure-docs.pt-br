---
title: 'Início rápido: Criar uma configuração any-to-any usando um modelo do ARM'
titleSuffix: Azure Virtual WAN
description: Este guia de início rápido mostra como criar uma configuração any-to-any usando um modelo do ARM (modelo do Azure Resource Manager).
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: quickstart
ms.date: 02/02/2021
ms.author: cherylmc
ms.custom: subject-armqs
ms.openlocfilehash: d31f490baec49e8e0b6fcf89caa8c19202fdf763
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102431373"
---
# <a name="quickstart-create-an-any-to-any-configuration-using-an-arm-template"></a>Início rápido: Criar uma configuração any-to-any usando um modelo do ARM

Este guia de início rápido descreve como usar um modelo do ARM (modelo do Azure Resource Manager) para criar um cenário any-to-any em que qualquer spoke possa acessar outro spoke.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f201-virtual-wan-with-all-gateways%2fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.
* Os dados do certificado de chave pública são necessários para essa configuração. Os dados de exemplo são fornecidos no artigo. No entanto, os dados de exemplo são fornecidos apenas para atender aos requisitos de modelo a fim de criar um gateway P2S. Depois que o modelo for concluído e os recursos forem implantados, você precisará atualizar esse campo com seus dados de certificado para que a configuração funcione. Confira [Certificados de VPN do usuário](certificates-point-to-site.md#cer).

## <a name="review-the-template"></a><a name="review"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/201-virtual-wan-with-all-gateways). O modelo deste artigo é muito longo para ser mostrado aqui. Para ver o modelo, confira [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/201-virtual-wan-with-all-gateways/azuredeploy.json).

Neste guia de início rápido, você criará uma implantação de vários hubs da WAN Virtual do Azure, incluindo todos os gateways e as conexões VNET. A lista de parâmetros de entrada foi mantida intencionalmente com o mínimo. O esquema de endereçamento IP pode ser alterado modificando as variáveis dentro do modelo. O cenário é explicado mais detalhadamente no artigo [Cenário da conectividade any-to-any](scenario-any-to-any.md).

:::image type="content" source="./media/routing-scenarios/any-any/figure-1.png" alt-text="Arquitetura de implantação":::

Este modelo cria um ambiente da WAN Virtual do Azure totalmente funcional com os seguintes recursos:

* Dois hubs distintos em regiões diferentes
* Quatro redes virtuais do Azure (VNets)
* Duas conexões VNET para cada hub da VWAN
* Um gateway de VPN P2S (ponto a site) em cada hub
* Um gateway de VPN S2S (site a site) em cada hub
* Um gateway do ExpressRoute em cada hub

Vários recursos do Azure são definidos no modelo:

* [**Microsoft.Network/virtualwans**](/azure/templates/microsoft.network/virtualwans)
* [**Microsoft.Network/virtualhubs**](/azure/templates/microsoft.network/virtualhubs)
* [**Microsoft.Network/virtualnetworks**](/azure/templates/microsoft.network/virtualnetworks)
* [**Microsoft.Network/hubvirtualnetworkconnections**](/azure/templates/microsoft.network/virtualhubs/hubvirtualnetworkconnections)
* [**Microsoft.Network/p2svpngateways**](/azure/templates/microsoft.network/p2svpngateways)
* [**Microsoft.Network/vpngateways**](/azure/templates/microsoft.network/vpngateways) 
* [**Microsoft.Network/expressroutegateways**](/azure/templates/microsoft.network/expressroutegateways)
* [**Microsoft.Network/vpnserverconfigurations**](/azure/templates/microsoft.network/vpnserverconfigurations)

>[!NOTE]
> Esse modelo do ARM não cria os recursos do lado do cliente necessários para a conectividade híbrida. Depois de implantar o modelo, você ainda precisará criar e configurar os clientes VPN P2S, os branches de VPN (sites locais) e conectar os circuitos do ExpressRoute.
>

Para encontrar mais modelos, confira [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a><a name="deploy"></a>Implantar o modelo

Para implantar esse modelo corretamente, você precisará usar o botão Implantar no Azure e o portal do Azure, em vez de outros métodos, pelos seguintes motivos:

* Para criar a configuração de P2S, você precisará carregar os dados do certificado raiz. O campo de dados não aceita os dados do certificado durante o uso do PowerShell ou da CLI.
* Esse modelo não funciona corretamente com o uso do Cloud Shell devido ao upload de dados do certificado.
* Além disso, você pode modificar com facilidade o modelo e os parâmetros no portal para acomodar intervalos de endereços IP e outros valores.

1. Clique em **Implantar no Azure**.

   [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f201-virtual-wan-with-all-gateways%2fazuredeploy.json)
1. Para ver o modelo, clique em **Editar modelo**. Nessa página, você poderá ajustar alguns dos valores, como o espaço de endereço ou o nome de determinados recursos. Clique em **Salvar** para salvar as alterações ou **Descartar**.
1. Na página do modelo, insira os valores. Para este modelo, os dados do certificado público P2S são necessários. Se você estiver usando este artigo como um exercício, use os dados a seguir deste arquivo .cer como os dados de exemplo para os dois hubs. Depois que o modelo for executado e a implantação for concluída, para usar a configuração de P2S, você precisará substituir essas informações pelos [dados do certificado](certificates-point-to-site.md#cer) da chave pública para sua implantação.

   ```certificate-data
   MIIC5zCCAc+gAwIBAgIQGxd3Av1q6LJDZ71e3TzqcTANBgkqhkiG9w0BAQsFADAW
   MRQwEgYDVQQDDAtQMlNSb290Q2VydDAeFw0yMDExMDkyMjMxNTVaFw0yMTExMDky
   MjUxNTVaMBYxFDASBgNVBAMMC1AyU1Jvb3RDZXJ0MIIBIjANBgkqhkiG9w0BAQEF
   AAOCAQ8AMIIBCgKCAQEA33fFra/E0YmGuXLKmYcdvjsYpKwQmw8DjjDkbwhE9jcc
   Dp50e7F1P6Rxo1T6Hm3dIhEji+0QkP4Ie0XPpw0eW77+RWUiG9XJxGqtJ3Q4tyRy
   vBfsHORcqMlpV3VZOXIxrk+L/1sSm2xAc2QGuOqKaDNNoKmjrSGNVAeQHigxbTQg
   zCcyeuhFxHxAaxpW0bslK2hEZ9PhuAe22c2SHht6fOIDeXkadzqTFeV8wEZdltLr
   6Per0krxf7N2hFo5Cfz0KgWlvgdKLL7dUc9cjHo6b6BL2pNbLh8YofwHQOQbwt6H
   miAkEnx1EJ5N8AWuruUTByR2jcWyCnEAUSH41+nk4QIDAQABozEwLzAOBgNVHQ8B
   Af8EBAMCAgQwHQYDVR0OBBYEFJMgnJSYHH5AJ+9XB11usKRwjbjNMA0GCSqGSIb3
   DQEBCwUAA4IBAQBOy8Z5FBd/nvgDcjvAwNCw9h5RHzgtgQqDP0qUjEqeQv3ALeC+
   k/F2Tz0OWiPEzX5N+MMrf/jiYsL2exXuaPWCF5U9fu8bvs89GabHma8MGU3Qua2x
   Imvt0whWExQMjoyU8SNUi2S13fnRie9ZlSwNh8B/OIUUEtVhQsd4OfuZZFVH4xGp
   ibJMSMe5JBbZJC2tCdSdTLYfYJqrLkVuTjynXOjmz2JXfwnDNqEMdIMMjXzlNavR
   J8SNtAoptMOK5vAvlySg4LYtFyXkl0W0vLKIbbHf+2UszuSCijTUa3o/Y1FoYSfi
   eJH431YTnVLuwdd6fXkXFBrXDhjNsU866+hE
   ```

1. Quando terminar de inserir os valores, selecione **Examinar + criar**.
1. Na página **Examinar + criar**, após as aprovações de validação, selecione **Criar**.
1. A conclusão da implantação leva cerca de 75 minutos. Veja o progresso na página **Visão geral** do modelo.  Se você fechar o portal, a implantação continuará.

   :::image type="content" source="./media/quickstart-any-to-any-template/template.png" alt-text="Exemplo de implantação concluída":::

## <a name="validate-the-deployment"></a><a name="validate"></a>Validar a implantação

1. Entre no [portal do Azure](https://portal.azure.com).
1. Escolha **Grupos de recursos** no painel esquerdo.
1. Selecione o grupo de recursos criado na seção anterior. Na página **Visão geral**, você verá algo semelhante a este exemplo: :::image type="content" source="./media/quickstart-any-to-any-template/resources.png" alt-text="Exemplo de recursos" lightbox="./media/quickstart-any-to-any-template/resources.png":::

1. Clique na WAN virtual para ver os hubs. Na página da WAN virtual, clique em cada hub para ver as conexões e outras informações do hub.
   :::image type="content" source="./media/quickstart-any-to-any-template/hub.png" alt-text="Exemplos de hubs" lightbox="./media/quickstart-any-to-any-template/hub.png":::

## <a name="complete-the-hybrid-configuration"></a><a name="complete"></a>Concluir a configuração híbrida

O modelo não define todas as configurações necessárias para uma rede híbrida. Você precisará concluir as configurações a seguir, dependendo dos seus requisitos.

* [Configurar os branches de VPN – sites locais](virtual-wan-site-to-site-portal.md#site)
* [Concluir a configuração de VPN P2S](virtual-wan-point-to-site-portal.md)
* [Conectar os circuitos do ExpressRoute](virtual-wan-expressroute-portal.md)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não precisar mais dos recursos que criou, exclua-os. Alguns dos recursos da WAN Virtual precisam ser excluídos em determinada ordem devido às dependências. A exclusão poderá levar cerca de 30 minutos para ser concluída.

[!INCLUDE [Delete resources](../../includes/virtual-wan-resource-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Concluir a configuração de VPN P2S](virtual-wan-point-to-site-portal.md)

> [!div class="nextstepaction"]
> [Configurar os branches de VPN – sites locais](virtual-wan-site-to-site-portal.md#site)

> [!div class="nextstepaction"]
> [Conectar os circuitos do ExpressRoute](virtual-wan-expressroute-portal.md)