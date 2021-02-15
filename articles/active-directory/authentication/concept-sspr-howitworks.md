---
title: Aprofundamento de redefinição de senha de autoatendimento – Azure Active Directory
description: Como a redefinição de senha de autoatendimento funciona
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/07/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 496a8dba9b9ea7fb82ad9016479154d6a61cb767
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98703268"
---
# <a name="how-it-works-azure-ad-self-service-password-reset"></a>Como funciona: Redefinição de senha de autoatendimento do Azure AD

A SSPR (redefinição de senha por autoatendimento) do Microsoft Azure AD (Active Directory) dá aos usuários a capacidade de alterar ou redefinir a senha, sem envolvimento do administrador ou do suporte técnico. Se a conta de um usuário estiver bloqueada ou se ele esquecer a senha, ele poderá seguir os avisos para desbloquear a si mesmo e voltar ao trabalho. Essa capacidade reduz as chamadas de suporte técnico e a perda de produtividade quando um usuário não consegue entrar no dispositivo ou em um aplicativo.

> [!IMPORTANT]
> Este artigo conceitual explica como administrador como a redefinição de senha de autoatendimento funciona. Se você for um usuário final já registrado para redefinição de senha de autoatendimento e precisar voltar à sua conta, vá para [https://aka.ms/sspr](https://aka.ms/sspr) .
>
> Se sua equipe de TI não tiver habilitado a capacidade de redefinir sua própria senha, entre em contato com sua assistência técnica para obter mais assistência.

## <a name="how-does-the-password-reset-process-work"></a>Como funciona o processo de redefinição de senha?

Um usuário pode redefinir ou alterar sua senha usando o [portal do SSPR](https://aka.ms/sspr). Eles devem primeiro registrar seus métodos de autenticação desejados. Quando um usuário acessa o portal do SSPR, a plataforma do Azure considera os seguintes fatores:

* Como a página deve ser localizada?
* A conta de usuário é válida?
* A qual organização o usuário pertence?
* Onde a senha do usuário é gerenciada?
* O usuário é licenciado para usar o recurso?

Quando um usuário seleciona o link **não consegue acessar sua conta** de um aplicativo ou uma página, ou vai diretamente para [https://aka.ms/sspr](https://passwordreset.microsoftonline.com) o, o idioma usado no portal do SSPR é baseado nas seguintes opções:

* Por padrão, a localidade do navegador é usada para exibir o SSPR no idioma apropriado. A experiência de redefinição de senha é localizada nos mesmos idiomas [aos quais Microsoft 365 dá suporte](https://support.microsoft.com/office/what-languages-is-office-available-in-26d30382-9fba-45dd-bf55-02ab03e2a7ec).
* Se você quiser vincular ao SSPR em um idioma localizado específico, acrescente `?mkt=` ao final da URL de redefinição de senha junto com a localidade necessária.
    * Por exemplo, para especificar a localidade *es-US* espanhol, use `?mkt=es-us`  -  [https://passwordreset.microsoftonline.com/?mkt=es-us](https://passwordreset.microsoftonline.com/?mkt=es-us) .

Depois que o portal do SSPR for exibido no idioma necessário, será solicitado que o usuário insira uma ID de usuário e passe um captcha. O Azure AD agora verifica se o usuário é capaz de usar o SSPR fazendo as seguintes verificações:

* Verifica se o usuário tem o SSPR habilitado e é atribuído a uma licença do Azure AD.
  * Se o usuário não estiver habilitado para SSPR ou não tiver uma licença atribuída, o usuário será solicitado a entrar em contato com o administrador para redefinir sua senha.
* Verifica se o usuário possui os métodos de autenticação corretos definidos em sua conta, de acordo com a política do administrador.
  * Se a política exigir apenas um método, verifique se o usuário tem os dados apropriados definidos para pelo menos um dos métodos de autenticação habilitados pela política de administrador.
    * Se os métodos de autenticação não estiverem configurados, é aconselhável que o usuário entre em contato com o administrador para redefinir a senha.
  * Se a política exigir dois métodos, verifique se o usuário tem os dados apropriados definidos para pelo menos dois dos métodos de autenticação habilitados pela política de administrador.
    * Se os métodos de autenticação não estiverem configurados, é aconselhável que o usuário entre em contato com o administrador para redefinir a senha.
  * Se uma função de administrador do Azure for atribuída ao usuário, a política de senha forte de duas portas será imposta. Para obter mais informações, confira [Diferenças da política de redefinição de senha de administrador](concept-sspr-policy.md#administrator-reset-policy-differences).
* Verifica se a senha do usuário é gerenciada localmente, como se o locatário do Azure AD estiver usando a autenticação federada, de passagem ou de hash de senha:
  * Se o Write-back SSPR estiver configurado e a senha do usuário for gerenciada localmente, o usuário poderá continuar a autenticar e redefinir sua senha.
  * Se o Write-back SSPR não for implantado e a senha do usuário for gerenciada localmente, o usuário será solicitado a entrar em contato com o administrador para redefinir sua senha.

Se todas as verificações anteriores forem concluídas com êxito, o usuário será guiado pelo processo de redefinição ou alteração de senha.

> [!NOTE]
> O SSPR pode enviar notificações por email aos usuários como parte do processo de redefinição de senha. Esses emails são enviados usando o serviço de retransmissão SMTP, que opera em um modo ativo-ativo em várias regiões.
>
> Os serviços de retransmissão SMTP recebem e processam o corpo do email, mas não os armazenam. O corpo do email SSPR que pode potencialmente conter informações fornecidas pelo cliente não é armazenado nos logs do serviço de retransmissão SMTP. Os logs contêm apenas metadados de protocolo.

Para começar a usar o SSPR, conclua o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: habilitar redefinição de senha de autoatendimento (SSPR)](tutorial-enable-sspr.md)


## <a name="require-users-to-register-when-they-sign-in"></a>Exigir que os usuários se cadastram ao entrarem

Você pode habilitar a opção para exigir que um usuário conclua o registro do SSPR se eles entrarem em qualquer aplicativo usando o Azure AD. Esse fluxo de trabalho inclui os seguintes aplicativos:

* Microsoft 365
* Portal do Azure
* Painel de acesso
* Aplicativos federados
* Aplicativos personalizados que usam o Azure AD

Quando você não exige registro, os usuários não são solicitados durante a entrada, mas podem registrá-los manualmente. Os usuários podem visitar [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) ou selecionar o link **registrar para redefinição de senha** na guia **perfil** no painel de acesso.

![Opções de registro para SSPR no portal do Azure][Registration]

> [!NOTE]
> Os usuários podem ignorar o portal de registro do SSPR selecionando **Cancelar** ou fechando a janela. No entanto, eles serão solicitados a registrar cada vez que entrarem até que concluam seu registro.
>
> Essa interrupção para registrar para SSPR não interromperá a conexão do usuário se já tiver entrado.

## <a name="reconfirm-authentication-information"></a>Reconfirmar informações de autenticação

Para certificar-se de que os métodos de autenticação estejam corretos quando forem necessários para redefinir ou alterar sua senha, você pode exigir que os usuários confirmem suas informações registradas sobre a informação após um determinado período de tempo. Essa opção só estará disponível se você habilitar a opção **exigir que os usuários se registrem ao entrar** .

Os valores válidos para solicitar que um usuário confirme seus métodos registrados são de *0* a *730* dias. Definir esse valor como *0* significa que os usuários nunca serão solicitados a confirmar suas informações de autenticação.

## <a name="authentication-methods"></a>Métodos de autenticação

Quando um usuário é habilitado para SSPR, ele deve registrar pelo menos um método de autenticação. É altamente recomendável que você escolha dois ou mais métodos de autenticação para que os usuários tenham mais flexibilidade caso não possam acessar um método quando necessário. Para obter mais informações, consulte [o que são métodos de autenticação?](concept-authentication-methods.md).

Os seguintes métodos de autenticação estão disponíveis para SSPR:

* Notificação de aplicativo móvel
* Código do aplicativo móvel
* Email
* Telefone celular
* Telefone comercial
* Perguntas de segurança

Os usuários só podem redefinir sua senha se tiverem registrado um método de autenticação que o administrador habilitou.

> [!WARNING]
> As contas atribuídas às funções de *administrador* do Azure são necessárias para usar métodos, conforme definido na seção [diferenças de política de redefinição de administrador](concept-sspr-policy.md#administrator-reset-policy-differences).

![Seleção de métodos de autenticação no portal do Azure][Authentication]

### <a name="number-of-authentication-methods-required"></a>Quantidade necessária de métodos de autenticação

Você pode configurar o número de métodos de autenticação disponíveis que um usuário deve fornecer para redefinir ou desbloquear sua senha. Esse valor pode ser definido como *um* ou *dois*.

Os usuários podem, e devem, registrar vários métodos de autenticação. Novamente, é altamente recomendável que os usuários registrem dois ou mais métodos de autenticação para que tenham mais flexibilidade caso não possam acessar um método quando necessário.

Se um usuário não tiver o número mínimo de métodos necessários registrados ao tentar usar o SSPR, ele verá uma página de erro que os direciona para solicitar que um administrador Redefina sua senha. Tome cuidado se você aumentar o número de métodos necessários de um para dois se tiver usuários existentes registrados para SSPR e, em seguida, não puderem usar o recurso. Para obter mais informações, consulte a seção a seguir para [alterar os métodos de autenticação](#change-authentication-methods).

#### <a name="mobile-app-and-sspr"></a>Aplicativo móvel e SSPR

Ao usar um aplicativo móvel como um método para redefinição de senha, como o aplicativo Microsoft Authenticator, as seguintes considerações se aplicam:

* Quando os administradores exigem que um método seja usado para redefinir uma senha, o código de verificação é a única opção disponível.
* Quando os administradores exigem dois métodos para redefinir uma senha, os usuários são capazes de usar o código de notificação **ou** de verificação Além de quaisquer outros métodos habilitados.

| Número de métodos necessários para redefinir | Um | Dois |
| :---: | :---: | :---: |
| Recursos de aplicativos para dispositivos móveis disponíveis | Código | Código ou notificação |

Os usuários não têm a opção de registrar seu aplicativo móvel ao se registrar para redefinição de senha de autoatendimento do [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) . Os usuários podem registrar seu aplicativo móvel em [https://aka.ms/mfasetup](https://aka.ms/mfasetup) ou no registro de informações de segurança combinadas em [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

> [!IMPORTANT]
> O aplicativo autenticador não pode ser selecionado como o único método de autenticação quando apenas um método é necessário. Da mesma forma, o aplicativo autenticador e apenas um método adicional não podem ser selecionados ao exigir dois métodos.
>
> Ao configurar políticas de SSPR que incluem o aplicativo autenticador como um método, pelo menos um método adicional deve ser selecionado quando um método é necessário e pelo menos dois métodos adicionais devem ser selecionados quando a configuração de dois métodos é necessária.
>
> Esse requisito é porque a atual experiência de registro de SSPR não inclui a opção de registrar o aplicativo autenticador. A opção para registrar o aplicativo autenticador está incluída na nova [experiência de registro combinada](./concept-registration-mfa-sspr-combined.md).
>
> Permitir políticas que usam apenas o aplicativo autenticador (quando um método é necessário) ou o aplicativo autenticador e apenas um método adicional (quando dois métodos são necessários) pode levar os usuários sendo impedidos de se registrarem para SSPR até que eles sejam configurados para usar a nova experiência de registro combinado.

### <a name="change-authentication-methods"></a>Alterar métodos de autenticação

Se você iniciar com uma política que tenha apenas um método de autenticação requerido para reiniciar ou desbloquear registrado e você alterar esse número para dois métodos, o que acontece?

| Número de métodos registrados | Número de métodos necessários | Result |
| :---: | :---: | :---: |
| 1 ou mais | 1 | **Capaz** de redefinir ou desbloquear |
| 1 | 2 | **Incapaz** de redefinir ou desbloquear |
| 2 ou mais | 2 | **Capaz** de redefinir ou desbloquear |

Alterar os métodos de autenticação disponíveis também pode causar problemas para os usuários. Se você alterar os tipos de métodos de autenticação que um usuário pode usar, você poderá inadvertidamente impedir que os usuários sejam capazes de usar SSPR se eles não tiverem a quantidade mínima de dados disponíveis.

Considere o seguinte cenário de exemplo:

1. A política original é configurada com dois métodos de autenticação necessários. Somente o número de telefone comercial e as questões de segurança são utilizados.
1. O administrador altera a política para não usar perguntas de segurança, mas permite o uso de telefone celular e um email alternativo.
1. Os usuários sem os campos telefone celular ou email alternativo preenchidos agora não podem redefinir suas senhas.

## <a name="notifications"></a>Notificações

Para melhorar a conscientização dos eventos de senha, o SSPR permite configurar notificações para os usuários e os administradores de identidade.

### <a name="notify-users-on-password-resets"></a>Notificar os usuários de redefinições de senha

Se essa opção for definida como **Sim**, os usuários que redefinirem sua senha receberão um email notificando-os de que sua senha foi alterada. O email é enviado por meio do portal do SSPR para seus endereços de email primários e alternativos que são armazenados no Azure AD. Ninguém mais é notificado sobre o evento de redefinição.

### <a name="notify-all-admins-when-other-admins-reset-their-passwords"></a>Notificar todos os administradores quando outros administradores redefinirem suas senhas

Se essa opção for definida como **Sim**, todos os outros administradores do Azure receberão um email para seu endereço de email principal armazenado no Azure AD. O email notifica que outro administrador alterou sua senha utilizando a SSPR.

Considere o seguinte cenário de exemplo:

* Há quatro administradores em um ambiente.
* *O administrador A* redefine sua senha usando SSPR.
* Os administradores *B*, *C* e *D* recebem um email alertando-os sobre a redefinição de senha.

## <a name="on-premises-integration"></a>Integração local

Se você tiver um ambiente híbrido, poderá configurar Azure AD Connect para gravar eventos de alteração de senha do Azure AD em um diretório local.

![A validação do write-back de senha está habilitada e funcionando][Writeback]

O Azure AD verifica sua conectividade híbrida atual e fornece uma das seguintes mensagens no portal do Azure:

* Seu cliente de write-back local está em execução.
* O Azure Active Directory está online e conectado ao seu cliente de write-back local. No entanto, parece que a versão instalada do Azure AD Connect está desatualizada. Considere [Atualizar o Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) para garantir que você tenha os recursos de conectividade e correções de bugs importantes mais recentes.
* Infelizmente, não é possível verificar seu status do cliente de write-back local porque a versão instalada do Azure AD Connect está desatualizada. [Atualize o Azure AD Connect](../hybrid/how-to-upgrade-previous-version.md) para poder verificar o status da conexão.
* Infelizmente, parece que neste momento não é possível conectarmos ao seu cliente de write-back local. [Solucionar problemas do Azure AD Connect](./troubleshoot-sspr-writeback.md) para restaurar a conexão.
* Infelizmente, não podemos nos conectar ao seu cliente de write-back local porque o write-back de senha não foi configurado corretamente. [Configurar o write-back de senha](./tutorial-enable-sspr-writeback.md) para restaurar a conexão.
* Infelizmente, parece que neste momento não é possível conectarmos ao seu cliente de write-back local. Isso pode ocorrer devido a problemas temporários em nossa extremidade. Se o problema persistir, consulte [Solucionar problemas o Azure AD Connect](./troubleshoot-sspr-writeback.md) para restaurar a conexão.

Para começar a usar o write-back da SSPR, conclua o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Habilitar a redefinição de senha self-service (SSPR)](./tutorial-enable-sspr-writeback.md)

### <a name="write-back-passwords-to-your-on-premises-directory"></a>Write-back de senhas para o diretório local

Você pode habilitar o Write-back de senha usando o portal do Azure. Você também pode desabilitar temporariamente o Write-back de senha sem precisar reconfigurar Azure AD Connect.

* Se a opção for definida como **Sim**, o Write-back será habilitado. Os usuários federados, de autenticação de passagem ou de hash de senha sincronizados são capazes de redefinir suas senhas.
* Se a opção for definida como **não**, o Write-back será desabilitado. Os usuários federados, de autenticação de passagem ou de hash de senha sincronizados não podem redefinir suas senhas.

### <a name="allow-users-to-unlock-accounts-without-resetting-their-password"></a>Permitir aos usuários desbloquear contas sem redefinir a senha

Por padrão, o Azure Active Directory desbloqueia contas quando ele executa uma redefinição de senha. Para fornecer flexibilidade, você pode optar por permitir que os usuários desbloqueiem suas contas locais sem precisar redefinir sua senha. Use essa configuração para separar essas duas operações.

* Se definido como **Sim**, os usuários recebem a opção de redefinir sua senha e desbloquear a conta ou desbloquear sua conta sem precisar redefinir a senha.
* Se definido como **não**, os usuários só poderão executar uma operação combinada de redefinição de senha e de desbloqueio de conta.

### <a name="on-premises-active-directory-password-filters"></a>Filtros de senha do Active Directory local

SSPR executa o equivalente a uma redefinição de senha iniciada pelo administrador em Active Directory. Se você usar um filtro de senha de terceiros para impor regras de senha personalizadas e precisar que esse filtro de senha seja verificado durante a redefinição de senha de autoatendimento do Azure AD, verifique se a solução de filtro de senha de terceiros está configurada para ser aplicada ao cenário de redefinição de senha de administrador. A [proteção de senha do Azure ad para Active Directory Domain Services](concept-password-ban-bad-on-premises.md) é suportada por padrão.

## <a name="password-reset-for-b2b-users"></a>Redefinição de senha para usuários B2B

A reinicialização e a mudança de senha são totalmente suportadas em todas as configurações B2B (entre empresas). A reinicialização da senha do usuário B2B tem suporte nos três casos a seguir:

* **Usuários de uma organização parceira com um locatário existente do Azure ad**: se a organização com a qual você se parceiro tiver um locatário existente do AD do Azure, respeitaremos quaisquer políticas de redefinição de senha habilitadas nesse locatário. Para que a reinicialização da senha funcione, a organização parceira precisa ter certeza de que a SSPR do Azure Active Directory está habilitada. Não há nenhum custo adicional para Microsoft 365 clientes.
* **Usuários que se inscrevem por meio** de inscrição de autoatendimento: se a organização com a qual você se parceiro usou o recurso de [inscrição de autoatendimento](../enterprise-users/directory-self-service-signup.md) para entrar em um locatário, permitimos que eles redefinam a senha com o email que eles registraram.
* **Usuários B2B**: quaisquer novos usuários B2B criados usando os novos [recursos B2B do Azure ad](../external-identities/what-is-b2b.md) também podem redefinir suas senhas com o email que eles registraram durante o processo de convite.

Para testar este cenário, acesse https://passwordreset.microsoftonline.com com um desses usuários parceiros. Se eles possuem um email alternativo ou um email de autenticação definido, a redefinição de senha funcionará como esperado.

> [!NOTE]
> Contas da Microsoft que receberam acesso de convidado ao seu locatário do Azure AD, como as de Hotmail.com, Outlook.com ou outros endereços de email pessoais, não são capazes de usar o SSPR do Azure AD. É necessário que definam a senha, utilizando as informações localizadas no artigo [Quando não for possível entrar na sua conta da Microsoft](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## <a name="next-steps"></a>Próximas etapas

Para começar a usar o SSPR, conclua o seguinte tutorial:

> [!div class="nextstepaction"]
> [Tutorial: habilitar redefinição de senha de autoatendimento (SSPR)](tutorial-enable-sspr.md)

Os artigos a seguir fornecem informações adicionais sobre a redefinição de senha através do Azure Active Directory:

[Authentication]: ./media/concept-sspr-howitworks/manage-authentication-methods-for-password-reset.png "Métodos de autenticação do Azure AD e quantidade necessária"
[Registration]: ./media/concept-sspr-howitworks/configure-registration-options.png "Configurar as opções de registro do SSPR no portal do Azure"
[Writeback]: ./media/concept-sspr-howitworks/on-premises-integration.png "Integração local para SSPR no portal do Azure"
