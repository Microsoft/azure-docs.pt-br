---
title: Conectar ao servidor FTP - aplicativos lógicos do Azure
description: Crie, monitore e gerencie arquivos em um servidor FTP com os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, klam, LADocs
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 66f1d726dcfa1a077abbff0d9f028036db43cc25
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293087"
---
# <a name="create-monitor-and-manage-ftp-files-by-using-azure-logic-apps"></a>Criar, monitorar e gerenciar arquivos FTP usando Aplicativos Lógicos do Azure

Com o conector FTP e dos Aplicativos Lógicos do Azure, você pode criar tarefas automatizadas e fluxos de trabalho que criar, monitoram, enviam e recebem arquivos por meio de sua conta em um servidor FTP, juntamente com outras ações, por exemplo:

* Monitorar quando arquivos são adicionados ou alterados.
* Obter, criar, copiar, atualizar, lista e excluir arquivos.
* Obter conteúdo e metadados do arquivo.
* Extrair o arquivo para pastas.

Você pode usar gatilhos que obtêm respostas de seu servidor FTP e disponibilizar a saída para outras ações. Use ações de execução nos aplicativos lógicos para gerenciar arquivos no servidor FTP. Você também pode ter outro uso de ações para usar a saída das ações do FTP. Por exemplo, se você obter arquivos regularmente do servidor FTP, poderá enviar um email sobre esses arquivos e seu conteúdo usando o conector do Outlook do Office 365 ou do Outlook.com. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>limites

* O conector de FTP dá suporte a apenas explícita FTP sobre SSL (FTPS) e não é compatível com FTPS implícito.

* Por padrão, as ações de FTP podem ler ou gravar arquivos que são *50 MB ou menos*. Para lidar com arquivos maiores que 50 MB, o suporte a ações de FTP [agrupamento de mensagem](../logic-apps/logic-apps-handle-large-messages.md). O **obter conteúdo do arquivo** ação usa implicitamente o agrupamento.

* Gatilhos FTP não oferecem suporte a agrupamento. Ao solicitar o conteúdo do arquivo, disparadores selecione somente os arquivos que são 50 MB ou menos. Para obter arquivos maiores que 50 MB, siga este padrão:

  * Usar um gatilho FTP que retorna propriedades de arquivo, como **quando um arquivo é adicionado ou modificado (somente propriedades)** .

  * Siga o gatilho com o FTP **obter conteúdo do arquivo** ação, que lê o arquivo completo e usa implicitamente o agrupamento.

## <a name="how-ftp-triggers-work"></a>Como o FTP dispara o trabalho

FTP dispara um trabalho de sondagem no sistema de arquivos do FTP e procurando por qualquer arquivo que foi alterado desde a última sondagem. Algumas ferramentas permitem preservar o registro de data e hora quando os arquivos são alterados. Nesses casos, você precisa desativar esse recurso para que seu gatilho funcione. Aqui estão algumas configurações comuns:

| Cliente SFTP | Ação |
|-------------|--------|
| WinSCP | Vá para **opções** > **preferências** > **transferir** > **editar**  >  **Preservar o carimbo de hora** > **desabilitar** |
| FileZilla | Ir para **Transferir** > **Preservar registros de data e hora de arquivos transferidos** > **Desativar** |
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

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira "ftp" como filtro. Na lista de gatilhos, selecione o gatilho desejado.

   -ou-

   Para aplicativos lógicos existentes, na última etapa em que você deseja adicionar uma ação, escolha **Nova etapa**e, em seguida, selecione **Adicionar uma ação**. Na caixa de pesquisa, insira “ftp” como filtro. Na lista de ações, selecione a ação desejada.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. Escolha o sinal de adição ( **+** ) que aparece e selecione **adicionar uma ação**.

1. Forneça os detalhes necessários para sua conexão e, em seguida, escolha **Criar**.

1. Forneça os detalhes necessários para o gatilho ou a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="examples"></a>Exemplos

<a name="file-added-modified"></a>

### <a name="ftp-trigger-when-a-file-is-added-or-modified"></a>Gatilho do FTP: Quando um arquivo é adicionado ou modificado

Esse gatilho inicia um fluxo de trabalho do aplicativo lógico ao detectar quando um arquivo é adicionado ou alterado em um servidor FTP. Por exemplo, você pode adicionar uma condição que verifica o conteúdo do arquivo e decide se deve obter esse conteúdo, com base em se esse conteúdo atende a uma condição especificada. Por fim, você pode adicionar uma ação que obtém o conteúdo do arquivo e colocar esse conteúdo em uma pasta no servidor SFTP.

**Exemplo corporativo**: Use esse gatilho para monitorar uma pasta do FTP em busca de novos arquivos que descrevem os pedidos de clientes. Você pode, então, usar uma ação de FTP como **Obter conteúdo do arquivo**, para que possa obter o conteúdo da ordem para mais processamento e armazenar essa ordem em um banco de dados de ordens.

Este é um exemplo que mostra esse gatilho: **Quando um arquivo é adicionado ou modificado**

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira "ftp" como filtro. Na lista de gatilhos, selecione este gatilho: **Quando um arquivo é adicionado ou modificado – FTP**

   ![Localize e selecione o gatilho de FTP](./media/connectors-create-api-ftp/select-ftp-trigger.png)  

1. Forneça os detalhes necessários para sua conexão e, em seguida, escolha **Criar**.

   Por padrão, esse conector transfere arquivos no formato de texto. A transferência de arquivos no binário de formato, por exemplo, onde e quando a codificação é usada, selecione **transporte binário**.

   ![Criar conexão do FTP](./media/connectors-create-api-ftp/create-ftp-connection-trigger.png)  

1. Ao lado da **Pasta**, escolha o ícone da pasta, para que a lista apareça. Para localizar a pasta que você deseja monitorar arquivos novos ou editados, selecione a seta de ângulo direito ( **>** ), navegue até essa pasta e, em seguida, selecione a pasta.

   ![Localize e selecione a pasta a ser monitorada](./media/connectors-create-api-ftp/select-folder.png)  

   Sua pasta selecionada aparece na caixa **Pasta**.

   ![Pasta selecionada](./media/connectors-create-api-ftp/selected-folder.png)  

Agora que seu aplicativo lógico tem um gatilho, adicione as ações que você deseja executar quando seu aplicativo lógico encontrar um arquivo novo ou editado. Neste exemplo, você pode adicionar uma ação de FTP que obtém o conteúdo novo ou atualizado.

<a name="get-content"></a>

### <a name="ftp-action-get-content"></a>Ação do FTP: obter conteúdo

Esta ação obtém o conteúdo de um arquivo em um servidor FTP quando esse arquivo é adicionado ou atualizado. Por exemplo, você pode adicionar o gatilho do exemplo anterior e uma ação que obtém o conteúdo do arquivo depois que esse arquivo é adicionado ou editado.

Este é um exemplo que mostra essa ação: **Obter conteúdo**

1. No gatilho ou quaisquer outras ações, escolha **Nova etapa**.

1. Na caixa de pesquisa, insira “ftp” como filtro. Na lista de ações, selecione esta ação: **Obter conteúdo do arquivo – FTP**

   ![Selecionar ação FTP](./media/connectors-create-api-ftp/select-ftp-action.png)  

1. Se você já tiver uma conexão ao seu servidor FTP e a conta, vá para a próxima etapa. Caso contrário, forneça os detalhes necessários para essa conexão e, em seguida, escolha **Criar**.

   ![Criar conexão do FTP](./media/connectors-create-api-ftp/create-ftp-connection-action.png)

1. Após a ação **Obter conteúdo do arquivo** abrir, clique dentro da caixa do **Arquivo** para que apareça na lista de conteúdo dinâmica. Agora você pode selecionar propriedades para as saídas de etapas anteriores. Na lista de conteúdo dinâmico, selecione a propriedade **Conteúdo do Arquivo**, que tem o conteúdo do arquivo adicionado ou atualizado.  

   ![Localize e selecione o arquivo](./media/connectors-create-api-ftp/ftp-action-get-file-content.png)

   A propriedade do **Conteúdo do Arquivo** aparece agora na caixa **Arquivo**.

   ![Propriedade "Conteúdo do arquivo" selecionada](./media/connectors-create-api-ftp/ftp-action-selected-file-content-property.png)

1. Salve seu aplicativo lógico. Para testar seu fluxo de trabalho, adicione um arquivo para a pasta FTP que seu aplicativo lógico agora monitora.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre limites, ações e gatilhos, que são descritos por OpenAPI do conector (anteriormente conhecido como Swagger) descrição, examine os [página de referência do conector](/connectors/ftpconnector/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
