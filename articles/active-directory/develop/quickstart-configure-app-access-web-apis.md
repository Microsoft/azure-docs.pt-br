---
title: 'Início Rápido: Configurar um aplicativo para acessar uma API Web | Azure'
titleSuffix: Microsoft identity platform
description: Neste guia de início rápido, você configura um registro de aplicativo que representa uma API Web na plataforma de identidade da Microsoft para habilitar o acesso a recursos no escopo (permissões) a aplicativos cliente.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: lenalepa, aragra, sureshja
ms.openlocfilehash: 0b064e8491b5d4fa988e9f476143cc0711e3934a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651367"
---
# <a name="quickstart-configure-a-client-application-to-access-a-web-api"></a>Início Rápido: Configurar um aplicativo cliente para acessar uma API Web

Neste guia de início rápido, você fornece um aplicativo cliente registrado com a plataforma de identidade da Microsoft com acesso baseado em permissões no escopo à sua própria API Web. Você também fornece acesso ao aplicativo cliente para o Microsoft Graph.

Ao especificar os escopos de uma API Web no registro do aplicativo cliente, o aplicativo cliente pode obter um token de acesso que contém os escopos da plataforma de identidade da Microsoft. Dentro do código, a API Web pode fornecer acesso baseado em permissão a seus recursos com base nos escopos encontrados no token de acesso.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa – [criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Conclusão do [Início Rápido: Registrar um aplicativo](quickstart-register-app.md)
* Conclusão do [Início Rápido: Configurar um aplicativo para expor uma API Web](quickstart-configure-app-expose-web-apis.md)

## <a name="add-permissions-to-access-your-web-api"></a>Adicionar permissões para acessar sua API Web

No primeiro cenário, você concede a um aplicativo cliente acesso à sua própria API Web, que você deve ter registrado como parte dos pré-requisitos. Se você ainda não tem um aplicativo cliente e uma API Web registrados, conclua as etapas nos dois artigos de [Pré-requisitos](#prerequisites).

Este diagrama mostra a relação entre os dois registros de aplicativo. Nesta seção, você adiciona permissões ao registro do aplicativo cliente.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/diagram-01-app-permission-to-api-scopes.svg" alt-text="Diagrama de linha mostrando uma API Web com escopos expostos à direita e um aplicativo cliente à esquerda com esses escopos selecionados como permissões" border="false":::

Depois de registrar o aplicativo cliente e a API Web e expor a API criando escopos, você pode configurar as permissões do cliente para a API seguindo estas etapas:

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário que contém o registro do aplicativo cliente.
1. Selecione **Azure Active Directory** > **Registros de aplicativo** e selecione o aplicativo cliente (*não* sua API Web).
1. Selecione **Permissões de API** > **Adicionar uma permissão** > **Minhas APIs**.
1. Selecione a API Web que você registrou como parte dos pré-requisitos.

    **Permissões delegadas** é selecionado por padrão. As permissões delegadas são apropriadas para aplicativos cliente que acessam uma API Web como o usuário conectado e cujo acesso deve ser restrito às permissões selecionadas na próxima etapa. Deixe **Permissões delegadas** selecionado para este exemplo.

    As **Permissões de aplicativo** são para aplicativos do tipo serviço ou daemon que precisam acessar uma API Web por conta própria, sem interação com o usuário para conexão ou consentimento. A menos que você tenha definido funções de aplicativo para sua API Web, essa opção estará desabilitada.
1. Em **Selecionar permissões**, expanda o recurso cujos escopos você definiu para sua API Web e selecione as permissões que o aplicativo cliente deve ter em nome do usuário conectado.

    Se você usou os nomes de escopo de exemplo especificados no início rápido anterior, verá **Employees.Read.All** e **Employees.Write.All**.
    Selecione **Employees.Read.All** ou outra permissão que você tenha criado ao concluir os pré-requisitos.
1. Selecione **Adicionar permissões** para concluir o processo.

Depois de adicionar permissões à sua API, você deverá ver as permissões selecionadas em **Permissões configuradas**. A imagem a seguir mostra o exemplo de permissão delegada *Employees.Read.All* adicionada ao registro do aplicativo cliente.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-02-configured-permissions-pane.png" alt-text="Painel Permissões configuradas no portal do Azure mostrando a permissão que acaba de ser adicionada":::

Você também pode observar a permissão *User.Read* para a API do Microsoft Graph. Essa permissão é adicionada automaticamente quando você registra um aplicativo no portal do Azure.

## <a name="add-permissions-to-access-microsoft-graph"></a>Adicionar permissões para acessar o Microsoft Graph

Além de acessar sua própria API Web em nome do usuário conectado, seu aplicativo também pode precisar acessar ou modificar os dados do usuário (ou outros) armazenados no Microsoft Graph. Você também pode ter um aplicativo de serviço ou daemon que precise acessar o Microsoft Graph em próprio nome, executando operações sem nenhuma interação com o usuário.

### <a name="delegated-permission-to-microsoft-graph"></a>Permissão delegada para o Microsoft Graph

Configure a permissão delegada para o Microsoft Graph para permitir que o aplicativo cliente execute operações em nome do usuário conectado, por exemplo, ler o email ou modificar o perfil dele. Por padrão, os usuários do seu aplicativo cliente são solicitados a entrar para dar consentimento às permissões delegadas que você configurou para ele.

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário que contém o registro do aplicativo cliente.
1. Selecione **Azure Active Directory** > **Registros de aplicativo** e selecione o aplicativo cliente.
1. Selecione **Permissões de API** > **Adicionar uma permissão** > **Microsoft Graph**
1. Selecione **Permissões delegadas**. O Microsoft Graph expõe muitas permissões, sendo a mais usada mostrada no topo da lista.
1. Em **Selecionar permissões**, selecione as seguintes permissões:

    | Permissão       | Descrição                                         |
    |------------------|-----------------------------------------------------|
    | `email`          | Exibir o endereço de email dos usuários                           |
    | `offline_access` | Manter o acesso aos dados aos quais você lhe deu acesso |
    | `openid`         | Entrada de usuários                                       |
    | `profile`        | Exibir o perfil básico dos usuários                           |
1. Selecione **Adicionar permissões** para concluir o processo.

Sempre que você configurar permissões, os usuários do seu aplicativo serão solicitados a conectarem-se para dar consentimento para seu aplicativo acessar a API de recurso em seu nome.

Como administrador, você também pode conceder consentimento em nome de *todos* os usuários para que eles não sejam solicitados a fazer isso. O consentimento do administrador é discutido posteriormente na seção [Mais sobre permissões de API e consentimento do administrador](#more-on-api-permissions-and-admin-consent) deste artigo.

### <a name="application-permission-to-microsoft-graph"></a>Permissão de aplicativo para o Microsoft Graph

Configure permissões de aplicativo para um aplicativo que precisa autenticar-se como ele mesmo sem interação nem consentimento do usuário. As permissões de aplicativo normalmente são usadas pelos serviços em segundo plano ou por aplicativos de daemon que acessam uma API "sem periféricos" e por APIs da Web que acessam outra API (downstream).

Nas etapas a seguir, você concede a permissão *Files.Read.All* do Microsoft Graph como um exemplo.

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/quickstart-configure-app-access-web-apis/portal-01-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário que contém o registro do aplicativo cliente.
1. Selecione **Azure Active Directory** > **Registros de aplicativo** e selecione o aplicativo cliente.
1. Selecione **Permissões de API** > **Adicionar uma permissão** > **Microsoft Graph** > **Permissões de aplicativo**.
1. Todas as permissões expostas pelo Microsoft Graph são mostradas em **Selecionar permissões**.
1. Selecione uma ou mais permissões que você deseja conceder ao seu aplicativo. Por exemplo, você pode ter um aplicativo daemon que examina arquivos em sua organização, alertando sobre um nome ou tipo de arquivo específico.

    Em **Selecionar permissões**, expanda **Arquivos** e selecione a permissão *Files.Read.All*.
1. Escolha **Adicionar permissões**.

Algumas permissões, como *Files.Read.All* do Microsoft Graph, exigem consentimento do administrador. Dê consentimento do administrador selecionando o botão **Dar consentimento do administrador**, discutido posteriormente na seção [Botão Consentimento do administrador](#admin-consent-button).

### <a name="configure-client-credentials"></a>Configurar as credenciais do cliente

Aplicativos que usam permissões de aplicativo autenticam-se como eles próprios usando suas respectivas credenciais sem necessidade de nenhuma interação com o usuário. Para que o aplicativo (ou a API) possa acessar o Microsoft Graph, sua API Web ou outra API usando permissões de aplicativo, você precisa configurar as credenciais do aplicativo cliente.

Para obter mais informações sobre como configurar as credenciais de um aplicativo, confira a seção [Adicionar credenciais](quickstart-register-app.md#add-credentials) do [Início Rápido: Registrar um aplicativo na plataforma de identidade da Microsoft](quickstart-register-app.md).

## <a name="more-on-api-permissions-and-admin-consent"></a>Mais sobre permissões de API e consentimento do administrador

O painel **Permissões de API** de um registro de aplicativo contêm uma tabela [Permissões configuradas](#configured-permissions) e também podem conter uma tabela [Outras permissões concedidas](#other-permissions-granted). As tabelas e o [botão Consentimento do administrador](#admin-consent-button) são descritos nas seções a seguir.

### <a name="configured-permissions"></a>Permissões configuradas

A tabela **Permissões configuradas** no painel **Permissões de API** mostra a lista de permissões que seu aplicativo requer para a operação básica – a RRA (*acesso a recursos necessários*). Os usuários, ou seus administradores, precisarão dar consentimento a essas permissões antes de usarem seu aplicativo. Outras permissões opcionais podem ser solicitadas posteriormente no runtime (usando o consentimento dinâmico).

Esta é a lista mínima de permissões que as pessoas precisarão dar consentimento para seu aplicativo. Pode haver mais, mas estas sempre serão necessárias. Para segurança e ajudar os usuários e administradores a se sentirem mais confortáveis com o uso do seu aplicativo, nunca peça nada de que você não precise.

Você pode adicionar ou remover as permissões que aparecem nessa tabela usando as etapas descritas acima ou em [Outras permissões concedidas](#other-permissions-granted) (descrito na próxima seção). Como administrador, você pode dar consentimento do administrador para o conjunto completo de permissões de uma API que aparece na tabela e revogar o consentimento para permissões individuais.

### <a name="other-permissions-granted"></a>Outras permissões concedidas

Você também pode ver uma tabela chamada **Outras permissões concedidas para {seu locatário}** no painel **Permissões de API**. A tabela **Outras permissões concedidas para {seu locatário}** mostra as permissões concedidas para o locatário que não foram explicitamente configurados no objeto do aplicativo. Essas permissões foram solicitadas e consentidas dinamicamente. Essa seção será exibida apenas se houver pelo menos uma permissão aplicável.

Você pode adicionar o conjunto completo de permissões de uma API ou permissões individuais que aparecem nesta tabela à tabela **Permissões configuradas**. Como administrador, você pode revogar o consentimento do administrador para APIs ou permissões individuais nesta seção.

### <a name="admin-consent-button"></a>Botão de consentimento do administrador

O botão **Dar consentimento do administrador para {seu locatário}** permite que um administrador dê consentimento do administrador às permissões configuradas para o aplicativo. Quando você seleciona o botão, uma caixa de diálogo solicita que você confirme a ação de consentimento.

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-03-grant-admin-consent-button.png" alt-text="Botão Dar consentimento do administrador realçado no painel Permissões configuradas no portal do Azure":::

Depois de dar consentimento, as permissões que exigiam o consentimento do administrador são mostradas como tendo o consentimento:

:::image type="content" source="media/quickstart-configure-app-access-web-apis/portal-04-admin-consent-granted.png" alt-text="Configurar a tabela de permissões no portal do Azure mostrando o consentimento do administrador dado para a permissão Files.Read.All":::

O botão **Dar consentimento do administrador** é *desabilitado* se você não é um administrador ou se nenhuma permissão foi configurada para o aplicativo. Se você tiver permissões que foram concedidas, mas ainda não foram configuradas, o botão de consentimento do administrador solicitará que você lide com essas permissões. Você pode adicioná-las a permissões configuradas ou removê-las.

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo início rápido da série para saber como configurar quais tipos de conta podem acessar seu aplicativo. Por exemplo, talvez você queira limitar o acesso somente a alguns usuários na organização (locatário único) ou queira permitir usuários em outros locatários do Azure AD (multilocatário) e usuários com MSAs (contas Microsoft pessoais).

> [!div class="nextstepaction"]
> [Modificar as contas que têm suporte por um aplicativo](quickstart-modify-supported-accounts.md)
