---
title: Segurança
description: Saiba como o Serviço de Aplicativo ajuda a proteger o aplicativo e como é possível bloquear ainda mais o aplicativo contra ameaças.
keywords: serviço de aplicativo do azure, aplicativo Web, aplicativo móvel, aplicativo de api, aplicativo de funções, segurança, seguro, protegido, conformidade, compatível, certificado, certificados, https, ftps, tls, confiar, criptografia, criptografar, criptografado, restrição de ip, autenticação, autorização, authn, autho, msi, identidade de serviço gerenciada, identidade gerenciada, segredos, segredo, aplicação de patch, patch, patches, versão, isolamento, isolamento de rede, ddos, mitm
ms.topic: article
ms.date: 08/24/2018
ms.custom: seodec18
ms.openlocfilehash: 61bffcfa8b98ed666e450c344023258b752e4880
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98736098"
---
# <a name="security-in-azure-app-service"></a>Segurança no Serviço de Aplicativo do Azure

Este artigo mostra como o [Serviço de Aplicativo do Azure](overview.md) ajuda a proteger o aplicativo Web, back-end de aplicativo móvel, aplicativo de API e [aplicativo de funções](../azure-functions/index.yml). Ele também mostra como é possível proteger ainda mais o aplicativo com os recursos internos do Serviço de Aplicativo.

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

As seções a seguir mostram como proteger ainda mais o aplicativo do Serviço de Aplicativo contra ameaças.

## <a name="https-and-certificates"></a>HTTPS e certificados

O Serviço de Aplicativo permite que você proteja os aplicativos com [HTTPS](https://wikipedia.org/wiki/HTTPS). Quando seu aplicativo é criado, seu nome de domínio padrão ( \<app_name> . azurewebsites.net) já está acessível usando HTTPS. Se você [configurar um domínio personalizado para o aplicativo](app-service-web-tutorial-custom-domain.md), também deverá [protegê-lo com um certificado TLS/SSL](configure-ssl-bindings.md) para que os navegadores do cliente possam estabelecer conexões HTTPS seguras com o domínio personalizado. Há vários tipos de certificados compatíveis com o Serviço de Aplicativo:

- Certificado gerenciado do Serviço de Aplicativo gratuito
- Certificado de Serviço de Aplicativo
- Certificado de terceiros
- Certificado importado do Azure Key Vault

Para obter mais informações, confira [Adicionar um certificado TLS/SSL no Serviço de Aplicativo do Azure](configure-ssl-certificate.md).

## <a name="insecure-protocols-http-tls-10-ftp"></a>Protocolos não seguros (HTTP, TLS 1.0, FTP)

Para proteger o aplicativo contra todas as conexões não criptografadas (HTTP), o Serviço de Aplicativo fornece configuração com um clique para impor o HTTPS. As solicitações não seguras serão recusadas antes mesmo de alcançarem o código do aplicativo. Para mais informações, consulte [Impor o HTTPS](configure-ssl-bindings.md#enforce-https).

[TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 não é mais considerado seguro pelos padrões do setor, como [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). O Serviço de Aplicativo permite desabilitar os protocolos desatualizados [impondo o TLS 1.1/1.2](configure-ssl-bindings.md#enforce-tls-versions).

O Serviço de Aplicativo dá suporte ao FTP e FTPS para implantar os arquivos. No entanto, utilize FTPS em vez de FTP, se possível. Quando um ou ambos os protocolos não estiverem em uso será necessário [desabilitá-los](deploy-ftp.md#enforce-ftps).

## <a name="static-ip-restrictions"></a>Restrições de IP estático

Por padrão, o aplicativo do Serviço de Aplicativo aceita solicitações de todos os endereços IP da Internet, mas é possível limitar esse acesso a um pequeno subconjunto de endereços IP. O Serviço de Aplicativo no Windows permite definir uma lista de endereços IP com permissão para acessar o aplicativo. A lista permitida pode incluir endereços IP individuais ou um intervalo de endereços IP definidos por uma máscara de sub-rede. Para obter mais informações, consulte [Restrições de IP estático do Serviço de Aplicativo do Azure](app-service-ip-restrictions.md).

Para o serviço de aplicativo no Windows, você também pode restringir os endereços IP dinamicamente Configurando o _web.config_. Para obter mais informações, consulte [ \<dynamicIpSecurity> segurança de IP dinâmico](/iis/configuration/system.webServer/security/dynamicIpSecurity/).

## <a name="client-authentication-and-authorization"></a>Autenticação e autorização do cliente

O Serviço de Aplicativo do Azure fornece autenticação e autorização imediata de usuários ou aplicativos clientes. Quando habilitado, ele pode entrar em usuários e aplicativos clientes com pouco ou nenhum código do aplicativo. É possível implementar sua própria solução de autorização e autenticação ou permitir que o Serviço de Aplicativo processa isso para você. O módulo de autenticação e autorização processa solicitações da Web antes de entregá-las ao código do aplicativo e nega solicitações não autorizadas antes que elas atinjam o código.

A autenticação e autorização do Serviço de Aplicativo dão suporte a vários provedores de autenticação, incluindo o Azure Active Directory, contas da Microsoft, Facebook, Google e Twitter. Para saber mais, confira [Autenticação e autorização no Serviço de Aplicativo do Azure](overview-authentication-authorization.md).

## <a name="service-to-service-authentication"></a>Autenticação serviço a serviço

Ao autenticar um serviço de back-end, o Serviço de Aplicativo fornece dois mecanismos diferentes, dependendo da necessidade:

- **Serviço de identidade** - Faça logon no recurso remoto usando a identidade do próprio aplicativo. O Serviço de Aplicativo permite criar facilmente uma [identidade gerenciada](overview-managed-identity.md), que pode ser usada para autenticação com outros serviços como o [Banco de Dados SQL do Azure](/azure/sql-database/) ou [Azure Key Vault](../key-vault/index.yml). Para obter um tutorial completo desta abordagem, confira [Conexão segura do Banco de Dados SQL do Azure do Serviço de Aplicativo usando a identidade gerenciada](app-service-web-tutorial-connect-msi.md).
- **OBO (em nome de)** - Faça acesso delegado a recursos remotos em nome do usuário. Com o Azure Active Directory como provedor de autenticação, o aplicativo do Serviço de Aplicativo pode realizar a conexão delegada em um serviço remoto, como [API do Microsoft Graph](../active-directory/develop/microsoft-graph-intro.md) ou um aplicativo de API remoto no Serviço de Aplicativo. Para obter um tutorial de ponta a ponta dessa abordagem, consulte [Autenticar e autorizar usuários de ponta a ponta no Serviço de Aplicativo do Azure ](tutorial-auth-aad.md).

## <a name="connectivity-to-remote-resources"></a>Conectividade para recursos remotos

Há três tipos de recursos remotos que o aplicativo pode precisar acessar: 

- [Recursos do Azure](#azure-resources)
- [Recursos dentro de uma Rede Virtual do Microsoft Azure](#resources-inside-an-azure-virtual-network)
- [Recursos locais](#on-premises-resources)

Em cada um desses casos, o Serviço de Aplicativo fornece uma maneira para fazer conexões seguras, no entanto, você ainda deve observar as melhores práticas de segurança. Por exemplo, sempre use conexões criptografadas mesmo se o recurso de back-end permitir conexões não criptografadas. Além disso, verifique se o serviço back-end do Azure permite o conjunto mínimo de endereços IP. É possível localizar os endereços IP de saída para aplicativo em [Endereços IP de entrada e saída no Serviço de Aplicativo do Azure](overview-inbound-outbound-ips.md).

### <a name="azure-resources"></a>Recursos do Azure

Quando o aplicativo conecta os recursos do Azure, como [Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/) e [Armazenamento do Microsoft Azure](../storage/index.yml), a conexão permanece no Azure e não ultrapassa limites de rede. No entanto, a conexão passa pela rede compartilhada no Azure e, portanto, sempre verifique se a conexão está criptografada. 

Se o aplicativo estiver hospedado em um [ambiente do Serviço de Aplicativo ](environment/intro.md), você deverá [conectar os serviços do Azure com suporte usando pontos de extremidade de serviço de Rede Virtual](../virtual-network/virtual-network-service-endpoints-overview.md).

### <a name="resources-inside-an-azure-virtual-network"></a>Recursos dentro de uma Rede Virtual do Microsoft Azure

O aplicativo pode acessar recursos em uma [Rede Virtual do Microsoft Azure](../virtual-network/index.yml) através da [integração de Rede Virtual](web-sites-integrate-with-vnet.md). A integração é estabelecida com uma Rede Virtual usando uma VPN ponto a site. O aplicativo pode acessar os recursos na rede virtual usando os endereços IP privados. A conexão ponto a site, no entanto, ainda ultrapassa as redes compartilhadas no Azure. 

Para isolar completamente a conectividade de recursos das redes compartilhadas no Azure, crie o aplicativo em um [ambiente do Serviço de Aplicativo](environment/intro.md). Como um ambiente do Serviço de Aplicativo é sempre implantado em uma Rede Virtual dedicada, a conectividade entre o aplicativo e os recursos dentro da Rede Virtual é totalmente isolada. Para outros aspectos da segurança de rede em um ambiente do Serviço de Aplicativo, consulte [Isolamento de rede](#network-isolation).

### <a name="on-premises-resources"></a>Recursos locais

É possível acessar os recursos locais com segurança, como bancos de dados, de três maneiras: 

- [Conexões híbridas](app-service-hybrid-connections.md) - Estabelece uma conexão ponto a ponto com o recurso remoto por meio de um túnel TCP. O túnel TCP é estabelecido usando o TLS 1.2 com chaves de SAS (assinatura de acesso compartilhado).
- [Integração de Rede Virtual](web-sites-integrate-with-vnet.md) com VPN site a site - Conforme descrito em [Recursos dentro de uma Rede Virtual do Microsoft Azure](#resources-inside-an-azure-virtual-network), mas a Rede Virtual pode ser conectada à rede local por meio de um [VPN site a site](../vpn-gateway/tutorial-site-to-site-portal.md). Nesta topologia de rede, o aplicativo pode conectar recursos locais, como outros recursos na Rede Virtual.
- [Ambiente do Serviço de Aplicativo](environment/intro.md) com VPN site a site - Conforme descrito em [Recursos dentro de uma Rede Virtual do Microsoft Azure](#resources-inside-an-azure-virtual-network), mas a Rede Virtual pode ser conectada à rede local por meio de um [VPN site a site](../vpn-gateway/tutorial-site-to-site-portal.md). Nesta topologia de rede, o aplicativo pode conectar recursos locais, como outros recursos na Rede Virtual.

## <a name="application-secrets"></a>Segredos do aplicativo

Não armazene segredos do aplicativo, como credenciais de banco de dados, tokens de API e chaves privadas nos arquivos de configuração ou código. A abordagem comumente aceita é acessá-los como [variáveis de ambiente](https://wikipedia.org/wiki/Environment_variable) usando o padrão standard na linguagem de sua escolha. No Serviço de Aplicativo, a maneira de definir variáveis de ambiente é através de [configurações de aplicativo](configure-common.md#configure-app-settings) (e, especialmente para aplicativo .NETs, [cadeias de conexão](configure-common.md#configure-connection-strings)). As configurações de aplicativo e as cadeias de conexão são armazenadas criptografadas no Azure e serão descriptografadas somente antes de serem injetadas na memória de processo do aplicativo quando o aplicativo for iniciado. As chaves de criptografia são giradas regularmente.

Como alternativa, é possível integrar o aplicativo do Serviço de Aplicativo ao [Azure Key Vault](../key-vault/index.yml) para gerenciamento de segredos avançado. Ao [acessar o Key Vault com uma identidade gerenciada](../key-vault/general/tutorial-net-create-vault-azure-web-app.md), o aplicativo do Serviço de Aplicativo poderá acessar com segurança os segredos que você precisa.

## <a name="network-isolation"></a>Isolamento da rede

Exceto para tipo de preço **Isolado** todas as camadas executam os aplicativos na infraestrutura de rede compartilhada no Serviço de Aplicativo. Por exemplo, os endereços IP públicos e os balanceadores de carga front-end são compartilhados com outros locatários. A camada **Isolado** oferece isolamento de rede completo, executando os aplicativos em um [Ambiente do Serviço de Aplicativo](environment/intro.md) dedicado. Um ambiente do Serviço de Aplicativo é executado na própria instância da [Rede Virtual do Microsoft Azure](../virtual-network/index.yml). Isso permite que você: 

- Atenda os aplicativos por meio de um pondo de extremidade público dedicado, com front-ends dedicados.
- Atenda o aplicativo interno usando um ILB (balanceador de carga interno), que permite o acesso somente de dentro da Rede Virtual do Microsoft Azure. O ILB tem um endereço IP da sub-rede privada, que fornece isolamento total dos aplicativos da Internet.
- [Use um ILB atrás de um WAF (firewall do aplicativo Web)](environment/integrate-with-application-gateway.md). O WAF oferece proteção de nível empresarial para aplicativos voltados ao público, como proteção contra DDoS, filtragem de URI e prevenção de injeção de SQL.

Para obter mais informações, consulte [Introdução aos Ambientes do Serviço de Aplicativo do Azure](environment/intro.md).