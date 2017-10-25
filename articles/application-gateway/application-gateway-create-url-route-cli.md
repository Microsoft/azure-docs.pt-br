---
title: "Criar um Gateway de Aplicativo usando as regras de roteamento de URL – CLI do Azure 2.0 | Microsoft Docs"
description: "Esta página oferece instruções sobre como criar e configurar um gateway de aplicativo usando as regras de roteamento de URL."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: 10d01d5d80e2d111d6b39598eed3612f80162b23
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing-with-azure-cli-20"></a>Criar um Gateway de Aplicativo usando roteamento com base em caminho com a CLI do Azure 2.0

> [!div class="op_single_selector"]
> * [Portal do Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell do Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [CLI 2.0 do Azure](application-gateway-create-url-route-cli.md)

Com o roteamento com base em caminho de URL, você associa rotas com base no caminho de URL das solicitações HTTP. Ele verifica se há uma rota para um pool de servidores back-end configurado para a URL listada no gateway de aplicativo e envia o tráfego de rede para o pool definido. Um uso comum para o roteamento com base em caminho de URL é balancear a carga das solicitações para tipos de conteúdo diferentes para pools de servidores back-end diferentes.

O Gateway de Aplicativo do Azure usa dois tipos de regra: básica e com base no caminho de URL. O tipo de regra básica fornece serviço round robin para os pools de back-end. As regras com base no caminho, além da distribuição round robin, também usam o padrão de caminho da URL de solicitação para escolher o pool de back-ends apropriado.

## <a name="scenario"></a>Cenário

No exemplo a seguir, um gateway de aplicativo fornece o tráfego para contoso.com com dois pools de servidor back-end: o pool de servidores padrão e o pool de servidores de imagem.

Solicitações para http://contoso.com/image* são roteadas para o pool de servidores de imagem (**imagesBackendPool**). Se o padrão de caminho não corresponder, o gateway de aplicativo selecionará o pool padrão de servidor (**appGatewayBackendPool**).

![Roteamento de URL](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="sign-in-to-azure"></a>Entrar no Azure

Abra o **Prompt de comando do Microsoft Azure** e entre:

```azurecli
az login -u "username"
```

> [!NOTE]
> Você também pode usar `az login` sem a opção de logon de dispositivo que exige a inserção de um código em aka.ms/devicelogin.

Depois de inserir o comando anterior, você receberá um código. Acesse https://aka.ms/devicelogin em um navegador para continuar o processo de entrada.

![Cmd mostrando logon do dispositivo][1]

No navegador, digite o código recebido. Isso o redireciona para uma página de entrada.

![Navegador para inserir código][2]

Insira o código para entrar e, em seguida, feche o navegador para continuar.

![Conectado com êxito][3]

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>Adicionar uma regra com base em caminho a um Gateway de Aplicativo existente

As etapas a seguir mostram como adicionar uma regra com base em caminho a um gateway de aplicativo existente.
### <a name="create-a-new-back-end-pool"></a>Criar um novo pool de back-end

Defina a configuração de gateway de aplicativo **imagesBackendPool** para o tráfego de rede com balanceamento de carga no pool de back-end. Neste exemplo, defina as diferentes configurações de pool de back-end para o novo pool de back-end. Cada pool de back-end pode ter sua própria configuração. As regras com base em caminho usam as configurações HTTP de back-end rotear o tráfego para os membros do pool de back-end corretos. Isso determina o protocolo e a porta usados ao enviar tráfego aos membros do pool de back-ends. As configurações de HTTP do back-end também determinam as sessões baseadas em cookies.  Se habilitada, a afinidade de sessão baseada em cookies envia o tráfego para o mesmo back-end das solicitações anteriores para cada pacote.

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port-for-an-application-gateway"></a>Criar uma nova porta de front-end para um gateway de aplicativo

O objeto de configuração de porta front-end é usado por um ouvinte para definir a porta cujo tráfego deve ser ouvido pelo gateway de aplicativo.

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>Criar um novo ouvinte

Esta etapa configura o ouvinte para o endereço IP público, e a porta usada para receber o tráfego de rede. O exemplo a seguir usa a configuração de IP de front-end definida anteriormente, uma configuração de porta front-end e um protocolo (http ou https, que diferencia maiúsculas de minúsculas) e configura o ouvinte. Neste exemplo, o ouvinte ouve o tráfego HTTP na porta 82 no endereço IP público que foi criado anteriormente neste cenário.

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>Criar o mapa de caminho de URL

Esta etapa configura o caminho relativo de URL usado pelo gateway de aplicativo para definir o mapeamento entre o caminho e o pool de back-end atribuído para lidar com o tráfego de entrada.

> [!IMPORTANT]
> Cada caminho deve começar com "/", e o único lugar no qual um asterisco é permitido é no final. Exemplos válidos são /xyz, /xyz* ou /xyz/*. A cadeia de caracteres inserida no correspondente de caminho não inclui qualquer texto após o primeiro “?” ou “#”, e esses caracteres não são permitidos. 

O exemplo a seguir cria uma regra para o caminho /images/\* roteando o tráfego para o **imagesBackendPool** do back-end. Essa regra garante que o tráfego para cada conjunto de URLs seja roteado para o back-end. Por exemplo, http://adatum.com/images/figure1.jpg vai para **imagesBackendPool**. A configuração de mapa do caminho de regra também configurará um pool de endereços de back-end padrão se o caminho não corresponder a nenhuma das regras de caminho predefinidas. Por exemplo, http://adatum.com/shoppingcart/test.html vai para o **pool1**, já que ele está definido como o pool padrão para tráfego sem correspondência.

```azurecli-interactive
az network application-gateway url-path-map create \
--gateway-name AdatumAppGateway \
--name imagespathmap \
--paths /images/* \
--resource-group myresourcegroup2 \
--address-pool imagesBackendPool \
--default-address-pool appGatewayBackendPool \
--default-http-settings appGatewayBackendHttpSettings \
--http-settings appGatewayBackendHttpSettings \
--rule-name images
```

## <a name="next-steps"></a>Próximas etapas

Se você quiser aprender sobre o descarregamento de protocolo SSL, consulte [Configurar um Gateway de Aplicativo para descarregamento SSL](application-gateway-ssl-cli.md).


[scenario]: ./media/application-gateway-create-url-route-cli/scenario.png
[1]: ./media/application-gateway-create-url-route-cli/figure1.png
[2]: ./media/application-gateway-create-url-route-cli/figure2.png
[3]: ./media/application-gateway-create-url-route-cli/figure3.png
