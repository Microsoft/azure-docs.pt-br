---
title: Acessar e gerenciar arquivos no Microsoft OneDrive
description: Carregar e gerenciar arquivos no OneDrive criando fluxos de trabalho automatizados em aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 9fe4988b5499943f4b11ec5d640209ceb68e84ec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040225"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Acessar e gerenciar arquivos no conector do OneDrive usando aplicativos lógicos do Azure

Usando os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o [conector do onedrive](/connectors/onedriveconnector/), você pode criar tarefas e fluxos de trabalho automatizados para gerenciar seus arquivos, incluindo carregar, obter, excluir arquivos e muito mais. Com o OneDrive, você pode executar estas tarefas:

* Compile seu fluxo de trabalho armazenando os arquivos no OneDrive ou atualize os arquivos existentes no OneDrive. 
* Use gatilhos para iniciar o fluxo de trabalho quando um arquivo é criado ou atualizado em seu OneDrive.
* Usar ações para criar um arquivo, excluir um arquivo e muito mais. Por exemplo, quando um novo email do Office 365 for recebido com um anexo (um gatilho), crie um novo arquivo no OneDrive (uma ação).

Este artigo mostra como usar o conector do OneDrive em um aplicativo lógico, além de listar os gatilhos e as ações.

Para saber mais sobre os Aplicativos Lógicos, consulte [O que são aplicativos lógicos](../logic-apps/logic-apps-overview.md) e [Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Conectar o OneDrive

Antes do aplicativo lógico poder acessar qualquer serviço, primeiro crie uma *conexão* com o serviço. Uma conexão fornece uma conectividade entre um aplicativo lógico e outro serviço. Por exemplo, para se conectar ao OneDrive, primeiramente é necessária uma *conexão* do OneDrive. Para criar uma conexão, insira as credenciais que você normalmente usa para acessar o serviço ao qual deseja conectar-se. Assim, com o OneDrive, insira as credenciais de sua conta OneDrive para criar a conexão.

### <a name="create-the-connection"></a>Criar a conexão

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Usar um gatilho

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. Gatilhos "sondam" o serviço no intervalo e na frequência desejados. [Saiba mais sobre gatilhos](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. No designer do aplicativo lógico, digite `onedrive` para obter uma lista dos gatilhos:  

   ![Uma caixa de diálogo intitulada "mostrar Microsoft Managed A P ' s" tem uma caixa que contém "onedrive". Abaixo, está uma lista de quatro gatilhos. O primeiro deles é "OneDrive-quando um arquivo é criado". O segundo, "OneDrive-quando um arquivo é modificado", foi selecionado.](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Escolha **Quando um arquivo é modificado**. Se já existe uma conexão, escolha o botão Mostrar Seletor para selecionar uma pasta.

   ![Uma caixa de diálogo intitulada "quando um arquivo é modificado" tem uma caixa intitulada "pasta" com um botão de procura associado.](./media/connectors-create-api-onedrive/sample-folder.png)

   Se você for solicitado a entrar, insira os detalhes do logon para criar a conexão. [Criar a conexão](connectors-create-api-onedrive.md#create-the-connection) neste artigo lista as etapas.

   Neste exemplo, o aplicativo lógico é executado quando um arquivo na pasta escolhida é atualizado. Para ver os resultados deste gatilho, adicione outra ação que envia um email. Por exemplo, adicione a ação *Enviar um email* do Outlook do Office 365 que envia um email para você quando um arquivo é atualizado.

3. Selecione o botão **Editar** e defina os valores **Frequência** e **Intervalo**. Por exemplo, se você quiser que o gatilho faça uma sondagem a cada 15 minutos, defina a **Frequência** como **Minuto** e **Intervalo** como **15**. 

   ![Uma caixa de diálogo intitulada "quando um arquivo é modificado" mostra cinco caixas rotuladas: "pasta", "frequência", "intervalo", "fuso horário" e "hora de início". Há listas suspensas para os campos "frequência" e "fuso horário".](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Salve** as alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## <a name="use-an-action"></a>Usar uma ação

Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Selecione o sinal de mais. Você tem várias opções: **adicionar uma ação**, **adicionar uma condição** ou uma das opções **Mais**.

   ![Uma captura de tela mostra quatro botões: "+ nova etapa", "adicionar uma ação", "adicionar uma condição" e "... Mais ".](./media/connectors-create-api-onedrive/add-action.png)

2. Escolha **Adicionar uma ação**.

3. Na caixa de pesquisa, digite `onedrive` para obter uma lista de todas as ações disponíveis.

   ![Uma caixa de diálogo intitulada "mostrar Microsoft Managed A P ' s" tem uma caixa que contém "onedrive". Abaixo, está uma lista de oito ações. O primeiro é "OneDrive-Create File" e é selecionado.](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. Em nosso exemplo, escolha **OneDrive – criar arquivo**. Se já existir uma conexão, escolha o **Caminho da Pasta** para colocar o arquivo, insira o **Nome do Arquivo** e escolha o **Conteúdo do Arquivo** desejado:  

   ![Uma caixa de diálogo intitulada "criar arquivo" mostra três caixas rotuladas "caminho da pasta", "nome do arquivo" e "conteúdo da pasta". Há um botão procurar no diretório ao lado da caixa "caminho da pasta".](./media/connectors-create-api-onedrive/sample-action.png)

   Se as informações de conexão forem solicitadas, insira os detalhes para [criar a conexão, conforme descrito](#create-the-connection) neste tópico.

   Neste exemplo, você cria um novo arquivo em uma pasta do OneDrive. Você pode usar a saída de outro gatilho para criar o arquivo do OneDrive. Por exemplo, adicione o gatilho *Quando um novo email chegar* do Outlook do Office 365. Em seguida, adicione a ação *Criar arquivo* do OneDrive, que usa os campos Anexos e Tipo de Conteúdo em um ForEach para criar o novo arquivo no OneDrive.

   ![Uma caixa de diálogo intitulada "para cada" tem uma caixa rotulada "selecionar uma saída de etapas anteriores" que contém "anexos". Há uma caixa de diálogo "criar arquivo" que cobre o restante da caixa "para cada", com caixas rotuladas "caminho da pasta", "nome do arquivo" e "conteúdo do arquivo". ](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Salve** as alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Veja os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/onedriveconnector/).

## <a name="next-steps"></a>Próximas etapas

* [Conectores de Aplicativos Lógicos do Azure](apis-list.md)