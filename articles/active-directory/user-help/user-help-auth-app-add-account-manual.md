---
title: Adicionar uma conta manualmente ao aplicativo – Azure Active Directory | Microsoft Docs
description: Como adicionar suas contas manualmente ao aplicativo Microsoft Authenticator para verificação de dois fatores.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f2145811338200b38e84766459844b4c83e5a80
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68382762"
---
# <a name="manually-add-an-account-to-the-app"></a>Adicionar uma conta manualmente ao aplicativo

Se a câmera não conseguir capturar o código QR, você poderá adicionar manualmente as informações da conta ao aplicativo Microsoft Authenticator para verificação de dois fatores. Isso funciona para contas corporativas ou de estudante e contas não Microsoft.

Os códigos fornecidos para suas contas não diferenciam maiúsculas de minúsculas e não exigem espaços quando adicionados ao aplicativo Microsoft Authenticator.

>[!Important]
>Antes de poder adicionar sua conta, você deverá baixar e instalar o aplicativo Microsoft Authenticator. Se você ainda não tiver feito isso, siga as etapas no artigo [Baixar e instalar o aplicativo](user-help-auth-app-download-install.md).

## <a name="add-your-work-or-school-account"></a>Adicionar a conta corporativa ou de estudante

1. Em seu computador, observe as informações **Código** e **URL** na página **Configurar aplicativo móvel**. Mantenha essa página aberta para que você possa ver o código e a URL.

    ![Tela que fornece o código QR](./media/user-help-auth-app-add-account-manual/auth-app-barcode.png)

2. Abra o aplicativo Microsoft Authenticator, selecione **adicionar conta** da **personalizar e controle** ícone no canto superior direito e, em seguida, selecione **trabalho ou conta de escola**.

3. Selecione **OU INSIRA O CÓDIGO MANUALMENTE**.

    ![Tela para verificar um código QR](./media/user-help-auth-app-add-account-manual/auth-app-manual-code.png)

4. Insira o **Código** e a **URL** da Etapa 1 e, em seguida, selecione **Concluir**.

    ![Tela para inserir código e a URL](./media/user-help-auth-app-add-account-manual/auth-app-code-url.png)

    A tela **Contas** do aplicativo mostra seu nome de conta e um código de verificação de seis dígitos. Para segurança adicional, o código de verificação muda a cada 30 segundos, evitando que alguém use o mesmo código várias vezes.

## <a name="add-your-google-account"></a>Adicionar sua conta do Google

1. Em seu computador, selecione **NÃO É POSSÍVEL DIGITALIZAR** na página **Configurar o Authenticator** com o código QR.

    A página **Não é possível digitalizar o código de barras** é exibida com o código secreto. Mantenha essa página aberta para que você possa ver o código secreto.

2. Abra o aplicativo Microsoft Authenticator, selecione **Adicionar conta** do ícone **Personalizar e controlar** no canto superior direito e, em seguida, selecione **Outra conta (Google, Facebook, etc.)** e, em seguida, selecione **OU INSERIR CÓDIGO MANUALMENTE**.

3. Insira um **Nome da conta** (por exemplo, Google) e digite a **chave secreta** da Etapa 1 e, em seguida, selecione **Concluir**.

4. Na página **Configurar o Authenticator** no computador, digite o código de verificação de seis dígitos fornecido no aplicativo para sua conta do Google e, em seguida, selecione **Verificar**.

    A tela **Contas** do aplicativo mostra seu nome de conta e um código de verificação de seis dígitos. Para segurança adicional, o código de verificação muda a cada 30 segundos, evitando que alguém use o mesmo código várias vezes.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e a conta do Google, confira [Ativar a verificação em duas etapas](https://support.google.com/accounts/answer/185839) e [Saiba mais sobre a verificação em duas etapas](https://www.google.com/landing/2step/help.html).

## <a name="add-your-facebook-account"></a>Adicionar sua conta do Facebook

1. Na página **Configurar por meio de autenticador de terceiros**, que inclui o código QR e um código escrito para entrada em seu aplicativo. Mantenha essa página aberta para que você possa ver o código.

2. Abra o aplicativo Microsoft Authenticator, selecione **Adicionar conta** do ícone **Personalizar e controlar** no canto superior direito e, em seguida, selecione **Outra conta (Google, Facebook, etc.)** e, em seguida, selecione **OU INSERIR CÓDIGO MANUALMENTE**.

3. Insira um **Nome da conta** (por exemplo, Facebook) e digite a **chave secreta** da Etapa 1 e, em seguida, selecione **Concluir**.

4. Na página **Authenticator de Dois Fatores** no computador, digite o código de verificação de seis dígitos fornecido no aplicativo para sua conta do Facebook e, em seguida, selecione **Verificar**.

    A tela **Contas** do aplicativo mostra seu nome de conta e um código de verificação de seis dígitos. Para segurança adicional, o código de verificação muda a cada 30 segundos, evitando que alguém use o mesmo código várias vezes.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e sua conta do Facebook, confira [O que é a autenticação de dois fatores e como ela funciona?](https://www.facebook.com/help/148233965247823).

## <a name="add-your-amazon-account"></a>Adicionar sua conta da Amazon

Você pode adicionar sua conta da Amazon ativando a verificação de dois fatores e, em seguida, adicionando a conta ao aplicativo.

1. Em seu computador, selecione **Não é possível digitalizar o código de barras** da página **Escolha como você vai receber códigos** com o código QR.

    A mensagem **Não é possível digitalizar o código de barras** é exibida com o código secreto. Mantenha essa mensagem aberta para que você possa ver o código secreto.

2. Abra o aplicativo Microsoft Authenticator, selecione **Adicionar conta** do ícone **Personalizar e controlar** no canto superior direito e, em seguida, selecione **Outra conta (Google, Facebook, etc.)** e, em seguida, selecione **OU INSERIR CÓDIGO MANUALMENTE**.

3. Insira um **Nome da conta** (por exemplo, Amazon) e digite a **chave secreta** da Etapa 1 e, em seguida, selecione **Concluir**.

4. Conclua o restante do processo de inscrição, incluindo a adição de um método de verificação de backup, tal como uma mensagem de texto. Em seguida, selecione **Enviar código**.

5. No computador, na página **Adicionar um método de verificação de backup**, digite o código de verificação de seis dígitos fornecido pelo método de verificação de backup para sua conta da Amazon e escolha **Verificar código e continuar**.

6. Na página Quase concluído, decida se deseja tornar seu computador um dispositivo confiável e escolha **Entendi. Ative a verificação em duas etapas**.

    A página **Configurações de Segurança Avançadas** é exibida, mostrando os detalhes da sua verificação de dois fatores atualizada.

    >[!NOTE]
    >Para obter mais informações sobre a verificação de dois fatores e a conta da Amazon, confira [Sobre a verificação em duas etapas](https://www.amazon.com/gp/help/customer/display.html?nodeId=201596330) e [Entrando com a verificação em duas etapas](https://www.amazon.com/gp/help/customer/display.html?nodeId=201962440).    

## <a name="next-steps"></a>Próximas etapas

- Depois de adicionar as contas ao aplicativo, você poderá entrar usando o aplicativo Microsoft Authenticator em seu dispositivo. Para obter mais informações, confira [Entrar usando o aplicativo](user-help-auth-app-sign-in.md).

- Se você estiver tendo problemas para obter seu código de verificação para seu conta Microsoft pessoal, consulte a seção Solucionando problemas de **código de verificação** do artigo [conta Microsoft informações de segurança & códigos de verificação](https://support.microsoft.com/en-us/help/12428/microsoft-account-security-info-verification-codes) .

- Para dispositivos que executam o iOS, você também pode fazer backup das credenciais da sua conta e das configurações de aplicativos relacionadas, tais como a ordem de suas contas, na nuvem. Para obter mais informações, confira [Fazer backup e recuperar informações com o aplicativo Microsoft Authenticator](user-help-auth-app-backup-recovery.md).
