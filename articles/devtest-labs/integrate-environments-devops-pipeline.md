---
title: Integre ambientes em Azure Pipelines no Azure DevTest Labs
description: Saiba como integrar ambientes de Azure DevTest Labs em seus pipelines de CI (integração contínua) e distribuição contínua (CD) do Azure DevOps.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be726b2a3f67fd3dada4fdc3cf794922a3c18d06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85483016"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integre ambientes em seus pipelines de CI/CD do Azure DevOps
Você pode usar a extensão de tarefas de Azure DevTest Labs que é instalada no Azure DevOps Services (anteriormente conhecido como Visual Studio Team Services) para integrar facilmente seu pipeline de integração contínua (CI)/entrega contínua (CD) com o Azure DevTest Labs. Essas extensões facilitam a implantação rápida de um [ambiente](devtest-lab-test-env.md) para uma tarefa de teste específica e, em seguida, a exclusão quando o teste é concluído. 

Este artigo mostra como criar e implantar um ambiente e, em seguida, excluir o ambiente, tudo em um único pipeline completo. Normalmente, você executaria cada uma dessas tarefas individualmente em seu próprio pipeline personalizado de compilação-teste-implantação. As extensões usadas neste artigo são além das [tarefas criar/excluir VM DTL](devtest-lab-integrate-ci-cd.md):

- Criar um ambiente
- Excluir um ambiente

## <a name="before-you-begin"></a>Antes de começar
Antes de integrar seu pipeline de CI/CD com Azure DevTest Labs, instale [Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) extensão de tarefas do Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Criar e configurar o laboratório para ambientes
Esta seção descreve como criar e configurar um laboratório no qual o ambiente do Azure será implantado.

1. [Crie um laboratório](devtest-lab-create-lab.md) se você ainda não tiver um. 
2. Configure o laboratório e crie um modelo de ambiente seguindo as instruções deste artigo: [criar ambientes de várias VMs e recursos de PaaS com modelos de Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
3. Para este exemplo, use um modelo de início rápido do Azure existente [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/) .
4. Copie a pasta **201-Web-App-Redis-cache-SQL-Database** para a pasta **ArmTemplate** no repositório configurado na etapa 2.

## <a name="create-a-release-definition"></a>Criar uma definição de versão
Para criar uma definição da versão, faça o seguinte:

1.  Na guia **versões** do **Hub Build & versão**, selecione o botão de sinal de **adição (+)** .
2.  Na janela **Criar definição da versão**, selecione o modelo **Vazio** e selecione **Avançar**.
3.  Selecione **Escolher Depois** e selecione **Criar** para criar uma nova definição da versão com um ambiente padrão e nenhum artefato vinculado.
4.  Para abrir o menu de atalho, na nova definição de versão, selecione as **reticências (...)** ao lado do nome do ambiente e, em seguida, selecione **configurar variáveis**.
5.  Na janela **Configurar – ambiente**, para as variáveis usadas nas tarefas de definição da versão, insira os seguintes valores:
1.  Para **administratorLogin**, insira o nome de logon do administrador do SQL.
2.  Para **administratorLoginPassword**, insira a senha a ser usada pelo logon do administrador do SQL. Use o ícone de "cadeado" para ocultar e proteger a senha.
3.  Para **DatabaseName**, insira o nome do banco de dados SQL.
4.  Essas variáveis são específicas para os ambientes de exemplo, ambientes diferentes podem ter variáveis diferentes.

## <a name="create-an-environment"></a>Criar um ambiente
O próximo estágio da implantação é criar o ambiente a ser usado para fins de desenvolvimento ou teste.

1. Na definição da versão, selecione **Adicionar tarefas**.
2. Na guia **tarefas** , adicione uma tarefa Azure DevTest Labs criar ambiente. Configure as tarefas da seguinte forma:
    1. Para **Assinatura do Azure RM**, selecione uma conexão na lista **Conexões de Serviço do Azure Disponíveis** ou crie uma conexão de permissões mais restritas para sua assinatura do Azure. Para saber mais, confira [Ponto de extremidade de serviço do Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
2. Para **nome do laboratório**, selecione o nome da instância que você criou anteriormente *.
3. Para **nome do repositório**, selecione o repositório onde o modelo do Resource Manager (201) foi enviado por push para *.
4. Para **nome do modelo**, selecione o nome do ambiente que você salvou em seu repositório de código-fonte *. 
5. O nome do **laboratório**, o **nome do repositório** e o **nome do modelo** são as representações amigáveis das IDs de recursos do Azure. A inserção manual do nome amigável causará falhas, use as listas suspensas para selecionar as informações.
6. Para **nome do ambiente**, insira um nome para identificar exclusivamente a instância de ambiente no laboratório.  Ele deve ser exclusivo no laboratório.
7. O **arquivo de parâmetro** e os **parâmetros** permitem que os parâmetros personalizados sejam passados para o ambiente. Um ou ambos podem ser usados para definir os valores de parâmetro. Para este exemplo, a seção de parâmetros será usada. Use os nomes das variáveis que você definiu no ambiente, por exemplo: `-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. As informações no modelo de ambiente podem ser passadas na seção de saída do modelo. Marque **criar variáveis de saída com base na saída do modelo de ambiente** para que outras tarefas possam usar os dados. `$(Reference name.Output Name)` é o padrão a ser seguido. Por exemplo, se o nome de referência era DTL e o nome de saída no modelo era local, a variável seria `$(DTL.location)` .

## <a name="delete-the-environment"></a>Excluir o ambiente
O estágio final é excluir o ambiente implantado em sua instância de Azure DevTest Labs. Normalmente, você excluiria o ambiente depois de executar as tarefas de desenvolvimento ou executar os testes necessários nos recursos implantados.

Na definição da versão, selecione **adicionar tarefas** e, em seguida, na guia **implantar** , adicione uma tarefa **Azure DevTest Labs excluir ambiente** . Configure-o da seguinte maneira:

1. Para excluir a VM, consulte [tarefas de Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. Para **Assinatura do Azure RM**, selecione uma conexão na lista **Conexões de Serviço do Azure Disponíveis** ou crie uma conexão de permissões mais restritas para sua assinatura do Azure. Para saber mais, confira [Ponto de extremidade de serviço do Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints).
    2. Para **nome do laboratório**, selecione o laboratório no qual o ambiente existe.
    3. Para **nome do ambiente**, insira o nome do ambiente a ser removido.
2. Insira um nome para a definição da versão e, em seguida, salve-a.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir: 
- [Crie ambientes com várias VMs com modelos do Resource Manager](devtest-lab-create-environment-from-arm.md).
- Modelos de início rápido do Resource Manager para a automação do DevTest Labs do [repositório GitHub do DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
- [Página de solução de problemas do VSTS](/azure/devops/pipelines/troubleshooting)

