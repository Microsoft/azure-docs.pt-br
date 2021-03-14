---
title: Porta de frente do Azure-perguntas frequentes
description: Esta página fornece respostas para perguntas frequentes sobre a porta frontal do Azure
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2020
ms.author: duau
ms.openlocfilehash: 6fdcedd19c02251035a838d34ba51e786595134b
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212441"
---
# <a name="frequently-asked-questions-for-azure-front-door"></a>Perguntas frequentes sobre a porta frontal do Azure

Este artigo responde a perguntas comuns sobre recursos e funcionalidades de porta frontal do Azure. Se você não vir a resposta para sua pergunta aqui, poderá entrar em contato conosco pelos seguintes canais (em ordem progressiva):

1. A seção de comentários deste artigo.
2. [UserVoice do Azure de porta frontal](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Suporte da Microsoft:** Para criar uma nova solicitação de suporte, na portal do Azure, na guia **ajuda** , selecione o botão **ajuda + suporte** e, em seguida, selecione **nova solicitação de suporte**.

## <a name="general"></a>Geral

### <a name="what-is-azure-front-door"></a>O que é o Azure Front Door?

A porta frontal do Azure é um Rede de Distribuição de Aplicativos (ADN) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para seus aplicativos. Ele fornece a DSA (Aceleração de Site Dinâmico) junto com o balanceamento de carga global com failover quase em tempo real. É um serviço altamente disponível e escalonável, totalmente gerenciado pelo Azure.

### <a name="what-features-does-azure-front-door-support"></a>Quais recursos o Azure front door dá suporte?

A porta frontal do Azure dá suporte a Dynamic site Acceleration (DSA), descarregamento de TLS/SSL e TLS de ponta a ponta, firewall de aplicativo Web, afinidade de sessão baseada em cookie, roteamento baseado em caminho de URL, certificados gratuitos e gerenciamento de vários domínios, entre outros. Para obter uma lista completa dos recursos com suporte, consulte [visão geral da porta frontal do Azure](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Qual é a diferença entre a porta frontal do Azure e o gateway de Aplicativo Azure?

Embora tanto a porta de frente quanto o gateway de aplicativo sejam balanceadores de carga de camada 7 (HTTP/HTTPS), a principal diferença é que a porta frontal é um serviço global, enquanto o gateway de aplicativo é um serviço regional. Embora a porta frontal possa balancear a carga entre as diferentes unidades de escala/clusters/unidades de carimbo entre regiões, o gateway de aplicativo permite balancear a carga entre suas VMs/contêineres, etc. que está dentro da unidade de escala.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Quando devemos implantar um gateway de aplicativo por trás da porta frontal?

Os principais cenários pelos quais um deve usar o gateway de aplicativo por trás da porta frontal são:

- A porta frontal pode executar o balanceamento de carga baseado em caminho apenas no nível global, mas se um quiser balancear a carga do tráfego ainda mais dentro de sua rede virtual (VNET), eles deverão usar o gateway de aplicativo.
- Como a porta da frente não funciona em nível de VM/contêiner, isso não pode fazer o descarregamento da conexão. No entanto, o gateway de aplicativo permite que você faça o descarregamento de conexão. 
- Com um gateway de aplicativo por trás da porta frontal, é possível obter um descarregamento de 100% TLS/SSL e rotear apenas solicitações HTTP em sua VNET (rede virtual).
- A porta de frente e o gateway de aplicativo dão suporte à afinidade de sessão. Embora a porta frontal possa direcionar o tráfego subsequente de uma sessão de usuário para o mesmo cluster ou back-end em uma determinada região, o gateway de aplicativo pode direcionar relacionar o tráfego para o mesmo servidor dentro do cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Podemos implantar Azure Load Balancer atrás da porta frontal?

A porta frontal do Azure precisa de um VIP público ou um nome DNS publicamente disponível para rotear o tráfego para o. Implantar um Azure Load Balancer atrás da porta frontal é um caso de uso comum.

### <a name="what-protocols-does-azure-front-door-support"></a>Para quais protocolos o Azure front door dá suporte?

A porta frontal do Azure dá suporte a HTTP, HTTPS e HTTP/2.

### <a name="how-does-azure-front-door-support-http2"></a>Como o Azure front door dá suporte a HTTP/2?

O suporte a protocolo HTTP/2 está disponível para clientes que se conectam somente à porta frontal do Azure. A comunicação aos back-ends no pool de back-end é por HTTP/1.1. O suporte a HTTP/2 está habilitado por padrão.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Quais recursos têm suporte atualmente como parte do pool de back-end?

Os pools de back-end podem ser compostos de armazenamento, aplicativo Web, instâncias de kubernetes ou qualquer outro nome de host personalizado que tenha conectividade pública. A porta frontal do Azure requer que os back-ends sejam definidos por meio de um IP público ou um nome de host DNS que possa ser resolvido publicamente. Os membros de pools de back-end podem estar entre zonas, regiões ou até mesmo fora do Azure, desde que tenham conectividade pública.

### <a name="what-regions-is-the-service-available-in"></a>Em quais regiões o serviço está disponível?

A porta frontal do Azure é um serviço global e não está vinculada a nenhuma região específica do Azure. O único local que você precisa especificar ao criar uma porta frontal é o local do grupo de recursos, que está basicamente especificando onde os metadados do grupo de recursos serão armazenados. O próprio recurso de porta frontal é criado como um recurso global e a configuração é implantada globalmente em todos os POPs (ponto de presença). 

### <a name="what-are-the-pop-locations-for-azure-front-door"></a>Quais são os locais POP para a porta frontal do Azure?

A porta frontal do Azure tem a mesma lista de locais POP (ponto de presença) que a CDN do Azure da Microsoft. Para obter a lista completa de nossos POPs, consulte os [locais pop de CDN do Azure da Microsoft](../cdn/cdn-pop-locations.md).

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>O Azure front door é uma implantação dedicada para o meu aplicativo ou é compartilhado entre clientes?

A porta frontal do Azure é um serviço de vários locatários distribuído globalmente. Portanto, a infraestrutura para a porta frontal é compartilhada entre todos os seus clientes. No entanto, ao criar um perfil de porta frontal, você define a configuração específica necessária para seu aplicativo e nenhuma alteração feita na porta de frente afeta outras configurações de porta frontal.

### <a name="is-http-https-redirection-supported"></a>O redirecionamento HTTP-> HTTPS é suportado?

Sim. Na verdade, o Azure front door dá suporte ao host, caminho e redirecionamento de cadeia de caracteres de consulta, bem como parte do redirecionamento de URL. Saiba mais sobre o [redirecionamento de URL](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>Em que ordem as regras de roteamento são processadas?

As rotas para sua porta frontal não são ordenadas e uma rota específica é selecionada com base na melhor correspondência. Saiba mais sobre [como a porta frontal corresponde às solicitações para uma regra de roteamento](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Como fazer bloquear o acesso ao meu back-end para apenas a porta frontal do Azure?

> [!NOTE]
> A nova porta de início de SKU Premium fornece uma maneira mais recomendada de bloquear seu aplicativo por meio do ponto de extremidade privado. [Saiba mais sobre o ponto de extremidade privado](./standard-premium/concept-private-link.md)

Para bloquear seu aplicativo para aceitar o tráfego somente de sua porta de frente específica, você precisará configurar ACLs de IP para o back-end e, em seguida, restringir o tráfego no back-end para o valor específico do cabeçalho ' X-Azure-FDID ' enviado pela porta frontal. Essas etapas são detalhadas como a seguir:

- Configure o IP atuação para seus back-ends para aceitar o tráfego do espaço de endereço IP de back-end da porta do Azure e dos serviços de infraestrutura do Azure apenas. Consulte os detalhes de IP abaixo para atuação seu back-end:
 
    - Consulte a seção *AzureFrontDoor. backend* em [intervalos de IP do Azure e marcas de serviço](https://www.microsoft.com/download/details.aspx?id=56519) para o intervalo de endereços IP de back-end IPv4 da porta frontal ou você também pode usar a marca de serviço *AzureFrontDoor. backend* em seus [grupos de segurança de rede](../virtual-network/network-security-groups-overview.md#security-rules).
    - Serviços de [infraestrutura básica](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) do Azure por meio de endereços IP de host virtualizados: `168.63.129.16` e `169.254.169.254`

    > [!WARNING]
    > O espaço de IP de back-end da porta frontal pode ser alterado mais tarde. no entanto, garantiremos que, antes disso, isso seria integrado aos [intervalos de IP e às marcas de serviço do Azure](https://www.microsoft.com/download/details.aspx?id=56519). Recomendamos que você assine [intervalos de IP do Azure e marcas de serviço](https://www.microsoft.com/download/details.aspx?id=56519) para quaisquer alterações ou atualizações.

- Procure o `Front Door ID` valor na seção visão geral da página do portal de porta frontal. Em seguida, você pode filtrar o cabeçalho de entrada '**X-Azure-FDID**' enviado pela porta frontal ao seu back-end com esse valor para garantir que apenas sua própria instância de porta frontal específica seja permitida (porque os intervalos de IP acima são compartilhados com outras instâncias de porta frontal de outros clientes).

- Aplique a filtragem de regra em seu servidor Web de back-end para restringir o tráfego com base no valor de cabeçalho ' X-Azure-FDID ' resultante. Observe que alguns serviços como Azure App serviço fornecem esse recurso de [filtragem baseado em cabeçalho](../app-service/app-service-ip-restrictions.md#restrict-access-to-a-specific-azure-front-door-instance-preview) sem a necessidade de alterar seu aplicativo ou host.

  Aqui está um exemplo de [Microsoft serviços de informações da Internet (IIS)](https://www.iis.net/):

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```



### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>O IP anycast pode ser alterado durante o tempo de vida da minha porta frontal?

O IP de anycast de front-end para sua porta frontal normalmente não deve ser alterado e pode permanecer estático durante o tempo de vida da porta frontal. No entanto, **não há nenhuma garantia** para o mesmo. Não use nenhuma dependência direta no IP.

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>A Azure front door dá suporte a IPs estáticos ou dedicados?

Não, a porta frontal do Azure atualmente não dá suporte a IPs de anycast de frontend estáticos ou dedicados. 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>A porta frontal do Azure dá suporte a cabeçalhos x-Forwarded-for?

Sim, a porta frontal do Azure dá suporte aos cabeçalhos X-Forwardd-for, X-Forwarded-host e de proto. Para X-Forwarded – para se o cabeçalho já estiver presente, a porta frontal acrescentará o IP de soquete do cliente a ele. Caso contrário, ele adiciona o cabeçalho com o IP de soquete do cliente como o valor. Para o X-Forwarded-host e o protocolo X-Forwarded-proto, o valor é substituído.

Saiba mais sobre os [cabeçalhos HTTP com suporte da porta frontal](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Quanto tempo leva para implantar uma porta frontal do Azure? Minha porta frontal ainda funciona quando está sendo atualizada?

Uma nova criação de porta de front-end ou qualquer atualização de uma porta de frente existente leva cerca de 3 a 5 minutos para a implantação global. Isso significa que, em cerca de 3 a 5 minutos, sua configuração de porta frontal será implantada em todos os nossos POPs globalmente.

Observação-as atualizações personalizadas do certificado TLS/SSL levam cerca de 30 minutos para serem implantadas globalmente.

Todas as atualizações em rotas ou pools de back-end etc. são diretas e causarão zero tempo de inatividade (se a nova configuração estiver correta). As atualizações de certificado também são atômicas e não causarão nenhuma interrupção, a menos que se alterne de ' AFD gerenciado ' para ' usar seu próprio certificado ' ou vice-versa.


## <a name="configuration"></a>Configuração

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>O balanceamento de carga da porta do Azure pode balancear ou rotear o tráfego em uma rede virtual?

A porta frontal do Azure (AFD) requer um IP público ou um nome DNS que possa ser resolvido publicamente para rotear o tráfego. Portanto, a resposta não é um AFD diretamente não pode ser roteada em uma rede virtual, mas usar um gateway de aplicativo ou Azure Load Balancer no entre resolverá esse cenário.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Quais são os vários tempos limite e limites para a porta frontal do Azure?

Saiba mais sobre todos os [tempos limite e limites documentados para a porta frontal do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits).

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Quanto tempo leva para uma regra entrar em vigor depois de ser adicionada ao mecanismo de regras de porta frontal?

A configuração do mecanismo de regras leva cerca de 10 a 15 minutos para concluir uma atualização. Você pode esperar que a regra tenha efeito assim que a atualização for concluída. 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-vice-versa"></a>Posso configurar a CDN do Azure por trás do meu perfil de porta de frente ou vice-versa?

A porta frontal do Azure e a CDN do Azure não podem ser configuradas em conjunto porque ambos os serviços utilizam os mesmos sites do Azure Edge ao responder a solicitações. 

## <a name="performance"></a>Desempenho

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Como o Azure front door dá suporte à alta disponibilidade e escalabilidade?

O Azure front door é uma plataforma multilocatário distribuída globalmente com grandes volumes de capacidade para atender às necessidades de escalabilidade do seu aplicativo. Entregue da borda da rede global da Microsoft, a porta da frente fornece o recurso de balanceamento de carga global que permite que você faça failover de todo o seu aplicativo ou mesmo de microserviços individuais entre regiões ou nuvens diferentes.

## <a name="tls-configuration"></a>Configuração TLS

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Quais versões do TLS têm suporte na porta frontal do Azure?

Todos os perfis de porta frontal criados depois de setembro de 2019 usam TLS 1,2 como o mínimo padrão.

A porta frontal dá suporte às versões de TLS 1,0, 1,1 e 1,2. O TLS 1,3 ainda não tem suporte.

### <a name="what-certificates-are-supported-on-azure-front-door"></a>Quais certificados têm suporte na porta frontal do Azure?

Para habilitar o protocolo HTTPS para fornecer conteúdo com segurança em um domínio personalizado de porta frontal, você pode optar por usar um certificado gerenciado pela porta frontal do Azure ou usar seu próprio certificado.
A opção gerenciada de porta frontal provisiona um certificado TLS/SSL padrão via DigiCert e armazenada no Key Vault da porta frontal. Se você optar por usar seu próprio certificado, poderá integrar um certificado de uma autoridade de certificação com suporte e pode ser um TLS padrão, certificado de validação estendido ou até mesmo um certificado curinga. Não há suporte para certificados autoassinados. Saiba [como habilitar o HTTPS para um domínio personalizado](./front-door-custom-domain-https.md).

### <a name="does-front-door-support-autorotation-of-certificates"></a>A porta frontal dá suporte à rotação autocertificada de certificados?

Para a opção de certificado gerenciado de porta frontal, os certificados são girados de acordo com a porta frontal. Se você estiver usando um certificado gerenciado de porta de front-end e ver que a data de expiração do certificado está abaixo de 60 dias de folga, arquivo um tíquete de suporte.
</br>Para seu próprio certificado TLS/SSL personalizado, a rotação não tem suporte. Semelhante ao modo como ele foi configurado na primeira vez para um determinado domínio personalizado, você precisará apontar a porta da frente para a versão correta do certificado no seu Key Vault e garantir que a entidade de serviço para a porta frontal ainda tenha acesso ao Key Vault. Essa operação de distribuição de certificado atualizada pela porta frontal é atômica e não causa nenhum impacto de produção desde que o nome da entidade ou a SAN para o certificado não seja alterada.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door"></a>Quais são os conjuntos de codificação atuais com suporte na porta frontal do Azure?

Para o TLS 1.2, há suporte para os seguintes conjuntos de codificação: 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

Ao usar domínios personalizados com o TLS 1.0/1.1 habilitado, há suporte para os seguintes conjuntos de codificação:

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
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Posso configurar a política TLS para controlar as versões do protocolo TLS?

Você pode configurar uma versão mínima do TLS na porta frontal do Azure nas configurações de HTTPS do domínio personalizado por meio do portal do Azure ou da [API REST do Azure](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). No momento, você pode escolher entre 1,0 e 1,2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Posso configurar a porta frontal para dar suporte apenas a conjuntos de codificação específicos?

Não, não há suporte para a configuração da porta frontal para conjuntos de codificação específicos. No entanto, você pode obter seu próprio certificado TLS/SSL personalizado de sua autoridade de certificação (digamos, Verisign, Entrust ou DigiCert) e ter conjuntos de codificação específicos marcados no certificado quando você o tiver gerado. 

### <a name="does-front-door-support-ocsp-stapling"></a>A porta frontal dá suporte a grampeamento OCSP?

Sim, a associação OCSP é suportada por padrão pela porta frontal e nenhuma configuração é necessária.

### <a name="does-azure-front-door-also-support-re-encryption-of-traffic-to-the-backend"></a>A porta frontal do Azure também dá suporte à recriptografia de tráfego para o back-end?

Sim, a porta frontal do Azure dá suporte ao descarregamento de TLS/SSL e ao TLS de ponta a ponta, que criptografa novamente o tráfego para o back-end. Na verdade, como as conexões com o back-end ocorrem em seu IP público, é recomendável que você configure sua porta frontal para usar HTTPS como o protocolo de encaminhamento.

### <a name="does-front-door-support-self-signed-certificates-on-the-backend-for-https-connection"></a>A porta frontal dá suporte a certificados autoassinados no back-end para conexão HTTPS?

Não, não há suporte para certificados autoassinados na porta frontal e a restrição se aplica a ambos:

1. **Back-ends**: não é possível usar certificados autoassinados quando você está encaminhando o tráfego como investigações de integridade HTTPS ou HTTPS ou preenchendo o cache de origem para regras de roteamento com Caching habilitado.
2. **Frontend**: você não pode usar certificados autoassinados ao usar seu próprio certificado TLS/SSL personalizado para habilitar o HTTPS em seu domínio personalizado.

### <a name="why-is-https-traffic-to-my-backend-failing"></a>Por que o tráfego HTTPS para meu back-end falha?

Para ter conexões HTTPS bem-sucedidas para o back-end se for para investigações de integridade ou solicitações de encaminhamento, pode haver dois motivos pelos quais o tráfego HTTPS pode falhar:

1. **Incompatibilidade de nome de entidade do certificado**: para conexões HTTPS, a porta frontal espera que seu back-end apresente o certificado de uma AC válida com nome (s) de entidade correspondente ao nome de host de back-end. Por exemplo, se o nome de host de back-end for definido como `myapp-centralus.contosonews.net` e o certificado que seu back-end apresenta durante o handshake de TLS que não tem `myapp-centralus.contosonews.net` nem `*myapp-centralus*.contosonews.net` no nome da entidade, a porta da frente recusará a conexão e resultará em um erro. 
    1. **Solução**: embora não seja recomendado em um ponto de vista de conformidade, você pode solucionar esse erro ao desabilitar a verificação do nome da entidade do certificado para sua porta frontal. Isso está presente em configurações em portal do Azure e em BackendPoolsSettings na API.
2. **Certificado de Hospedagem de back-end de AC inválida**: somente certificados de [CAS válidas](./front-door-troubleshoot-allowed-ca.md) podem ser usados no back-end com a porta frontal. Certificados de CAs internos ou certificados autoassinados não são permitidos.

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Posso usar a autenticação de cliente/mútua com a porta frontal do Azure?

Não. Embora o Azure front door dê suporte a TLS 1,2, que introduziu a autenticação de cliente/mútua no [RFC 5246](https://tools.ietf.org/html/rfc5246), atualmente, a porta frontal do Azure não dá suporte à autenticação de cliente/mútua.

## <a name="diagnostics-and-logging"></a>Diagnóstico e registro em log

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>Quais tipos de métricas e logs estão disponíveis com a porta frontal do Azure?

Para obter informações sobre logs e outros recursos de diagnóstico, consulte [monitoramento de métricas e logs da porta frontal](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>O que é a política de retenção nos logs de diagnóstico?

Os logs de diagnóstico fluem para a conta de armazenamento de clientes, e os clientes podem definir a política de retenção com base em suas preferências. Os logs de diagnóstico também podem ser enviados para o Hub de Eventos ou os logs do Azure Monitor. Para obter mais informações, consulte [Azure front door Diagnostics](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Como fazer obter os logs de auditoria para a porta frontal do Azure?

Os logs de auditoria estão disponíveis para a porta frontal do Azure. No portal, clique em **log de atividades** na folha do menu da sua porta frontal para acessar o log de auditoria. 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Posso definir alertas com a porta frontal do Azure?

Sim, a porta frontal do Azure dá suporte a alertas. Os alertas são configurados nas métricas. 

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar um Front Door](quickstart-create-front-door.md).
- Saiba [como o Front Door funciona](front-door-routing-architecture.md).
