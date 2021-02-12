---
title: Planejar e solucionar problemas de alterações de UPN (nome principal de usuário) do Azure
description: Entender problemas conhecidos e mitigações para alterações de UPN
services: active-directory
ms.service: active-directory
ms.subservice: hybrid
ms.topic: how-to
ms.date: 03/13/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19e40d135316c1c7cd270d2804fff1f487937685
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96858528"
---
# <a name="plan-and-troubleshoot-user-principal-name-changes-in-azure-active-directory"></a>Planejar e solucionar problemas de alterações de nome principal de usuário no Azure Active Directory

Um nome principal de usuário (UPN) é um atributo que é um padrão de comunicação da Internet para contas de usuário. Um UPN consiste em um prefixo UPN (o nome da conta de usuário) e um sufixo UPN (um nome de domínio de DNS). O prefixo une o sufixo usando o símbolo " \@ ". Por exemplo, someone@example.com. Um UPN deve ser exclusivo entre todos os objetos de entidade de segurança em uma floresta de diretórios. 

**Este artigo pressupõe que você está usando o UPN como o identificador de usuário. Ele aborda o planejamento de alterações de UPN e a recuperação de problemas que podem resultar de alterações de UPN.**

> [!NOTE]
> Para os desenvolvedores, recomendamos que você use o objectID do usuário como o identificador imutável, em vez de endereços UPN ou de email, pois seus valores podem ser alterados.


## <a name="learn-about-upns-and-upn-changes"></a>Saiba mais sobre os UPNs e as alterações de UPN
As páginas de entrada geralmente solicitam que os usuários insiram seu endereço de email quando o valor necessário é, na verdade, seu UPN. Portanto, você deve ter certeza de alterar o UPN de usuários sempre que seu endereço de email principal for alterado.

Os endereços de email primários dos usuários podem mudar por vários motivos:

* Remarcação da empresa

* funcionários mudando para diferentes divisões da empresa 

* fusões e aquisições

* alterações de nome de funcionário

### <a name="types-of-upn-changes"></a>Tipos de alterações de UPN

Você pode alterar um UPN alterando o prefixo, o sufixo ou ambos.

* **Alterando o prefixo**.

   *  Por exemplo, se o nome de uma pessoa foi alterado, você pode alterar o nome da conta:  
BSimon@contoso.comparaBJohnson@contoso.com

   * Você também pode alterar o padrão corporativo para prefixos:  
Bsimon@contoso.comparaBritta.Simon@contoso.com

* **Alterando o sufixo**. <br>

    Por exemplo, se uma pessoa alterou as divisões, você pode alterar seu domínio: 

   * Britta.Simon@contoso.com em Britta.Simon@contosolabs.com <br>
     Ou<br>
    * Britta.Simon@corp.contoso.com em Britta.Simon@labs.contoso.com 

É recomendável alterar o UPN de usuários sempre que seu endereço de email principal for atualizado.

Durante a sincronização inicial do Active Directory ao Azure AD, verifique se os emails dos usuários são idênticos aos seus UPNs.

### <a name="upns-in-active-directory"></a>UPNs em Active Directory

No Active Directory, o sufixo UPN padrão é o nome DNS do domínio em que você criou a conta de usuário. Na maioria dos casos, esse é o nome de domínio que você registra como o domínio corporativo na Internet. Se você criar a conta de usuário no domínio contoso.com, o UPN padrão será

username@contoso.com

 No entanto, você pode [adicionar mais sufixos UPN](../fundamentals/add-custom-domain.md) usando Active Directory domínios e relações de confiança. 

Por exemplo, você pode querer adicionar labs.contoso.com e fazer com que os UPNs e email dos usuários reflitam isso. Em seguida, eles se tornarão

username@labs.contoso.com.

>[!IMPORTANT]
> Se você estiver [alterando o sufixo em Active Directory](../fundamentals/add-custom-domain.md), deverá garantir que um nome de domínio personalizado correspondente tenha sido [adicionado e verificado no Azure ad](../fundamentals/add-custom-domain.md). 

![Uma captura de tela dos domínios verificados](./media/howto-troubleshoot-upn-changes/custom-domains.png)

### <a name="upns-in-azure-active-directory"></a>UPNs em Azure Active Directory

Os usuários entram no Azure AD com o valor em seu atributo userPrincipalName. 

Quando você usa o Azure AD em conjunto com sua Active Directory local, as contas de usuário são sincronizadas usando o serviço Azure AD Connect. Por padrão, o assistente de Azure AD Connect usa o atributo userPrincipalName do Active Directory local como o UPN no Azure AD. Você pode alterá-lo para um atributo diferente em uma instalação personalizada.

É importante que você tenha um processo definido ao atualizar um UPN (nome principal de usuário) de um único usuário ou para toda a organização. 

Consulte os problemas conhecidos e as soluções alternativas neste documento.

Quando você estiver sincronizando contas de usuário de Active Directory para o Azure AD, verifique se os UPNs no Active Directory mapear para domínios verificados no Azure AD.

![Captura de tela que mostra exemplos de UPNs mapeados para os domínios de D do Azure verificados.](./media/howto-troubleshoot-upn-changes/verified-domains.png)

Se o valor do atributo userPrincipalName não corresponder a um domínio verificado no Azure AD, o processo de sincronização substituirá o sufixo por um valor default. onmicrosoft.com.


### <a name="roll-out-bulk-upn-changes"></a>Alterações de UPN em massa de distribuição

Siga as [práticas recomendadas para um piloto](../fundamentals/active-directory-deployment-plans.md) para alterações de UPN em massa. Tenha também um plano de reversão testado para reverter UPNs se encontrar problemas que não podem ser resolvidos rapidamente. Depois que o piloto estiver em execução, você poderá começar a direcionar conjuntos pequenos de usuários com várias funções organizacionais e seus conjuntos específicos de aplicativos ou dispositivos.

Passar pelo primeiro subconjunto de usuários dará a você uma boa ideia do que os usuários devem esperar como parte da alteração. Inclua essas informações em suas comunicações de usuário.

Crie um procedimento definido para alterar os UPNs em usuários individuais como parte das operações normais. É recomendável ter um procedimento testado que inclua documentação sobre problemas conhecidos e soluções alternativas.

As seções a seguir detalham possíveis problemas conhecidos e soluções alternativas quando os UPNs são alterados.

## <a name="apps-known-issues-and-workarounds"></a>Problemas conhecidos de aplicativos e soluções alternativas

Aplicativos [SaaS (software como serviço](https://azure.microsoft.com/overview/what-is-saas/) ) e LOB (linha de negócios) geralmente dependem de UPNs para localizar usuários e armazenar informações de perfil de usuário, incluindo funções. Os aplicativos que usam o [provisionamento just in time](../app-provisioning/user-provisioning.md) para criar um perfil de usuário quando os usuários entram no aplicativo pela primeira vez podem ser afetados por alterações de UPN.

**Problema conhecido**<br>
Alterar o UPN de um usuário pode interromper a relação entre o usuário do Azure AD e o perfil do usuário criado no aplicativo. Se o aplicativo usar o  [provisionamento just-in-time](../app-provisioning/user-provisioning.md), ele poderá criar um perfil de usuário totalmente novo. Isso exigirá que o administrador do aplicativo faça alterações manuais para corrigir essa relação.

**Solução alternativa**<br>
O [provisionamento automatizado de usuários do Azure ad](../app-provisioning/user-provisioning.md) permite criar, manter e remover automaticamente suas identidades de usuário em aplicativos de nuvem com suporte. A configuração do provisionamento automatizado de usuários em seus aplicativos atualiza automaticamente os UPNs nos aplicativos. Teste os aplicativos como parte da distribuição progressiva para validar que eles não são afetados por alterações de UPN.
Se você for um desenvolvedor, considere [Adicionar suporte a scim ao seu aplicativo](../app-provisioning/use-scim-to-provision-users-and-groups.md) para habilitar o provisionamento de usuário automático de Azure Active Directory. 

## <a name="managed-devices-known-issues-and-workarounds"></a>Problemas conhecidos de dispositivos gerenciados e soluções alternativas

Ao [trazer seus dispositivos para o Azure ad](../devices/overview.md), você maximiza a produtividade de seus usuários por meio de SSO (logon único) em sua nuvem e em seus recursos locais.

### <a name="azure-ad-joined-devices"></a>Dispositivos adicionados ao Azure AD

Os dispositivos [ingressados no Azure ad](../devices/concept-azure-ad-join.md) são adicionados diretamente ao Azure AD e permitem que os usuários entrem no dispositivo usando a identidade da organização.

**Problemas conhecidos** <br>
Os usuários podem enfrentar problemas de logon único com aplicativos que dependem do Azure AD para autenticação.

**Resolução** <br>
Os problemas mencionados nesta seção foram corrigidos no Windows 10 maio 2020 atualização (2004).

**Solução alternativa** <br>
Permita tempo suficiente para que a alteração de UPN seja sincronizada com o Azure AD. Depois de verificar se o novo UPN é refletido no portal do AD do Azure, peça ao usuário para selecionar o bloco "outro usuário" para entrar com seu novo UPN. Você também pode verificar por meio do [PowerShell](/powershell/module/azuread/get-azureaduser). Depois de entrar com seu novo UPN, as referências ao UPN antigo ainda podem aparecer na configuração do Windows "acesso corporativo ou de estudante".

![Captura de tela dos domínios verificados](./media/howto-troubleshoot-upn-changes/other-user.png)


### <a name="hybrid-azure-ad-joined-devices"></a>Dispositivos ingressado no Azure AD Híbrido

Dispositivos [ingressados no Azure ad híbrido](../devices/concept-azure-ad-join-hybrid.md) são associados a Active Directory e ao Azure AD. Você pode implementar o ingresso no Azure AD híbrido se o seu ambiente tiver uma superfície de Active Directory local e você também quiser se beneficiar dos recursos fornecidos pelo Azure AD.

**Problemas conhecidos** 

Dispositivos ingressados no Azure AD híbrido do Windows 10 provavelmente sofrerão reinicializações inesperadas e problemas de acesso.

Se os usuários entrarem no Windows antes que o novo UPN tenha sido sincronizado com o Azure AD, ou continuar usando uma sessão existente do Windows, ele poderá enfrentar problemas de logon único com aplicativos que usam o Azure AD para autenticação se o acesso condicional tiver sido configurado para impor o uso de dispositivos ingressados híbridos para acessar recursos. 

Além disso, a seguinte mensagem será exibida, forçando uma reinicialização após um minuto. 

"Seu PC será reiniciado automaticamente em um minuto. O Windows encontrou um problema e precisa ser reiniciado. Você deve fechar esta mensagem agora e salvar seu trabalho ".

**Resolução** <br>
Os problemas mencionados nesta seção foram corrigidos no Windows 10 maio 2020 atualização (2004).

**Solução alternativa** 

O dispositivo deve ser retirado do Azure AD e reiniciado. Após a reinicialização, o dispositivo automaticamente ingressará no Azure AD novamente e o usuário deverá entrar usando o novo UPN selecionando o bloco "outro usuário". Para desassociar um dispositivo do Azure AD, execute o seguinte comando no prompt de comando:

**dsregcmd/Leave**

O usuário precisará se [inscrever novamente](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-whfb-provision) no Windows Hello para empresas se ele estiver sendo usado. Os dispositivos Windows 7 e 8,1 não são afetados por esse problema após as alterações de UPN.


## <a name="microsoft-authenticator-known-issues-and-workarounds"></a>Microsoft Authenticator problemas conhecidos e soluções alternativas

Sua organização pode exigir o uso do [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) para entrar e acessar dados e aplicativos organizacionais. Embora um nome de usuário possa aparecer no aplicativo, a conta não é configurada para funcionar como um método de verificação até que o usuário conclua o processo de registro.

O [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) tem quatro funções principais:

* Autenticação multifator por meio de uma notificação por Push ou código de verificação

* Agir como um agente de autenticação em dispositivos iOS e Android para fornecer logon único para aplicativos que usam [autenticação orientada](../develop/msal-android-single-sign-on.md)

* Registro de dispositivo (também conhecido como Workplace Join) para o Azure AD, que é um requisito para outros recursos como Proteção de Aplicativo do Intune e dispositivo registro/gerenciamento,

* Entrada no telefone, que requer MFA e registro de dispositivo.

### <a name="multi-factor-authentication-with-android-devices"></a>Autenticação multifator com dispositivos Android

O aplicativo Microsoft Authenticator oferece uma opção de verificação fora de banda. Em vez de fazer uma chamada telefônica automatizada ou SMS para o usuário durante a entrada, a [MFA (autenticação multifator)](../authentication/concept-mfa-howitworks.md) envia uma notificação para o aplicativo Microsoft Authenticator no smartphone ou Tablet do usuário. O usuário simplesmente toca em aprovar (ou insere um PIN ou biométrica e toca em "autenticar") no aplicativo para concluir sua entrada.

**Problemas conhecidos** 

Quando você altera o UPN de um usuário, o UPN antigo ainda é exibido na conta de usuário e uma notificação pode não ser recebida. Os [códigos de verificação](../user-help/user-help-auth-app-faq.md) continuam a funcionar.

**Solução alternativa**

Se uma notificação for recebida, instrua o usuário a ignorar a notificação, abrir o aplicativo autenticador, tocar na opção "verificar notificações" e aprovar o prompt do MFA. Depois disso, o UPN exibido na conta será atualizado. Observe que o UPN atualizado pode ser exibido como uma nova conta, devido a outras funcionalidades de autenticador sendo usadas. Para obter mais informações, consulte os problemas conhecidos adicionais neste artigo.

### <a name="brokered-authentication"></a>Autenticação orientada

Em agentes Android e iOS, como Microsoft Authenticator habilitar:

* SSO (logon único) – seus usuários não precisarão entrar em cada aplicativo.

* Identificação do dispositivo-o agente acessa o certificado do dispositivo criado no dispositivo quando ele foi ingressado no local de trabalho.

* Verificação de identificação do aplicativo – quando um aplicativo chama o agente, ele passa sua URL de redirecionamento e o agente o verifica.

Além disso, ele permite que os aplicativos participem de recursos mais avançados, como o [acesso condicional](../conditional-access/index.yml), e oferece suporte a [cenários de Microsoft Intune](../develop/msal-net-use-brokers-with-xamarin-apps.md).

**Problemas conhecidos**<br>
O usuário recebe prompts de autenticação mais interativos em novos aplicativos que usam a entrada assistida pelo agente devido a uma incompatibilidade entre o login_hint passado pelo aplicativo e o UPN armazenado no agente.

**Solução alternativa** <br> O usuário precisa remover manualmente a conta de Microsoft Authenticator e iniciar uma nova entrada de um aplicativo assistido por agente. A conta será adicionada automaticamente após a autenticação inicial.

### <a name="device-registration"></a>Registro de dispositivos

O aplicativo Microsoft Authenticator é responsável por registrar o dispositivo no Azure AD. O registro de dispositivo permite que o dispositivo seja autenticado no Azure AD e é um requisito para os seguintes cenários:

* Proteção de Aplicativo do Intune

* Registro de dispositivo do Intune

* Entrada pelo telefone

**Problemas conhecidos**<br>
Quando você altera o UPN, uma nova conta com o novo UPN aparece listada no aplicativo Microsoft Authenticator, enquanto a conta com o UPN antigo ainda está listada. Além disso, o UPN antigo é exibido na seção de registro do dispositivo nas configurações do aplicativo. Não há nenhuma alteração na funcionalidade normal do registro de dispositivos ou nos cenários dependentes.

**Solução alternativa** <br> Para remover todas as referências ao UPN antigo no aplicativo Microsoft Authenticator, instrua o usuário a remover manualmente as contas antigas e novas de Microsoft Authenticator, registrar novamente para o MFA e ingressar novamente no dispositivo.

### <a name="phone-sign-in"></a>Entrada pelo telefone

A entrada pelo telefone permite que os usuários entrem no Azure AD sem uma senha. Para habilitar a entrada pelo telefone, o usuário precisa se registrar no MFA usando o aplicativo autenticador e, em seguida, habilitar a entrada pelo telefone diretamente no autenticador. Como parte da configuração, o dispositivo é registrado com o Azure AD.

**Problemas conhecidos** <br>
Os usuários não podem usar a entrada pelo telefone porque não recebem nenhuma notificação. Se o usuário tocar em verificar se há notificações, ele receberá um erro.

**Solução alternativa**<br>
O usuário precisa selecionar o menu suspenso na conta habilitada para entrada pelo telefone e selecionar desabilitar entrada pelo telefone. Se desejar, a entrada pelo telefone poderá ser habilitada novamente.

## <a name="security-key-fido2-known-issues-and-workarounds"></a>Problemas conhecidos e soluções alternativas da chave de segurança (FIDO2)

**Problemas conhecidos** <br>
Quando vários usuários são registrados na mesma chave, a tela de entrada mostra uma página de seleção de conta na qual o UPN antigo é exibido. As entradas que usam chaves de segurança não são afetadas pelas alterações de UPN.  

**Solução alternativa**<br>
Para remover referências a UPNs antigos, os usuários devem [redefinir a chave de segurança e registrá-lo novamente](../authentication/howto-authentication-passwordless-security-key.md#known-issues).

## <a name="onedrive-known-issues-and-workarounds"></a>Problemas conhecidos e soluções alternativas do OneDrive

Os usuários do OneDrive são conhecidos por experimentarem problemas após as alterações de UPN. Para obter mais informações, consulte [como as alterações de UPN afetam a URL do onedrive e os recursos do onedrive](/onedrive/upn-changes).

## <a name="next-steps"></a>Próximas etapas

Consulte estes recursos:
* [Azure AD Connect: conceitos de design](./plan-connect-design-concepts.md)

* [Preenchimento de UserPrincipalName do Azure AD](./plan-connect-userprincipalname.md)

* [Tokens de ID da plataforma Microsoft Identity](../develop/id-tokens.md)
