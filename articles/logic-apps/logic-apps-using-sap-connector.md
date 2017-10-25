---
title: "Conectar a um sistema SAP local no Aplicativo Lógico do Azure | Microsoft Docs"
description: "Conectar ao sistema SAP local no fluxo de trabalho do aplicativo lógico por meio do gateway de dados local"
services: logic-apps
author: padmavc
manager: anneta
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3fea93f558d5a4ef62550fd1f6486903cb812930
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-to-an-on-premises-sap-system-from-logic-apps-with-the-sap-connector"></a>Conectar a um sistema SAP local por meio de aplicativos lógicos com o conector do SAP 

O gateway de dados local permite gerenciar dados e acessar com segurança recursos locais. Este tópico mostra como é possível conectar aplicativos lógicos a um sistema SAP local. Neste exemplo, o aplicativo lógico solicita um IDOC por HTTP e envia a resposta de volta.    

> [!NOTE]
> Limitações atuais: 
> - O aplicativo lógico atingirá o tempo limite se todas as etapas necessárias para a resposta não forem concluídas dentro do [tempo limite de solicitação](./logic-apps-limits-and-config.md). Nesse cenário, as solicitações podem ser bloqueadas. 
> - O seletor de arquivos não exibe todos os campos disponíveis. Nesse cenário, você pode adicionar os caminhos manualmente.

## <a name="prerequisites"></a>Pré-requisitos

- Instale e configure a versão mais recente do [gateway de dados local](https://www.microsoft.com/download/details.aspx?id=53127), versão 1.15.6150.1 ou superior. [Como conectar-se ao gateway de dados local em um aplicativo lógico](http://aka.ms/logicapps-gateway) lista as etapas. O gateway deve ser instalado em um computador local antes de prosseguir.

- Baixe e instale a biblioteca de cliente SAP mais recente no mesmo computador em que você instalou o gateway de dados. Use qualquer uma das versões de SAP a seguir: 
    - Servidor SAP
        - Qualquer Servidor SAP compatível com o .NET Connector (NCo) 3.0
 
    - Cliente SAP
        - Conector SAP do .NET (NCo) 3.0

## <a name="add-triggers-and-actions-for-connecting-to-your-sap-system"></a>Adicionar gatilhos e ações para conectar ao sistema SAP

O conector do SAP tem ações, mas não gatilhos. Portanto, precisamos usar outro gatilho no início do fluxo de trabalho. 

1. Adicione o gatilho Solicitação/Resposta e, em seguida, selecione **Nova etapa**.

2. Selecione **Adicionar uma ação** e, em seguida, escolha o conector SAP digitando `SAP` no campo de pesquisa:    

     ![Selecionar Servidor de Aplicativos SAP ou Servidor de Mensagens SAP](media/logic-apps-using-sap-connector/sap-action.png)

3. Selecione [**Servidor de Aplicativos SAP**](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) ou [**Servidor de Mensagens SAP**](http://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm), de acordo com a configuração do SAP. Caso não tenha uma conexão existente, é solicitado que você crie uma.

   1. Selecione **Conectar-se por meio do gateway de dados local** e insira os detalhes do sistema SAP:   

       ![Adicionar cadeia de conexão ao SAP](media/logic-apps-using-sap-connector/picture2.png)  

   2. Em **Gateway**, selecione um gateway existente ou, para instalar um novo gateway, selecione **Instalar Gateway**.

        ![Instalar um novo gateway](media/logic-apps-using-sap-connector/install-gateway.png)
  
   3. Após inserir todos os detalhes, selecione **Criar**. 
   Os Aplicativos Lógicos configuram e testam a conexão, garantindo seu funcionamento correto.

4. Insira um nome para a conexão SAP.

5. Agora, as diferentes opções de SAP estão disponíveis. Para localizar a categoria do IDOC, selecione uma na lista. Ou digite manualmente o caminho e selecione a resposta HTTP no campo **corpo**:

     ![Ação do SAP](media/logic-apps-using-sap-connector/picture3.png)

6. Adicione a ação para criar uma **resposta HTTP**. A mensagem de resposta deve se originar da saída do SAP.

7. Salve seu aplicativo lógico. Teste-o enviando um IDOC por meio da URL de gatilho HTTP. Após o envio do IDOC, aguarde a resposta do aplicativo lógico:   

     > [!TIP]
     > Confira como [monitorar seus Aplicativos Lógicos](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Com o conector SAP adicionado ao aplicativo lógico, comece a explorar outras funcionalidades:

- BAPI
- RFC

## <a name="get-help"></a>Obter ajuda

Para fazer perguntas, responder a perguntas e saber o que os outros usuários dos Aplicativos Lógicos do Azure estão fazendo, visite o [fórum de Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ajudar a melhorar os Aplicativos Lógicos do Azure e conectores, vote ou envie ideias no [site de comentários do usuário dos Aplicativos Lógicos do Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

- Saiba como validar, transformar e outras funções semelhantes ao BizTalk no [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). 
- [Conectar-se a dados locais](../logic-apps/logic-apps-gateway-connection.md) por meio de aplicativos lógicos
