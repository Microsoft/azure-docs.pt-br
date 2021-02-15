---
title: 'Tutorial: Implantar aplicativos ASP.NET no Azure Functions com o Azure DevOps Starter'
description: Com o Azure DevOps Starter, é mais fácil começar a usar o Azure. Com o DevOps Starter, é possível implantar seu aplicativo ASP.NET no Azure Functions com algumas etapas rápidas.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-csharp
ms.openlocfilehash: 3277fefb5615fd492b2859b741ef8e6f991eaf23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355467"
---
# <a name="deploy-to-azure-functions-with-devops-starter"></a>Implantar no Azure Functions com o DevOps Starter

O Azure DevOps Starter apresenta uma experiência simplificada na qual é possível trazer seu código e seu repositório Git existentes ou escolher um aplicativo de exemplo para criar um pipeline de CI (integração contínua) e CD (entrega contínua) para o Azure.

Além disso, o DevOps Starter:

* Cria automaticamente recursos do Azure, como o Azure Functions

* Cria e configura um pipeline de lançamento no Azure DevOps para CI/CD

Neste tutorial, você irá:

> [!div class="checklist"]
>* Usar o DevOps Starter para implantar um aplicativo ASP.NET em uma Função do Azure
>* Configurar o Azure DevOps e uma assinatura do Azure
>* Examinar a Função do Azure
>* Examinar o pipeline de CI
>* Examinar o pipeline de CD
>* Confirmar alterações no Git e implantá-las automaticamente no Azure
>* Limpar os recursos

Atualmente, os runtimes com suporte para as funções são **.NET** e **Node.js**. Usamos o tempo de execução do .NET para este tutorial para implantação no Azure Functions. 

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Você pode obter uma gratuita por meio do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-starter-to-deploy-an-aspnet-app-to-azure-functions"></a>Usar o DevOps Starter para implantar um aplicativo ASP.NET no Azure Functions

O DevOps Starter cria um pipeline de CI/CD no Azure Pipelines. É possível criar uma nova organização do Azure DevOps ou usar uma existente. O DevOps Projects também cria recursos do Azure, como um Hub IoT, na assinatura do Azure de sua escolha.

1. Entre no [Portal do Azure](https://portal.azure.com)

1. Na caixa de pesquisa, digite **DevOps Starter** e selecione. Clique em **Adicionar** para criar um.

    ![O painel do DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Selecione **.NET** e selecione **Avançar**. Em **Escolher uma estrutura do aplicativo**, selecione **ASP.NET** e clique em **Avançar**.

1. Selecione **Aplicativo de Funções** e, em seguida, selecione **Avançar**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Configurar o Azure DevOps e uma assinatura do Azure

1. Insira um nome para o projeto do Azure DevOps.

1. Crie uma nova organização do Azure DevOps ou selecione uma existente.

1. Selecione sua assinatura do Azure.

1. Para ver configurações adicionais do Azure e identificar o tipo de preço e a localização, clique em **Configurações adicionais**. Este painel exibe várias opções para configurar o tipo de preço e o local dos serviços do Azure.

1. Saia da área de configuração do Azure e selecione **Concluído**.

1. Depois de alguns minutos, o processo é concluído. Um exemplo de aplicativo do ASP.NET é configurado em um repositório Git na sua organização do Azure DevOps, um Aplicativo de Funções e o Application Insights são criados, um pipeline de CI/CD é executado e seu aplicativo é implantado no Azure.

   Após tudo isso ser concluído, o painel do Azure DevOps Starter é exibido no portal do Azure. Também é possível ir até o painel do DevOps Starter diretamente de **Todos os recursos** no portal do Azure.

   Esse painel oferece visibilidade de seu repositório de código do Azure DevOps, seu pipeline de CI/CD e sua Função do Azure. Também é possível configurar outras opções de CI/CD no pipeline do Azure DevOps. À direita, selecione **Aplicativo de Funções** para que seja exibido.

## <a name="examine-the-function-app"></a>Examinar o Aplicativo de Funções

O DevOps Starter configura automaticamente o aplicativo de funções, que pode ser explorado e personalizado. Para conhecer o aplicativo de funções, faça o seguinte:

1. Vá até o painel do DevOps Starter.

    ![Painel do DevOps Projects](_img/azure-devops-project-functions/fapp-dashboard.png)

1. À direita, selecione o aplicativo de funções. É aberto um painel para o aplicativo de funções. Nessa exibição, você pode executar várias ações, como monitoramento de operações e pesquisa de logs.

    ![Aplicativo de Funções](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Examinar o pipeline de CI

O DevOps Starter configura automaticamente um pipeline de CI/CD em sua organização do Azure DevOps. É possível explorar e personalizar o pipeline. Para se familiarizar com ele, faça o seguinte:

1. Vá até o painel do DevOps Starter.

1. Clique no hiperlink em **Build**. Uma guia do navegador exibe o pipeline de build do seu novo projeto.

    ![A captura de tela mostra o DevOps Starter no portal do Azure com uma seta apontando para um link em Build no pipeline de CI/CD.](_img/azure-devops-project-functions/build.png)

1. Selecione **Editar**. Nesse painel, é possível examinar as diversas tarefas do pipeline de build. O build realiza várias tarefas, como buscar código-fonte no repositório Git, compilar o aplicativo, executar testes de unidade e publicar as saídas usadas para implantações.

1. Selecione **Gatilhos**. O DevOps Starter cria automaticamente um gatilho de CI, e cada confirmação no repositório inicia um novo build. Como opção, é possível incluir ou excluir os branches do processo de CI.

1. Selecione **Retenção**. Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

1. Na parte superior do pipeline de build, selecione o nome desse pipeline.

1. Altere o nome do pipeline de build para algo mais descritivo e, em seguida, selecione **Salvar** no menu suspenso **Salvar e enfileirar**.

1. Embaixo do nome do pipeline de build, selecione **Histórico**. Esse painel exibe uma trilha de auditoria das alterações recentes do build. O Azure DevOps controla quaisquer alterações feitas no pipeline de build e permite que você compare versões.

## <a name="examine-the-cd-release-pipeline"></a>Examinar o pipeline de lançamento de CD

O DevOps Starter cria e configura automaticamente as etapas necessárias para implantar a organização do Azure DevOps na assinatura do Azure. Essas etapas incluem a configuração de uma conexão de serviço do Azure para autenticar o Azure DevOps em sua assinatura do Azure. A automação também cria um pipeline de lançamento, o qual fornece a CD para o Azure. Para saber mais sobre um pipeline de lançamento, faça o seguinte:

1. Navegue até **Pipelines | Versões**.

1. Clique em **Editar**.

1. Em **Artefatos**, selecione **Soltar**. O pipeline de build examinado nas etapas anteriores produz a saída usada para o artefato.

1. À direita do ícone **Soltar**, selecione **Gatilho de implantação contínua**. Esse pipeline de lançamento tem um gatilho de CD habilitado, que executa uma implantação sempre que houver um novo artefato de build disponível. Outra opção é desabilitar o gatilho para que suas implantações exijam uma execução manual.

1. À direita, selecione **Exibir versões** para exibir um histórico de versões.

1. Clique na versão e o pipeline será exibido. Clique em qualquer ambiente para verificar as informações de **Resumo, Confirmações** da versão e os **, Itens de Trabalho** associados.

1. Selecione **Confirmações**. Essa exibição mostra as confirmações de código associadas a essa implantação. Compare as versões para exibir as diferenças de confirmação entre implantações.

1. Selecione **Exibir Logs**. Os logs contêm informações úteis sobre o processo de implantação. É possível exibi-los durante e após as implantações.

## <a name="commit-code-changes-and-execute-cicd"></a>Confirmar as alterações de código e executar CI/CD

> [!NOTE]
> O procedimento a seguir testa o pipeline de CI/CD fazendo uma alteração de texto simples.

Agora você está pronto para colaborar com uma equipe em seu aplicativo usando um processo de CI/CD que implanta automaticamente o trabalho mais recente em sua Função do Azure. Cada alteração do repositório Git inicia um build no Azure DevOps, e um pipeline de CD executa uma implantação no Azure. Siga o procedimento nesta seção ou use outra técnica para confirmar as alterações em seu repositório. Por exemplo, é possível clonar o repositório Git em sua ferramenta favorita ou IDE e depois efetuar push nas alterações desse repositório.

1. No menu do Azure DevOps, selecione **Repositórios | Arquivos** e, em seguida, vá até o repositório.

1. O repositório já contém código chamado **SampleFunctionApp** baseado na linguagem de aplicativo que você escolheu no processo de criação. Abra o arquivo **Application/SampleFunctionApp/Function1.cs**.

1. Selecione **Editar** e, em seguida, faça uma alteração no **número de linha 31**. Por exemplo, você pode atualizá-lo para **Olá! Bem-vindo ao Azure Functions usando o DevOps Starter**

1. No canto superior direito, selecione **Confirmar** e selecione **Confirmar** novamente para efetuar push da alteração.

1. Abra o arquivo **Application/SampleFunctionApp.Test/Function1TestRunner.cs**. 

1. Selecione **Editar** e, em seguida, faça uma alteração no **número de linha 21**. Por exemplo, você pode atualizá-lo para **Olá! Bem-vindo ao Azure Functions usando o Azure DevOps Starter**.

     Após alguns instantes, um build é iniciado no Azure DevOps e uma versão é executada para implantar as alterações. Monitore o status do build no painel do DevOps Starter ou no navegador com sua organização do Azure DevOps.

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua os recursos relacionados que você criou quando não precisar mais deles. Use a funcionalidade **Excluir** no painel do DevOps Starter.

## <a name="next-steps"></a>Próximas etapas

Como opção, você pode modificar esses pipelines de build e de lançamento para atender às necessidades de sua equipe. Você também pode usar esse padrão de CI/CD como um modelo para outros projetos. Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Usar o DevOps Starter para implantar um aplicativo ASP.NET Core em uma Função do Azure
> * Configurar o Azure DevOps e uma assinatura do Azure 
> * Examinar a Função do Azure
> * Examinar o pipeline de CI
> * Examinar o pipeline de CD
> * Confirmar alterações no Git e implantá-las automaticamente no Azure
> * Limpar os recursos

