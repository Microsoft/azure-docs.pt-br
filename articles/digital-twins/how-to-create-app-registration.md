---
title: Criar o Registro do aplicativo
titleSuffix: Azure Digital Twins
description: Consulte como criar um registro de aplicativo do Azure AD, como uma opção de autenticação para aplicativos cliente.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f252471cd3cd7e3a950bf2cfe324e580da129041
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209418"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Criar um registro de aplicativo para usar com o gêmeos digital do Azure

Ao trabalhar com uma instância do gêmeos digital do Azure, é comum interagir com essa instância por meio de aplicativos cliente, como um aplicativo cliente personalizado ou um exemplo como o [ADT Explorer](quickstart-adt-explorer.md). Esses aplicativos precisam ser autenticados com o gêmeos digital do Azure para interagir com ele e alguns dos [mecanismos de autenticação](how-to-authenticate-client.md) que os aplicativos podem usar envolvem um **registro de aplicativo** [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) .

Isso não é necessário para todos os cenários de autenticação. No entanto, se você estiver usando uma estratégia de autenticação ou um exemplo de código que exija um registro de aplicativo, incluindo uma **ID de cliente** e uma **ID de locatário**, este artigo mostra como configurar um.

## <a name="using-azure-ad-app-registrations"></a>Usando registros de aplicativo do Azure AD

O [Azure Active Directory (AD do Azure)](../active-directory/fundamentals/active-directory-whatis.md) é o serviço de gerenciamento de identidade e acesso baseado em nuvem da Microsoft. Configurar um **registro de aplicativo** no Azure AD é uma maneira de conceder a um aplicativo cliente acesso ao gêmeos digital do Azure.

Esse registro de aplicativo é onde você configura permissões de acesso para as [APIs do Azure digital gêmeos](how-to-use-apis-sdks.md). Posteriormente, os aplicativos cliente podem se autenticar no registro do aplicativo usando os **valores de ID de locatário e cliente**do registro e, como resultado, receber as permissões de acesso configuradas para as APIs.

>[!TIP]
> Você pode preferir configurar um novo registro de aplicativo toda vez que precisar de um *ou* fazer isso apenas uma vez, estabelecendo um único registro de aplicativo que será compartilhado entre todos os cenários que o exigem.

## <a name="create-the-registration"></a>Criar o registro

Comece navegando até [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) na portal do Azure (você pode usar este link ou encontrá-lo com a barra de pesquisa do Portal). Selecione *registros de aplicativo* no menu serviço e *+ novo registro*.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Exibição da página de serviço do Azure AD no portal do Azure, realçando a opção de menu ' Registros de aplicativo ' e o botão ' + novo registro '":::

Na página *registrar um aplicativo* a seguir, preencha os valores solicitados:
* **Nome**: um nome de exibição do aplicativo do Azure ad a ser associado ao registro
* **Tipos de conta com suporte**: selecione *contas neste diretório organizacional somente (somente diretório padrão-locatário único)*
* **URI de redirecionamento**: uma *URL de resposta do aplicativo do Azure ad* para o aplicativo do Azure AD. Adicione um URI de *cliente público/nativo (mobile & Desktop)* para `http://localhost` .

Quando tiver terminado, pressione o botão *registrar* .

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Exibição da página de serviço do Azure AD no portal do Azure, realçando a opção de menu ' Registros de aplicativo ' e o botão ' + novo registro '":::

Quando a configuração do registro for concluída, o portal irá redirecioná-lo para sua página de detalhes.

## <a name="collect-client-id-and-tenant-id"></a>Coletar ID do cliente e ID do locatário

Em seguida, colete alguns valores importantes sobre o registro do aplicativo em sua página de detalhes:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Exibição da página de serviço do Azure AD no portal do Azure, realçando a opção de menu ' Registros de aplicativo ' e o botão ' + novo registro '":::

Anote a ID do _**aplicativo (cliente)**_ e a _**ID do diretório (locatário)**_ mostradas **na página.** Esses são os valores que um aplicativo cliente precisará para usar esse registro para autenticar com o gêmeos digital do Azure.

## <a name="provide-azure-digital-twins-api-permission"></a>Fornecer permissão de API de gêmeos digital do Azure

Em seguida, configure o registro de aplicativo que você criou com permissões de linha de base para as APIs do Azure digital gêmeos.

Na página do portal para o registro do aplicativo, selecione *permissões de API* no menu. Na página permissões a seguir, clique no botão *+ Adicionar uma permissão* .

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Exibição da página de serviço do Azure AD no portal do Azure, realçando a opção de menu ' Registros de aplicativo ' e o botão ' + novo registro '":::

Na página *solicitar permissões de API* que segue, alterne para a guia *APIs que minha organização usa* e pesquise *gêmeos do Azure digital*. Selecione _**Azure digital gêmeos**_ nos resultados da pesquisa para continuar com a atribuição de permissões para as APIs do gêmeos digital do Azure.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Exibição da página de serviço do Azure AD no portal do Azure, realçando a opção de menu ' Registros de aplicativo ' e o botão ' + novo registro '":::

>[!NOTE]
> Se sua assinatura ainda tiver uma instância de gêmeos digital do Azure existente da visualização pública anterior do serviço (antes de julho de 2020), você precisará pesquisar e selecionar o _**serviço de espaços inteligentes do Azure**_ em vez disso. Esse é um nome mais antigo para o mesmo conjunto de APIs (Observe que a *ID do aplicativo (cliente)* é a mesma que na captura de tela acima) e sua experiência não será alterada além desta etapa.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Exibição da página de serviço do Azure AD no portal do Azure, realçando a opção de menu ' Registros de aplicativo ' e o botão ' + novo registro '":::

Em seguida, você selecionará quais permissões conceder para essas APIs. Expanda a permissão **Read (1)** e marque a caixa que diz *Read. Write* para conceder a esse aplicativo o leitor de registro e permissões de gravador.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Exibição da página de serviço do Azure AD no portal do Azure, realçando a opção de menu ' Registros de aplicativo ' e o botão ' + novo registro '":::

Clique em *adicionar permissões* quando terminar.

### <a name="verify-success"></a>Verificar êxito

Na página *permissões de API* , verifique se agora há uma entrada para o Azure digital gêmeos que reflete as permissões de leitura/gravação:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Exibição da página de serviço do Azure AD no portal do Azure, realçando a opção de menu ' Registros de aplicativo ' e o botão ' + novo registro '":::

Você também pode verificar a conexão com o gêmeos digital do Azure dentro domanifest.jsdo registro do aplicativo * em*, que foi atualizado automaticamente com as informações do gêmeos digital do Azure quando você adicionou as permissões de API.

Para fazer isso, selecione *manifesto* no menu para exibir o código do manifesto do registro do aplicativo. Role até a parte inferior da janela de código e procure esses campos em `requiredResourceAccess` . Os valores devem corresponder aos da captura de tela abaixo:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Exibição da página de serviço do Azure AD no portal do Azure, realçando a opção de menu ' Registros de aplicativo ' e o botão ' + novo registro '":::

Se esses valores estiverem ausentes, repita as etapas na [seção para adicionar a permissão de API](#provide-azure-digital-twins-api-permission).

## <a name="other-possible-steps-for-your-organization"></a>Outras etapas possíveis para sua organização

É possível que sua organização exija ações adicionais de proprietários/administradores de assinatura para configurar com êxito um registro de aplicativo. As etapas necessárias podem variar dependendo das configurações específicas da sua organização.

Aqui estão algumas atividades possíveis comuns que um proprietário/administrador na assinatura pode precisar executar. Essas e outras operações podem ser executadas na página [*aplicativo Azure ad registros*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) no portal do Azure.
* Conceda consentimento de administrador para o registro do aplicativo. Sua organização pode ter *consentimento de administrador necessário* ativado globalmente no Azure ad para todos os registros de aplicativo em sua assinatura. Nesse caso, o proprietário/administrador precisará selecionar esse botão para sua empresa na página de *permissões de API* do registro de aplicativo para que o registro do aplicativo seja válido:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Exibição da página de serviço do Azure AD no portal do Azure, realçando a opção de menu ' Registros de aplicativo ' e o botão ' + novo registro '":::
  - Se o consentimento tiver sido concedido com êxito, a entrada para o gêmeos digital do Azure deverá mostrar um valor de *status* de _concedido para **(sua empresa)** _
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Exibição da página de serviço do Azure AD no portal do Azure, realçando a opção de menu ' Registros de aplicativo ' e o botão ' + novo registro '":::
* Ativar acesso de cliente público
* Definir URLs de resposta específicas para acesso à Web e à área de trabalho
* Permitir fluxos de autenticação OAuth2 implícitos

Para obter mais informações sobre o registro de aplicativo e suas opções de configuração diferentes, consulte [*registrar um aplicativo com a plataforma de identidade da Microsoft*](/graph/auth-register-app-v2).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você configura um registro de aplicativo do Azure AD que pode ser usado para autenticar aplicativos cliente com as APIs do gêmeos digital do Azure.

Em seguida, leia sobre os mecanismos de autenticação, incluindo um que usa registros de aplicativo e outros que não:
* [*Como: escrever código de autenticação do aplicativo*](how-to-authenticate-client.md)