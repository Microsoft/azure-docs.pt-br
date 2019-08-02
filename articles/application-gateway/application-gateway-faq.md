---
title: Perguntas frequentes sobre o Gateway de aplicativo do Azure
description: Encontre respostas para perguntas frequentes sobre o Gateway de aplicativo do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 6/1/2019
ms.author: victorh
ms.openlocfilehash: 71f1beb68171613fe926ba4d87a13ef58cac1edf
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655268"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Perguntas frequentes sobre o Gateway de aplicativo

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A seguir estão perguntas frequentes sobre o Gateway de aplicativo do Azure.

## <a name="general"></a>Geral

### <a name="what-is-application-gateway"></a>O que é o Gateway de Aplicativo?

O Gateway de aplicativo do Azure fornece um controlador de entrega de aplicativos (ADC) como um serviço. Ele oferece vários recursos de balanceamento de carga de camada 7 para seus aplicativos. Esse serviço é altamente disponível, escalonável e totalmente gerenciado pelo Azure.

### <a name="what-features-does-application-gateway-support"></a>Quais recursos o Gateway de Aplicativo suporta?

O Gateway de aplicativo dá suporte a dimensionamento automático, descarregamento de SSL e SSL de ponta a ponta, firewall do aplicativo web (WAF), afinidade de sessão baseada em cookies, URL roteamento com base no caminho, hospedagem multissite e outros recursos. Para obter uma lista completa dos recursos com suporte, confira [Introdução ao Gateway de Aplicativo](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Como o Gateway de aplicativo e balanceador de carga do Azure diferem?

Gateway de aplicativo é um balanceador de carga de camada 7, que significa que ele funciona apenas com o tráfego da web (HTTP, HTTPS, WebSocket e HTTP/2). Ele dá suporte a recursos como terminação SSL, afinidade de sessão baseada em cookie e round robin para balanceamento de carga de tráfego. A carga do balanceador equilibra a carga do tráfego na camada 4 (TCP ou UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quais protocolos o Gateway de Aplicativo suporta?

O Gateway de Aplicativo fornece suporte HTTP, HTTPS, HTTP/2 e WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Como o Gateway de aplicativo dá suporte a HTTP/2?

Ver [suporte HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Quais recursos têm suporte como parte de um pool de back-end?

Ver [suporte para recursos de back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>Em quais regiões o Gateway de aplicativo está disponível?

O Gateway de Aplicativo está disponível em todas as regiões do Azure global. Ele também está disponível no [do Azure na China 21Vianet](https://www.azure.cn/) e [do Azure governamental](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>É essa implantação dedicada da minha assinatura ou é compartilhado entre os clientes?

O Gateway de Aplicativo é uma implementação dedicada em sua rede virtual.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>O Gateway de aplicativo dá suporte a redirecionamento de HTTP para HTTPS?

Há suporte para redirecionamento. Ver [visão geral de redirecionamento do Gateway de aplicativo](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>Em que ordem os ouvintes são processados?

Consulte a [ordem de processamento de ouvinte](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Onde encontrar o IP do Gateway de aplicativo e o DNS?

Se você estiver usando um endereço IP público como um ponto de extremidade, você encontrará as informações de IP e DNS no recurso de endereço IP público. Ou encontrá-lo no portal, na página de visão geral do gateway de aplicativo. Se você estiver usando endereços IP internos, localize as informações na página de visão geral.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Quais são as configurações de tempo limite Keep-Alive e tempo limite de ociosidade do TCP?

 SKU de Gateway de aplicativo v1, o tempo limite de Keep-Alive é 120 segundos. O tempo limite de Keep Alive para a SKU v2 é 75 segundos. O tempo limite de ociosidade de TCP é um padrão de 4 minutos no front-end IP virtual (VIP) do Gateway de aplicativo.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>O IP ou nome DNS é alterada ao longo do tempo de vida do gateway de aplicativo?

O VIP pode mudar se você interromper e iniciar o gateway de aplicativo. Mas o nome DNS associado com o gateway de aplicativo não muda durante o tempo de vida do gateway. Porque não altera o nome DNS, você deve usar um alias de CNAME e apontá-lo para o endereço DNS do gateway de aplicativo.

### <a name="does-application-gateway-support-static-ip"></a>O Gateway de Aplicativo suporta IP estático?

Sim, o SKU do Gateway de aplicativo v2 dá suporte a endereços IP públicos estáticos. O v1 SKU suporta IPs internos estáticos.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>O Gateway de Aplicativo suporta vários IPs públicos no gateway?

Um gateway de aplicativo dá suporte a apenas um endereço IP público.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Quão grande devo criar minha sub-rede para o Application Gateway?

Ver [considerações de tamanho de sub-rede de Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Posso implantar mais de um recurso de Gateway de Aplicativo em uma única sub-rede?

Sim. Além de várias instâncias de uma determinada implantação de Gateway de aplicativo, você pode provisionar um outro recurso de Gateway de aplicativo exclusivo para uma sub-rede existente que contém um recurso de Gateway de aplicativo diferente.

Uma única sub-rede não oferece suporte a Standard_v2 e o Gateway de aplicativo Standard em conjunto.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>O Gateway de aplicativo dá suporte a cabeçalhos x-forwarded-for?

Sim. Ver [modificações a uma solicitação](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Quanto tempo demora para implantar um gateway de aplicativo? O gateway de aplicativo funcionará enquanto ele está sendo atualizado?

As novas implantações de SKU do Application Gateway v1 podem levar até 20 minutos para provisionar. Alterações no tamanho da instância ou contagem não são interrompidas, e o gateway permanece ativo durante esse tempo.

Implantações que usam o SKU v2 podem levar até 6 minutos para ser provisionado.

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Pode usar o Exchange Server como um back-end com o Gateway de aplicativo?

Nº O Gateway de aplicativo não dá suporte a protocolos de email como SMTP, IMAP e POP3. 

## <a name="performance"></a>Desempenho

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Como o Application Gateway suporta alta disponibilidade e escalabilidade?

O SKU do Gateway de aplicativo v1 dá suporte a cenários de alta disponibilidade quando você implantou duas ou mais instâncias. O Azure distribui essas instâncias entre domínios de falha e atualização para garantir que instâncias não falham todos ao mesmo tempo. O v1 SKU suporta escalabilidade adicionando várias instâncias do mesmo gateway para compartilhar a carga.

A SKU v2 garante automaticamente que novas instâncias sejam distribuídas entre domínios de falha e domínios de atualização. Se você escolher redundância de zona, as instâncias mais recentes também são distribuídas entre zonas de disponibilidade para oferecer resiliência de zona falha.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Como obter um cenário de recuperação de desastres em data centers usando o Gateway de aplicativo?

Use o Gerenciador de tráfego para distribuir o tráfego entre vários gateways de aplicativo em diferentes data centers.

### <a name="does-application-gateway-support-autoscaling"></a>O Gateway de aplicativo dá suporte a dimensionamento automático?

Sim, o SKU Application Gateway v2 suporta escalonamento automático. Para obter mais informações, consulte [dimensionamento automático e com redundância de zona de Gateway de aplicativo](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Não escala manual para cima ou reduzir verticalmente causa tempo de inatividade?

Nº As instâncias são distribuídas entre domínios de atualização e domínios de falha.

### <a name="does-application-gateway-support-connection-draining"></a>O Gateway de Aplicativo suporta a drenagem de conexão?

Sim. Você pode configurar a conexão drenagem para alterar os membros dentro de um pool de back-end sem interrupções. Essa configuração permite que você continue a enviar as conexões existentes para seu destino anterior até que essa conexão é fechada ou expira um tempo limite configurável. Drenagem de Conexão espera apenas conexões em trânsito atuais concluir. O Gateway de aplicativo não está ciente do estado de sessão do aplicativo.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Posso alterar o tamanho da instância de médio para grande sem interrupção?

Sim. O Azure distribui instâncias entre domínios de falha e atualização para garantir que as instâncias não falham todos ao mesmo tempo. O Gateway de Aplicativo dá suporte à escalabilidade adicionando várias instâncias do mesmo gateway para compartilhar a carga.

## <a name="configuration"></a>Configuração

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>O Application Gateway está sempre implantado em uma rede virtual?

Sim. O Gateway de aplicativo é sempre implantado em uma sub-rede de rede virtual. Essa sub-rede pode conter apenas gateways de aplicativo. Para obter mais informações, consulte [requisitos de rede e sub-rede virtual](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>O Gateway de aplicativo pode se comunicar com instâncias fora da sua rede virtual ou fora de sua assinatura?

Desde que tenham conectividade IP, Gateway de aplicativo podem se comunicar com instâncias fora da rede virtual que está. O Gateway de aplicativo também pode se comunicar com instâncias fora da assinatura está em. Se você planeja usar IPs internos como membros do pool de back-end, use [emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md) ou [Gateway de VPN do Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Posso implantar coisa na sub-rede do gateway de aplicativo?

Nº Mas você pode implantar outros gateways de aplicativo na sub-rede.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Há suporte para grupos de segurança de rede na sub-rede do gateway de aplicativo?

Ver [grupos de segurança na sub-rede do Gateway de aplicativo de rede](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>A sub-rede de gateway de aplicativo dá suporte a rotas definidas pelo usuário?

Ver [rotas definidas pelo usuário com suporte na sub-rede do Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quais são os limites no Gateway de Aplicativo? Posso aumentar esses limites?

Ver [limita o Gateway de aplicativo](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Ao mesmo tempo posso usar o Gateway de aplicativo para o tráfego interno e externo?

Sim. O Gateway de aplicativo dá suporte a um endereço IP interno e um IP externo por gateway de aplicativo.

### <a name="does-application-gateway-support-virtual-network-peering"></a>O Gateway de aplicativo dá suporte a emparelhamento de rede virtual?

Sim. Emparelhamento de rede virtual ajuda o tráfego de balanceamento de carga em outras redes virtuais.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Posso falar com servidores locais quando eles estão conectados por túneis de VPN ou ExpressRoute?

Sim, contanto que o tráfego seja permitido.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Um pool de back-end pode servir a vários aplicativos em portas diferentes?

Há suporte para arquitetura de Microsserviço. Para investigação em portas diferentes, você precisará definir várias configurações de HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Investigações personalizadas têm suporte curingas ou regex nos dados de resposta?

Nº 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Como as regras de roteamento são processadas no Gateway de aplicativo?

Ver [ordem das regras de processamento](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Para investigações personalizadas, o que o campo Host significa?

O campo Host Especifica o nome para enviar a investigação para quando você tiver configurado vários sites no Gateway de aplicativo. Caso contrário, use '127.0.0.1'. Esse valor é diferente do nome de host de máquina virtual. Seu formato é \<protocolo\>://\<host\>:\<porta\>\<caminho\>.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Posso permitir acesso do Gateway de aplicativo para apenas alguns endereços IP de origem?

Sim. Ver [restringir o acesso a IPs de origem específico](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Pode usar a mesma porta para ouvintes voltados ao público e privado voltado?

Nº

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Há diretrizes disponíveis para migrar do SKU v1 para o SKU do v2?

Sim. Para obter detalhes, consulte [migrar o Gateway de aplicativo do Azure e o Firewall do aplicativo Web de v1 para v2](migrate-v1-v2.md).


## <a name="configuration---ssl"></a>Configuração - SSL

### <a name="what-certificates-does-application-gateway-support"></a>Gateway de aplicativo oferece suporte a quais certificados?

O Gateway de aplicativo dá suporte a certificados autoassinados, certificados de autoridade (CA), certificados de validação estendida (EV) e certificados curinga.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Quais pacotes de codificações faz o Gateway de aplicativo oferecer suporte?

Gateway de aplicativo suporta os seguintes conjuntos de codificação. 

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

Para obter informações sobre como personalizar as opções de SSL, consulte [versões de política de configurar SSL e conjuntos de codificação no Gateway de aplicativo](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>O Gateway de aplicativo dá suporte à nova criptografia de tráfego para o back-end?

Sim. O Gateway de aplicativo dá suporte ao descarregamento SSL e SSL de ponta a ponta, qual recriptografar o tráfego para o back-end.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Posso configurar a política SSL para versões do protocolo SSL controle?

Sim. Você pode configurar o Gateway de aplicativo para negar TLS1.0, TLS1.1 e TLS1.2. Por padrão, o SSL 2.0 e 3.0 já estão desabilitados e não são configuráveis.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Posso configurar conjuntos de criptografia e ordem de política?

Sim. No Gateway de aplicativo, você pode [configurar conjuntos de codificação](application-gateway-ssl-policy-overview.md). Para definir uma política personalizada, habilite pelo menos um dos seguintes conjuntos de codificação. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

O Gateway de aplicativo usa o SHA256 para gerenciamento de back-end.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Gateway de aplicativo oferece suporte a quantos certificados SSL?

O Gateway de aplicativo dá suporte a até 100 certificados SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Gateway de aplicativo oferece suporte a quantos certificados de autenticação para a nova criptografia de back-end?

O Gateway de aplicativo dá suporte a até 10 certificados de autenticação. O padrão é 5.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>O Gateway de aplicativo integra nativamente com o Azure Key Vault?

Sim, o SKU do Gateway de aplicativo v2 dá suporte ao Cofre de chaves. Para obter mais informações, consulte [terminação SSL com certificados do Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Como fazer para configurar os ouvintes HTTPS para sites. com e .net? 

Para vários baseado em domínio (baseado em host) roteamento, criar ouvintes multissite, configure os ouvintes que usam HTTPS como o protocolo e associar os ouvintes com as regras de roteamento. Para obter mais informações, consulte [hospedando vários sites usando o Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Configuração - firewall do aplicativo web (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>O SKU do WAF oferece todos os recursos disponíveis no SKU Standard?

Sim. WAF oferece suporte a todos os recursos no SKU Standard.

### <a name="which-crs-versions-does-application-gateway-support"></a>Gateway de aplicativo oferece suporte a quais versões do CRS?

O Gateway de Aplicativo dá suporte a CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e a CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Como monitorar o WAF?

Monitorar o WAF por meio do log de diagnóstico. Para obter mais informações, consulte [log de diagnóstico e métricas para o Gateway de aplicativo](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Modo de detecção bloqueia o tráfego?

Nº Modo de detecção apenas registra o tráfego que dispara uma regra WAF.

### <a name="can-i-customize-waf-rules"></a>Como posso personalizar regras WAF?

Sim. Para obter mais informações, consulte [regras e grupos de regras de WAF personalizar](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Quais regras estão atualmente disponíveis para o WAF?

Atualmente, o WAF oferece suporte a CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) e [3.0](application-gateway-crs-rulegroups-rules.md#owasp30). Essas regras fornecem segurança de linha de base contra a maioria das 10 principais vulnerabilidades que identifica Open Web Application Security OWASP (projeto): 

* Proteção contra injeção de SQL
* Proteção de script entre sites
* Proteção contra ataques da web comuns como injeção de comando, as solicitações HTTP indesejadas, divisão de resposta HTTP e ataque de inclusão de arquivo remoto
* Proteção contra violações de protocolo HTTP
* Proteção contra anomalias de protocolo HTTP, como ausência de host de agente do usuário e de cabeçalhos de aceitação
* Prevenção contra bots, rastreadores e scanners
* Detecção de problemas de configuração de aplicativo comuns (ou seja, Apache, IIS e assim por diante)

Para obter mais informações, consulte [vulnerabilidades do OWASP top 10](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>WAF oferece suporte à proteção contra DDoS?

Sim. Você pode habilitar a proteção contra DDoS em uma rede virtual em que o gateway de aplicativo é implantado. Essa configuração garante que o serviço de proteção de DDoS do Azure também protege o application gateway virtual IP (VIP).

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Há diretrizes disponíveis para migrar do SKU v1 para o SKU do v2?

Sim. Para obter detalhes, consulte [migrar o Gateway de aplicativo do Azure e o Firewall do aplicativo Web de v1 para v2](migrate-v1-v2.md).

## <a name="diagnostics-and-logging"></a>Diagnóstico e registro em log

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Quais tipos de logs é fornecida pelo Gateway de aplicativo?

Gateway de aplicativo fornece três logs: 

* **ApplicationGatewayAccessLog**: O log de acesso contém cada solicitação enviada ao front-end do gateway de aplicativo. Os dados incluem IP do chamador, URL solicitada, latência de resposta, o código de retorno e bytes de entrada e saída. O log de acesso é coletado a cada 300 segundos. Ele contém um registro por gateway de aplicativo.
* **ApplicationGatewayPerformanceLog**: O log de desempenho captura informações de desempenho para cada gateway de aplicativo. As informações incluem a taxa de transferência em bytes, total de solicitações atendidas, a contagem de solicitações com falha e a contagem de instâncias de back-end íntegros e não íntegros.
* **ApplicationGatewayFirewallLog**: Para gateways de aplicativo que você configura com o WAF, o log de firewall contém solicitações registradas por meio do modo de detecção ou modo de prevenção.

Para obter mais informações, consulte [back-end de integridade, os logs de diagnóstico e métricas para o Gateway de aplicativo](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Como sei se meus membros do pool de back-end são saudáveis?

Verificar a integridade usando o cmdlet do PowerShell `Get-AzApplicationGatewayBackendHealth` ou no portal. Para obter mais informações, consulte [Diagnósticos do Gateway de Aplicativo](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>O que é a política de retenção dos logs de diagnóstico?

Fluxo de logs de diagnóstico para a conta de armazenamento do cliente. Os clientes podem definir a política de retenção com base em suas preferências. Os logs de diagnóstico também podem ser enviados para um hub de eventos ou logs do Azure Monitor. Para obter mais informações, consulte [Diagnósticos do Gateway de Aplicativo](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Como posso obter os logs de auditoria para o Gateway de aplicativo?

No portal, na folha do menu de um gateway de aplicativo, selecione **Log de atividades** para acessar o log de auditoria. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Configurar alertas com o Gateway de aplicativo?

Sim. No Gateway de aplicativo, os alertas são configurados nas métricas. Para obter mais informações, consulte [métricas do Gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) e [receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Como faço para analisar as estatísticas de tráfego do Application Gateway?

Você pode exibir e analisar os logs de acesso de várias maneiras. Use os logs do Azure Monitor, Excel, Power BI e assim por diante.

Você também pode usar um modelo do Resource Manager que instala e executa a popular [GoAccess](https://goaccess.io/) log analyzer para acessar logs de Gateway de aplicativo. GoAccess fornece valiosas estatísticas de tráfego HTTP como visitantes exclusivos, arquivos solicitados, hosts, sistemas operacionais, navegadores e códigos de status HTTP. Para obter mais informações, no GitHub, consulte o [o arquivo Leiame na pasta de modelo do Resource Manager](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>O que poderia causar a integridade de back-end retornar um status desconhecido?

Normalmente, você verá um status desconhecido, quando o acesso ao back-end está bloqueado por um grupo de segurança de rede (NSG), DNS personalizado ou definido pelo usuário roteamento (UDR) na sub-rede do gateway de aplicativo. Para obter mais informações, consulte [back-end de integridade, o log de diagnóstico e métricas para o Gateway de aplicativo](application-gateway-diagnostics.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Gateway de aplicativo, consulte [o que é o Gateway de aplicativo do Azure?](overview.md).
