---
title: 'Início Rápido: Criar filas do Armazenamento do Azure no portal'
description: Use o portal do Azure para criar uma fila. Em seguida, use o portal do Azure para adicionar uma mensagem, exibir as propriedades da mensagem e remover a mensagem da fila.
author: twooley
ms.author: twooley
ms.reviewer: dineshm
ms.date: 08/13/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom:
- mode-portal
ms.openlocfilehash: f0e7a5a514f2d68fce025ea9fb354f29fa068561
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534394"
---
# <a name="quickstart-create-a-queue-and-add-a-message-with-the-azure-portal"></a>Início Rápido: Criar uma fila e adicionar uma mensagem com o portal do Azure

Neste início rápido, você aprenderá como usar o [portal do Azure](https://portal.azure.com/) para criar uma fila no Armazenamento do Azure e para adicionar mensagens removidas da fila.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

## <a name="create-a-queue"></a>Criar uma fila

Para criar uma fila no portal do Azure, siga estas etapas:

1. Navegue até sua nova conta de armazenamento no portal do Azure.
2. No menu à esquerda da conta de armazenamento, role a página até a seção **Armazenamento de Filas** e selecione **Filas**.
3. Selecione o botão **+ Fila**.
4. Digite um nome para a nova fila. O nome da fila deve estar com letras minúsculas, começar com uma letra ou número e pode incluir apenas letras, números e o caractere traço (-).
6. Selecione **OK** para criar a fila.

    ![Captura de tela mostrando como criar uma fila no portal do Azure](media/storage-quickstart-queues-portal/create-queue.png)

## <a name="add-a-message"></a>Adicionar uma mensagem

Em seguida, adicione uma mensagem à nova fila. Uma mensagem pode ter tamanho de até 64 KB.

1. Selecione a nova fila na lista de filas na conta de armazenamento.
1. Selecione o botão **+ Adicionar mensagem** para adicionar uma mensagem à fila. Insira uma mensagem no campo **Texto da mensagem**.
1. Especifique quando a mensagem expira. Os valores válidos que podem ser inseridos no campo **Expira em** estão entre 1 segundo e 7 dias. Selecione **Mensagem nunca expira** para indicar uma mensagem que permanecerá na fila até que ela seja explicitamente removida.
1. Indique se você deseja codificar a mensagem como Base64. A codificação de dados binários é recomendada.
1. Selecione o botão **OK** para adicionar a mensagem.

    ![Captura de tela mostrando como adicionar uma mensagem a uma fila](media/storage-quickstart-queues-portal/add-message.png)

## <a name="view-message-properties"></a>Exibir propriedades da mensagem

Após adicionar a mensagem, o portal do Azure exibirá uma lista de todas as mensagens na fila. É possível exibir a ID, o conteúdo, a hora de inserção e a hora de expiração da mensagem. Também é possível ver quantas vezes esta mensagem foi removida da fila.

![Captura de tela mostrando as propriedades da mensagem](media/storage-quickstart-queues-portal/view-message-properties.png)

## <a name="dequeue-a-message"></a>Remover uma mensagem da fila

É possível remover uma mensagem da frente da fila no portal do Azure. Quando você remove uma mensagem da frente da fila, ela é excluída.

A remoção da fila sempre remove a mensagem mais antiga na fila.

![Captura de tela mostrando como remover uma mensagem da fila no portal](media/storage-quickstart-queues-portal/dequeue-message.png)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a criar uma fila, adicionar uma mensagem, exibir as propriedades da mensagem e remover uma mensagem da fila no portal do Azure.

> [!div class="nextstepaction"]
> [O que é o Armazenamento de Filas do Azure?](storage-queues-introduction.md)
