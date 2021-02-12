---
title: Efetuar pull da configuração do aplicativo settingsfromo com Azure Pipelines
description: Aprenda a usar Azure Pipelines para efetuar pull de valores de chave para um repositório de configuração de aplicativo
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 7bd163781203a277f4c9d6866a156c11e4d5d520
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979565"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Configurações de pull para configuração de aplicativo com Azure Pipelines

A tarefa de [configuração de Azure app](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) extrai valores de chave de seu repositório de configuração de aplicativo e os define como variáveis de pipeline do Azure, que podem ser consumidas por tarefas subsequentes. Essa tarefa complementa a tarefa [push de configuração de Azure app](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) que envia por push os valores de chave de um arquivo de configuração para o repositório de configurações do aplicativo. Para obter mais informações, consulte [configurações de push para configuração de aplicativo com Azure pipelines](push-kv-devops-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- Repositório de configuração de aplicativo – crie um gratuitamente no [portal do Azure](https://portal.azure.com).
- Projeto DevOps do Azure – [crie um gratuitamente](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure App tarefa de configuração-Baixe gratuitamente do [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.).  

## <a name="create-a-service-connection"></a>Criar uma conexão de serviço

Uma [conexão de serviço](/azure/devops/pipelines/library/service-endpoints) permite que você acesse recursos em sua assinatura do Azure de seu projeto DevOps do Azure.

1. No Azure DevOps, vá para o projeto que contém o pipeline de destino e abra as **configurações do projeto** na parte inferior esquerda.
1. Em **pipelines** , selecione **conexões de serviço**.
1. Se você não tiver nenhuma conexão de serviço existente, clique no botão **criar conexão de serviço** no meio da tela. Caso contrário, clique em **nova conexão de serviço** no canto superior direito da página.
1. Selecione **Azure Resource Manager**.
1. Selecione a **entidade de serviço (automática)**.
1. Preencha sua assinatura e recurso. Dê um nome à sua conexão de serviço.

Agora que a conexão de serviço foi criada, localize o nome da entidade de serviço atribuída a ela. Você adicionará uma nova atribuição de função a essa entidade de serviço na próxima etapa.

1. Vá para **configurações do projeto**  >  **conexões de serviço**.
1. Selecione a conexão de serviço que você criou na seção anterior.
1. Selecione **gerenciar entidade de serviço**.
1. Observe o **nome de exibição** listado.

## <a name="add-role-assignment"></a>Adicionar atribuição de função

Atribua a função de configuração de aplicativo apropriada à conexão de serviço que está sendo usada na tarefa para que a tarefa possa acessar o repositório de configurações de aplicativo.

1. Navegue até o repositório de configuração do aplicativo de destino. Para obter instruções sobre como configurar um repositório de configuração de aplicativo, consulte [criar um repositório de configuração de aplicativo](./quickstart-dotnet-core-app.md#create-an-app-configuration-store) em um dos guias de início rápido de configuração de Azure app.
1. À esquerda, selecione **controle de acesso (iam)**.
1. Na parte superior, selecione **+ Adicionar** e escolha **Adicionar atribuição de função**.
1. Em **função**, selecione **leitor de dados de configuração de aplicativo**. Essa função permite que a tarefa seja lida no repositório de configuração do aplicativo. 
1. Selecione a entidade de serviço associada à conexão de serviço que você criou na seção anterior.

> [!NOTE]
> Para resolver referências de Azure Key Vault dentro da configuração do aplicativo, a conexão de serviço também deve receber permissão para ler segredos nos cofres de chaves do Azure referenciados.
  
## <a name="use-in-builds"></a>Usar em compilações

Esta seção abordará como usar a tarefa de configuração de Azure App em um pipeline de compilação DevOps do Azure.

1. Navegue até a página Criar pipeline **clicando em pipelines pipelines**  >  . Para obter a documentação do pipeline de compilação, consulte  [criar seu primeiro pipeline](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser).
      - Se você estiver criando um novo pipeline de compilação, clique em **novo pipeline**, selecione o repositório para seu pipeline. Selecione **Mostrar assistente** no lado direito do pipeline e procure a tarefa de configuração de **Azure app** .
      - Se você estiver usando um pipeline de Build existente, selecione **Editar** para editar o pipeline. Na guia **tarefas** , procure a tarefa de **configuração de Azure app** .
1. Configure os parâmetros necessários para que a tarefa receba os valores de chave do repositório de configurações de aplicativo. As descrições dos parâmetros estão disponíveis na seção **parâmetros** abaixo e nas dicas de ferramenta ao lado de cada parâmetro.
      - Defina o parâmetro de **assinatura do Azure** como o nome da conexão de serviço que você criou em uma etapa anterior.
      - Defina o **nome de configuração do aplicativo** como o nome do recurso do repositório de configuração do aplicativo.
      - Deixe os valores padrão para os parâmetros restantes.
1. Salve e enfileirar uma compilação. O log de compilação exibirá todas as falhas ocorridas durante a execução da tarefa.

## <a name="use-in-releases"></a>Usar em versões

Esta seção abordará como usar a tarefa de configuração de Azure App em um pipeline de versão do Azure DevOps.

1. Navegue até a página de pipeline de liberação selecionando versões de **pipelines**  >  . Para obter a documentação do pipeline de liberação, consulte [pipelines de versão](/azure/devops/pipelines/release).
1. Escolha um pipeline de lançamento existente. Se você não tiver um, clique em **novo pipeline** para criar um novo.
1. Selecione o botão **Editar** no canto superior direito para editar o pipeline de liberação.
1. Escolha o **estágio** para adicionar a tarefa. Para obter mais informações sobre os estágios, consulte [Adicionar estágios, dependências, & condições](/azure/devops/pipelines/release/environments).
1. Clique em **+** para "executar no agente" e, em seguida, adicione a tarefa de **configuração de Azure app** na guia **adicionar tarefas** .
1. Configure os parâmetros necessários na tarefa para efetuar pull dos valores de chave de seu repositório de configurações de aplicativo. As descrições dos parâmetros estão disponíveis na seção **parâmetros** abaixo e nas dicas de ferramenta ao lado de cada parâmetro.
      - Defina o parâmetro de **assinatura do Azure** como o nome da conexão de serviço que você criou em uma etapa anterior.
      - Defina o **nome de configuração do aplicativo** como o nome do recurso do repositório de configuração do aplicativo.
      - Deixe os valores padrão para os parâmetros restantes.
1. Salve e enfileirar uma versão. O log de liberação exibirá todas as falhas encontradas durante a execução da tarefa.

## <a name="parameters"></a>Parâmetros

Os parâmetros a seguir são usados pela tarefa de configuração de Azure App:

- **Assinatura do Azure**: uma lista suspensa que contém as conexões de serviço do Azure disponíveis. Para atualizar e atualizar sua lista de conexões de serviço do Azure disponíveis, pressione o botão **Atualizar assinatura do Azure** à direita da caixa de texto.
- **Nome da configuração do aplicativo**: uma lista suspensa que carrega os armazenamentos de configuração disponíveis na assinatura selecionada. Para atualizar e atualizar sua lista de repositórios de configuração disponíveis, pressione o botão **Atualizar nome de configuração do aplicativo** à direita da caixa de texto.
- **Filtro de chave**: o filtro pode ser usado para selecionar quais valores de chave são solicitados da configuração Azure app. Um valor de * selecionará todos os valores de chave. Para obter mais informações sobre o, consulte [valores de chave de consulta](concept-key-value.md#query-key-values).
- **Rótulo**: especifica qual rótulo deve ser usado ao selecionar valores de chave do repositório de configurações de aplicativo. Se nenhum rótulo for fornecido, os valores de chave com o rótulo não serão recuperados. Os seguintes caracteres não são permitidos:, *.
- **Recortar prefixo de chave**: especifica um ou mais prefixos que devem ser cortados das chaves de configuração de aplicativo antes de configurá-los como variáveis. Vários prefixos podem ser separados por um caractere de nova linha.

## <a name="use-key-values-in-subsequent-tasks"></a>Usar valores de chave em tarefas subsequentes

Os valores de chave que são buscados na configuração do aplicativo são definidos como variáveis de pipeline, que são acessíveis como variáveis de ambiente. A chave da variável de ambiente é a chave do valor-chave que é recuperado da configuração do aplicativo depois de cortar o prefixo, se especificado.

Por exemplo, se uma tarefa subsequente executar um script do PowerShell, ela poderá consumir um valor chave com a chave ' myBuildSetting ' como esta:
```powershell
echo "$env:myBuildSetting"
```
E o valor será impresso no console.

> [!NOTE]
> Azure Key Vault referências dentro da configuração do aplicativo serão resolvidas e definidas como [variáveis secretas](/azure/devops/pipelines/process/variables#secret-variables). Nos pipelines do Azure, as variáveis secretas são mascaradas do log. Elas não são passadas para tarefas como variáveis de ambiente e devem ser passadas como entradas. 

## <a name="troubleshooting"></a>Solução de problemas

Se ocorrer um erro inesperado, os logs de depuração poderão ser habilitados definindo a variável de pipeline `system.debug` como `true` .

## <a name="faq"></a>Perguntas frequentes

**Como fazer compor minha configuração de várias chaves e rótulos?**

Há ocasiões em que a configuração pode precisar ser composta de vários rótulos, por exemplo, padrão e desenvolvimento. Várias tarefas de configuração de aplicativo podem ser usadas em um pipeline para implementar esse cenário. Os valores de chave buscados por uma tarefa em uma etapa posterior substituirão quaisquer valores das etapas anteriores. No exemplo acima, uma tarefa pode ser usada para selecionar valores-chave com o rótulo padrão, enquanto uma segunda tarefa pode selecionar valores-chave com o rótulo de desenvolvimento. As chaves com o rótulo de desenvolvimento substituirão as mesmas chaves pelo rótulo padrão.
