---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999528"
---
1. No [portal do Azure](https://portal.azure.com), crie um aplicativo lógico em branco. 

2. Mo Designer de Aplicativos Lógicos, insira "github" como filtro. 

3. Selecione o conector GitHub e o gatilho que você quer usar.

   ![Selecionar o conector GitHub e um gatilho](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Todos os fluxos de trabalho de aplicativo lógico devem iniciar com um gatilho. Você poderá selecionar ações somente quando a lógica de fluxo de trabalho iniciar com um gatilho. 

4. Se uma conexão não foi criada anteriormente, escolha **Entrar** para que você possa fornecer as credenciais do GitHub quando for solicitado.  

   ![Entrar com as credenciais do GitHub](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   O aplicativo lógico usa essas credenciais para autorizar a conexão e o acesso a dados da sua conta do GitHub. 

5. Forneça o nome de usuário e senha do GitHub e confirme sua autorização.

   ![Fornecer credenciais e confirmar autorização](./media/connectors-create-api-github/github-connector-authorize.png)   

   Sua conexão é criada no Portal do Azure e está pronta para uso.

6. Continue definindo o fluxo de trabalho do aplicativo lógico.

   ![Adicionar mais ações ao fluxo de trabalho do aplicativo lógico](./media/connectors-create-api-github/github-connector-logic-app.png)

