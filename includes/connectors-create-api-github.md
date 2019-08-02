---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: logic-apps
author: MandiOhlinger
ms.service: logic-apps
ms.topic: include
ms.date: 03/02/2018
ms.author: mandia
ms.custom: include file
ms.openlocfilehash: 11280e1678f52ede928cb2a85ea83add222e15fa
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172185"
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

