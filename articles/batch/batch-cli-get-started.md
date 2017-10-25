---
title: "Introdução à CLI do Azure do Lote | Microsoft Docs"
description: "Obtenha uma introdução rápida dos comandos do Lote na CLI do Azure para gerenciar recursos de serviço do Lote do Azure"
services: batch
documentationcenter: 
author: v-dotren
manager: timlt
editor: 
ms.assetid: fcd76587-1827-4bc8-a84d-bba1cd980d85
ms.service: batch
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 09/28/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 68a5493282fa4a0b54ba551c48ae963a42b94dca
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-batch-resources-with-azure-cli"></a>Gerenciar recursos do Lote com a CLI do Azure

A CLI 2.0 do Azure é a nova experiência de linha de comando do Azure para gerenciar recursos do Azure. Ela pode ser usada em Windows, Linux e macOS. A CLI do Azure 2.0 é otimizada para gerenciar e administrar os recursos do Azure na linha de comando. Você pode usar a CLI do Azure para gerenciar suas contas do Lote do Azure e para gerenciar recursos como grupos, trabalhos e tarefas. Com a CLI do Azure, você pode criar scripts para diversas tarefas que você executa com as APIs do Lote, com o Portal do Azure e com cmdlets de PowerShell do Lote.

Este artigo fornece uma visão geral do uso da [CLI do Azure versão 2.0](https://docs.microsoft.com/cli/azure/overview) com o Lote. Consulte [Introdução ao Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) para uma visão geral de como usar a CLI com o Azure.

A Microsoft recomenda usar a versão mais recente da CLI do Azure, versão 2.0. Para saber mais sobre a versão 2.0, consulte [CLI do Azure 2.0 agora disponível](https://azure.microsoft.com/blog/announcing-general-availability-of-vm-storage-and-network-azure-cli-2-0/).

## <a name="set-up-the-azure-cli"></a>Configurar a CLI do Azure

Para instalar a CLI do Azure, siga as etapas descritas em [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

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

> [!NOTE]
> Versões anteriores da CLI do Azure usavam `azure` para preceder um comando CLI. Na versão 2.0, todos os comandos agora são precedidos por `az`. Não deixe de atualizar os scripts para usar a nova sintaxe com a versão 2.0.
>
>  

Além disso, consulte a documentação de referência da CLI do Azure para obter detalhes sobre [comandos da CLI do Azure para o Lote](https://docs.microsoft.com/cli/azure/batch). 

## <a name="log-in-and-authenticate"></a>Fazer logon e autenticar

Para usar a CLI do Azure com o Lote, você precisa fazer logon e autenticar. Siga estas duas etapas simples:

1. **Faça logon no Azure.** O logon no Azure fornece acesso a comandos do Azure Resource Manager, incluindo comandos do [serviço de gerenciamento em lotes](batch-management-dotnet.md).  
2. **Faça logon em sua conta do Lote.** O logon em sua conta do Lote fornece acesso a comandos do serviço Lote.   

### <a name="log-in-to-azure"></a>Fazer logon no Azure

Existem algumas maneiras diferentes de fazer logon no Azure, descritas detalhadamente em [Entrar com a CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/authenticate-azure-cli):

1. [Fazer logon interativamente](https://docs.microsoft.com/cli/azure/authenticate-azure-cli#az_authenticate_azure_cli_interactive_log_in). Faça logon interativamente quando você estiver executando comandos da CLI do Azure na linha de comando.
2. [Fazer logon com uma entidade de serviço](https://docs.microsoft.com/cli/azure/authenticate-azure-cli#az_authenticate_azure_cli_logging_in_with_a_service_principal). Faça logon com uma entidade de serviço quando você estiver executando comandos da CLI do Azure de um script ou aplicativo.

Para os fins deste artigo, vamos mostrar como entrar no Azure interativamente. Digite [az login](https://docs.microsoft.com/cli/azure/#login) na linha de comando:

```azurecli
# Log in to Azure and authenticate interactively.
az login
```

O comando `az login` retorna um token que você pode usar para autenticar, conforme mostrado aqui. Siga as instruções fornecidas para abrir uma página da Web e enviar o token do Azure:

![Fazer logon no Azure](./media/batch-cli-get-started/az-login.png)

Os exemplos listados na seção [Scripts de shell de exemplo](#sample-shell-scripts) também mostra como iniciar a sessão da CLI do Azure entrando no Azure interativamente. Depois de fazer logon, você pode chamar comandos para trabalhar com recursos do gerenciamento em lotes, incluindo contas, chaves, pacotes de aplicativos e cotas do Lote.  

### <a name="log-in-to-your-batch-account"></a>Fazer logon sua conta do Lote

Para usar a CLI do Azure e gerenciar recursos do Lote, como pools, trabalhos e tarefas, você precisará entrar na sua conta do Lote e autenticar. Para entrar no serviço Lote, use o comando [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login). 

Você tem duas opções para autenticação na sua conta do Lote:

- **Usando a autenticação do Azure AD (Azure Active Directory)** 

    A autenticação com o Azure AD é o padrão quando você usa a CLI do Azure com o Lote e é recomendada na maioria dos cenários. 
    
    Quando você entra no Azure interativamente, conforme descrito na seção anterior, as credenciais são armazenadas em cache para que a CLI do Azure possa entrar na conta do Lote usando as mesmas credenciais. Se você entrar no Azure usando uma entidade de serviço, essas credenciais também serão usadas para entrar na sua conta do Lote.

    Uma vantagem do Azure AD é que ele oferece RBAC (controle de acesso baseado em função). Com o RBAC, acesso de um usuário depende de suas funções atribuídas em vez da posse ou não de chaves da conta. Em vez de gerenciar chaves de conta, você pode gerenciar funções RBAC e permitir que o Azure AD lide com autenticação e acesso.  

        To log in to your Batch account using Azure AD, call the [az batch account login](https://docs.microsoft.com/cli/azure/batch/account#az_batch_account_login) command: 

    ```azurecli
    az batch account login -g myresource group -n mybatchaccount
    ```

- **Usando a autenticação de Chave compartilhada**

    [Autenticação de chave compartilhada](https://docs.microsoft.com/rest/api/batchservice/authenticate-requests-to-the-azure-batch-service#authentication-via-shared-key) usa suas chaves de acesso de conta a fim de autenticar comandos da CLI do Azure para o serviço Lote.

    Se você estiver criando scripts da CLI do Azure para automatizar a chamada de comandos do Lote, poderá usar a autenticação de Chave Compartilhada ou uma entidade de serviço do Azure AD. Em alguns cenários, o uso da autenticação de Chave Compartilhada pode ser mais simples do que criar uma entidade de serviço.  

    Para entrar usando autenticação de Chave Compartilhada, inclua a opção `--shared-key-auth` na linha de comando:

    ```azurecli
    az batch account login -g myresourcegroup -n mybatchaccount --shared-key-auth
    ```

Os exemplos listados na seção [Scripts de shell de exemplo](#sample-shell-scripts) mostram como entrar na conta do Lote com a CLI do Azure usando o Azure AD e a Chave Compartilhada.

## <a name="use-azure-batch-cli-templates-and-file-transfer-preview"></a>Usar modelos CLI do Lote do Azure e o arquivo de transferência (Versão prévia)

Você pode usar a CLI do Azure para executar trabalhos do Lote de ponta a ponta sem escrever código. Os arquivos de modelo do Lote dão suporte à criação de pools, trabalhos e tarefas com a CLI do Azure. Você também pode usar a CLI do Azure para carregar arquivos de entrada de trabalho na conta de Armazenamento do Azure associada à conta do Lote e baixar arquivos de saída de trabalho dele. Para saber mais, confira [Usar modelos da CLI do Lote do Azure e Transferência de Arquivos (Versão prévia)](batch-cli-templates.md).

## <a name="sample-shell-scripts"></a>Scripts de shell de exemplo

Os scripts de exemplo listados na tabela a seguir mostram como usar os comandos da CLI do Azure com o serviço Lote e o serviço de gerenciamento em lotes para realizar tarefas comuns. Esses scripts de exemplo cobrem muitos dos comandos disponíveis na CLI do Azure para o Lote. 

| Script | Observações |
|---|---|
| [Criar uma conta do Lote](./scripts/batch-cli-sample-create-account.md) | Cria uma conta do Lote e associa uma conta de armazenamento. |
| [Adicionar um aplicativo](./scripts/batch-cli-sample-add-application.md) | Adiciona um aplicativo e carrega os binários do pacote.|
| [Gerenciar pools do Lote](./scripts/batch-cli-sample-manage-pool.md) | Demonstra como criar, redimensionar e gerenciar pools. |
| [Executar um trabalho e tarefas com o Lote](./scripts/batch-cli-sample-run-job.md) | Demonstra como executar um trabalho e adicionar tarefas. |

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

| Cláusula | Descrição |
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
* Você pode exibir a **saída do comando como JSON** com a opção `--json`. Por exemplo, o `az batch pool show pool001 --json` exibe propriedades do pool001 no formato JSON. Você pode copiar e modificar essa saída a fim de usá-la em um `--json-file` (consulte [Arquivos JSON](#json-files) anteriormente neste artigo).
<!---Loc Comment: Please, check link [JSON files] since it's not redirecting to any location.--->
* O [Fórum do Lote][batch_forum] é monitorado por membros da equipe do Lote. Você pode publicar suas perguntas nesse local caso tenha problemas ou queira obter ajuda com uma operação específica.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).
* Para saber mais sobre recursos do Lote, consulte [Visão geral do Lote do Azure para desenvolvedores](batch-api-basics.md).
* Para saber mais sobre como usar modelos do Lote para criar pools, trabalhos e tarefas sem escrever código, confira [Usar modelos da CLI do Lote do Azure e Transferência de Arquivos (Versão prévia)](batch-cli-templates.md).

[batch_forum]: https://social.msdn.microsoft.com/forums/azure/home?forum=azurebatch
[github_readme]: https://github.com/Azure/azure-xplat-cli/blob/dev/README.md
[rest_api]: https://msdn.microsoft.com/library/azure/dn820158.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
