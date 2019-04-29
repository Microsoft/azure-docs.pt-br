---
title: Criação visual no Azure Data Factory | Microsoft Docs
description: Saiba como usar a criação visual no Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: d2e51551643e154714b9d2368e63d7af9ebfa204
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60593098"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Criação visual no Azure Data Factory
A experiência (UX) da interface do usuário do Azure Data Factory permite criar e implantar visualmente recursos para seu data factory sem ter que gravar nenhum código. Você pode arrastar atividades para uma tela de pipeline, realizar execuções de teste, depurar iterativamente e implantar e monitorar as execuções do pipeline. Há duas abordagens para usar a UX para executar a criação visual:

- Criar diretamente com o serviço de Data Factory.
- Crie com a integração do Git ao Azure Repos para obter controle de versão, controle do código-fonte e colaboração.

## <a name="author-directly-with-the-data-factory-service"></a>Criar diretamente com o serviço de Data Factory
A criação visual com o serviço Data Factory difere da criação visual com a integração do Git em duas maneiras:

- O serviço de Data Factory não inclui um repositório para armazenar as entidades JSON para as alterações.
- O serviço de Data Factory não está otimizado para colaboração ou controle de versão.

![Configurar o serviço do Data Factory](media/author-visually/configure-data-factory.png)

Quando você usa a **Tela de criação** UX para criar diretamente com o serviço do Data Factory, somente o modo **Publica Tudo** estará disponível. As alterações feitas são publicadas diretamente no serviço do Data Factory.

![Modo Publicar](media/author-visually/data-factory-publish.png)

## <a name="author-with-azure-repos-git-integration"></a>Criar com a integração do Git ao Azure Repos
A criação visual com a integração do Git ao Azure Repos dá suporte ao controle do código-fonte e à colaboração para trabalhar nos pipelines de data factory. Você pode associar um data factory a um repositório da organização do Git do Azure Repos para obter controle do código-fonte, colaboração, controle de versão e assim por diante. Uma única organização do Git do Azure Repos pode ter vários repositórios, mas um repositório Git do Azure Repos pode ser associado a apenas um data factory. Se você não tiver uma organização ou um repositório do Azure Repos, siga [estas instruções](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student) para criar seus recursos.

> [!NOTE]
> Você pode armazenar arquivos de script e de dados em um repositório Git do Azure Repos. No entanto, você precisa carregar os arquivos manualmente para o Armazenamento do Azure. Um pipeline do Data Factory não faz upload automaticamente dos arquivos de dados ou de script armazenados em um repositório Git do Azure Repos para o Armazenamento do Azure.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Configurar um repositório Git do Azure Repos com o Azure Data Factory
Você pode configurar um repositório Git do Azure Repos com um data factory usando dois métodos.

#### <a name="method1"></a> Método de configuração 1 (repositório Git do Azure Repos): Página Introdução

No Azure Data Factory, vá para a página **Vamos começar**. Selecione **Configurar Repositório de Código**:

![Configurar um repositório de código do Azure Repos](media/author-visually/configure-repo.png)

O painel de configuração **Definições do repositório** é exibido:

![Configurar as definições do repositório de código](media/author-visually/repo-settings.png)

O painel mostra as seguintes configurações do repositório de código do Azure Repos:

| Configuração | DESCRIÇÃO | Value |
|:--- |:--- |:--- |
| **Tipo de repositório** | O tipo do repositório de código do Azure Repos.<br/>**Observação**: Atualmente, o GitHub não é compatível. | Git do Azure Repos |
| **Azure Active Directory** | Seu nome de locatário do Microsoft Azure AD. | `<your tenant name>` |
| **Organização do Azure Repos** | O nome da organização do Azure Repos. Localize o nome de organização do Azure Repos em `https://{organization name}.visualstudio.com`. Você pode [entrar na sua organização do Azure Repos](https://www.visualstudio.com/team-services/git/) para acessar seu perfil do Visual Studio e ver seus repositórios e projetos. | `<your organization name>` |
| **ProjectName** | O nome do projeto do Azure Repos. Localize o nome do projeto do Azure Repos em `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **RepositoryName** | O nome do seu repositório de código do Azure Repos. Os projetos do Azure Repos contêm repositórios Git para gerenciar seu código-fonte à medida que o projeto aumenta. Você pode criar um novo repositório ou usar um existente que já esteja no projeto. | `<your Azure Repos code repository name>` |
| **Ramificação de colaboração** | Seu branch de colaboração do Azure Repos que é usado para publicação. Por padrão, é `master`. Altere essa configuração se você desejar publicar recursos de outra ramificação. | `<your collaboration branch name>` |
| **Pasta raiz** | A pasta raiz em seu branch de colaboração do Azure Repos. | `<your root folder name>` |
| **Importar recursos existentes do Data Factory para o repositório** | Especifica se é necessário importar recursos existentes do data factory da **Tela de criação** da UX em um repositório Git do Azure Repos. Selecione a caixa para importar os recursos do data factory para o repositório do Git associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, os serviços vinculados e conjuntos de dados são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionada (padrão) |

#### <a name="configuration-method-2-azure-repos-git-repo-ux-authoring-canvas"></a>Método de configuração 2 (repositório Git do Azure Repos): Tela de criação de UX
Na UX **Tela de criação** do Azure Data Factory, localize seu data factory. Selecione o menu suspenso **Data Factory** e selecione **Configurar repositório de código**.

Um painel de configuração é exibido. Para obter detalhes sobre as definições de configuração, consulte as descrições no <a href="#method1">Método de configuração 1</a>.

![Configurar as definições do repositório de código para a criação de UX](media/author-visually/configure-repo-2.png)

### <a name="use-a-different-azure-active-directory-tenant"></a>Usar um locatário do Azure Active Directory diferente

Você pode criar um repositório Git do Azure Repos em um locatário diferente do Azure Active Directory. Para especificar outro locatário do Azure AD, você precisa ter permissões de administrador para a assinatura do Azure que está usando.

### <a name="use-your-personal-microsoft-account"></a>Usar sua conta Microsoft pessoal

Para usar uma conta Microsoft pessoal para integração com o Git, você poderá vincular seu Repositório do Azure pessoal ao Active Directory da sua organização.

1. Adicione sua conta Microsoft pessoal ao Active Directory da sua organização como convidado. Para saber mais, veja [Adicionar usuários de colaboração B2B do Azure Active Directory no portal do Azure](../active-directory/b2b/add-users-administrator.md).

2. Faça logon no portal do Azure com sua conta Microsoft pessoal. Em seguida, alterne para o Active Directory da sua organização.

3. Vá para a seção Azure DevOps, onde você agora vê seu repositório pessoal. Selecione o repositório e conecte-se com o Active Directory.

Após essas etapas de configuração, seu repositório pessoal estará disponível quando você configurar a integração de Git na IU do Data Factory.

Para saber mais sobre como se conectar ao Azure Repos para o Active Directory da sua organização, veja [Conectar sua organização do Azure DevOps ao Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

### <a name="switch-to-a-different-git-repo"></a>Alternar para um repositório Git diferente

Para alternar para um repositório Git diferente, localize o ícone no canto superior direito da página de visão geral da Data Factory, conforme mostrado na seguinte captura de tela. Se você não vir o ícone, limpe o cache do navegador local. Selecione o ícone para remover a associação com o repositório atual.

Depois de remover a associação com o repositório atual, você pode configurar as configurações de Git para usar um repositório diferente. Em seguida, você pode importar recursos da Data Factory existentes para o novo repositório.

![Remover a associação com o repositório Git atual](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>Usar controle de versão
Os sistemas de controle de versão (também conhecidos como _controle do código-fonte_) permitem aos desenvolvedores colaborar em código e acompanhar as alterações feitas no código base. O controle do código-fonte é uma ferramenta essencial para projetos de vários desenvolvedores.

Cada repositório Git do Azure Repos que está associado a um data factory tem um branch de colaboração. (`master` é a ramificação de colaboração padrão). Os usuários criam ramificações do recurso clicando **+ Nova ramificação** e fazem o desenvolvimento nas ramificações do recurso.

![Alterar o código sincronizando ou publicando](media/author-visually/sync-publish.png)

Quando estiver pronto com o desenvolvimento de recurso em sua ramificação de recurso, clique em **Criar solicitação pull**. Essa ação o levará para o Git do Azure Repos, em que será possível gerar solicitações de pull, realizar revisões de código e mesclar alterações com o branch de colaboração. (`master` é o padrão). Você só tem permissão para publicar no serviço do Data Factory de sua ramificação de colaboração. 

![Criar uma nova solicitação pull](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Definir configurações de publicação

Para configurar o branch de publicação, ou seja, o branch em que os modelos do Resource Manager são salvos, adicione um arquivo `publish_config.json` à pasta raiz no branch de colaboração. O Data Factory lê esse arquivo, procura o campo `publishBranch` e cria um branch (caso ele ainda não exista) com o valor fornecido. Em seguida, ele salva todos os modelos do Resource Manager no local especificado. Por exemplo: 

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Ao publicar usando o modo do Git, você pode confirmar se o Data Factory está usando o branch de publicação esperado, conforme é mostrado na seguinte captura de tela:

![Confirmar o branch de publicação correto](media/author-visually/configure-publish-branch.png)

Quando você especifica um novo branch de publicação, o Data Factory não exclui o branch de publicação anterior. Se você quiser remover o branch de publicação anterior, exclua-o manualmente.

O Data Factory apenas lê o arquivo `publish_config.json` quando ele carrega o factory. Se o factory já estiver carregado no portal, atualize o navegador para que as alterações entrem em vigor.

### <a name="publish-code-changes"></a>Publicar alterações de código
Depois de ter mesclado alterações para a ramificação de colaboração (`master` é o padrão), selecione **Publicar** para publicar manualmente as alterações de código na ramificação mestre para o serviço do Data Factory.

![Publicar as alterações no serviço do Data Factory](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> O branch mestre não é representativo do que é implantado no serviço de Data Factory. O branch mestre *deve* ser publicado manualmente no serviço de Data Factory.

### <a name="advantages-of-git-integration"></a>Vantagens da integração do Git

-   **Controle do código-fonte**. Conforme as cargas de trabalho do data factory se tornam cruciais, você desejaria integrar seu factory ao Git para aproveitar os vários benefícios de controle do código-fonte com o seguinte:
    -   Capacidade de controlar/auditar as alterações.
    -   Capacidade de reverter as alterações que introduziram bugs.
-   **Salvamento parcial**. Conforme você faz muitas alterações em seu factory, perceberá que no modo normal dinâmico, você não pode salvar suas alterações como rascunho, porque não terminou ou não deseja perder as alterações no caso de falha do seu computador. Com a integração do Git, você pode continuar salvando as alterações incrementalmente e publicar o factory somente quando você tiver terminado. O Git atua como um local de preparo para seu trabalho, até que você teste suas alterações de acordo com sua satisfação.
-   **Colaboração e controle**. Se você tiver vários membros da equipe participando do mesmo factory, talvez queira permitir que seus colegas de equipe colaborem entre si por meio de um processo de revisão de código. Você também pode configurar seu factory de tal forma que nem todo colaborador dela tenha permissão para implantar nela. Os membros da equipe apenas podem fazer alterações por meio do Git, mas somente determinadas pessoas da equipe têm permissão para "Publicar" as alterações para seu factory.
-   **Mostrando comparações**. No modo de Git, você consegue ver uma comparação interessante do conteúdo que está prestes a ser publicado no factory. Essa comparação mostra todos os recursos/entidades que foram modificados/adicionados/excluídos desde a última vez em que você publicou no seu factory. Com base nessa comparação, você pode prosseguir com a publicação ou voltar e verificar suas alterações e retornar posteriormente.
-   **Melhor CI/CD**. Se você estiver usando o modo Git, poderá configurar seu pipeline de lançamento para disparar automaticamente assim que houver qualquer alteração feita no factory de desenvolvimento. Você também poderá personalizar as propriedades no factory que estão disponíveis como parâmetros no modelo do Resource Manager. Isso pode ser útil manter somente o conjunto necessário de propriedades como parâmetros e ter todos os demais elementos embutidos no código.
-   **Melhor desempenho**. Um factory médio carrega 10 vezes mais rápido no modo de Git que no modo DINÂMICO regular, porque os recursos são baixados por meio do Git.

### <a name="best-practices-for-git-integration"></a>Melhores práticas para a integração do Git

-   **Permissões**. Normalmente você não quer que todos os membros da equipe tenham permissões para atualizar o factory.
    -   Todos os membros da equipe devem ter permissões de leitura para o data factory.
    -   Apenas um conjunto selecionado de pessoas deve ter permissão para publicar no factory e, para isso, elas precisam fazer parte da função de “Colaborador do Data Factory” no factory.
    -   Uma das boas práticas do controle do código-fonte também é não permitir check-ins diretos no branch de colaboração. Esse requisito impede bugs, uma vez que check-in passa por um processo de solicitação de pull.
-   **Alternando modos**.
    -    Quando você estiver no modo Git, não recomendamos alternar entre ele e o modo DINÂMICO, principalmente porque todas as alterações feitas no modo DINÂMICO não serão vistas ao alternar novamente para o Git. Tente fazer as alterações no modo de Git em si e, em seguida, publicá-las por meio da interface do usuário.
    -   Da mesma forma, não use qualquer cmdlet do powershell do data factory, uma vez que obtêm o mesmo efeito aplicando diretamente as alterações fornecidas ao factory ativo.
-   **Usar senhas do Azure Key Vault**.
    -   É altamente recomendável usar o AzureKeyVault para armazenar qualquer cadeia de conexão ou senha para os Serviços Vinculados do DataFactory.
    -   Não armazenamos tais informações secretas no Git (por motivos de segurança), para que nenhuma alteração nos Serviços Vinculados seja publicada imediatamente no factory ativo. Essa publicação imediata, às vezes, não é desejada, uma vez que as alterações podem não ter sido testadas, o que anula a finalidade do Git.
    -   Como resultado, todos esses segredos devem ser buscados em Serviços Vinculados que usam o Azure Key Vault como base.
    -   Alguns dos benefícios de usar o Key Vault é que ele facilita a CICD, ao não fazer você fornecer esses segredos durante a implantação do modelo do Resource Manager.

## <a name="author-with-github-integration"></a>Criar com a integração do GitHub

A criação visual com a integração do GitHub oferece suporte ao controle do código-fonte e à colaboração para trabalhar em seus pipelines de data factory. Você pode associar um data factory com um repositório de conta do GitHub para controle do código-fonte, colaboração e controle de versão. Uma única conta do GitHub pode ter vários repositórios, mas um repositório do GitHub pode ser associado somente a um data factory. Se não tiver uma conta ou repositório do GitHub, siga  [estas instruções](https://github.com/join)  para criar seus recursos.

A integração do GitHub com o Data Factory dá suporte ao GitHub público (ou seja, [https://github.com](https://github.com)) e ao GitHub Enterprise. Você pode usar os repositórios GitHub públicos e privados com o Data Factory, desde que você tenha permissão de leitura e de escrita para o repositório no GitHub.

Para configurar um repositório GitHub, você precisa ter permissões de administrador para a assinatura do Azure que está usando.

Para ver uma introdução de nove minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="limitations"></a>Limitações

- Você pode armazenar arquivos de dados e scripts em um repositório GitHub. No entanto, você precisa carregar os arquivos manualmente para o Armazenamento do Azure. Um pipeline do Data Factory não carrega automaticamente os arquivos de dados ou scripts armazenados em um repositório GitHub para o Armazenamento do Azure.

- O GitHub Enterprise com uma versão mais antiga que 2.14.0 não funciona no navegador Microsoft Edge.

- A integração do GitHub com as ferramentas de criação visual do Data Factory funciona apenas na versão geralmente disponível do Data Factory.

### <a name="configure-a-public-github-repository-with-azure-data-factory"></a>Configurar um repositório do GitHub público com o Azure Data Factory

Você pode configurar um repositório do GitHub com um data factory por meio de dois métodos.

**Método de configuração 1 (repositório público): Página Introdução**

No Azure Data Factory, vá para a página  **Vamos começar** . Selecione  **Configurar Repositório de Código**:

![Página Introdução ao Data Factory](media/author-visually/github-integration-image1.png)

O painel de configuração  **Configurações do Repositório**  é exibido:

![Configurações do repositório do GitHub](media/author-visually/github-integration-image2.png)

O painel mostra as seguintes configurações do repositório de código do Azure Repos:

| **Configuração**                                              | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Valor**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Tipo de repositório**                                      | O tipo do repositório de código do Azure Repos.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **Conta do GitHub**                                       | Seu nome de conta do GitHub. Esse nome pode ser localizado em https:\//github.com/{account nome} / {nome do repositório}. Navegar até essa página solicita que você insira as credenciais do GitHub OAuth para sua conta do GitHub.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | O nome do repositório de código do GitHub. As contas do GitHub contêm repositórios Git para gerenciar seu código-fonte. Você pode criar um novo repositório ou usar um existente que já esteja na conta.                                                                                                                                                                                                                              |                    |
| **Ramificação de colaboração**                                 | Sua ramificação de colaboração do GitHub usada para publicação. Por padrão, é o primário. Altere essa configuração se você desejar publicar recursos de outra ramificação.                                                                                                                                                                                                                                                               |                    |
| **Pasta raiz**                                          | Sua pasta raiz em sua ramificação de colaboração GitHub.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Importar recursos existentes do Data Factory para o repositório** | Especifica se é necessário importar recursos existentes do data factory da  **Tela de criação**  da Experiência do Usuário em um repositório do GitHub. Selecione a caixa para importar os recursos do data factory para o repositório do Git associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, os serviços vinculados e conjuntos de dados são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionada (padrão) |
| **Branch para importar o recurso**                       | Especifica em qual branch os recursos do data factory (pipelines, conjuntos de dados, serviços vinculados etc.) serão importados. Você pode importar recursos para um dos seguintes branches: a. Colaboração b. Criar novo c. Usar Existente                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-public-repo-ux-authoring-canvas"></a>Método de configuração 2 (repositório público): Tela de criação de UX

Na  **Tela de criação** da Experiência do Usuário do Azure Data Factory, localize seu data factory. Selecione o menu suspenso  **Data Factory**  e então selecione  **Configurar Repositório de Código**.

Um painel de configuração é exibido. Para obter detalhes sobre as definições de configuração, confira as descrições no  *Método de configuração 1* acima.

### <a name="configure-a-github-enterprise-repository-with-azure-data-factory"></a>Configurar um repositório do GitHub Enterprise com o Azure Data Factory

Você pode configurar um repositório do GitHub Enterprise com um data factory por meio de dois métodos.

#### <a name="configuration-method-1-enterprise-repo-lets-get-started-page"></a>Método de configuração 1 (repositório Enterprise): Página Introdução

No Azure Data Factory, vá para a página  **Vamos começar** . Selecione  **Configurar Repositório de Código**:

![Página Introdução ao Data Factory](media/author-visually/github-integration-image1.png)

O painel de configuração  **Configurações do Repositório**  é exibido:

![Configurações do repositório do GitHub](media/author-visually/github-integration-image3.png)

O painel mostra as seguintes configurações do repositório de código do Azure Repos:

| **Configuração**                                              | **Descrição**                                                                                                                                                                                                                                                                                                                                                                                                                   | **Valor**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **Tipo de repositório**                                      | O tipo do repositório de código do Azure Repos.                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **Usar GitHub Enterprise**                                | Caixa de seleção para selecionar o GitHub Enterprise                                                                                                                                                                                                                                                                                                                                                                                              |                    |
| **URL do GitHub Enterprise**                                | A URL raiz do GitHub Enterprise. Por exemplo: https://github.mydomain.com                                                                                                                                                                                                                                                                                                                                                          |                    |
| **Conta do GitHub**                                       | Seu nome de conta do GitHub. Esse nome pode ser localizado em https:\//github.com/{account nome} / {nome do repositório}. Navegar até essa página solicita que você insira as credenciais do GitHub OAuth para sua conta do GitHub.                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | O nome do repositório de código do GitHub. As contas do GitHub contêm repositórios Git para gerenciar seu código-fonte. Você pode criar um novo repositório ou usar um existente que já esteja na conta.                                                                                                                                                                                                                              |                    |
| **Ramificação de colaboração**                                 | Sua ramificação de colaboração do GitHub usada para publicação. Por padrão, é o primário. Altere essa configuração se você desejar publicar recursos de outra ramificação.                                                                                                                                                                                                                                                               |                    |
| **Pasta raiz**                                          | Sua pasta raiz em sua ramificação de colaboração GitHub.                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **Importar recursos existentes do Data Factory para o repositório** | Especifica se é necessário importar recursos existentes do data factory da  **Tela de criação**  da Experiência do Usuário em um repositório do GitHub. Selecione a caixa para importar os recursos do data factory para o repositório do Git associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, os serviços vinculados e conjuntos de dados são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionada (padrão) |
| **Branch para importar o recurso**                       | Especifica em qual branch os recursos do data factory (pipelines, conjuntos de dados, serviços vinculados etc.) serão importados. Você pode importar recursos para um dos seguintes branches: a. Colaboração b. Criar novo c. Usar Existente                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-enterprise-repo-ux-authoring-canvas"></a>Método de configuração 2 (repositório Enterprise): Tela de criação de UX

Na  **Tela de criação** da Experiência do Usuário do Azure Data Factory, localize seu data factory. Selecione o menu suspenso  **Data Factory**  e então selecione  **Configurar Repositório de Código**.

Um painel de configuração é exibido. Para obter detalhes sobre as definições de configuração, confira as descrições no  *Método de configuração 1* acima.

## <a name="use-the-expression-language"></a>Usar linguagem de expressão
Você pode especificar expressões para valores de propriedade usando a linguagem de expressão com suporte do Azure Data Factory.

Especifique as expressões para valores de propriedade selecionando **Adicionar Conteúdo Dinâmico**:

![Usar linguagem de expressão](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>Usar funções e parâmetros

Você pode usar funções ou especificar parâmetros para pipelines e conjunto de dados no **construtor de expressões** do Data Factory:

Para obter informações sobre as expressões compatíveis, consulte [Expressões e funções no Azure Data Factory](control-flow-expression-language-functions.md).

![Adicionar conteúdo dinâmico](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>Fornecer comentários
Selecione **Feedback** para comentar sobre os recursos ou notificar a Microsoft sobre problemas com a ferramenta:

![Comentários](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o monitoramento e o gerenciamento de pipelines, c [Monitorar e gerenciar os pipelines programaticamente](monitor-programmatically.md).
