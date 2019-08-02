---
title: Problemas ao configurar o SSO de senha para um aplicativo inexistente na Galeria | Microsoft Docs
description: Problemas comuns que ocorrem quando você definir senha logon único (SSO) para aplicativos personalizados que não estão na Galeria de aplicativos do Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24330dc874173ba1c6f15abb7b4caf9f23e2e00c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440347"
---
# <a name="problems-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Problemas ao configurar senha logon único para um aplicativo inexistente na Galeria

Este artigo descreve problemas comuns que podem ocorrer quando você configura *logon único senha* (SSO) para um aplicativo inexistente na Galeria.

## <a name="capture-sign-in-fields-for-an-app"></a>Capturar campos de entrada para um aplicativo

Captura de campo de entrada só tem suporte para páginas de entrada habilitados por HTML. Ele não tem suporte para não-padrão páginas de entrada, como aqueles que usam o Adobe Flash ou outras tecnologias de HTML não habilitado.

Há duas maneiras para capturar campos de entrada para seus aplicativos personalizados:

- **Captura de campo de entrada automática** funciona bem com a maioria dos habilitados por HTML páginas de entrada, *se eles usarem IDs DIV conhecidos* para os campos de nome e a senha do usuário. O HTML da página é extraído para localizar IDs de DIV que correspondem a determinados critérios. Se os metadados é salvo para que ele possa ser reproduzido para o aplicativo mais tarde.

- **Captura de campo de entrada manual** será usado se o fornecedor do aplicativo *não rotular os campos de entrada entrar*. Captura manual também será usada se o fornecedor *renderiza vários campos que não podem ser detectados automaticamente*. Azure Active Directory (Azure AD) pode armazenar dados para quantos campos existem na página de entrada, se você o informe onde esses campos estão na página.

Em geral, se a captura de campo de entrada automática não funcionar, tente a opção manual.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Capturar automaticamente os campos de entrada para um aplicativo

Para configurar o SSO baseado em senha por meio da captura de campo de entrada automática, siga estas etapas:

1. Abra o [Portal do Azure](https://portal.azure.com/). Entrar como um administrador global ou um coadministrador.

2. No painel de navegação à esquerda, selecione **todos os serviços** para abrir a extensão do AD do Azure.

3. Tipo de **Azure Active Directory** na caixa de pesquisa do filtro e selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para exibir uma lista dos seus aplicativos.

   > [!NOTE]
   > Se você não vir o aplicativo que você deseja, use o **filtro** controle na parte superior das **todos os aplicativos** lista. Defina as **Mostrar** opção para "Todos os aplicativos".

6. Selecione o aplicativo que você deseja configurar para que o SSO.

7. Depois que o aplicativo for carregado, selecione **logon único** no painel de navegação à esquerda.

8. Selecione **baseado em senha logon** modo.

9. Insira o **URL de logon**, que é a URL da página em que os usuários inserem o nome de usuário e a senha para entrar. *Certifique-se de que os campos de entrada estão visíveis na página para a URL que você fornecer*.

10. Clique em **Salvar**.

    A página é extraída automaticamente para as caixas de chamadas entradas nome e senha de usuário. Agora você pode usar o AD do Azure para transmitir com segurança as senhas para que o aplicativo usando a extensão de navegador do painel de acesso.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Capturar manualmente os campos de entrada para um aplicativo

Para capturar manualmente campos de entrada, você deve ter a extensão de navegador do painel de acesso instalada. Além disso, seu navegador não pode estar em execução no *inPrivate*, *incognito*, ou *privada* modo.

Para instalar a extensão, consulte o [instalar a extensão de navegador do painel de acesso](#install-the-access-panel-browser-extension) seção deste artigo.

Para configurar o SSO baseado em senha para um aplicativo usando a captura de campo de entrada manual, siga estas etapas:

1. Abra o [Portal do Azure](https://portal.azure.com/). Entrar como um administrador global ou um coadministrador.

2. No painel de navegação à esquerda, selecione **todos os serviços** para abrir a extensão do AD do Azure.

3. Tipo de **Azure Active Directory** na caixa de pesquisa do filtro e selecione **Azure Active Directory**.

4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.

5. Selecione **todos os aplicativos** para exibir uma lista dos seus aplicativos.

   > [!NOTE] 
   > Se você não vir o aplicativo que você deseja, use o **filtro** controle na parte superior das **todos os aplicativos** lista. Defina as **Mostrar** opção para "Todos os aplicativos".

6. Selecione o aplicativo que você deseja configurar para que o SSO.

7. Depois que o aplicativo for carregado, selecione **logon único** no painel de navegação à esquerda.

8. Selecione **baseado em senha logon** modo.

9. Insira o **URL de logon**, que é a página em que os usuários inserem o nome de usuário e a senha para entrar. *Certifique-se de que os campos de entrada estão visíveis na página para a URL que você fornecer*.

10. Selecione **configurar *&lt;appname&gt;* configurações de logon único com senha**.

11. Selecione **detectar manualmente campos de entrada**.

14. Selecione **Okey**.

15. Clique em **Salvar**.

16. Siga as instruções para usar o painel de acesso.

## <a name="troubleshoot-problems"></a>Solucionar problemas

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Recebo um erro "Não foi possível encontrar quaisquer campos de entrada nessa URL"

Você receber essa mensagem de erro quando ocorre falha na detecção automática de campos de entrada. Para resolver o problema, tente detecção de campo de entrada manual. Consulte a [capturar manualmente os campos de entrada para um aplicativo](#manually-capture-sign-in-fields-for-an-app) seção deste artigo.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Recebo um "não é possível salvar a configuração de logon única" Erro

Raramente, atualizando a configuração de SSO falhará. Para resolver esse problema, tente salvar a configuração novamente.

Se você estiver recebendo o erro, abra um caso de suporte. Incluir as informações que são descritas o [exibir detalhes de notificação no portal](#view-portal-notification-details) e [enviar detalhes da notificação a um engenheiro de suporte para obter ajuda](#send-notification-details-to-a-support-engineer-to-get-help) seções deste artigo.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Não posso manualmente detectar os campos de entrada para meu aplicativo

Quando a detecção manual não está funcionando, você pode observar os seguintes comportamentos:

- O processo de captura manual pareceu funcionar, mas os campos capturados não estiverem corretos.

- Os campos corretos não são destacados quando o processo de captura é executado.

- O processo de captura guiará à página de entrada do aplicativo conforme o esperado, mas nada acontece.

- Captura manual parece funcionar, mas SSO não acontece quando os usuários navegam para o aplicativo do painel de acesso.

Se você tiver qualquer um desses problemas, faça o seguinte:

- Certifique-se de que você tenha a versão mais recente da extensão de navegador do painel de acesso *instalado e habilitado*. Consulte a [instalar a extensão de navegador do painel de acesso](#install-the-access-panel-browser-extension) seção deste artigo.

- Certifique-se de que seu navegador não está na *incognito*, *inPrivate*, ou *privada* modo durante o processo de captura. Não há suporte para a extensão do painel de acesso em um desses modos.

- Certifique-se de que os usuários não estão tentando entrar no aplicativo de painel de acesso ao mesmo tempo em *incognito*, *inPrivate*, ou *modo particular*.

- Repita o processo de captura manual. Certifique-se de que os marcadores vermelhos estão sobre os campos corretos.

- Se o processo de captura manual parece parar de responder ou página de entrada não responde, repita o processo de captura manual. Mas, desta vez, depois de concluir o processo, pressione a tecla F12 para abrir o console do desenvolvedor do navegador. Selecione o **console** guia. Tipo de **Window " *&lt;a URL de entrada que você especificou ao configurar o aplicativo&gt;* "** , e pressione Enter. Isso força um redirecionamento de página que termina o processo de captura e armazena os campos que foram capturados.

### <a name="contact-support"></a>Contate o suporte

Se você ainda tiver problemas, abra um caso com o Microsoft Support. Descreva o que você tentou. Incluir os detalhes que são descritos na [exibir detalhes de notificação no portal](#view-portal-notification-details) e [enviar detalhes da notificação a um engenheiro de suporte para obter ajuda](#send-notification-details-to-a-support-engineer-to-get-help) seções deste artigo (se aplicável).

## <a name="install-the-access-panel-browser-extension"></a>Instalar a extensão de navegador do painel de acesso

Siga estas etapas:

1. Abra [painel de acesso](https://myapps.microsoft.com) em um navegador com suporte. Entrar no Azure AD como um *usuário*.

2. Selecione **aplicativo de SSO de senha** no painel de acesso.

3. Quando você for solicitado a instalar o software, selecione **instalar agora**.

4. Você será direcionado para uma página de download para o seu navegador. Escolher **adicionar** a extensão.

5. Se você for solicitado, selecione **habilitar** ou **permitir**.

6. Após a instalação, reinicie o navegador.

7. Entrar no painel de acesso. Veja se você pode abrir os aplicativos habilitados por SSO de senha.

Também diretamente, você pode baixar a extensão do navegador para Chrome e Firefox por meio destes links:

-   [Extensão do painel de acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do painel de acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="view-portal-notification-details"></a>Exibir detalhes de notificação no portal

Para ver os detalhes de qualquer notificação no portal, siga estas etapas:

1. Selecione o **notificações** ícone (o sino) no canto superior direito do portal do Azure.

2. Selecione qualquer notificação que mostra uma *erro* estado. (Elas têm um vermelho "!".)

   > [!NOTE]
   > Não é possível selecionar as notificações que estão na *bem-sucedida* ou *em andamento* estado.

3. Isso abre o painel **Detalhes de Notificação**. Leia as informações para saber mais sobre o problema.

5. Se você ainda precisar de Ajuda, compartilhe informações com um engenheiro de suporte ou o grupo de produtos. Selecione o **cópia** ícone à direita do **copiar erro** caixa para copiar os detalhes de notificação para compartilhar.

## <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Enviar detalhes da notificação a um engenheiro de suporte para obter ajuda

É importante que você compartilhe *todos os* os detalhes que estão listados nesta seção com suporte para que eles podem ajudá-lo rapidamente. Para registrar a ele, você pode tirar uma captura de tela ou selecionar **copiar erro**.

As informações a seguir explicam o que significa que cada item de notificação e fornece exemplos.

### <a name="essential-notification-items"></a>Itens de notificação essenciais

- **Título**: o título descritivo da notificação.

   Exemplo: *Configurações de proxy de aplicativo*

- **Descrição**: o que ocorreu como resultado da operação.

   Exemplo: *URL interna inserida já está sendo usado por outro aplicativo.*

- **ID da notificação**: a ID exclusiva da notificação.

    Exemplo: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID de solicitação do cliente**: a ID de solicitação específica feito do seu navegador.

    Exemplo: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Hora UTC de carimbo de data /** : o carimbo de hora de quando a notificação ocorreu, em UTC.

    Exemplo: *2017-03-23T19:50:43.7583681Z*

- **ID de transação interna**: a ID interna que é usada para procurar o erro em nossos sistemas.

    Exemplo: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN**: O usuário que executou a operação.

    Exemplo: *tperkins\@f128.info*

- **ID do locatário**: a ID exclusiva do que o usuário que executou a operação é um membro do locatário.

    Exemplo: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID de objeto de usuário**: A ID exclusiva do usuário que executou a operação.

    Exemplo: *17f84be4-51f8-483a-b533-383791227a99*

### <a name="detailed-notification-items"></a>Itens de notificação detalhados

- **Nome de exibição**: (pode estar vazia) um nome de exibição mais detalhada do erro.

    Exemplo: *Configurações de proxy de aplicativo*

- **Status**: o status específico da notificação.

    Exemplo: *Com falha*

- **ID de objeto**: (pode estar vazia) a ID de objeto em relação ao qual a operação foi executada.

   Exemplo: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Detalhes**: a descrição detalhada do que ocorreu como resultado da operação.

    Exemplo: *Url interna '<https://bing.com/>' é inválido, pois ele já está em uso.*

- **Copiar erro**: Permite que você selecione os **ícone de cópia** à direita do **copiar erro** caixa de texto para copiar os detalhes de notificação para ajudar com suporte.

    Exemplo:   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Próximas etapas
[Fornecer logon único para seus aplicativos com Proxy de Aplicativo](application-proxy-configure-single-sign-on-with-kcd.md)
