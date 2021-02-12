---
title: Proxy de Aplicativo do Azure Active Directory perguntas frequentes
description: Aprenda as respostas para perguntas frequentes sobre como usar o Proxy de Aplicativo do AD do Azure para publicar aplicativos internos e locais para usuários remotos.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 07/23/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: 121dcdf51374f625ad7393bb181b1be215775a0b
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257770"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Perguntas frequentes sobre o proxy de aplicativo Active Directory (Azure AD)

Esta página responde a perguntas frequentes sobre o proxy de aplicativo Azure Active Directory (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Habilitar o Proxy de Aplicativo do Azure AD

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Qual licença é necessária para usar o Azure Proxy de Aplicativo do AD?

Para usar o Azure Proxy de Aplicativo do AD, você deve ter uma licença Azure AD Premium P1 ou P2. Para obter mais informações sobre licenciamento, consulte [preços de Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="what-happens-to-azure-ad-application-proxy-in-my-tenant-if-my-license-expires"></a>O que acontece com o Azure Proxy de Aplicativo do AD em meu locatário, se minha licença expirar?
Se sua licença expirar, o proxy de aplicativo será desabilitado automaticamente. As informações do aplicativo serão salvas por até um ano.

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Por que o botão "Habilitar proxy de aplicativo está esmaecido?"

Verifique se você tem pelo menos uma licença Azure AD Premium P1 ou P2 e um conector de Proxy de Aplicativo do AD do Azure instalado. Depois de instalar com êxito seu primeiro conector, o serviço de Proxy de Aplicativo do AD do Azure será habilitado automaticamente.

## <a name="connector-configuration"></a>Configuração do conector

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Os serviços do conector de proxy de aplicativo podem ser executados em um contexto de usuário diferente do padrão?

Não, esse cenário não tem suporte. As configurações padrão são:

- Conector de proxy de aplicativo do Microsoft AAD-WAPCSvc-serviço de rede
- Atualizador do Conector de Proxy de Aplicativo do Microsoft AAD-WAPCUpdaterSvc-NT Authority\System

### <a name="can-a-guest-user-with-the-global-administrator-or-the-application-administrator-role-register-the-connector-for-the-guest-tenant"></a>Um usuário convidado com o administrador global ou a função de administrador de aplicativos registra o conector para o locatário (convidado)?

Não, atualmente, isso não é possível. A tentativa de registro sempre é feita no locatário inicial do usuário.

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Meu aplicativo de back-end é hospedado em vários servidores Web e requer persistência de sessão de usuário (adesão). Como posso obter persistência de sessão? 

Para obter recomendações, consulte [alta disponibilidade e balanceamento de carga dos seus aplicativos e conectores de proxy de aplicativo](application-proxy-high-availability-load-balancing.md).

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>A terminação TLS (inspeção ou aceleração de TLS/HTTPS) no tráfego dos servidores de conector para o Azure tem suporte?

O conector de proxy de aplicativo executa a autenticação baseada em certificado no Azure. A terminação TLS (inspeção ou aceleração TLS/HTTPS) interrompe esse método de autenticação e não tem suporte. O tráfego do conector para o Azure deve ignorar todos os dispositivos que estão executando a terminação de TLS.  

### <a name="is-tls-12-required-for-all-connections"></a>O TLS 1,2 é necessário para todas as conexões?
Sim. Para fornecer a melhor criptografia da categoria para nossos clientes, o serviço do Proxy de Aplicativo limita o acesso somente a protocolos TLS 1.2. Essas alterações foram distribuídas gradualmente e estão entrando em vigor desde 31 de agosto de 2019. Certifique-se de que todas as suas combinações cliente-servidor e navegador-servidor estejam atualizadas para usar o TLS 1.2 para manter a conexão com o serviço do Proxy de Aplicativo. Elas incluem clientes que seus usuários estão usando para acessar aplicativos publicados pelo Proxy de Aplicativo. Confira Preparação para [TLS 1.2 no Office 365](/microsoft-365/compliance/prepare-tls-1.2-in-office-365) para obter referências e recursos.

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Posso fazer um dispositivo proxy de encaminhamento entre os servidores de conector e o servidor de aplicativos back-end?
Sim, esse cenário tem suporte a partir da versão 1.5.1526.0 do conector. Consulte [trabalhar com servidores proxy locais existentes](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Devo criar uma conta dedicada para registrar o conector com o Proxy de Aplicativo do AD do Azure?

Não há motivo para isso. Qualquer conta de administrador global ou de administração de aplicativos funcionará. As credenciais inseridas durante a instalação não são usadas após o processo de registro. Em vez disso, um certificado é emitido para o conector, que é usado para autenticação desse ponto em diante.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Como posso monitorar o desempenho do conector de Proxy de Aplicativo do AD do Azure?

Há contadores do monitor de desempenho que são instalados junto com o conector do. Para exibi-los:  

1. Selecione **Iniciar**, digite "Perfmon" e pressione Enter.
2. Selecione **Monitor de desempenho** e clique no **+** ícone verde.
3. Adicione os contadores do **conector de proxy de aplicativo do Microsoft AAD** que você deseja monitorar.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>O conector do Proxy de Aplicativo do AD do Azure precisa estar na mesma sub-rede que o recurso?

O conector não precisa estar na mesma sub-rede. No entanto, ele precisa de resolução de nomes (DNS, arquivo de hosts) para o recurso e a conectividade de rede necessária (roteamento para o recurso, portas abertas no recurso, etc.). Para obter recomendações, consulte [considerações de topologia de rede ao usar proxy de aplicativo do Azure Active Directory](application-proxy-network-topology.md).

### <a name="what-versions-of-windows-server-can-i-install-a-connector-on"></a>Em quais versões do Windows Server posso instalar um conector?

O proxy de aplicativo requer o Windows Server 2012 R2 ou posterior. Atualmente, há uma limitação no HTTP2 para o Windows Server 2019. Para usar com êxito o conector no Windows Server 2019, será necessário adicionar a seguinte chave do registro e reiniciar o servidor:

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
```

## <a name="application-configuration"></a>Configuração de aplicativo

### <a name="i-am-receiving-an-error-about-an-invalid-certificate-or-possible-wrong-password"></a>Estou recebendo um erro sobre um certificado inválido ou uma possível senha incorreta

Depois de carregar o certificado SSL, você receberá a mensagem "certificado inválido, possível senha incorreta" no Portal.

Aqui estão algumas dicas para solucionar esse erro:
- Verifique se há problemas com o certificado. Instale-o em seu computador local. Se você não tiver problemas, o certificado será bom.
- Verifique se a senha não contém nenhum caractere especial. Para teste, a senha deve conter apenas os caracteres 0-9, A-Z e a-z.
- Se o certificado foi criado com o provedor de armazenamento de chaves de software da Microsoft, o algoritmo RSA deve ser usado.

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Qual é o comprimento do tempo limite de back-end padrão e "longo"? O tempo limite pode ser estendido?

O comprimento padrão é de 85 segundos. A configuração "longa" é de 180 segundos. O limite de tempo limite não pode ser estendido.

### <a name="can-a-service-principal-manage-application-proxy-using-powershell-or-microsoft-graph-apis"></a>Uma entidade de serviço pode gerenciar o proxy de aplicativo usando o PowerShell ou Microsoft Graph APIs?

Não, não há suporte para esse recurso no momento.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Como fazer alterar a página de aterrissagem que meu aplicativo carrega?

Na página registros do aplicativo, você pode alterar a URL da Home Page para a URL externa desejada da página de aterrissagem. A página especificada será carregada quando o aplicativo for iniciado em meus aplicativos ou no portal do Office 365. Para obter as etapas de configuração, consulte [definir um Home Page personalizado para aplicativos publicados usando o Azure proxy de aplicativo do AD](./application-proxy-configure-custom-home-page.md)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Somente aplicativos baseados no IIS podem ser publicados? E quanto aos aplicativos Web em execução em servidores Web que não são do Windows? O conector precisa ser instalado em um servidor com o IIS instalado?

Não, não há nenhum requisito do IIS para aplicativos publicados. Você pode publicar aplicativos Web em execução em servidores diferentes do Windows Server. No entanto, talvez você não consiga usar a pré-autenticação com um servidor não Windows, dependendo de se o servidor Web dá suporte a Negotiate (autenticação Kerberos). O IIS não é necessário no servidor onde o conector está instalado.

### <a name="can-i-configure-application-proxy-to-add-the-hsts-header"></a>Posso configurar o proxy de aplicativo para adicionar o cabeçalho HSTS?
O proxy de aplicativo não adiciona automaticamente o cabeçalho HTTP Strict-Transport-Security a respostas HTTPS, mas manterá o cabeçalho se ele estiver na resposta original enviada pelo aplicativo publicado. Provar uma configuração para habilitar essa funcionalidade está no roteiro. Se você estiver interessado em uma visualização que permite adicionar isso a respostas, entre em contato com aadapfeedback@microsoft.com para obter detalhes.

## <a name="integrated-windows-authentication"></a>Autenticação Integrada do Windows

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Quando devo usar o método PrincipalsAllowedToDelegateToAccount ao configurar a delegação restrita de Kerberos (KCD)?

O método PrincipalsAllowedToDelegateToAccount é usado quando servidores de conector estão em um domínio diferente da conta de serviço de aplicativo Web. Ele requer o uso da delegação restrita baseada em recursos.
Se os servidores do conector e a conta de serviço do aplicativo Web estiverem no mesmo domínio, você poderá usar Active Directory usuários e computadores para definir as configurações de delegação em cada uma das contas do computador do conector, permitindo que elas deleguem ao SPN de destino.

Se os servidores do conector e a conta de serviço de aplicativo Web estiverem em domínios diferentes, a delegação baseada em recursos será usada. As permissões de delegação são configuradas no servidor Web de destino e na conta de serviço de aplicativo Web. Esse método de delegação restrita é relativamente novo. O método foi introduzido no Windows Server 2012, que dá suporte à delegação entre domínios, permitindo que o proprietário do recurso (serviço Web) controle quais contas de computador e serviço podem delegar a ele. Não há nenhuma interface do usuário para ajudar nessa configuração, portanto, você precisará usar o PowerShell.
Para obter mais informações, consulte o White Paper [noções básicas sobre a delegação restrita de Kerberos com o proxy de aplicativo](https://aka.ms/kcdpaper).

### <a name="does-ntlm-authentication-work-with-azure-ad-application-proxy"></a>A autenticação NTLM funciona com o Proxy de Aplicativo do AD do Azure?

A autenticação NTLM não pode ser usada como um método de pré-autenticação ou logon único. A autenticação NTLM pode ser usada somente quando pode ser negociada diretamente entre o cliente e o aplicativo Web publicado. Usar a autenticação NTLM geralmente faz com que uma solicitação de entrada apareça no navegador.

### <a name="can-i-use-the-logon-identity-on-premises-user-principal-name-or-on-premises-sam-account-name-in-a-b2b-iwa-single-sign-on-scenario"></a>Posso usar a identidade de logon "nome principal de usuário local" ou "nome de conta SAM local" em um cenário de logon único IWA B2B?

Não, isso não funcionará, pois um usuário convidado no Azure AD não tem o atributo exigido por qualquer uma das identidades de logon mencionadas acima.

Nesse caso, haverá um fallback para "nome principal do usuário". Para obter mais detalhes sobre o cenário B2B, leia [conceder usuários B2B no acesso do Azure ad aos seus aplicativos locais](../external-identities/hybrid-cloud-to-on-premises.md).

## <a name="pass-through-authentication"></a>Autenticação de passagem

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Posso usar políticas de acesso condicional para aplicativos publicados com autenticação de passagem?

As políticas de acesso condicional são impostas somente para usuários previamente autenticados com êxito no Azure AD. A autenticação de passagem não dispara a autenticação do Azure AD, portanto, as políticas de acesso condicional não podem ser impostas. Com a autenticação de passagem, as políticas de MFA devem ser implementadas no servidor local, se possível, ou habilitando a pré-autenticação com o Azure Proxy de Aplicativo do AD.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Posso publicar um aplicativo Web com requisito de autenticação de certificado de cliente?

Não, esse cenário não tem suporte porque o proxy de aplicativo encerrará o tráfego TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Publicação Área de Trabalho Remota gateway

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Como posso publicar Área de Trabalho Remota gateway sobre o Proxy de Aplicativo do AD do Azure?

Consulte [publicar área de trabalho remota com o proxy de aplicativo do AD do Azure](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>Posso usar a delegação restrita de Kerberos (autenticação única Sign-On integrada do Windows) no cenário de publicação de gateway Área de Trabalho Remota?

Não, esse cenário não tem suporte.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Meus usuários não usam o Internet Explorer 11 e o cenário de pré-autenticação não funciona para eles. Isso é esperado?

Sim, é esperado. O cenário de pré-autenticação requer um controle ActiveX, que não tem suporte em navegadores de terceiros.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>Há suporte para o Área de Trabalho Remota Web Client (HTML5)?

Sim, esse cenário está atualmente em visualização pública. Consulte [publicar área de trabalho remota com o proxy de aplicativo do AD do Azure](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>Depois de configurar o cenário de pré-autenticação, percebi que o usuário precisa autenticar duas vezes: primeiro no formulário de entrada do Azure AD e, em seguida, no formulário de entrada do RDWeb. Isso é esperado? Como posso reduzir isso para uma entrada?

Sim, é esperado. Se o computador do usuário for ingressado no Azure AD, o usuário entrará no Azure AD automaticamente. O usuário precisa fornecer suas credenciais somente no formulário de entrada do RDWeb.

## <a name="sharepoint-publishing"></a>Publicação do SharePoint

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Como posso publicar o SharePoint no Azure Proxy de Aplicativo do AD?

Consulte [habilitar o acesso remoto ao SharePoint com o Azure proxy de aplicativo do AD](application-proxy-integrate-with-sharepoint-server.md).

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>Posso usar o aplicativo móvel do SharePoint (iOS/Android) para acessar um SharePoint Server publicado?

O [aplicativo do SharePoint Mobile](/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) não oferece suporte a Azure Active Directory pré-autenticação no momento.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Publicação de Serviços de Federação do Active Directory (AD FS) (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Posso usar o Azure Proxy de Aplicativo do AD como AD FS proxy (como o proxy de aplicativo Web)?

Não. O Proxy de Aplicativo do AD do Azure foi projetado para funcionar com o Azure AD e não atende aos requisitos para atuar como um proxy de AD FS.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>O suporte ao WebSocket funciona para aplicativos diferentes do QlikSense?

Atualmente, o suporte ao protocolo WebSocket ainda está em visualização pública e pode não funcionar para outros aplicativos. Alguns clientes tiveram sucesso misto usando o protocolo WebSocket com outros aplicativos. Se você testar esses cenários, adoraríamos ouvir seus resultados. Envie-nos seus comentários em aadapfeedback@microsoft.com .

Os recursos (EventLogs, PowerShell e Serviços de Área de Trabalho Remota) no centro de administração do Windows (WAC) ou no cliente Web do Área de Trabalho Remota (HTML5) não funcionam com o Azure Proxy de Aplicativo do AD no momento.

## <a name="link-translation"></a>Conversão de link

### <a name="does-using-link-translation-affect-performance"></a>O uso da conversão de links afeta o desempenho?

Sim. A conversão de link afeta o desempenho. O serviço de proxy de aplicativo examina o aplicativo em busca de links codificados e os substitui por suas respectivas URLs externas publicadas antes de apresentá-los ao usuário. 

Para obter o melhor desempenho, é recomendável usar URLs internas e externas idênticas Configurando [domínios personalizados](./application-proxy-configure-custom-domain.md). Se o uso de domínios personalizados não for possível, você poderá melhorar o desempenho da tradução de links usando a extensão de entrada segura de meus aplicativos ou o navegador Microsoft Edge em dispositivos móveis. Consulte [redirecionar links codificados para aplicativos publicados com o Azure proxy de aplicativo do AD](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Curingas

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Como fazer usar curingas para publicar dois aplicativos com o mesmo nome de domínio personalizado, mas com protocolos diferentes, um para HTTP e outro para HTTPS?

Esse cenário não tem suporte diretamente. As opções para esse cenário são:

1. Publique as URLs HTTP e HTTPS como aplicativos separados com um curinga, mas forneça a cada um um domínio personalizado diferente. Essa configuração funcionará, pois elas têm diferentes URLS externas.

2. Publique a URL HTTPS por meio de um aplicativo curinga. Publicar os aplicativos HTTP separadamente usando estes cmdlets do PowerShell do proxy de aplicativo:
   - [Gerenciamento de aplicativos de proxy de aplicativo](/powershell/module/azuread/#application_proxy_application_management&preserve-view=true)
   - [Gerenciamento de conector de proxy de aplicativo](/powershell/module/azuread/#application_proxy_connector_management&preserve-view=true)
