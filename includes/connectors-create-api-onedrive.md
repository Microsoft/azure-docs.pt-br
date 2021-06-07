---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 07/21/2020
ms.openlocfilehash: 27df83552b450b7657c0595d1d419001df98148c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040226"
---
## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Antes de usar sua conta do OneDrive com aplicativos lógicos, você deve autorizar que os aplicativos lógicos se conectem à sua conta do OneDrive na portal do Azure.

Siga estas etapas para autorizar os aplicativos lógicos a se conectarem à sua conta do OneDrive:  

1. Entre no portal do Azure. 

1. Em **Serviços do Azure**, selecione **aplicativos lógicos**. Em seguida, selecione o nome do seu aplicativo lógico na lista.

1. No menu do aplicativo lógico, selecione **Designer de aplicativo lógico** em **ferramentas de desenvolvimento**.

1. No designer de aplicativos lógicos, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e, em seguida, digite *onedrive* na caixa de pesquisa. Selecione o gatilho ou a ação a ser usada:

   ![Captura de tela do designer de aplicativos lógicos, mostrando a lista de ações da API do OneDrive a serem adicionadas.](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Se você ainda não tiver criado nenhuma conexão com o OneDrive, siga o prompt para entrar usando suas credenciais do OneDrive:  

   ![Captura de tela do designer de aplicativos lógicos, mostrando prompt de entrada para a API do OneDrive.](./media/connectors-create-api-onedrive/onedrive-2.png)

3. Selecione **Entrar** e insira seu nome de usuário e senha. Selecione **entrar**: 

   ![Captura de tela da página de entrada conta Microsoft para autorização da API do OneDrive.](./media/connectors-create-api-onedrive/onedrive-3.png)   

    Essas credenciais são usadas para autorizar seu aplicativo lógico a acessar os dados em sua conta do OneDrive. 

4. Selecione **Sim** para autorizar o aplicativo lógico para usar sua conta do OneDrive:  

   ![Captura de tela de conta Microsoft autorização para aplicativos lógicos, mostrando as ações permitidas.](./media/connectors-create-api-onedrive/onedrive-4.png)   
   
5. A conexão agora está listada na etapa. Selecione salvar e, em seguida, continuar criando seu aplicativo lógico. 

   ![Captura de tela do designer de aplicativos lógicos, mostrando o editor de ações com a conexão de API do OneDrive.](./media/connectors-create-api-onedrive/onedrive-5.png)
