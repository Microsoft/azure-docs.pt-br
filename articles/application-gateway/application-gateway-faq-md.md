---
title: Perguntas frequentes sobre o Gateway de Aplicativo Azure
description: Esta página dá respostas às perguntas frequentes sobre o Gateway de Aplicativo Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/26/2020
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: d46c1d8efb5234d47c80ca4256c2f56d56ccf805
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060690"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Perguntas frequentes sobre o Gateway de Aplicativo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Veja a seguir as perguntas comuns sobre o Gateway de Aplicativo Azure.

## <a name="general"></a>Geral

### <a name="what-is-application-gateway"></a>O que é o Gateway de Aplicativo?

O Gateway de Aplicativo Azure fornece o controlador de entrega de aplicativo (ADC) como um serviço. Ele oferece vários recursos de balanceamento de carga de camada 7 a seus aplicativos. Este serviço é altamente disponível e escalonável e totalmente gerenciado pelo Azure.

### <a name="what-features-does-application-gateway-support"></a>Quais recursos o Gateway de Aplicativo suporta?

O Gateway de Aplicativo dá suporte para dimensionamento automático, descarregamento de TSL e TSL de ponta a ponta, firewall do aplicativo Web (WAF), afinidade de sessão baseada em cookies, roteamento baseado em caminho de URL, hospedagem em vários sites e outros. Para obter uma lista completa dos recursos com suporte, confira [Introdução ao Gateway de Aplicativo](./overview.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>O que é a diferença entre o Gateway de Aplicativo e o balanceador de carga do Azure?

O Gateway de Aplicativo é um balanceador de carga de camada 7, o que significa que ele funciona apenas com tráfego da Web (HTTP/HTTPS/WebSocket e HTTP/2). Ele oferece suporte a recursos como terminação TSL, a afinidade de sessão baseada em cookie e rodízio para tráfego de balanceamento de carga. O Load Balancer balanceia o tráfego na camada 4 (TCP ou UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quais protocolos o Gateway de Aplicativo suporta?

O Gateway de Aplicativo fornece suporte HTTP, HTTPS, HTTP/2 e WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Como o Gateway de aplicativo dá suporte a HTTP/2?

Confira o [Suporte do HTTP/2](./configuration-listeners.md#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Quais recursos têm suporte atualmente como parte do pool de back-end?

Consulte [recursos de back-end com suporte](./application-gateway-components.md#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>Em quais regiões o gateway de aplicativo está disponível?

O gateway de aplicativo V1 (Standard e WAF) está disponível em todas as regiões do Azure global. Ele também está disponível no [Azure China 21Vianet](https://www.azure.cn/) e no [Azure Governamental](https://azure.microsoft.com/overview/clouds/government/).

Para disponibilidade do gateway de aplicativo v2 (Standard_v2 e WAF_v2), consulte [regiões com suporte para o gateway de aplicativo v2](./application-gateway-autoscaling-zone-redundant.md#supported-regions)

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Esta é uma implantação dedicada à minha assinatura ou é compartilhada entre os clientes?

O Gateway de Aplicativo é uma implementação dedicada em sua rede virtual.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Como o Gateway de Aplicativo dá suporte ao redirecionamento de HTTP para HTTPS?

Há suporte para redirecionamento. Consulte a [Visão geral do redirecionamento do Gateway de Aplicativo](./redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>Em que ordem os ouvintes são processados?

Consulte a [ordem de processamento de ouvinte](./configuration-listeners.md#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Onde encontro o IP e o DNS do Gateway de Aplicativo?

Se você estiver usando um endereço IP público como um ponto de extremidade, encontrará as informações de IP e DNS no recurso de endereço IP público. Ou encontrá-lo no portal, na página Visão geral do gateway de aplicativo. Se você estiver usando endereços IP internos, localize as informações na página Visão geral.

Para a SKU v2, abra o recurso IP público e selecione **Configuração**. O campo de **Rótulo de nome DNS (opcional)** está disponível para configurar o nome DNS.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Quais são as configurações de tempo limite de Keep-Alive e tempo limite de ociosidade de TCP?

*O tempo limite de Keep-Alive* controla quanto tempo o Gateway de Aplicativo aguardará até que um cliente envie outra solicitação HTTP em uma conexão persistente antes de reusá-lo ou fechá-lo. *O tempo limite de ociosidade de TCP* define quanto tempo uma conexão TCP é mantida aberta no caso de nenhuma atividade. 

*O tempo limite de Keep-Alive* no SKU do Gateway de Aplicativo v1 é de 120 segundos e na SKU V2 é de 75 segundos. O *tempo limite de ociosidade de TCP* é um padrão de 4 minutos no IP virtual de front-end (VIP) do SKU v1 e v2 do Gateway de Aplicativo. Você pode configurar o valor de tempo limite de ociosidade de TCP nos gateways de aplicativo v1 e V2 em qualquer lugar entre 4 minutos e 30 minutos. Para os gateways de aplicativo v1 e v2, você precisará navegar até o IP público do gateway de aplicativo e alterar o tempo limite de ociosidade de TCP na folha "configuração" do IP público no Portal. Você pode definir o valor de tempo limite de ociosidade de TCP do IP público por meio do PowerShell executando os seguintes comandos: 

```azurepowershell-interactive
$publicIP = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyResourceGroup
$publicIP.IdleTimeoutInMinutes = "15"
Set-AzPublicIpAddress -PublicIpAddress $publicIP
```

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>O nome IP ou DNS muda durante a vida útil do Gateway de Aplicativo?

No SKU do Gateway de Aplicativo v1, o VIP pode ser alterado se você parar e iniciar o gateway de aplicativo. Mas o nome DNS associado ao Gateway de Aplicativo não é alterado ao longo do ciclo de vida do gateway. Como o nome DNS não muda, é recomendado usar um alias do CNAME e apontá-lo para o endereço DNS do Gateway de Aplicativo. No SKU do Gateway de Aplicativo v2, você pode definir o endereço IP como estático, portanto, o nome de IP e DNS não será alterado durante o tempo de vida do gateway de aplicativo. 

### <a name="does-application-gateway-support-static-ip"></a>O Gateway de Aplicativo suporta IP estático?

Sim, o SKU do Gateway de Aplicativo v2 permite endereços IP públicos estáticos. O v1 SKU suporta IPs internos estáticos.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>O Gateway de Aplicativo suporta vários IPs públicos no gateway?

Há suporte apenas para um único endereço IP público em um Gateway de Aplicativo.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Quão grande devo criar minha sub-rede para o Application Gateway?

Consulte [Considerações sobre o tamanho da sub-rede do Gateway de Aplicativo](./configuration-infrastructure.md#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Posso implantar mais de um recurso de Gateway de Aplicativo em uma única sub-rede?

Sim. Além de ter várias instâncias de uma determinada implantação do Gateway de Aplicativo, você pode provisionar outro recurso exclusivo do Gateway de Aplicativo para uma sub-rede existente que contém um recurso diferente do Gateway de Aplicativo.

Uma única sub-rede não dá suporte a SKUs de Gateway de Aplicativo v2 e v1.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>O Gateway de Aplicativo v2 permite rotas definidas pelo usuário (UDR)?

Sim, mas apenas cenários específicos. Para obter mais informações, consulte [configuração de infraestrutura do gateway de aplicativo](configuration-infrastructure.md#supported-user-defined-routes).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>O Gateway de aplicativo dá suporte a cabeçalhos x-forwarded-for?

Sim. Consulte [Modificações a uma solicitação](./how-application-gateway-works.md#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Quanto tempo demora para implantar um Gateway de Aplicativo? O meu Gateway de Aplicativo ainda funciona quando está sendo atualizado?

As novas implantações de SKU do Application Gateway v1 podem levar até 20 minutos para provisionar. Alterações de tamanho/contagem de instâncias não são interrompidas, e o gateway permanece ativo durante esse tempo.

A maioria das implantações que usam a SKU v2 levam cerca de 6 minutos para serem provisionadas. No entanto, pode levar mais tempo dependendo do tipo de implantação. Por exemplo, as implantações em vários Zonas de Disponibilidade com muitas instâncias podem levar mais de 6 minutos. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Posso usar o Exchange Server como um back-end com o Gateway de Aplicativo?

Não. O Gateway de Aplicativo não dá suporte a protocolos de email como SMTP, IMAP e POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Há orientações disponíveis para migrar da SKU v1 para a SKU v2?

Sim. Para saber mais detalhes, confira [Migrar o Gateway de Aplicativo e Firewall do aplicativo Web do v1 para v2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>O SKU do Gateway de Aplicativo v1 continuará a ter suporte?

Sim. O SKU do Gateway de Aplicativo v1 continuará a ter suporte. No entanto, é altamente recomendável que você passe para a v2 para aproveitar as atualizações de recursos nesse SKU. Para obter mais informações, confira [Dimensionamento automático e o Gateway de Aplicativo com redundância de zona v2](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>O Gateway de Aplicativo v2 dá suporte a solicitações de proxy com autenticação NTLM?

Não. O gateway de aplicativo v2 não dá suporte a solicitações de proxy com autenticação NTLM.

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>O cookie de afinidade do Gateway de Aplicativo dá suporte ao atributo SameSite?
Sim, o [navegador Chromium](https://www.chromium.org/Home) [atualização de v80](https://chromiumdash.appspot.com/schedule) introduziu uma exigência em cookies HTTP sem que o atributo SameSite seja tratado como SameSite=Lax. Isso significa que o cookie de afinidade do Gateway de Aplicativo não será enviado pelo navegador em um contexto de terceiros. 

Para dar suporte a esse cenário, o Gateway de Aplicativo injeta outro cookie chamado *ApplicationGatewayAffinityCORS* além do cookie *ApplicationGatewayAffinity* existente.  Esses cookies são semelhantes, mas o cookie *ApplicationGatewayAffinityCORS* tem mais dois atributos adicionados a ele: *SameSite=None; Secure*. Esses atributos mantêm afinidades de sessão mesmo para solicitações entre origens. Consulte a [seção da afinidade baseada em cookie](configuration-http-settings.md#cookie-based-affinity) para obter mais informações.

## <a name="performance"></a>Desempenho

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Como o Application Gateway suporta alta disponibilidade e escalabilidade?

O SKU do Gateway de Aplicativo v1 permite cenários de alta disponibilidade quando você tem duas ou mais instâncias implantadas. O Azure distribui essas instâncias entre domínios de atualização e de falha para garantir que todas as instâncias não falham ao mesmo tempo. O v1 SKU suporta escalabilidade adicionando várias instâncias do mesmo gateway para compartilhar a carga.

A SKU v2 garante automaticamente que novas instâncias sejam distribuídas entre domínios de falha e domínios de atualização. Se a redundância de zona for escolhida, as instâncias mais recentes também serão distribuídas entre as zonas de disponibilidade para oferecer resiliência zonal em caso de falha.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Como obter o cenário de recuperação de desastres em data centers com o Gateway de Aplicativo?

Use o Gerenciador de Tráfego para distribuir o tráfego entre vários Gateways de Aplicativo em data centers diferentes.

### <a name="does-application-gateway-support-autoscaling"></a>Como o Gateway de Aplicativo dá suporte a dimensionamento automático?

Sim, o SKU Application Gateway v2 suporta escalonamento automático. Para obter mais informações, confira [Dimensionamento automático e o Gateway de Aplicativo com redundância de zona](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>O dimensionamento manual ou automático aumenta ou reduz verticalmente a causa do tempo de inatividade?

Não. As instâncias são distribuídas entre domínios de atualização e domínios de falha.

### <a name="does-application-gateway-support-connection-draining"></a>O Gateway de Aplicativo suporta a drenagem de conexão?

Sim. Você pode configurar o descarregamento de conexão para alterar os membros dentro de um pool de back-end sem interrupções. Para obter mais informações, confira a [seção drenagem de conexão do Gateway de Aplicativo](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Posso alterar o tamanho da instância de médio para grande sem interrupção?

Sim.

## <a name="configuration"></a>Configuração

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>O Application Gateway está sempre implantado em uma rede virtual?

Sim. O Gateway de Aplicativo é sempre implantado em uma sub-rede de rede virtual. Essa sub-rede só pode conter Gateways de Aplicativos. Para saber mais, confira [exigências de rede virtual e requisitos de sub-rede](./configuration-infrastructure.md#virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>O Gateway de Aplicativo pode se comunicar com instâncias fora de sua rede virtual ou fora de sua assinatura?

O Gateway de Aplicativo pode se comunicar com instâncias fora da rede virtual em que está, desde que exista conectividade de IP. O Gateway de Aplicativo também pode se comunicar com instâncias fora da assinatura em que ele está. Se você planeja usar IPs internos como membros de pool de back-end, será necessário usar o [emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md) ou o [Gateway de VPN da Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Posso implantar coisa na sub-rede do gateway de aplicativo?

Não. Mas você pode implantar outros gateways de aplicativo na sub-rede.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Os grupos de segurança de rede são compatíveis na sub-rede do gateway de aplicativos?

Consulte [Grupos de segurança de rede na sub-rede do Gateway de Aplicativo](./configuration-infrastructure.md#network-security-groups).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>A sub-rede do gateway de aplicativo permite rotas definidas pelo usuário?

Consulte [As rotas definidas pelo usuário são permitidas na sub-rede do Gateway de Aplicativo?](./configuration-infrastructure.md#supported-user-defined-routes).

### <a name="are-service-endpoint-policies-supported-in-the-application-gateway-subnet"></a>As políticas de ponto de extremidade de serviço têm suporte na sub-rede do gateway de aplicativo?

Não. [As políticas de ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoint-policies-overview.md) para contas de armazenamento não têm suporte na sub-rede do gateway de aplicativo e configurá-la bloqueará o tráfego de infraestrutura do Azure.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quais são os limites no Gateway de Aplicativo? Posso aumentar esses limites?

Confira os [Limites do Gateway de Aplicativo](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Posso usar o Gateway de Aplicativo para tráfego externo e interno simultaneamente?

Sim. O Gateway de Aplicativo dá suporte a um endereço IP interno e a um IP externo por Gateway de Aplicativo.

### <a name="does-application-gateway-support-virtual-network-peering"></a>O Gateway de Aplicativo dá suporte ao emparelhamento de rede virtual?

Sim. O emparelhamento de rede virtual ajuda a balancear a carga de tráfego em outras redes virtuais.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Posso falar com servidores locais quando eles estão conectados por túneis de rota expressa ou VPN?

Sim, contanto que o tráfego seja permitido.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Posso ter um pool de back-end que atende muitos aplicativos em portas diferentes?

Há suporte para arquitetura de microsserviço. Você precisaria de várias configurações http definidas para investigação em portas diferentes.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Os testes personalizados permitem curingas/expressões regulares nos dados de resposta?

Não. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Como as regras de roteamento são processadas no Gateway de Aplicativo?

Consulte a [Ordem das regras de processamento](./configuration-request-routing-rules.md#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>O que significa o campo Host para investigações personalizadas?

O campo Host especifica o nome para o qual enviar a investigação quando você configurou vários sites no Gateway de Aplicativo. Caso contrário, use '127.0.0.1'. Este valor é diferente do nome do host de máquina virtual. Seu formato é \<protocol\> :// \<host\> : \<port\> \<path\> .

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Posso permitir o acesso do Gateway de Aplicativo a apenas alguns endereços IP de origem?

Sim. Confira [Restringir o acesso a intervalos de IP específicos](./configuration-infrastructure.md#allow-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>A mesma porta pode ser usada tanto para ouvintes voltados para o público quanto para o privado?

Não.

### <a name="does-application-gateway-support-ipv6"></a>Como o Gateway de Aplicativo dá suporte a IPv6?

O Gateway de Aplicativo v2 não dá suporte a IPv6 no momento. Ele pode operar em uma VNet de pilha dupla usando somente IPv4, mas a sub-rede de gateway deve ser somente IPv4. O Gateway de Aplicativo v1 não dá suporte a pilha dupla VNets. 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Como eu uso o Gateway de Aplicativo V2 com apenas endereço IP de front-end privado?

O Gateway de Aplicativo v2 atualmente não dá suporte apenas ao modo de IP privado. Ele permite as seguintes combinações
* IP privado e IP público
* Somente IP público

Mas se você quiser usar o Gateway de Aplicativo v2 somente com o IP privado, você pode seguir o processo abaixo:
1. Criar um Gateway de Aplicativo com o endereço IP de front-end público e privado
2. Não crie nenhum ouvinte para o endereço IP de front-end público. O gateway de aplicativo não escutará nenhum tráfego no endereço IP público se nenhum ouvinte for criado para ele.
3. Crie e anexe um [Grupo de segurança de rede](../virtual-network/network-security-groups-overview.md) para a sub-rede do Gateway de Aplicativo com a seguinte configuração na ordem de prioridade:
    
    a. Permita o tráfego da Origem como **GatewayManager** marca de serviço e destino como **Qualquer** e porta de Destino como **65200-65535**. Esse intervalo de porta é necessário para a comunicação da infraestrutura do Azure. Essas portas são protegidas (bloqueadas) por autenticação de certificado. Entidades externas, incluindo os administradores de usuário do gateway, não podem iniciar alterações nesses pontos de extremidade sem os certificados apropriados em vigor
    
    b. Permita o tráfego da Origem como marca de serviço e destino **AzureLoadBalancer** e porta de destino  **Qualquer**
    
    c. Permita o tráfego da Origem como marca de serviço e destino **Internet** e porta de destino e portas de destino como **Qualquer**. Dê a essa regra *menos prioridade* nas regras de entrada
    
    d. Mantenha as regras padrão como permitir a entrada de VirtualNetwork para que o acesso no endereço IP privado não seja bloqueado
    
    e. A conectividade de internet de saída não pode ser bloqueada. Caso contrário, você enfrentará problemas de registro em log, métricas etc.

Exemplo de configuração de NSG para acesso somente IP privado: ![Configuração de NSG do Gateway de Aplicativo v2 somente para acesso IP privado](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>Configuração - TLS

### <a name="what-certificates-does-application-gateway-support"></a>Quais certificados o Gateway de Aplicativo permite?

O gateway de aplicativo dá suporte a certificados autoassinados, certificados de AC (autoridade de certificação), certificados de validação estendida (EV), certificados de vários domínios (SAN) e certificados curinga.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Quais conjuntos de codificação o Gateway de Aplicativo permite?

O Gateway de Aplicativo dá suporte aos seguintes conjuntos de codificação. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Para obter mais informações sobre como personalizar opções de TLS, confira [Configurar versões de política SSL e conjuntos de codificação no Gateway de Aplicativo](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>O Gateway de Aplicativo também permite a re-criptografia do tráfego para o back-end?

Sim. Sim, o Gateway de Aplicativo oferece suporte ao descarregamento TSL, TSL de ponta a ponta, que criptografa novamente o tráfego para o back-end.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Posso configurar a política TSL para controlar as versões do Protocolo SSL?

Sim. Você pode configurar o Gateway de Aplicativo para negar TLS1.0, TLS1.1 e TLS1.2. SSL 2.0 e 3.0 já estão desabilitados por padrão e não são configuráveis.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Posso configurar conjuntos de criptografia e ordem de política?

Sim. No Gateway de Aplicativo, você pode [configurar conjuntos de codificação](application-gateway-ssl-policy-overview.md). Ao definir uma política personalizada, pelo menos um dos seguintes pacotes de codificação deve ser habilitado. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

O Gateway de Aplicativo usa SHA256 para gerenciamento de back-end.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Quais certificados TLS/SSL o Gateway de Aplicativo  permite?

O Gateway de Aplicativo permite até 100 certificados TLS/SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Quantos certificados de autenticação para re-criptografia de back-end o Gateway de Aplicativo permite?

O Gateway de Aplicativo permite até 100 certificados TLS/SSL.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>O Gateway de Aplicativo se integra ao Azure Key Vault nativamente?

Sim, o SKU de Gateway de Aplicativo v2 permite Key Vault. Para obter mais informações, confira [Encerramento de TLS com certificados do Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Como configurar ouvintes HTTPS para sites. com e .net? 

Para vários roteamentos baseados em domínio (baseados em host), você pode criar ouvintes de vários sites, configurar ouvintes que usam HTTPS como o protocolo e associar os ouvintes às regras de roteamento. Para obter mais informações, consulte [Hospedagem de vários sites com o Gateway de Aplicativo](./multiple-site-overview.md).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Posso usar caracteres especiais na senha de seu arquivo .pfx?

Não, use apenas caracteres alfanuméricos em sua senha de arquivo. pfx.

### <a name="my-ev-certificate-is-issued-by-digicert-and-my-intermediate-certificate-has-been-revoked-how-do-i-renew-my-certificate-on-application-gateway"></a>Meu certificado EV é emitido por DigiCert e meu certificado intermediário foi revogado. Como fazer renovar meu certificado no gateway de aplicativo?

Os membros do navegador da AC (autoridade de certificação) publicaram recentemente relatórios que detalham vários certificados emitidos por fornecedores de autoridade de certificação que são usados por nossos clientes, pela Microsoft e pela maior comunidade de tecnologia que estavam fora de conformidade com os padrões do setor para autoridades de certificação publicamente confiáveis.Os relatórios sobre o CAs não compatível podem ser encontrados aqui:  

* [Bug 1649951](https://bugzilla.mozilla.org/show_bug.cgi?id=1649951)
* [Bug 1650910](https://bugzilla.mozilla.org/show_bug.cgi?id=1650910)

De acordo com os requisitos de conformidade do setor, os fornecedores de CA começaram a revogar CAs não compatíveis e a emissão de CAs em conformidade, o que exige que os clientes tenham seus certificados reemitidos.A Microsoft está fazendo uma parceria com esses fornecedores para minimizar o impacto potencial nos serviços do Azure, **no entanto, seus certificados emitidos automaticamente ou certificados usados em cenários de BYOC ("Traga seu próprio certificado") ainda estão em risco de serem revogados inesperadamente**.

Para verificar se os certificados utilizados pelo seu aplicativo foram revogados, consulte o [anúncio do DigiCert](https://knowledge.digicert.com/alerts/DigiCert-ICA-Replacement) de referência e o [controlador de revogação de certificado](https://misissued.com/#revoked). Se os certificados tiverem sido revogados ou forem revogados, você precisará solicitar novos certificados do fornecedor da autoridade de certificação utilizado em seus aplicativos. Para evitar que a disponibilidade do aplicativo seja interrompida devido aos certificados serem revogados inesperadamente ou para atualizar um certificado que foi revogado, consulte nossa postagem de atualizações do Azure para obter links de correção de vários serviços do Azure que dão suporte a BYOC: https://azure.microsoft.com/updates/certificateauthorityrevocation/

Para obter informações específicas do gateway de aplicativo, consulte abaixo-

Se você estiver usando um certificado emitido por um dos ICAs revogados, a disponibilidade do aplicativo poderá ser interrompida e, dependendo do aplicativo, você receberá uma variedade de mensagens de erro, incluindo, mas não se limitando a: 

1.  Certificado inválido/certificado revogado
2.  A conexão atingiu o tempo limite
3.  HTTP 502

Para evitar qualquer interrupção em seu aplicativo devido a esse problema, ou para emitir novamente uma AC que foi revogada, você precisa executar as seguintes ações: 

1.  Entre em contato com seu provedor de certificados para emitir novamente seus certificados
2.  Depois de reemitido, atualize seus certificados na Aplicativo Azure gateway/WAF com a cadeia completa [de confiança](/windows/win32/seccrypto/certificate-chains) (folha, intermediária, certificado raiz). Com base em onde você está usando seu certificado, no ouvinte ou nas configurações de HTTP do gateway de aplicativo, siga as etapas abaixo para atualizar os certificados e verifique os links de documentação mencionados para obter mais informações.
3.  Atualize seus servidores de aplicativos de back-end para usar o certificado emitido novamente. Dependendo do servidor de back-end que você está usando, as etapas de atualização do certificado podem variar. Verifique a documentação do seu fornecedor.

Para atualizar o certificado em seu ouvinte:

1.  No [portal do Azure](https://portal.azure.com/), abra o recurso de gateway de aplicativo
2.  Abrir as configurações de ouvinte associadas ao seu certificado
3.  Clique em "renovar ou editar o certificado selecionado"
4.  Carregue seu novo certificado PFX com a senha e clique em salvar
5.  Acesse o site e verifique se o site está funcionando conforme o esperado para obter mais informações, consulte a documentação [aqui](./renew-certificates.md).

Se você estiver fazendo referência a certificados do Azure keyvault no ouvinte do gateway de aplicativo, recomendamos as seguintes etapas para uma alteração rápida –

1.  Na [portal do Azure](https://portal.azure.com/), navegue até as configurações do cofre de chaves do Azure que foram associadas ao gateway de aplicativo
2.  Adicione/importe o certificado reemitido em seu repositório. Consulte a documentação [aqui](../key-vault/certificates/quick-create-portal.md) para obter mais informações sobre como.
3.  Depois que o certificado tiver sido importado, navegue até as configurações do ouvinte do gateway de aplicativo e, em "escolher um certificado de Key Vault", clique no menu suspenso "certificado" e escolha o certificado recentemente adicionado
4.  Clique em salvar para obter mais informações sobre o encerramento de TLS no gateway de aplicativo com certificados Key Vault, consulte a documentação [aqui](./key-vault-certs.md).


Para atualizar o certificado em suas configurações de HTTP:

Se estiver usando a SKU v1 do serviço Application Gateway/WAF, você precisará carregar o novo certificado como seu certificado de autenticação de back-end.
1.  No [portal do Azure](https://portal.azure.com/), abra o recurso de gateway de aplicativo
2.  Abra as configurações de HTTP associadas ao seu certificado
3.  Clique em "Adicionar certificado" e carregue o certificado reemitido e clique em salvar
4.  Você pode remover o certificado antigo mais tarde clicando no botão "..." botão de opções ao lado do certificado antigo e selecione Excluir e clique em salvar.
Para obter mais informações, consulte a documentação [aqui](./end-to-end-ssl-portal.md#add-authenticationtrusted-root-certificates-of-back-end-servers).

Se você estiver usando a SKU v2 do serviço Application Gateway/WAF, não precisará carregar o novo certificado nas configurações de HTTP, já que a SKU v2 usa "certificados raiz confiáveis" e nenhuma ação precisa ser executada aqui.

## <a name="configuration---ingress-controller-for-aks"></a>Configuração - Controlador de Entrada para AKS

### <a name="what-is-an-ingress-controller"></a>O que é um Controlador de Entrada?

Kubernetes permite a criação dos recursos `deployment` e `service` para expor um grupo de pods internamente no cluster. Para expor o mesmo serviço externamente, um recurso [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) é definido, o que fornece balanceamento de carga, término de TLS e hospedagem virtual baseada em nome.
Para atender a esse recurso de `Ingress`, é necessário um Controlador de Entrada que escuta quaisquer alterações em `Ingress` recursos e configura as políticas do balanceador de carga.

O AGIC (controlador de entrada do gateway de aplicativo) permite que [aplicativo Azure gateway](https://azure.microsoft.com/services/application-gateway/) seja usado como a entrada para um [serviço kubernetes do Azure](https://azure.microsoft.com/services/kubernetes-service/) também conhecido como um cluster AKs.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Uma única instância do controlador de entrada pode gerenciar vários Gateways de Aplicativo?

Atualmente, uma instância do Controlador de Entrada só pode ser associada a um gateway de aplicativo.

### <a name="why-is-my-aks-cluster-with-kubenet-not-working-with-agic"></a>Por que meu cluster AKS com kubenet não está funcionando com o AGIC?

O AGIC tenta associar automaticamente o recurso de tabela de rotas à sub-rede do gateway de aplicativo, mas pode falhar ao fazer isso devido à falta de permissões do AGIC. Se AGIC não puder associar a tabela de rotas à sub-rede do gateway de aplicativo, haverá um erro nos logs de AGIC que dizem assim; nesse caso, você terá que associar manualmente a tabela de rotas criada pelo cluster AKS à sub-rede do gateway de aplicativo. Para obter mais informações, consulte [rotas definidas pelo usuário com suporte](configuration-infrastructure.md#supported-user-defined-routes).

### <a name="can-i-connect-my-aks-cluster-and-application-gateway-in-separate-virtual-networks"></a>Posso conectar meu cluster do AKS e o gateway de aplicativo em redes virtuais separadas? 

Sim, desde que as redes virtuais estejam emparelhadas e não tenham espaços de endereço sobrepostos. Se você estiver executando o AKS com kubenet, certifique-se de associar a tabela de rotas gerada pelo AKS à sub-rede do gateway de aplicativo. 

### <a name="what-features-are-not-supported-on-the-agic-add-on"></a>Quais recursos não têm suporte no complemento AGIC? 

Consulte as diferenças entre o AGIC implantado por meio do Helm versus implantado como um complemento do AKS [aqui](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on)

### <a name="when-should-i-use-the-add-on-versus-the-helm-deployment"></a>Quando devo usar o complemento em vez da implantação do Helm? 

Consulte as diferenças entre o AGIC implantado por meio do Helm versus implantado como um complemento do AKS [aqui](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on), especialmente as tabelas que documentam quais cenários são suportados pelo AGIC implantado por meio de Helm em oposição a um complemento AKs. Em geral, a implantação por meio do Helm permitirá que você teste os recursos beta e libere candidatos antes de uma versão oficial. 

### <a name="can-i-control-which-version-of-agic-will-be-deployed-with-the-add-on"></a>Posso controlar qual versão do AGIC será implantada com o complemento?

Não, o complemento AGIC é um serviço gerenciado, o que significa que a Microsoft atualizará automaticamente o complemento para a versão estável mais recente. 

## <a name="diagnostics-and-logging"></a>Diagnóstico e registro em log

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Que tipos de logs o gateway de aplicativo fornece?

O gateway de aplicativo fornece três logs: 

* **ApplicationGatewayAccessLog**: O  log de acesso contém cada solicitação enviada ao front-end do Gateway de Aplicativo. Os dados incluem o IP do chamador, a URL solicitada, latência da resposta, o código de retorno, bytes de entrada e saída. Esse log contém um registro por instância do Gateway de Aplicativo.
* **ApplicationGatewayPerformanceLog**: O log de desempenho captura informações de desempenho para cada gateway de aplicativo. As informações incluem a taxa de transferência em bytes, total de solicitações atendidas, contagem de solicitações com falha e contagem de instâncias de back-end íntegras e não íntegras.
* **ApplicationGatewayFirewallLog**: Para gateways de aplicativo que você configura com o WAF, o log do firewall contém solicitações que são registradas por meio do modo de detecção ou do modo de prevenção.

O log de acesso é coletado a cada 60 segundos. Confira [Integridade do back-end, registro em log de diagnóstico e métricas do Gateway de Aplicativo](application-gateway-diagnostics.md) para saber mais.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Como sei se meus membros do pool de back-end são saudáveis?

Verifique a integridade usando o cmdlet do PowerShell `Get-AzApplicationGatewayBackendHealth` ou o Portal. Para obter mais informações, consulte [Diagnósticos do Gateway de Aplicativo](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>O que é a política de retenção nos logs de diagnóstico?

Logs de diagnóstico se encaminham para a conta de armazenamento do cliente. Os clientes podem definir a política de retenção com base em sua preferência. Os logs de diagnóstico também podem ser enviados para o Hub de Eventos ou os logs do Azure Monitor. Para obter mais informações, consulte [Diagnósticos do Gateway de Aplicativo](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Como posso obter os logs de auditoria para o Gateway de aplicativo?

No portal, clique em **Log de Atividades** na folha do menu de um Gateway de Aplicativo para acessar o log de auditoria. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Configurar alertas com o Gateway de aplicativo?

Sim. No Gateway de aplicativo, os alertas são configurados nas métricas. Para obter mais informações, consulte [métricas do gateway de aplicativo](./application-gateway-metrics.md) e [receber notificações de alerta](../azure-monitor/platform/alerts-overview.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Como faço para analisar as estatísticas de tráfego do Application Gateway?

Você pode exibir e analisar os logs de acesso de várias maneiras. Use Azure Monitor logs, Excel, Power BI e assim por diante.

Usamos um modelo do Resource Manager que instala e executa o popular analisador de logs [GoAccess](https://goaccess.io/) para logs de acesso do Gateway de Aplicativo. O GoAccess fornece valiosas estatísticas de tráfego HTTP, tais como visitantes exclusivos, arquivos solicitados, hosts, sistemas operacionais, navegadores, códigos de status HTTP e muito mais. Para obter mais detalhes, consulte o [arquivo Leiame na pasta de modelo do Resource Manager no GitHub](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>O que poderia fazer com que a integridade de back-end retorne um status desconhecido?

Normalmente, você vê um status desconhecido quando o acesso ao back-end é bloqueado por um NSG (grupo de segurança de rede), DNS personalizado ou UDR (roteamento definido pelo usuário) na sub-rede do gateway de aplicativo. Confira [Integridade do back-end, registro em log de diagnóstico e métricas do Gateway de Aplicativo](application-gateway-diagnostics.md) para saber mais.

### <a name="are-nsg-flow-logs-supported-on-nsgs-associated-to-application-gateway-v2-subnet"></a>Os logs de fluxo NSG são compatíveis com o NSGs associado à sub-rede v2 do gateway de aplicativo?

Devido às limitações da plataforma atual, se você tiver um NSG na sub-rede do gateway de aplicativo v2 (Standard_v2, WAF_v2) e se tiver habilitado logs de fluxo do NSG nele, você verá um comportamento não determinístico e esse cenário não tem suporte no momento.

### <a name="does-application-gateway-store-customer-data"></a>O gateway de aplicativo armazena dados do cliente?

Não, o gateway de aplicativo não armazena dados do cliente.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Gateway de Aplicativo, confira [O que é o Gateway de Aplicativo Azure?](overview.md).
