---
title: "Executar operações no armazenamento de Fila do Azure com o PowerShell | Microsoft Docs"
description: "Tutorial - Executar operações no armazenamento de Fila do Azure com o PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 357d8db329a6a3c782753804d681029fdb07b5f7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Executar operações de armazenamento de Fila do Azure com o Azure PowerShell

O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Para obter informações detalhadas, veja [Introdução às Filas do Azure](storage-queues-introduction.md). Este tutorial aborda as operações comuns de armazenamento de Fila. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma fila
> * Recuperar uma fila
> * Adicionar uma mensagem
> * Ler uma mensagem
> * Excluir uma mensagem 
> * Excluir uma fila

Este tutorial requer o módulo do Azure PowerShell, versão 3.6 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela.

```powershell
Login-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Recuperar a lista de locais

Se você não sabe qual localização deseja usar, você pode listar as localizações disponíveis. Depois que a lista for exibida, encontre o que deseja usar. Este tutorial usará **eastus**. Armazene-a na variável **local** para uso futuro.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Armazene o nome do grupo de recursos em uma variável para uso futuro. Neste exemplo, um grupo de recursos denominado *howtoqueuesrg* é criado na região *eastus*.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Criar Conta de Armazenamento

Crie uma conta de armazenamento padrão de uso geral com o armazenamento localmente redundante (LRS) usando [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Obtenha o contexto da conta de armazenamento que define a conta de armazenamento a ser usada. Ao agir em uma conta de armazenamento, você pode referenciar o contexto em vez de fornecer repetidamente as credenciais.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Criar uma fila

O exemplo a seguir primeiro estabelece uma conexão com o armazenamento do Azure usando o contexto de conta de armazenamento, que inclui o nome da conta de armazenamento e sua chave de acesso primária. -Em seguida, ele chama o cmdlet [New-AzureStorageQueue](/powershell/module/azure.storage/new-azurestoragequeue) para criar uma fila denominada 'queuename'.

```powershell
$queueName = "howtoqueue"
$queue = New-AzureStorageQueue –Name $queueName -Context $ctx
```

Para obter informações sobre convenções de nomenclatura do serviço Fila do Azure, consulte [Nomear filas e metadados](http://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Recuperar uma fila

Você pode consultar e recuperar uma lista de todas as filas em uma conta de armazenamento ou de uma fila específica. Os exemplos a seguir demonstram como recuperar todas as filas na conta de armazenamento e uma fila específica; os dois comandos utilizam o cmdlet [Get-AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue).

```powershell
# Retrieve a specific queue
$queue = Get-AzureStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzureStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem a uma fila

Para adicionar uma mensagem em uma fila, primeiro crie uma nova instância da classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). Em seguida, chame o método [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) . Um CloudQueueMessage pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes.

O exemplo a seguir demonstra como adicionar uma mensagem à sua fila.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessage($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessage($QueueMessage)
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessage($QueueMessage)
```

Se você usar o [Azure Storage Explorer](http://storageexplorer.com), poderá se conectar à sua conta do Azure e exibir as filas na conta de armazenamento e fazer uma busca detalhada em uma para exibir as mensagens na fila. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Ler uma mensagem na fila e depois excluí-la

As mensagens são lidas na ordem do primeiro a entrar, primeiro a sair de melhor tentativa. Isso não é garantido. Quando você lê a mensagem da fila, ela se torna invisível para todos os outros processos que estejam olhando para a fila. Isso garante que quando o código não processar uma mensagem devido a falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente.  

Esse **tempo de invisibilidade** define por quanto tempo a mensagem permanecerá invisível antes de estar disponível novamente para processamento. O padrão é 30 segundos. 

Seu código remove uma mensagem de uma fila em duas etapas. Quando você chamar o método [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx), receberá a próxima mensagem em uma fila. Uma mensagem retornada de **GetMessage** torna-se invisível para todas as outras mensagens de leitura de código da fila. Para concluir a remoção da mensagem da fila, chame o método [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx). 

No exemplo a seguir, leia as mensagens em três filas e aguarde 10 segundos (o tempo de invisibilidade). Em seguida, leia as três mensagens novamente, excluindo as mensagens depois de lê-las ao chamar **DeleteMessage**. Se você tentar ler a fila após a exclusão das mensagens, $queueMessage será retornada como NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
```

## <a name="delete-a-queue"></a>Excluir uma fila
Para excluir uma fila e todas as mensagens contidas nela, chame o cmdlet Remove-AzureStorageQueue. O exemplo a seguir mostra como excluir a fila específica usada neste exercício usando o cmdlet Remove-AzureStorageQueue.

```powershell
# Delete the queue 
Remove-AzureStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os ativos que você criou neste exercício, remova o grupo de recursos, isso também excluirá todos os recursos contidos no grupo. Nesse caso, ele remove a conta de armazenamento criada e o próprio grupo de recursos.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre gerenciamento de armazenamento de Fila básico com o PowerShell, incluindo como:

> [!div class="checklist"]
> * Criar uma fila
> * Recuperar uma fila
> * Adicionar uma mensagem
> * Ler a próxima mensagem
> * Excluir uma mensagem 
> * Excluir uma fila

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Cmdlets de Armazenamento do Microsoft Azure PowerShell
* [Cmdlets do PowerShell do Armazenamento](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Gerenciador do Armazenamento do Microsoft Azure
* [O Gerenciador de Armazenamento do Microsoft Azure](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) é um aplicativo autônomo e gratuito da Microsoft que possibilita o trabalho visual com os dados do Armazenamento do Azure no Windows, MacOS e Linux.