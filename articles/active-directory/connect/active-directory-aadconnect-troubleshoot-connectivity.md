---
title: 'Azure AD Connect: solucionar problemas de conectividade | Microsoft Docs'
description: Explica como solucionar problemas de conectividade com o Azure AD Connect.
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: f9631e8a383b88421c55d9c42c8059df9e732800
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Solucionar problemas de conectividade com o Azure AD Connect
Esse artigo explica como funciona a conectividade entre o Azure AD Connect e o AD do Azure e como solucionar problemas de conectividade. Esses problemas são mais prováveis de serem vistos em um ambiente com um servidor proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Solucionar problemas de conectividade no assistente de instalação
O Azure AD Connect está usando Autenticação Moderna (usando a biblioteca ADAL) para autenticação. O assistente de instalação e o mecanismo de sincronização adequado exigem machine.config para ser configurado corretamente já que são dois aplicativos .NET.

Neste artigo, mostraremos como a Fabrikam se conecta ao AD do Azure por meio de seu proxy. O servidor proxy é chamado fabrikamproxy e está usando a porta 8080.

Primeiro, precisamos garantir que [**machine.config**](active-directory-aadconnect-prerequisites.md#connectivity) esteja configurado corretamente.  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

> [!NOTE]
> Em alguns blogs sem relação com a Microsoft, está documentado que as alterações devem ser feitas em miiserver.exe.config. No entanto, esse arquivo será substituído em cada atualização, portanto, mesmo que ele funcione durante a instalação inicial, o sistema deixará de funcionar na primeira atualização. Por esse motivo, a recomendação é atualizar o machine.config.
>
>

O servidor proxy também deve ter as URLs necessárias abertas. A lista oficial é documentada em [intervalos de endereços IP e URLs do Office 365 ](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Dessas URLs, a tabela a seguir é o mínimo absoluto para oferecer a capacidade de se conectar ao AD do Azure. Essa lista não inclui quaisquer recursos opcionais, como write-back de senha ou Azure AD Connect Health. Ela está documentado aqui para ajudar na solução de problemas da configuração inicial.

| URL | Port | Descrição |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Usada para baixar as listas CRL. |
| \*.verisign.com |HTTP/80 |Usada para baixar as listas CRL. |
| \*.entrust.com |HTTP/80 |Usada para baixar as listas CRL para o MFA. |
| \*.windows.net |HTTPS/443 |Usado para entrar no AD do Azure. |
| Secure.aadcdn.microsoftonline p.com |HTTPS/443 |Usado para MFA. |
| \*.microsoftonline.com |HTTPS/443 |Usado para configurar o diretório do Azure AD e importar/exportar dados. |

## <a name="errors-in-the-wizard"></a>Erros no assistente
O assistente de instalação está usando dois contextos de segurança diferentes. Na página **Conectar-se ao Azure AD** utiliza o usuário conectado no momento. Na página **Configurar** o assistente muda para a [conta que executa o serviço no mecanismo de sincronização](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account). Se houver um problema, provavelmente ele aparecerá já na página **Conectar ao Azure AD** do assistente, uma vez que a configuração do proxy é global.

Estes são os problemas mais comuns que você encontrará no assistente de instalação.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>O assistente de instalação não foi configurado corretamente
Esse erro aparecerá quando o assistente não conseguir acessar o proxy.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

* Se você vir esse erro, verifique se o [machine.config](active-directory-aadconnect-prerequisites.md#connectivity) foi configurado corretamente.
* Se parecer correto, siga as etapas em [Verificar a conectividade do proxy](#verify-proxy-connectivity) para ver se o problema também ocorre fora do assistente.

### <a name="a-microsoft-account-is-used"></a>Uma conta da Microsoft é usada
Se você usar uma **conta da Microsoft** em vez de uma conta **corporativa ou de estudante**, você verá um erro genérico.  
![Uma conta da Microsoft é usada](./media/active-directory-aadconnect-troubleshoot-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Não é possível alcançar o ponto de extremidade da MFA
Esse erro aparecerá se o ponto de extremidade **https://secure.aadcdn.microsoftonline-p.com** não puder ser alcançado e o administrador global tiver a MFA habilitada.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

* Se você vir esse erro, verifique se o ponto de extremidade **secure.aadcdn.microsoftonline-p.com** foi adicionado ao proxy.

### <a name="the-password-cannot-be-verified"></a>A senha não pode ser verificada
Se o assistente de instalação for bem-sucedido ao conectar-se ao AD do Azure, mas a senha não puder ser verificada, você verá este erro:  
![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

* A senha é uma senha temporária e deve ser alterada? É realmente a senha correta? Tente fazer logon em https://login.microsoftonline.com (em outro computador que não seja o servidor do Azure AD Connect) e verifique se a conta é utilizável.

### <a name="verify-proxy-connectivity"></a>Verificar a conectividade do proxy
Para verificar se o servidor do Azure AD Connect tem conectividade real com o Proxy e a Internet, use o PowerShell para ver se o proxy está permitindo solicitações da Web ou não. Em um prompt do PowerShell, execute `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (Tecnicamente, a primeira chamada é para https://login.microsoftonline.com e esse URI também funciona, mas o outro URI responde mais rápido).

O PowerShell usa a configuração em machine.config para entrar em contato com o proxy. As configurações no winhttp/netsh não devem afetar esses cmdlets.

Se o proxy estiver configurado corretamente, você deverá obter um status de êxito: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Se você receber **Não é possível se conectar ao servidor remoto** é porque o PowerShell está tentando fazer uma chamada direta sem usar o proxy ou o DNS não está configurado corretamente. Verifique se o arquivo **machine.config** está configurado corretamente.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Se o proxy não estiver configurado corretamente, você receberá um erro: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

| Erro | Texto do erro | Comentário |
| --- | --- | --- |
| 403 |Proibido |O proxy não foi aberto para a URL solicitada. Examine a configuração do proxy e verifique se as [URLs](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) foram abertas. |
| 407 |Autenticação de proxy necessária |O servidor proxy solicitou uma entrada e nenhuma foi fornecida. Se o servidor proxy exigir autenticação, verifique se isso está configurado em machine.config. Verifique também se você está usando contas de domínio para o usuário que executa o assistente e para a conta de serviço. |

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>O padrão de comunicação entre o Azure AD Connect e o AD do Azure
Se você executou todas essas etapas anteriores e ainda não conseguiu se conectar, comece a examinar os logs de rede. Esta seção está documentando um padrão de conectividade normal e bem-sucedido. Também está listando distrações comuns que podem ser ignoradas ao ler os logs de rede.

* Há chamadas para https://dc.services.visualstudio.com. Não é necessário que esta URL esteja aberta no proxy para que a instalação tenha êxito e essas chamadas podem ser ignoradas.
* Veja que a resolução DNS lista os hosts reais no namespace DNS nsatc.net e em outros namespaces que não estejam em microsoftonline.com. No entanto, não há solicitações de serviços Web nos nomes de servidor reais e você não precisará adicionar essas URLs ao proxy.
* Os pontos de extremidade adminwebservice e provisioningapi são pontos de extremidade de descoberta usados para localizar o ponto de extremidade real a ser usado. Esses pontos de extremidade são diferentes dependendo de sua região.

### <a name="reference-proxy-logs"></a>Logs de proxy de referência
Veja um despejo de um log de proxy real e a página do assistente de instalação de onde ele foi tirado (entradas duplicadas para o mesmo ponto de extremidade foram removidas). Esta seção pode ser usada como referência para seus próprios logs de proxy e de rede. Os pontos de extremidade reais podem ser diferentes em seu ambiente (especialmente as URLs em *itálico*).

**Conecte-se ao Azure AD**

| Hora | URL |
| --- | --- |
| 11/01/2016 08:31 |connect://login.microsoftonline.com:443 |
| 11/01/2016 08:31 |connect://adminwebservice.microsoftonline.com:443 |
| 11/01/2016 08:32 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 11/01/2016 08:32 |connect://login.microsoftonline.com:443 |
| 11/01/2016 08:33 |connect://provisioningapi.microsoftonline.com:443 |
| 11/01/2016 08:33 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Configurar**

| Hora | URL |
| --- | --- |
| 11/01/2016 08:43 |connect://login.microsoftonline.com:443 |
| 11/01/2016 08:43 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 11/01/2016 08:43 |connect://login.microsoftonline.com:443 |
| 11/01/2016 08:44 |connect://adminwebservice.microsoftonline.com:443 |
| 11/01/2016 08:44 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 11/01/2016 08:44 |connect://login.microsoftonline.com:443 |
| 11/01/2016 08:44 |connect://adminwebservice.microsoftonline.com:443 |
| 11/01/2016 08:44 |connect://*bba800-anchor*.microsoftonline.com:443 |
| 11/01/2016 08:44 |connect://login.microsoftonline.com:443 |
| 11/01/2016 08:46 |connect://provisioningapi.microsoftonline.com:443 |
| 11/01/2016 08:46 |connect://*bwsc02-relay*.microsoftonline.com:443 |

**Sincronização inicial**

| Hora | URL |
| --- | --- |
| 11/01/2016 08:48 |connect://login.windows.net:443 |
| 11/01/2016 08:49 |connect://adminwebservice.microsoftonline.com:443 |
| 11/01/2016 08:49 |connect://*bba900-anchor*.microsoftonline.com:443 |
| 11/01/2016 08:49 |connect://*bba800-anchor*.microsoftonline.com:443 |

## <a name="authentication-errors"></a>Erros de autenticação
Esta seção aborda os erros que podem ser retornados do ADAL (a biblioteca de autenticação usada pelo Azure AD Connect) e do PowerShell. O erro explicado deve ajudá-lo a entender as próximas etapas.

### <a name="invalid-grant"></a>Concessão Inválida
Senha ou nome de usuário inválido. Para saber mais, confira [A senha não pode ser verificada](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Tipo de usuário desconhecido
O seu diretório do Azure AD não pode ser encontrado ou resolvido. Talvez você esteja tentando fazer logon com um nome de usuário em um domínio não verificado?

### <a name="user-realm-discovery-failed"></a>Falha na descoberta do realm de usuário
Problemas de configuração de rede ou proxy. Não é possível acessar a rede. Confira [Solucionar problemas de conectividade no assistente de instalação](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>A senha do usuário expirou
Suas credenciais expiraram. Altere a sua senha.

### <a name="authorizationfailure"></a>AuthorizationFailure
Problema desconhecido.

### <a name="authentication-cancelled"></a>Autenticação cancelada
O desafio da autenticação multifator (MFA) foi cancelado.

### <a name="connecttomsonline"></a>ConnectToMSOnline
A autenticação foi bem-sucedida, mas o PowerShell do Azure AD tem um problema de autenticação.

### <a name="azurerolemissing"></a>AzureRoleMissing
A autenticação foi bem-sucedida. Você não é um administrador global.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
A autenticação foi bem-sucedida. O gerenciamento de identidades com privilégios foi habilitado e atualmente você não é um administrador global. Para saber mais, confira [Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md).

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
A autenticação foi bem-sucedida. Não foi possível recuperar as informações da empresa do Azure AD.

### <a name="retrievedomains"></a>RetrieveDomains
A autenticação foi bem-sucedida. Não foi possível recuperar informações de domínio do Azure AD.

### <a name="unexpected-exception"></a>Exceção inesperada
Mostrada como um erro inesperado no assistente de instalação. Poderá ocorrer se você usar uma **conta da Microsoft** em vez de uma conta **corporativa ou de estudante**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Etapas para solucionar problemas de versões anteriores.
O assistente de conexão foi desativado a partir das versões com número de compilação 1.1.105.0 (lançada em fevereiro de 2016). Esta seção e a configuração não são mais necessárias, mas são mantidas como referência.

Para que o assistente de conexão funcione, o winhttp deve ser configurado. Essa configuração pode ser feita com [ **netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>O assistente de conexão não foi configurado corretamente
Esse erro ocorre quando o Assistente de conexão não consegue acessar o proxy ou o proxy não está permitindo a solicitação.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

* Se você vir esse erro, examine a configuração do proxy no [netsh](active-directory-aadconnect-prerequisites.md#connectivity) e verifique se ela está correta.
  ![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
* Se parecer correto, siga as etapas em [Verificar a conectividade do proxy](#verify-proxy-connectivity) para ver se o problema também ocorre fora do assistente.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](active-directory-aadconnect.md).
