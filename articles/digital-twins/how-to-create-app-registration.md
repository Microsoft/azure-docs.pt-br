---
title: Criar o Registro do aplicativo
titleSuffix: Azure Digital Twins
description: Confira de que modo criar um registro de aplicativo do Azure AD como uma opção de autenticação para aplicativos cliente.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bd45bb264f8e29a2aad870a7daff45fdd44e0d3c
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478804"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Criar um registro de aplicativo para usar com os Gêmeos Digitais do Azure

Ao trabalhar com uma instância dos Gêmeos Digitais do Azure será comum interagir com ela por meio de aplicativos cliente, incluindo um aplicativo cliente personalizado ou um exemplo como o [Azure Digital Twins Explorer](quickstart-azure-digital-twins-explorer.md). Será preciso autenticar esses aplicativos nos Gêmeos Digitais do Azure para interagir com eles. Além disso, alguns [mecanismos de autenticação](how-to-authenticate-client.md) que os aplicativos podem usar envolvem obter um **registro de aplicativo** do [Azure AD (Azure Active Directory)](../active-directory/fundamentals/active-directory-whatis.md).

Isso não será necessário para todos os cenários de autenticação. No entanto, caso esteja usando uma estratégia de autenticação ou um exemplo de código que exija obter um registro de aplicativo, incluindo uma **ID de cliente** e uma **ID de locatário**, este artigo mostrará de que modo executar a configuração nesse cenário.

## <a name="using-azure-ad-app-registrations"></a>Como usar Registros de aplicativo do Azure AD

O [Azure AD (Azure Active Directory)](../active-directory/fundamentals/active-directory-whatis.md) é um serviço de gerenciamento de acesso e identidade baseado em nuvem da Microsoft. Configurar um **registro de aplicativo** no Azure AD é um modo de permitir que o aplicativo cliente acesse os Gêmeos Digitais do Azure.

É possível configurar permissões de acesso para as [APIs dos Gêmeos Digitais do Azure](how-to-use-apis-sdks.md) no registro de aplicativo. Posteriormente, os aplicativos cliente poderão se autenticar no registro de aplicativo usando **valores de ID do cliente e do locatário** do registro. Como resultado, eles obterão permissões de acesso configuradas às APIs.

>[!TIP]
> Talvez você prefira configurar um novo registro de aplicativo sempre que precisar de um *ou* fazer isso somente uma vez, estabelecendo um registro de aplicativo único que será compartilhado entre todos os cenários em que ele é necessário.

## <a name="create-the-registration"></a>Criar o registro

Comece acessando o [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) no portal do Azure (é possível usar este link ou localizá-lo na barra de pesquisa do portal). Selecione a opção *Registros de aplicativo* no menu de serviço, depois clique em *+Novo registro*.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Exibição da página de serviço do Azure AD no portal do Azure realçando a opção de menu &quot;Registros de aplicativo&quot; e o botão &quot;+Novo registro&quot;":::

Na seguinte página *Registrar um aplicativo*, preencha os valores solicitados:
* **Nome**: um nome de exibição do aplicativo do Azure AD a ser associado ao registro
* **Tipos de contas com suporte**: selecione *Contas somente neste diretório organizacional (somente o diretório padrão – locatário único)*
* **URI de redirecionamento**: uma *URL de resposta do aplicativo do Azure AD* para o aplicativo do Azure AD. Adicione um URI *Cliente/nativo público (móvel e desktop)* para `http://localhost`.

Após terminar, clique no botão *Registrar*.

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Exibição da página &quot;Registrar um aplicativo&quot; com os valores descritos preenchidos":::

Quando a configuração do registro for concluída, o portal vai redirecionar você para a página de detalhes.

## <a name="collect-client-id-and-tenant-id"></a>Coletar a ID do cliente e a ID do locatário

Em seguida, colete alguns valores importantes sobre o registro de aplicativo na página de detalhes:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Exibição do portal de valores importantes para o registro de aplicativo":::

Anote a _**ID de aplicativo (cliente)**_ e a _**ID de diretório (locatário)**_ mostradas em **sua** página. Um aplicativo cliente precisará desses valores para usar esse registro a fim de executar uma autenticação nos Gêmeos Digitais do Azure.

## <a name="provide-azure-digital-twins-api-permission"></a>Fornecer uma permissão de API dos Gêmeos Digitais do Azure

Em seguida, configure o registro de aplicativo criado com permissões de linha de base para as APIs dos Gêmeos Digitais do Azure.

Na página do portal do registro de aplicativo, selecione a opção *Permissões de API* no menu. Na página de permissões a seguir, clique no botão *+Adicionar uma permissão*.

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Exibição do registro de aplicativo no portal do Azure realçando a opção de menu &quot;Permissões de API&quot; e o botão &quot;+Adicionar uma permissão&quot;":::

Na página *Solicitar permissões de API* exibida a seguir, mude para a guia *APIs que minha organização usa* e pesquise por *Gêmeos Digitais do Azure*. Selecione a opção _**Gêmeos Digitais do Azure**_ nos resultados da pesquisa a fim de prosseguir com a atribuição de permissões para as APIs dos Gêmeos Digitais do Azure.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Exibição do resultado da pesquisa da página &quot;Solicitar Permissões de API&quot; mostrando os Gêmeos Digitais do Azure com uma ID de aplicativo (cliente) de 0b07f429-9f4b-4714-9392-cc5e8e80c8b0.":::

>[!NOTE]
> Caso sua assinatura ainda tenha uma instância existente dos Gêmeos Digitais do Azure da versão prévia pública anterior ao serviço (antes de julho de 2020), será preciso pesquisar e selecionar o _**Serviço de Espaços Inteligentes do Azure**_ como alternativa. Esse é um nome anterior do mesmo conjunto de APIs (observe que a *ID de aplicativo (cliente)* é igual à captura de tela acima). Além disso, sua experiência não será alterada além desta etapa.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Exibição do resultado da pesquisa da página &quot;Solicitar Permissões de API&quot; mostrando o Serviço de Espaços Inteligentes do Azure":::

Depois, será preciso selecionar quais permissões conceder a essas APIs. Expanda a permissão **Leitura (1)** e marque a caixa que exibe a opção *Leitura/Gravação* para conceder a esse registro de aplicativo permissões de leitura e gravação de registro.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Exibição da página &quot;Solicitar Permissões de API&quot; mostrando como selecionar permissões de &quot;Leitura/Gravação&quot; para as APIs dos Gêmeos Digitais do Azure":::

Clique em *Adicionar permissões* após a conclusão.

### <a name="verify-success"></a>Verificar êxito

Na página *Permissões de API*, verifique se agora há uma entrada para os Gêmeos Digitais do Azure representando as permissões de Leitura/Gravação:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Exibição do portal de permissões de API para o registro de aplicativo do Azure AD mostrando a opção de &quot;Acesso de Leitura/Gravação&quot; para os Gêmeos Digitais do Azure":::

Também é possível verificar se a conexão com os Gêmeos Digitais do Azure está no arquivo *manifest.json* do registro de aplicativo, que foi atualizado de modo automático com as informações dos Gêmeos Digitais do Azure quando você adicionou as permissões de API.

Para executar essa etapa, selecione a opção *Manifesto* na barra de menus para ver o código do manifesto do registro de aplicativo. Role até a parte inferior da janela do código e procure esses campos em `requiredResourceAccess`. Os valores deverão corresponder aos mostrados na captura de tela abaixo:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Exibição do portal do manifesto do registro de aplicativo do Azure AD. Há um valor de 'resourceAppId' de 0b07f429-9f4b-4714-9392-cc5e8e80c8b0, bem como um valor de 'resourceAccess > id' de 4589bd03-58cb-4e6c-b17f-b580e39652f8 aninhados em 'requiredResourceAccess'":::

Caso esses valores estejam ausentes, repita as etapas da [seção criada para adicionar a permissão de API](#provide-azure-digital-twins-api-permission).

## <a name="other-possible-steps-for-your-organization"></a>Outras etapas possíveis para sua organização

Talvez sua organização precise executar ações adicionais de proprietários/administradores de assinatura para configurar com êxito um registro de aplicativo. As etapas necessárias podem variar dependendo das configurações específicas da organização.

Aqui, temos algumas atividades possíveis comuns que um proprietário/administrador da assinatura pode precisar executar. Todas as operações podem ser executadas na página de [*Registros de aplicativo do Azure AD*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) do portal do Azure.
* Conceda consentimento do administrador para o registro do aplicativo. Sua organização poderá ter o *Consentimento do Administrador Necessário* ativado de modo global no Azure AD para todos os Registros de aplicativo em sua assinatura. Nesse caso, o proprietário/administrador precisará clicar neste botão na página de *permissões de API* do registro de aplicativo a fim de validar o registro de aplicativo para sua empresa:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Exibição do portal do botão &quot;Fornecer consentimento do administrador&quot; na opção de permissões de API":::
  - Caso o consentimento seja fornecido com êxito, a entrada para os Gêmeos Digitais do Azure deverá mostrar um valor de *Status* da opção _Fornecido para **(sua empresa)**_
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Exibição do portal do consentimento do administrador fornecido para a empresa na opção de permissões de API":::
* Ativar o acesso de cliente público
* Definir URLs de resposta específicas para obter acesso à Web e à área de trabalho
* Permitir fluxos de autenticação implícitos do OAuth2

Para obter mais informações sobre o registro de aplicativo e as diferentes opções de configuração, confira como [*Registrar um aplicativo na plataforma de identidade da Microsoft*](/graph/auth-register-app-v2).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você configurou um registro de aplicativo do Azure AD que pode ser usado para autenticar aplicativos cliente nas APIs dos Gêmeos Digitais do Azure.

Em seguida, leia informações sobre os mecanismos de autenticação, incluindo um que usa Registros de aplicativo e outros que não usam:
* [*Instruções: escrever código de autenticação de aplicativo*](how-to-authenticate-client.md)