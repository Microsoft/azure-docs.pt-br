---
title: "Manter resultados ou logs de trabalhos e tarefas concluídos para um armazenamento de dados – Lote do Azure | Microsoft Docs"
description: "Saiba mais sobre as diferentes opções para persistir dados de saída de trabalhos e tarefas em Lote. Você pode manter dados no Armazenamento do Azure ou em outro armazenamento de dados."
services: batch
author: tamram
manager: timlt
editor: 
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3ca93e823f02b1483ed290cf89de191937d1e2c3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="persist-job-and-task-output"></a>Persistir saída de tarefa e de trabalho

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Alguns exemplos comuns de saída de tarefa incluem:

- Arquivos criados quando a tarefa processa dados de entrada.
- Arquivos de log associados à execução da tarefa. 

Este artigo descreve várias opções para persistir a saída da tarefa e os cenários para os quais cada opção é mais apropriada.   

## <a name="about-the-batch-file-conventions-standard"></a>Sobre o padrão Convenções do Arquivo em Lotes

Lote define um conjunto opcional de convenções de nomenclatura de arquivos de saída de tarefa no Armazenamento do Azure. O [padrão de Convenções de Arquivo em Lotes](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) descreve essas convenções. O padrão de Convenções de Arquivo determina os nomes do caminho de blob e do contêiner de destino no Armazenamento do Azure para um determinado arquivo de saída com base nos nomes do trabalho e da tarefa.

Cabe a você decidir usar o padrão de Convenções de Arquivo para nomear seus arquivos de dados de saída. Você também pode nomear o contêiner de destino e o blob como desejar. Se você usar o padrão de Convenções de Arquivo para nomear arquivos de saída, os arquivos de saída estarão disponíveis para exibição no [portal do Azure][portal].

Há algumas maneiras diferentes de usar o padrão de Convenções de Arquivo:

- Se você estiver usando a API de serviço em Lote para manter os arquivos de saída, poderá optar por nomear contêineres e blobs de destino de acordo com o padrão de Convenções de Arquivo. A API do serviço de Lote permite manter os arquivos de saída no código do cliente sem modificar seu aplicativo de tarefa.
- Se você estiver desenvolvendo com o .NET, poderá usar a [biblioteca Convenções de Arquivo em Lote do Azure para .NET][nuget_package]. Uma vantagem de usar essa biblioteca é que ela dá suporte a consultas de seus arquivos de saída de acordo com sua ID ou finalidade. A funcionalidade de consulta interna facilita o acesso a arquivos de saída de um aplicativo cliente ou de outras tarefas. No entanto, seu aplicativo de tarefa deve ser modificado para chamar a biblioteca de Convenções de Arquivo. Para obter mais informações, consulte a referência para a [biblioteca Convenções de Arquivo para .NET](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.aspx).
- Se você estiver desenvolvendo com uma linguagem que não .NET, você mesmo poderá implementar o padrão de Convenções de Arquivo em seu aplicativo.

## <a name="design-considerations-for-persisting-output"></a>Considerações de design para manter a saída 

Ao projetar sua solução em Lote, considere os seguintes fatores relacionados às saídas de trabalho e tarefa.

* **Tempo de vida de nó de computação**: os nós de computação geralmente são transitórios, especialmente em pools com dimensionamento automático habilitado. Saída de uma tarefa que é executada em um nó está disponível somente enquanto o nó existe e somente dentro do período de retenção de arquivo definido para a tarefa. Se uma tarefa produzir saída que possa ser necessária após a tarefa ter sido concluída, a tarefa deverá carregar seus arquivos de saída para um repositório durável, como o Armazenamento do Azure.

* **Armazenamento de saída**: o Armazenamento do Azure é recomendado como um armazenamento de dados para a saída da tarefa, mas você pode usar qualquer armazenamento durável. A gravação da saída da tarefa no Armazenamento do Azure é integrada à API de serviço de Lote. Se você usar outra forma de armazenamento durável, precisará escrever a lógica do aplicativo para manter a saída da tarefa você mesmo.   

* **Recuperação de saída**: você poderá recuperar a saída da tarefa diretamente dos nós de computação no seu pool ou do Armazenamento do Azure ou de outro armazenamento de dados, se tiver mantido a saída da tarefa. Para recuperar a saída da tarefa diretamente de um nó de computação, você precisa do nome do arquivo e do local de saída no nó. Se você mantiver a saída da tarefa no Armazenamento do Azure, precisará do caminho completo para o arquivo no Armazenamento do Azure para baixar os arquivos de saída com o SDK de Armazenamento do Azure.

* **Exibição da saída**: quando você navegar para uma tarefa do Lote no portal do Azure e selecionar **Arquivos no nó**, verá todos os arquivos associados à tarefa, não apenas os arquivos de saída em que está interessado. Novamente, os arquivos em nós de computação estão disponíveis apenas enquanto o nó existe e apenas no tempo de retenção de arquivo definido para a tarefa. Para exibir a saída da tarefa mantida no Armazenamento do Azure, você pode usar o portal do Azure ou um aplicativo de cliente de Armazenamento do Azure, como o [Gerenciador de Armazenamento do Azure][storage_explorer]. Para exibir dados de saída no armazenamento do Azure com o portal ou outra ferramenta, você deve saber o local do arquivo e navegar diretamente até ele.

## <a name="options-for-persisting-output"></a>Opções para manter a saída

Dependendo do seu cenário, há algumas abordagens diferentes que podem ser adotadas para manter a saída da tarefa:

- Usar a API de serviço de Lote.  
- Usar biblioteca Convenções de Arquivo em Lote para .NET.  
- Implementar o padrão de Convenções de Arquivo em Lote em seu aplicativo.
- Implementar uma solução de movimentação de arquivo personalizada.

As seções a seguir descrevem cada abordagem mais detalhadamente.

### <a name="use-the-batch-service-api"></a>Usar a API de serviço de Lote

Com a versão de 01/05/2017, o serviço de Lote adiciona suporte para especificar os arquivos de saída no Armazenamento do Azure para dados de tarefa quando você [adiciona uma tarefa a um trabalho](https://docs.microsoft.com/rest/api/batchservice/add-a-task-to-a-job) ou [adiciona uma coleção de tarefas a um trabalho](https://docs.microsoft.com/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

A API do serviço de Lote dá suporte a manter dados de tarefa para uma conta de Armazenamento do Azure de pools criados com a configuração de máquina virtual. Com a API de serviço de lote, você pode manter dados de tarefa sem modificar o aplicativo que sua tarefa executa. Opcionalmente, você pode cumprir o [padrão de Convenções de Arquivo em Lotes](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) para nomear os arquivos que você mantém no Armazenamento do Azure. 

Use a API do serviço de Lote para manter a saída da tarefa quando:

- Você desejar manter os dados das tarefas em Lote e as tarefas do gerenciador de trabalho em pools criados com a configuração de máquina virtual.
- Você desejar manter os dados a um contêiner de Armazenamento do Azure com um nome arbitrário.
- Você desejar manter os dados em um contêiner de Armazenamento do Azure nomeado de acordo com o [padrão de Convenções de Arquivo em Lotes](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> A API do serviço de Lote não dá suporte a manter dados de tarefas em execução em pools criados com a configuração de serviço de nuvem. Para obter informações sobre manter a saída de tarefas de pools que estejam executando a configuração de serviços de nuvem, consulte [Manter dados de trabalhos e tarefas no Armazenamento do Azure com a biblioteca de Convenções de Arquivo em Lotes para .NET para manter ](batch-task-output-file-conventions.md)
> 
> 

Para obter mais informações sobre manter a saída da tarefa com a API de serviço de Lote, consulte [Manter dados de tarefa para o Armazenamento do Azure com a API de serviço de Lote](batch-task-output-files.md). Consulte também o projeto de exemplo [PersistOutputs][github_persistoutputs] no GitHub, que demonstra como usar a biblioteca cliente de Lote para .NET para manter a saída da tarefa para o armazenamento durável.

### <a name="use-the-batch-file-conventions-library-for-net"></a>Usar biblioteca Convenções de Arquivo em Lotes para .NET

Os desenvolvedores que criam soluções de Lote com C# e .NET podem usar a [biblioteca Convenções de Arquivo para .NET][nuget_package] para manter dados de tarefa para uma conta de Armazenamento do Azure, de acordo com o [padrão de Convenções de Arquivo em Lotes](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). A biblioteca de convenções de arquivo trata a movimentação de arquivos de saída para o Armazenamento do Azure e nomear contêineres e blobs de destino de uma maneira bem conhecida.

A biblioteca de convenções de arquivo dá suporte à consulta de arquivos de saída por ID ou finalidade, tornando fácil localizá-los sem necessidade de URIs de arquivo completos. 

Use a biblioteca de Convenções de Arquivo em Lotes para .NET para manter a saída de tarefa quando:

- Você desejar transmitir dados para o Armazenamento do Azure enquanto a tarefa ainda está em execução.
- Você desejar manter os dados dos pools criados com a configuração do serviço de nuvem ou a configuração de máquina virtual.
- O aplicativo cliente ou outras tarefas no trabalho precisar localizar e baixar arquivos de saída de tarefas por ID ou por finalidade. 
- Você desejar executar a verificação de pontos ou o upload precoce dos resultados iniciais.
- Você desejar exibir a saída da tarefa no portal do Azure.

Para obter mais informações sobre manter a saída da tarefa com a biblioteca de Convenções de Arquivo para .NET, consulte [Manter dados de trabalho e tarefa no Armazenamento do Azure com a biblioteca de Convenções de Arquivo em Lotes para .NET para manter ](batch-task-output-file-conventions.md). Consulte também o projeto de exemplo [PersistOutputs][github_persistoutputs] no GitHub, que demonstra como usar a biblioteca de Convenções de Arquivo para .NET para manter a saída da tarefa para o armazenamento durável.

O exemplo de projeto [PersistOutputs][github_persistoutputs] no GitHub demonstra como usar a biblioteca cliente de Lote para .NET para manter a saída da tarefa para o armazenamento durável.

### <a name="implement-the-batch-file-conventions-standard"></a>Implementar o padrão Convenções do Arquivo em Lotes

Se você estiver usando uma linguagem diferente de .NET, poderá implementar o [padrão de Convenções de Arquivo em Lotes](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) em seu próprio aplicativo. 

Talvez você mesmo queira implementar o padrão de nomenclatura de Convenções de Arquivo quando quiser um esquema de nomenclatura comprovado ou quando quiser exibir a saída da tarefa no portal do Azure.

### <a name="implement-a-custom-file-movement-solution"></a>Implementar uma solução de movimentação de arquivo personalizada

Você também pode implementar sua própria solução de movimentação de arquivo completa. Use esta abordagem quando:

- Você desejar manter os dados de tarefa em um armazenamento de dados diferente do Armazenamento do Azure. Para carregar arquivos para um armazenamento de dados como o SQL Azure ou o Azure DataLake, crie um script personalizado ou um executável para carregar para esse local. Em seguida, você pode chamá-lo na linha de comando depois de executar o executável principal. Por exemplo, em um nó do Windows, você pode chamar estes dois comandos:`doMyWork.exe && uploadMyFilesToSql.exe`
- Você desejar executar a verificação de pontos ou o upload precoce dos resultados iniciais.
- Você deseja manter controle granular sobre o tratamento de erro. Por exemplo, talvez você queira implementar sua própria solução se quiser usar ações de dependência de tarefas para executar determinadas ações de upload com base em códigos de saída de tarefa específicos. Para obter mais informações sobre ações de dependência de tarefas, consulte [Criar dependências de tarefas para executar tarefas que dependem de outras tarefas](batch-task-dependencies.md). 

## <a name="next-steps"></a>Próximas etapas

- Explore usando os novos recursos na API do serviço de Lote para manter dados da tarefa em [Manter dados da tarefa no Armazenamento do Azure com a API de serviço de Lote](batch-task-output-files.md).
- Saiba como usar a biblioteca de Convenções de Arquivo em Lotes para .NET em [Manter dados de trabalhos e tarefas no Armazenamento do Azure com a biblioteca de Convenções de Arquivo em Lotes para .NET para manter ](batch-task-output-file-conventions.md).
- Consulte o projeto de exemplo [PersistOutputs][github_persistoutputs] no GitHub, que demonstra como usar a biblioteca cliente de Lote para .NET e a biblioteca de Convenções de Arquivo para .NET para manter a saída da tarefa para o armazenamento durável.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/
