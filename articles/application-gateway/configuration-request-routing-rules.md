---
title: Configuração de regras de roteamento de solicitação Aplicativo Azure gateway
description: Este artigo descreve como configurar as regras de roteamento de solicitação do Aplicativo Azure gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 047ce9b33836e2c23a37b1383942323d7c382485
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397528"
---
# <a name="application-gateway-request-routing-rules"></a>Regras de roteamento de solicitação do gateway de aplicativo

Ao criar um gateway de aplicativo usando o portal do Azure, você cria uma regra padrão (*rule1*). Essa regra associa o ouvinte padrão (*appGatewayHttpListener*) ao pool de back-ends padrão (*appGatewayBackendPool*) e as configurações de http de back-end padrão (*appgatewaybackendhttp*). Depois de criar o gateway, você pode editar as configurações da regra padrão ou criar novas regras.

## <a name="rule-type"></a>Tipo de regra

Ao criar uma regra, você escolhe entre [ *básica* e *baseada em caminho*](./application-gateway-components.md#request-routing-rules).

- Escolha básico se desejar encaminhar todas as solicitações no ouvinte associado (por exemplo, *blog <i></i> . contoso.com/ \* )* a um único pool de back-ends.
- Escolha baseado em caminho se desejar rotear solicitações de caminhos de URL específicos para pools de back-end específicos. O padrão de caminho é aplicado somente ao caminho da URL, não aos seus parâmetros de consulta.

### <a name="order-of-processing-rules"></a>Ordem das regras de processamento

Para a SKU v1 e v2, a correspondência de padrões de solicitações de entrada é processada na ordem em que os caminhos são listados no mapa de caminho de URL da regra com base no caminho. Se uma solicitação corresponder ao padrão em dois ou mais caminhos no mapa de caminho, o caminho listado primeiro será correspondido. E a solicitação é encaminhada para o back-end associado a esse caminho.

## <a name="associated-listener"></a>Ouvinte associado

Associe um ouvinte à regra para que a *regra de roteamento de solicitação* associada ao ouvinte seja avaliada para determinar o pool de back-ends para o qual rotear a solicitação.

## <a name="associated-back-end-pool"></a>Pool de back-ends associado

Associe à regra o pool de back-end que contém os destinos de back-end que atendem às solicitações que o ouvinte recebe.

 - Para uma regra básica, apenas um pool de back-end é permitido. Todas as solicitações no ouvinte associado são encaminhadas para esse pool de back-end.

 - Para uma regra com base em caminho, adicione vários pools de back-end que correspondem a cada caminho de URL. As solicitações que correspondem ao caminho de URL inserido são encaminhadas para o pool de back-ends correspondente. Além disso, adicione um pool de back-ends padrão. As solicitações que não correspondem a nenhum caminho de URL na regra são encaminhadas para esse pool.

## <a name="associated-back-end-http-setting"></a>Configuração de HTTP de back-end associada

Adicione uma configuração de HTTP de back-end para cada regra. As solicitações são roteadas do gateway de aplicativo para os destinos de back-end usando o número da porta, o protocolo e outras informações especificadas nessa configuração.

Para uma regra básica, apenas uma configuração de HTTP de back-end é permitida. Todas as solicitações no ouvinte associado são encaminhadas para os destinos de back-end correspondentes usando essa configuração de HTTP.

Para uma regra com base em caminho, adicione várias configurações de HTTP de back-end que correspondam a cada caminho de URL. As solicitações que correspondem ao caminho da URL nessa configuração são encaminhadas para os destinos de back-end correspondentes usando as configurações de HTTP que correspondem a cada caminho de URL. Além disso, adicione uma configuração HTTP padrão. As solicitações que não correspondem a nenhum caminho de URL nesta regra são encaminhadas para o pool de back-ends padrão usando a configuração HTTP padrão.

## <a name="redirection-setting"></a>Configuração de redirecionamento

Se o redirecionamento estiver configurado para uma regra básica, todas as solicitações no ouvinte associado serão redirecionadas para o destino. Esse é o redirecionamento *global* . Se o redirecionamento estiver configurado para uma regra baseada em caminho, somente as solicitações em uma área específica do site serão redirecionadas. Um exemplo é uma área de carrinho de compras que é denotada por */cart/ \**. Esse é o redirecionamento *baseado em caminho* .

Para obter mais informações sobre redirecionamentos, consulte [visão geral do redirecionamento do gateway de aplicativo](redirect-overview.md).

### <a name="redirection-type"></a>Tipo de redirecionamento

Escolha o tipo de redirecionamento necessário: *permanente (301)*, *temporário (307)*, *encontrado (302)* ou *Consulte outro (303)*.

### <a name="redirection-target"></a>Destino de redirecionamento

Escolha outro ouvinte ou um site externo como o destino de redirecionamento.

#### <a name="listener"></a>Ouvinte

Escolha o ouvinte como o destino de redirecionamento para redirecionar o tráfego de um ouvinte para outro no gateway. Essa configuração é necessária quando você deseja habilitar o redirecionamento de HTTP para HTTPS. Ele redireciona o tráfego do ouvinte de origem que verifica as solicitações HTTP de entrada para o ouvinte de destino que verifica as solicitações HTTPS de entrada. Você também pode optar por incluir a cadeia de caracteres de consulta e o caminho da solicitação original na solicitação que é encaminhada para o destino de redirecionamento.

![Caixa de diálogo componentes do gateway de aplicativo](./media/configuration-overview/configure-redirection.png)

Para obter mais informações sobre o redirecionamento de HTTP para HTTPS, consulte:
- [Redirecionamento de HTTP para HTTPS usando o portal do Azure](redirect-http-to-https-portal.md)
- [Redirecionamento de HTTP para HTTPS usando o PowerShell](redirect-http-to-https-powershell.md)
- [Redirecionamento de HTTP para HTTPS usando o CLI do Azure](redirect-http-to-https-cli.md)

#### <a name="external-site"></a>Site externo

Escolha site externo quando desejar redirecionar o tráfego no ouvinte associado a essa regra a um site externo. Você pode optar por incluir a cadeia de caracteres de consulta da solicitação original na solicitação que é encaminhada para o destino de redirecionamento. Não é possível encaminhar o caminho para o site externo que estava na solicitação original.

Para obter mais informações sobre o redirecionamento, consulte:
- [Redirecionar o tráfego para um site externo usando o PowerShell](redirect-external-site-powershell.md)
- [Redirecionar o tráfego para um site externo usando a CLI](redirect-external-site-cli.md)

## <a name="rewrite-http-headers-and-url"></a>Reescrever cabeçalhos HTTP e URL

Usando regras de reescrita, você pode adicionar, remover ou atualizar os cabeçalhos de solicitação e resposta HTTP (S), bem como o caminho da URL e os parâmetros da cadeia de caracteres de consulta, pois os pacotes de solicitação e resposta são movidos entre o cliente e os pools de back-end por meio do gateway de aplicativo.

Os cabeçalhos e os parâmetros de URL podem ser definidos para valores estáticos ou para outros cabeçalhos e variáveis de servidor. Isso ajuda com casos de uso importantes, como extração de endereços IP do cliente, remoção de informações confidenciais sobre o back-end, adição de mais segurança e assim por diante.
Para obter mais informações, consulte:

 - [Reescrever cabeçalhos HTTP e visão geral da URL](rewrite-http-headers-url.md)
 - [Configurar a regravação do cabeçalho HTTP](rewrite-http-headers-portal.md)
 - [Configurar a regravação de URL](rewrite-url-portal.md)

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre as configurações de HTTP](configuration-http-settings.md)