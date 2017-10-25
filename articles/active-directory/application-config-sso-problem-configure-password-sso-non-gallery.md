---
title: "Como configurar o logon único com senha para um aplicativo que não seja da galeria | Microsoft Docs"
description: "Compreender os problemas comuns que as pessoas enfrentam ao configurar o Logon Único com Senha para aplicativos personalizados que não estão listados na Galeria do Aplicativo Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 9c76b6f3495e2dd759a156fcef97b57aece8d632
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="problem-configuring-password-single-sign-on-for-a-non-gallery-application"></a>Como configurar o logon único com senha para um aplicativo que não seja da galeria

Este artigo ajuda você a compreender os problemas comuns que as pessoas enfrentam ao configurar o **Logon Único com Senha** com um aplicativo que não seja da Galeria.

## <a name="how-to-capture-sign-in-fields-for-an-application"></a>Como capturar campos de entrada para um aplicativo

Captura de campo de entrada só tem suporte para páginas de entrada habilitadas por HTML e **não tem suporte para páginas de entrada não padrão**, como aquelas que usam Flash ou outras tecnologias de HTML não habilitado.

Há duas maneiras que você pode capturar campos de entrada para aplicativos personalizados:

-   Captura automática de campo de entrada

-   Captura manual de campo de entrada

**Captura automática de campo de entrada** funciona bem com a maioria das páginas de entrada habilitadas com HTML, se eles usarem **IDs DIV conhecidos para o campo de nome de usuário e a senha de entrada**. A maneira como isso funciona é pela captura do HTML na página para localizar IDs de DIV que correspondem a determinados critérios e salvando esses metadados para este aplicativo, para que possamos repetir senhas mais tarde.

**Captura manual de campo de entrada** pode ser usada no caso do **fornecedor do aplicativo não rotular** os campos de entrada usados para entrar. Captura manual de campo de entrada também pode ser usado no caso de quando o **fornecedor renderiza vários campos** que não podem ser detectados automaticamente. O Azure AD pode armazenar dados para quantos campos forem necessários na página de entrada, contanto que você nos informe onde esses campos estão na página.

Em geral, **se captura manual do campo de entrada não funcionar, é recomendável sempre tentar a opção manual.**

### <a name="how-to-automatically-capture-sign-in-fields-for-an-application"></a>Como capturar automaticamente os campos de entrada para um aplicativo

Configurar **Logon único baseado em senha** para um aplicativo usando **captura automática do campo de entrada**, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global** ou **Coadministrador.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

  * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Selecione o aplicativo para o qual você deseja configurar o logon único.

7.  Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação à esquerda do aplicativo.

8.  Selecione o modo **Logon baseado em senha.**

9.  Insira a **URL de Logon**. Esta é a URL no qual os usuários introduzem o nome de usuário e a senha para se conectarem. **Verifique se os campos de entrada estão visíveis na URL que você fornece**.

10. Clique no botão **Salvar** .

11. Depois de fazer isso, vamos extrair automaticamente essa URL para uma caixa de entrada de nome de usuário e de senha e permitir que você use o Azure AD para transmitir com segurança as senhas para o aplicativo usando a extensão de navegador de painel de acesso.

## <a name="how-to-manually-capture-sign-in-fields-for-an-application"></a>Como capturar manualmente campos de entrada para um aplicativo

Para capturar manualmente os campos de entrada, você deve primeiramente ter a extensão de navegador do painel de acesso instalada e **não estar sendo executado no modo inPrivate, incógnito ou privado.** Para instalar a extensão do navegador, siga as etapas na seção [Como instalar a extensão de navegador do painel de acesso](#i-cannot-manually-detect-sign-in-fields-for-my-application).

Para configurar **Logon único baseado em senha** para um aplicativo usando **captura manual do campo de entrada**, siga as etapas abaixo:

1.  Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como **Administrador Global** ou **Coadministrador.**

2.  Abra a **Extensão do Azure Active Directory** clicando em **Mais serviços** na parte inferior do menu de navegação esquerdo principal.

3.  Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4.  Clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6.  Selecione o aplicativo para o qual você deseja configurar o logon único.

7.  Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação à esquerda do aplicativo.

8.  Selecione o modo **Logon baseado em senha.**

9.  Insira a **URL de Logon**. Esta é a URL no qual os usuários introduzem o nome de usuário e a senha para se conectarem. **Verifique se os campos de entrada estão visíveis na URL que você fornece**.

10. Clique no botão **Salvar** .

11. Depois de fazer isso, vamos extrair automaticamente essa URL para uma caixa de entrada de nome de usuário e de senha e permitir que você use o Azure AD para transmitir com segurança as senhas para o aplicativo usando a extensão de navegador de painel de acesso. No caso em que isso falhar, você pode **alterar o modo de entrada para usar a captura manual do campo de entrada** continuando para a etapa 12.

12. clique em **Configurar &lt;appname&gt; configurações de logon único com senha**.

13. Selecione a opção de configuração **Detectar manualmente os campos de entrada**.

14. Clique em **OK**.

15. Clique em **Salvar**.

16. Siga as instruções na tela para usar o painel de acesso.

## <a name="i-see-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Vejo o erro "Não foi possível encontrar os campos de entrada nessa URL"

Você vê este erro quando a detecção automática de campos de entrada falha. Para resolver esse problema, tente detecção de campo de entrada manual, seguindo as etapas na seção [Como capturar manualmente os campos de entrada para um aplicativo](#how-to-manually-capture-sign-in-fields-for-an-application).

## <a name="i-see-an-unable-to-save-single-sign-on-configuration-error"></a>Vejo um erro de "não é possível salvar a configuração de logon único"

Em alguns casos raros, atualizar a configuração de logon único pode falhar. Para resolver isso, tente salvar a configuração de logon único novamente.

Se isso continuar falhando consistentemente, abra um caso de suporte e forneça as informações coletadas das seções [Como ver os detalhes de uma notificação no portal](#i-cannot-manually-detect-sign-in-fields-for-my-application) e [Como obter ajuda enviando detalhes de notificação a um engenheiro de suporte](#how-to-get-help-by-sending-notification-details-to-a-support-engineer).

## <a name="i-cannot-manually-detect-sign-in-fields-for-my-application"></a>Não posso manualmente detectar os campos de entrada para meu aplicativo

Alguns dos comportamentos que você pode ver quando a detecção manual não está funcionando incluem:

-   O processo de captura manual pareceu funcionar, mas os campos capturados não estavam corretos

-   Os campos à direita não são destacados ao executar o processo de captura

-   O processo de captura me leva à página de logon do aplicativo conforme o esperado, mas nada acontece

-   Captura manual parece funcionar, mas SSO não acontece quando meus usuários navegam para o aplicativo de painel de acesso.

Verifique se você encontra algum desses problemas:

-   Verifique se você tem a versão mais recente da extensão de navegador do painel de acesso **instalada** e **habilitada**, seguindo as etapas na seção [Como instalar a extensão de navegador do painel de acesso](#how-to-install-the-access-panel-browser-extension).

-   Certifique-se de que você não está tentando o processo de captura ao seu navegador em **modo incógnito, inPrivate ou privado**. Não há suporte para a extensão do painel de acesso nesses modos.

-   Certifique-se de que os usuários não estão tentando entrar no aplicativo usando o painel de acesso no **modo incógnita, inPrivate ou privado**. Não há suporte para a extensão do painel de acesso nesses modos.

-   Tente o processo de captura manual novamente, garantindo que os marcadores vermelhos estão sobre os campos corretos.

-   Se o processo de captura manual parece parar de responder, ou na página de entrada não faz nada (caso 3 acima), repita o processo de captura manual. Mas, desta vez depois de concluir o processo, pressione **F12** para abrir o console do desenvolvedor do navegador. Uma vez lá, abra o **console** e digite **window.location= "&lt; insira o símbolo na URL de conexão que você especificou ao configurar o aplicativo&gt;"** e pressione **Enter**. Isso força um redirecionamento da página que termina o processo de captura e armazena os campos que foram capturados.

Se nenhuma dessas abordagens funcionar para você, podemos ajudar. Abra um caso de suporte com os detalhes de que você tentou, bem como as informações coletadas das seções [Como ver os detalhes de uma notificação no portal](#i-cannot-manually-detect-sign-in-fields-for-my-application) e [Como obter ajuda enviando detalhes de notificação a um engenheiro de suporte](#how-to-get-help-by-sending-notification-details-to-a-support-engineer) (se aplicável).

## <a name="how-to-install-the-access-panel-browser-extension"></a>Como instalar a extensão do Navegador do Painel de Acesso

Para instalar a extensão do Navegador do Painel de Acesso, siga as etapas a seguir:

1.  Abra o [Painel de Acesso](https://myapps.microsoft.com) em um dos navegadores compatíveis e entre como um **usuário** no Azure AD.

2.  Clique no **aplicativo de SSO com senha** no Painel de Acesso.

3.  No prompt solicitando a instalação do software, selecione **Instalar Agora**.

4.  Com base no seu navegador, você será direcionado para o link de download. **Adicione** a extensão ao seu navegador.

5.  Se o navegador solicitar, selecione como **Habilitar** ou **Permitir** a extensão.

6.  Quando estiver instalado, **reinicie** a sessão do navegador.

7.  Entrar no Painel de Acesso e ver se é possível **iniciar** os aplicativos de SSO de senha.

Também é possível baixar a extensão para Chrome e Firefox diretamente pelos links abaixo:

-   [Extensão do Painel de Acesso do Chrome](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensão do Painel de Acesso do Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Como ver os detalhes de uma notificação do portal

Veja os detalhes de qualquer notificação do portal executando as etapas abaixo:

1.  Clique no ícone **Notificações** (o sino) na parte superior direita do Portal do Azure

2.  Selecione qualquer notificação com estado de **Erro** (aquelas com um (!) vermelho ao lado).

  >!NOTA] Não é possível clicar em notificações com estado de **Êxito** ou **Em Andamento**.
  >
  >

3.  Isso abre a folha **Detalhes da Notificação**.

4.  Use essas informações para saber mais detalhes sobre o problema.

5.  Se ainda precisar de ajuda, você também poderá compartilhar essas informações com um engenheiro de suporte ou com o grupo de produtos para obter ajuda com o problema.

6.  Clique no **ícone** de **cópia** à direita da caixa de texto **Copiar erro** para copiar todos os detalhes da notificação para compartilhar com um engenheiro de suporte ou de grupo de produtos.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Como obter ajuda enviando detalhes da notificação a um engenheiro de suporte

É muito importante que você compartilhe **todos os detalhes abaixo** com um engenheiro de suporte caso precise de ajuda, para que ele possa ajudar rapidamente. Faça isso facilmente **tirando uma captura de tela** ou clicando no **ícone Copiar erro**, localizado à direita da caixa de texto **Copiar erro**.

## <a name="notification-details-explained"></a>Detalhes da notificação explicados

Abaixo, explicamos mais sobre o significado de cada item de notificação e fornecemos exemplos de cada um deles.

### <a name="essential-notification-items"></a>Itens de notificação essenciais

-   **Título** – o título descritivo da notificação

    -   Exemplo – **Configurações do proxy do aplicativo**

-   **Descrição** – a descrição do que ocorreu como resultado da operação

    -   Exemplo – **A URL interna inserida já está sendo usada por outro aplicativo**

-   **ID da Notificação** – a ID exclusiva da notificação

    -   Exemplo – **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

-   **ID de Solicitação do Cliente** – a ID de solicitação específica feita por seu navegador

    -   Exemplo – **302fd775-3329-4670-a9f3-bea37004f0bc**

-   **Carimbo de Data/Hora UTC** – o carimbo de data/hora durante o qual a notificação ocorreu, em UTC

    -   Exemplo – **2017-03-23T19:50:43.7583681Z**

-   **ID de Transação Interna** – a ID interna que podemos usar para procurar o erro em nossos sistemas

    -   Exemplo – **71a2f329-ca29-402f-aa72-bc00a7aca603**

-   **UPN** – o usuário que realizou a operação

    -   Exemplo – **tperkins@f128.info**

-   **Id do Locatário** – a ID exclusiva do locatário do qual o usuário que realizou a operação era membro

    -   Exemplo – **7918d4b5-0442-4a97-be2d-36f9f9962ece**

-   **Id de objeto de usuário** – a ID exclusiva do usuário que realizou a operação

    -   Exemplo – **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Itens de notificação detalhados

-   **Nome de Exibição** – **(pode estar vazio)** um nome de exibição mais detalhado do erro

    -   Exemplo *– **Configurações do proxy do aplicativo**

-   **Status** – o status específico da notificação

    -   Exemplo – **Falha**

-   **Id do Objeto** – **(pode estar vazio)** a ID do objeto em que a operação foi executada

    -   Exemplo – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Detalhes** – a descrição detalhada do que ocorreu como resultado da operação

    -   Exemplo – **A URL interna 'http://bing.com/' é inválida, uma vez que está sendo utilizada**

-   **Copiar erro** – clique no **ícone de cópia** à direita da caixa de texto **Copiar erro** para copiar todos os detalhes de notificação para compartilhar com um engenheiro de suporte ou de grupo de produtos

    -   Exemplo – ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'http://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'http://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

## <a name="next-steps"></a>Próximas etapas
[Fornecer logon único para seus aplicativos com Proxy de Aplicativo](active-directory-application-proxy-sso-using-kcd.md)

