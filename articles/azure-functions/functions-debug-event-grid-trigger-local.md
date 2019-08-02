---
title: Depuração do Grade de Eventos do Azure Functions local
description: Aprenda a depurar localmente as funções do Azure por meio de um evento de Grade de Eventos
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: azure functions, funções, arquitetura sem servidor
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 82e3744c8129a076bbae34314b1f829c6f48d548
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479850"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Depuração local do gatilho da Grade de Eventos do Azure Functions

Este artigo demonstra como depurar uma função local que manipula um evento de grade de eventos do Azure gerado por uma conta de armazenamento. 

## <a name="prerequisites"></a>Pré-requisitos

- Criar ou usar um aplicativo de função existente
- Criar ou usar uma conta de armazenamento existente
- Realizar o download do [ngrok](https://ngrok.com/) para permitir que o Azure chame sua função local

## <a name="create-a-new-function"></a>Criar uma nova função

Abra seu aplicativo de funções no Visual Studio e, com o botão direito no nome do projeto no Gerenciador de Soluções e clique em **Adicionar > Nova função do Azure**.

Na janela *Nova função do Azure*, selecione **gatilho da Grade de Eventos** e clique em **OK**.

![Criar nova função](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Depois que a função é criada, abra o arquivo de código e o copie a URL comentada na parte superior do arquivo. Esse local é usado ao configurar o gatilho de Grade de Eventos.

![Copiar local](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Em seguida, defina um ponto de interrupção na linha que começa com `log.LogInformation`.

![Definir ponto de interrupção](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Em seguida, **pressione F5** para iniciar uma sessão de depuração.

## <a name="allow-azure-to-call-your-local-function"></a>Permitir que o Azure chame sua função local

Para interromper uma função que está sendo depurada em seu computador, você deve habilitar uma maneira para o Azure se comunicar com sua função local da nuvem.

O utilitário [ngrok](https://ngrok.com/) oferece uma maneira para o Azure chamar a função em execução no computador. Inicie *ngrok* usando o seguinte comando:

```bash
ngrok http -host-header=localhost 7071
```
Como o utilitário de configuração, a janela de comando deve ser semelhante à seguinte captura de tela:

![Iniciar ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Cópia de **HTTPS** URL gerada quando *ngrok* é executado. Esse local é usado ao configurar o gatilho de Grade de Eventos.

## <a name="add-a-storage-event"></a>Adicionar uma chave de armazenamento

Abra o portal do Azure, navegue até uma conta de armazenamento e clique na opção **Eventos**.

![Adicionar evento da conta de armazenamento](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

Na janela *Eventos*, clique no botão **Assinatura de Evento**. Na janela *Até Assinatura*, clique na lista suspensa *Tipo de ponto de extremidade* e selecione **Web hook**.

![Selecionar tipo de assinatura](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Depois que o tipo de ponto de extremidade é configurado, clique em **Selecionar um ponto de extremidade** para configurar o valor de ponto de extremidade.

![Selecionar o tipo de ponto de extremidade](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

O valor do *Ponto de extremidade do assinante* é composto de três valores diferentes. O prefixo é a URL HTTPS gerada pelo *ngrok*. O restante da URL vem da URL encontrada no arquivo de código de função, com o nome da função adicionado ao final. Começando com a URL do arquivo de código de função, o *ngrok* URL substitui `http://localhost:7071` e a função nome substitui `{functionname}`.

Captura de tela a seguir mostra como a URL final deve ser:

![Seleção de ponto de extremidade](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Depois de inserir o valor apropriado, clique em **Confirmar seleção**.

> [!IMPORTANT]
> Sempre que você iniciar o *ngrok*, a URL HTTPS é gerada novamente e o valor é alterado. Portanto você deve criar uma nova assinatura de evento cada vez que expor sua função no Azure por meio do *ngrok*.

## <a name="upload-a-file"></a>Carregar um arquivo

Agora você pode carregar um arquivo para sua conta de armazenamento para disparar um Evento de Grade de Eventos para sua função local para tratar. 

Abra o [Gerenciador de Armazenamento](https://azure.microsoft.com/features/storage-explorer/) e conecte-se à sua conta de armazenamento. 

- Expanda **Contêineres de Blob** 
- Clique com botão direito e selecione **Criar contêiner de Blob**.
- Nomeie o contêiner **teste**
- Selecione o contêiner *testar*
- Clique no botão **Carregar**
- Clique em **Carregar arquivos**
- Selecione um arquivo e carregá-o para o contêiner de blob

## <a name="debug-the-function"></a>Depurar a função

Depois que a Grade de Eventos reconhece que um novo arquivo for carregado para o contêiner de armazenamento, o ponto de interrupção é atingido em sua função local.

![Iniciar ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Limpar recursos

Para limpar os recursos criados neste artigo, exclua o contêiner **Testar** em sua conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

- [Automatizar o redimensionamento de imagens carregadas usando a Grade de Eventos](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Gatilho de Grade de Eventos para o Azure Functions](./functions-bindings-event-grid.md)
