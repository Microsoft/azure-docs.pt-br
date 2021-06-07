---
title: Autenticação de senha avulsa para usuários convidados de B2B - Azure AD
description: Como usar a senha avulsa por email para autenticar os usuários convidados de B2B sem a necessidade de uma conta Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30f22282b00a7ead2e19805f32d78338126e8087
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552743"
---
# <a name="email-one-time-passcode-authentication"></a>Autenticação de senha de uso único por email

Este artigo descreve como habilitar a Autenticação de senha de uso único por email para usuários B2B convidados. O recurso de senha de uso único por email autentica usuários B2B convidados quando eles não podem ser autenticados por outros meios, tais como o Azure AD, uma MSA (conta Microsoft) ou uma federação do Google. Com a autenticação por senha avulsa, não é necessário criar uma conta Microsoft. Quando o usuário convidado resgata um convite ou acessa um recurso compartilhado, ele pode solicitar um código temporário, que é enviado para seu endereço de email. Em seguida, ele digita esse código para continuar o processo de entrada.

![Diagrama de visão geral de senha de uso único por email](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> - **A partir de outubro de 2021**, o recurso de senha de uso único por email será ativado para todos os locatários atuais e habilitado por padrão para os novos locatários. Se não quiser permitir que esse recurso seja ativado automaticamente, você poderá desabilitá-lo. Confira [Desabilitar senha de uso único por email](#disable-email-one-time-passcode), abaixo.
> - As configurações de senha de uso único por email foram movidas no portal do Azure de **Configurações de colaboração externa** para **Todos os provedores de identidade**.

> [!NOTE]
> Os usuários de senha descartável devem entrar usando um link que inclui o contexto do locatário (por exemplo, `https://myapps.microsoft.com/?tenantid=<tenant id>`, `https://portal.azure.com/<tenant id>` ou, no caso de um domínio verificado, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Links diretos para aplicativos e recursos também funcionam desde que incluam o contexto do locatário. No momento, os usuários convidados não conseguem entrar usando pontos de extremidade sem contexto do locatário. Por exemplo, usar `https://myapps.microsoft.com` ou `https://portal.azure.com` resultará em um erro.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Experiência do usuário para usuários convidados com senha avulsa

Quando o recurso de senha de uso único por email estiver habilitado, os usuários convidados [que atenderem a determinadas condições](#when-does-a-guest-user-get-a-one-time-passcode) usarão a autenticação por senha de uso único. Os usuários convidados que resgataram um convite antes da habilitação da senha de uso único por email continuarão a usar o mesmo método de autenticação.

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

Você pode identificar se um usuário convidado é autenticado usando senhas de uso único visualizando a propriedade **Fonte** nos detalhes do usuário. No portal do Azure, acesse **Azure Active Directory** > **Usuários** e selecione o usuário cuja página de detalhes deseja abrir.

![Captura de tela mostrando um usuário de senha avulsa com o valor Origem OTP](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> Quando um usuário resgatar uma senha avulsa e, posteriormente, obtém uma MSA, conta do Azure AD ou outra conta federada, ele continuará a ser autenticado usando uma senha avulsa. Se quiser atualizar o método de autenticação do usuário, você poderá [redefinir seu status de resgate](reset-redemption-status.md).

### <a name="example&quot;></a>Exemplo

O usuário convidado teri@gmail.com é convidado para a Fabrikam, que não tem a federação do Google configurada. Teri não tem uma conta Microsoft. Receberemos uma senha avulsa para autenticação.

## <a name=&quot;disable-email-one-time-passcode&quot;></a>Desabilitar a senha de uso único por email

A partir de outubro de 2021, o recurso de senha de uso único por email será ativado para todos os locatários atuais e habilitado por padrão para os novos locatários. Neste período, a Microsoft não dará mais suporte para o resgate de convites criando contas e locatários do Azure AD não gerenciados (&quot;virais&quot; ou &quot;just-in-time") para cenários de colaboração B2B. Estamos habilitando o recurso de senha de uso único por email por ser um método de autenticação de reserva contínuo para os usuários convidados. No entanto, você tem a opção de desabilitar o recurso caso não deseje usá-lo.

> [!NOTE]
>
> Se o recurso de senha de uso único por email tiver sido habilitado em seu locatário e você desabilitá-lo, todos os usuários convidados que tiverem resgatado uma senha de uso único não poderão entrar. Você pode [redefinir o status de resgate deles](reset-redemption-status.md) para que eles possam entrar novamente usando outro método de autenticação.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>Para desabilitar o recurso de senha de uso único por email

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do Azure AD.

2. No painel de navegação, selecione **Azure Active Directory**.

3. Confira **Identidades Externas** > **Todos os provedores de identidade**.

4. Selecione **Senha de uso único por email** e, em seguida, escolha **Desabilitar senha de uso único por email para convidados**.

   > [!NOTE]
   > As configurações de senha de uso único por email foram movidas no portal do Azure de **Configurações de colaboração externa** para **Todos os provedores de identidade**.
   > Caso você veja uma alternância em vez das opções de senha de uso único por email, isso significa que você habilitou, desabilitou ou aceitou anteriormente a versão prévia do recurso. Selecione **Não** para desabilitar o recurso.
   >
   >![Alternância de senha de uso único por email desabilitada](media/one-time-passcode/enable-email-otp-disabled.png)

5. Clique em **Salvar**.

## <a name="note-for-public-preview-customers"></a>Observação para clientes de versão prévia pública

Se você tiver optado anteriormente pela versão prévia pública da senha de uso único por email, a data de outubro de 2021 para a habilitação automática desse recurso não se aplicará a você, de modo que seus processos comerciais relacionados não serão afetados. Além disso, no portal do Azure, nas propriedades **senha de uso único por email para convidados**, você não verá a opção de **Habilitar automaticamente a senha de uso único por email para convidados a partir de outubro de 2021**. Em vez disso, você verá a seguinte opção de alternância **Sim** ou **não**:

![Aceitar senha de uso único por email](media/one-time-passcode/enable-email-otp-opted-in.png)

No entanto, se você preferir recusar o recurso e permitir que ele seja habilitado automaticamente em outubro de 2021, você poderá reverter para as configurações padrão usando o [tipo de recurso para configuração do método de autenticação por email](/graph/api/resources/emailauthenticationmethodconfiguration) da API do Microsoft Graph. Depois de reverter para as configurações padrão, as seguintes opções estarão disponíveis em **Senha de uso único por email para convidados**:

![Habilitar o recurso de senha de uso único por email aceito](media/one-time-passcode/email-otp-options.png)

- **Habilitar automaticamente a senha de uso único por email para convidados a partir de outubro de 2021**. (Padrão) Se o recurso de senha de uso único por email ainda não estiver habilitado para o seu locatário, ele será automaticamente ativado em outubro de 2021. Nenhuma ação adicional será necessária se você quiser habilitar o recurso nesse momento. Se você já tiver habilitado ou desabilitado o recurso, essa opção não ficará disponível.

- **Habilitar a senha de uso único por email para convidados imediatamente**. Ativa o recurso de senha de uso único de email para seu locatário.

- **Desabilitar a senha de uso único por email para convidados**. Desativa o recurso de senha de uso único por email para o seu locatário e impede que o recurso seja ativado em outubro de 2021.

## <a name="note-for-azure-us-government-customers"></a>Observação para os clientes Azure do governo dos EUA

O recurso de senha de uso único por email é desabilitado por padrão na nuvem do Azure para o governo dos EUA.  

 ![Recurso de senha de uso único por email desabilitado](media/one-time-passcode/enable-email-otp-disabled.png)

Para habilitar o recurso de senha de uso único por email na nuvem do Azure para o governo dos EUA:

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador global do Azure AD.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Selecione **Relações organizacionais** > **Configurações**.

   > [!NOTE]
   > - Se você não vir **Relações Organizacionais**, procure "Identidades Externas" na barra de pesquisa da parte superior.

4. Selecione **senha de uso único por email** e escolha **Sim**.
5. Clique em **Salvar**.

Para obter mais informações sobre as limitações atuais, confira [nuvens do Azure para o governo dos EUA](current-limitations.md#azure-us-government-clouds).