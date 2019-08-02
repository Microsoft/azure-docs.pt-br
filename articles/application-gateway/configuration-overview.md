---
title: Visão geral da configuração de Gateway de aplicativo do Azure
description: Este artigo descreve como configurar os componentes do Gateway de aplicativo do Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/1/2019
ms.author: absha
ms.openlocfilehash: 86b41bb2554dbefeaeb724e746bcaf757463f8ae
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795912"
---
# <a name="application-gateway-configuration-overview"></a>Visão geral da configuração de Gateway de aplicativo

O Gateway de aplicativo do Azure consiste em vários componentes que podem ser configuradas de várias maneiras para cenários diferentes. Este artigo mostra como configurar cada componente.

![Gráfico de fluxo de componentes de Gateway de aplicativo](./media/configuration-overview/configuration-overview1.png)

Esta imagem ilustra um aplicativo que tem três ouvintes. As duas primeiras são ouvintes multissites `http://acme.com/*` e `http://fabrikam.com/*`, respectivamente. Ambos escutam na porta 80. O terceiro é um ouvinte básico que tenha o encerramento de Secure Sockets Layer (SSL) de ponta a ponta.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Rede virtual do Azure e a sub-rede dedicada

Um gateway de aplicativo é uma implementação dedicada em sua rede virtual. Em sua rede virtual, uma sub-rede dedicada é necessária para o gateway de aplicativo. Você pode ter várias instâncias de uma implantação de gateway de aplicativo fornecido em uma sub-rede. Você também pode implantar outros gateways de aplicativo na sub-rede. Mas você não pode implantar qualquer outro recurso na sub-rede do gateway de aplicativo.

> [!NOTE]
> Você não pode misturar Standard_v2 e Gateway de aplicativo do Azure Standard na mesma sub-rede.

#### <a name="size-of-the-subnet"></a>Tamanho da sub-rede

O Gateway de aplicativo consome 1 endereço IP privado por instância, além de outro endereço IP privado, se um IP de front-end privado é configurado.

O Azure também reserva 5 endereços IP em cada sub-rede para uso interno: o primeiro 4 e o último endereços IP. Por exemplo, considere a 15 instâncias de gateway de aplicativo com nenhum endereço IP privado front-end. Você precisa de pelo menos 20 endereços IP para essa sub-rede: 5 para uso interno e 15 para as instâncias de gateway de aplicativo. Portanto, você precisa de um/27 sub-rede tamanho ou maior.

Considere a possibilidade de uma sub-rede que tenha 27 instâncias de gateway de aplicativo e um endereço IP para um IP de front-end privado. Nesse caso, você precisa de 33 endereços IP: 27 para as instâncias de gateway de aplicativo, 1 para o front-end privado e 5 para uso interno. Portanto, você precisa de um /26 sub-rede tamanho ou maior.

É recomendável que você use um tamanho de sub-rede pelo menos/28. Esse tamanho fornece 11 endereços IP utilizáveis. Se sua carga de aplicativo requer mais de 10 endereços IP, considere um/27 ou/26 tamanho da sub-rede.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Grupos de segurança de rede na sub-rede do Gateway de aplicativo

Grupos de segurança de rede (NSGs) têm suporte no Gateway de aplicativo. Mas há várias restrições:

- Você deve incluir exceções para tráfego de entrada em portas 65503-65534 para a SKU do Gateway de aplicativo v1 e portas 65200-65535 para a SKU de v2. Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure. Essas portas são protegidas (bloqueadas) por certificados do Azure. Entidades externas, incluindo os clientes desses gateways, não é possível iniciar as alterações nesses pontos de extremidade sem certificados apropriados em vigor.

- A conectividade de internet de saída não pode ser bloqueada. Regras de saída padrão no NSG permitem que a conectividade com a internet. É recomendável que você:

  - Não remova as regras de saída padrão.
  - Não crie outras regras de saída que negam a conectividade de internet de saída.

- O tráfego dos **AzureLoadBalancer** marca deve ser permitida.

##### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Permitir o acesso do Gateway de aplicativo para alguns IPs de origem

Para este cenário, use os NSGs na sub-rede do Gateway de aplicativo. Coloque as seguintes restrições na sub-rede nesta ordem de prioridade:

1. Permitir o tráfego de entrada de uma fonte de intervalo de IP/IP.
2. Permitir solicitações de entrada de todas as fontes para portas 65503-65534 para [comunicação de integridade do back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure. Essas portas são protegidas (bloqueadas) por certificados do Azure. Sem certificados apropriados em vigor, as entidades externas não podem iniciar alterações nesses pontos de extremidade.
3. Permitir que investiga entrada balanceador de carga do Azure (*AzureLoadBalancer* marca) e o tráfego de rede virtual de entrada (*VirtualNetwork* marca) na [grupo de segurança de rede](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Bloquear todos os outros tráfegos de entrada usando uma regra negar tudo.
5. Permitir tráfego de saída para a internet para todos os destinos.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Rotas definidas pelo usuário com suporte na sub-rede do Gateway de aplicativo

Para o SKU de v1, rotas definidas pelo usuário (UDRs) têm suporte na sub-rede do Gateway de aplicativo, desde que elas não alteram a comunicação de solicitação/resposta de ponta a ponta. Por exemplo, você pode configurar uma UDR na sub-rede do Gateway de aplicativo para apontar para um dispositivo de firewall para inspeção de pacotes. Mas, certifique-se de que o pacote pode atingir seu destino pretendido após a inspeção. Falha ao fazer isso pode resultar na investigação de integridade incorreta ou o comportamento de roteamento de tráfego. Isso inclui aprendidas rotas ou padrão 0.0.0.0/0 que são propagadas pelos gateways VPN ou ExpressRoute do Azure na rede virtual.

Para a SKU do v2, UDRs não têm suporte na sub-rede do Gateway de aplicativo. Para obter mais informações, consulte [SKU do Gateway de aplicativo do Azure v2](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku).

> [!NOTE]
> UDRs não têm suporte para a SKU de v2.  Se você precisar de UDRs, você deve continuar a implantar o SKU de v1.

> [!NOTE]
> Usar UDRs na sub-rede do Gateway de aplicativo faz com que o status de integridade na [modo de exibição de integridade do back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) seja exibido como "Desconhecido". Ele também faz com que a geração de logs do Gateway de aplicativo e métricas de falha. É recomendável que você não usar UDRs na sub-rede do Gateway de aplicativo para que você possa exibir a integridade do back-end, logs e métricas.

## <a name="front-end-ip"></a>IP de front-end

Você pode configurar o gateway de aplicativo para ter um endereço IP público, um endereço IP privado ou ambos. Um IP público é necessário quando você hospede um back-end que os clientes devem acessar a Internet por meio de um para a internet IP virtual (VIP). 

Um IP público não é necessário para um ponto de extremidade interno não exposto à internet. Isso é conhecido como um *balanceador de carga interno* ponto de extremidade (ILB). Um gateway de aplicativo ILB é útil para aplicativos de linha de negócios internos que não são expostos à internet. Também é útil para serviços e camadas em um aplicativo de várias camada em um limite de segurança que não são expostos à internet, mas que exigem o rodízio de carga distribuição, adesão da sessão ou terminação SSL.

Há suporte para apenas 1 endereço IP público ou 1 endereço IP privado. Quando você cria o gateway de aplicativo, você escolher o IP de front-end.

- Para um IP público, você pode criar um novo endereço IP público ou usar um IP público existente no mesmo local que o gateway de aplicativo. Se você criar um novo IP público, o tipo de endereço IP que você seleciona (estático ou dinâmico) não pode ser alterado posteriormente. Para obter mais informações, consulte [estática versus o endereço IP público dinâmico](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Para um endereço IP privado, você pode especificar um endereço IP privado da sub-rede onde o gateway de aplicativo é criado. Se você não for especificado, um endereço IP arbitrário é selecionado automaticamente da sub-rede. Para obter mais informações, consulte [criar um gateway de aplicativo com um balanceador de carga interno](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Um endereço IP de front-end é associado a um *ouvinte*, que verifica as solicitações de entrada no IP front-end.

## <a name="listeners"></a>Ouvintes

Um ouvinte é uma entidade lógica que verifica a conexão para solicitações de entrada usando a porta, protocolo, host e endereço IP. Quando você configura o ouvinte, você deve inserir valores para esses que correspondem aos valores correspondentes na solicitação de entrada no gateway.

Quando você cria um gateway de aplicativo usando o portal do Azure, você também cria um ouvinte padrão ao escolher o protocolo e porta para o ouvinte. Você pode optar por habilitar o suporte de HTTP2 no ouvinte. Depois de criar o gateway de aplicativo, você pode editar as configurações do ouvinte que padrão (*appGatewayHttpListener*/*appGatewayHttpsListener*) ou criar novos ouvintes.

### <a name="listener-type"></a>Tipo de ouvinte

Quando você cria um novo ouvinte, você escolher entre [ *básicas* e *multissite*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Se você estiver hospedando um único site por trás de um gateway de aplicativo, escolha básica. Saiba mais [como criar um gateway de aplicativo com um ouvinte básico](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Se você estiver configurando mais de um aplicativo web ou vários subdomínios do mesmo domínio pai na mesma instância de gateway de aplicativo, escolha o ouvinte multissite. Para um ouvinte multissite, você também deve inserir um nome de host. Isso ocorre porque o Gateway de aplicativo depende de cabeçalhos de host HTTP 1.1 para hospedar mais de um site no mesmo endereço IP público e porta.

#### <a name="order-of-processing-listeners"></a>Ordem de processamento de ouvintes

Para a SKU v1, os ouvintes são processados na ordem em que estão listados. Se um ouvinte básico corresponder a uma solicitação de entrada, o ouvinte processa pela primeira vez que a solicitação. Então, configure os ouvintes multissite antes dos ouvintes básicos para certificar-se de que o tráfego seja roteado para o back-end correto.

Para a SKU do v2, ouvintes multissite são processadas antes ouvintes básicos.

### <a name="front-end-ip"></a>IP de front-end

Escolha o endereço IP de front-end que você planeja associar este ouvinte. O ouvinte escutará solicitações de entrada nesse IP.

### <a name="front-end-port"></a>Porta front-end

Escolha a porta de front-end. Selecione uma porta existente ou crie um novo. Escolha qualquer valor entre o [intervalo de portas permitido](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Você pode usar não apenas portas bem conhecidas, como 80 e 443, mas qualquer porta permitida personalizada que é adequada. Uma porta pode ser usada para ouvintes para o público ou privado voltado para ouvintes.

### <a name="protocol"></a>Protocol

Escolha HTTP ou HTTPS:

- Se você optar por HTTP, o tráfego entre o cliente e o gateway de aplicativo não criptografado.

- Escolha HTTPS se quiser [terminação SSL](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssltls-termination) ou [criptografia de SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/ssl-overview). O tráfego entre o cliente e o gateway de aplicativo é criptografado. E será encerrado a conexão SSL no gateway de aplicativo. Se desejar que a criptografia SSL de ponta a ponta, você deve escolher o HTTPS e configurar o **HTTP de back-end** configuração. Isso garante que o tráfego é criptografado novamente quando são transmitidos entre o gateway de aplicativo e o back-end.

Para configurar a terminação SSL e criptografia de SSL de ponta a ponta, você deve adicionar um certificado para o ouvinte para habilitar o gateway de aplicativo derivar uma chave simétrica. Isso é determinado pela especificação do protocolo SSL. A chave simétrica é usada para criptografar e descriptografar o tráfego que é enviado para o gateway. O certificado de gateway deve estar no formato de troca de informações pessoais (PFX). Esse formato permite exportar a chave privada que o gateway usa para criptografar e descriptografar o tráfego.

#### <a name="supported-certificates"></a>Certificados compatíveis

Ver [certificados com suporte para a terminação SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Suporte a protocolos adicionais

#### <a name="http2-support"></a>Suporte de HTTP2

Suporte de protocolo HTTP/2 está disponível para clientes que se conectam apenas ouvintes de gateway de aplicativo. A comunicação para pools de servidores de back-end é sobre HTTP/1.1. Por padrão, o suporte HTTP/2 está desabilitado. O seguinte trecho de código do PowerShell do Azure mostra como habilitar isso:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Suporte para WebSocket

Suporte ao WebSocket está habilitado por padrão. Não há nenhuma configuração configurável pelo usuário para habilitar ou desabilitá-lo. Você pode usar WebSockets com ouvintes HTTP e HTTPS.

### <a name="custom-error-pages"></a>Páginas de erro personalizadas

Você pode definir o erro personalizado no nível global ou no nível de ouvinte. Mas a criação de páginas de erro personalizadas de nível global do portal do Azure não é suportado atualmente. Você pode configurar uma página de erro personalizada para um erro de firewall de aplicativo 403 web ou uma página de 502 manutenção no nível de ouvinte. Você também deve especificar uma URL de blob publicamente acessível para o código de status de erro especificado. Para obter mais informações, confira [Criar páginas de erro personalizadas do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Códigos de erro do Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Para configurar uma página de erro personalizada global, consulte [configuração do Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>Política SSL

Você pode centralizar o gerenciamento de certificados SSL e reduzir a sobrecarga de um farm de servidores de back-end de criptografia descriptografia. Tratamento SSL centralizado também permite que você especifique uma política SSL central adequada aos seus requisitos de segurança. Você pode escolher *padrão*, *predefinidos*, ou *personalizado* política de SSL.

Configure a política de SSL para o controle versões do protocolo SSL. Você pode configurar um gateway de aplicativo para negar TLS1.0, TLS1.1 e TLS1.2. Por padrão, o SSL 2.0 e 3.0 são desabilitados e não são configuráveis. Para obter mais informações, consulte [visão geral da política de SSL do Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Depois de criar um ouvinte, você pode associá-lo com uma regra de roteamento de solicitação. Essa regra determina como as solicitações recebidas no ouvinte são roteadas para o back-end.

## <a name="request-routing-rules"></a>As regras de roteamento de solicitação

Quando você cria um gateway de aplicativo usando o portal do Azure, você cria uma regra padrão (*rule1*). Essa regra vincula o ouvinte padrão (*appGatewayHttpListener*) com o pool de back-end padrão (*appGatewayBackendPool*) e as configurações de HTTP de back-end padrão ( *appGatewayBackendHttpSettings*). Depois de criar o gateway, você pode editar as configurações da regra padrão ou criar novas regras.

### <a name="rule-type"></a>Tipo de regra

Quando você cria uma regra, você a escolher entre [ *básicas* e *com base em caminho*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Escolha básica, se você quiser encaminhar todas as solicitações no ouvinte associado (por exemplo, *blog<i></i>.contoso.com/\*)* para um único pool de back-end.
- Escolha o caminho com base em se você quiser rotear as solicitações de caminhos de URL específicas para pools de back-end específicos. O padrão de caminho é aplicado somente para o caminho da URL, não para seus parâmetros de consulta.

#### <a name="order-of-processing-rules"></a>Ordem das regras de processamento

Para a SKU v1, correspondência de padrão de solicitações de entrada é processada na ordem em que os caminhos são listados no mapa de caminho de URL da regra com base no caminho. Se uma solicitação corresponde ao padrão em dois ou mais caminhos no mapa de caminho, o caminho listado primeiro é correspondido. E a solicitação é encaminhada para o back-end que está associado a esse caminho.

Para a SKU do v2, uma correspondência exata é uma prioridade maior que a ordem de caminho no mapa de caminho de URL. Se uma solicitação corresponde ao padrão em dois ou mais caminhos, a solicitação é encaminhada para o back-end que está associada com o caminho que corresponda exatamente à solicitação. Se o caminho na solicitação de entrada não corresponder exatamente a qualquer caminho no mapa, correspondência de padrões de solicitação é processada na lista de ordem de mapa do caminho para a regra de caminho.

### <a name="associated-listener"></a>Ouvinte associado

Associar um ouvinte para a regra para que o *regra de roteamento de solicitação* que está associado com o ouvinte é avaliado para determinar o pool de back-end para rotear a solicitação para.

### <a name="associated-back-end-pool"></a>Pool de back-end associado

Associe o pool de back-end que contém os destinos de back-end que atender às solicitações que recebe o ouvinte para a regra.

 - Para uma regra básica, apenas um pool de back-end é permitido. Todas as solicitações no ouvinte associado são encaminhadas para esse pool de back-end.

 - Para uma regra com base em caminho, adicione vários pools de back-end que correspondem a cada caminho de URL. As solicitações que correspondem ao caminho da URL que é inserido são encaminhadas para o pool de back-end correspondente. Além disso, adicione um pool de back-end padrão. Solicitações que não correspondem a qualquer caminho de URL na regra são encaminhadas para esse pool.

### <a name="associated-back-end-http-setting"></a>Configuração de HTTP de back-end associada

Adicione uma configuração de HTTP de back-end para cada regra. Solicitações são roteadas entre o gateway de aplicativo e os destinos de back-end usando o número da porta, protocolo e outras informações que são especificadas nessa configuração.

Para uma regra básica, apenas uma configuração de HTTP de back-end é permitida. Todas as solicitações no ouvinte associado são encaminhadas para os destinos de back-end correspondentes usando essa configuração de HTTP.

Adicione uma configuração de HTTP de back-end para cada regra. Solicitações são roteadas entre o gateway de aplicativo e os destinos de back-end usando o número da porta, protocolo e outras informações que é especificada nessa configuração.

Para uma regra básica, apenas uma configuração de HTTP de back-end é permitida. Todas as solicitações no ouvinte associado são encaminhadas para os destinos de back-end correspondentes usando essa configuração de HTTP.

Para uma regra com base em caminho, adicione várias configurações de HTTP de back-end que correspondem a cada caminho de URL. Solicitações que correspondem ao caminho da URL nesta configuração são encaminhadas para os destinos correspondentes do back-end, usando as configurações de HTTP que correspondem a cada caminho de URL. Além disso, adicione uma configuração de HTTP padrão. As solicitações que não correspondem a qualquer caminho de URL nesta regra são encaminhadas para o pool de back-end padrão usando a configuração de HTTP padrão.

### <a name="redirection-setting"></a>Configuração de redirecionamento

Se o redirecionamento é configurado para uma regra básica, todas as solicitações no ouvinte associado são redirecionadas para o destino. Isso é *global* redirecionamento. Se o redirecionamento estiver configurado para uma regra com base em caminho, somente as solicitações em uma área específica do site são redirecionadas. Um exemplo é uma área de carrinho de compras é denotada por */cart/\** . Isso é *com base em caminho* redirecionamento.

Para obter mais informações sobre como redirecionamentos, consulte [visão geral de redirecionamento do Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="redirection-type"></a>Tipo de redirecionamento

Escolha o tipo de redirecionamento necessário: *Permanent(301)* , *Temporary(307)* , *Found(302)* , ou *consulte other(303)* .

#### <a name="redirection-target"></a>Destino de redirecionamento

Escolha outro ouvinte ou um site externo como o destino de redirecionamento.

##### <a name="listener"></a>Ouvinte

Escolha o ouvinte como o destino de redirecionamento para redirecionar o tráfego de um ouvinte para outro no gateway. Essa configuração é necessária quando você deseja habilitar o redirecionamento de HTTP para HTTPS. Ele redireciona o tráfego do ouvinte de origem que verifica as solicitações HTTP de entrada para o ouvinte de destino que verifica se há solicitações de HTTPS de entrada. Você também pode optar por incluir a cadeia de caracteres de consulta e o caminho da solicitação original na solicitação que é encaminhada para o destino de redirecionamento.

![Caixa de diálogo de componentes de Gateway de aplicativo](./media/configuration-overview/configure-redirection.png)

Para obter mais informações sobre o redirecionamento de HTTP para HTTPS, consulte:
- [Redirecionamento de HTTP para HTTPS, usando o portal do Azure](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [Redirecionamento de HTTP para HTTPS usando o PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [Redirecionamento de HTTP para HTTPS usando a CLI do Azure](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>Site externo

Escolha o site externo quando você deseja redirecionar o tráfego no ouvinte que está associada com esta regra para um site externo. Você pode optar por incluir a cadeia de caracteres de consulta da solicitação original na solicitação que é encaminhada para o destino de redirecionamento. Você não pode encaminhar o caminho para o site externo que estava na solicitação original.

Para obter mais informações sobre o redirecionamento, consulte:
- [Redirecionar o tráfego para um site externo por meio do PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [Redirecionar o tráfego para um site externo usando a CLI](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>Reescreva a configuração do cabeçalho HTTP

Essa configuração adiciona, remove ou cabeçalhos de solicitação e resposta HTTP durante a solicitação de atualizações e pacotes de resposta se mover entre o cliente e os pools de back-end. Você só pode configurar essa funcionalidade por meio do PowerShell. Portal do Azure e o suporte da CLI ainda não estão disponíveis. Para obter mais informações, consulte:

 - [Reescreva a visão geral de cabeçalhos HTTP](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [Configurar a reconfiguração do cabeçalho HTTP](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-the-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>Configurações de HTTP

O gateway de aplicativo roteia o tráfego para os servidores de back-end usando a configuração que você especificar aqui. Depois de criar uma configuração de HTTP, você deve associá-lo com uma ou mais regras de roteamento de solicitação.

### <a name="cookie-based-affinity"></a>Afinidade baseada em cookie

Esse recurso é útil quando você deseja manter uma sessão de usuário no mesmo servidor. Cookies gerenciados de gateway permitem que o application gateway direto tráfego subsequente de uma sessão de usuário para o mesmo servidor para processamento. Isso é importante quando o estado de sessão é salvo localmente no servidor para uma sessão de usuário. Se o aplicativo não pode lidar com a afinidade de baseada em cookies, você não poderá usar esse recurso. Para usá-lo, certifique-se de que os clientes oferecem suporte a cookies.

### <a name="connection-draining"></a>Descarregamento de conexão

Drenagem de Conexão ajuda normalmente remover membros do pool de back-end durante as atualizações de serviço planejada. Você pode aplicar essa configuração para todos os membros de um pool de back-end durante a criação da regra. Isso garante que todas as instâncias de cancelar o registro de um pool de back-end não recebem novas solicitações. Enquanto isso, as solicitações existentes podem ser concluídas dentro de um limite de tempo configurado. Drenagem de Conexão se aplica a instâncias de back-end que são explicitamente removidas do pool de back-end por uma chamada à API. Ele também se aplica a instâncias de back-end que são relatadas como *Íntegro* pela integridade investigações.

### <a name="protocol"></a>Protocol

O Gateway de aplicativo dá suporte a HTTP e HTTPS para rotear solicitações para servidores de back-end. Se você escolher HTTP, o tráfego para os servidores de back-end será criptografado. Se a comunicação não criptografada não for aceitável, escolha HTTPS.

Essa configuração é combinado com HTTPS no oferece suporte ao ouvinte [SSL de ponta a ponta](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Isso permite transmitir com segurança dados confidenciais criptografados para o back-end. Cada servidor de back-end no pool de back-end com SSL de ponta a ponta habilitado deve ser configurado com um certificado para permitir a comunicação segura.

### <a name="port"></a>Porta

Essa configuração especifica a porta em que os servidores de back-end escutam tráfego do gateway de aplicativo. Você pode configurar as portas que variam de 1 a 65535.

### <a name="request-timeout"></a>Tempo limite da solicitação

Essa configuração é o número de segundos que o gateway de aplicativo espera receber uma resposta do pool de back-end antes de retornar uma mensagem de erro "atingiu o tempo limite de conexão".

### <a name="override-back-end-path"></a>Substituir o caminho de back-end

Essa configuração permite configurar um caminho de encaminhamento personalizado opcional a ser usado quando a solicitação é encaminhada para o back-end. Qualquer parte do caminho de entrada que corresponde ao caminho personalizado na **substituir o caminho de back-end** campo é copiado para o caminho encaminhado. A tabela a seguir mostra como esse recurso funciona:

- Quando a configuração de HTTP é anexada a uma regra básica de roteamento de solicitação:

  | Solicitação original  | Substituir o caminho de back-end | Solicitação encaminhada ao back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override/            | / Substituir/home /              |
  | / home/secondhome / | /override/            | substituição/home/secondhome /   |

- Quando a configuração de HTTP é anexada a uma regra de roteamento de solicitação com base no caminho:

  | Solicitação original           | Regra de caminho       | Substituir o caminho de back-end | Solicitação encaminhada ao back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | / Substituir/home /              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | substituição/home/secondhome /   |
  | /Home/                     | /pathrule*      | /override/            | / Substituir/home /              |
  | / home/secondhome /          | /pathrule*      | /override/            | substituição/home/secondhome /   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | / Substituir/secondhome /        |
  | /pathrule/                 | /pathrule/      | /override/            | /override/                   |

### <a name="use-for-app-service"></a>Uso do serviço de aplicativo

Esse é um atalho de interface do usuário que seleciona as duas configurações necessárias para o back-end do serviço de aplicativo do Azure. Ele permite **Escolher nome de host do endereço de back-end**, e cria uma nova investigação personalizada. (Para obter mais informações, consulte o [nome do host de escolha de endereços de back-end](#pick) definindo seção deste artigo.) Uma nova investigação é criada, e o cabeçalho de investigação é obtido do endereço de back-end do membro.

### <a name="use-custom-probe"></a>Usar investigação personalizada

Essa configuração associa um [investigação personalizada](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) com uma configuração de HTTP. Você pode associar apenas uma investigação personalizada uma configuração de HTTP. Se você não associar explicitamente uma investigação personalizada, o [investigação padrão](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) é usado para monitorar a integridade do back-end. É recomendável que você crie uma investigação personalizada para um controle maior sobre o monitoramento de integridade do seu back-ends.

> [!NOTE]
> A investigação personalizada não monitora a integridade do pool de back-end, a menos que a configuração de HTTP correspondente é explicitamente associada um ouvinte.

### <a id="pick"/></a>Escolher nome de host do endereço de back-end

Esse recurso define dinamicamente os *host* cabeçalho na solicitação para o nome do host do pool de back-end. Ele usa um endereço IP ou FQDN.

Esse recurso ajuda quando o nome de domínio do back-end for diferente do nome DNS do gateway de aplicativo e o back-end se baseia em um cabeçalho de host específico ou a extensão de indicação de nome de servidor (SNI) para resolver para o ponto de extremidade correto.

Um exemplo de caso é multilocatário serviços como o back-end. Um serviço de aplicativo é um serviço multilocatário que usa um espaço compartilhado com um único endereço IP. Portanto, um serviço de aplicativo só pode ser acessado por meio de nomes de host configurados nas configurações de domínio personalizado.

Por padrão, é o nome de domínio personalizado *example.azurewebsites.<i> </i>net*. Para acessar o serviço de aplicativo usando um gateway de aplicativo por meio de um nome de host que não são explicitamente está registrado no serviço de aplicativo ou FQDN do gateway de aplicativo, você deve substituir o nome do host na solicitação original para o nome de host do serviço de aplicativo. Para fazer isso, ative a **Escolher nome de host do endereço de back-end** configuração.

Para um domínio personalizado cujo nome DNS personalizado existente é mapeado para o serviço de aplicativo, você não precisa habilitar essa configuração.

> [!NOTE]
> Essa configuração não é necessária para o ambiente do serviço de aplicativo para o PowerApps, que é uma implementação dedicada.

### <a name="host-name-override"></a>Substituição do nome de host

Essa capacidade substitui o *host* cabeçalho na solicitação de entrada no gateway de aplicativo com o nome de host que você especificar.

Por exemplo, se *www.contoso<i></i>.com* é especificado no **nome do Host** definindo a solicitação original *https:/<i></i>/appgw.eastus.cloudapp.net/path1* é alterada para *https:/<i></i>/www.contoso.com/path1* quando a solicitação é encaminhada para o servidor de back-end.

## <a name="back-end-pool"></a>Pool de back-end

Você pode apontar um pool de back-end para quatro tipos de membros de back-end: um serviço de aplicativo, um conjunto de dimensionamento de máquina virtual, um endereço IP/FQDN ou uma máquina virtual específica. Cada pool de back-end pode apontar para vários membros do mesmo tipo. Apontando para os membros de tipos diferentes no mesmo pool de back-end não é suportado.

Depois de criar um pool de back-end, você deve associá-lo com uma ou mais regras de roteamento de solicitação. Você também deve configurar investigações de integridade para cada pool de back-end no gateway de aplicativo. Quando uma condição de regra de roteamento de solicitação for atendida, o gateway de aplicativo encaminha o tráfego para os servidores íntegros (conforme determinado por investigações de integridade) no pool de back-end correspondente.

## <a name="health-probes"></a>Investigações de integridade

Um gateway de aplicativo monitora a integridade de todos os recursos no seu back-end por padrão. Mas é altamente recomendável que você crie uma investigação personalizada para cada configuração de HTTP de back-end obter maior controle sobre o monitoramento de integridade. Para saber como configurar uma investigação personalizada, consulte [configurações de investigação de integridade personalizadas](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]
> Depois de criar uma investigação de integridade personalizadas, você precisa associá-la a uma configuração de HTTP de back-end. Uma investigação personalizada não monitora a integridade do pool de back-end, a menos que a configuração de HTTP correspondente é explicitamente associada um ouvinte.

## <a name="next-steps"></a>Próximas etapas

Agora que você sabe sobre os componentes do Gateway de aplicativo, você pode:

- [Criar um gateway de aplicativo no portal do Azure](quick-create-portal.md)
- [Criar um gateway de aplicativo usando o PowerShell](quick-create-powershell.md)
- [Criar um gateway de aplicativo usando a CLI do Azure](quick-create-cli.md)
