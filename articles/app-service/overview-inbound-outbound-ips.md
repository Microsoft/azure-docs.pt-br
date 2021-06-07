---
title: Endereços IP de entrada/saída
description: Saiba como os endereços IP de entrada e saída são usados no serviço Azure App, quando eles mudam e como encontrar os endereços para seu aplicativo.
ms.topic: article
ms.date: 08/25/2020
ms.custom: seodec18
ms.openlocfilehash: 4237e51251a7ece05800aa7efa328a9c6cf65e76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591360"
---
# <a name="inbound-and-outbound-ip-addresses-in-azure-app-service"></a>Endereços IP de entrada e saída no Serviço de Aplicativo do Azure

O [Serviço de Aplicativo do Azure](overview.md) é um serviço multilocatário, exceto para [Ambientes de Serviço de Aplicativo](environment/intro.md). Aplicativos que não estão em um ambiente de Serviço de Aplicativo (não na [Camada isolada](https://azure.microsoft.com/pricing/details/app-service/)) compartilham a infraestrutura de rede com outros aplicativos. Como resultado, os endereços IP de entrada e saída de um aplicativo podem ser diferentes e até alterar em determinadas situações.

[Ambientes de Serviço de Aplicativo](environment/intro.md) usam infraestruturas de rede dedicadas para que aplicativos executados em um ambiente de Serviço de Aplicativo obtenham endereços IP dedicados e estáticos para conexões de entrada e saída.

## <a name="how-ip-addresses-work-in-app-service"></a>Como os endereços IP funcionam no serviço de aplicativo

Um aplicativo do serviço de aplicativo é executado em um plano do serviço de aplicativo, e os planos do serviço de aplicativo são implantados em uma das unidades de implantação na infraestrutura do Azure (internamente chamada de espaço Web). Cada unidade de implantação recebe um conjunto de endereços IP virtuais, que inclui um endereço IP de entrada público e um conjunto de [endereços IP de saída](#find-outbound-ips). Todos os planos do serviço de aplicativo na mesma unidade de implantação e as instâncias de aplicativo que são executados neles compartilham o mesmo conjunto de endereços IP virtuais. Para um Ambiente do Serviço de Aplicativo (um plano do serviço de aplicativo na [camada isolada](https://azure.microsoft.com/pricing/details/app-service/)), o plano do serviço de aplicativo é a própria unidade de implantação e, portanto, os endereços IP virtuais são dedicados a ele como resultado.

Como você não tem permissão para mover um plano do serviço de aplicativo entre as unidades de implantação, os endereços IP virtuais atribuídos ao seu aplicativo geralmente permanecem os mesmos, mas há exceções.

## <a name="when-inbound-ip-changes"></a>Quando ocorrem alterações do IP de entrada

Independentemente do número de instâncias dimensionadas, cada aplicativo tem um único endereço IP de entrada. O endereço IP de entrada pode alterar ao executar uma das ações a seguir:

- Exclua um aplicativo e recrie-o em um grupo de recursos diferente (a unidade de implantação pode ser alterada).
- Excluir o último aplicativo em um grupo de recursos _e_ uma combinação de região e recriá-lo (a unidade de implantação pode ser alterada).
- Exclua uma associação TLS/SSL baseada em IP existente, como durante a renovação do certificado (consulte [renovar certificado](configure-ssl-certificate.md#renew-certificate)).

## <a name="find-the-inbound-ip"></a>Localizar o IP de entrada

Basta executar o seguinte comando em um terminal local:

```bash
nslookup <app-name>.azurewebsites.net
```

## <a name="get-a-static-inbound-ip"></a>Obter um IP de entrada estático

Às vezes, é possível querer um endereço IP dedicado e estático para o aplicativo. Para obter um endereço IP de entrada estático, você precisa [proteger um domínio personalizado](configure-ssl-bindings.md#secure-a-custom-domain). Se você não precisar realmente da funcionalidade de TLS para proteger seu aplicativo, você pode até mesmo carregar um certificado autoassinado para essa associação. Em uma associação TLS baseada em IP, o certificado é associado ao endereço IP em si, portanto, o serviço de aplicativo provisiona um endereço IP estático para que ele aconteça. 

## <a name="when-outbound-ips-change"></a>Quando ocorrem alterações dos IPs de saída

Independentemente do número de instâncias dimensionadas, cada aplicativo tem um número definido de endereços IP de saída a qualquer momento. Qualquer conexão de saída do aplicativo do Serviço de Aplicativo, como um banco de dados back-end, usa um dos endereços IP de saída como o endereço IP de origem. O endereço IP a ser usado é selecionado aleatoriamente em tempo de execução, de modo que o serviço de back-end deve abrir seu firewall para todos os endereços IP de saída para seu aplicativo.

O conjunto de endereços IP de saída para seu aplicativo é alterado quando você executa uma das seguintes ações:

- Exclua um aplicativo e recrie-o em um grupo de recursos diferente (a unidade de implantação pode ser alterada).
- Excluir o último aplicativo em um grupo de recursos _e_ uma combinação de região e recriá-lo (a unidade de implantação pode ser alterada).
- Dimensione seu aplicativo entre as camadas inferiores (**Basic**, **Standard** e **Premium**) e a camada **Premium v2** (os endereços IP podem ser adicionados ou subtraídos do conjunto).

Você pode encontrar o conjunto de todos os endereços IP de saída possíveis que seu aplicativo pode usar, independentemente dos tipos de preço, procurando a `possibleOutboundIpAddresses` propriedade ou no campo **endereços IP de saída adicionais** na folha **Propriedades** no portal do Azure. Consulte [Localizar IPs de saída](#find-outbound-ips).

## <a name="find-outbound-ips"></a>Localizar IPs de saída

Para localizar os endereços IP de saída atualmente usados pelo aplicativo no Portal do Azure, clique em **Propriedades** na navegação esquerda do aplicativo. Eles são listados no campo **endereços IP de saída** .

É possível localizar as mesmas informações, executando o comando a seguir no [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).OutboundIpAddresses
```

Para localizar _todos os_ endereços IP de saída possíveis para seu aplicativo, independentemente dos tipos de preço, clique em **Propriedades** na navegação esquerda do seu aplicativo. Eles são listados no campo **endereços IP de saída adicionais** .

É possível localizar as mesmas informações, executando o comando a seguir no [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

```azurepowershell
(Get-AzWebApp -ResourceGroup <group_name> -name <app_name>).PossibleOutboundIpAddresses
```

## <a name="next-steps"></a>Próximas etapas

Saiba como restringir o tráfego de entrada por endereços IP de origem.

> [!div class="nextstepaction"]
> [Restrições de IP estático](app-service-ip-restrictions.md)
