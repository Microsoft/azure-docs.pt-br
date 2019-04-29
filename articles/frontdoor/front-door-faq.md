---
title: Serviço de porta da frente do Azure - perguntas frequentes para a porta da frente | Microsoft Docs
description: Esta página fornece respostas para perguntas frequentes sobre o serviço de porta da frente do Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: 256435dfd016ebbd86dbbe49f4abbb346fb1cd19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736659"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Perguntas frequentes sobre o serviço de porta da frente do Azure

Este artigo responde a perguntas comuns sobre o serviço do Azure da frente recursos e funcionalidades. Se você não vir a resposta para sua pergunta aqui, poderá entrar em contato conosco pelos seguintes canais (em ordem progressiva):

1. A seção de comentários deste artigo.
2. [UserVoice do serviço do Azure da frente](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Suporte da Microsoft:** Para criar uma nova solicitação de suporte, no Portal do Azure, na guia **Ajuda**, selecione o botão **Ajuda + suporte** e, em seguida, selecione **Nova solicitação de suporte**.

## <a name="general"></a>Geral

### <a name="what-is-azure-front-door-service"></a>O que é o Azure Front Door Service?

Serviço de porta da frente do Azure é uma rede de entrega de aplicativo (ADN) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para seus aplicativos. Ele fornece a aceleração de site dinâmico (DSA) juntamente com balanceamento de carga global com o próximo failover em tempo real. É um serviço altamente disponível e escalonável, que é totalmente gerenciado pelo Azure.

### <a name="what-features-does-azure-front-door-service-support"></a>O serviço de porta da frente do Azure dá suporte a quais recursos?

Serviço de porta da frente do Azure dá suporte a aceleração de site dinâmico (DSA), descarregamento de SSL e SSL de ponta a ponta, Firewall do aplicativo Web, afinidade de sessão baseada em cookies, roteamento baseado em caminho de url, livres certificados e gerenciamento de vários domínios e outras pessoas. Para obter uma lista completa dos recursos com suporte, consulte [visão geral do serviço de porta da frente do Azure](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>O que é a diferença entre o serviço de porta da frente do Azure e o Gateway de aplicativo do Azure?

Embora a porta da frente e Gateway de aplicativo são de camada 7 balanceadores de carga (HTTP/HTTPS), a principal diferença é que a porta da frente é um serviço global, enquanto o Gateway de aplicativo é um serviço regional. Enquanto a porta da frente pode balancear a carga entre suas unidades de clusters/unidades/carimbo de escala diferente entre regiões, o Gateway de aplicativo permite que você balancear a carga entre seu etc. de VMs/contêineres que está dentro da unidade de escala.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Quando estamos deve implantar um Gateway de aplicativo por trás da frente?

Os cenários principais por que o Gateway de aplicativo por trás da frente devem ser usados são:

- Porta da frente pode executar somente no nível global, mas se você quiser balancear carga de tráfego ainda mais em sua rede virtual (VNET) e em seguida, eles devem usar o Gateway de aplicativo de balanceamento de carga com base no caminho.
- Uma vez que a porta de entrada não funcionar em um nível VM/contêiner, portanto, ele não pode fazer drenagem de Conexão. No entanto, o Gateway de aplicativo permite que você faça a drenagem de Conexão. 
- Com um Gateway de aplicativo por trás do AFD, um pode atingir 100% de descarregamento de SSL e rotear solicitações HTTP apenas dentro de sua rede virtual (VNET).
- Porta de entrada e o Gateway de aplicativo dar suporte à afinidade de sessão. Enquanto a porta da frente pode direcionar o tráfego subsequente de uma sessão de usuário no mesmo cluster ou back-end em uma determinada região, o Gateway de aplicativo direct podem relacionar o tráfego para o mesmo servidor dentro do cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Podemos implantar o balanceador de carga do Azure por trás da frente?

Serviço de porta da frente do Azure precisa de um VIP público ou um nome DNS disponível publicamente para rotear o tráfego para. Implantar um balanceador de carga do Azure por trás da frente é um comum de caso de uso.

### <a name="what-protocols-does-azure-front-door-service-support"></a>O serviço de porta da frente do Azure dá suporte a quais protocolos?

Serviço de porta da frente do Azure dá suporte a HTTP, HTTPS e HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>Como o serviço de porta da frente do Azure dá suporte a HTTP/2?

Suporte de protocolo HTTP/2 está disponível para clientes que se conectam ao serviço de porta da frente do Azure apenas. A comunicação com o back-ends no pool de back-end é sobre HTTP/1.1. Suporte a HTTP/2 é habilitado por padrão.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Quais recursos têm suporte atualmente como parte do pool de back-end?

Pools de back-end podem ser compostos de armazenamento, aplicativo Web, instâncias de Kubernetes ou qualquer outro nome de host personalizado que tenha conectividade pública. Serviço de porta da frente do Azure requer que o back-ends são definidos por meio de um IP público ou um nome de host DNS resolvível publicamente. Membros dos pools de back-end podem ser entre zonas, regiões, ou até mesmo fora do Azure, desde que eles tenham conectividade pública.

### <a name="what-regions-is-the-service-available-in"></a>Em quais regiões o serviço está disponível?

O serviço do Azure da frente é um serviço global e não está vinculado a qualquer região do Azure específico. O único local em que você precisa especificar durante a criação de uma porta da frente é o local do grupo de recursos, que basicamente é Especifica onde os metadados para o grupo de recursos serão armazenados. Recurso de porta da frente em si é criado como um recurso global e a configuração é implantada globalmente para todos os POPs (ponto de presença). 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Quais são os locais POP da frente do serviço do Azure?

Serviço de porta da frente do Azure tem a mesma lista de locais POP (ponto de presença) como a CDN do Azure da Microsoft. Para obter uma lista de nosso POPs, consulte gentilmente [locais POP de CDN do Azure da Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>É o serviço do Azure da frente uma implantação dedicada para o meu aplicativo ou é compartilhado entre os clientes?

Serviço de porta da frente do Azure é um serviço multilocatário distribuído globalmente. Portanto, a infraestrutura para a porta da frente é compartilhada entre todos os seus clientes. No entanto, com a criação de uma porta da frente, você define a configuração específica necessária para seu aplicativo e 

### <a name="is-http-https-redirection-supported"></a>O redirecionamento HTTP-> HTTPS é suportado?

Porta da frente atualmente não dá suporte a redirecionamento de URL.

### <a name="in-what-order-are-routing-rules-processed"></a>Em que ordem as regras de roteamento são processadas?

As rotas para a porta de entrada não são ordenadas e uma rota específica é selecionada com base na melhor correspondência. Saiba mais sobre [como porta de entrada corresponde a solicitação para uma regra de roteamento](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-service"></a>Como bloquear o acesso ao meu back-end apenas serviço de porta da frente do Azure?

Você pode configurar IP listagem na ACL para seu back-ends para aceitar o tráfego do serviço de porta da frente do Azure. Você pode restringir seu aplicativo para aceitar conexões de entrada apenas do espaço IP de back-end do serviço de porta da frente do Azure. Estamos trabalhando para integrar [intervalos de IP do Azure e marcas de serviço](https://www.microsoft.com/download/details.aspx?id=56519) mas, por agora, você pode consultar os intervalos de IP, conforme mostrado a seguir:
 
- **IPv4** - `147.243.0.0/16`
- **IPv6** - `2a01:111:2050::/44`

> [!WARNING]
> Nosso espaço IP de back-end pode ser alteradas posteriormente, no entanto, podemos irá assegurar que antes que isso ocorra, o que seria integramos com [intervalos de IP do Azure e marcas de serviço](https://www.microsoft.com/download/details.aspx?id=56519). É recomendável que você tenha uma assinatura [intervalos de IP do Azure e marcas de serviço](https://www.microsoft.com/download/details.aspx?id=56519) para quaisquer alterações ou atualizações. 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>O IP anycast pode mudar ao longo do tempo de vida de minha frente?

O IP anycast de front-end para a porta da frente normalmente não deve ser alterada e pode permanecer estático durante a vida útil da frente. No entanto, há **nenhuma garantia** para o mesmo. Gentilmente não têm dependências diretas no IP.  

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>O serviço de porta da frente do Azure dá suporte a IPs estáticos ou dedicado?

Não, o serviço de porta da frente do Azure atualmente não dá suporte front-end dedicado ou estático anycast IPs. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>O serviço de porta da frente do Azure dá suporte a cabeçalhos x-forwarded-for?

Sim, o serviço de porta da frente do Azure aceita os cabeçalhos X-Forwarded-For, X-Forwarded-Host e X-Forwarded-Proto. O cabeçalho X-Forwarded-For, se já estava presente e em seguida, porta da frente acrescenta o IP de soquete de cliente a ele. Caso contrário, ele adiciona o cabeçalho com o IP de soquete de cliente como o valor. Para obter X-Forwarded-Proto e X-Forwarded-Host, o valor é substituído.

Saiba mais sobre o [cabeçalhos HTTP com suporte de porta da frente](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Quanto tempo demora para implantar um serviço de porta da frente do Azure? Minha frente ainda funciona quando está sendo atualizado?

Todas as atualizações para uma porta da frente existente ou uma nova criação de porta da frente leva cerca de 3 a 5 minutos para implantação global. Isso significa que em cerca de 3 a 5 minutos, sua configuração de porta da frente será implantada em todos os nossos POPs globalmente.

Observação - personalizado atualizações de certificado SSL levar cerca de 30 minutos para ser implantado globalmente.

## <a name="configuration"></a>Configuração

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Balanceamento de carga do Azure da frente ou rota pode tráfego em uma rede virtual?

Porta da frente do Azure (AFD) requer um IP público ou nome DNS resolvível publicamente para rotear o tráfego. Portanto, a resposta é não AFD não pode rotear diretamente em uma rede virtual, mas usar um balanceador de carga do Azure ou o Gateway de aplicativo entre resolverá esse cenário.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Quais são os diversos tempos limite e limites de porta da frente do serviço do Azure?

Saiba mais sobre todos os o documentadas [tempos limite e limites de serviço do Azure da frente](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Desempenho

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Como o serviço de porta da frente do Azure dá suporte de alta disponibilidade e escalabilidade?

Serviço de porta da frente do Azure é uma plataforma multilocatário distribuída globalmente com grandes volumes de capacidade para atender às necessidades de escalabilidade do seu aplicativo. Entregue da borda da rede global da Microsoft, a porta da frente fornece recurso global de balanceamento de carga que permite que você fazer failover de seu aplicativo inteiro ou microsserviços individuais até mesmo em regiões ou em nuvens diferentes.

## <a name="ssl-configuration"></a>Configuração de SSL

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Quais versões do TLS são compatíveis com o serviço de porta da frente do Azure?

Porta da frente dá suporte a TLS versões 1.0, 1.1 e 1.2. Ainda não há suporte para TLS 1.3.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Quais certificados têm suporte no serviço de porta da frente do Azure?

Para habilitar o protocolo HTTPS para o fornecimento seguro de conteúdo em um domínio personalizado da porta da frente, você pode optar por usar um certificado que é gerenciado pelo serviço de porta da frente do Azure ou usar seu próprio certificado.
A porta de entrada gerenciado provisões de opção um certificado SSL padrão por meio do Digicert e armazenados na frente Key Vault da porta do. Se você optar por usar seu próprio certificado e, em seguida, você pode integrar um certificado de uma autoridade de certificação com suporte e pode ser um SSL padrão, o certificado de validação estendida ou até mesmo um certificado curinga. Os certificados autoassinados não são suportados. Saiba mais [como habilitar HTTPS para um domínio personalizado](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Quais são os conjuntos de criptografia atuais com suporte pelo serviço de porta da frente do Azure?

Estes são os conjuntos de criptografia atuais com suporte pelo serviço de porta da frente do Azure:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>O serviço de porta da frente do Azure também dá suporte nova criptografia de tráfego no back-end?

Sim, o serviço de porta da frente do Azure dá suporte a descarregamento SSL e SSL de ponta a ponta, que criptografa novamente o tráfego para o back-end. Na verdade, uma vez que as conexões com o back-end é feita via é IP público, é recomendável que você configure a porta da frente para usar HTTPS como o protocolo de encaminhamento.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Posso configurar a política SSL para controlar as versões do Protocolo SSL?

Não, atualmente não dá suporte para negar a versões específicas do TLS a porta da frente nem pode definir as versões mínimas do TLS. 

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Posso configurar a porta da frente para só oferecem suporte a conjuntos de codificação específico?

Não, não há suporte para configuração de porta da frente para conjuntos de codificação específico. 

## <a name="diagnostics-and-logging"></a>Diagnóstico e registro em log

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Quais tipos de logs e métricas estão disponíveis com o serviço de porta da frente do Azure?

Para obter informações sobre logs e outros recursos de diagnóstico, consulte [métricas e logs de monitoramento para frente](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>O que é a política de retenção nos logs de diagnóstico?

Os logs de diagnóstico fluem para a conta de armazenamento de clientes, e os clientes podem definir a política de retenção com base em suas preferências. Os logs de diagnóstico também podem ser enviados para o Hub de Eventos ou os logs do Azure Monitor. Para obter mais informações, consulte [diagnóstico do serviço do Azure da frente](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Como posso obter os logs de auditoria da frente do serviço do Azure?

Os logs de auditoria estão disponíveis para o serviço de porta da frente do Azure. No portal, clique em **Log de atividades** na folha do menu de porta para acessar o log de auditoria. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Posso configurar alertas com o serviço de porta da frente do Azure?

Sim, o serviço de porta da frente do Azure oferece suporte a alertas. Os alertas são configurados nas métricas. 

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).