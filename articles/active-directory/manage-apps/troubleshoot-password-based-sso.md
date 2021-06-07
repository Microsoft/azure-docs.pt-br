---
title: Solucionar problemas de logon único baseado em senha no Azure Active Directory
description: Solucionar problemas com um aplicativo do Azure AD configurado para logon único baseado em senha.
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.openlocfilehash: fdbbc6c53e43d3ecc9fd4ecb563bd7ec798f8888
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257571"
---
# <a name="troubleshoot-password-based-single-sign-on-in-azure-ad"></a>Solucionar problemas de logon único baseado em senha no Azure Active Directory

Para usar o SSO (logon único) baseado em senha em meus aplicativos, a extensão do navegador deve ser instalada. A extensão é baixada automaticamente quando você seleciona um aplicativo configurado para SSO baseado em senha. Para saber mais sobre como usar meus aplicativos de uma perspectiva do usuário final, consulte [a ajuda do portal de meus aplicativos](../user-help/my-apps-portal-end-user-access.md).

## <a name="my-apps-browser-extension-not-installed"></a>Extensão do navegador meus aplicativos não instalada
Verifique se a extensão do navegador está instalada. Para saber mais, consulte [planejar uma Azure Active Directory implantação de meus aplicativos](my-apps-deployment-plan.md). 

## <a name="single-sign-on-not-configured"></a>Logon único não configurado
Verifique se o logon único baseado em senha está configurado. Para saber mais, confira [Configurar logon único baseado em senha](configure-password-single-sign-on-non-gallery-applications.md).

## <a name="users-not-assigned"></a>Usuários não atribuídos
Verifique se o usuário está atribuído ao aplicativo. Para saber mais, consulte [atribuir um usuário ou grupo a um aplicativo](assign-user-or-group-access-portal.md).

## <a name="credentials-are-filled-in-but-the-extension-does-not-submit-them"></a>As credenciais são preenchidas, mas a extensão não as envia

Este problema geralmente acontece se o fornecedor do aplicativo alterou a página de entrada recentemente para adicionar um campo, alterar um identificador que usamos para detectar os campos de nome de usuário e senha ou modificar como a experiência de entrada do usuário funciona no seu aplicativo. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicativos para resolver rapidamente esses problemas.

Ainda que a Microsoft possua tecnologias para detectar automaticamente quando integrações interrompem, talvez não seja possível localizar esses problemas imediatamente ou os problemas levem algum tempo para serem corrigidos. Quando uma dessas integrações não funcionar corretamente, abra um caso de suporte para que seja corrigido o mais rapidamente possível.

**Se você estiver em contato com o fornecedor desse aplicativo,** coloque-o em contato conosco de modo que a Microsoft possa trabalhar em conjunto para integrar nativamente o aplicativo com o Azure Active Directory. É possível enviar o fornecedor para o [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md) para iniciá-los.

## <a name="credentials-are-filled-in-and-submitted-but-the-page-indicates-the-credentials-are-incorrect"></a>As credenciais estão preenchidas e enviadas, mas a página indica que as credenciais estão incorretas

Para resolver este problema, primeiro teste estas coisas:

- Peça ao usuário que primeiro tente **entrar diretamente no site do aplicativo** com as credenciais armazenadas para eles.

  * Se a entrada funcionar, faça com que o usuário clique no botão **Atualizar credenciais** no **bloco do aplicativo** na seção **aplicativos** de [meus aplicativos](https://myapps.microsoft.com/) para atualizá-los para o nome de usuário e a senha de trabalho mais recentes conhecidos.

  * Se você ou outro administrador atribuiu as credenciais para esse usuário, localize a atribuição de aplicativo do usuário ou grupo, navegando até a guia **Usuários e Grupos** do aplicativo, selecionando a atribuição e clicando no botão **Atualizar Credenciais**.

- Se o usuário atribuiu suas próprias credenciais, solicite ao usuário que **certifique-se de que a senha dele não tenha expirado no aplicativo** e, esse for o caso, **atualize a senha expirada** entrando diretamente no aplicativo.

  * Depois que a senha tiver sido atualizada no aplicativo, solicite que o usuário clique no botão **Atualizar credenciais** no **bloco do aplicativo** na seção **aplicativos** de [meus aplicativos](https://myapps.microsoft.com/) para atualizá-los para o nome de usuário e a senha mais recentes conhecidos.

  * Se você ou outro administrador atribuiu as credenciais para esse usuário, localize a atribuição de aplicativo do usuário ou grupo, navegando até a guia **Usuários e Grupos** do aplicativo, selecionando a atribuição e clicando no botão **Atualizar Credenciais**.

- Verifique se a extensão do navegador meus aplicativos está em execução e habilitada no navegador do usuário.

- Verifique se os usuários não estão tentando entrar no aplicativo de meus aplicativos no **modo Incognito, InPrivate ou Private**. Não há suporte para a extensão meus aplicativos nesses modos.

Caso as sugestões anteriores não funcionem, pode ser que tenha ocorrido uma alteração no aplicativo que interrompeu temporariamente a integração do aplicativo com o Azure AD. Por exemplo, isso pode ocorrer quando o fornecedor do aplicativo introduz um script em sua página que se comporta de forma diferente para entrada manual ou automatizada, o que faz com que a integração automatizada, como a nossa, seja interrompida. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicativos para resolver rapidamente esses problemas.

Ainda que a Microsoft possua tecnologias para detectar automaticamente quando integrações interrompem, talvez não seja possível localizar esses problemas imediatamente ou os problemas levem algum tempo para serem corrigidos. Quando uma integração não funciona corretamente, você pode abrir um caso de suporte para arrumar o problema o mais rápido possível. 

Além disso, **se você estiver em contato com o fornecedor desse aplicativo,** **coloque-o em contato conosco** de modo que possamos trabalhar em conjunto para integrar nativamente o aplicativo com o Azure Active Directory. É possível enviar o fornecedor para o [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md) para iniciá-los.

## <a name="check-if-the-applications-login-page-has-changed-recently-or-requires-an-additional-field"></a>Verifique se a página de logon do aplicativo foi alterada recentemente ou exige um campo adicional

Se a página de logon do aplicativo foi drasticamente alterada, às vezes isso causa a interrupção de nossas integrações. Um exemplo disso é quando um fornecedor de aplicativo adiciona um campo de entrada, um captcha ou uma autenticação multifator às suas experiências. Felizmente, em muitos casos, a Microsoft pode trabalhar com fornecedores de aplicativos para resolver rapidamente esses problemas.

Ainda que a Microsoft possua tecnologias para detectar automaticamente quando integrações interrompem, talvez não seja possível localizar esses problemas imediatamente ou os problemas levem algum tempo para serem corrigidos. Quando uma integração não funciona corretamente, você pode abrir um caso de suporte para arrumar o problema o mais rápido possível. 

Além disso, **se você estiver em contato com o fornecedor desse aplicativo,** **coloque-o em contato conosco** de modo que possamos trabalhar em conjunto para integrar nativamente o aplicativo com o Azure Active Directory. É possível enviar o fornecedor para o [Listar seu aplicativo na galeria de aplicativos do Azure Active Directory](../develop/v2-howto-app-gallery-listing.md) para iniciá-los.

## <a name="capture-sign-in-fields-for-an-app"></a>Capturar campos de entrada para um aplicativo

A captura de campo de entrada tem suporte apenas para páginas de entrada habilitadas para HTML. Não há suporte para páginas de entrada não padrão, como as que usam o Adobe Flash ou outras tecnologias não habilitadas para HTML.

Há duas maneiras de capturar os campos de entrada para seus aplicativos personalizados:

- A **captura automática do campo de entrada** funciona bem com a maioria das páginas de entrada habilitadas para HTML, *se usarem IDs de div conhecidos* para os campos nome de usuário e senha. O HTML na página é recortado para localizar IDs de DIV que correspondem a determinados critérios. Esses metadados são salvos para que possam ser reproduzidos para o aplicativo posteriormente.

- A **captura de campo de entrada manual** será usada se o fornecedor do aplicativo *não rotular os campos de entrada de conexão*. A captura manual também será usada se o fornecedor *renderizar vários campos que não podem ser detectados automaticamente*. O Azure Active Directory (AD do Azure) pode armazenar dados para o máximo de campos que houver na página de entrada, se você informar onde esses campos estão na página.

Em geral, se a captura automática do campo de entrada não funcionar, tente a opção manual.

### <a name="automatically-capture-sign-in-fields-for-an-app"></a>Capturar automaticamente os campos de entrada para um aplicativo

Para configurar o SSO baseado em senha usando a captura automática de campo de entrada, siga estas etapas:
1. Abra o [Portal do Azure](https://portal.azure.com/). Entre como um administrador global ou coadministrador.
2. No painel de navegação no lado esquerdo, selecione **todos os serviços** para abrir a extensão do Azure AD.
3. Digite **Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.
4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.
5. Selecione **todos os aplicativos** para exibir uma lista de seus aplicativos.
   > [!NOTE]
   > Se você não vir o aplicativo desejado, use o controle de **filtro** na parte superior da lista **todos os aplicativos** . Defina a opção **Mostrar** como "todos os aplicativos".
6. Selecione o aplicativo que você deseja configurar para o SSO.
7. Depois que o aplicativo for carregado, selecione **logon único** no painel de navegação no lado esquerdo.
8. Selecione o modo de **logon baseado em senha** .
9. Insira a **URL de logon**, que é a URL da página em que os usuários inserem seu nome de usuário e senha para entrar. *Certifique-se de que os campos de entrada estejam visíveis na página para a URL que você fornecer*.
10. Clique em **Salvar**.
    A página é automaticamente recortada para as caixas de entrada nome de usuário e senha. Agora você pode usar o Azure AD para transmitir com segurança as senhas para esse aplicativo usando a extensão do navegador meus aplicativos.

### <a name="manually-capture-sign-in-fields-for-an-app"></a>Capturar manualmente os campos de entrada para um aplicativo

Para capturar manualmente os campos de entrada, você deve ter a extensão de navegador meus aplicativos instalada. Além disso, seu navegador não pode ser executado no modo *InPrivate*, *Incognito* ou *Private* .

Para configurar o SSO baseado em senha para um aplicativo usando a captura de campo de entrada manual, siga estas etapas:
1. Abra o [Portal do Azure](https://portal.azure.com/). Entre como um administrador global ou coadministrador.
2. No painel de navegação no lado esquerdo, selecione **todos os serviços** para abrir a extensão do Azure AD.
3. Digite **Azure Active Directory** na caixa de pesquisa de filtro e, em seguida, selecione **Azure Active Directory**.
4. Selecione **aplicativos empresariais** no painel de navegação do Azure AD.
5. Selecione **todos os aplicativos** para exibir uma lista de seus aplicativos.
   > [!NOTE] 
   > Se você não vir o aplicativo desejado, use o controle de **filtro** na parte superior da lista **todos os aplicativos** . Defina a opção **Mostrar** como "todos os aplicativos".
6. Selecione o aplicativo que você deseja configurar para o SSO.
7. Depois que o aplicativo for carregado, selecione **logon único** no painel de navegação no lado esquerdo.
8. Selecione o modo de **logon baseado em senha** .
9. Insira a **URL de logon**, que é a página em que os usuários inserem seu nome de usuário e senha para entrar. *Certifique-se de que os campos de entrada estejam visíveis na página para a URL que você fornecer*.
10. Selecione **definir as configurações de logon único de senha do *&lt; AppName &gt;***.
11. Selecione **detectar manualmente os campos de entrada**.
14. Selecione **OK**.
15. Clique em **Salvar**.
16. Siga as instruções para usar meus aplicativos.


## <a name="troubleshoot-problems"></a>Solução de problemas

### <a name="i-get-a-we-couldnt-find-any-sign-in-fields-at-that-url-error"></a>Recebo um erro "não foi possível encontrar nenhum campo de entrada na URL"

Você recebe essa mensagem de erro quando a detecção automática de campos de entrada falha. Para resolver o problema, experimente a detecção de campo de entrada manual. Consulte a seção [capturar manualmente os campos de entrada para um aplicativo](#manually-capture-sign-in-fields-for-an-app) deste artigo.

### <a name="i-get-an-unable-to-save-single-sign-on-configuration-error"></a>Recebo um erro "não é possível salvar a configuração de logon único"

Raramente, a atualização da configuração de SSO falha. Para resolver esse problema, tente salvar a configuração novamente.

Se você continuar recebendo o erro, abra um caso de suporte. Inclua as informações descritas no [portal exibir detalhes da notificação](#view-portal-notification-details) e [Enviar detalhes da notificação para um engenheiro de suporte para obter ajuda](#send-notification-details-to-a-support-engineer-to-get-help) seções deste artigo.

### <a name="i-cant-manually-detect-sign-in-fields-for-my-app"></a>Não consigo detectar manualmente os campos de entrada para meu aplicativo

Você pode observar os seguintes comportamentos quando a detecção manual não está funcionando:
- O processo de captura manual parecia funcionar, mas os campos capturados não estão corretos.
- Os campos corretos não são realçados quando o processo de captura é executado.
- O processo de captura leva você para a página de entrada do aplicativo como esperado, mas nada acontece.
- A captura manual parece funcionar, mas o SSO não acontece quando os usuários navegam para o aplicativo em meus aplicativos.

Se você tiver qualquer um desses problemas, faça o seguinte:
- Verifique se você tem a versão mais recente da extensão de navegador meus aplicativos *instalada e habilitada*.
- Verifique se o navegador não está no modo *Incognito*, *InPrivate* ou *Private* durante o processo de captura. A extensão meus aplicativos não tem suporte nesses modos.
- Certifique-se de que os usuários não estão tentando entrar no aplicativo de meus aplicativos enquanto estiverem no modo *Incognito*, *InPrivate* ou *Private*.
- Tente o processo de captura manual novamente. Verifique se os marcadores vermelhos estão sobre os campos corretos.
- Se o processo de captura manual parecer parar de responder ou a página de entrada não responder, tente o processo de captura manual novamente. Mas, desta vez, depois de concluir o processo, pressione a tecla F12 para abrir o console do desenvolvedor do navegador. Selecione a guia **console** . digite **Window. Location = "*&lt; a URL de entrada que você especificou ao configurar o aplicativo &gt;*"** e pressione Enter. Isso força um redirecionamento de página que encerra o processo de captura e armazena os campos que foram capturados.

### <a name="i-cant-add-another-user-to-my-password-based-sso-app"></a>Não consigo adicionar outro usuário ao meu aplicativo SSO baseado em senha

O aplicativo SSO baseado em senha tem um limite de 48 usuários. Portanto, ele tem um limite de 48 chaves para pares de nome de usuário/senha por aplicativo.
Se você quiser adicionar outros usuários, você pode:
-   Adicionar instância adicional do aplicativo
-   Remover usuários que não estão mais usando o aplicativo primeiro

## <a name="request-support"></a>Solicitar suporte 
Se você receber uma mensagem de erro ao configurar o SSO e atribuir usuários, abra um tíquete de suporte. Inclua o máximo possível das seguintes informações:

-   ID de erro de correlação
-   UPN (endereço de email de usuário)
-   TenantID
-   Tipo de navegador
-   Fuso horário e período de hora/hora em que o erro ocorreu
-   Rastreamentos do Fiddler

### <a name="view-portal-notification-details"></a>Exibir detalhes de notificação do portal

Para ver os detalhes de qualquer notificação do portal, siga estas etapas:
1. Selecione o ícone de **notificações** (o Bell) no canto superior direito do portal do Azure.
2. Selecione qualquer notificação que mostre um estado de *erro* . (Eles têm um "!" vermelho.)
   > [!NOTE]
   > Não é possível selecionar notificações que estejam no estado *bem-sucedido* ou *em andamento* .
3. Isso abre o painel **Detalhes de Notificação**. Leia as informações para saber mais sobre o problema.
5. Se você ainda precisar de ajuda, compartilhe as informações com um engenheiro de suporte ou o grupo de produtos. Selecione o ícone de **cópia** à direita da caixa de **erro de cópia** para copiar os detalhes de notificação a serem compartilhados.

### <a name="send-notification-details-to-a-support-engineer-to-get-help"></a>Enviar detalhes de notificação para um engenheiro de suporte para obter ajuda

É importante que você compartilhe *todos* os detalhes listados nesta seção com suporte para que eles possam ajudá-lo rapidamente. Para gravá-lo, você pode fazer uma captura de tela ou selecionar **erro de cópia**.

As informações a seguir explicam o que significa cada item de notificação e fornece exemplos.

#### <a name="essential-notification-items"></a>Itens de notificação essenciais

- **Título**: o título descritivo da notificação.

   Exemplo: *configurações de proxy de aplicativo*

- **Descrição**: o que ocorreu como resultado da operação.

   Exemplo: *a URL interna inserida já está sendo usada por outro aplicativo.*

- **ID da notificação**: a ID exclusiva da notificação.

    Exemplo: *clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068*

- **ID de solicitação do cliente**: a ID de solicitação específica que seu navegador fez.

    Exemplo: *302fd775-3329-4670-a9f3-bea37004f0bc*

- **Carimbo de data/hora UTC**: o carimbo de hora de quando a notificação ocorreu, em UTC.

    Exemplo: *2017-03-23T19:50:43.7583681 z*

- **ID da transação interna**: a ID interna que é usada para pesquisar o erro em nossos sistemas.

    Exemplo: **71a2f329-ca29-402F-AA72-bc00a7aca603**

- **UPN**: o usuário que executou a operação.

    Exemplo: *tperkins \@ f128.info*

- **ID do locatário**: a ID exclusiva do locatário do qual o usuário que executou a operação é membro.

    Exemplo: *7918d4b5-0442-4a97-be2d-36f9f9962ece*

- **ID de objeto de usuário**: a ID exclusiva do usuário que executou a operação.

    Exemplo: *17f84be4-51f8-483a-b533-383791227a99*

#### <a name="detailed-notification-items"></a>Itens de notificação detalhados

- **Nome de exibição**: (pode estar vazio) um nome de exibição mais detalhado para o erro.

    Exemplo: *configurações de proxy de aplicativo*

- **Status**: o status específico da notificação.

    Exemplo: *falha*

- **ID do objeto**: (pode estar vazio) a ID de objeto na qual a operação foi executada.

   Exemplo: *8e08161d-f2fd-40ad-a34a-a9632d6bb599*

- **Detalhes**: a descrição detalhada do que ocorreu como resultado da operação.

    Exemplo: a *URL interna ' <https://bing.com/> ' é inválida porque já está em uso.*

- **Erro de cópia**: permite que você selecione o **ícone de cópia** à direita da caixa de texto **copiar erro** para copiar os detalhes de notificação para ajudar com o suporte.

    Exemplo   ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```


## <a name="next-steps"></a>Próximas etapas
* [Série de Início Rápido no Gerenciamento de Aplicativos](view-applications-portal.md)
* [Planejar uma implantação de Meus Aplicativos](my-apps-deployment-plan.md)
