---
title: Tutorial do aplicativo Web – Configuração do aplicativo cliente
description: Este tutorial apresenta as etapas para registrar um aplicativo público para preparar-se para implantar um aplicativo Web
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bf357dcc9c4790632a1131278edc41c13ac810b1
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/29/2020
ms.locfileid: "97802602"
---
# <a name="client-application-registration"></a>Registro de aplicativo cliente
No tutorial anterior, você implantou e configurou sua API do Azure para FHIR. Agora que você tem a configuração da API do Azure para FHIR, vamos registrar um aplicativo cliente público. Você pode ler todo o guia de instruções passo a passo para [registrar um aplicativo cliente público](register-public-azure-ad-client-app.md) para obter mais detalhes ou solução de problemas, mas nós apresentamos as principais etapas para este tutorial abaixo.

1. Navegue para o Azure Active Directory
1. Selecione **Registro de Aplicativo** --> **Novo Registro**
1. Nomeie seu aplicativo
1. Selecione **Cliente público/nativo (móvel e área de trabalho)** e defina o URI de redirecionamento como `https://www.getpostman.com/oauth2/callback`.

   :::image type="content" source="media/tutorial-web-app/register-public-app.png" alt-text="Captura de tela do painel Registrar um aplicativo e um exemplo de nome de aplicativo e URL de redirecionamento.":::

## <a name="client-application-settings"></a>Configurações de aplicativo cliente

Depois que o aplicativo cliente for registrado, copie a ID do Aplicativo (cliente) e a ID do Locatário da página Visão Geral. Você precisará desses dois valores posteriormente ao acessar o cliente.

:::image type="content" source="media/tutorial-web-app/client-id-tenant-id.png" alt-text="Captura de tela do painel de configurações do aplicativo cliente, com as IDs do aplicativo e do diretório realçadas.":::

### <a name="connect-with-web-app"></a>Conectar-se ao aplicativo Web

Se você [escreveu seu aplicativo Web](tutorial-web-app-write-web-app.md) para se conectar à API do Azure para FHIR, também precisa definir as opções de autenticação corretas. 

1. No menu à esquerda, em **Gerenciar**, selecione **Autenticação**. 

1. Para adicionar uma nova configuração de plataforma, selecione **Web**.

1. Configure o URI de redirecionamento em preparação para quando criar seu aplicativo Web na quarta parte deste tutorial. Para fazer isso, adicione `https://\<WEB-APP-NAME>.azurewebsites.net` à lista de URI de redirecionamento. Se você escolher um nome diferente durante a etapa de [gravar seu aplicativo Web](tutorial-web-app-write-web-app.md), será necessário voltar e atualizar isso.

1. Marque as caixas de seleção **Token de Acesso** e **Token de ID**.

   :::image type="content" source="media/tutorial-web-app/web-app-authentication.png" alt-text="Captura de tela da folha Configurações de autenticação do aplicativo, com as etapas usadas para adicionar uma plataforma realçadas.":::

## <a name="add-api-permissions"></a>Adicionar permissões de API

Agora que você configurou a autenticação correta, defina as permissões de API:

1. Selecione **Permissões de API** e clique em **Adicionar uma permissão**.
1. Em **APIs que a minha organização usa**, procure as APIs de Serviços de Saúde do Azure.
1. Selecione **user_impersonation** e clique em **Adicionar permissões**.

:::image type="content" source="media/tutorial-web-app/api-permissions.png" alt-text="Captura de tela da folha Adicionar permissões de API, com as etapas para adicionar permissões de API realçadas.":::

## <a name="next-steps"></a>Próximas etapas
Agora você tem um aplicativo cliente público. No próximo tutorial, veremos como testar e obter acesso a esse aplicativo por meio do Postman.

>[!div class="nextstepaction"]
>[Testar aplicativo cliente no Postman](tutorial-web-app-test-postman.md)
