---
title: Como proteger o dispositivo restrito de entrada com as APIs REST do Azure AD e do Azure Maps
titleSuffix: Azure Maps
description: Como configurar um aplicativo sem navegador que dá suporte à entrada no Azure AD e chama as APIs REST do Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 3833cbfd0802f334e482203d269984eb0e299797
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895623"
---
# <a name="secure-an-input-constrained-device-with-azure-ad-and-azure-maps-rest-apis"></a>Proteger um dispositivo restrito de entrada com as APIs REST do Azure AD e do Azure Maps

Este guia discute como proteger aplicativos públicos ou dispositivos que não podem armazenar segredos com segurança ou aceitar a entrada do navegador. Esses tipos de aplicativos se enquadram na categoria de IoT ou Internet das coisas. Alguns exemplos desses aplicativos podem incluir: dispositivos Smart TV ou aplicativos de emissão de dados de sensor. 

[!INCLUDE [authentication details](./includes/view-authentication-details.md)]

## <a name="create-an-application-registration-in-azure-ad"></a>Criar um registro de aplicativo no Azure AD

> [!NOTE]
> * **Leitura de pré-requisito:** [cenário: aplicativo de desktop que chama APIs da Web](../active-directory/develop/scenario-desktop-overview.md)
> * O cenário a seguir usa o fluxo de código do dispositivo, que não envolve um navegador da Web para adquirir um token.

Crie o aplicativo baseado em dispositivo no Azure AD para habilitar a entrada no Azure AD. Este aplicativo terá acesso às APIs REST do Azure Maps.

1. Na portal do Azure, na lista de serviços do Azure, selecione **Azure Active Directory**  >  **registros de aplicativo**  >  **novo registro** .  

    > [!div class="mx-imgBorder"]
    > ![Registro do Aplicativo](./media/how-to-manage-authentication/app-registration.png)

2. Insira um **nome** , escolha **contas neste diretório organizacional somente** como o **tipo de conta com suporte** . Em **URIs de redirecionamento** , especifique **cliente público/nativo (Mobile & Desktop)** e, em seguida, adicione `https://login.microsoftonline.com/common/oauth2/nativeclient` ao valor. Para obter mais detalhes, consulte [aplicativo de área de trabalho do Azure AD que chama APIs da Web: registro de aplicativo](../active-directory/develop/scenario-desktop-app-registration.md). Em seguida, **Registre** o aplicativo.

    > [!div class="mx-imgBorder"]
    > ![Adicionar detalhes de registro do aplicativo para nome e URI de redirecionamento](./media/azure-maps-authentication/devicecode-app-registration.png)

3. Navegue até **autenticação** e habilite **tratar aplicativo como um cliente público** . Isso habilitará a autenticação de código do dispositivo com o Azure AD.
    
    > [!div class="mx-imgBorder"]
    > ![Habilitar o registro de aplicativo como cliente público](./media/azure-maps-authentication/devicecode-public-client.png)

4.  Para atribuir permissões de API delegadas para mapas do Azure, vá para o aplicativo. Em seguida, selecione **permissões**  >  **de API adicionar uma permissão** . Em **APIs que minha organização usa** , pesquise e selecione **mapas do Azure** .

    > [!div class="mx-imgBorder"]
    > ![Adicionar permissões de API de aplicativo](./media/how-to-manage-authentication/app-permissions.png)

5. Marque a caixa de seleção ao lado de **acessar mapas do Azure** e, em seguida, selecione **adicionar permissões** .

    > [!div class="mx-imgBorder"]
    > ![Selecionar permissões de API de aplicativo](./media/how-to-manage-authentication/select-app-permissions.png)

6. Configure o controle de acesso baseado em função do Azure (RBAC do Azure) para usuários ou grupos. Consulte [conceder acesso baseado em função para usuários para mapas do Azure](#grant-role-based-access-for-users-to-azure-maps).

7. Adicione código para adquirir o fluxo de token no aplicativo, para obter detalhes de implementação, consulte [fluxo de código de dispositivo](../active-directory/develop/scenario-desktop-acquire-token.md#device-code-flow). Ao adquirir tokens, referencie o escopo: `user_impersonation` que foi selecionado nas etapas anteriores.

> [!Tip]
> Use a MSAL (biblioteca de autenticação da Microsoft) para adquirir tokens de acesso. Consulte as recomendações no [aplicativo de área de trabalho que chama APIs da Web: configuração de código](../active-directory/develop/scenario-desktop-app-configuration.md)

8. Redija a solicitação HTTP com o token adquirido do Azure AD e envie a solicitação com um cliente HTTP válido.

### <a name="sample-request"></a>Solicitação de exemplo
Aqui está um corpo de solicitação de exemplo para carregar um limite geográfico simples representado como uma geometria de círculo usando um ponto central e um raio.

```http
POST /mapData/upload?api-version=1.0&dataFormat=geojson
Host: atlas.microsoft.com
x-ms-client-id: 30d7cc….9f55
Authorization: Bearer eyJ0e….HNIVN
```

 O corpo da solicitação de exemplo abaixo está em geojson:
```json
{
    "type": "FeatureCollection",
    "features": [{
        "type": "Feature",
        "geometry": {
            "type": "Point",
            "coordinates": [-122.126986, 47.639754]
        },
        "properties": {
            "geometryId": "001",
            "radius": 500
        }
    }]
}
```

### <a name="sample-response"></a>Exemplo de resposta:

Cabeçalhos:
```http
Location: https://atlas.microsoft.com/mapData/metadata/{udid}?api-version=1.0
Access-Control-Expose-Headers: Location
```

Corpo:
```json
{
  "operationId": "{operationId}",
  "status": "Succeeded",
  "created": "2020-01-02 1:02:03 AM +00:00",
  "resourceLocation": "https://atlas.microsoft.com/mapData/metadata/{resourceId}?api-version=1.0"
}
```

[!INCLUDE [grant role-based access to users](./includes/grant-rbac-users.md)]

## <a name="next-steps"></a>Próximas etapas

Localize as métricas de uso da API para sua conta do Azure Maps:
> [!div class="nextstepaction"]
> [Obter métricas de uso](how-to-view-api-usage.md)