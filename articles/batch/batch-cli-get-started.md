---
title: Introdução à CLI do Azure do Lote | Microsoft Docs
description: Obtenha uma introdução rápida dos comandos do Lote na CLI do Azure para gerenciar recursos de serviço do Lote do Azure
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: fcd76587-1827-4bc8-a84d-bba1cd980d85
ms.service: batch
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 07/24/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e2bd67ebb977a37c75631f16fbbf4c7dbd6bf250
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782541"
---
# <a name="manage-batch-resources-with-azure-cli"></a>Gerenciar recursos do Lote com a CLI do Azure

A CLI do Azure é a experiência de linha de comando do Azure para gerenciar recursos do Azure. Ela pode ser usada em Windows, Linux e macOS. A CLI do Azure é otimizada para gerenciar e administrar os recursos do Azure na linha de comando. Você pode usar a CLI do Azure para gerenciar suas contas do Lote do Azure e para gerenciar recursos como grupos, trabalhos e tarefas. Com a CLI do Azure, você pode criar scripts para diversas tarefas que você executa com as APIs do Lote, com o Portal do Azure e com cmdlets de PowerShell do Lote.

Este artigo fornece uma visão geral do uso da [CLI do Azure versão 2.0](https://docs.microsoft.com/cli/azure) com o Lote. Veja [Introdução à CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) para uma visão geral de como usar a CLI com o Azure.

## <a name="set-up-the-azure-cli"></a>Configurar a CLI do Azure

Você pode executar a CLI do Azure mais recente no [Azure Cloud Shell](../cloud-shell/overview.md). Para instalar a CLI do Azure localmente, siga as etapas descritas em [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

> [!TIP]
> Recomendamos que você atualize frequentemente sua instalação da CLI do Azure para aproveitar as atualizações e aprimoramentos do serviço.
> 
> 

## <a name="command-help"></a>Ajuda de comando

Você pode exibir um texto de ajuda para todo comando na CLI do Azure acrescentando `-h` ao comando. Omita as outras opções. Por exemplo: 

* Para obter ajuda sobre o comando `az`, insira: `az -h`
* Para obter uma lista de todos os comandos do Lote na CLI, use: `az batch -h`
* Para obter ajuda com a criação de uma conta do Lote, insira: `az batch account create -h`

Em caso de dúvida, use a opção de linha de comando `-h` para obter ajuda sobre qualquer comando da CLI do Azure.



Além disso, consulte a documentação de referência da CLI do Azure para obter detalhes sobre [comandos da CLI do Azure para o Lote](/cli/azure/batch). 

## <a name="log-in-and-authenticate"></a>Fazer logon e autenticar

Para usar a CLI do Azure com o Lote, você precisa fazer logon e autenticar. Siga estas duas etapas simples:

1. **Faça logon no Azure.** O logon no Azure fornece acesso a comandos do Azure Resource Manager, incluindo comandos do [serviço de gerenciamento em lotes](batch-management-dotnet.md).  
2. **Faça logon em sua conta do Lote.** O logon em sua conta do Lote fornece acesso a comandos do serviço Lote.   

### <a name="log-in-to-azure"></a>Fazer logon no Azure

Existem algumas maneiras diferentes de fazer logon no Azure, descritas detalhadamente em [Entrar com a CLI do Azure](/cli/azure/authenticate-azure-cli):

1. [Fazer logon interativamente](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Faça logon interativamente quando você estiver executando comandos da CLI do Azure na linha de comando.
2. [Fazer logon com uma entidade de serviço](https://docs.microsoft.com/cli/azure/authenticate-azure-cli). Faça logon com uma entidade de serviço quando você estiver executando comandos da CLI do Azure de um script ou aplicativo.

Para os fins deste artigo, vamos mostrar como entrar no Azure interativamente. Digite [az login](https://docs.microsoft.com/cli/azure/reference-index#az-login) na linha de comando:

```azurecli
# Log in to Azure and authenticate interactively.
az login
```

O comando `az login` retorna um token que você pode usar para autenticar, conforme mostrado aqui. Siga as instruções fornecidas para abrir uma página da Web e enviar o token do Azure:

![Fazer logon no Azure](./media/batch-cli-get-started/az-login.png)

Os exemplos listados na seção Scripts do shell de exemplo também mostra como iniciar a sessão da CLI do Azure fazendo logon no Azure de forma interativa. Depois de fazer logon, você pode chamar comandos para trabalhar com recursos do gerenciamento em lotes, incluindo contas, chaves, pacotes de aplicativos e cotas do Lote.  

### <a name="log-in-to-your-batch-account"></a>Fazer logon sua conta do Lote

Para usar a CLI do Azure e gerenciar recursos do Lote, como pools, trabalhos e tarefas, você precisará entrar na sua conta do Lote e autenticar. Para entrar no serviço Lote, use o comando [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az-batch-account-login). 

Você tem duas opções para autenticação na sua conta do Lote:

- **Usando a autenticação do Azure AD (Azure Active Directory)** 

    A autenticação com o Azure AD é o padrão quando você usa a CLI do Azure com o Lote e é recomendada na maioria dos cenários. 
    
    Quando você entra no Azure interativamente, conforme descrito na seção anterior, as credenciais são armazenadas em cache para que a CLI do Azure possa entrar na conta do Lote usando as mesmas credenciais. Se você entrar no Azure usando uma entidade de serviço, essas credenciais também serão usadas para entrar na sua conta do Lote.

    Uma vantagem do Azure AD é que ele oferece RBAC (controle de acesso baseado em função). Com o RBAC, acesso de um usuário depende de suas funções atribuídas em vez da posse ou não de chaves da conta. Em vez de gerenciar chaves de conta, você pode gerenciar funções RBAC e permitir que o Azure AD lide com autenticação e acesso.  

     Para entrar na sua conta do Lote usando o Azure AD, chame o comando [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az-batch-account-login): 

    ```azurecli
    az batch account login -g myresource group -n mybatchaccount
    ```

- **Usando a autenticação de chave compartilhada**

    [Autenticação de chave compartilhada](/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service#authentication-via-shared-key) usa suas chaves de acesso de conta a fim de autenticar comandos da CLI do Azure para o serviço Lote.

    Se você estiver criando scripts da CLI do Azure para automatizar a chamada de comandos do Lote, poderá usar a autenticação de Chave Compartilhada ou uma entidade de serviço do Azure AD. Em alguns cenários, o uso da autenticação de Chave Compartilhada pode ser mais simples do que criar uma entidade de serviço.  

    Para entrar usando autenticação de Chave Compartilhada, inclua a opção `--shared-key-auth` na linha de comando:

    ```azurecli
    az batch account login -g myresourcegroup -n mybatchaccount --shared-key-auth
    ```

Os exemplos listados na seção Scripts do shell de exemplo mostram como fazer logon na conta do Lote com a CLI do Azure usando o Azure AD e a Chave Compartilhada.

## <a name="use-azure-batch-cli-extension-commands"></a>Usar comandos de extensão da CLI do Lote do Azure

Instalando a extensão de CLI do Lote do Azure, você pode usar a CLI do Azure para executar trabalhos do Lote de ponta a ponta sem escrever código. Os comandos do Lote com suporte pela extensão permitem que você use modelos JSON para criar pools, trabalhos e tarefas com a CLI do Azure. Você também pode usar os comandos de CLI da extensão para carregar arquivos de entrada de trabalho na conta de Armazenamento do Azure associada à conta do Lote e baixar arquivos de saída de trabalho dele. Para saber mais, confira [Usar modelos da CLI do Lote do Azure e transferência de arquivos](batch-cli-templates.md).

## <a name="script-examples"></a>Exemplos de script

Consulte os [exemplos de script da CLI](cli-samples.md) para o Lote a fim de realizar tarefas comuns. Estes exemplos abordam muitos dos comandos disponíveis na CLI do Azure para o Lote para criar e gerenciar contas, pools, trabalhos e tarefas. 

## <a name="json-files-for-resource-creation"></a>Arquivos JSON para a criação de recursos

Ao criar recursos do Lote, como pools e trabalhos, você pode especificar um arquivo JSON contendo a nova configuração do recurso, em vez de passar seus parâmetros como opções da linha de comando. Por exemplo: 

```azurecli
az batch pool create my_batch_pool.json
```

Embora seja possível criar a maioria dos recursos do Lote usando apenas as opções de linha de comando, alguns recursos exigem a especificação de um arquivo formatado em JSON contendo os detalhes do recurso. Por exemplo, você deve usar um arquivo JSON se quiser especificar arquivos de recurso para uma tarefa de inicialização.

Para ver a sintaxe JSON necessária para criar um recurso, consulte a documentação de [referência da API REST do Lote][rest_api]. Cada tópico "Adicionar *tipo de recurso*" na referência da API REST contém exemplos de scripts JSON para criar esse recurso. Você pode usar esses exemplos de scripts JSON como modelos para que os arquivos JSON usem com a CLI do Azure. Por exemplo, para ver a sintaxe JSON para criação do pool, consulte [Adicionar um pool a uma conta][rest_add_pool].

Para um exemplo de script que especifica um arquivo JSON, consulte [Executar um trabalho e tarefas com o Lote](./scripts/batch-cli-sample-run-job.md).

> [!NOTE]
> Se você especificar um arquivo JSON durante a criação de um recurso, todos os outros parâmetros especificados na linha de comando desse recurso serão ignorados.
> 
> 

## <a name="efficient-queries-for-batch-resources"></a>Consultas eficientes para recursos do Lote

Cada tipo de recurso do Lote dá suporte a um comando `list` que consulta sua conta do Lote e lista os recursos desse tipo. Por exemplo, você pode listar os pools em sua conta e as tarefas em um trabalho:

```azurecli
az batch pool list
az batch task list --job-id job001
```

Quando você consulta o serviço Lote com uma operação `list`, pode especificar uma cláusula OData para limitar a quantidade de dados retornados. Como toda a filtragem ocorre no lado do servidor, apenas os dados solicitados são transmitidos. Use essas cláusulas para economizar largura de banda (e, portanto, tempo) ao executar operações de lista.

A tabela abaixo descreve as cláusulas OData com suporte no serviço Lote:

| Cláusula | DESCRIÇÃO |
|---|---|
| `--select-clause [select-clause]` | Retorna um subconjunto de propriedades para cada entidade. |
| `--filter-clause [filter-clause]` | Retorna apenas as entidades que correspondem à expressão de OData especificada. |
| `--expand-clause [expand-clause]` | Obtém as informações de entidade em uma única chamada REST subjacente. A cláusula expand atualmente dá suporte apenas à propriedade `stats`. |

Para um script de exemplo que mostra como usar uma cláusula OData, consulte [Executar um trabalho e tarefas com o Lote](./scripts/batch-cli-sample-run-job.md).

Para saber mais sobre como executar consultas de lista eficientes com cláusulas OData, consulte [Consultar o serviço Lote do Azure com eficiência](batch-efficient-list-queries.md).

## <a name="troubleshooting-tips"></a>Dicas de solução de problemas

As dicas abaixo poderão ajudar você quando estiver solucionando problemas da CLI do Azure:

* Use `-h` para obter um **texto de ajuda** para qualquer comando da CLI
* Use `-v` e `-vv` para exibir saídas de comando **detalhadas**. Quando o sinalizador `-vv` é incluído, a CLI do Azure exibe as respostas e solicitações REST reais. Essas opções são úteis para exibir a saída completa do erro.
* Você pode exibir a **saída do comando como JSON** com a opção `--json`. Por exemplo, o `az batch pool show pool001 --json` exibe propriedades do pool001 no formato JSON. Em seguida, você poderá copiar e modificar essa saída para usá-la em um `--json-file` (confira Arquivos JSON anteriormente neste artigo).
<!---Loc Comment: Please, check link [JSON files] since it's not redirecting to any location.--->

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).
* Para saber mais sobre recursos do Lote, consulte [Visão geral do Lote do Azure para desenvolvedores](batch-api-basics.md).
* Para saber mais sobre como usar modelos do Lote para criar pools, trabalhos e tarefas sem escrever código, confira [Usar modelos da CLI do Lote do Azure e transferência de arquivos](batch-cli-templates.md).

[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
