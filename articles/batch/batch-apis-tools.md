---
title: APIs e ferramentas para desenvolvedores – Lote do Azure | Microsoft Docs
description: Saiba mais sobre as APIs e ferramentas disponíveis para o desenvolvimento de soluções com o serviço de Lote do Azure.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 3eeb955d312e21f96f4b1029548c5e84b5e586ec
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323768"
---
# <a name="overview-of-batch-apis-and-tools"></a>Visão geral das ferramentas e APIs de Lote

O processamento de cargas de trabalho paralelas com o Lote do Azure normalmente é feito de forma programática usando uma das APIs do Lote. O aplicativo cliente ou serviço pode usar as APIs do Lote para se comunicar com o serviço de Lote. Com as APIs do Lote, você pode criar e gerenciar pools de nós de computação, máquinas virtuais ou serviços de nuvem. Em seguida, você pode agendar trabalhos e tarefas para serem executadas em nós. 

Você pode processar com eficiência cargas de trabalho em grande escala para sua organização ou fornecer um front-end de serviço a seus clientes para que eles possam executar trabalhos e tarefas, sob demanda ou de acordo com uma agenda, em um ou em centenas ou em milhares de nós. Você também pode usar o Lote do Azure como parte de um fluxo de trabalho maior, gerenciado por ferramentas como o [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Quando estiver pronto para se aprofundar na API do Lote e obter uma compreensão mais profunda dos recursos que ele fornece, confira a [Visão geral do recurso Lote para desenvolvedores](batch-api-basics.md).
> 
> 

## <a name="azure-accounts-for-batch-development"></a>Contas do Azure para desenvolvimento de Lote
Ao desenvolver soluções de Lote, você usará as contas a seguir na sua assinatura do Azure:

* **Conta do Lote** - Recursos do Lote do Azure, incluindo pools, nós de computação, trabalhos e tarefas, são associados a uma [conta do Lote](batch-api-basics.md#account) do Azure. Quando o aplicativo faz uma solicitação ao serviço de Lote, ele autentica a solicitação usando o nome de conta do Lote do Azure, a URL da conta e uma chave de acesso ou um token do Azure Active Directory. Você pode [criar uma conta do Lote](batch-account-create-portal.md) no portal do Azure ou de forma programática.
* **Conta de armazenamento** -o lote inclui suporte interno para trabalhar com arquivos no [armazenamento do Azure][azure_storage]. Quase todos os cenários de Lote usam o Armazenamento de Blobs do Azure para preparar os programas que as tarefas executam e os dados que eles processam e para o armazenamento dos dados de saída que eles geram. Para opções de conta de armazenamento em Lote, confira a [Visão geral do recurso de Lote](batch-api-basics.md#azure-storage-account).

## <a name="batch-service-apis"></a>APIs de serviço do Lote

Os aplicativos e serviços podem emitir chamadas da REST API diretamente ou usar uma ou mais das bibliotecas de cliente a seguir para executar e gerenciar as cargas de trabalho do Lote do Azure.

| API | Referência de API | Baixar | Tutorial | Exemplos de código | Mais informações |
| --- | --- | --- | --- | --- | --- |
| **REST do Lote** |[docs.microsoft.com][batch_rest] |N/D |- |- | [Versões com suporte](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **.NET do Lote** |[docs.microsoft.com][api_net] |[NuGet][api_net_nuget] |[Tutorial](tutorial-parallel-dotnet.md) |[GitHub][api_sample_net] | [Notas de versão](https://aka.ms/batch-net-dataplane-changelog) |
| **Python em lotes** |[docs.microsoft.com][api_python] |[PyPI][api_python_pypi] |[Tutorial](tutorial-parallel-python.md)|[GitHub][api_sample_python] | [Leiame](https://github.com/Azure/azure-sdk-for-python/blob/master/doc/batch.rst) |
| **Lote do Node.js** |[docs.microsoft.com][api_nodejs] |[npm][api_nodejs_npm] |[Tutorial](batch-nodejs-get-started.md) |- | [Leiame](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Lote Java** |[docs.microsoft.com][api_java] |[Maven][api_java_jar] |- |[Leiame][api_sample_java] | [Leiame](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>APIs de Gerenciamento do Lote

As APIs do Azure Resource Manager para o Lote fornecem acesso programático a contas do Lote. Usando essas APIs, você pode gerenciar programaticamente contas, cotas, pacotes de aplicativos e outros recursos do Lote por meio do provedor Microsoft.Batch.  

| API | Referência de API | Baixar | Tutorial | Exemplos de código |
| --- | --- | --- | --- | --- |
| **REST do Gerenciamento do Lote** |[docs.microsoft.com][api_rest_mgmt] |N/D |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **.NET de Gerenciamento do Lote** |[docs.microsoft.com][api_net_mgmt] |[NuGet][api_net_mgmt_nuget] | [Tutorial](batch-management-dotnet.md) |[GitHub][api_sample_net] |
| **Python de Gerenciamento do Lote** |[docs.microsoft.com][api_python_mgmt] |[PyPI][api_python_mgmt_pypi] |- |- |
| **Node.js de Gerenciamento do Lote** |[docs.microsoft.com][api_nodejs_mgmt] |[npm][api_nodejs_mgmt_npm] |- |- | 
| **Java de Gerenciamento do Lote** |- |[Maven][api_java_mgmt_jar] |- |- |
## <a name="batch-command-line-tools"></a>Ferramentas de linha de comando do Lote

Essas ferramentas de linha de comando fornecem a mesma funcionalidade que o serviço do Lote e as APIs de gerenciamento do Lote: 

* [Cmdlets do PowerShell do lote][batch_ps]: Os cmdlets do Lote do Azure no módulo do [Azure PowerShell](/powershell/azure/overview) permitem que você gerencie recursos do Lote com o PowerShell.
* [CLI do Azure](/cli/azure): A CLI do Azure é um conjunto de ferramentas multiplataforma que fornece comandos do shell para interação com muitos serviços do Azure, incluindo o serviço de Lote e o serviço de Gerenciamento de Lote. Consulte [Gerenciar recursos do Lote de com a CLI do Azure](batch-cli-get-started.md) para obter mais informações sobre como usar a CLI do Azure com o Lote.

## <a name="other-tools-for-application-development"></a>Outras ferramentas para desenvolvimento de aplicativos

Aqui estão algumas ferramentas adicionais que podem ser úteis para compilar e depurar seus aplicativos e serviços do Lote:

* [Portal do Azure][portal]: Crie, monitore e exclua pools, trabalhos e tarefas do Lote no portal do Azure. Você pode exibir as informações de status para esses e outros recursos ao executar seus trabalhos e até mesmo baixar arquivos de nós de computação em seus pools. Por exemplo, você pode baixar uma `stderr.txt` de uma tarefa com falha durante a solução de problemas. Você também pode baixar arquivos da área de trabalho remota (RDP) que pode usar para fazer logon em nós de computação.
* [Azure batch Explorer][batch_labs]: O Batch Explorer (anteriormente chamado de BatchLabs) é uma ferramenta de cliente gratuita, autônoma e repleta de recursos para ajudar a criar, depurar e monitorar aplicativos do Lote do Azure. Baixe um [pacote de instalação](https://azure.github.io/BatchExplorer/) para Mac, Linux ou Windows.
* [Shipyard do Lote do Azure](https://github.com/Azure/batch-shipyard): O Shipyard do Lote é uma ferramenta usada para ajudar a provisionar, executar e monitorar o processamento em lotes baseado em contêiner e cargas de trabalho HPC no Lote do Azure.
* [Gerenciador de armazenamento do Azure][storage_explorer]: Embora não seja estritamente uma ferramenta do Lote do Azure, o Gerenciador de Armazenamento é outra ferramenta útil a ser usada durante o desenvolvimento e a depuração das soluções do Lote.

## <a name="additional-resources"></a>Recursos adicionais

- Para saber mais sobre log de eventos do seu aplicativo do Lote, consulte [Eventos de log para a avaliação de diagnóstico e monitoramento de soluções do lote](batch-diagnostics.md). Para obter uma referência para eventos gerados pelo serviço do Lote, consulte [Análise do Lote](batch-analytics.md).
- Para obter informações sobre variáveis de ambiente para nós de computação, consulte [Variáveis de ambiente do nó de computação do Lote do Azure](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Próximas etapas

* Leia ae [Visão geral de recursos do Lote para desenvolvedores](batch-api-basics.md), informações essenciais para qualquer pessoa que está se preparando para usar o Lote. O artigo contém informações mais detalhadas sobre os recursos de serviço do Lote, como pools, nós, trabalhos e tarefas, e os muitos recursos da API que você pode usar ao criar o aplicativo do Lote.
* [Introdução à biblioteca do Lote do Azure para .NET](tutorial-parallel-dotnet.md) para aprender a usar o C# e a biblioteca do .NET do Lote para executar uma carga de trabalho simples usando um fluxo de trabalho comum do Lote. Uma [versão do Python](tutorial-parallel-python.md) e um [tutorial do Node.js](batch-nodejs-get-started.md) também estão disponíveis.
* Baixe os [exemplos de código no GitHub][github_samples] para ver como C# o e o Python podem interagir com o lote para agendar e processar cargas de trabalho de exemplo.

[azure_storage]: https://azure.microsoft.com/services/storage/
[api_java]: /java/api/overview/azure/batch
[api_java_mgmt]: /java/api/overview/azure/batch/managementapi
[api_java_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_java_mgmt_jar]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-batch%22
[api_net]: /dotnet/api/overview/azure/batch/
[api_net_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Batch/
[api_rest_mgmt]: /rest/api/batchmanagement/
[api_net_mgmt]: /dotnet/api/overview/azure/batch/management
[api_net_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[api_nodejs]: /javascript/api/overview/azure/batch/client
[api_nodejs_mgmt]: /javascript/api/overview/azure/batch/management
[api_nodejs_npm]: https://www.npmjs.com/package/azure-batch
[api_nodejs_mgmt_npm]: https://www.npmjs.com/package/azure-arm-batch
[api_python]: /python/api/overview/azure/batch/client
[api_python_mgmt]: /python/api/overview/azure/batch/management
[api_python_pypi]: https://pypi.python.org/pypi/azure-batch
[api_python_mgmt_pypi]: https://pypi.python.org/pypi/azure-mgmt-batch
[api_sample_net]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp
[api_sample_python]: https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch
[api_sample_java]: https://github.com/Azure/azure-batch-samples/tree/master/Java/
[batch_ps]: /powershell/module/az.batch/
[batch_rest]: /rest/api/batchservice/
[free_account]: https://azure.microsoft.com/free/
[github_samples]: https://github.com/Azure/azure-batch-samples
[msdn_benefits]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[batch_labs]: https://azure.github.io/BatchExplorer/
[storage_explorer]: https://storageexplorer.com/
[portal]: https://portal.azure.com
