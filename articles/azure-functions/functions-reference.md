---
title: Diretrizes para o desenvolvimento de Azure Functions
description: Aprenda os conceitos e técnicas do Azure Functions que você precisa para desenvolver funções no Azure, em todas as linguagens de programação e associações.
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.topic: conceptual
ms.date: 10/12/2017
ms.openlocfilehash: 7030ca1c1950f7c06580ce7417a4429fbe330c4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102614812"
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

| Propriedade    | Valores | Digite | Comentários|
|---|---|---|---|
| type  | Nome da associação.<br><br>Por exemplo, `queueTrigger`. | string | |
| direction | `in`, `out`  | string | Indica se a associação é para receber dados na função ou enviar dados a partir da função. |
| name | Identificador de função.<br><br>Por exemplo, `myQueue`. | string | O nome que é usado para os dados associados na função. Em C#, esse é um nome de um argumento. Em JavaScript, é a chave em uma lista de chaves/valores. |

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


## <a name="connections"></a>conexões

Seu projeto de função faz referência a informações de conexão por nome de seu provedor de configuração. Ele não aceita diretamente os detalhes da conexão, permitindo que eles sejam alterados entre ambientes. Por exemplo, uma definição de gatilho pode incluir uma `connection` propriedade. Isso pode se referir a uma cadeia de conexão, mas você não pode definir a cadeia de conexão diretamente em um `function.json` . Em vez disso, você definiria `connection` como o nome de uma variável de ambiente que contém a cadeia de conexão.

O provedor de configuração padrão usa variáveis de ambiente. Eles podem ser definidos por [configurações de aplicativo](./functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) durante a execução no serviço de Azure Functions ou no [arquivo de configurações local](functions-run-local.md#local-settings-file) ao desenvolver localmente.

### <a name="connection-values"></a>Valores de conexão

Quando o nome da conexão é resolvido para um único valor exato, o tempo de execução identifica o valor como uma _cadeia de conexão_, que normalmente inclui um segredo. Os detalhes de uma cadeia de conexão são definidos pelo serviço ao qual você deseja se conectar.

No entanto, um nome de conexão também pode se referir a uma coleção de vários itens de configuração. As variáveis de ambiente podem ser tratadas como uma coleção usando um prefixo compartilhado que termina em sublinhados duplos `__` . Em seguida, o grupo pode ser referenciado definindo o nome da conexão com esse prefixo.

Por exemplo, a `connection` propriedade para uma definição de gatilho de blob do Azure pode ser `Storage1` . Desde que não haja um valor de cadeia de caracteres único configurado com `Storage1` como seu nome, `Storage1__serviceUri` seria usado para a `serviceUri` propriedade da conexão. As propriedades de conexão são diferentes para cada serviço. Consulte a documentação da extensão que usa a conexão.

### <a name="configure-an-identity-based-connection"></a>Configurar uma conexão baseada em identidade

Algumas conexões no Azure Functions são configuradas para usar uma identidade em vez de um segredo. O suporte depende da extensão usando a conexão. Em alguns casos, uma cadeia de conexão ainda pode ser necessária em funções, embora o serviço ao qual você está se conectando ofereça suporte a conexões baseadas em identidade.

> [!IMPORTANT]
> Mesmo que uma extensão de associação ofereça suporte a conexões baseadas em identidade, essa configuração pode não ter suporte ainda no plano de consumo. Consulte a tabela de suporte abaixo.

As conexões baseadas em identidade são suportadas pelo gatilho e extensões de associação a seguir:

| Nome da extensão | Versão da extensão                                                                                     | Com suporte no plano de consumo |
|----------------|-------------------------------------------------------------------------------------------------------|---------------------------------------|
| Blob do Azure     | [Versão 5.0.0-beta1 ou posterior](./functions-bindings-storage-blob.md#storage-extension-5x-and-higher)  | Não                                    |
| Fila do Azure    | [Versão 5.0.0-beta1 ou posterior](./functions-bindings-storage-queue.md#storage-extension-5x-and-higher) | Não                                    |
| Hubs de eventos do Azure    | [Versão 5.0.0-beta1 ou posterior](./functions-bindings-event-hubs.md#event-hubs-extension-5x-and-higher) | Não                                    |

> [!NOTE]
> O suporte para conexões baseadas em identidade ainda não está disponível para conexões de armazenamento usadas pelo tempo de execução do Functions para comportamentos de núcleo. Isso significa que a `AzureWebJobsStorage` configuração deve ser uma cadeia de conexão.

#### <a name="connection-properties"></a>Propriedades da conexão

Uma conexão baseada em identidade para um serviço do Azure aceita as seguintes propriedades:

| Propriedade    | Necessário para extensões | Variável de ambiente | Descrição |
|---|---|---|---|
| URI de serviço | Blob do Azure, fila do Azure | `<CONNECTION_NAME_PREFIX>__serviceUri` |  O URI do plano de dados do serviço ao qual você está se conectando. |
| Namespace totalmente qualificado | Hubs de Eventos | `<CONNECTION_NAME_PREFIX>__fullyQualifiedNamespace` | O namespace totalmente qualificado do hub de eventos. |

Outras opções podem ter suporte para um determinado tipo de conexão. Consulte a documentação do componente que está fazendo a conexão.

Quando hospedado no serviço de Azure Functions, as conexões baseadas em identidade usam uma [identidade gerenciada](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json). A identidade atribuída pelo sistema é usada por padrão. Quando executado em outros contextos, como o desenvolvimento local, a identidade do desenvolvedor é usada, embora isso possa ser personalizado usando parâmetros de conexão alternativos.

##### <a name="local-development"></a>Desenvolvimento local

Ao executar localmente, a configuração acima informa o tempo de execução para usar sua identidade de desenvolvedor local. A conexão tentará obter um token dos seguintes locais, na ordem:

- Um cache local compartilhado entre aplicativos da Microsoft
- O contexto do usuário atual no Visual Studio
- O contexto do usuário atual no Visual Studio Code
- O contexto do usuário atual no CLI do Azure

Se nenhuma dessas opções for bem-sucedida, ocorrerá um erro.

Em alguns casos, talvez você queira especificar o uso de uma identidade diferente. Você pode adicionar propriedades de configuração para a conexão que aponta para a identidade alternativa.

> [!NOTE]
> As opções de configuração a seguir não têm suporte quando hospedadas no serviço de Azure Functions.

Para se conectar usando uma entidade de serviço Azure Active Directory com uma ID do cliente e um segredo, defina a conexão com as seguintes propriedades necessárias além das [Propriedades de conexão](#connection-properties) acima:

| Propriedade    | Variável de ambiente | Descrição |
|---|---|---|
| ID do locatário | `<CONNECTION_NAME_PREFIX>__tenantId` | A ID do locatário do Azure Active Directory (diretório). |
| ID do Cliente | `<CONNECTION_NAME_PREFIX>__clientId` |  A ID do cliente (aplicativo) de um registro de aplicativo no locatário. |
| Segredo do cliente | `<CONNECTION_NAME_PREFIX>__clientSecret` | Um segredo do cliente que foi gerado para o registro do aplicativo. |

Exemplo de `local.settings.json` Propriedades necessárias para a conexão baseada em identidade com o blob do Azure: 
```json
{
  "IsEncrypted": false,
  "Values": {
    "<CONNECTION_NAME_PREFIX>__serviceUri": "<serviceUri>",
    "<CONNECTION_NAME_PREFIX>__tenantId": "<tenantId>",
    "<CONNECTION_NAME_PREFIX>__clientId": "<clientId>",
    "<CONNECTION_NAME_PREFIX>__clientSecret": "<clientSecret>"
  }
}
```

#### <a name="grant-permission-to-the-identity"></a>Conceder permissão para a identidade

Qualquer identidade que esteja sendo usada deve ter permissões para executar as ações pretendidas. Isso normalmente é feito atribuindo uma função no RBAC do Azure ou especificando a identidade em uma política de acesso, dependendo do serviço ao qual você está se conectando. Consulte a documentação de cada serviço sobre quais permissões são necessárias e como elas podem ser definidas.

> [!IMPORTANT]
> Algumas permissões podem ser expostas pelo serviço que não são necessárias para todos os contextos. Sempre que possível, siga o **princípio do privilégio mínimo**, concedendo à identidade apenas os privilégios necessários. Por exemplo, se o aplicativo precisar apenas ler de um blob, use a função de [leitor de dados de blob de armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) , pois o proprietário de dados do blob de [armazenamento](../role-based-access-control/built-in-roles.md#storage-blob-data-owner) inclui permissões excessivas para uma operação de leitura.


## <a name="reporting-issues"></a>Problemas de relatórios
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Próximas etapas
Para saber mais, consulte os recursos a seguir:

* [Gatilhos e associações de Azure Functions](functions-triggers-bindings.md)
* [Codificar e testar o Azure Functions localmente](./functions-develop-local.md)
* [Práticas recomendadas para Azure Functions](functions-best-practices.md)
* [Referência do desenvolvedor de C# do Azure Functions](functions-dotnet-class-library.md)
* [Referência do Azure Functions Node.js Developer](functions-reference-node.md)
