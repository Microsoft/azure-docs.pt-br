---
title: Gerenciar autenticação
titleSuffix: Azure Maps
description: Familiarize-se com a autenticação do Azure Maps. Veja qual abordagem funciona melhor em qual cenário. Saiba como usar o portal para exibir as configurações de autenticação.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/12/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 57e847116febcea66e1e3ac4ba131617463b6c94
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92895759"
---
# <a name="manage-authentication-in-azure-maps"></a>Gerenciar autenticação no Azure Mapas

Depois de criar uma conta do Azure Maps, uma ID do cliente e as chaves são criadas para dar suporte à autenticação do Azure Active Directory (Azure AD) e à autenticação de chave compartilhada.

## <a name="view-authentication-details"></a>Exibir detalhes de autenticação

Depois de criar uma conta do Azure Maps, as chaves primária e secundária são geradas. Recomendamos que você use uma chave primária como uma chave de assinatura ao [usar a autenticação de chave compartilhada para chamar o Azure Maps](./azure-maps-authentication.md#shared-key-authentication). Você pode usar uma chave secundária em cenários como alterações de chave sem interrupção. Para obter mais informações, consulte [autenticação no Azure Maps](./azure-maps-authentication.md).

Você pode exibir os detalhes de autenticação no portal do Azure. Lá, em sua conta, no menu **configurações** , selecione **autenticação** .

> [!div class="mx-imgBorder"]
> ![Detalhes da autenticação](./media/how-to-manage-authentication/how-to-view-auth.png)

## <a name="discover-category-and-scenario"></a>Descobrir categoria e cenário

Dependendo das necessidades do aplicativo, há caminhos específicos para proteger o aplicativo. O Azure AD define as categorias para dar suporte a uma ampla gama de fluxos de autenticação. Consulte [categorias de aplicativo](../active-directory/develop/authentication-flows-app-scenarios.md#application-categories) para entender a qual categoria o aplicativo se ajusta.

> [!NOTE]
> Mesmo que você use a autenticação de chave compartilhada, entender categorias e cenários ajuda a proteger o aplicativo.

## <a name="determine-authentication-and-authorization"></a>Determinar a autenticação e a autorização

A tabela a seguir descreve os cenários comuns de autenticação e autorização no Azure Maps. A tabela fornece uma comparação dos tipos de proteção que cada cenário oferece.

> [!IMPORTANT]
> A Microsoft recomenda implementar o Azure Active Directory (Azure AD) com o Azure RBAC (controle de acesso baseado em função) para aplicativos de produção.

| Cenário                                                                                    | Autenticação | Autorização | Esforço de desenvolvimento | Esforço operacional |
| ------------------------------------------------------------------------------------------- | -------------- | ------------- | ------------------ | ------------------ |
| [Aplicativo de cliente não interativo/daemon confiável](./how-to-secure-daemon-app.md)        | Chave compartilhada     | N/D           | Médio             | Alta               |
| [Aplicativo de cliente não interativo/daemon confiável](./how-to-secure-daemon-app.md)        | AD do Azure       | Alto          | Baixo                | Médio             |
| [Aplicativo de página única da Web com logon único interativo](./how-to-secure-spa-users.md) | AD do Azure       | Alto          | Médio             | Médio             |
| [Aplicativo de página única da Web com logon não interativo](./how-to-secure-spa-app.md)      | AD do Azure       | Alto          | Médio             | Médio             |
| [Aplicativo Web com logon único interativo](./how-to-secure-webapp-users.md)          | AD do Azure       | Alta          | Alto               | Médio             |
| [Dispositivo IoT/dispositivo restrito de entrada](./how-to-secure-device-code.md)                     | AD do Azure       | Alto          | Médio             | Médio             |

Os links na tabela levam você a informações de configuração detalhadas para cada cenário.

## <a name="view-role-definitions"></a>Exibir definições de função

Para exibir as funções do Azure que estão disponíveis para mapas do Azure, vá para **controle de acesso (iam)** . Selecione **funções** e, em seguida, procure funções que começam com o *Azure Maps* . Essas funções do Azure Maps são as funções às quais você pode conceder acesso.

> [!div class="mx-imgBorder"]
> ![Visualizar funções disponíveis](./media/how-to-manage-authentication/how-to-view-avail-roles.png)

## <a name="view-role-assignments"></a>Exibir atribuições de função

Para exibir usuários e aplicativos que receberam acesso para mapas do Azure, vá para **controle de acesso (iam)** . Lá, selecione **atribuições de função** e, em seguida, filtre por **mapas do Azure** .

> [!div class="mx-imgBorder"]
> ![Exibir usuários e aplicativos que receberam acesso](./media/how-to-manage-authentication/how-to-view-amrbac.png)

## <a name="request-tokens-for-azure-maps"></a>Solicitar tokens para o Azure Mapas

Solicite um token do ponto de extremidade de token do Azure AD. Em sua solicitação do Azure AD, use os seguintes detalhes:

| Ambiente do Azure      | Ponto de extremidade de token do Azure AD             | ID de recurso do Azure              |
| ---------------------- | ----------------------------------- | ------------------------------ |
| Nuvem pública do Azure     | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Nuvem do Azure governamental | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` |

Para obter mais informações sobre como solicitar tokens de acesso do Azure AD para usuários e entidades de serviço, consulte [cenários de autenticação do Azure ad](../active-directory/develop/authentication-vs-authorization.md) e exibir cenários específicos na tabela de [cenários](./how-to-manage-authentication.md#determine-authentication-and-authorization).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte [Azure AD e SDK da Web do Azure Maps](./how-to-use-map-control.md).

Localize as métricas de uso da API para sua conta do Azure Maps:
> [!div class="nextstepaction"]
> [Obter métricas de uso](how-to-view-api-usage.md)

Explore os exemplos que mostram como integrar o Azure AD ao Azure Maps:

> [!div class="nextstepaction"]
> [Exemplos de autenticação do Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)