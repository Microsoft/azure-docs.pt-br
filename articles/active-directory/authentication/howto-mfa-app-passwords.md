---
title: Configurar senhas de aplicativo para a autenticação multifator do Azure AD – Azure Active Directory
description: Saiba como configurar e usar senhas de aplicativo para aplicativos herdados na autenticação multifator do Azure AD
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/05/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfb38f9fcdba6898b690d0af68b715fea07e80bb
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743098"
---
# <a name="enable-and-use-azure-ad-multi-factor-authentication-with-legacy-applications-using-app-passwords"></a>Habilitar e usar a autenticação multifator do Azure AD com aplicativos herdados usando senhas de aplicativo

Alguns aplicativos mais antigos, sem navegador, como o Office 2010 ou anterior e o Apple mail antes do iOS 11, não entendem pausas ou interrupções no processo de autenticação. Se um usuário estiver habilitado para a autenticação multifator do Azure AD e tentar usar um desses aplicativos mais antigos, sem navegador, eles não poderão se autenticar com êxito. Para usar esses aplicativos de forma segura com a autenticação multifator do Azure AD habilitada para contas de usuário, você pode usar senhas de aplicativo. Essas senhas de aplicativo substituíram sua senha tradicional para permitir que um aplicativo ignore a autenticação multifator e funcione corretamente.

Autenticação moderna fornece suporte ao clientes do Microsoft Office 2013 e posteriores. Os clientes do Office 2013, incluindo o Outlook, dão suporte a protocolos de autenticação modernos e podem ser habilitados para funcionar com a verificação em duas etapas. Assim que o cliente estiver habilitado, as senhas de aplicativo não serão necessárias para o cliente.

Este artigo mostra como habilitar e usar senhas de aplicativo para aplicativos herdados que não dão suporte a prompts de autenticação multifator.

>[!NOTE]
> As senhas de aplicativo não funcionam com o acesso condicional com base nas políticas de autenticação multifator e na autenticação moderna.

## <a name="overview-and-considerations"></a>Visão geral e considerações

Quando uma conta de usuário é habilitada para a autenticação multifator do Azure AD, o prompt de entrada regular é interrompido por uma solicitação de verificação adicional. Alguns aplicativos mais antigos não entendem essa interrupção no processo de entrada; portanto, a autenticação falha. Para manter a segurança da conta de usuário e deixar a autenticação multifator do Azure AD habilitada, as senhas de aplicativo podem ser usadas, em vez do nome de usuário e da senha regulares. Quando uma senha de aplicativo é usada durante a entrada, não há nenhum prompt de verificação adicional, portanto, a autenticação é bem-sucedida.

As senhas de aplicativo são geradas automaticamente, não especificadas pelo usuário. Essa senha gerada automaticamente dificulta a adivinhação de um invasor, portanto, é mais segura. Os usuários não precisam manter o controle das senhas ou inseri-las sempre que as senhas de aplicativo são inseridas apenas uma vez por aplicativo.

Quando você usa senhas de aplicativo, as seguintes considerações se aplicam:

* Há um limite de 40 senhas de aplicativo por usuário.
* Os aplicativos que armazenam senhas em cache e as usam em cenários locais podem falhar porque a senha do aplicativo não é conhecida fora da conta corporativa ou de estudante. Um exemplo dessa situação é o dos emails do Exchange que estão locais, mas os emails arquivado que estão na nuvem. Nessa situação, a mesma senha não funciona.
* Após a habilitação da autenticação multifator do Azure AD na conta de um usuário, as senhas de aplicativo podem ser usadas com a maioria dos clientes sem navegador, como o Outlook e o Microsoft Skype for Business. No entanto, as ações administrativas não podem ser executadas usando senhas de aplicativo por meio de aplicativos sem navegador, como o Windows PowerShell. As ações não podem ser executadas, mesmo que o usuário tenha uma conta administrativa.
    * Para executar scripts do PowerShell, crie uma conta de serviço com uma senha forte e não habilite a conta para a verificação em duas etapas.
* Se você suspeitar que uma conta de usuário está comprometida e revogar/redefinir a senha da conta, as senhas de aplicativo também devem ser atualizadas. As senhas de aplicativo não são revogadas automaticamente quando uma senha de conta de usuário é revogada/redefinida. O usuário deve excluir as senhas de aplicativo existentes e criar novas.
   * Para obter mais informações, consulte [criar e excluir senhas de aplicativo na página de verificação de segurança adicional](../user-help/multi-factor-authentication-end-user-app-passwords.md#create-and-delete-app-passwords-from-the-additional-security-verification-page).

>[!WARNING]
> As senhas de aplicativo não funcionam em ambientes híbridos onde os clientes se comunicam com pontos de extremidade de descoberta automática local e na nuvem. As senhas de domínio são necessárias para autenticar localmente. As senhas de aplicativo são necessárias para autenticar na nuvem.

### <a name="app-password-names"></a>Nomes de senha de aplicativo

Os nomes das senhas de aplicativo devem refletir o dispositivo em que elas serão usadas. Se você tem um laptop que tem aplicativos não usados em navegador, como o Outlook, o Word e o Excel, crie uma senha de aplicativo chamada **Laptop** para esses aplicativos. Crie outra senha de aplicativo chamada **Desktop** para os mesmos aplicativos que são executados em um computador desktop.

É recomendável criar uma senha de aplicativo por dispositivo, em vez de uma senha de aplicativo por aplicativo.

## <a name="federated-or-single-sign-on-app-passwords"></a>Senhas de aplicativo federado ou de logon único

O Azure AD dá suporte à Federação, ou SSO (logon único), com Active Directory Domain Services local (AD DS). Se sua organização for federada com o Azure AD e você estiver usando a autenticação multifator do Azure AD, as seguintes considerações de senha de aplicativo se aplicarão:

>[!NOTE]
> Os pontos a seguir se aplicam somente a clientes federados (SSO).

* As senhas de aplicativo são verificadas pelo Azure AD e, portanto, ignoram a federação. A federação é usada ativamente apenas na configuração das senhas de aplicativo.
* O IdP (Provedor de Identidade) não é contatado para usuários federados (SSO), diferentemente do fluxo passivo. As senhas de aplicativo são armazenadas na conta corporativa ou de estudante. Se um usuário sai da empresa, as informações do usuário fluem para a conta corporativa ou de estudante usando o **DirSync** em tempo real. A desabilitação/exclusão da conta pode levar até três horas para sincronizar, o que pode atrasar a desabilitação/exclusão da senha do aplicativo no Azure AD.
* As configurações do Controle de Acesso de cliente local não são respeitadas pelo recurso senhas de aplicativo.
* Nenhum recurso de log de autenticação local ou de auditoria está disponível com o recurso de senhas de aplicativo.

Algumas arquiteturas avançadas exigem uma combinação de credenciais para autenticação multifator com clientes. Essas credenciais podem incluir um nome de usuário e senhas de conta corporativa ou de estudante, além das senhas de aplicativo. Os requisitos dependem de como a autenticação é realizada. Para clientes que se autenticam em uma infraestrutura local, um nome de usuário e uma senha de conta corporativa ou de estudante são necessários. Para clientes que se autenticam no Azure AD, é necessária uma senha de aplicativo.

Por exemplo, suponha que você tem a seguinte arquitetura:

* Sua instância local do Active Directory é federada com o Azure AD.
* Você usa o Exchange Online.
* Você usa o Skype for Business local.
* Você usa a autenticação multifator do Azure AD.

Nesse cenário, você usa as seguintes credenciais:

* Para entrar no Skype for Business, use seu nome de usuário e senha da conta corporativa ou de estudante.
* Para acessar o catálogo de endereços de um cliente Outlook que se conecta com o Exchange online, use uma senha de aplicativo.

## <a name="allow-users-to-create-app-passwords"></a>Permitir que os usuários criem senhas de aplicativo

Por padrão, os usuários não podem criar senhas de aplicativo. O recurso de senhas de aplicativo deve ser habilitado para que os usuários possam usá-los. Para conceder aos usuários a capacidade de criar senhas de aplicativo, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com).
2. Procure e selecione **Azure Active Directory** e, em seguida, escolha **usuários**.
3. Selecione **autenticação multifator** na barra de navegação na parte superior da janela *usuários* .
4. Em Autenticação Multifator do Microsoft Azure, selecione **configurações de serviço**.
5. Na página **Configurações do Serviço**, selecione a opção **Permitir que usuários criem senhas de aplicativo para entrarem em aplicativos que não são navegadores**.

    ![Captura de tela do portal do Azure que mostra as configurações do serviço da autenticação multifator para permitir o usuário de senhas de aplicativo](media/concept-authentication-methods/app-password-authentication-method.png)
    
> [!NOTE]
>
> Quando você desabilita a capacidade de os usuários criarem senhas de aplicativo, as senhas de aplicativo existentes continuam funcionando. No entanto, os usuários não podem gerenciar nem excluir as senhas de aplicativo existentes depois de desabilitar essa capacidade.
>
> Quando você desabilita a capacidade de criar senhas de aplicativo, também é recomendável [criar uma política de acesso condicional para desabilitar o uso da autenticação herdada](../conditional-access/block-legacy-authentication.md). Essa abordagem impede que as senhas de aplicativo existentes funcionem e força o uso de métodos de autenticação modernos.

## <a name="create-an-app-password"></a>Criar uma senha de aplicativo

Quando os usuários concluírem o registro inicial da autenticação multifator do Azure AD, haverá uma opção para criar senhas de aplicativo no final do processo de registro.

Os usuários também podem criar senhas de aplicativo após o registro. Para obter mais informações e etapas detalhadas para seus usuários, consulte [o que são senhas de aplicativo na autenticação multifator do Azure ad?](../user-help/multi-factor-authentication-end-user-app-passwords.md)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como permitir que os usuários se registrem rapidamente na autenticação multifator do Azure AD, consulte [visão geral de registro de informações de segurança combinadas](concept-registration-mfa-sspr-combined.md).
