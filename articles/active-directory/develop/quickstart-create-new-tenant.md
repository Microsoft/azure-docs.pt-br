---
title: 'Início Rápido: Criar um locatário do Azure Active Directory'
titleSuffix: Microsoft identity platform
description: Neste início rápido, você aprenderá a criar um locatário do Azure Active Directory para usar no desenvolvimento de aplicativos que usam a plataforma de identidade da Microsoft para autenticação e autorização.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 02/15/2021
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: cb4995c0c71d0bcebb3cdd8ec471a92b7820cb9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100535154"
---
# <a name="quickstart-set-up-a-tenant"></a>Início Rápido: Configurar um locatário

Para criar aplicativos que usam a plataforma de identidade da Microsoft para o gerenciamento de identidades e acesso, você precisará ter acesso a um *locatário* do Azure AD (Azure Active Directory). Ele está no locatário do Azure AD que você registra e gerencia seus aplicativos, configura o acesso deles a dados no Microsoft 365 e em outras APIs Web e habilita recursos como o acesso condicional.

Um locatário representa uma organização. É uma instância dedicada do Azure AD que um desenvolvedor de aplicativos ou uma organização recebe no início de um relacionamento com a Microsoft. Esse relacionamento pode começar com a inscrição no Azure, no Microsoft Intune ou no Microsoft 365, por exemplo.

Cada locatário do Azure AD é distinto e separado dos demais locatários do Azure AD. Ele tem uma representação própria de identidades corporativas e de estudante, identidades de consumidor (se ele é um locatário do Azure AD B2C) e Registros de aplicativo. Um registro de aplicativo dentro do locatário só pode permitir autenticações de contas no seu locatário ou em todos os locatários.

## <a name="prerequisites"></a>Pré-requisitos

Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="determining-the-environment-type"></a>Como determinar o tipo de ambiente

Você pode criar dois tipos de ambientes. O ambiente depende exclusivamente dos tipos de usuários que serão autenticados pelo aplicativo. 

Este guia de início rápido aborda dois cenários para o tipo de aplicativo que você deseja criar:

* Contas corporativas ou de estudante (contas do Azure AD) ou contas Microsoft (como Outlook.com e Live.com)
* Contas sociais e locais (Azure AD B2C)

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Contas corporativas ou de estudante ou contas pessoais da Microsoft

Para criar um ambiente para contas corporativas e de estudante ou contas Microsoft pessoais, use um locatário existente do Azure AD ou crie um.
### <a name="use-an-existing-azure-ad-tenant"></a>Usar um locatário existente do Azure Active Directory

Muitos desenvolvedores já contam com locatários por meio de serviços ou assinaturas vinculadas a locatários do Azure AD, como assinaturas do Microsoft 365 ou do Azure.

Para verificar o locatário:

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>. Use a conta que você usará para gerenciar seu aplicativo.
1. Verifique o canto superior direito. Se você tiver um locatário, será automaticamente conectado. Você verá o nome do locatário diretamente abaixo do nome da sua conta.
   * Posicione o cursor sobre o nome da conta para ver seu nome, o endereço de email, o diretório ou a ID do locatário (um GUID) e um domínio.
   * Se sua conta estiver associada a vários locatários, você pode selecionar o nome da sua conta para abrir um menu no qual você pode alternar entre locatários. Cada locatário tem sua própria ID exclusiva.

> [!TIP]
> Para localizar a ID do locatário, você pode:
> * Posicionar o cursor sobre o nome da sua conta para obter o diretório ou a ID do locatário.
> * Pesquisar e selecionar **Azure Active Directory** > **Propriedades** > **ID do Locatário** no portal do Azure.

Caso não tenha um locatário associado à sua conta, você verá um GUID abaixo do nome dela. Você não poderá executar ações como registrar aplicativos até criar um locatário do Azure AD.

### <a name="create-a-new-azure-ad-tenant"></a>Criar um novo locatário do Azure Active Directory

Se você ainda não tiver um locatário do Azure AD ou quiser criar um para desenvolvimento, confira [Criar um locatário no Azure AD](../fundamentals/active-directory-access-create-new-tenant.md). Ou, então, use a [experiência de criação de diretório](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) no portal do Azure. 

Você fornecerá as seguintes informações para criar seu locatário:

- **Nome da organização**
- **Domínio inicial**: esse domínio faz parte de *.onmicrosoft.com. Você poderá personalizar o domínio mais tarde.
- **País ou região**

> [!NOTE]
> Ao nomear seu locatário, use caracteres alfanuméricos. Caracteres especiais não são permitidos. O nome não pode ultrapassar 256 caracteres.

## <a name="social-and-local-accounts"></a>Contas locais e sociais

Para começar a criar aplicativos que entram em contas sociais e locais, crie um locatário do Azure AD B2C. Para começar, confira [Criar um locatário do Azure AD B2C](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registre um aplicativo](quickstart-register-app.md) e integre à plataforma de identidade da Microsoft.
