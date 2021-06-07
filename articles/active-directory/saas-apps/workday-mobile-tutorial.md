---
title: 'Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Aplicativo Móvel do Workday | Microsoft Docs'
description: Saiba como configurar o logon único entre o Azure Active Directory e o Aplicativo Móvel do Workday.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: jeedes
ms.openlocfilehash: 94ab61a5c6335d43842ada48f17572c186af773c
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077186"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday-mobile-application"></a>Tutorial: Integração do SSO (logon único) do Azure Active Directory ao Aplicativo Móvel do Workday

Neste tutorial, você aprenderá a integrar o Azure AD (Azure Active Directory), o Acesso Condicional e o Intune ao Aplicativo Móvel do Workday. Quando você integra o Aplicativo Móvel do Workday com a Microsoft, é possível:

* Verificar se os dispositivos estão em conformidade com suas políticas antes de entrar.
* Adicionar controles ao Aplicativo Móvel do Workday para verificar se os usuários estão acessando dados corporativos com segurança. 
* Controlar no Azure AD quem tem acesso ao Workday.
* Permitir que os usuários se conectem automaticamente ao Workday com a conta do Azure AD.
* Gerenciar suas contas em um local central: o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Introdução:

* integrate o Workday ao Azure AD.
* Leia [Integração do SSO (logon único) do Azure Active Directory ao Workday](./workday-tutorial.md).

## <a name="scenario-description"></a>Descrição do cenário

Neste tutorial, você vai configurar e testar as políticas de Acesso Condicional do Azure AD e o Intune com o Aplicativo Móvel do Workday.

Para habilitar o SSO (logon único), configure o aplicativo Workday Federated com o Azure AD. Para saber mais, confira [Integração do SSO (logon único) do Azure Active Directory ao Workday](./workday-tutorial.md).

> [!NOTE] 
> O Workday não é compatível com as políticas de proteção de aplicativo do Intune. É preciso usar o gerenciamento de dispositivo móvel para utilizar o Acesso Condicional.


## <a name="ensure-users-have-access-to-workday-mobile-application"></a>Verifique se os usuários têm acesso ao Aplicativo Móvel do Workday

Configure o Workday para permitir o acesso aos aplicativos móveis deles. É preciso configurar as seguintes políticas para o Aplicativo Móvel do Workday:

1. Acessar o relatório de políticas de segurança de domínio para área funcional.
1. Selecione a política de segurança correta:
    * Uso Móvel – Android
    * Uso Móvel – iPad
    * Uso Móvel – iPhone
1. Selecione **Editar Permissões**.
1. Marque a caixa de seleção **Exibir ou Modificar** para conceder aos grupos de segurança acesso aos itens protegíveis de tarefa ou relatório.
1. Marque a caixa de seleção **Obter ou Colocar** para conceder aos grupos de segurança acesso à integração e às ações protegíveis de tarefa ou relatório.

Ative as alterações de política de segurança pendentes executando **Ativar as Alterações de Política de Segurança Pendentes**.

## <a name="open-workday-sign-in-page-in-workday-mobile-browser"></a>Abrir a página de entrada no Navegador Móvel do Workday

Para aplicar o Acesso Condicional ao Aplicativo Móvel do Workday, é necessário abri-lo em um navegador externo. Em **Editar Configuração do Locatário – Segurança**, selecione **Habilitar SSO do Navegador Móvel para Aplicativos Nativos**. Para fazer isso, um navegador aprovado pelo Intune precisa estar instalado no dispositivo para iOS e no perfil de trabalho para Android.

![Captura de tela do logon de Navegador Móvel do Workday.](./media/workday-tutorial/mobile-browser.png)

## <a name="set-up-conditional-access-policy"></a>Configurar política de Acesso Condicional

Essa política só afeta a entrada em um dispositivo iOS ou Android. Para usá-la em todas as plataformas, selecione **Qualquer Dispositivo**. O dispositivo precisa estar em conformidade com essa política, e isso é verificado pelo Intune. Como o Android tem perfis de trabalho, isso impede os usuários de entrar no Workday, a menos que eles se conectem por meio do perfil de trabalho e tenham instalado o aplicativo pelo portal da empresa do Intune. No iOS, há mais uma etapa para verificar se isso também ocorre.

O Workday é compatível com estes controles de acesso:
* Exigir autenticação multifator
* Exigir que o dispositivo seja marcado como em conformidade

O Aplicativo Workday não é compatível com:
* Exigir um aplicativo cliente aprovado
* Exigir uma política de proteção do aplicativo (versão prévia)

Para configurar o Workday como um dispositivo gerenciado, siga estas etapas:

![Captura de tela de Somente Dispositivos Gerenciados e Ações ou aplicativos de nuvem.](./media/workday-tutorial/managed-devices-only.png)

1. Selecione **Página Inicial** > **Microsoft Intune** > **Políticas de Acesso Condicional**. Em seguida, selecione **Somente Dispositivos Gerenciados**. 

1. Em **Somente Dispositivos Gerenciados**, em **Nome**, selecione **Somente Dispositivos Gerenciados** e **Ações ou aplicativos de nuvem**.

1. Em **Ações ou aplicativos de nuvem**:

    a. Alterne **Selecione a que essa política se aplica** para **Aplicativos de nuvem**.

    b. Em **Incluir**, clique em **Selecionar aplicativos**.

    c. Na lista **Selecionar**, escolha **Workday**.

    d. Selecione **Concluído**.

1. Defina **Habilitar política** como **Ativo**.

1. Selecione **Salvar**.

Para **Conceder** acesso, execute as seguintes etapas:

![Captura de tela de Somente Dispositivos Gerenciados e Concessão.](./media/workday-tutorial/managed-devices-only-2.png)

1. Selecione **Página Inicial** > **Microsoft Intune** > **Políticas de Acesso Condicional**. Em seguida, selecione **Somente Dispositivos Gerenciados**. 

1. Em **Somente Dispositivos Gerenciados**, em **Nome**, selecione **Somente Dispositivos Gerenciados**. Em **Controles de acesso**, selecione **Conceder**.

1. Em **Concessão**:

    a. Selecione os controles a serem impostos como **Conceder acesso**.

    b. Selecione **Exigir que o dispositivo seja marcado como em conformidade**.

    c. Selecione **Exigir um dos controles selecionados**.

    d. Escolha **Selecionar**.

1. Defina **Habilitar política** como **Ativo**.

1. Selecione **Salvar**.

## <a name="set-up-device-compliance-policy"></a>Configurar a política de conformidade do dispositivo

Para que os dispositivos iOS só possam entrar por meio do Workday gerenciado pelo gerenciamento de dispositivo móvel, é preciso bloquear o aplicativo da App Store adicionando **com.workday.workdayapp** à lista de aplicativos restritos. Com isso, apenas os dispositivos que tiverem o Workday instalado por meio do portal da empresa poderão acessar o Workday. No navegador, só será possível acessar o Workday se o dispositivo for gerenciado pelo Intune e estiver usando um navegador gerenciado.

![Captura de tela da política de conformidade do dispositivo iOS.](./media/workday-tutorial/ios-policy.png)

## <a name="set-up-intune-app-configuration-policies"></a>Definir políticas de configuração de aplicativos do Intune

| Cenário | Pares chave-valor |
|----------------------------------------------------------------------------------------   |-----------|
| Preencha automaticamente os campos Locatário e Endereço Web para:<br>● Workday no Android, quando você habilitar o Android para perfis de trabalho.<br>● Workday no iPad e iPhone.     | Use estes valores para configurar seu Locatário: <br>● Chave de Configuração = `UserGroupCode`<br>● Tipo de valor = String <br>● Valor de Configuração = nome do seu locatário. Exemplo: `gms`<br>Use estes valores para configurar seu Endereço Web:<br>●  Chave de Configuração = `AppServiceHost`<br>● Tipo de valor = String<br>● Valor de Configuração = a URL base para seu locatário. Exemplo: `https://www.myworkday.com`                                | 
| Desabilite estas ações para o Workday no iPad e no iPhone:<br>● Recortar, Copiar e Colar<br>● Imprimir                       | Defina o valor (booliano) como `False` nestas chaves para desabilitar a funcionalidade:<br>●   `AllowCutCopyPaste`<br>●    `AllowPrint`    |
| Desabilite capturas de tela para o Workday no Android. |Defina o valor (booliano) como `False` na chave `AllowScreenshots` para desabilitar a funcionalidade.|
| Desabilite as atualizações sugeridas para seus usuários.|Defina o valor (booliano) como `False` na chave `AllowSuggestedUpdates` para desabilitar a funcionalidade.|
|Personalize a URL da loja de aplicativos para direcionar os usuários móveis à loja de aplicativos de sua escolha.|Use estes valores para alterar a URL da loja de aplicativos:<br>● Chave de Configuração = `AppUpdateURL`<br>● Tipo de valor = String<br> ● Valor de Configuração = URL da loja de aplicativos|

## <a name="ios-configuration-policies"></a>Políticas de configuração de iOS

1. Acesse o [portal do Azure](https://portal.azure.com/) e faça logon.
1. Pesquise **Intune** ou selecione o widget na lista.
1. Acesse **Aplicativos Cliente** > **Aplicativos** > **Políticas de Configuração de Aplicativo**. Em seguida, selecione **+ Adicionar** > **Dispositivos Gerenciados**.
1. Insira um nome.
1. Em **Plataforma**, escolha **iOS/iPadOS**.
1. Em **Aplicativo Associado**, escolha o aplicativo Workday para iOS que você adicionou.
1. Selecione **Configurações**. Em **Formato das configurações**, selecione **Inserir dados XML**.
1. Veja um arquivo XML de exemplo. Adicione as configurações que você deseja aplicar. Substitua `STRING_VALUE` pela cadeia de caracteres que você quer usar. Substitua `<true /> or <false />` por `<true />` ou `<false />`. Se você não adicionar uma configuração, esse exemplo funcionará como `True`.

    ```
    <dict>
    <key>UserGroupCode</key>
    <string>STRING_VALUE</string>
    <key>AppServiceHost</key>
    <string>STRING_VALUE</string>
    <key>AllowCutCopyPaste</key>
    <true /> or <false />
    <key>AllowPrint</key>
    <true /> or <false />
    <key>AllowSuggestedUpdates</key>
    <true /> or <false />
    <key>AppUpdateURL</key>
    <string>STRING_VALUE</string>
    </dict>

    ```
1. Selecione **Adicionar**.
1. Atualize a página e clique na política recém-criada.
1. Clique em **Atribuições** e escolha a quem você deseja que o aplicativo se aplique.
1. Selecione **Salvar**.

## <a name="android-configuration-policies"></a>Políticas de configuração do Android

1. Acesse o [portal do Azure](https://portal.azure.com/) e faça logon.
2. Pesquise **Intune** ou selecione o widget na lista.
3. Acesse **Aplicativos Cliente** > **Aplicativos** > **Políticas de Configuração de Aplicativo**. Em seguida, selecione **+ Adicionar** > **Dispositivos Gerenciados**.
5. Insira um nome. 
6. Em **Plataforma**, escolha **Android**.
7. Em **Aplicativo Associado**, escolha o aplicativo Workday para Android que você adicionou.
8. Selecione **Configurações**. Em **Formato das configurações**, selecione **Inserir dados JSON**.
