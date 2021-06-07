---
title: Controle do código-fonte
description: Saiba como configurar o controle do código-fonte no Azure Data Factory
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/26/2021
ms.openlocfilehash: e5cb9090025fe541ad3f70cdac876aca0552f154
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553148"
---
# <a name="source-control-in-azure-data-factory"></a>Controle do código-fonte no Azure Data Factory
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Por padrão, a UX (experiência de interface do usuário) do Azure Data Factory faz a autenticação diretamente no serviço de Data Factory. Essa experiência tem as seguintes limitações:

- O serviço de Data Factory não inclui um repositório para armazenar as entidades JSON para as alterações. A única maneira de salvar as alterações é por meio do botão **Publicar Tudo**, e todas as alterações são publicadas diretamente no serviço de Data Factory.
- O serviço de Data Factory não está otimizado para colaboração e controle de versão.
- O modelo do Azure Resource Manager exigido para implantar o Data Factory não está incluído.

Para fornecer uma experiência de criação melhor, o Azure Data Factory permite que você configure um repositório Git com o Azure Repos ou o GitHub. O Git é um sistema de controle de versão que facilita o controle de alterações e a colaboração. Este artigo descreverá como configurar e trabalhar em um repositório git, além de realçar as melhores práticas e apresentar um guia de solução de problemas.

> [!NOTE]
> Para a nuvem do Azure Government, somente o *GitHub Enterprise Server* está disponível.

Para saber mais sobre como o Azure Data Factory se integra ao Git, veja o vídeo do tutorial de 15 minutos abaixo:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4GNKv]

## <a name="advantages-of-git-integration"></a>Vantagens da integração do Git

Abaixo está uma lista de algumas das vantagens que a integração com o Git fornece à experiência de criação:

-   **Controle do código-fonte:** Conforme as cargas de trabalho do data factory se tornam cruciais, você desejaria integrar seu factory ao Git para aproveitar os vários benefícios de controle do código-fonte com o seguinte:
    -   Capacidade de controlar/auditar as alterações.
    -   Capacidade de reverter as alterações que introduziram bugs.
-   **Salvamento parcial:** Quando você cria no serviço de Data Factory, não pode salvar as alterações como um rascunho, e todas as publicações devem passar pela validação do data factory. Se seus pipelines não forem concluídos ou se você simplesmente não quiser perder as alterações se o computador falhar, a integração do git permitirá alterações incrementais de recursos do data factory independentemente do estado em que estão. A configuração de um repositório Git permite que você salve as alterações e possa publicar apenas quando tiver testado as alterações a contento.
-   **Colaboração e controle:** Se você tiver vários membros da equipe contribuindo para o mesmo alocador, talvez queira permitir que seus colegas de equipe colaborem entre si por meio de um processo de revisão de código. Você também pode configurar seu alocador de forma que nem todos os colaboradores tenham permissões iguais. Alguns membros da equipe podem fazer alterações apenas por meio do Git, e somente determinadas pessoas da equipe têm permissão para publicar as alterações no seu alocador.
-   **Melhor CI/CD:**  Se você estiver implantando em vários ambientes com um [processo de entrega contínua](continuous-integration-deployment.md), a integração com o Git facilitará certas ações. Algumas dessas ações incluem:
    -   Configurar seu pipeline de liberação para disparar automaticamente assim que houver uma alteração feita no alocador de "desenvolvimento".
    -   Personalize as propriedades no alocador que estejam disponíveis como parâmetros no modelo do Resource Manager. Isso pode ser útil manter somente o conjunto necessário de propriedades como parâmetros e ter todos os demais elementos embutidos no código.
-   **Melhor desempenho:** Um alocador médio integrado ao Git é carregado 10 vezes mais rápido do que uma criação no serviço de Data Factory. Essa melhoria de desempenho ocorre porque os recursos são baixados por meio do Git.

> [!NOTE]
> A criação direta com o serviço de Data Factory é desabilitada na UX do Azure Data Factory quando um repositório Git é configurado. As alterações feitas por meio do PowerShell ou de um SDK são publicadas diretamente no serviço de Data Factory e não são inseridas no Git.

## <a name="connect-to-a-git-repository"></a>Conectar-se a um repositório Git

Há quatro maneiras diferentes de conectar um repositório Git ao seu data factory para o Azure Repos e o GitHub. Depois de se conectar a um repositório do Git, você pode ver e gerenciar sua configuração no [hub de gerenciamento](author-management-hub.md) em **Configuração do Git** na seção **Controle do código-fonte**.

### <a name="configuration-method-1-home-page"></a>Método de configuração 1: home page

Na home page do Azure Data Factory, selecione **Configurar o repositório de código**.

![Configurar um repositório de código da home page](media/author-visually/configure-repo.png)

### <a name="configuration-method-2-authoring-canvas"></a>Método de configuração 2: tela de criação

Na tela de criação de UX do Azure Data Factory, selecione o menu suspenso **Data Factory** e clique em **Configurar o repositório de código**.

![Configurar as definições do repositório de código de criação](media/author-visually/configure-repo-2.png)

### <a name="configuration-method-3-management-hub"></a>Método de configuração 3: hub de gerenciamento

Acesse o hub de gerenciamento na UX do ADF. Selecione **Configuração do Git** na seção **Controle do código-fonte**. Se você não tiver um repositório conectado, clique em **Configurar**.

![Definir as configurações do repositório de código do hub de gerenciamento](media/author-visually/configure-repo-3.png)

### <a name="configuration-method-4-during-factory-creation"></a>Método de configuração 4: durante a criação de fábrica

Ao criar um data factory no portal do Azure, você pode configurar as informações do repositório do Git na guia **Configuração do Git**.

> [!NOTE]
> Ao configurar o Git no portal do Azure, as configurações, como nome do projeto e nome do repositório, devem ser inseridas manualmente em vez de serem parte de uma lista suspensa.

![Configurar as definições do repositório de código no portal do Azure](media/author-visually/configure-repo-4.png)

## <a name="author-with-azure-repos-git-integration"></a>Criar com a integração do Git ao Azure Repos

A criação visual com a integração do Git ao Azure Repos dá suporte ao controle do código-fonte e à colaboração para trabalhar nos pipelines de data factory. Você pode associar um data factory a um repositório da organização do Git do Azure Repos para obter controle do código-fonte, colaboração, controle de versão e assim por diante. Uma única organização do Git do Azure Repos pode ter vários repositórios, mas um repositório Git do Azure Repos pode ser associado a apenas um data factory. Se você não tiver uma organização ou um repositório do Azure Repos, siga [estas instruções](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) para criar seus recursos.

> [!NOTE]
> Você pode armazenar arquivos de script e de dados em um repositório Git do Azure Repos. No entanto, você precisa carregar os arquivos manualmente para o Armazenamento do Azure. Um pipeline do data factory não faz upload automático dos arquivos de dados ou de script armazenados em um repositório Git do Azure Repos para o Armazenamento do Azure.

### <a name="azure-repos-settings"></a>Configurações do Azure Repos

![Configurar as definições do repositório de código](media/author-visually/repo-settings.png)

O painel de configuração mostra as seguintes configurações do repositório de código do Azure Repos:

| Configuração | Descrição | Valor |
|:--- |:--- |:--- |
| **Tipo de repositório** | O tipo do repositório de código do Azure Repos.<br/> | Git ou GitHub do Azure DevOps |
| **Azure Active Directory** | Seu nome de locatário do Microsoft Azure AD. | `<your tenant name>` |
| **Organização do Azure Repos** | O nome da organização do Azure Repos. Localize o nome de organização do Azure Repos em `https://{organization name}.visualstudio.com`. Você pode [entrar na sua organização do Azure Repos](https://www.visualstudio.com/team-services/git/) para acessar seu perfil do Visual Studio e ver seus repositórios e projetos. | `<your organization name>` |
| **ProjectName** | O nome do projeto do Azure Repos. Localize o nome do projeto do Azure Repos em `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **RepositoryName** | O nome do seu repositório de código do Azure Repos. Os projetos do Azure Repos contêm repositórios Git para gerenciar seu código-fonte à medida que o projeto aumenta. Você pode criar um novo repositório ou usar um existente que já esteja no projeto. | `<your Azure Repos code repository name>` |
| **Ramificação de colaboração** | Seu branch de colaboração do Azure Repos que é usado para publicação. Por padrão, ele é `main`. Altere essa configuração se você desejar publicar recursos de outra ramificação. | `<your collaboration branch name>` |
| **Pasta raiz** | A pasta raiz em seu branch de colaboração do Azure Repos. | `<your root folder name>` |
| **Importar recursos existentes do Data Factory para o repositório** | Especifica se é necessário importar recursos existentes do data factory da **Tela de criação** da UX em um repositório Git do Azure Repos. Selecione a caixa para importar os recursos do data factory para o repositório do Git associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, os serviços vinculados e conjuntos de dados são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionada (padrão) |
| **Branch para importar o recurso** | Especifica em qual branch os recursos do data factory (pipelines, conjuntos de dados, serviços vinculados etc.) serão importados. Você pode importar recursos para um dos seguintes branches: a. Colaboração b. Criar novo c. Usar Existente |  |

> [!NOTE]
> Se você estiver usando o Microsoft Edge e não vir nenhum valor em seu menu suspenso da conta do Azure DevOps, adicione https://*.visualstudio.com à lista de sites confiáveis.

### <a name="use-a-different-azure-active-directory-tenant"></a>Usar um locatário do Azure Active Directory diferente

O repositório Git do Azure Repos pode estar em um locatário diferente do Azure Active Directory. Para especificar outro locatário do Azure AD, você precisa ter permissões de administrador para a assinatura do Azure que está usando. Para saber mais, confira [alterar a assinatura do administrador](../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator)

> [!IMPORTANT]
> Para se conectar a outro Azure Active Directory, o usuário conectado deve fazer parte do Active Directory. 

### <a name="use-your-personal-microsoft-account"></a>Usar sua conta Microsoft pessoal

Para usar uma conta Microsoft pessoal para integração com o Git, você poderá vincular seu Repositório do Azure pessoal ao Active Directory da sua organização.

1. Adicione sua conta Microsoft pessoal ao Active Directory da sua organização como convidado. Para saber mais, veja [Adicionar usuários de colaboração B2B do Azure Active Directory no portal do Azure](../active-directory/external-identities/add-users-administrator.md).

2. Faça logon no portal do Azure com sua conta Microsoft pessoal. Em seguida, alterne para o Active Directory da sua organização.

3. Vá para a seção Azure DevOps, onde você agora vê seu repositório pessoal. Selecione o repositório e conecte-se com o Active Directory.

Após essas etapas de configuração, seu repositório pessoal estará disponível quando você configurar a integração de Git na IU do Data Factory.

Para saber mais sobre como se conectar ao Azure Repos para o Active Directory da sua organização, veja [Conectar sua organização do Azure DevOps ao Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Criar com a integração do GitHub

A criação visual com a integração do GitHub oferece suporte ao controle do código-fonte e à colaboração para trabalhar em seus pipelines de data factory. Você pode associar um data factory com um repositório de conta do GitHub para controle do código-fonte, colaboração e controle de versão. Uma única conta do GitHub pode ter vários repositórios, mas um repositório do GitHub pode ser associado somente a um data factory. Se não tiver uma conta ou repositório do GitHub, siga [estas instruções](https://github.com/join) para criar seus recursos.

A integração do GitHub com o Data Factory dá suporte ao GitHub público (ou seja, [https://github.com](https://github.com)) e ao GitHub Enterprise. Você pode usar os repositórios GitHub públicos e privados com o Data Factory, desde que você tenha permissão de leitura e de escrita para o repositório no GitHub.

Para configurar um repositório GitHub, você precisará ter permissões de administrador para a assinatura do Azure que está usando.

### <a name="github-settings"></a>Configurações do GitHub

![Configurações do repositório do GitHub](media/author-visually/github-integration-image2.png)

O painel de configuração mostra as seguintes configurações do repositório do GitHub:

| **Configuração** | **Descrição**  | **Valor**  |
|:--- |:--- |:--- |
| **Tipo de repositório** | O tipo do repositório de código do Azure Repos. | GitHub |
| **Usar GitHub Enterprise** | Caixa de seleção para selecionar o GitHub Enterprise | não selecionado (padrão) |
| **URL do GitHub Enterprise** | A URL raiz do GitHub Enterprise (precisa ser HTTPS para o servidor do GitHub Enterprise local). Por exemplo: `https://github.mydomain.com`. Obrigatório somente se a opção **Usar o GitHub Enterprise** for selecionada | `<your GitHub enterprise url>` |                                                           
| **Conta do GitHub** | Seu nome de conta do GitHub. Esse nome pode ser encontrado em https:\//github.com/{nome da conta}/{nome do repositório}. Navegar até essa página solicita que você insira as credenciais do GitHub OAuth para sua conta do GitHub. | `<your GitHub account name>` |
| **Nome do repositório**  | O nome do repositório de código do GitHub. As contas do GitHub contêm repositórios Git para gerenciar seu código-fonte. Você pode criar um novo repositório ou usar um existente que já esteja na conta. | `<your repository name>` |
| **Ramificação de colaboração** | Sua ramificação de colaboração do GitHub usada para publicação. Por padrão, é a principal. Altere essa configuração se você desejar publicar recursos de outra ramificação. | `<your collaboration branch>` |
| **Pasta raiz** | Sua pasta raiz em sua ramificação de colaboração GitHub. |`<your root folder name>` |
| **Importar recursos existentes do Data Factory para o repositório** | Especifica se deve-se importar recursos do data factory existentes da UX Tela de criação em um repositório do GitHub. Selecione a caixa para importar os recursos do data factory para o repositório do Git associado no formato JSON. Esta ação exporta cada recurso individualmente (ou seja, os serviços vinculados e conjuntos de dados são exportados para JSONs separados). Quando essa caixa não está selecionada, os recursos existentes não são importados. | Selecionada (padrão) |
| **Branch para importar o recurso** | Especifica em qual branch os recursos do data factory (pipelines, conjuntos de dados, serviços vinculados etc.) serão importados. Você pode importar recursos para um dos seguintes branches: a. Colaboração b. Criar novo c. Usar Existente |  |

### <a name="github-organizations"></a>Organizações do GitHub

Conectar-se a uma organização do GitHub requer que a organização conceda permissão para o Azure Data Factory. Um usuário com permissões de ADMINISTRADOR na organização deve realizar as etapas a seguir para permitir que o data factory se conecte.

#### <a name="connecting-to-github-for-the-first-time-in-azure-data-factory"></a>Conectar-se ao GitHub pela primeira vez no Azure Data Factory

Se você estiver se conectando ao GitHub do Azure Data Factory pela primeira vez, siga estas etapas para se conectar a uma organização do GitHub.

1. No painel configuração do Git, insira o nome da organização no campo *Conta do GitHub*. Será exibido um prompt para fazer logon no GitHub. 
1. Faça logon usando suas credenciais de usuário.
1. Você precisará autorizar o Azure Data Factory como um aplicativo chamado *AzureDataFactory*. Nessa tela, você verá uma opção para conceder permissão para o ADF acessar a organização. Se essa opção não aparecer, peça a um administrador que conceda manualmente a permissão por meio do GitHub.

Depois de seguir essas etapas, sua fábrica poderá se conectar a repositórios públicos e privados em sua organização. Se você não conseguir se conectar, tente limpar o cache do navegador e tente novamente.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Já conectado ao GitHub usando uma conta pessoal

Se você já se conectou ao GitHub e concedeu permissão apenas para acessar uma conta pessoal, siga as etapas abaixo para conceder permissões a uma organização. 

1. Acesse o GitHub e abra **Configurações**.

    ![Abrir as configurações do GitHub](media/author-visually/github-settings.png)

1. Selecione **Aplicativos**. Na guia **Aplicativos OAuth autorizados**, você verá *AzureDataFactory*.

    ![Selecionar aplicativos OAuth](media/author-visually/github-organization-select-application.png)

1. Selecione o aplicativo e conceda acesso à sua organização.

    ![Permitir acesso](media/author-visually/github-organization-grant.png)

Depois de seguir essas etapas, sua fábrica poderá se conectar a repositórios públicos e privados em sua organização. 

### <a name="known-github-limitations"></a>Limitações conhecidas do GitHub

- Você pode armazenar arquivos de dados e scripts em um repositório GitHub. No entanto, você precisa carregar os arquivos manualmente para o Armazenamento do Azure. Um pipeline do Data Factory não carrega automaticamente os arquivos de dados ou scripts armazenados em um repositório GitHub para o Armazenamento do Azure.

- O GitHub Enterprise com uma versão mais antiga que 2.14.0 não funciona no navegador Microsoft Edge.

- A integração do GitHub com as ferramentas de criação visual do Data Factory funciona apenas na versão geralmente disponível do Data Factory.


- É possível buscar um máximo de 1.000 entidades por tipo de recurso (como pipelines e conjuntos de valores) em uma única ramificação do GitHub. Se esse limite for atingido, sugerimos dividir seus recursos em alocadores separados. O Git do Azure DevOps não tem essa limitação.

## <a name="version-control"></a>Controle de versão

Os sistemas de controle de versão (também conhecidos como _controle do código-fonte_) permitem aos desenvolvedores colaborar em código e acompanhar as alterações feitas no código base. O controle do código-fonte é uma ferramenta essencial para projetos de vários desenvolvedores.

### <a name="creating-feature-branches"></a>Criando branches de recurso

Cada repositório Git do Azure Repos que está associado a um data factory tem um branch de colaboração. (`main`) é o branch de colaboração padrão. Os usuários também podem criar branches de recurso clicando em **+ Novo Branch** na lista suspensa do branch. Depois que o novo painel do branch for exibido, insira o nome do branch de recurso.

![Criar uma nova ramificação](media/author-visually/new-branch.png)

Quando você estiver pronto para mesclar as alterações do branch de recurso com o branch de colaboração, clique na lista suspensa do branch e selecione **Criar solicitação de pull**. Essa ação o levará para o Git do Azure Repos, em que será possível gerar solicitações de pull, realizar revisões de código e mesclar alterações com o branch de colaboração. (`main` é o padrão). Você só tem permissão para publicar no serviço do Data Factory de sua ramificação de colaboração. 

![Criar uma nova solicitação pull](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Definir configurações de publicação

Por padrão, o data factory gera os modelos do Resource Manager do alocador publicado e os salva em um branch chamado `adf_publish`. Para configurar um branch de publicação personalizada, adicione um arquivo `publish_config.json` à pasta raiz no branch de colaboração. Na publicação, o ADF lê esse arquivo, procura o campo `publishBranch` e salva todos os modelos do Resource Manager no local especificado. Se o branch não existir, o data factory o criará automaticamente. Veja abaixo um exemplo de como é o arquivo:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

O Azure Data Factory pode ter apenas um branch de publicação por vez. Quando você especifica um novo branch de publicação, o Data Factory não exclui o branch de publicação anterior. Se você quiser remover o branch de publicação anterior, exclua-o manualmente.

> [!NOTE]
> O Data Factory apenas lê o arquivo `publish_config.json` quando ele carrega o factory. Se o factory já estiver carregado no portal, atualize o navegador para que as alterações entrem em vigor.

### <a name="publish-code-changes"></a>Publicar alterações de código

Depois de mesclar alterações para o branch de colaboração (`main` é o padrão), clique em **Publicar** para publicar manualmente as alterações de código no branch principal para o serviço do Data Factory.

![Publicar as alterações no serviço do Data Factory](media/author-visually/publish-changes.png)

Um painel lateral será aberto para você confirmar que o branch de publicação e as alterações pendentes estão corretas. Depois de verificar as alterações, clique em **OK** para confirmar a publicação.

![Confirmar o branch de publicação correto](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> O branch principal não representa o que é implantado no serviço de Data Factory. O branch principal *deve* ser publicado manualmente no serviço de Data Factory.

## <a name="best-practices-for-git-integration"></a>Melhores práticas para a integração do Git

### <a name="permissions"></a>Permissões

Normalmente, você não quer que todos os membros da equipe tenham permissões para atualizar o Data Factory. As seguintes configurações de permissões são recomendadas:

*   Todos os membros da equipe devem ter permissões de leitura para o Data Factory.
*   Somente um conjunto selecionado de pessoas deve ter permissão para publicar no Data Factory. Para fazer isso, é necessária a função de **Colaborador do Data Factory** no **Grupo de recursos** que contém o Data Factory. Para obter mais informações sobre permissões, confira [Funções e permissões para o Azure Data Factory](concepts-roles-permissions.md).

É recomendável não permitir check-ins diretos no branch de colaboração. Essa restrição pode ajudar a evitar bugs, uma vez que cada check-in passará por um processo de revisão de solicitação de pull descrito em [Criando branches de recurso](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Usando senhas do Azure Key Vault

É recomendável usar o Azure Key Vault para armazenar cadeias de conexão, senhas ou a autenticação de identidade gerenciada de Serviços Vinculados do Data Factory. Por motivos de segurança, o Data Factory não armazena segredos no Git. As alterações nos Serviços Vinculados que contenham segredos, como senhas, são publicadas imediatamente no serviço do Azure Data Factory.

Usar a autenticação do Key Vault ou MSI também facilita a integração e a implantação contínuas, pois você não precisará fornecer esses segredos durante a implantação do modelo do Resource Manager.

## <a name="troubleshooting-git-integration"></a>Solução de problemas na integração com o Git

### <a name="stale-publish-branch"></a>Branch de publicação obsoleto

Se o branch de publicação estiver fora de sincronia com o branch principal e contiver recursos desatualizados apesar de ter havido uma publicação recente, tente seguir estas etapas:

1. Remover seu repositório Git atual
1. Reconfigure o Git com as mesmas configurações, mas verifique se **Importar recursos de Data Factory existentes para o repositório** está selecionado e escolha **Novo branch**
1. Criar uma solicitação de pull para mesclar as alterações com o branch de colaboração 

Abaixo estão alguns exemplos de situações que podem tornar um branch de publicação obsoleto:
- Um usuário tem vários branches. Em um branch de recurso, eles excluíram um serviço vinculado que não está associado ao AKV (os serviços vinculados que não são do AKV são publicados imediatamente, independentemente de estarem no Git ou não) e nunca mesclaram o branch de recursos com o branch de colaboração.
- Um usuário modificou o data factory usando o SDK ou o PowerShell
- Um usuário moveu todos os recursos para um novo branch e tentou publicar pela primeira vez. Os serviços vinculados devem ser criados manualmente na importação de recursos.
- Um usuário carrega um serviço vinculado que não é o AKV ou um Integration Runtime JSON manualmente. Eles fazem referência a esse recurso de outro recurso, como um conjunto de dados, um serviço vinculado ou um pipeline. Um serviço vinculado que não é o AKV criado por meio da UX é publicado imediatamente porque as credenciais precisam ser criptografadas. Se você carregar um conjunto de dados que faz referência a esse serviço vinculado e tentar publicá-lo, a UX permitirá porque ele existe no ambiente do Git. Ele será rejeitado no momento da publicação, pois não existe no serviço de Data Factory.

## <a name="switch-to-a-different-git-repository"></a>Alternar para um repositório Git diferente

Para alternar para um repositório do Git diferente, vá para a página de configuração do git no hub de gerenciamento sob **Controle do código-fonte**. Selecione **Desconectar**. 

![Ícone do Git](media/author-visually/remove-repository.png)

Insira seu nome do Data Factory e clique em **Confirmar** para remover o repositório Git associado ao data factory.

![Remover a associação com o repositório Git atual](media/author-visually/remove-repository-2.png)

Depois de remover a associação com o repositório atual, você poderá definir as configurações do Git para usar um repositório diferente e importar recursos existentes do Data Factory para o novo repositório.

> [!IMPORTANT]
> A remoção da configuração do Git de um data factory não exclui nada do repositório. O alocador conterá todos os recursos publicados. Você pode continuar a editar o alocador diretamente no serviço.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o monitoramento e o gerenciamento de pipelines, c [Monitorar e gerenciar os pipelines programaticamente](monitor-programmatically.md).
* Para implementar a integração e a implantação contínuas, confira [CI/CD (integração e entrega contínuas) no Azure Data Factory](continuous-integration-deployment.md).
