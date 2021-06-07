---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 06a503e800309d8689735417931820d9f16a6e75
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87102442"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta [do onedrive for Business](https://OneDrive.com) 

Antes de usar sua conta do OneDrive for Business com aplicativos lógicos, você deve autorizar os aplicativos lógicos a se conectarem à sua conta do OneDrive for Business no portal do Azure.

Siga estas etapas para autorizar aplicativos lógicos a se conectarem à sua conta do OneDrive for Business:  

1. Entre no portal do Azure. 

1. Em **Serviços do Azure**, selecione **aplicativos lógicos**. Em seguida, selecione o nome do seu aplicativo lógico na lista.

1. No menu do aplicativo lógico, selecione **Designer de aplicativo lógico** em **ferramentas de desenvolvimento**.

1. No designer de aplicativos lógicos, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e, em seguida, insira *onedrive for Business* na caixa de pesquisa. Selecione o gatilho ou a ação a ser usada:  

   ![Captura de tela do designer de aplicativos lógicos mostrando o gatilho de recorrência com as ações da API do OneDrive for Business.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-1.png)

2. Se você ainda não tiver criado nenhuma conexão com o OneDrive for Business antes, siga o prompt para fornecer suas credenciais do OneDrive for Business. Essas credenciais são usadas para autorizar seu aplicativo lógico a acessar os dados da sua conta do OneDrive for Business:  

   ![Captura de tela do designer de aplicativos lógicos, mostrando prompt de entrada para o OneDrive for Business.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-2.png)

3. Forneça seu nome de usuário e senha do OneDrive for Business para autorizar seu aplicativo lógico:  

   ![Captura de tela da página de entrada do OneDrive for Business, mostrando o prompt de entrada.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-3.png)   

4. A conexão agora está listada na etapa. Selecione salvar e, em seguida, continuar criando seu aplicativo lógico. 

   ![Captura de tela do designer de aplicativos lógicos, mostrando o gatilho com a conexão do OneDrive for Business listada.](./media/connectors-create-api-onedriveforbusiness/onedriveforbusiness-4.png)   
