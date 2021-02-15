---
title: Registrar dispositivos pessoais na rede de uma organização – Azure Active Directory
description: Saiba como registrar seu dispositivo pessoal na rede da sua organização para acessar os recursos protegidos da sua organização.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/31/2020
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 0435b99525c34eb72d7cc5315ccb4359859cd528
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90033040"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Registre seu dispositivo pessoal na rede da sua organização
Registre seu dispositivo pessoal, geralmente um telefone ou tablet, na rede da sua organização. Depois que seu dispositivo for registrado, ele poderá acessar os recursos restritos da sua organização.

>[!Note]
>Este artigo usa um dispositivo Windows para fins de demonstração, mas você também pode registrar dispositivos executando iOS, Android ou macOS.

## <a name="what-happens-when-you-register-your-device"></a>O que acontece quando você registra seu dispositivo
Enquanto você estiver registrando seu dispositivo na rede da sua organização, as seguintes ações ocorrerá:

- Windows registra seu dispositivo na rede da sua organização.

- Opcionalmente, com base nas opções da sua organização, você pode ser solicitado a configurar a verificação em duas etapas por meio de [autenticação de dois fatores](multi-factor-authentication-end-user-first-time.md) ou [informações de segurança](./security-info-setup-signin.md).

- Opcionalmente, com base nas opções da sua organização, você pode estar automaticamente inscrito no gerenciamento de dispositivos móveis, como o Microsoft Intune. Para obter mais informações sobre como se inscrever no Microsoft Intune, consulte [Inscrever seu dispositivo no Intune](/intune-user-help/enroll-your-device-in-intune-all).

- Você percorrerá o processo de entrada, usando o nome de usuário e a senha para sua conta corporativa ou de estudante.

## <a name="to-register-your-windows-device"></a>Para registrar seu dispositivo Windows

Siga estas etapas para registrar seu dispositivo pessoal em sua rede.

1. Abra **as configurações**e, em seguida, selecione **contas**.

    ![Contas na tela Configurações](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Selecione **Acesso corporativo ou de estudante** e, em seguida, selecione **Conectar** da tela **Acesso corporativo ou de estudante**.

    ![Tela Acesso corporativo ou de estudante com a opção Conectar realçada](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. Na tela **Adicionar uma conta corporativa ou de estudante**, digite o endereço de email da sua conta corporativa ou de estudante e, em seguida, selecione **Avançar**. Por exemplo, alain@contoso.com.

4. Entre em sua conta corporativa ou de estudante e depois selecione **Entrar**.

5. Conclua o restante do processo de registro, incluindo aprovar sua solicitação de verificação de identidade (se você usar a verificação em duas etapas) e configurar Windows Hello (se necessário).

## <a name="to-verify-that-youre-registered"></a>Para verificar se você está registrado
Você pode ter certeza de que você está registrado, observando as configurações.

1. Abra **as configurações**e, em seguida, selecione **contas**.

    ![Contas na tela Configurações](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Selecione **Acesso corporativo ou de estudante** e verifique se você vê sua conta corporativa ou de estudante.

    ![Acessar tela de trabalho ou escola com conta contoso conectada](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Próximas etapas
Depois de registrar seu dispositivo pessoal para a rede da sua organização, você poderá acessar a maioria de seus recursos.

- Se sua organização quer que você ingressar seu dispositivo de trabalho, consulte [ingressar seu dispositivo de trabalho para a rede da sua organização](user-help-join-device-on-network.md).