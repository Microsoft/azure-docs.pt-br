---
title: Solucionar problemas de códigos de erro para a extensão NPS do Azure MFA-Azure Active Directory
description: Obter ajuda para resolver problemas com a extensão NPS da Autenticação Multifator do Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c0d04db6e9ccedc1e67ed0cdfd914ab42ebea0b1
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "67536937"
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Resolver mensagens de erro da extensão NPS da Autenticação Multifator do Azure

Caso você encontre erros na extensão NPS da Autenticação Multifator do Azure, use este artigo para obter uma solução mais rápida. Os logs de extensão NPS são localizados no Visualizador de Eventos em **Modos de Exibição Personalizados** > **Funções de Servidor** > **Serviços de Acesso e Política de Rede** no servidor em que a Extensão NPS está instalada.

## <a name="troubleshooting-steps-for-common-errors"></a>Etapas de solução de problemas para erros comuns

| Código de erro | Etapas de solução de problemas |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Contate o suporte](#contact-microsoft-support) e mencione a lista de etapas para a coleta de logs. Forneça o máximo de informações possíveis sobre o que aconteceu antes do erro, incluindo a ID de locatário e o nome UPN. |
| **CLIENT_CERT_INSTALL_ERROR** | Pode haver um problema com a forma como o certificado do cliente foi instalado ou associado ao locatário. Siga as instruções em [Solução de problemas da extensão NPS do MFA](howto-mfa-nps-extension.md#troubleshooting) para investigar problemas de certificado do cliente. |
| **ESTS_TOKEN_ERROR** | Siga as instruções em [Solução de problemas da extensão NPS do MFA](howto-mfa-nps-extension.md#troubleshooting) para investigar problemas de certificado do cliente e token ADAL. |
| **HTTPS_COMMUNICATION_ERROR** | O servidor NPS não pode receber respostas do Azure MFA. Verifique se os firewalls estão abertos bidirecionalmente para o tráfego de entrada e saída de https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | No servidor que executa a extensão do NPS, verifique se é possível acessar https://adnotifications.windowsazure.com e https://login.microsoftonline.com/. Se os sites não forem carregados, resolva os problemas de conectividade no servidor. |
| **Extensão NPS para o MFA do Azure:** <br> Extensão do NPS para o Azure MFA executa apenas a autenticação secundária para solicitações de Radius no estado AccessAccept. Solicitação recebida para o nome de usuário com o estado de resposta AccessReject, ignorando a solicitação. | Esse erro geralmente reflete uma falha de autenticação no AD ou que o servidor NPS não pode receber respostas do Microsoft Azure Active Directory. Verifique se seus firewalls estão abertos bidirecionalmente para o tráfego de e para https://adnotifications.windowsazure.com e https://login.microsoftonline.com usando as portas 80 e 443. Também é importante verificar que, na guia DIAL-IN de permissões de acesso de rede, a configuração é definida como "controlar o acesso por meio do Azure Policy de rede do NPS". Esse erro também pode disparar se o usuário não tiver uma licença atribuída. |
| **REGISTRY_CONFIG_ERROR** | Uma chave está ausente no registro do aplicativo, que pode ser devido à não execução do [script do PowerShell](howto-mfa-nps-extension.md#install-the-nps-extension) após a instalação. A mensagem de erro deve incluir a chave ausente. Verifique se você tem a chave em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Solicitação do RADIUS ausente. Atributo userName\Identifier obrigatório do RADIUS. Verifique se o NPS está recebendo as solicitações RADIUS | Esse erro geralmente reflete um problema de instalação. A extensão NPS deve ser instalada em servidores NPS que podem receber solicitações RADIUS. Servidores NPS que são instalados como dependências para serviços como RDG e RRAS não recebem solicitações RADIUS. A Extensão NPS não funciona quando é instalada em instalações como essas e ocorre um erro, pois ela não pode ler os detalhes da solicitação de autenticação. |
| **REQUEST_MISSING_CODE** | Verifique se o protocolo de criptografia de senha entre os servidores NPS e NAS suportam o método de autenticação secundário que você está usando. O **PAP** dá suporte a todos os métodos de autenticação do Azure MFA na nuvem: chamada telefônica, mensagem de texto unidirecional, notificação de aplicativo móvel e código de verificação de aplicativo móvel. **CHAPV2** e **EAP** dão suporte a chamada telefônica e notificação de aplicativo móvel. |
| **USERNAME_CANONICALIZATION_ERROR** | Verifique se o usuário está presente na instância do Active Directory local e se o Serviço NPS tem permissões para acessar o diretório. Se estiver usando relações de confiança entre florestas, [contate o suporte](#contact-microsoft-support) para obter mais ajuda. |

### <a name="alternate-login-id-errors"></a>Erros de ID de logon alternativo

| Código de erro | Mensagem de erro | Etapas de solução de problemas |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: falha na pesquisa do userObjectSid | Verifique se o usuário existe na instância do Active Directory local. Se estiver usando relações de confiança entre florestas, [contate o suporte](#contact-microsoft-support) para obter mais ajuda. |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: Falha na pesquisa de LoginId alternativa | Verifique se LDAP_ALTERNATE_LOGINID_ATTRIBUTE está definido como um [atributo válido do Active Directory](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Se LDAP_FORCE_GLOBAL_CATALOG for definido como True ou LDAP_LOOKUP_FORESTS for configurado com um valor não vazio, verifique se você configurou um Catálogo Global e que o atributo AlternateLoginId está adicionado a ele. <br><br> Se LDAP_LOOKUP_FORESTS estiver configurado com um valor não vazio, verifique se o valor está correto. Se houver mais de um nome de floresta, os nomes deverão ser separados por pontos e vírgulas, não espaços. <br><br> Se essas etapas não corrigirem o problema, [entre em contato com o suporte](#contact-microsoft-support) para obter mais ajuda. |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: O valor da LoginId alternativa está vazio | Verifique se o atributo AlternateLoginId está configurado para o usuário. |

## <a name="errors-your-users-may-encounter"></a>Erros que os usuários podem ver

| Código de erro | Mensagem de erro | Etapas de solução de problemas |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | O locatário chamador não tem permissões de acesso para fazer a autenticação do usuário | Verifique se o domínio do locatário e o domínio do nome UPN são os mesmos. Por exemplo, verifique se user@contoso.com está tentando se autenticar no locatário da Contoso. O UPN representa um usuário válido para o locatário no Azure. |
| **AuthenticationMethodNotConfigured** | O método de autenticação especificado não foi configurado para o usuário | Solicite ao usuário para adicionar ou verificar seus métodos de verificação de acordo com as instruções em [Gerenciar as configurações da verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Não há suporte para o método de autenticação especificado. | Colete todos os logs que incluem esse erro e [contate o suporte](#contact-microsoft-support). Quando você contatar o suporte, forneça o nome de usuário e o método de verificação secundário que disparou o erro. |
| **BecAccessDenied** | Acesso negado de retorno de chamada a MSODS Bec. Provavelmente, o nome de usuário não está definido no locatário | O usuário está presente no Active Directory local, mas não está sincronizado no Azure AD pelo AD Connect. Ou o usuário está ausente no locatário. Adicione o usuário ao Azure AD e solicite a ele para adicionar seus métodos de verificação de acordo com as instruções em [Gerenciar as configurações da verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** ou **StrongAuthenticationServiceInvalidParameter** | O número de telefone está em um formato que não pode ser reconhecido | Solicite ao usuário para corrigir seus números de telefone de verificação. |
| **InvalidSession** | A sessão especificada é inválida ou pode ter expirado | A sessão levou mais de três minutos para ser concluída. Verifique se o usuário está inserindo o código de verificação ou respondendo à notificação do aplicativo em até três minutos após o início da solicitação de autenticação. Se isso não resolver o problema, verifique se não há nenhuma latência de rede entre o cliente, Servidor NAS, Servidor NPS e o ponto de extremidade do Azure MFA.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Nenhum método de autenticação padrão foi configurado para o usuário | Solicite ao usuário para adicionar ou verificar seus métodos de verificação de acordo com as instruções em [Gerenciar as configurações da verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). Verifique se o usuário escolheu um método de autenticação padrão e configurou desse método para sua conta. |
| **OathCodePinIncorrect** | Código e PIN incorretos inseridos. | Esse erro não é esperado na extensão do NPS. Se o usuário ver esse erro, [contate o suporte](#contact-microsoft-support) para obter ajuda na solução de problemas. |
| **ProofDataNotFound** | Os dados de prova não foram configurados para o método de autenticação especificado. | Solicite ao usuário para tentar outro método de verificação ou adicionar um novo método de verificação de acordo com as instruções em [Gerenciar as configurações da verificação em duas etapas](../user-help/multi-factor-authentication-end-user-manage-settings.md). Se o usuário continuar vendo esse erro depois de confirmar que o método de verificação está configurado corretamente, [contate o suporte](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Código e PIN incorretos inseridos. (OneWaySMS) | Esse erro não é esperado na extensão do NPS. Se o usuário ver esse erro, [contate o suporte](#contact-microsoft-support) para obter ajuda na solução de problemas. |
| **TenantIsBlocked** | O locatário está bloqueado | [Contate o suporte](#contact-microsoft-support) com a ID de Diretório da página de propriedades do Azure AD no portal do Azure. |
| **UserNotFound** | O usuário especificado não foi encontrado | O locatário não está mais visível como ativo no Azure AD. Verifique se sua assinatura está ativa e se você tem os aplicativos de terceiros necessários. Também verifique se o locatário na entidade de certificado é conforme esperado e se o certificado ainda é válido e está registrado na entidade de serviço. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>Mensagens que os usuários podem ver que não são erros

Às vezes, os usuários podem receber mensagens da Autenticação Multifator, pois sua solicitação de autenticação falhou. Elas não são erros no produto de configuração, mas são avisos intencionais que explicam por que uma solicitação de autenticação foi negada.

| Código de erro | Mensagem de erro | Etapas recomendadas | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Código incorreto inserido\Código OATH incorreto | O usuário inseriu o código incorreto. Solicite a ele para que repita a solicitação de um novo código ou entre novamente. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Repetição de código máxima permitida atingida | O usuário não passou no desafio de verificação várias vezes. Dependendo das configurações, ele pode precisar ser desbloqueado por um administrador nesse momento.  |
| **SMSAuthFailedWrongCodeEntered** | Código incorreto inserido/OTP da mensagem de texto incorreto | O usuário inseriu o código incorreto. Solicite a ele para que repita a solicitação de um novo código ou entre novamente. |

## <a name="errors-that-require-support"></a>Erros que exigem suporte

Se você encontrar um desses erros, recomendamos que [contate o suporte](#contact-microsoft-support) para obter ajuda de diagnóstico. Não há nenhum conjunto padrão de etapas que pode corrigir esses erros. Ao contatar o suporte, lembre-se de incluir o máximo de informações possíveis sobre as etapas que levaram a um erro, bem como as informações de locatário.

| Código de erro | Mensagem de erro |
| ---------- | ------------- |
| **InvalidParameter** | A solicitação não pode ser nula |
| **InvalidParameter** | ObjectId não deve ser nulo nem vazio para ReplicationScope:{0} |
| **InvalidParameter** | O tamanho de CompanyName \{0}\é maior que o tamanho máximo permitido {1} |
| **InvalidParameter** | O UserPrincipalName não deve ser nulo nem vazio |
| **InvalidParameter** | A TenantId fornecida não está no formato correto |
| **InvalidParameter** | A SessionId não deve ser nula nem vazia |
| **InvalidParameter** | Não foi possível resolver nenhum ProofData da solicitação ou do Msods. O ProofData é desconhecido |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Próximas etapas

### <a name="troubleshoot-user-accounts"></a>Solução de problemas de contas de usuário

Se os usuários estiverem [tendo problemas com a verificação em duas etapas](../user-help/multi-factor-authentication-end-user-troubleshoot.md), ajude-os a diagnosticar os problemas por conta própria.

### <a name="contact-microsoft-support"></a>Contatar Suporte da Microsoft

Caso você precise de mais ajuda, contate um profissional de suporte por meio do [suporte do Servidor de Autenticação Multifator do Azure](https://support.microsoft.com/oas/default.aspx?prid=14947). Ao entrar em contato conosco, é útil incluir o máximo possível de informações sobre o problema. As informações que você pode fornecer incluem a página em que viu o erro, o código de erro específico, a ID da sessão específica, a ID do usuário que viu o erro e os logs de depuração.

Para coletar logs de depuração para o diagnóstico de suporte, use as seguintes etapas no servidor de NPS:

1. Abra o Editor do Registro e navegue até HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa e defina **VERBOSE_LOG** como **TRUE**
2. Abra um prompt de comando do Administrador e execute estes comandos:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

3. Reproduzir o problema

4. Pare o rastreamento com estes comandos:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

5. Abra o Editor do Registro e navegue até HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa e defina **VERBOSE_LOG** como **FALSE**
6. Compacte o conteúdo da pasta C:\NPS e anexe o arquivo compactado ao caso de suporte.
