---
title: Introdução à autenticação do Azure AD utilizando o Portal do Azure | Microsoft Docs
description: Saiba como utilizar o portal do Azure para acessar a autenticação do Azure Active Directory (Azure AD) para consumir a API dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: d9ad439fe3f41d9e2634fbf9a76cfd21114a5dbc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013050"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Introdução à autenticação do Azure AD utilizando o Portal do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](../latest/index.yml). Além disso, consulte s [diretrizes de migração da v2 para a v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Saiba como utilizar o portal do Azure para acessar a autenticação do Azure Active Directory (Azure AD) para acessar a API dos Serviços de Mídia do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure. Se você não tiver uma conta, comece com uma [avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Uma conta dos Serviços de Mídia. Para obter mais informações, consulte [Criar uma conta dos Serviços de Mídia do Azure usando o portal do Azure](media-services-portal-create-account.md).

Quando você usar a autenticação do AD Azure com Serviços de Mídia do Azure haverá duas opções de autenticação:

- **Autenticação da entidade de serviço**. Autentica um serviço. Os aplicativos que geralmente usam esse método de autenticação são aplicativos que executam serviços de daemon, serviços de camada intermediária ou trabalhos agendados: aplicativos Web, aplicativos de funções, aplicativos lógicos, APIs ou um microsserviço.
- **Autenticação do usuário**. Autentica uma pessoa que está usando o aplicativo para interagir com os recursos dos Serviços de Mídia. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais. Um exemplo é um aplicativo de console de gerenciamento usado por usuários autorizados para monitorar trabalhos de codificação ou uma transmissão ao vivo. 

## <a name="access-the-media-services-api"></a>Instalar a API de Serviços de Mídia

Esta página permite que você selecione o método de autenticação que deseja usar para se conectar à API. A página também fornece os valores necessários para se conectar à API.

1. No [Portal do Azure](https://portal.azure.com/), selecione sua conta dos Serviços de Mídia.
2. Selecione como se conectar à API dos Serviços de Mídia.
3. Em **conectar à API dos serviços de mídia**, selecione a versão da API dos serviços de mídia à qual você deseja se conectar.

## <a name="service-principal-authentication--recommended"></a>Autenticação de entidade de serviço (recomendada)

Autentica um serviço usando um aplicativo e segredo do Azure Active Directory (Azure AD). Isso é recomendado para qualquer serviço de camada intermediária que chame a API de Serviços de Mídia. Entre os exemplos estão aplicativos Web, funções, Aplicativos Lógicos, APIs e microsserviços. Este é o método de autenticação recomendado.

### <a name="manage-your-azure-ad-app-and-secret"></a>Gerenciar seu aplicativo e segredo do Azure AD

A seção **Gerenciar seu aplicativo e segredo do ADD** permite selecionar ou criar um novo aplicativo do Azure AD e gerar um segredo. Por motivos de segurança, o segredo não pode ser mostrado depois que a folha é fechada. O aplicativo usa a ID e o segredo do aplicativo para autenticação para obter um token válido para serviços de mídia.

Verifique se você tem permissões suficientes para registrar um aplicativo com o seu locatário do Azure AD e para atribuir o aplicativo a uma função na sua assinatura do Azure. Para obter mais informações, consulte [Permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

### <a name="connect-to-media-services-api"></a>Conecte-se à API de Serviços de Mídia

O **Conecte-se à API de Serviços de Mídia** fornece valores que você usa para conectar seu aplicativo de entidade de serviço. É possível obter valores de texto ou copiar os blocos JSON ou XML.

## <a name="user-authentication"></a>Autenticação de usuário

Essa opção pode ser usada para autenticar um funcionário ou membro de um Azure Active Directory que está usando um aplicativo para interagir com os recursos de Serviços de Mídia. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais do usuário. Este método de autenticação deve ser usado apenas para aplicativos de gerenciamento.

### <a name="connect-to-media-services-api"></a>Conecte-se à API de Serviços de Mídia

Copie as credenciais para conectar seu aplicativo de usuário na seção  **Conecte-se à API de Serviços de Mídia**. É possível obter valores de texto ou copiar os blocos JSON ou XML.

## <a name="next-steps"></a>Próximas etapas

Comece a [carregar arquivos para sua conta](media-services-portal-upload-files.md).
