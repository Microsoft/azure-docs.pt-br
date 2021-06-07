---
title: Políticas de segurança e privacidade de dados para o Google Connectors
description: Saiba mais sobre o impacto que as políticas de segurança e privacidade do Google têm em conectores do Google, como Gmail, em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: conceptual
ms.date: 06/05/2020
ms.openlocfilehash: 2a5204be638f108b40e431b148c9cb97788c4a52
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91400750"
---
# <a name="data-security-and-privacy-policies-for-google-connectors-in-azure-logic-apps"></a>Políticas de segurança de dados e privacidade para o Google Connectors em aplicativos lógicos do Azure

A partir de **1º de maio de 2020**, alterações devido às [políticas de privacidade e segurança de dados](https://www.blog.google/technology/safety-security/project-strobe/) do Google podem afetar os fluxos de trabalho do aplicativo lógico que usam o conector do [gmail](/connectors/gmail/). Se seus aplicativos lógicos usam o conector do Gmail com uma conta de consumidor do gmail (endereço de email que termina com @gmail.com ou @googlemail.com ), seus aplicativos lógicos podem usar somente [gatilhos, ações e conectores aprovados pelo Google](#approved-connectors)específicos.

> [!NOTE]
> Se seus aplicativos lógicos usarem o conector do Gmail com uma conta de negócios do G-Suite (endereço de email com um domínio personalizado), seus aplicativos lógicos não serão afetados e não terão restrições quanto ao uso do conector do gmail.

## <a name="affected-logic-apps"></a>Aplicativos lógicos afetados

Se você tiver aplicativos lógicos que usam o conector do Gmail, receberá um email sobre aplicativos lógicos potencialmente afetados. No entanto, a partir de **15 de junho de 2020**, qualquer fluxo de trabalho não compatível será desabilitado. Você pode executar uma destas ações:

* Atualize os aplicativos lógicos afetados seguindo [as etapas neste tópico](#update-affected-workflows). Você precisa criar um aplicativo cliente do Google, que fornece uma ID do cliente e um segredo do cliente que você usa para autenticação em seu gatilho ou ação do gmail.

* Atualize os aplicativos lógicos afetados para que eles usem somente os [conectores aprovados pelo Google](#approved-connectors) antes de reabilitar os aplicativos lógicos desabilitados.

<a name="approved-connectors"></a>

## <a name="google-approved-connectors"></a>Conectores aprovados pelo Google

Nessa política, quando você usa uma conta de consumidor do Gmail, você pode usar o conector do Gmail com apenas serviços específicos do Google aprovados, que estão sujeitos a alterações. Nossas equipes de engenharia continuam trabalhando com o Google para adicionar mais serviços a essa lista. Por enquanto, aqui estão os gatilhos, as ações e os conectores aprovados pelo Google que você pode usar no mesmo fluxo de trabalho do aplicativo lógico com o conector do gmail ao usar uma conta de consumidor do Gmail:

* Gatilhos e ações internos de aplicativos lógicos: lote, controle, operações de dados, data e hora, arquivo simples, Liquid, solicitação, agendamento, variáveis e XML

  Gatilhos internos e ações que não são aprovadas pelo Google, como HTTP, Azure Functions, aplicativos lógicos do Azure e outros, tornam um aplicativo lógico não compatível com o conector do Gmail porque o aplicativo pode enviar ou receber dados de qualquer lugar.

* Serviços do Google: gmail, Google Agenda, contatos do Google, Google Drive, Google sheets e tarefas do Google

* Serviços da Microsoft aprovados: Dynamics 365, Excel online, Microsoft Teams, Microsoft 365, OneDrive e SharePoint Online

* Conectores para fontes de dados gerenciadas pelo cliente: FTP, RSS, SFTP, SMTP e SQL Server

## <a name="non-compliant-examples"></a>Exemplos em não conformidade

Aqui estão alguns exemplos que usam o conector do Gmail com gatilhos e ações internas ou conectores gerenciados que não são aprovados pelo Google:

* Esse aplicativo lógico usa o conector do Gmail com o gatilho interno HTTP:

  ![Aplicativo lógico não compatível-exemplo 1](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-1.png)
  
  O aplicativo lógico também usa o conector do Google Agenda, que é aprovado.

* Este aplicativo lógico usa o conector do Gmail com o conector de armazenamento de BLOBs do Azure:

  ![Aplicativo lógico não compatível-exemplo 2](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-2.png)

* Este aplicativo lógico usa o conector do Gmail com o conector do Twitter:

  ![Aplicativo lógico não compatível-exemplo 3](./media/connectors-google-data-security-privacy-policy/not-compliant-logic-app-3.png)

Para obter as informações mais recentes, consulte a [documentação de referência técnica do conector do Gmail](/connectors/gmail/).

<a name="update-affected-workflows"></a>

## <a name="steps-for-affected-logic-apps"></a>Etapas para aplicativos lógicos afetados

Se você precisar usar o conector do Gmail com uma conta de consumidor do Gmail e conectores não aprovados do Google em um aplicativo lógico, poderá criar seu próprio aplicativo do Google para uso pessoal ou interno em sua empresa. Para este cenário, estas são as etapas de alto nível que você precisa executar:

1. Crie um aplicativo cliente do Google usando o [console de API do Google](https://console.developers.google.com).

1. No conector do Gmail, use os valores de ID do cliente e segredo do cliente do seu aplicativo cliente do Google.

Para obter mais informações, consulte a [documentação de referência técnica do conector do Gmail](/connectors/gmail/#authentication-and-bring-your-own-application).

### <a name="create-google-client-app"></a>Criar aplicativo cliente do Google

Para configurar um projeto para seu aplicativo cliente, use o [Assistente do console de API do Google](https://console.developers.google.com/start/api?id=gmail&credential=client_key) e siga as instruções. Ou, para obter etapas detalhadas, examine as instruções na [documentação de referência técnica do conector do Gmail](/connectors/gmail/#authentication-and-bring-your-own-application).

Quando terminar, a tela será semelhante a este exemplo, exceto que você terá seus próprios valores de **ID de cliente** e de **segredo do cliente** , que você usa posteriormente em seu aplicativo lógico.

![ID do cliente e segredo do cliente para seu aplicativo cliente do Google](./media/connectors-google-data-security-privacy-policy/google-api-console.png)

### <a name="use-client-app-settings-in-logic-app"></a>Usar as configurações do aplicativo cliente no aplicativo lógico

Para usar a ID do cliente e o segredo do cliente do seu aplicativo cliente do Google em seu gatilho ou ação do Gmail, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. Se você estiver adicionando um novo gatilho ou ação do Gmail e criando uma conexão totalmente nova, vá para a próxima etapa. Caso contrário, no gatilho ou na ação do Gmail, selecione **alterar conexão**  >  **Adicionar novo**, por exemplo:

   ![Selecione "alterar conexão" > "Adicionar novo"](./media/connectors-google-data-security-privacy-policy/change-gmail-connection.png)

1. Forneça suas informações de conexão, por exemplo:

   ![Fornecer informações de conexão](./media/connectors-google-data-security-privacy-policy/authentication-type-bring-your-own.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Tipo de Autenticação** | **Traga seu próprio aplicativo** | Especifica que você usará seu próprio aplicativo cliente para autenticação. |
   | **ID do Cliente** | <*client-ID*> | A ID do cliente do seu aplicativo cliente do Google |
   | **Segredo do Cliente** | <*segredo do cliente*> | O segredo do cliente do seu aplicativo cliente do Google |
   ||||

1. Quando terminar, selecione **entrar**.

   Aparece uma página que mostra o aplicativo cliente que você criou. Se você estiver usando uma conta de consumidor do Gmail, poderá obter uma página que mostra seu aplicativo cliente não é verificado pelo Google e solicita que você primeiro permita o acesso à sua conta do Google.

   ![Solicitar acesso à sua conta do Google](./media/connectors-google-data-security-privacy-policy/allow-access-authorized-domain.png)

1. Se necessário, selecione **permitir**.

   Agora você pode usar o conector do Gmail sem restrições em seu aplicativo lógico.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [conector do Gmail](/connectors/gmail/)

