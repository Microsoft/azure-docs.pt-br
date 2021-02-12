---
title: Diretrizes para o desenvolvimento de Azure Functions
description: Aprenda os conceitos e técnicas do Azure Functions que você precisa para desenvolver funções no Azure, em todas as linguagens de programação e associações.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: dd9a517749030f9f99731d36947c4d4ff2f13b01
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936729"
---
# <a name="azure-functions-developer-guide"></a>Guia do desenvolvedor do Azure Functions
No Azure Functions, funções específicas compartilham alguns componentes e conceitos técnicos, independentemente da linguagem ou da associação usada. Antes de aprender detalhes específicos de uma determinada linguagem ou binding, leia esta visão geral que se aplica a todos eles.

Este artigo pressupõe que você já tenha lido a [Visão geral do Azure Functions](functions-overview.md).

## <a name="function-code"></a>Código de função
Uma *função* é o principal conceito no Azure Functions. Uma função contém duas partes importantes: seu código, que pode estar escrito em várias linguagens e ter alguma configuração, e o arquivo function.json. Para linguagens compiladas, o arquivo de configuração é gerado automaticamente com base nas anotações no código. Para linguagens de script, você deve fornecer seu próprio arquivo de configuração.

O arquivo function.json define o gatilho, as associações e outras definições de configuração da função. Cada função tem apenas um gatilho. O runtime usa o arquivo de configuração para determinar os eventos a serem monitorados, bem como para passar e retornar dados de uma execução da função. Veja a seguir um arquivo function.json de exemplo.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Para obter mais informações, consulte [Azure Functions os conceitos de gatilhos e associações](functions-triggers-bindings.md).

A propriedade `bindings` é onde você configura gatilhos e associações. Cada binding compartilha algumas configurações comuns e outras que são específicas para um determinado tipo de binding. Todas as associações exigem as seguintes configurações:

| Propriedade | Valores/Tipos | Comentários |
| --- | --- | --- |
| `type` |string |Tipo de binding. Por exemplo, `queueTrigger`. |
| `direction` |'in', 'out' |Indica se a associação é para receber dados na função ou enviar dados a partir da função. |
| `name` |string |O nome que é usado para os dados associados na função. Em C#, esse é um nome de um argumento. Em JavaScript, é a chave em uma lista de chaves/valores. |

## <a name="function-app"></a>Aplicativo de funções
O aplicativo de funções fornece um contexto de execução no Azure no qual suas funções são executadas. Como tal, é a unidade de implantação e gerenciamento para suas funções. Um aplicativo de funções é composto por uma ou mais funções individuais que são gerenciadas, implantadas e dimensionadas em conjunto. Todas as funções em um aplicativo de funções compartilham o mesmo plano de preços, método de implantação e versão de tempo de execução. Pense em um aplicativo de funções como uma forma de organizar e gerenciar coletivamente suas funções. Para saber mais, consulte [como gerenciar um aplicativo de funções](functions-how-to-use-azure-function-app-settings.md). 

> [!NOTE]
> Todas as funções em um aplicativo de funções devem ser criadas na mesma linguagem. Em [versões anteriores](functions-versions.md) do Azure Functions Runtime, isso não era obrigatório.

## <a name="folder-structure"></a>Estrutura de pastas
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

A estrutura de pastas acima é a estrutura padrão (e recomendada) de um aplicativo de funções. Se você quiser alterar o local do arquivo do código de uma função, modifique a seção `scriptFile` do arquivo _function.json_. Também recomendamos usar a [implantação de pacote](deployment-zip-push.md) para implantar o projeto no aplicativo de funções no Azure. Você também pode usar ferramentas existentes, como [integração contínua e implantação](functions-continuous-deployment.md) e Azure DevOps.

> [!NOTE]
> Ao implantar um pacote manualmente, implante seu arquivo _host.json_ e pastas da função diretamente na pasta `wwwroot`. Não inclua a pasta `wwwroot` nas implantações. Caso contrário, você acabará com pastas `wwwroot\wwwroot`.

#### <a name="use-local-tools-and-publishing"></a>Usar ferramentas locais e publicação
Os aplicativos de funções podem ser criados e publicados com várias ferramentas, incluindo o [Visual Studio](./functions-develop-vs.md), o [Visual Studio Code](./create-first-function-vs-code-csharp.md), o [IntelliJ](./functions-create-maven-intellij.md), o [Eclipse](./functions-create-maven-eclipse.md) e o [Azure Functions Core Tools](./functions-develop-local.md). Para mais informações, confira [Codificar e testar o Azure Functions localmente](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a name="how-to-edit-functions-in-the-azure-portal"></a><a id="fileupdate"></a> Como editar funções no portal do Azure
O editor do Functions interno do portal do Azure permite que você atualize o código e o arquivo *function.json* diretamente em linha. Isso é recomendado apenas para pequenas alterações ou provas de conceito. A melhor prática é usar uma ferramenta de desenvolvimento local, como o VS Code.

## <a name="parallel-execution"></a>Execução paralela
Quando vários eventos de gatilho ocorrem mais rápido do que um runtime single-threaded de função pode processar, o runtime pode invocar a função várias vezes em paralelo.  Se um aplicativo de funções estiver usando o [Plano de hospedagem de consumo](event-driven-scaling.md), ele poderá escalar horizontalmente de maneira automática.  Cada instância do aplicativo de funções, quer seja executada no Plano de hospedagem de consumo, quer em um [Plano de hospedagem do Serviço de Aplicativo](../app-service/overview-hosting-plans.md) comum, pode processar invocações de função simultâneas em paralelo usando vários threads.  O número máximo de invocações de função simultâneas em cada instância do aplicativo de funções varia com base no tipo de gatilho que está sendo usado, bem como nos recursos usados por outras funções no aplicativo de funções.

## <a name="functions-runtime-versioning"></a>Controle de versão de runtime de funções

Você pode configurar a versão do runtime de Funções usando a configuração de aplicativo `FUNCTIONS_EXTENSION_VERSION`. Por exemplo, o valor "~ 3" indica que seu aplicativo de funções usará 3. x como sua versão principal. Os aplicativos de funções são atualizados para cada nova versão secundária à medida que são liberados. Para saber mais, incluindo como exibir a versão exata do aplicativo de funções, consulte [Como direcionar versões do Azure Functions runtime](set-runtime-version.md).

## <a name="repositories"></a>Repositórios
O código para o Azure Functions é software livre e é armazenado em repositórios do GitHub:

* [Azure Functions](https://github.com/Azure/Azure-Functions)
* [Host Azure Functions](https://github.com/Azure/azure-functions-host/)
* [portal das Azure Functions](https://github.com/azure/azure-functions-ux)
* [Modelos do Azure Functions](https://github.com/azure/azure-functions-templates)
* [SDK do Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/)
* [Extensões do SDK do Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Associações
Veja uma tabela de todas as associações com suporte.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

Está tendo problemas com erros provenientes de associações? Examine a documentação de [códigos de erro de associação do Azure Functions](functions-bindings-error-pages.md).

## <a name="reporting-issues"></a>Problemas de relatórios
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir:

* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)
* [Codificar e testar o Azure Functions localmente](./functions-develop-local.md)
* [Práticas recomendadas para Azure Functions](functions-best-practices.md)
* [Referência do desenvolvedor de C# do Azure Functions](functions-dotnet-class-library.md)
* [Referência do Azure Functions Node.js Developer](functions-reference-node.md)