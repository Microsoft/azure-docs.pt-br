---
title: Como usar a Remover um aplicativo registrado da plataforma de identidade da Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Nestas Instruções, você aprenderá a remover um aplicativo registrado na plataforma de identidade da Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: e04884c078bd9a5693ddcbc4e71470bb23e13d60
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079662"
---
# <a name="how-to-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Como remover um aplicativo registrado na plataforma de identidade da Microsoft

Os desenvolvedores corporativos e provedores de SaaS (software como serviço) que registraram aplicativos na plataforma de identidade da Microsoft podem precisar remover o registro do aplicativo.

Nas seções seguintes, você aprenderá a:

* Remover um aplicativo criado por você ou por sua organização
* Remover um aplicativo criado por outra organização

## <a name="prerequisites"></a>Pré-requisitos

* Um [aplicativo registrado no locatário do Azure AD](quickstart-register-app.md)

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>Remover um aplicativo criado por você ou por sua organização

Os aplicativos que você ou sua organização registraram são representados por um objeto de aplicativo e pelo objeto de entidade de serviço em seu locatário. Para saber mais, consulte [Objetos de aplicativo e objetos de entidade de serviço](./app-objects-and-service-principals.md).

> [!NOTE]
> Excluir um aplicativo também excluirá seu objeto de entidade de serviço no diretório base do aplicativo. Para aplicativos multilocatários, os objetos de entidade de serviço em outros diretórios não serão excluídos.

Para excluir um aplicativo, você precisa estar listado como proprietário do aplicativo ou ter privilégios de administrador.

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário no qual deseja registrar o aplicativo.
1. Pesquise e selecione o **Azure Active Directory**. 
1. Em **Gerenciar**, selecione **Registros de aplicativo** e escolha o aplicativo que deseja configurar. Depois de selecionar o aplicativo, você verá a página **Visão Geral** do aplicativo.
1. Na página **Visão Geral**, selecione **Excluir**.
1. Leia as consequências da exclusão.  Se uma caixa for exibida na parte inferior do painel, marque-a.
1. Selecione **Excluir** para confirmar que você deseja excluir o aplicativo.

## <a name="remove-an-application-authored-by-another-organization"></a>Remover um aplicativo criado por outra organização

Se você estiver exibindo **Registros de aplicativo** no contexto de um locatário, um subconjunto dos aplicativos que são exibidos na guia **Todos os aplicativos** são de outro locatário e foram registrados no seu locatário durante o processo de consentimento. Mais especificamente, eles são representados apenas por um objeto de entidade de serviço em seu locatário, sem objeto de aplicativo correspondente. Para saber mais sobre as diferenças entre os objetos de aplicativo e entidade de serviço, confira [Objetos de aplicativo e entidade de serviço no Azure AD](./app-objects-and-service-principals.md).

Para remover o acesso de um aplicativo ao seu diretório (depois de ter dado autorização), o administrador da empresa deverá remover sua entidade de serviço. O administrador precisa ter acesso de Administrador Global e pode remover o aplicativo pelo portal do Azure ou usar os [Cmdlets do PowerShell do Azure AD](/previous-versions/azure/jj151815(v=azure.100)) para remover o acesso.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [objetos de entidade de serviço e aplicativo](app-objects-and-service-principals.md) na plataforma de identidade da Microsoft.
