---
title: Conectar-se ao servidor FTP
description: Automatizar tarefas e fluxos de trabalho que criam, monitoram e gerenciam arquivos em um servidor FTP usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 12/15/2019
tags: connectors
ms.openlocfilehash: f4cad2b658547d56d00efdd5e1496110f8e4a5e6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "95999563"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Criar, monitorar e gerenciar arquivos FTP usando Aplicativos Lógicos do Azure

Com o conector FTP e dos Aplicativos Lógicos do Azure, você pode criar tarefas automatizadas e fluxos de trabalho que criar, monitoram, enviam e recebem arquivos por meio de sua conta em um servidor FTP, juntamente com outras ações, por exemplo:

* Monitorar quando arquivos são adicionados ou alterados.
* Obter, criar, copiar, atualizar, lista e excluir arquivos.
* Obter conteúdo e metadados do arquivo.
* Extrair o arquivo para pastas.

Você pode usar gatilhos que obtêm respostas de seu servidor FTP e disponibilizar a saída para outras ações. Use ações de execução nos aplicativos lógicos para gerenciar arquivos no servidor FTP. Você também pode ter outro uso de ações para usar a saída das ações do FTP. Por exemplo, se você obter arquivos regularmente do servidor FTP, poderá enviar um email sobre esses arquivos e seu conteúdo usando o conector do Outlook do Office 365 ou do Outlook.com. Se você for novo em aplicativos lógicos, examine [o que são os aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md).

## <a name="limitations"></a>Limitações

* O conector de FTP dá suporte apenas a FTP explícito sobre TLS/SSL (FTPS) e não é compatível com FTPS implícita.

* Por padrão, as ações de FTP podem ler ou gravar arquivos que são *50 MB ou menores*. Para lidar com arquivos com mais de 50 MB, as ações de FTP dão suporte a [agrupamento de mensagens](../logic-apps/logic-apps-handle-large-messages.md). A ação **obter conteúdo do arquivo** usa implicitamente o agrupamento.

* Os gatilhos de FTP não dão suporte a agrupamentos. Ao solicitar o conteúdo do arquivo, os gatilhos selecionam apenas os arquivos que são 50 MB ou menores. Para obter arquivos maiores que 50 MB, siga este padrão:

  * Use um gatilho de FTP que retorne Propriedades de arquivo, como **quando um arquivo é adicionado ou modificado (somente Propriedades)**.

  * Siga o gatilho com a ação FTP **Get file Content** , que lê o arquivo completo e usa o agrupamento implicitamente.

* Se você tiver um servidor FTP local, considere criar um [ISE (ambiente do serviço de integração)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) ou usar [Azure app conexões híbridas do serviço](../app-service/app-service-hybrid-connections.md), que permitem que você acesse fontes de dados locais sem usar um gateway de dados local.

## <a name="how-ftp-triggers-work"></a>Como funcionam os gatilhos de FTP

Os gatilhos de FTP funcionam sondando o sistema de arquivos FTP e procurando qualquer arquivo que tenha sido alterado desde a última sondagem. Algumas ferramentas permitem preservar o registro de data e hora quando os arquivos são alterados. Nesses casos, você precisa desativar esse recurso para que seu gatilho funcione. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| WinSCP | Vá para **Opções**  >  **preferências**  >  **transferir**  >  **Editar**  >  **preservar carimbo de data/hora**  >  **desabilitar** |
| FileZilla | Ir para **transferir**  >  **preservar carimbos de data/hora dos arquivos transferidos**  >  **desabilitar** |
|||

Quando um gatilho encontra um novo arquivo, o gatilho verifica se ele está concluído e não gravado parcialmente. Por exemplo, um arquivo pode ter alterações em andamento quando o gatilho verifica o servidor de arquivos. Para evitar o retorno de um arquivo gravado parcialmente, o gatilho observa o carimbo de data/hora do arquivo que tem alterações recentes, mas não retorna o arquivo imediatamente. O gatilho retorna o arquivo apenas ao executar a sondagem do servidor novamente. Às vezes, esse comportamento pode causar um atraso que é até duas vezes o intervalo de sondagem do gatilho.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Seu endereço de servidor de host FTP e credenciais de conta

  O conector de FTP exige que o servidor FTP seja acessível da internet e configure para operar no modo *passivo*. Suas credenciais permitem que o aplicativo lógico crie uma conexão e acesse sua conta do FTP.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar sua conta FTP. Para iniciar com um gatilho de FTP [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação FTP, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho **Recorrência**.

## <a name="connect-to-ftp"></a>Conectar-se ao FTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com)e abra seu aplicativo lógico no designer de aplicativo lógico.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira `ftp` como seu filtro. Na lista de **gatilhos** , selecione o gatilho desejado.

   - ou -

   Para os aplicativos lógicos existentes, na última etapa em que você deseja adicionar uma ação, selecione **nova etapa** e, em seguida, selecione **Adicionar uma ação**. Na caixa de pesquisa, insira `ftp` como o filtro. Na lista **ações** , selecione a ação desejada.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Selecione o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Forneça suas informações de conexão e selecione **criar**.

1. Forneça as informações para o gatilho ou ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="add-ftp-trigger"></a>Adicionar gatilho de FTP

O gatilho **quando um arquivo é adicionado ou modificado (somente Propriedades)** inicia um fluxo de trabalho do aplicativo lógico quando o gatilho detecta que um arquivo é adicionado ou alterado em um servidor FTP. Por exemplo, você pode adicionar uma condição que verifica o conteúdo do arquivo e decide se deseja obter esse conteúdo, com base em se esse conteúdo atende a uma condição especificada. Por fim, você pode adicionar uma ação que obtém o conteúdo do arquivo e colocar esse conteúdo em uma pasta diferente no servidor SFTP.

Por exemplo, você pode usar esse gatilho para monitorar uma pasta de FTP em busca de novos arquivos que descrevem pedidos de clientes. Você pode usar uma ação de FTP como **obter metadados de arquivo** para obter as propriedades desse novo arquivo e, em seguida, usar **obter conteúdo do arquivo** para obter o conteúdo desse arquivo para processamento adicional e armazenar esse pedido em um banco de dados de pedidos.

Aqui está um exemplo que mostra como usar o gatilho **quando um arquivo é adicionado ou modificado (somente Propriedades)** .

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira `ftp` como seu filtro. Na lista de gatilhos, selecione este gatilho: **quando um arquivado é adicionado ou modificado (somente Propriedades)**

   ![Localizar e selecionar o gatilho de FTP](./media/connectors-create-api-ftp/select-ftp-trigger-logic-app.png)

1. Forneça os detalhes necessários para sua conexão e, em seguida, selecione **criar**.

   Por padrão, esse conector transfere arquivos no formato de texto. Para transferir arquivos no formato binário, por exemplo, nos casos em que a codificação é usada, selecione **Transporte Binário**.

   ![Criar conexão com o servidor FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)

1. Na caixa **pasta** , selecione o ícone de pasta para que uma lista seja exibida. Para localizar a pasta que você deseja monitorar para arquivos novos ou editados, selecione a seta de ângulo à direita ( **>** ), navegue até essa pasta e, em seguida, selecione a pasta.

   ![Localize e selecione a pasta a ser monitorada](./media/connectors-create-api-ftp/select-folder-ftp-trigger.png)

   Sua pasta selecionada aparece na caixa **Pasta**.

   ![A pasta selecionada aparece na propriedade "pasta"](./media/connectors-create-api-ftp/selected-folder-ftp-trigger.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Agora que seu aplicativo lógico tem um gatilho, adicione as ações que você deseja executar quando seu aplicativo lógico encontrar um arquivo novo ou editado. Neste exemplo, você pode adicionar uma ação de FTP que obtém o conteúdo novo ou atualizado.

<a name="get-content"></a>

### <a name="add-ftp-action"></a>Adicionar ação de FTP

A ação **obter metadados do arquivo** Obtém as propriedades de um arquivo que está no servidor FTP e a ação **obter conteúdo do arquivo** Obtém o conteúdo do arquivo com base nas informações sobre esse arquivo no servidor FTP. Por exemplo, você pode adicionar o gatilho do exemplo anterior e essas ações para obter o conteúdo do arquivo depois que esse arquivo é adicionado ou editado.

1. No gatilho ou em qualquer outra ação, selecione **nova etapa**.

1. Na caixa de pesquisa, insira `ftp` como o filtro. Na lista ações, selecione esta ação: **obter metadados do arquivo**

   ![Selecione a ação "obter metadados de arquivo"](./media/connectors-create-api-ftp/select-get-file-metadata-ftp-action.png)

1. Se você já tiver uma conexão ao seu servidor FTP e a conta, vá para a próxima etapa. Caso contrário, forneça os detalhes necessários para essa conexão e, em seguida, selecione **criar**.

   ![Criar conexão do FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Após a ação **obter metadados do arquivo** aparecer, clique dentro da caixa **arquivo** para que a lista de conteúdo dinâmico seja exibida. Agora você pode selecionar propriedades para as saídas de etapas anteriores. Na lista conteúdo dinâmico, em **obter metadados de arquivo**, selecione a propriedade **ID de arquivos** , que faz referência à coleção em que o arquivo foi adicionado ou atualizado.

   ![Localize e selecione a propriedade "lista de arquivos ID"](./media/connectors-create-api-ftp/select-list-of-files-id-output.png)

   A **lista de propriedades de ID de arquivos** agora aparece na caixa **arquivo** .

   ![Propriedade "lista de arquivos de ID" selecionada](./media/connectors-create-api-ftp/selected-list-file-ids-ftp-action.png)

1. Agora, adicione esta ação de FTP: **obter conteúdo do arquivo**

   ![Localize e selecione a ação "obter conteúdo do arquivo"](./media/connectors-create-api-ftp/select-get-file-content-ftp-action.png)

1. Após a ação **obter conteúdo do arquivo** aparecer, clique dentro da caixa **arquivo** para que a lista de conteúdo dinâmico seja exibida. Agora você pode selecionar propriedades para as saídas de etapas anteriores. Na lista de conteúdo dinâmico, em **obter metadados de arquivo**, selecione a propriedade **ID** , que faz referência ao arquivo que foi adicionado ou atualizado.

   ![Localizar e selecionar a propriedade "ID"](./media/connectors-create-api-ftp/get-file-content-id-output.png)

   A propriedade **ID** agora aparece na caixa **arquivo** .

   ![Propriedade "ID" selecionada](./media/connectors-create-api-ftp/selected-get-file-content-id-ftp-action.png)

1. Salve seu aplicativo lógico.

## <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

Para verificar se o fluxo de trabalho retorna o conteúdo esperado, adicione outra ação que lhe envie o conteúdo do arquivo carregado ou atualizado.

1. Na ação **obter conteúdo do arquivo** , adicione uma ação que possa enviar o conteúdo do arquivo. Este exemplo adiciona a ação **enviar um email** para o Outlook do Office 365.

   ![Adicionar uma ação para enviar email](./media/connectors-create-api-ftp/select-send-email-action.png)

1. Depois que a ação for exibida, forneça as informações e inclua as propriedades que você deseja testar. Por exemplo, inclua a propriedade **Content File** , que aparece na lista de conteúdo dinâmico depois de selecionar **Ver mais** na seção **obter conteúdo do arquivo** .

   ![Fornecer informações sobre a ação de email](./media/connectors-create-api-ftp/selected-send-email-action.png)

1. Salve seu aplicativo lógico. Para executar e disparar o aplicativo lógico, na barra de ferramentas, selecione **executar** e, em seguida, adicione um arquivo à pasta de FTP que seu aplicativo lógico agora monitora.

## <a name="connector-reference"></a>Referência de conector

Para obter mais detalhes técnicos sobre esse conector, como gatilhos, ações e limites, conforme descrito pelo arquivo Swagger do conector, consulte a [página de referência do conector](/connectors/ftpconnector/).

> [!NOTE]
> Para aplicativos lógicos em um [ambiente do serviço de integração (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), a versão rotulada do ISE do conector usa os [limites de mensagem do ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) em vez disso.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)

