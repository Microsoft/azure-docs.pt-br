---
title: Conectar a sistemas de arquivos locais – Aplicativos Lógicos do Azure | Microsoft Docs
description: Automatize tarefas e fluxos de trabalho que se conectam a sistemas de arquivos locais com o conector do sistema de arquivos por meio do gateway de dados local nos Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: a933824e9553ee7f638da495b46ebed19e04169d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60846662"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Conectar a sistemas de arquivos locais com os Aplicativos Lógicos do Azure

Com o conector do sistema de arquivos e os Aplicativos Lógicos do Azure, você pode criar tarefas e fluxos de trabalho automatizados que criam e gerenciam arquivos em um compartilhamento de arquivos local, por exemplo:  

- Criar, obter, acrescentar, atualizar e excluir arquivos.
- Listar arquivos em pastas ou pastas raiz.
- Obter conteúdo e metadados do arquivo.

Este artigo mostra como se conectar a um sistema de arquivos local como descrito por este cenário de exemplo: copiar um arquivo carregado no Dropbox para um compartilhamento de arquivos e depois enviar um email. Para se conectar e acessar sistemas locais com segurança, os aplicativos lógicos usam o [gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

Para seguir o exemplo, você precisa destes itens:

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* Antes de poder conectar aplicativos lógicos a sistemas locais, como seu servidor do sistema de arquivos, você precisará [instalar e configurar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md). Dessa forma, você pode especificar para usar sua instalação de gateway quando criar a conexão do sistema de arquivos do seu aplicativo lógico.

* Um [conta do Dropbox](https://www.dropbox.com/), que você pode se inscrever gratuitamente. Suas credenciais de conta são necessárias para a criação de uma conexão entre seu aplicativo lógico e sua conta do Dropbox. 

* Acesso ao computador que tem o sistema de arquivos que você deseja usar. Por exemplo, se você instalar o gateway de dados no mesmo computador que o seu sistema de arquivos, você precisa das credenciais de conta para o computador. 

* Uma conta de email de um provedor compatível com os Aplicativos Lógicos, como o Outlook do Office 365, o Outlook.com ou o Gmail. Para outros provedores, [revise a lista de conectores aqui](https://docs.microsoft.com/connectors/). Esso aplicativo lógico usa uma conta do Outlook do Office 365. Se você usar outra conta de email, as etapas gerais são as mesmos, mas a interface do usuário pode ser ligeiramente diferente. 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md). Neste exemplo, você precisa de um aplicativo lógico em branco.

## <a name="add-trigger"></a>Adicionar gatilho

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Na caixa de pesquisa, insira "dropbox" como o seu filtro. Na lista de gatilhos, selecione este gatilho: **Quando um arquivo é criado**

   ![Selecionar o gatilho do Dropbox](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Entre com suas credenciais de conta do Dropbox e autorize o acesso aos dados do Dropbox para os Aplicativos Lógicos do Azure. 

1. Forneça as informações necessárias para o gatilho.

   ![Gatilho do Dropbox](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Adicionar ações

1. No gatilho, escolha **Próxima etapa**. Na caixa de pesquisa, insira "sistema de arquivos" como filtro. Na lista de ações, selecione esta ação: **Criar arquivo – Sistema de arquivos**

   ![Localizar o conector do sistema de arquivos](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Se você ainda não tiver uma conexão com seu sistema de arquivos, receberá uma solicitação para criar essa conexão.

   ![Criar conexão](media/logic-apps-using-file-connector/file-system-connection.png)

   | Propriedade | Necessário | Value | DESCRIÇÃO | 
   | -------- | -------- | ----- | ----------- | 
   | **Nome da Conexão** | Sim | <*connection-name*> | O nome desejado para a conexão | 
   | **Pasta raiz** | Sim | <*root-folder-name*> | A pasta raiz do sistema de arquivos, por exemplo, se você tiver instalado seu gateway de dados local, como uma pasta local no computador em que o gateway de dados local está instalado ou a pasta de um compartilhamento de rede que o computador pode acessar. <p>Por exemplo: `\\PublicShare\\DropboxFiles` <p>A pasta raiz é a pasta pai principal, que é usada para caminhos relativos de todas as ações relacionadas ao arquivo. | 
   | **Tipo de autenticação** | Não  | <*auth-type*> | O tipo de autenticação usado pelo seu sistema de arquivos, por exemplo, **Windows** | 
   | **Nome de Usuário** | Sim | <*domain*>\\<*username*> | O nome de usuário do computador no qual você tem seu sistema de arquivos | 
   | **Senha** | Sim | <*your-password*> | A senha do computador no qual você tem seu sistema de arquivos | 
   | **gateway** | Sim | <*installed-gateway-name*> | O nome do seu gateway instalado anteriormente | 
   ||| 

1. Quando terminar, escolha **Criar**.

   Os Aplicativos Lógicos configuram e testam a conexão, garantindo seu funcionamento correto. 
   Se a conexão estiver configurada de forma correta, você verá opções para a ação selecionada anteriormente. 

1. Na ação **Criar arquivo**, forneça os detalhes para copiar os arquivos do Dropbox para a pasta raiz no compartilhamento de arquivos local. Para adicionar saídas de etapas anteriores, clique dentro das caixas e selecione os campos disponíveis quando a lista de conteúdo dinâmico for exibida.

   ![Ação Criar arquivo](media/logic-apps-using-file-connector/create-file-filled.png)

1. Agora, adicione uma ação do Outlook que envia um email para que os usuários apropriados saibam sobre o novo arquivo. Insira os destinatários, o título e o corpo do email. Para testar, você pode usar seu próprio endereço de email.

   ![Ação Enviar email](media/logic-apps-using-file-connector/send-email.png)

1. Salve seu aplicativo lógico. Teste o aplicativo carregando um arquivo no Dropbox. 

   Seu aplicativo lógico deve copiar o arquivo para o compartilhamento de arquivos local e enviar aos destinatários um email sobre o arquivo copiado.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/fileconnector/) do conector.

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Para ajudar a melhorar os Aplicativos Lógicos do Azure e conectores, vote ou envie ideias no [site do User Voice dos Aplicativos Lógicos do Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba como [se conectar a dados locais](../logic-apps/logic-apps-gateway-connection.md) 
* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
