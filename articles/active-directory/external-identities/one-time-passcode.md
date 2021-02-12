---
title: Autenticação de senha avulsa para usuários convidados de B2B - Azure AD
description: Como usar a senha avulsa por email para autenticar os usuários convidados de B2B sem a necessidade de uma conta Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/18/2020
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9a0668b3ea651d129dc076e5f2247e38f5ab7d0
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725488"
---
# <a name="email-one-time-passcode-authentication"></a>Autenticação de senha de uso único de email

Este artigo descreve como habilitar a autenticação de senha de uso único de email para usuários de convidado B2B. O recurso de senha de email único autentica os usuários convidados B2B quando eles não podem ser autenticados por outros meios como o Azure AD, um conta Microsoft (MSA) ou Google Federation. Com a autenticação por senha avulsa, não é necessário criar uma conta Microsoft. Quando o usuário convidado resgata um convite ou acessa um recurso compartilhado, ele pode solicitar um código temporário, que é enviado para seu endereço de email. Em seguida, ele digita esse código para continuar o processo de entrada.

![Diagrama de visão geral de senha de email de uso único](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> A **partir de março de 2021**, o recurso de senha de uso único de email será ativado para todos os locatários existentes e habilitado por padrão para novos locatários. Se não quiser permitir que esse recurso seja ativado automaticamente, você poderá desabilitá-lo. Consulte [Desabilitar senha de uso único de email](#disable-email-one-time-passcode) abaixo.

> [!NOTE]
> Os usuários de senha descartável devem entrar usando um link que inclui o contexto do locatário (por exemplo, `https://myapps.microsoft.com/?tenantid=<tenant id>`, `https://portal.azure.com/<tenant id>` ou, no caso de um domínio verificado, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Links diretos para aplicativos e recursos também funcionam desde que incluam o contexto do locatário. No momento, os usuários convidados não conseguem entrar usando pontos de extremidade sem contexto do locatário. Por exemplo, usando `https://myapps.microsoft.com` , `https://portal.azure.com` resultará em um erro.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Experiência do usuário para usuários convidados com senha avulsa

Quando o recurso de senha de uso único de email estiver habilitado, os usuários convidados [que atenderem a determinadas condições](#when-does-a-guest-user-get-a-one-time-passcode) usarão a autenticação de senha única. Os usuários convidados que resgataram um convite antes de enviar uma senha de uso único por email continuarão a usar o mesmo método de autenticação.

Com a autenticação por senha avulsa, o usuário convidado pode resgatar seu convite clicando em um link direto ou usando o email de convite. Em ambos os casos, uma mensagem no navegador indica que um código será enviado ao endereço de email do usuário convidado. O usuário convidado seleciona **Enviar código**:

   ![Captura de tela mostrando o botão Enviar código](media/one-time-passcode/otp-send-code.png)

Uma senha é enviada ao endereço de email do usuário. O usuário recupera a senha de email e insere-a na janela do navegador:

   ![Captura de tela mostrando a página Inserir código](media/one-time-passcode/otp-enter-code.png)

O usuário convidado está agora autenticado e pode ver o recurso compartilhado ou continuar o processo de entrada.

> [!NOTE]
> Senhas de uso único são válidas por 30 minutos. Após 30 minutos, essa senha avulsa específica não é mais válida e o usuário precisa solicitar uma nova. Sessões de usuário expiram após 24 horas. Após esse tempo, o usuário convidado recebe uma nova senha quando acessa o recurso. A expiração de sessão fornece maior segurança, especialmente quando um usuário convidado deixa a empresa ou não precisa mais de acesso.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>Quando um usuário convidado obtém uma senha avulsa?

Quando um usuário convidado resgata um convite ou usa um link para um recurso que foi compartilhado consigo, ele recebe uma senha avulsa se:

- Ele não tem uma conta do Azure AD
- Ele não tem uma conta Microsoft
- O locatário que convida não configurou a federação do Google para usuários @gmail.com e @googlemail.com

No momento do convite, não há nenhuma indicação de que o usuário que você está convidando usará a autenticação de senha avulsa. Mas quando o usuário convidado entrar, a autenticação de senha avulsa será o método de fallback se nenhum outro método de autenticação puder ser usado.

Você pode ver se um usuário convidado é autenticado usando senhas de uso único, exibindo a propriedade **Source** nos detalhes do usuário. Na portal do Azure, acesse **Azure Active Directory**  >  **usuários** e, em seguida, selecione o usuário para abrir a página de detalhes.

![Captura de tela mostrando um usuário de senha avulsa com o valor Origem OTP](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> Quando um usuário resgatar uma senha avulsa e, posteriormente, obtém uma MSA, conta do Azure AD ou outra conta federada, ele continuará a ser autenticado usando uma senha avulsa. Se quiser atualizar o método de autenticação do usuário, você poderá excluir a conta de usuário convidado dele e convidá-lo novamente.

### <a name="example"></a>Exemplo

O usuário convidado teri@gmail.com é convidado para a Fabrikam, que não tem a federação do Google configurada. Teri não tem um conta Microsoft. Receberemos uma senha avulsa para autenticação.

## <a name="disable-email-one-time-passcode"></a>Desabilitar senha de uso único de email

A partir de março de 2021, o recurso de senha de uso único de email será ativado para todos os locatários existentes e habilitado por padrão para novos locatários. Nesse momento, a Microsoft não dará mais suporte ao resgate de convites criando contas e locatários não gerenciados ("viral" ou "Just-in-time") do Azure AD para cenários de colaboração B2B. Estamos habilitando o recurso de senha de uso único de email porque ele fornece um método de autenticação de fallback contínuo para seus usuários convidados. No entanto, você tem a opção de desabilitar esse recurso se optar por não usá-lo.

> [!NOTE]
>
> Se o recurso de senha de uso único de email tiver sido habilitado em seu locatário e você desligá-lo, todos os usuários convidados que tiverem resgatado uma senha de uso único não poderão entrar. Você pode excluir o usuário convidado e convidá-los novamente para que eles possam entrar novamente usando outro método de autenticação.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>Para desabilitar o recurso de senha de uso único de email

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do Azure AD.

2. No painel de navegação, selecione **Azure Active Directory**.

3. Selecione **Identidades Externas** > **Configurações de colaboração externa**.

4. Em **senha de uso único de email para convidados**, selecione **desabilitar a senha de uso único de email para convidados**.

    ![Configurações de senha de uso único de email](media/one-time-passcode/otp-admin-settings.png)

   > [!NOTE]
   > Se você vir a seguinte alternância em vez das opções mostradas acima, isso significa que você já habilitou, desabilitou ou aceitou a visualização do recurso. Selecione **não** para desabilitar o recurso.
   >
   >![Habilitar o email de senha de uso único aceito](media/delegate-invitations/enable-email-otp-opted-in.png)

5. Clique em **Salvar**.

## <a name="note-for-public-preview-customers"></a>Observação para clientes de visualização pública

Se você tiver optado anteriormente pela visualização pública de senha de uso único de email, a data de março de 2021 para a habilitação automática de recursos não se aplicará a você, de modo que seus processos comerciais relacionados não serão afetados. Além disso, na portal do Azure, sob a **senha do email de uso único para as propriedades de convidados** , você não verá a opção de **habilitar automaticamente o email de senha de uso único para convidados em março de 2021**. Em vez disso, você verá a seguinte opção **Sim** ou **não** alternar:

![Habilitar o email de senha de uso único aceito](media/delegate-invitations/enable-email-otp-opted-in.png)

No entanto, se você preferir recusar o recurso e permitir que ele seja habilitado automaticamente em março de 2021, você poderá reverter para as configurações padrão usando o tipo de recurso de [configuração do método de autenticação de email](/graph/api/resources/emailauthenticationmethodconfiguration)Microsoft Graph API. Depois de reverter para as configurações padrão, as opções a seguir estarão disponíveis em **senha de uso único de email para convidados**:

- **Habilite automaticamente a senha de uso único de email para convidados em março de 2021**. Os Se o recurso de senha de email de uso único já não estiver habilitado para seu locatário, ele será automaticamente ativado em março de 2021. Nenhuma ação adicional será necessária se você quiser habilitar o recurso no momento. Se você já tiver habilitado ou desabilitado o recurso, essa opção não estará disponível.

- **Habilite o email de senha de uso único para convidados em vigor agora**. Ativa o recurso de senha de uso único de email para seu locatário.

- **Desabilite a senha de uso único de email para convidados**. Desativa o recurso de senha de uso único de email para seu locatário e impede que o recurso seja ligado em março de 2021.