---
title: Efetuar pull de configurações da Configuração de Aplicativos com o Azure Pipelines
description: Aprenda a usar o Azure Pipelines para efetuar pull de valores de chave para um repositório da Configuração de Aplicativos
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 1c01984f6a359c0fd1f5d06d26d97d4a84973f57
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056718"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Efetuar pull de configurações para a Configuração de Aplicativos com o Azure Pipelines

A tarefa [Configuração de Aplicativos do Azure](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) extrai valores de chave do repositório da Configuração de Aplicativos e os define como variáveis de pipeline do Azure, que podem ser consumidas pelas próximas tarefas. Essa tarefa complementa a tarefa [Push da Configuração de Aplicativos do Azure](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) que envia por push os valores de chave de um arquivo de configuração para o repositório da Configuração de Aplicativos. Para obter mais informações, confira [Enviar configurações por push para a Configuração de Aplicativos com o Azure Pipelines](push-kv-devops-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- Repositório da Configuração de Aplicativos: crie um gratuitamente no [portal do Azure](https://portal.azure.com).
- Projeto do Azure DevOps: [crie um gratuitamente](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Tarefa Configuração de Aplicativos do Azure: baixe-a gratuitamente do [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.).  

## <a name="create-a-service-connection"></a>Criar uma conexão de serviço

Uma [conexão de serviço](/azure/devops/pipelines/library/service-endpoints) permite que você acesse os recursos na sua assinatura do Azure por meio do projeto do Azure DevOps.

1. No Azure DevOps, acesse o projeto que contém o pipeline de destino e abra as **Configurações do projeto** no canto inferior esquerdo.
1. Em **Pipelines**, selecione **Conexões de serviço**.
1. Se você não tem nenhuma conexão de serviço, clique no botão **Criar conexão de serviço** no meio da tela. Caso contrário, clique em **Nova conexão de serviço** no canto superior direito da página.
1. Selecione **Azure Resource Manager**.
![Captura de tela que mostra a seleção do Azure Resource Manager na lista suspensa Nova conexão de serviço.](./media/new-service-connection.png)
1. Na caixa de diálogo **Método de autenticação**, selecione **Entidade de serviço (automático)** .
    > [!NOTE]
    > Atualmente, não há suporte para a autenticação da **identidade gerenciada** na tarefa Configuração de Aplicativos.
1. Preencha sua assinatura e o recurso. Dê um nome à conexão de serviço.

Agora que a conexão de serviço foi criada, localize o nome da entidade de serviço atribuída a ela. Você adicionará uma nova atribuição de função a essa entidade de serviço na próxima etapa.

1. Acesse **Configurações do Projeto** > **Conexões de serviço**.
1. Escolha a conexão de serviço criada na seção anterior.
1. Selecione **Gerenciar Entidade de Serviço**.
1. Anote o **Nome de exibição** listado.

## <a name="add-role-assignment"></a>Adicionar atribuição de função

Atribua a função apropriada da Configuração de Aplicativos à conexão de serviço que está sendo usada na tarefa para que a tarefa possa acessar o repositório da Configuração de Aplicativos.

1. Acesse o repositório de destino da Configuração de Aplicativos. Para obter um passo a passo de como configurar um repositório da Configuração de Aplicativos, confira [Criar um repositório da Configuração de Aplicativos](./quickstart-dotnet-core-app.md#create-an-app-configuration-store) em um dos guias de início rápido da Configuração de Aplicativos do Azure.
1. À esquerda, selecione **Controle de acesso (IAM)** .
1. No lado direito, clique no botão **Adicionar atribuições de função**.
![Captura de tela que mostra o botão Adicionar atribuições de função.](./media/add-role-assignment-button.png)
1. Em **Função**, selecione **Leitor de Dados da Configuração de Aplicativos**. Essa função permite que a tarefa seja lida no repositório da Configuração de Aplicativos. 
1. Selecione a entidade de serviço associada à conexão de serviço criada na seção anterior.
![Captura de tela que mostra a tela Adicionar atribuição de função.](./media/add-role-assignment-reader.png)

> [!NOTE]
> Para resolver as referências do Azure Key Vault na Configuração de Aplicativos, a conexão de serviço também precisa receber permissão para ler segredos nos cofres de chaves do Azure referenciados.
  
## <a name="use-in-builds"></a>Uso em builds

Esta seção abordará como usar a tarefa Configuração de Aplicativos do Azure em um pipeline de build do Azure DevOps.

1. Acesse a página do pipeline de build clicando em **Pipelines** > **Pipelines**. Para obter a documentação do pipeline de build, confira [Criar seu primeiro pipeline](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser).
      - Se você estiver criando um pipeline de build, na última etapa do processo, na guia **Examinar**, selecione **Mostrar assistente** no lado direito do pipeline.
      ![Captura de tela que mostra o botão Mostrar assistente para um novo pipeline.](./media/new-pipeline-show-assistant.png)
      - Se você estiver usando um pipeline de build existente, clique no botão **Editar** no canto superior direito.
      ![Captura de tela que mostra o botão Editar de um pipeline existente.](./media/existing-pipeline-show-assistant.png)
1. Pesquise a tarefa **Configuração de Aplicativos do Azure**.
![Captura de tela que mostra a caixa de diálogo Adicionar Tarefa com a Configuração de Aplicativos do Azure na caixa de pesquisa.](./media/add-azure-app-configuration-task.png)
1. Configure os parâmetros necessários para que a tarefa efetue pull dos valores de chave do repositório da Configuração de Aplicativos. As descrições dos parâmetros estão disponíveis na seção **Parâmetros** abaixo e nas dicas de ferramenta ao lado de cada parâmetro.
      - Defina o parâmetro de **assinatura do Azure** com o nome da conexão de serviço que você criou em uma etapa anterior.
      - Defina o **nome da Configuração de Aplicativos** com o nome do recurso do repositório da Configuração de Aplicativos.
      - Mantenha os valores padrão nos parâmetros restantes.
![Captura de tela que mostra os parâmetros da tarefa Configuração de Aplicativos.](./media/azure-app-configuration-parameters.png)
1. Salve e coloque um build na fila. O log de build exibirá todas as falhas ocorridas durante a execução da tarefa.

## <a name="use-in-releases"></a>Uso em versões

Esta seção abordará como usar a tarefa Configuração de Aplicativos do Azure em um pipeline de lançamento do Azure DevOps.

1. Acesse a página do pipeline de lançamento selecionando **Pipelines** > **Versões**. Para obter a documentação do pipeline de lançamento, confira [Pipelines de lançamento](/azure/devops/pipelines/release).
1. Escolha um pipeline de lançamento existente. Se não tiver um, clique em **Novo pipeline** para criar um.
1. Selecione o botão **Editar** no canto superior direito para editar o pipeline de lançamento.
1. Na lista suspensa **Tarefas**, escolha a **Fase** à qual deseja adicionar a tarefa. Encontre mais informações sobre as fases [aqui](/azure/devops/pipelines/release/environments).
![Captura de tela que mostra a fase selecionada na lista suspensa Tarefas.](./media/pipeline-stage-tasks.png)
1. Clique em **+** próximo ao trabalho ao qual deseja adicionar uma nova tarefa.
![Captura de tela que mostra o botão de adição ao lado do trabalho.](./media/add-task-to-job.png)
1. Pesquise a tarefa **Configuração de Aplicativos do Azure**.
![Captura de tela que mostra a caixa de diálogo Adicionar Tarefa com a Configuração de Aplicativos do Azure na caixa de pesquisa.](./media/add-azure-app-configuration-task.png)
1. Configure os parâmetros necessários na tarefa para efetuar pull dos valores de chave do seu repositório da Configuração de Aplicativos. As descrições dos parâmetros estão disponíveis na seção **Parâmetros** abaixo e nas dicas de ferramenta ao lado de cada parâmetro.
      - Defina o parâmetro de **assinatura do Azure** com o nome da conexão de serviço que você criou em uma etapa anterior.
      - Defina o **nome da Configuração de Aplicativos** com o nome do recurso do repositório da Configuração de Aplicativos.
      - Mantenha os valores padrão nos parâmetros restantes.
1. Salve e coloque uma versão na fila. O log de versão exibirá todas as falhas encontradas durante a execução da tarefa.

## <a name="parameters"></a>Parâmetros

Os seguintes parâmetros são usados pela tarefa Configuração de Aplicativos do Azure:

- **Assinatura do Azure**: uma lista suspensa que contém as conexões de serviço do Azure disponíveis. Para atualizar a lista de conexões de serviço do Azure disponíveis, pressione o botão **Atualizar assinatura do Azure** à direita da caixa de texto.
- **Nome da Configuração de Aplicativos**: uma lista suspensa que carrega os repositórios de configurações disponíveis na assinatura selecionada. Para atualizar a lista de repositórios de configurações disponíveis, pressione o botão **Atualizar Nome da Configuração de Aplicativos** à direita da caixa de texto.
- **Filtro de Chave**: o filtro pode ser usado para selecionar quais valores de chave são solicitados pela Configuração de Aplicativos do Azure. Um valor igual a * selecionará todos os valores de chave. Para obter mais informações, confira [Consultar valores de chave](concept-key-value.md#query-key-values).
- **Rótulo**: especifica qual rótulo deve ser usado na seleção de valores de chave do repositório da Configuração de Aplicativos. Se nenhum rótulo for fornecido, os valores de chave Sem rótulo não serão recuperados. Os seguintes caracteres não são permitidos: , e *.
- **Recortar Prefixo de Chave**: especifica um ou mais prefixos que devem ser cortados das chaves da Configuração de Aplicativos antes de configurá-los como variáveis. Vários prefixos podem ser separados por um caractere de nova linha.

## <a name="use-key-values-in-subsequent-tasks"></a>Usar valores de chave nas tarefas seguintes

Os valores de chave que são buscados na Configuração de Aplicativos são definidos como variáveis de pipeline, que são acessíveis como variáveis de ambiente. A chave da variável de ambiente é a chave do valor de chave recuperada da Configuração de Aplicativos após o recorte do prefixo, se especificado.

Por exemplo, se uma tarefa seguinte executar um script do PowerShell, ela poderá consumir um valor de chave com a chave 'myBuildSetting' desta forma:
```powershell
echo "$env:myBuildSetting"
```
Além disso, o valor será impresso no console.

> [!NOTE]
> As referências do Azure Key Vault dentro da Configuração de Aplicativos serão resolvidas e definidas como [variáveis secretas](/azure/devops/pipelines/process/variables#secret-variables). No Azure Pipelines, as variáveis secretas são mascaradas do log. Elas não são transmitidas para tarefas como variáveis de ambiente e precisam ser transmitidas como entradas. 

## <a name="troubleshooting"></a>Solução de problemas

Em caso de um erro inesperado, os logs de depuração podem ser habilitados pela definição da variável de pipeline `system.debug` como `true`.

## <a name="faq"></a>Perguntas frequentes

**Como fazer para compor minha configuração com base em uma variedade de chaves e rótulos?**

Há ocasiões em que a configuração pode precisar ser composta com base em vários rótulos, por exemplo, padrão e desenvolvimento. Várias tarefas da Configuração de Aplicativos podem ser usadas em um pipeline para implementar esse cenário. Os valores de chave buscados por uma tarefa em uma etapa posterior substituirão os valores das etapas anteriores. No exemplo acima, uma tarefa pode ser usada para selecionar valores de chave com o rótulo padrão, enquanto uma segunda tarefa pode selecionar valores de chave com o rótulo de desenvolvimento. As chaves com o rótulo de desenvolvimento substituirão as mesmas chaves pelo rótulo padrão.
