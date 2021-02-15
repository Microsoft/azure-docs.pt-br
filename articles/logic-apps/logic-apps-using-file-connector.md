---
title: Conectar-se a sistemas de arquivos locais
description: Automatize tarefas e fluxos de trabalho que se conectam a sistemas de arquivos locais com o conector do sistema de arquivos por meio do gateway de dados local nos Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 4715d7173dd959d12350229e457717c908a83756
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91873227"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Conectar a sistemas de arquivos locais com os Aplicativos Lógicos do Azure

Com os aplicativos lógicos do Azure e o conector do sistema de arquivos, você pode criar tarefas e fluxos de trabalho automatizados que criam e gerenciam arquivos em um compartilhamento de arquivos local, por exemplo:

- Criar, obter, acrescentar, atualizar e excluir arquivos.
- Listar arquivos em pastas ou pastas raiz.
- Obter conteúdo e metadados do arquivo.

  > [!IMPORTANT]
  > Atualmente, o conector do sistema de arquivos só dá suporte a sistemas de arquivos Windows em sistemas operacionais Windows.  

Este artigo mostra como se conectar a um sistema de arquivos local como descrito por este cenário de exemplo: copiar um arquivo carregado no Dropbox para um compartilhamento de arquivos e depois enviar um email. Para se conectar e acessar sistemas locais com segurança, os aplicativos lógicos usam o [gateway de dados local](../logic-apps/logic-apps-gateway-connection.md). Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure?](../logic-apps/logic-apps-overview.md). Para obter informações técnicas específicas do conector, consulte a [referência do conector do sistema de arquivos](/connectors/filesystem/).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Antes de poder conectar aplicativos lógicos a sistemas locais, como seu servidor do sistema de arquivos, você precisará [instalar e configurar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md). Dessa forma, você pode especificar para usar sua instalação de gateway quando criar a conexão do sistema de arquivos do seu aplicativo lógico.

* Uma [conta do Dropbox](https://www.dropbox.com/), que você pode assinar gratuitamente. Suas credenciais de conta são necessárias para criar uma conexão entre seu aplicativo lógico e sua conta do dropbox.

* Acesso ao computador que tem o sistema de arquivos que você deseja usar. Por exemplo, se você instalar o gateway de dados no mesmo computador que o sistema de arquivos, precisará das credenciais de conta para esse computador.

* Uma conta de email de um provedor compatível com os Aplicativos Lógicos, como o Outlook do Office 365, o Outlook.com ou o Gmail. Para outros provedores, [revise a lista de conectores aqui](/connectors/). Esse aplicativo lógico usa uma conta corporativa ou de estudante. Se você usar outra conta de email, as etapas gerais são as mesmos, mas a interface do usuário pode ser ligeiramente diferente.

  > [!IMPORTANT]
  > Se você quiser usar o conector do Gmail, somente as contas comerciais do G Suite poderão usar esse conector sem restrição nos aplicativos lógicos. Se você tiver uma conta de consumidor do Gmail, poderá usar esse conector somente com serviços específicos do Google aprovados ou poderá [criar um aplicativo cliente do Google para usar para autenticação com o conector do Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Para obter mais informações, confira [Políticas de privacidade e segurança de dados para os conectores do Google nos Aplicativos Lógicos do Azure](../connectors/connectors-google-data-security-privacy-policy.md).

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

1. No gatilho, escolha **Próxima etapa**. Na caixa de pesquisa, insira "sistema de arquivos" como filtro. Na lista ações, selecione esta ação: **criar arquivo**

   ![Localizar o conector do sistema de arquivos](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Se você ainda não tiver uma conexão com seu sistema de arquivos, receberá uma solicitação para criar essa conexão.

   ![Criar conexão](media/logic-apps-using-file-connector/file-system-connection.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   | -------- | -------- | ----- | ----------- |
   | **Nome da conexão** | Sim | <*nome da conexão*> | O nome desejado para a conexão |
   | **Pasta raiz** | Sim | <*nome da pasta raiz*> | A pasta raiz do sistema de arquivos, por exemplo, se você tiver instalado seu gateway de dados local, como uma pasta local no computador em que o gateway de dados local está instalado ou a pasta de um compartilhamento de rede que o computador pode acessar. <p>Por exemplo: `\\PublicShare\\DropboxFiles` <p>A pasta raiz é a pasta pai principal, que é usada para caminhos relativos de todas as ações relacionadas ao arquivo. |
   | **Tipo de autenticação** | Não | <*tipo de autenticação*> | O tipo de autenticação que o sistema de arquivos usa: **Windows** |
   | **Nome de usuário** | Sim | <*domínio* > \\ do < *nome de usuário*> <p>-ou- <p><*computador* > \\ local < *nome de usuário*> | O nome de usuário do computador em que você tem a pasta do sistema de arquivos. <p>Se a sua pasta do sistema de arquivos estiver no mesmo computador que o gateway de dados local, você poderá usar <nome de usuário de *computadores locais* > \\ < *username*>. |
   | **Senha** | Sim | <*sua senha*> | A senha do computador no qual você tem seu sistema de arquivos |
   | **Gateway** | Sim | <*installed-gateway-Name*> | O nome do seu gateway instalado anteriormente |
   |||||

1. Quando terminar, escolha **Criar**.

   Os Aplicativos Lógicos configuram e testam a conexão, garantindo seu funcionamento correto. Se a conexão estiver configurada de forma correta, você verá opções para a ação selecionada anteriormente.

1. Na ação **Criar arquivo**, forneça os detalhes para copiar os arquivos do Dropbox para a pasta raiz no compartilhamento de arquivos local. Para adicionar saídas de etapas anteriores, clique dentro das caixas e selecione os campos disponíveis quando a lista de conteúdo dinâmico for exibida.

   ![Ação Criar arquivo](media/logic-apps-using-file-connector/create-file-filled.png)

1. Agora, adicione uma ação do Outlook que envia um email para que os usuários apropriados saibam sobre o novo arquivo. Insira os destinatários, o título e o corpo do email. Para testar, você pode usar seu próprio endereço de email.

   ![Ação Enviar email](media/logic-apps-using-file-connector/send-email.png)

1. Salve seu aplicativo lógico. Teste o aplicativo carregando um arquivo no Dropbox.

   Seu aplicativo lógico deve copiar o arquivo para o compartilhamento de arquivos local e enviar aos destinatários um email sobre o arquivo copiado.

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre esse conector, como gatilhos, ações e limites, conforme descrito pelo arquivo Swagger do conector, consulte a [página de referência do conector](/connectors/fileconnector/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), a versão rotulada do ISE do conector usa os [limites de mensagem do ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) em vez disso.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [se conectar a dados locais](../logic-apps/logic-apps-gateway-connection.md) 
* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
