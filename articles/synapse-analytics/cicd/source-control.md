---
title: Controle do código-fonte no Synapse Studio
description: Saiba como configurar o controle do código-fonte no Azure Synapse Studio
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 3564609d869bef090f0a3db5e6040ba0f5ad80b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98796966"
---
# <a name="source-control-in-azure-synapse-studio"></a>Controle do código-fonte no Azure Synapse Studio

Por padrão, os autores do Azure Synapse Studio diretamente no serviço Synapse. Se você tiver uma necessidade de colaboração usando o Git para controle do código-fonte, o Synapse Studio permitirá que você associe seu espaço de trabalho a um repositório git, Azure DevOps ou GitHub. 

Este artigo descreverá como configurar e trabalhar em um espaço de trabalho do Synapse com o repositório git habilitado. Além disso, destacaremos algumas práticas recomendadas e um guia de solução de problemas.

> [!NOTE]
> A integração do git do Azure Synapse Studio não está disponível na nuvem do Azure governamental.

## <a name="configure-git-repository-in-synapse-studio"></a>Configurar o repositório git no Synapse Studio 

Depois de iniciar o Synapse Studio, você pode configurar um repositório git em seu espaço de trabalho. Um espaço de trabalho do Synapse Studio pode ser associado a apenas um repositório git por vez. 

### <a name="configuration-method-1-global-bar"></a>Método de configuração 1: barra global

Na barra global do Synapse Studio, selecione o menu suspenso **Synapse Live** e, em seguida, selecione **configurar repositório de código**.

![Definir as configurações do repositório de código da criação](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>Método de configuração 2: gerenciar o Hub

Acesse o Hub gerenciar do Synapse Studio. Selecione **configuração do git** na seção **controle do código-fonte** . Se você não tiver um repositório conectado, clique em **Configurar**.

![Definir as configurações do repositório de código do hub de gerenciamento](media/configure-repo-2.png)

> [!NOTE]
> Os usuários concedidos como colaborador de espaço de trabalho, proprietário ou funções de nível superior podem configurar, editar a configuração e desconectar o repositório git no Azure Synapse Studio 

Você pode conectar o Azure DevOps ou o repositório Git do GitHub em seu espaço de trabalho.

## <a name="connect-with-azure-devops-git"></a>Conectar-se ao git DevOps do Azure 

Você pode associar um espaço de trabalho do Synapse a um repositório DevOps do Azure para controle do código-fonte, colaboração, versionamento e assim por diante. Se você não tiver um repositório DevOps do Azure, siga [estas instruções](/azure/devops/organizations/accounts/create-organization-msa-or-work-student) para criar os recursos do repositório.

### <a name="azure-devops-git-repository-settings"></a>Configurações do repositório Git do Azure DevOps

Ao conectar-se ao repositório git, primeiro selecione o tipo de repositório como Azure DevOps git e, em seguida, selecione um locatário do Azure AD na lista suspensa e clique em **continuar**.

![Configurar as definições do repositório de código](media/connect-with-azuredevops-repo-selected.png)

O painel de configuração mostra as seguintes configurações do git DevOps do Azure:

| Configuração | Descrição | Valor |
|:--- |:--- |:--- |
| **Tipo de repositório** | O tipo do repositório de código do Azure Repos.<br/> | Git ou GitHub do Azure DevOps |
| **Azure Active Directory** | Seu nome de locatário do Microsoft Azure AD. | `<your tenant name>` |
| **Conta do Azure DevOps** | O nome da organização do Azure Repos. Localize o nome de organização do Azure Repos em `https://{organization name}.visualstudio.com`. Você pode [entrar na sua organização do Azure Repos](https://www.visualstudio.com/team-services/git/) para acessar seu perfil do Visual Studio e ver seus repositórios e projetos. | `<your organization name>` |
| **ProjectName** | O nome do projeto do Azure Repos. Localize o nome do projeto do Azure Repos em `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **RepositoryName** | O nome do seu repositório de código do Azure Repos. Os projetos do Azure Repos contêm repositórios Git para gerenciar seu código-fonte à medida que o projeto aumenta. Você pode criar um novo repositório ou usar um existente que já esteja no projeto. | `<your Azure Repos code repository name>` |
| **Ramificação de colaboração** | Seu branch de colaboração do Azure Repos que é usado para publicação. Por padrão, é o `master`. Altere essa configuração se você desejar publicar recursos de outra ramificação. Você pode selecionar branches existentes ou criar novos | `<your collaboration branch name>` |
| **Pasta raiz** | A pasta raiz em seu branch de colaboração do Azure Repos. | `<your root folder name>` |
| **Importar recursos existentes para o repositório** | Especifica se é para importar os recursos existentes do Synapse Studio para um repositório git Azure Repos. Marque a caixa para importar os recursos do espaço de trabalho (exceto pools) para o repositório git associado no formato JSON. Essa ação exporta cada recurso individualmente. Quando essa caixa não está marcada, os recursos existentes não são importados. | Marcado (padrão) |
| **Importar recurso para este Branch** | Selecione quais Branch os recursos (script SQL, Notebook, definição de trabalho Spark, DataSet, Dataflow etc.) são importados para o. 

O também pode usar o link do repositório para apontar rapidamente para o repositório git ao qual você deseja se conectar. 

### <a name="use-a-different-azure-active-directory-tenant"></a>Usar um locatário do Azure Active Directory diferente

O repositório Git do Azure Repos pode estar em um locatário diferente do Azure Active Directory. Para especificar outro locatário do Azure AD, você precisa ter permissões de administrador para a assinatura do Azure que está usando. Para obter mais informações, consulte [alterar o administrador da assinatura](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)

> [!IMPORTANT]
> Para se conectar a outro Azure Active Directory, o usuário conectado deve fazer parte do Active Directory. 

### <a name="use-your-personal-microsoft-account"></a>Usar sua conta Microsoft pessoal

Para usar uma conta Microsoft pessoal para integração com o Git, você poderá vincular seu Repositório do Azure pessoal ao Active Directory da sua organização.

1. Adicione sua conta Microsoft pessoal ao Active Directory da sua organização como convidado. Para saber mais, veja [Adicionar usuários de colaboração B2B do Azure Active Directory no portal do Azure](../../active-directory/external-identities/add-users-administrator.md).

2. Faça logon no portal do Azure com sua conta Microsoft pessoal. Em seguida, alterne para o Active Directory da sua organização.

3. Vá para a seção Azure DevOps, onde você agora vê seu repositório pessoal. Selecione o repositório e conecte-se com o Active Directory.

Após essas etapas de configuração, seu repositório pessoal estará disponível quando você configurar a integração do git no Synapse Studio.

Para obter mais informações sobre como conectar Azure Repos à Active Directory da sua organização, consulte [conectar sua organização ao Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="connect-with-github"></a>Conectar-se com o GitHub 

 Você pode associar um espaço de trabalho a um repositório GitHub para controle do código-fonte, colaboração, versionamento. Se não tiver uma conta ou repositório do GitHub, siga [estas instruções](https://github.com/join) para criar seus recursos.

A integração do GitHub com o Synapse Studio dá suporte ao GitHub público (ou seja, [https://github.com](https://github.com) ) e ao GitHub Enterprise. Você pode usar repositórios GitHub públicos e privados, contanto que tenha permissão de leitura e gravação para o repositório no GitHub.

### <a name="github-settings"></a>Configurações do GitHub

Ao se conectar ao repositório git, primeiro selecione o tipo de repositório como GitHub e, em seguida, forneça a sua conta do GitHub ou a URL do GitHub Enterprise Server se você usar o GitHub Enterprise Server e clicar em **continuar**.

![Configurações do repositório do GitHub](media/connect-with-github-repo-1.png)

O painel de configuração mostra as seguintes configurações do repositório do GitHub:

| **Configuração** | **Descrição**  | **Valor**  |
|:--- |:--- |:--- |
| **Tipo de repositório** | O tipo do repositório de código do Azure Repos. | GitHub |
| **Usar GitHub Enterprise** | Caixa de seleção para selecionar o GitHub Enterprise | não selecionado (padrão) |
| **URL do GitHub Enterprise** | A URL raiz do GitHub Enterprise (precisa ser HTTPS para o servidor do GitHub Enterprise local). Por exemplo: `https://github.mydomain.com`. Obrigatório somente se a opção **Usar o GitHub Enterprise** for selecionada | `<your GitHub enterprise url>` |                                                           
| **Conta do GitHub** | Seu nome de conta do GitHub. Esse nome pode ser encontrado em https:\//github.com/{nome da conta}/{nome do repositório}. Navegar até essa página solicita que você insira as credenciais do GitHub OAuth para sua conta do GitHub. | `<your GitHub account name>` |
| **Nome do repositório**  | O nome do repositório de código do GitHub. As contas do GitHub contêm repositórios Git para gerenciar seu código-fonte. Você pode criar um novo repositório ou usar um existente que já esteja na conta. | `<your repository name>` |
| **Ramificação de colaboração** | Sua ramificação de colaboração do GitHub usada para publicação. Por padrão, seu mestre. Altere essa configuração se você desejar publicar recursos de outra ramificação. | `<your collaboration branch>` |
| **Pasta raiz** | Sua pasta raiz em sua ramificação de colaboração GitHub. |`<your root folder name>` |
| **Importar recursos existentes para o repositório** | Especifica se é para importar os recursos existentes do Synapse Studio para um repositório git. Marque a caixa para importar os recursos do espaço de trabalho (exceto pools) para o repositório git associado no formato JSON. Essa ação exporta cada recurso individualmente. Quando essa caixa não está marcada, os recursos existentes não são importados. | Selecionada (padrão) |
| **Importar recurso para este Branch** | Selecione qual ramificação os recursos (script SQL, Notebook, definição de trabalho do Spark, DataSet, Dataflow etc.) são importados. 

### <a name="github-organizations"></a>Organizações do GitHub

Conectar-se a uma organização do GitHub requer que a organização Conceda permissão ao Synapse Studio. Um usuário com permissões de administrador na organização deve executar as etapas a seguir.

#### <a name="connecting-to-github-for-the-first-time"></a>Conectar-se ao GitHub pela primeira vez

Se você estiver se conectando ao GitHub do Synapse Studio pela primeira vez, siga estas etapas para se conectar a uma organização do GitHub.

1. No painel configuração do git, insira o nome da organização no campo *conta do GitHub* . Será exibido um prompt para fazer logon no GitHub. 

1. Faça logon usando suas credenciais de usuário.

1. Você será solicitado a autorizar o Synapse como um aplicativo chamado *Azure Synapse*. Nessa tela, você verá uma opção para conceder permissão para o Synapse acessar a organização. Se você não vir a opção de conceder permissão, peça a um administrador para conceder manualmente a permissão por meio do GitHub.

Depois de seguir essas etapas, seu espaço de trabalho poderá se conectar a repositórios públicos e privados em sua organização. Se você não conseguir se conectar, tente limpar o cache do navegador e tentar novamente.

#### <a name="already-connected-to-github-using-a-personal-account"></a>Já conectado ao GitHub usando uma conta pessoal

Se você já se conectou ao GitHub e concedeu apenas permissão para acessar uma conta pessoal, siga as etapas abaixo para conceder permissões a uma organização.

1. Vá para GitHub e abra **configurações**.

    ![Abrir configurações do GitHub](media/github-settings.png)

1. Selecione **Aplicativos**. Na guia **aplicativos OAuth autorizados** , você deve ver o *Azure Synapse*.

    ![Autorizar aplicativos OAuth](media/authorize-app.png)

1. Selecione o *Synapse do Azure* e conceda o acesso à sua organização.

    ![Conceder permissão de organização](media/grant-organization-permission.png)

Depois de concluir essas etapas, seu espaço de trabalho poderá se conectar a repositórios públicos e privados em sua organização.

## <a name="version-control"></a>Controle de versão

Os sistemas de controle de versão (também conhecidos como _controle do código-fonte_) permitem que os desenvolvedores colaborem no código e controlem as alterações. O controle do código-fonte é uma ferramenta essencial para projetos de vários desenvolvedores.

### <a name="creating-feature-branches"></a>Criando branches de recurso

Cada repositório git associado a um Synapse Studio tem uma ramificação de colaboração. ( `main` ou `master` é a ramificação de colaboração padrão). Os usuários também podem criar branches de recurso clicando em **+ Novo Branch** na lista suspensa do branch. Depois que o novo painel do branch for exibido, insira o nome do branch de recurso.

![Criar uma nova ramificação](media/create-new-branch.png)

Quando você estiver pronto para mesclar as alterações do branch de recurso com o branch de colaboração, clique na lista suspensa do branch e selecione **Criar solicitação de pull**. Essa ação leva você ao provedor git, no qual você pode gerar solicitações de pull, fazer revisões de código e mesclar alterações em sua ramificação de colaboração. Você só tem permissão para publicar no serviço Synapse de sua ramificação de colaboração. 

![Criar uma nova solicitação pull](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Definir configurações de publicação

Por padrão, o Synapse Studio gera os modelos de espaço de trabalho e os salva em uma ramificação chamada `workspace_publish` . Para configurar um branch de publicação personalizada, adicione um arquivo `publish_config.json` à pasta raiz no branch de colaboração. Ao publicar, o Synapse Studio lê esse arquivo, procura o campo `publishBranch` e salva os arquivos de modelo de espaço de trabalho no local especificado. Se a ramificação não existir, o Synapse Studio a criará automaticamente. Veja abaixo um exemplo de como é o arquivo:

```json
{
    "publishBranch": "workspace_publish"
}
```

O Azure Synapse Studio pode ter apenas uma ramificação de publicação por vez. Quando você especifica uma nova ramificação de publicação, a ramificação de publicação anterior não seria excluída. Se você quiser remover o branch de publicação anterior, exclua-o manualmente.


### <a name="publish-code-changes"></a>Publicar alterações de código

Depois de mesclar as alterações para a ramificação de colaboração, clique em **publicar** para publicar manualmente as alterações de código no Branch de colaboração para o serviço Synapse.

![Publicar alterações](media/gitmode-publish.png)

Um painel lateral será aberto para você confirmar que o branch de publicação e as alterações pendentes estão corretas. Depois de verificar as alterações, clique em **OK** para confirmar a publicação.

![Confirmar o branch de publicação correto](media/publish-change.png)

> [!IMPORTANT]
> A ramificação de colaboração não representa o que está implantado no serviço. As alterações na ramificação de colaboração *devem* ser publicadas manualmente serviço.

## <a name="switch-to-a-different-git-repository"></a>Alternar para um repositório Git diferente

Para alternar para um repositório git diferente, vá para a página de configuração do git no Hub de gerenciamento sob **controle do código-fonte**. Selecione **Desconectar**. 

![Ícone do Git](media/remove-repository.png)

Insira o nome do espaço de trabalho e clique em **Desconectar** para remover o repositório git associado ao seu espaço de trabalho.

Depois de remover a associação com o repositório atual, você pode definir as configurações do git para usar um repositório diferente e, em seguida, importar os recursos existentes para o novo repositório.

> [!IMPORTANT]
> A remoção da configuração do git de um espaço de trabalho não exclui nada do repositório. O espaço de trabalho Synapse conterá todos os recursos publicados. Você pode continuar a editar o espaço de trabalho diretamente no serviço.

## <a name="best-practices-for-git-integration"></a>Melhores práticas para a integração do Git

-   **Permissões**. Depois de ter um repositório git conectado ao seu espaço de trabalho, qualquer pessoa que possa acessar o repositório Git com qualquer função no espaço de trabalho poderá atualizar artefatos, como script SQL, Notebook, definição de trabalho do Spark, conjunto de fluxo de trabalhos e pipeline no modo git. Normalmente, você não quer que todos os membros da equipe tenham permissões para atualizar o espaço de trabalho. Conceda permissão somente ao repositório Git para autores de artefato do espaço de trabalho Synapse. 
-   **Colaboração**. É recomendável não permitir check-ins diretos no branch de colaboração. Essa restrição pode ajudar a evitar bugs, uma vez que cada check-in passará por um processo de revisão de solicitação de pull descrito em [Criando branches de recurso](source-control.md#creating-feature-branches).
-   **Modo dinâmico de Synapse**. Após a publicação no modo git, todas as alterações serão refletidas no modo dinâmico Synapse. No modo dinâmico do Synapse, a publicação está desabilitada. E você pode exibir, executar artefatos no modo dinâmico se tiver recebido a permissão certa. 
-   **Editar artefatos no estúdio**. O Synapse Studio é o único lugar em que você pode habilitar o controle de origem do espaço de trabalho e sincronizar as alterações para o Git automaticamente. Qualquer alteração via SDK, PowerShell, não será sincronizada com o git. Recomendamos que você sempre edite o artefato no estúdio quando o Git estiver habilitado.

## <a name="troubleshooting-git-integration"></a>Solução de problemas de integração do git

### <a name="access-to-git-mode"></a>Acesso ao modo git 

Se você tiver recebido a permissão para o repositório Git do GitHub vinculado ao seu espaço de trabalho, mas não puder acessar o modo git: 

1. Limpe o cache e atualize a página. 

1. Faça logon em sua conta do GitHub.

### <a name="stale-publish-branch"></a>Branch de publicação obsoleto

Se a ramificação de publicação estiver fora de sincronia com a ramificação de colaboração e contiver recursos desatualizados, independentemente de uma publicação recente, tente seguir estas etapas:

1. Remover seu repositório Git atual

1. Reconfigure o Git com as mesmas configurações, mas verifique se a opção **importar recursos existentes para o repositório** está marcada e escolha a mesma ramificação.  

1. Criar uma solicitação de pull para mesclar as alterações com o branch de colaboração 

## <a name="unsupported-features"></a>Recursos sem suporte

- O Synapse Studio não permite a seleção de Cherry de confirmações ou publicação seletiva de recursos. 
- O Synapse Studio não dá suporte à personalização de mensagem de confirmação.
- Por design, a ação de exclusão no estúdio será confirmada no git diretamente

## <a name="next-steps"></a>Próximas etapas

* Para implementar a integração e a implantação contínuas, consulte [integração e entrega contínuas (CI/CD)](continuous-integration-deployment.md).