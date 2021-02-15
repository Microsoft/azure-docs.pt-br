---
title: Introdução à autenticação do Azure AD
description: Saiba como acessar a autenticação do Azure Active Directory (Azure AD) para consumir a API dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 00808c25ac84da852cce6169fb210767ee2b56cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89265874"
---
# <a name="get-credentials-to-access-media-services-api"></a>Obter credenciais para acessar a API dos Serviços de Mídia

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ao usar a autenticação do Azure AD para acessar a API dos Serviços de Mídia do Azure, você tem duas opções de autenticação:

- **Autenticação de entidade de serviço** (recomendada)

    Autentica um serviço. Os aplicativos que geralmente usam esse método de autenticação são aplicativos que executam serviços de daemon, serviços de camada intermediária ou trabalhos agendados: aplicativos Web, aplicativos de funções, aplicativos lógicos, APIs ou um microsserviço.
- **Autenticação de usuário**

    Autentica uma pessoa que está usando o aplicativo para interagir com os recursos dos Serviços de Mídia. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais. Um exemplo é um aplicativo de console de gerenciamento usado por usuários autorizados para monitorar trabalhos de codificação ou uma transmissão ao vivo. 

Este artigo descreve as etapas para obter credenciais para acessar a API dos Serviços de Mídia. Escolha entre as seguintes guias.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você não tiver uma conta, comece com uma [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Serviços de Mídia. Para obter mais informações, consulte [Criar uma conta dos Serviços de Mídia do Azure usando o portal do Azure](create-account-howto.md).

## <a name="portal"></a>[Portal](#tab/portal/)

### <a name="api-access"></a>Acesso à API

A página **acesso à API** permite selecionar o método de autenticação que você deseja usar para se conectar à API. A página também fornece os valores necessários para se conectar à API.

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia.
2. Selecione a folha **acesso à API (novo)** na barra de navegação à esquerda.
3. Em **Conectar à API dos Serviços de Mídia**, selecione a versão da API dos Serviços de Mídia à qual você deseja se conectar (v3 é a versão mais recente do serviço).

### <a name="service-principal-authentication--recommended"></a>Autenticação de entidade de serviço (recomendada)

Autentica um serviço usando um aplicativo e segredo do Azure Active Directory (Azure AD). Isso é recomendado para qualquer serviço de camada intermediária que chame a API de Serviços de Mídia. Entre os exemplos estão aplicativos Web, funções, Aplicativos Lógicos, APIs e microsserviços. Este é o método de autenticação recomendado.

#### <a name="manage-your-azure-ad-app-and-secret"></a>Gerenciar seu aplicativo e segredo do Azure AD

A seção **Gerenciar seu aplicativo e segredo do ADD** permite selecionar ou criar um novo aplicativo do Azure AD e gerar um segredo. Por motivos de segurança, o segredo não pode ser mostrado depois que a folha é fechada. O aplicativo usa a ID e o segredo do aplicativo para autenticação para obter um token válido para serviços de mídia.

Verifique se você tem permissões suficientes para registrar um aplicativo com o seu locatário do Azure AD e para atribuir o aplicativo a uma função na sua assinatura do Azure. Para obter mais informações, consulte [Permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

#### <a name="connect-to-media-services-api"></a>Conecte-se à API de Serviços de Mídia

O **Conecte-se à API de Serviços de Mídia** fornece valores que você usa para conectar seu aplicativo de entidade de serviço. É possível obter valores de texto ou copiar os blocos JSON ou XML.

### <a name="user-authentication"></a>Autenticação de usuário

Essa opção pode ser usada para autenticar um funcionário ou membro de um Azure Active Directory que está usando um aplicativo para interagir com os recursos de Serviços de Mídia. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais do usuário. Este método de autenticação deve ser usado apenas para aplicativos de gerenciamento.

#### <a name="connect-to-media-services-api"></a>Conecte-se à API de Serviços de Mídia

Copie as credenciais para conectar seu aplicativo de usuário na seção  **Conecte-se à API de Serviços de Mídia**. É possível obter valores de texto ou copiar os blocos JSON ou XML.

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

---

## <a name="next-steps"></a>Próximas etapas

[Tutorial: Fazer upload, codificar e transmitir vídeos com os Serviços de Mídia v3](stream-files-tutorial-with-api.md).
