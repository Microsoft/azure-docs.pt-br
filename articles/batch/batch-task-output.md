---
title: Manter saída de trabalho e tarefa em um armazenamento de dados
description: Saiba mais sobre as diferentes opções para persistir dados de saída de trabalhos e tarefas em Lote. Você pode manter dados no Armazenamento do Azure ou em outro armazenamento de dados.
ms.topic: how-to
ms.date: 11/14/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4ebe0b6d57225eff9f3f1251d5e491c95e9b7ffc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85965103"
---
# <a name="persist-job-and-task-output"></a>Persistir saída de tarefa e de trabalho

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Alguns exemplos comuns de saída de tarefa incluem:

- Arquivos criados quando a tarefa processa dados de entrada.
- Arquivos de log associados à execução da tarefa.

Este artigo descreve várias opções para manter a saída da tarefa.

## <a name="options-for-persisting-output"></a>Opções para manter a saída

Dependendo do seu cenário, há algumas abordagens diferentes que podem ser adotadas para manter a saída da tarefa:

- [Usar a API do serviço de lote](batch-task-output-files.md).  
- [Usar a biblioteca de convenções de arquivo em lotes para .NET](batch-task-output-file-conventions.md).  
- Implementar o padrão de Convenções de Arquivo em Lote em seu aplicativo.
- Implementar uma solução de movimentação de arquivo personalizada.

As seções a seguir descrevem brevemente cada abordagem, bem como considerações gerais de design para manter a saída.

### <a name="use-the-batch-service-api"></a>Usar a API de serviço de Lote

O serviço Batch oferece suporte à especificação de arquivos de saída no Armazenamento do Microsoft Azure para dados da tarefa quando você [adiciona uma tarefa a uma tarefa](/rest/api/batchservice/add-a-task-to-a-job) ou [adiciona uma coleção de tarefas a uma tarefa](/rest/api/batchservice/add-a-collection-of-tasks-to-a-job).

Para obter mais informações sobre manter a saída da tarefa com a API de serviço de Lote, consulte [Manter dados de tarefa para o Armazenamento do Azure com a API de serviço de Lote](batch-task-output-files.md).

### <a name="use-the-batch-file-conventions-library-for-net"></a>Usar biblioteca Convenções de Arquivo em Lotes para .NET

Lote define um conjunto opcional de convenções de nomenclatura de arquivos de saída de tarefa no Armazenamento do Azure. O [padrão de Convenções de Arquivo em Lotes](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) descreve essas convenções. O padrão de Convenções de Arquivo determina os nomes do caminho de blob e do contêiner de destino no Armazenamento do Azure para um determinado arquivo de saída com base nos nomes do trabalho e da tarefa.

Cabe a você decidir usar o padrão de Convenções de Arquivo para nomear seus arquivos de dados de saída. Você também pode nomear o contêiner de destino e o blob como desejar. Se você usar o padrão de Convenções de Arquivo para nomear arquivos de saída, os arquivos de saída estarão disponíveis para exibição no [portal do Azure][portal].

Os desenvolvedores que criam soluções de Lote com C# e .NET podem usar a [biblioteca Convenções de Arquivo para .NET][nuget_package] para manter dados de tarefa para uma conta de Armazenamento do Azure, de acordo com o [padrão de Convenções de Arquivo em Lotes](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions). A biblioteca de convenções de arquivo trata a movimentação de arquivos de saída para o Armazenamento do Azure e nomear contêineres e blobs de destino de uma maneira bem conhecida.

Para obter mais informações sobre persistência de saída de tarefa com a biblioteca de Convenções de Arquivo para .NET, consulte [Persistir dados de tarefa e trabalho no Armazenamento do Microsoft Azure com a biblioteca de Convenções de Arquivo em Lotes para .NET](batch-task-output-file-conventions.md).

### <a name="implement-the-batch-file-conventions-standard"></a>Implementar o padrão Convenções do Arquivo em Lotes

Se você estiver usando uma linguagem diferente de .NET, poderá implementar o [padrão de Convenções de Arquivo em Lotes](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files#conventions) em seu próprio aplicativo.

Talvez você mesmo queira implementar o padrão de nomenclatura de Convenções de Arquivo quando quiser um esquema de nomenclatura comprovado ou quando quiser exibir a saída da tarefa no portal do Azure.

### <a name="implement-a-custom-file-movement-solution"></a>Implementar uma solução de movimentação de arquivo personalizada

Você também pode implementar sua própria solução de movimentação de arquivo completa. Use esta abordagem quando:

- Você desejar manter os dados de tarefa em um armazenamento de dados diferente do Armazenamento do Azure. Para carregar arquivos para um armazenamento de dados como o SQL Azure ou o Azure DataLake, crie um script personalizado ou um executável para carregar para esse local. Em seguida, você pode chamá-lo na linha de comando depois de executar o executável principal. Por exemplo, em um nó do Windows, você pode chamar estes dois comandos:`doMyWork.exe && uploadMyFilesToSql.exe`
- Você desejar executar a verificação de pontos ou o upload precoce dos resultados iniciais.
- Você deseja manter controle granular sobre o tratamento de erro. Por exemplo, talvez você queira implementar sua própria solução se quiser usar ações de dependência de tarefas para executar determinadas ações de upload com base em códigos de saída de tarefa específicos. Para obter mais informações sobre ações de dependência de tarefas, consulte [Criar dependências de tarefas para executar tarefas que dependem de outras tarefas](batch-task-dependencies.md).

## <a name="design-considerations-for-persisting-output"></a>Considerações de design para manter a saída

Ao projetar sua solução em Lote, considere os seguintes fatores relacionados às saídas de trabalho e tarefa.

- **Tempo de vida de nó de computação**: Os nós de computação geralmente são transitórios, especialmente em pools com dimensionamento automático habilitado. Saída de uma tarefa que é executada em um nó está disponível somente enquanto o nó existe e somente dentro do período de retenção de arquivo definido para a tarefa. Se uma tarefa produzir saída que possa ser necessária após a tarefa ter sido concluída, a tarefa deverá carregar seus arquivos de saída para um repositório durável, como o Armazenamento do Azure.

- **Armazenamento de saída**: O Armazenamento do Azure é recomendado como um armazenamento de dados para a saída da tarefa, mas você pode usar qualquer armazenamento durável. A gravação da saída da tarefa no Armazenamento do Azure é integrada à API de serviço de Lote. Se você usar outra forma de armazenamento durável, precisará escrever a lógica do aplicativo para manter a saída da tarefa você mesmo.

- **Recuperação de saída**: Você poderá recuperar a saída da tarefa diretamente dos nós de computação no seu pool ou do Armazenamento do Azure ou de outro armazenamento de dados se tiver mantido a saída da tarefa. Para recuperar a saída da tarefa diretamente de um nó de computação, você precisa do nome do arquivo e do local de saída no nó. Se você mantiver a saída da tarefa no Armazenamento do Azure, precisará do caminho completo para o arquivo no Armazenamento do Azure para baixar os arquivos de saída com o SDK de Armazenamento do Azure.

- **Exibição da saída**: Quando você navegar para uma tarefa do Lote no portal do Azure e selecionar **Arquivos no nó**, verá todos os arquivos associados à tarefa, não apenas os arquivos de saída em que está interessado. Novamente, os arquivos em nós de computação estão disponíveis apenas enquanto o nó existe e apenas no tempo de retenção de arquivo definido para a tarefa. Para exibir a saída da tarefa mantida no Armazenamento do Azure, você pode usar o portal do Azure ou um aplicativo de cliente de Armazenamento do Azure, como o [Gerenciador de Armazenamento do Azure][storage_explorer]. Para exibir dados de saída no armazenamento do Azure com o portal ou outra ferramenta, você deve saber o local do arquivo e navegar diretamente até ele.

## <a name="next-steps"></a>Próximas etapas

- Explore usando os novos recursos na API do serviço de Lote para manter dados da tarefa em [Manter dados da tarefa no Armazenamento do Azure com a API de serviço de Lote](batch-task-output-files.md).
- Saiba como usar a biblioteca de Convenções de Arquivo do Lote para .NET em [Persistir dados de tarefas e trabalho no Armazenamento do Microsoft Azure com a biblioteca de Convenções de Arquivo do Lote para .NET](batch-task-output-file-conventions.md).
- Confira o projeto de exemplo [PersistOutputs][github_persistoutputs] no GitHub, que demonstra como usar a biblioteca de clientes do Lote para .NET e a biblioteca de Convenções de arquivo para .NET para persistir a saída da tarefa em um armazenamento durável.

[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs 
