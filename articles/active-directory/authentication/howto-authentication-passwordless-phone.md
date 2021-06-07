---
title: Entrada sem senha com o Microsoft Authenticator app-Azure Active Directory
description: Habilitar a entrada sem senha no Azure AD usando o aplicativo Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/11/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e6cd7efcd0e851c15975aba5ff9b99c615eb7d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653466"
---
# <a name="enable-passwordless-sign-in-with-the-microsoft-authenticator-app"></a>Habilitar a entrada sem senha com o aplicativo Microsoft Authenticator 

O aplicativo Microsoft Authenticator pode ser usado para entrar em qualquer conta do Azure AD sem usar uma senha. O Microsoft Authenticator usa a autenticação baseada em chave para habilitar uma credencial de usuário vinculada a um dispositivo, em que o dispositivo usa um PIN ou biométrica. O [Windows Hello para empresas](/windows/security/identity-protection/hello-for-business/hello-identity-verification) usa uma tecnologia semelhante.

Essa tecnologia de autenticação pode ser usada em qualquer plataforma de dispositivo, incluindo dispositivos móveis. Essa tecnologia também pode ser usada com qualquer aplicativo ou site que se integre com as bibliotecas de autenticação da Microsoft.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/phone-sign-in-microsoft-authenticator-app.png" alt-text="Exemplo de uma entrada de navegador solicitando que o usuário aprove a entrada.":::

As pessoas que habilitaram a entrada pelo telefone do aplicativo Microsoft Authenticator veem uma mensagem que solicita que eles toquem em um número em seu aplicativo. Nenhum nome de usuário ou senha é solicitado. Para concluir o processo de entrada no aplicativo, um usuário deve seguir as seguintes ações:

1. Corresponde ao número.
2. Escolha **Aprovar**.
3. Forneça seu PIN ou biométrica.

## <a name="prerequisites"></a>Pré-requisitos

Para usar a entrada por telefone sem senha com o aplicativo Microsoft Authenticator, os seguintes pré-requisitos devem ser atendidos:

- Autenticação multifator do Azure AD, com notificações por push permitidas como um método de verificação.
- Versão mais recente do Microsoft Authenticator instalada em dispositivos que executam o iOS 8.0 ou superior ou o Android 6.0 ou superior.

> [!NOTE]
> Se você tiver habilitado Microsoft Authenticator entrada sem senha usando o PowerShell do Azure AD, ele foi habilitado para todo o diretório. Se você habilitar o uso desse novo método, ele substituirá a política do PowerShell. Recomendamos que você habilite para todos os usuários em seu locatário por meio do menu novos *métodos de autenticação* ; caso contrário, os usuários que não estiverem na nova política não poderão mais entrar sem uma senha.

## <a name="enable-passwordless-authentication-methods"></a>Habilitar métodos de autenticação com senha

Para usar a autenticação sem senha no Azure AD, primeiro habilite a experiência de registro combinada e, em seguida, habilite os usuários para a senha menos o método.

### <a name="enable-the-combined-registration-experience"></a>Habilitar a experiência de registro combinado

Os recursos de registro para métodos de autenticação com senha contam com o recurso de registro combinado. Para permitir que os usuários concluam o registro combinado, siga as etapas para [habilitar o registro de informações de segurança combinadas](howto-registration-mfa-sspr-combined.md).

### <a name="enable-passwordless-phone-sign-in-authentication-methods"></a>Habilitar métodos de autenticação de entrada por telefone sem senha

O Azure AD permite que você escolha quais métodos de autenticação podem ser usados durante o processo de entrada. Em seguida, os usuários se registram para os métodos que desejam usar.

Para habilitar o método de autenticação para entrada no telefone sem senha, conclua as seguintes etapas:

1. Entre no [portal do Azure](https://portal.azure.com) com uma conta de *administrador global* .
1. Procure e selecione *Azure Active Directory* e, em seguida, navegue até **segurança**  >  **métodos de autenticação**  >  **políticas**.
1. Em **Microsoft Authenticator**, escolha as seguintes opções:
   1. **Habilitar** -Sim ou não
   1. **Destino** -todos os usuários ou Selecionar usuários
1. Cada grupo ou usuário adicionado é habilitado por padrão para usar Microsoft Authenticator nos modos de notificação por senha e por push ("qualquer"). Para alterar isso, para cada linha:
   1. Procurar **...**  >  **Configurar**.
   1. Para o **modo de autenticação** -qualquer, sem senha ou por push
1. Para aplicar a nova política, selecione **salvar**.

## <a name="user-registration-and-management-of-microsoft-authenticator"></a>Registro de usuário e gerenciamento de Microsoft Authenticator

Os usuários se registram para o método de autenticação com senha do Azure AD usando as seguintes etapas:

1. Navegue até [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Entre e adicione o aplicativo autenticador selecionando **Adicionar método > aplicativo autenticador** e, em seguida, **Adicionar**.
1. Siga as instruções para instalar e configurar o aplicativo Microsoft Authenticator em seu dispositivo.
1. Selecione **concluído** para concluir a configuração do autenticador.
1. Em **Microsoft Authenticator**, escolha **Habilitar entrada pelo telefone** no menu suspenso da conta registrada.
1. Siga as instruções no aplicativo para concluir o registro da conta para entrada por telefone sem senha.

Uma organização pode direcionar seus usuários para entrar com seus telefones, sem usar uma senha. Para obter mais assistência sobre como configurar o aplicativo Microsoft Authenticator e habilitar a entrada pelo telefone, consulte [entrar em suas contas usando o aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-sign-in.md).

> [!NOTE]
> Os usuários que não são permitidos pela política para usar a entrada pelo telefone não podem mais habilitá-lo no aplicativo Microsoft Authenticator.

## <a name="sign-in-with-passwordless-credential"></a>Entrar com credencial sem senha

Um usuário pode começar a utilizar a entrada sem senha depois que todas as seguintes ações forem concluídas:

- Um administrador habilitou o locatário do usuário.
- O usuário atualizou seu aplicativo Microsoft Authenticator para habilitar a entrada pelo telefone.

Na primeira vez que um usuário inicia o processo de entrada do telefone, o usuário executa as seguintes etapas:

1. Insere seu nome na página de entrada.
2. Seleciona **Avançar**.
3. Se necessário, seleciona **outras maneiras de entrar**.
4. Seleciona **aprovar uma solicitação no meu aplicativo Microsoft Authenticator**.

Em seguida, o usuário é apresentado com um número. O aplicativo solicita que o usuário se autentique selecionando o número apropriado, em vez de inserir uma senha.

Depois que o usuário utilizar o logon no telefone sem senha, o aplicativo continuará a guiar o usuário através desse método. No entanto, o usuário verá a opção para escolher outro método.

:::image type="content" border="false" source="./media/howto-authentication-passwordless-phone/web-sign-in-microsoft-authenticator-app.png" alt-text="Exemplo de uma entrada no navegador usando o aplicativo Microsoft Authenticator.":::

## <a name="known-issues"></a>Problemas conhecidos

Os seguintes problemas conhecidos existem.

### <a name="not-seeing-option-for-passwordless-phone-sign-in"></a>Não está vendo a opção para entrar no telefone sem senha

Em um cenário, um usuário pode ter uma verificação de entrada de telefone sem senha não respondida que está pendente. Ainda assim, o usuário pode tentar entrar novamente. Quando isso acontece, o usuário pode ver apenas a opção de inserir uma senha.

Para resolver esse cenário, as etapas a seguir podem ser usadas:

1. Abra o aplicativo Microsoft Authenticator.
2. Responda a quaisquer prompts de notificação.

Em seguida, o usuário pode continuar a utilizar a entrada por telefone sem senha.

### <a name="federated-accounts"></a>Contas federadas

Quando um usuário tiver habilitado qualquer credencial com senha, o processo de logon do Azure AD parará de usar a dica de logon \_ . Portanto, o processo não acelera mais o usuário em direção a um local de logon federado.

Geralmente, essa lógica impede que um usuário em um locatário híbrido seja direcionado para Active Directory serviços federados (AD FS) para verificação de entrada. No entanto, o usuário retém a opção de clicar **em usar sua senha**.

### <a name="azure-mfa-server"></a>Servidor MFA do Azure

Um usuário final pode ser habilitado para a autenticação multifator (MFA) por meio de um servidor Azure MFA local. O usuário ainda pode criar e utilizar uma única credencial de entrada de telefone sem senha.

Se o usuário tentar atualizar várias instalações (5 +) do aplicativo Microsoft Authenticator com a credencial de entrada do telefone sem senha, essa alteração poderá resultar em um erro.

### <a name="device-registration"></a>Registro de dispositivos

Antes de poder criar essa nova credencial forte, há pré-requisitos. Um pré-requisito é que o dispositivo no qual o aplicativo Microsoft Authenticator está instalado deve ser registrado no locatário do Azure AD para um usuário individual.

Atualmente, um dispositivo só pode ser registrado em um único locatário. Esse limite significa que apenas uma conta corporativa ou de estudante no aplicativo Microsoft Authenticator pode ser habilitada para entrada por telefone.

> [!NOTE]
> O registro de dispositivo não é o mesmo que o gerenciamento de dispositivos ou MDM (gerenciamento de dispositivo móvel). O registro de dispositivo associa apenas uma ID do dispositivo e uma ID de usuário ao mesmo tempo, no diretório do Azure AD.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a autenticação do Azure AD e métodos com senha, consulte os seguintes artigos:

- [Saiba como funciona a autenticação com senha](concept-authentication-passwordless.md)
- [Saiba mais sobre registro de dispositivo](../devices/overview.md#getting-devices-in-azure-ad)
- [Saiba mais sobre a autenticação multifator do Azure AD](../authentication/howto-mfa-getstarted.md)
