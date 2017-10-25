---
title: "Configurar a verificação em duas etapas para minha conta corporativa ou de estudante | Microsoft Docs"
description: "Quando sua empresa configura a Autenticação Multifator do Azure, será solicitado que você se inscreva na verificação em duas etapas. Saiba como configurá-la. "
services: multi-factor-authentication
keywords: como usar o directory do azure, active directory na nuvem, tutorial do active directory
documentationcenter: 
author: barlanmsft
manager: femila
editor: pblachar
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: barlan
ms.custom: end-user
ms.openlocfilehash: 492d5dc4e20f67bcb9f72a022fec19f5ba86caac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-my-account-for-two-step-verification"></a>Configurar minha conta para verificação em duas etapas
A verificação em duas etapas é uma etapa adicional de segurança que ajuda a proteger sua conta, tornando mais difícil para outras pessoas invadi-la. Se você está lendo este artigo, é provável que tenha um email de seu administrador do trabalho ou escola sobre Autenticação Multifator. Ou talvez você tenha tentado entrar e recebido uma mensagem pedindo para configurar uma verificação de segurança adicional. Se esse for o caso, **você não poderá entrar até concluir o processo de registro automático**.

Este artigo ajuda você a configurar sua **conta corporativa ou de estudante**. Se você quiser habilitar a verificação em duas etapas para sua própria conta pessoal da Microsoft, veja [Sobre a verificação em duas etapas](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="set-up-your-account"></a>Configurar sua conta

Quando o departamento de TI exigir que você comece a usar a verificação em duas etapas, uma tela que diz **Seu administrador exigiu que você configure esta conta para verificação de segurança adicional** será exibida:

![Configuração](./media/multi-factor-authentication-end-user-first-time/first.png)

Para começar, selecione **Configurar agora.**

Se você não vir uma tela assim ao entrar, siga as instruções em [Gerenciar as configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page) para localizar a página de configurações, em que você pode gerenciar as opções de verificação.

## <a name="decide-how-you-want-to-verify-your-sign-ins"></a>Decida como você deseja verificar suas entradas

A primeira pergunta no processo de registro é como você deseja que entremos em contato com você. Examine as opções na tabela e use os links para ir até as etapas de configuração para cada método.

| Método de contato | Descrição |
| --- | --- |
| [Aplicativo móvel](#use-a-mobile-app-as-the-contact-method) |- **Receba notificações para verificação.** Essa opção envia uma notificação por push ao aplicativo autenticador no seu smartphone ou tablet. Veja a notificação e, se ela for legítima, selecione **Autenticar** no aplicativo. Seu trabalho ou escola pode exigir que você insira um PIN antes de se autenticar.<br>- **Use o código de verificação.** Nesse modo, o aplicativo autenticador gera um código de verificação que é atualizado a cada 30 segundos. Insira o código de verificação mais recente na interface de entrada.<br>O aplicativo Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| [Chamada telefônica ou SMS de celular](#use-your-mobile-phone-as-the-contact-method) |- **Chamada telefônica** faz uma chamada de voz automática para o número de telefone que você fornecer. O usuário atende à chamada e pressiona # no teclado do telefone para autenticar.<br>- **SMS** envia para o usuário uma mensagem de texto contendo um código de verificação. Após o prompt no texto, responda à mensagem de texto ou insira o código de verificação fornecido na interface de entrada. |
| [Ligação Para Telefone Comercial](#use-your-office-phone-as-the-contact-method) |Faz uma chamada de voz automática para o número de telefone que você fornecer. O usuário atende à chamada e pressiona # no teclado do telefone para autenticar. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Usar um aplicativo móvel como método de contato
Usar esse método requer que você instale um aplicativo autenticador em seu telefone ou tablet. As etapas nesse artigo baseiam-se no aplicativo Microsoft Authenticator, disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Selecione **Aplicativo móvel** na lista suspensa.
2. Selecione **Receber notificações para verificação** ou **Usar código de verificação**, então selecione **Configurar**.

   ![Tela de verificação de segurança adicional](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)

3. No seu telefone ou tablet, abra o aplicativo e selecione **+** para adicionar uma conta. (Em dispositivos Android, selecione os três pontos e depois **Adicionar conta**.)
4. Especifique que você deseja adicionar uma conta corporativa ou de estudante. O scanner de código QR em seu telefone se abre. Se a câmera não estiver funcionando corretamente, você optar por inserir as informações da sua empresa manualmente. Para obter mais informações, consulte [Adicionar uma conta manualmente](#add-an-account-manually).  
5. Digitalize a imagem do código QR que apareceu na tela para configurar o aplicativo móvel.  Selecione **Concluído** para fechar a tela do código QR.  

   ![Tela do código QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)

6. Quando a ativação for concluída no telefone, selecione **Contate-me**.  Esta etapa envia uma notificação ou um código de verificação para seu telefone. Selecione **Verificar**.  
7. Se sua empresa exigir um PIN para aprovar a verificação de entrada, digite-o.

   ![Caixa para inserir um PIN](./media/multi-factor-authentication-end-user-first-time/scan3.png)

8. Após digitar o PIN, selecione **Fechar**. Nesse ponto, sua verificação deve ter sido bem-sucedida.
9. É recomendável inserir o número de seu telefone celular caso você perca acesso ao aplicativo móvel. Especifique seu país na lista suspensa e insira o número de seu telefone celular na caixa ao lado do país. Selecione **Avançar**.
10. Neste ponto, você precisará configurar senhas de aplicativo para aplicativos que não são navegador assim como o Outlook 2010 ou anterior, ou o aplicativo de email nativo em dispositivos da Apple. Isso ocorre porque alguns aplicativos não dão suporte à verificação em duas etapas. Se você não usar esses aplicativos, clique em **Concluído** e ignore o restante das etapas.
11. Se estiver usando esses aplicativos, copie a senha de aplicativo fornecida e cole-a no aplicativo no lugar de sua senha regular. Você pode usar a mesma senha de aplicativo para vários aplicativos. Para obter mais informações, [ajuda com senhas de aplicativo].
12. Clique em **Concluído**.

### <a name="add-an-account-manually"></a>Adicionar uma conta manualmente
Se você quiser adicionar uma conta ao aplicativo móvel manualmente em vez de usar o leitor QR, siga estas etapas.

1. Selecione o botão **Inserir conta manualmente** .  
2. Insira o código e a URL que são fornecidos na mesma página que mostra o código de barras. Essas informações são inseridas nas caixas **Código** e **URL** no aplicativo móvel.

    ![Configuração](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Quando a ativação for concluída, selecione **Contate-me**. Esta etapa envia uma notificação ou um código de verificação para seu telefone. Selecione **Verificar**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Usar o telefone celular como o método de contato
1. Selecione **Telefone de Autenticação** na lista suspensa.  

    ![Configuração](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Escolha o seu país na lista suspensa e insira o número do celular.
3. Selecione o método que você prefere usar com o celular – texto ou ligação.
4. Selecione **Contate-me** para verificar seu número de telefone. Dependendo do modo selecionado, podemos lhe enviar uma mensagem de texto ou fazer uma chamada. Siga as instruções fornecidas na tela e selecione **Verificar**.
5. Neste ponto, você precisará configurar senhas de aplicativo para aplicativos que não são navegador assim como o Outlook 2010 ou anterior, ou o aplicativo de email nativo em dispositivos da Apple. Isso ocorre porque alguns aplicativos não dão suporte à verificação em duas etapas. Se você não usar esses aplicativos, clique em **Concluído** e ignore o restante das etapas.
6. Se estiver usando esses aplicativos, copie a senha de aplicativo fornecida e cole-a no aplicativo no lugar de sua senha regular. Você pode usar a mesma senha de aplicativo para vários aplicativos. Para obter mais informações, [ajuda com senhas de aplicativo].
7. Clique em **Concluído**.

## <a name="use-your-office-phone-as-the-contact-method"></a>Usar o telefone comercial como o método de contato
1. Selecione **Telefone Comercial** na lista suspensa  

    ![Configuração](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. A caixa de número de telefone é preenchida automaticamente com as informações de contato da empresa. Se o número estiver incorreto ou ausente, peça ao administrador para fazer alterações.
3. Selecione **Contate-me** para verificar seu número de telefone e ligaremos para ele. Siga as instruções fornecidas na tela e selecione **Verificar**.
4. Neste ponto, você precisará configurar senhas de aplicativo para aplicativos que não são navegador assim como o Outlook 2010 ou anterior, ou o aplicativo de email nativo em dispositivos da Apple. Isso ocorre porque alguns aplicativos não dão suporte à verificação em duas etapas. Se você não usar esses aplicativos, clique em **Concluído** e ignore o restante das etapas.
5. Se estiver usando esses aplicativos, copie a senha de aplicativo fornecida e cole-a no aplicativo no lugar de sua senha regular. Você pode usar a mesma senha de aplicativo para vários aplicativos. Para obter mais informações, consulte [O que são senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md).
6. Clique em **Concluído**.

## <a name="next-steps"></a>Próximas etapas
* Altere suas opções preferenciais e [gerencie suas configurações de verificação em duas etapas](multi-factor-authentication-end-user-manage-settings.md)
* Configure [senhas de aplicativo](multi-factor-authentication-end-user-app-passwords.md) para aplicativos de dispositivo nativo que não dão suporte à verificação em duas etapas.
* Confira o [aplicativo Microsoft Authenticator](microsoft-authenticator-app-how-to.md) para obter autenticação segura mesmo quando você não tiver um serviço de célula.
