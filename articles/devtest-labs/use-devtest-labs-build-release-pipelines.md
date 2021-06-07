---
title: Usar o DevTest Labs no build do Azure Pipelines e em pipelines de lançamento
description: Saiba como usar Azure DevTest Labs em pipelines de Build e versão do Azure Pipelines.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d04ed5dd7bebac0c8f24deb9145c3d2e4b77122e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88080327"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Usar o DevTest Labs no build do Azure Pipelines e em pipelines de lançamento
Este artigo fornece informações sobre como os DevTest Labs podem ser usados em pipelines de Build e versão Azure Pipelines. 

## <a name="overall-flow"></a>Fluxo geral
O fluxo básico é ter um **pipeline de compilação** que faça as seguintes tarefas:

1. Crie o código do aplicativo.
1. Crie o ambiente base no DevTest Labs.
1. Atualize o ambiente com informações personalizadas.
1. Implantar o aplicativo no ambiente do DevTest Labs
1. Teste o código. 

Depois que a compilação for concluída com êxito, o **pipeline de lançamento** usará os artefatos de compilação para implantar o preparo ou a produção. 

Uma das instalações necessárias é que todas as informações necessárias para recriar o ecossistema testado estão disponíveis nos artefatos de compilação, incluindo a configuração dos recursos do Azure. Como os recursos do Azure incorrem em um custo quando usados, as empresas desejam controlar ou controlar o uso desses recursos. Em algumas situações, Azure Resource Manager modelos usados para criar e configurar os recursos podem ser gerenciados por outro departamento como ele. E esses modelos podem ser armazenados em um repositório diferente. Ele leva a uma situação interessante em que uma compilação será criada e testada, e o código e a configuração precisarão ser armazenados nos artefatos de compilação para recriar corretamente o sistema em produção. 

Usando o DevTest Labs durante a fase de compilação/teste, você pode adicionar Azure Resource Manager modelos e arquivos de suporte às fontes de compilação, de modo que durante a fase de lançamento, a configuração exata usada para testar é implantada na produção. A tarefa **criar ambiente de Azure DevTest Labs** com a configuração apropriada salvará os modelos do Resource Manager nos artefatos de compilação. Para este exemplo, você usará o código do [tutorial: criar um aplicativo Web do .NET Core e do banco de dados SQL no serviço Azure app](../app-service/tutorial-dotnetcore-sqldb-app.md)para implantar e testar o aplicativo Web no Azure.

![Fluxo geral](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Configurar recursos do Azure
Há alguns itens que precisam ser criados antecipadamente:

- Dois repositórios. A primeira com o código do tutorial e um modelo do Resource Manager com duas VMs adicionais. O segundo conterá o modelo de Azure Resource Manager base (configuração existente).
- Um grupo de recursos para implantação do código de produção e da configuração.
- Um laboratório precisa ser configurado com uma conexão com [o repositório de configuração](devtest-lab-create-environment-from-arm.md) para o pipeline de compilação. O modelo do Resource Manager precisa ser verificado no repositório de configuração como azuredeploy.jsno com o metadata.jsem para permitir que o DevTest Labs reconheça e implante o modelo.

O pipeline de compilação criará um ambiente do DevTest Labs e implantará o código para teste.

## <a name="set-up-a-build-pipeline"></a>Configurar um pipeline de compilação
Em Azure Pipelines, crie um pipeline de compilação usando o código do [tutorial: criar um aplicativo Web .NET Core e do banco de dados SQL no serviço Azure app](../app-service/tutorial-dotnetcore-sqldb-app.md). Use o modelo **ASP.NET Core** , que preencherá a tarefa necessária para compilar, testar e publicar o código.

![Selecione o modelo ASP.NET](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

Você precisa adicionar três tarefas adicionais para criar o ambiente no DevTest Labs e implantar no ambiente.

![Pipeline com três tarefas](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Tarefa criar ambiente
Na tarefa criar ambiente (**Azure DevTest Labs tarefa criar ambiente** ), use as listas suspensas para selecionar os seguintes valores:

- Assinatura do Azure
- nome do laboratório
- nome do repositório
- nome do modelo (que mostra a pasta em que o ambiente está armazenado). 

Recomendamos que você use as listas suspensas na página em vez de inserir as informações manualmente. Se você inserir manualmente as informações, insira as IDs de recursos do Azure totalmente qualificadas. A tarefa exibe os nomes amigáveis em vez de IDs de recurso. 

O nome do ambiente é o nome exibido mostrado no DevTest Labs. Deve ser um nome exclusivo para cada compilação. Por exemplo: **TestEnv $ (Build. BuildId)**. 

Você pode especificar o arquivo de parâmetros ou parâmetros para passar informações para o modelo do Resource Manager. 

Selecione **criar variáveis de saída com base na opção de saída do modelo de ambiente** e insira um nome de referência. Para este exemplo, insira **BaseEnv** para o nome de referência. Você usará esse BaseEnv ao configurar a próxima tarefa. 

![Criar tarefa de ambiente de Azure DevTest Labs](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Preencher tarefa de ambiente
A segunda tarefa (**Azure DevTest Labs popular** tarefa de ambiente) é atualizar o ambiente existente do DevTest Labs. A tarefa criar ambiente gera **BaseEnv. environmentResourceId** que é usado para configurar o nome do ambiente para essa tarefa. O modelo do Resource Manager para este exemplo tem dois parâmetros- **adminUserName** e **adminPassword**. 

![Tarefa popular ambiente de Azure DevTest Labs](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Tarefa de implantação do serviço de aplicativo
A terceira tarefa é a tarefa de **implantação do serviço Azure app** . O tipo de aplicativo é definido como **aplicativo Web** e o nome do serviço de aplicativo é definido como **$ (site)**.

![Tarefa de implantação do serviço de aplicativo](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Configurar o pipeline de liberação
Você cria um pipeline de liberação com duas tarefas: **implantação do Azure: criar ou atualizar o grupo de recursos** e **implantar Azure app serviço**. 

Para a primeira tarefa, especifique o nome e o local do grupo de recursos. O local do modelo é um artefato vinculado. Se o modelo do Resource Manager incluir modelos vinculados, uma implantação de grupo de recursos personalizada precisará ser implementada. O modelo está no artefato de soltar publicado. Substitua os parâmetros do modelo do modelo do Resource Manager. Você pode deixar as configurações restantes com valores padrão. 

Para a segunda tarefa **implantar Azure app serviço**, especifique a assinatura do Azure, selecione **aplicativo Web** para o **tipo de aplicativo** e **$ (site)** para o **nome do serviço de aplicativo**. Você pode deixar as configurações restantes com valores padrão. 

## <a name="test-run"></a>Execução de teste
Agora que ambos os pipelines estão configurados, enfileirar manualmente uma compilação e vê-la funcionar. A próxima etapa é definir o gatilho apropriado para a compilação e conectar a compilação ao pipeline de lançamento.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Integre o Azure DevTest Labs em seu Azure Pipelines pipeline de integração e entrega contínua](devtest-lab-integrate-ci-cd.md)
- [Integre ambientes a seus Azure Pipelines pipelines de CI/CD](integrate-environments-devops-pipeline.md)
