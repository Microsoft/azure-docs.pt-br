---
title: 'Início Rápido: Conectar-se ao MySQL do Azure com o GitHub Actions'
description: Usar o MySQL do Azure de um fluxo de trabalho do GitHub Actions
author: juliakm
ms.service: mysql
ms.topic: quickstart
ms.author: jukullam
ms.date: 10/12/2020
ms.custom: github-actions-azure
ms.openlocfilehash: 807fdbb1844eb6f89f71e639537a65baf8c76ad5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761755"
---
# <a name="quickstart-use-github-actions-to-connect-to-azure-mysql"></a>Início Rápido: Usar o GitHub Actions para conectar-se ao MySQL do Azure

**APLICA-SE A**: :::image type="icon" source="./media/applies-to/yes.png" border="false":::Banco de dados do Azure para MySQL – servidor único :::image type="icon" source="./media/applies-to/yes.png" border="false":::Banco de Dados do Azure para MySQL – servidor flexível

Comece a usar o [GitHub Actions](https://docs.github.com/en/actions) usando um fluxo de trabalho para implantar atualizações de banco de dados no [Banco de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql/).


## <a name="prerequisites"></a>Pré-requisitos

Serão necessários: 
- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Um repositório do GitHub com os dados de exemplo (`data.sql`). Se você não tiver uma conta do GitHub, [inscreva-se gratuitamente](https://github.com/join).  
- Um servidor do Banco de Dados do Azure para MySQL.
    - [Início Rápido: Criar um servidor do Banco de Dados do Azure para MySQL no portal do Azure](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="workflow-file-overview"></a>Visão geral do arquivo do fluxo de trabalho

Um fluxo de trabalho do GitHub Actions é definido por um arquivo YAML (.yml) no caminho `/.github/workflows/` no repositório. Essa definição contém as várias etapas e os parâmetros que compõem o fluxo de trabalho.

O arquivo tem duas seções:

|Seção  |Tarefas  |
|---------|---------|
|**Autenticação** | 1. Definir uma entidade de serviço. <br /> 2. Criar um segredo do GitHub. |
|**Implantar** | 1. Implantar o banco de dados. |

## <a name="generate-deployment-credentials"></a>Gerar as credenciais de implantação

Crie uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md) com o comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac&preserve-view=true) na [CLI do Azure](/cli/azure/). Execute esse comando com o [Azure Cloud Shell](https://shell.azure.com/) no portal do Azure ou selecionando o botão **Experimentar**.

Substitua os espaços reservados `server-name` pelo nome do seu servidor MySQL hospedado no Azure. Substitua o `subscription-id` e `resource-group` pela ID da assinatura e o grupo de recursos conectados ao servidor MySQL.  

```azurecli-interactive
   az ad sp create-for-rbac --name {server-name} --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
```

A saída é um objeto JSON com as credenciais de atribuição de função que fornecem acesso ao banco de dados semelhante abaixo. Copie esse objeto JSON de saída para mais tarde.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> É sempre uma boa prática permitir acesso mínimo. O escopo no exemplo anterior é limitado ao servidor específico e não ao grupo de recursos inteiro.

## <a name="copy-the-mysql-connection-string"></a>Copiar a cadeia de conexão de MySQL 

No portal do Azure, acesse o Banco de Dados do Azure para MySQL e abra **Configurações** > **Cadeias de conexão**. Copie a cadeia de conexão **ADO.NET**. Substitua os valores de espaço reservado por `your_database` e `your_password`. A cadeia de conexão será semelhante ao exemplo a seguir. 

> [!IMPORTANT]
> - Para servidor único, use **Uid=adminusername@servername** . Observe que **@servername** é obrigatório.
> - Para servidor flexível, use **Uid= adminusername** sem o @servername. Observe que o servidor flexível MySQL está em versão prévia. 


```output
   Server=my-mysql-server.mysql.database.azure.com; Port=3306; Database={your_database}; Uid=adminname@my-mysql-server; Pwd={your_password}; SslMode=Preferred;
```
Você usará a cadeia de conexão como um segredo do GitHub. 

## <a name="configure-the-github-secrets"></a>Configurar os segredos do GitHub

1. Em [GitHub](https://github.com/), procure seu repositório.

1. Selecione **Configurações > Segredos > Novo segredo**.

1. Cole toda a saída JSON do comando da CLI do Azure no campo valor do segredo. Dê ao segredo o nome `AZURE_CREDENTIALS`.

    Ao configurar o arquivo de fluxo de trabalho posteriormente, você usa o segredo para o `creds` de entrada da ação de Logon do Azure. Por exemplo:

    ```yaml
    - uses: azure/login@v1
        with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
   ```

1. Selecione **Novo segredo** novamente. 

1. Cole o valor da cadeia de conexão no campo de valor do segredo. Dê ao segredo o nome `AZURE_MYSQL_CONNECTION_STRING`.


## <a name="add-your-workflow"></a>Adicionar seu fluxo de trabalho

1. Acesse **Ações** para seu repositório do GitHub. 

2. Selecione **Configurar seu fluxo de trabalho por conta própria**. 

2. Exclua tudo depois da seção `on:` do seu arquivo de fluxo de trabalho. Por exemplo, o fluxo de trabalho restante pode ter a aparência a seguir. 

    ```yaml
    name: CI

    on:
    push:
        branches: [ master ]
    pull_request:
        branches: [ master ]
    ```

1. Renomeie o fluxo de trabalho `MySQL for GitHub Actions` e adicione as ações de check-out e logon. Essas ações farão o check-out do código do site e a autenticação com o Azure usando o segredo do GitHub `AZURE_CREDENTIALS` criado anteriormente. 

    ```yaml
    name: MySQL for GitHub Actions

    on:
        push:
            branches: [ master ]
        pull_request:
            branches: [ master ]

    jobs:
        build:
            runs-on: windows-latest
            steps:
            - uses: actions/checkout@v1
            - uses: azure/login@v1
                with:
                    creds: ${{ secrets.AZURE_CREDENTIALS }}
    ```

1. Use a ação Implantar do MySQL do Azure para se conectar à sua instância do MySQL. Substitua `MYSQL_SERVER_NAME` pelo nome do seu servidor. Você deve ter um arquivo de dados do MySQL chamado `data.sql` no nível raiz do seu repositório. 

    ```yaml
    - uses: azure/mysql@v1
      with:
        server-name: MYSQL_SERVER_NAME
        connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
        sql-file: './data.sql'
    ``` 

1. Conclua o fluxo de trabalho adicionando uma ação para fazer logoff do Azure. Este é o fluxo de trabalho concluído. O arquivo será exibido na pasta `.github/workflows` do seu repositório.

    ```yaml
    name: MySQL for GitHub Actions

    on:
         push:
            branches: [ master ]
        pull_request:
            branches: [ master ]


     jobs:
        build:
            runs-on: windows-latest
            steps:
            - uses: actions/checkout@v1
            - uses: azure/login@v1
                with:
                    creds: ${{ secrets.AZURE_CREDENTIALS }}

            - uses: azure/mysql@v1
                with:
                    server-name: MYSQL_SERVER_NAME
                    connection-string: ${{ secrets.AZURE_MYSQL_CONNECTION_STRING }}
                    sql-file: './data.sql'

            # Azure logout 
            - name: logout
                run: |
                    az logout
    ```

## <a name="review-your-deployment"></a>Examinar sua implantação

1. Acesse **Ações** para seu repositório do GitHub. 

1. Abra o primeiro resultado para ver os logs detalhados da execução do fluxo de trabalho. 
 
    :::image type="content" source="media/quickstart-mysql-github-actions/github-actions-run-mysql.png" alt-text="Log das GitHub Actions executadas":::

## <a name="clean-up-resources"></a>Limpar os recursos

Quando o banco de dados MySQL do Azure e o repositório não forem mais necessários, limpe os recursos implantados excluindo o grupo de recursos e seu repositório do GitHub. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Saiba mais sobre a integração do Azure com o GitHub](/azure/developer/github/)
