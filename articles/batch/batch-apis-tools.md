---
title: APIs e ferramentas para desenvolvedores
description: Saiba mais sobre as APIs e ferramentas disponíveis para o desenvolvimento de soluções com o serviço de Lote do Azure.
ms.topic: conceptual
ms.date: 05/22/2020
ms.custom: seodec18
ms.openlocfilehash: b7c68ab16834bbd746cf52708db0cdb3f31c8d4d
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219919"
---
# <a name="overview-of-batch-apis-and-tools"></a>Visão geral das ferramentas e APIs de Lote

O processamento de cargas de trabalho paralelas com o Lote do Azure normalmente é feito de forma programática usando uma das APIs do Lote. O aplicativo cliente ou serviço pode usar as APIs do Lote para se comunicar com o serviço de Lote. Com as APIs do Lote, você pode criar e gerenciar pools de nós de computação, máquinas virtuais ou serviços de nuvem. Em seguida, você pode agendar trabalhos e tarefas para serem executadas em nós.

Você pode processar com eficiência cargas de trabalho em grande escala para sua organização ou fornecer um front-end de serviço a seus clientes para que eles possam executar trabalhos e tarefas, sob demanda ou de acordo com uma agenda, em um ou em centenas ou em milhares de nós. Você também pode usar o Lote do Azure como parte de um fluxo de trabalho maior, gerenciado por ferramentas como o [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Para saber mais sobre os recursos e o fluxo de trabalho usados no Lote do Azure, consulte [Fluxo de trabalho e recursos do serviço de lote](batch-service-workflow-features.md).

## <a name="azure-accounts-for-batch-development"></a>Contas do Azure para desenvolvimento de Lote

Ao desenvolver soluções de Lote, você usará as contas a seguir na sua assinatura do Azure:

- **Conta do Lote** - Recursos do Lote do Azure, incluindo pools, nós de computação, trabalhos e tarefas, são associados a uma [conta do Lote](accounts.md) do Azure. Quando o aplicativo faz uma solicitação ao serviço de Lote, ele autentica a solicitação usando o nome de conta do Lote do Azure, a URL da conta e uma chave de acesso ou um token do Azure Active Directory. Você pode [criar uma conta do Lote](batch-account-create-portal.md) no portal do Azure ou de forma programática.
- **Conta de armazenamento** – o Lote inclui suporte interno para trabalhar com arquivos no [Armazenamento do Azure](../storage/index.yml). Quase todos os cenários de Lote usam o Armazenamento de Blobs do Azure para preparar os programas que as tarefas executam e os dados que eles processam e para o armazenamento dos dados de saída que eles geram. Cada conta do Lote geralmente é associada a uma conta de armazenamento correspondente.

## <a name="service-level-and-management-level-apis"></a>APIs de nível de serviço e de gerenciamento

O Lote do Azure tem dois conjuntos de APIs, um para o nível de serviço e o outro para o nível de gerenciamento. A nomenclatura costuma ser semelhante, mas retorna resultados diferentes.

Somente ações das APIs de gerenciamento são rastreadas no log de atividades. As APIs de nível de serviço ignoram a camada de Gerenciamento de Recursos do Azure (management.azure.com) e não são registradas.

Por exemplo, a [API do serviço de Lote para excluir um pool](/rest/api/batchservice/pool/delete) é direcionada diretamente para a conta do lote: `DELETE {batchUrl}/pools/{poolId}`

Ao passo que a [API de gerenciamento de lotes para excluir um pool](/rest/api/batchmanagement/pool/delete) é direcionada à camada management.azure.com: `DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Batch/batchAccounts/{accountName}/pools/{poolName}`

## <a name="batch-service-apis"></a>APIs de serviço do Lote

Os aplicativos e serviços podem emitir chamadas da REST API diretamente ou usar uma ou mais das bibliotecas de cliente a seguir para executar e gerenciar as cargas de trabalho do Lote do Azure.

| API | Referência de API | Baixar | Tutorial | Exemplos de código | Obter mais informações |
| --- | --- | --- | --- | --- | --- |
| **REST do Lote** |[API REST do Azure – Documentação](/rest/api/batchservice/) |N/D |- |- | [Versões com suporte](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **.NET do Lote** |[SDK do Azure para .NET – Documentação](/dotnet/api/overview/azure/batch) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Batch/) |[Tutorial](tutorial-parallel-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) | [Notas de versão](https://aka.ms/batch-net-dataplane-changelog) |
| **Python em lotes** |[SDK do Azure para Python – Documentação](/python/api/overview/azure/batch/client) |[PyPI](https://pypi.org/project/azure-batch/) |[Tutorial](tutorial-parallel-python.md)|[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch) | [Leiame](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/batch/azure-batch/README.md) |
| **JavaScript do Lote** |[SDK do Azure para JavaScript – Documentação](/javascript/api/overview/azure/batch/client) |[npm](https://www.npmjs.com/package/azure-batch) |[Tutorial](batch-js-get-started.md) |- | [Leiame](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Lote Java** |[SDK do Azure para Java – Documentação](/java/api/overview/azure/batch) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Java) | [Leiame](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>APIs de Gerenciamento do Lote

As APIs do Azure Resource Manager para o Lote fornecem acesso programático a contas do Lote. Usando essas APIs, você pode gerenciar programaticamente contas, cotas, pacotes de aplicativos e outros recursos do Lote por meio do provedor Microsoft.Batch.  

| API | Referência de API | Baixar | Tutorial | Exemplos de código |
| --- | --- | --- | --- | --- |
| **REST do Gerenciamento do Lote** |[API REST do Azure – Documentação](/rest/api/batchmanagement/) |- |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **.NET de Gerenciamento do Lote** |[SDK do Azure para .NET – Documentação](/dotnet/api/overview/azure/batch/management) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) | [Tutorial](batch-management-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) |
| **Python de Gerenciamento do Lote** |[SDK do Azure para Python – Documentação](/python/api/overview/azure/batch/management) |[PyPI](https://pypi.org/project/azure-mgmt-batch/) |- |- |
| **JavaScript do Gerenciamento de Lote** |[SDK do Azure para JavaScript – Documentação](/javascript/api/overview/azure/batch/management) |[npm](https://www.npmjs.com/package/azure-arm-batch) |- |- | 
| **Java de Gerenciamento do Lote** |[SDK do Azure para Java – Documentação](/java/api/overview/azure/batch/management) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |- |

## <a name="batch-command-line-tools"></a>Ferramentas de linha de comando do Lote

Essas ferramentas de linha de comando fornecem a mesma funcionalidade que o serviço do Lote e as APIs de gerenciamento do Lote: 

- [Batch PowerShell cmdlets](/powershell/module/az.batch/): Os cmdlets do Lote do Azure no módulo do [Azure PowerShell](/powershell/azure/) permitem que você gerencie recursos do Lote com o PowerShell.
- [CLI do Azure](/cli/azure): A CLI do Azure é um conjunto de ferramentas multiplataforma que fornece comandos do shell para interação com muitos serviços do Azure, incluindo o serviço de Lote e o serviço de Gerenciamento de Lote. Consulte [Gerenciar recursos do Lote de com a CLI do Azure](batch-cli-get-started.md) para obter mais informações sobre como usar a CLI do Azure com o Lote.

## <a name="other-tools-for-application-development"></a>Outras ferramentas para desenvolvimento de aplicativos

Essas ferramentas adicionais podem ser úteis para compilar e depurar seus aplicativos e serviços do Lote.

- [Portal do Azure](https://portal.azure.com/): Crie, monitore e exclua pools, trabalhos e tarefas do Lote no portal do Azure. Você pode exibir as informações de status para esses e outros recursos ao executar seus trabalhos e até mesmo baixar arquivos de nós de computação em seus pools. Por exemplo, você pode baixar uma `stderr.txt` de uma tarefa com falha durante a solução de problemas. Você também pode baixar arquivos da área de trabalho remota (RDP) que pode usar para fazer logon em nós de computação.
- [Azure Batch Explorer](https://azure.github.io/BatchExplorer/): O Batch Explorer (anteriormente chamado de BatchLabs) é uma ferramenta de cliente gratuita, autônoma e repleta de recursos para ajudar a criar, depurar e monitorar aplicativos do Lote do Azure. Baixe um [pacote de instalação](https://azure.github.io/BatchExplorer/) para Mac, Linux ou Windows.
- [Shipyard do Lote do Azure](https://github.com/Azure/batch-shipyard): O Shipyard do Lote é uma ferramenta usada para ajudar a provisionar, executar e monitorar o processamento em lotes baseado em contêiner e cargas de trabalho HPC no Lote do Azure.
- [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/): Embora não seja estritamente uma ferramenta do Lote do Azure, o Gerenciador de Armazenamento é outra ferramenta útil a ser usada durante o desenvolvimento e a depuração das soluções do Lote.

## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre o registro de eventos do aplicativo do Lote, consulte [Métricas, alertas e logs de Lote para avaliação e monitoramento de diagnóstico](batch-diagnostics.md).
- Para obter informações de referência sobre eventos gerados pelo serviço do Lote, consulte [Análise do Lote](batch-analytics.md).
- Para obter informações sobre variáveis de ambiente para nós de computação, consulte [Variáveis de ambiente do runtime do Lote do Azure](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Fluxo de trabalho e recursos primários do serviço de lote](batch-service-workflow-features.md) como pools, nós, trabalhos e tarefas.
- [Introdução à biblioteca do Lote do Azure para .NET](tutorial-parallel-dotnet.md) para aprender a usar o C# e a biblioteca do .NET do Lote para executar uma carga de trabalho simples usando um fluxo de trabalho comum do Lote. Uma [versão do Python](tutorial-parallel-python.md) e um [tutorial do JavaScript](batch-js-get-started.md) também estão disponíveis.
- Baixe os [exemplos de código no GitHub](https://github.com/Azure-Samples/azure-batch-samples) para ver como o C# e o Python podem interagir com o Lote para agendar e processar cargas de trabalho de exemplo.
