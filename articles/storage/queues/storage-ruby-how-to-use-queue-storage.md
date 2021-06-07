---
title: Como usar o armazenamento de fila do Ruby-armazenamento do Azure
description: Saiba como usar o armazenamento de filas do Azure para criar e excluir filas, e inserir, obter e excluir mensagens. Exemplos gravados no Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/08/2016
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8558949e49bcf551c9276458d375fb9ac9636184
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97587655"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Como usar o armazenamento de fila do Ruby

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Visão geral

Este guia mostra como executar cenários comuns usando o serviço de Armazenamento de Fila do Microsoft Azure. Os exemplos são gravados usando a API do Ruby Azure. Os cenários abrangidos incluem **inserir**, **exibir**, **obter** e **excluir** mensagens da fila, bem como **criar e excluir filas**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Criar um aplicativo Ruby

Crie um aplicativo Ruby. Para obter instruções, consulte [criar um aplicativo Ruby no serviço de aplicativo no Linux](../../app-service/quickstart-ruby.md).

## <a name="configure-your-application-to-access-storage"></a>Configurar seu aplicativo para acessar o armazenamento

Para usar o armazenamento do Azure, você precisa baixar e usar o pacote do Azure do Ruby, que inclui um conjunto de bibliotecas de conveniência que se comunicam com os serviços REST de armazenamento.

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>Usar RubyGems para obter o pacote

1. Use uma interface de linha de comando como PowerShell (Windows), Terminal (Mac) ou Bash (Unix).
2. Digite `gem install Azure` na janela de comando para instalar o gem e as dependências.

### <a name="import-the-package"></a>Importar o pacote

Use seu editor de texto favorito e adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o armazenamento:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Configurar uma conexão de armazenamento do Azure

O módulo do Azure lerá as variáveis de ambiente `AZURE_STORAGE_ACCOUNT` e `AZURE_STORAGE_ACCESS_KEY` para obter as informações necessárias para se conectar à sua conta de armazenamento do Azure. Se essas variáveis de ambiente não estiverem definidas, você deverá especificar as informações da conta antes de usar `Azure::QueueService` o com o código a seguir:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Para obter esses valores de uma conta de armazenamento clássico ou do Resource Manager no Portal do Azure:

1. Faça logon no [Portal do Azure](https://portal.azure.com).
2. Navegue até a conta de armazenamento que você deseja usar.
3. Na folha **configurações** à direita, clique em **chaves de acesso**.
4. Na folha **chaves de acesso** exibida, você verá a tecla de acesso 1 e a tecla de acesso 2. Você pode usar qualquer uma das duas.
5. Clique no ícone de cópia para copiar a chave para a área de transferência.

## <a name="how-to-create-a-queue"></a>Como criar uma fila

O código a seguir cria um `Azure::QueueService` objeto, que permite que você trabalhe com filas.

```ruby
azure_queue_service = Azure::QueueService.new
```

Use o `create_queue()` método para criar uma fila com o nome especificado.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Como inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila, use o `create_message()` método para criar uma nova mensagem e adicioná-la à fila.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Como inspecionar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o `peek_messages()` método. Por padrão, `peek_messages()` o inspeciona uma única mensagem. Você também pode especificar quantas mensagens deseja inspecionar.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Como remover a próxima mensagem da fila

É possível remover uma mensagem da fila em duas etapas.

1. Ao chamar `list_messages()` , você obtém a próxima mensagem em uma fila por padrão. Você também pode especificar quantas mensagens deseja obter. As mensagens retornadas `list_messages()` tornam-se invisíveis para qualquer outra mensagem de leitura de código dessa fila. Passe o tempo limite de visibilidade em segundos como um parâmetro.
2. Para concluir a remoção da mensagem da fila, você também deve chamar `delete_message()` .

Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama `delete_message()` logo após a mensagem ser processada.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Como alterar o conteúdo de uma mensagem em fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. O código a seguir usa o `update_message()` método para atualizar uma mensagem. O método retornará uma tupla que contém o recebimento pop da mensagem da fila e um valor UTC `DateTime` que representa quando a mensagem será visível na fila.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Como: opções adicionais para o enfileiramento de mensagens

Há duas maneiras de personalizar a recuperação da mensagem de uma fila.

1. Você pode obter um lote de mensagens.
2. Você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem.

O exemplo de código a seguir usa o `list_messages()` método para obter 15 mensagens em uma chamada. Em seguida, ele lê e exclui cada mensagem. Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Como obter o comprimento da fila

Você pode obter uma estimativa do número de mensagens na fila. O `get_queue_metadata()` método retorna a contagem aproximada de mensagens e outros metadados de fila.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Como excluir uma fila

Para excluir uma fila e todas as mensagens contidas nela, chame o `delete_queue()` método no objeto de fila.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de filas, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

- Visite o [blog da equipe de armazenamento do Azure](/archive/blogs/windowsazurestorage/)
- Visite o repositório [SDK do Azure para Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) no github

Para obter uma comparação entre o armazenamento de filas do Azure discutido neste artigo e as filas do barramento de serviço do Azure discutidas em [como usar filas do barramento de serviço](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/), consulte [armazenamento de filas do Azure e filas do barramento de serviço-comparações e contrastes](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
