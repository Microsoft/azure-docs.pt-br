---
title: "Roaming e colaboração no Azure Machine Learning Workbench | Microsoft Docs"
description: "Lista de problemas conhecidos e um guia para ajudar a solucioná-los"
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/05/2017
ms.openlocfilehash: 156dd1b7f928df22b3feb9e7a13396d3b53a91d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Roaming e colaboração no Azure Machine Learning Workbench
Este documento mostra como o Azure Machine Learning Workbench pode ajudar a usar perfis móveis em projetos nos computadores, bem como habilitar a colaboração com sua equipe. 

Quando você cria um projeto do Azure Machine Learning com um link do repositório Git (repositório) remoto, os metadados do projeto e os instantâneos são armazenados na nuvem. O link de nuvem permite que você acesse o projeto de um computador diferente (roaming). Você também pode conceder acesso aos seus colegas de trabalho, permitindo a colaboração. 

## <a name="prerequisites"></a>Pré-requisitos
Primeiramente, instale o Azure Machine Learning Workbench com acesso a uma conta de Experimentação. Siga o [guia de instalação](quickstart-installation.md) para obter mais detalhes.

Em seguida, acesse o [Visual Studio Team System](https://www.visualstudio.com) e crie um repositório para vincular ao projeto. Para obter informações detalhadas sobre o Git, consulte o artigo [Uso do repositório Git com um projeto do Azure Machine Learning Workbench](using-git-ml-project.md).

## <a name="create-a-new-azure-machine-learning-project"></a>Crie um novo projeto do Azure Machine Learning
Inicie o Azure Machine Learning Workbench e crie um novo projeto (por exemplo, _iris_). Preencha a caixa de texto **URL do repositório GIT Visualstudio.com** com uma URL de repositório Git do VSTS válida. 
>[!IMPORTANT]
>A criação do projeto falha se você não tem acesso de leitura/gravação no repositório Git e o repositório Git não está vazio, por exemplo, ele já tem uma branch mestre.

Quando o projeto for criado, envie algumas execuções em qualquer script dentro do projeto. Essa ação confirma o estado do projeto na branch de histórico de execução do repositório Git remoto. 

Se você tem que configurar a autenticação de Git, é possível operar explicitamente na branch mestre ou criar uma nova branch. 

Por exemplo: 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>Roaming
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>Abra o Azure Machine Learning Workbench no segundo computador
Depois que o repositório Git do VSTS é vinculado ao seu projeto, você pode acessar o projeto _iris_ de qualquer computador no qual você instalou o Azure Machine Learning Workbench. 

Para acessar o projeto iris em outro computador, você precisa fazer logon no aplicativo com as mesmas credenciais usadas durante a criação do projeto. Além disso, você precisará navegar até a mesma conta de Experimentação e espaço de trabalho. O projeto _iris_ será listado em ordem alfabética com outros projetos dentro do espaço de trabalho. 

### <a name="download-project-on-second-machine"></a>Baixe o projeto no segundo computador
Quando o espaço de trabalho é aberto no segundo computador, o ícone adjacente ao projeto _iris_ é diferente do ícone da pasta comum. O ícone de download indica que o conteúdo do projeto está na nuvem e precisa ser baixado no computador atual. 

![criar projeto](./media/roaming-and-collaboration/downloadable-project.png)

Clicar no projeto _iris_ inicia uma ação de download. Após um tempo, quando o download for concluído, o projeto estará pronto para ser acessado no segundo computador. 

No Windows, é `C:\Users\<username>\Documents\AzureML`

Em macOS, ele fica em:`/home/<username>/Documents/AzureML`

Em uma versão futura, planejamos aprimorar a funcionalidade para permitir que você selecione uma pasta de destino. 

>Observe que se você tiver uma pasta no diretório do Azure ML com o mesmo nome do projeto, o download falhará. No momento, você precisa renomear a pasta existente para contornar esse problema.


### <a name="work-on-the-downloaded-project"></a>Trabalhar no projeto baixado 
O projeto baixado recentemente reflete o estado do projeto a partir da última execução do projeto. Um instantâneo do estado do projeto é confirmado automaticamente para a branch do histórico de execuções no repositório Git do VSTS toda vez que você envia uma execução. Usamos o instantâneo associado à execução mais recente ao instanciar o projeto no segundo computador. 
 

## <a name="collaboration"></a>Colaboração
Você pode colaborar com sua equipe em projetos vinculados a um repositório Git do VSTS. Você pode atribuir permissões a usuários na conta de Experimentação, espaço de trabalho e projeto. Neste momento, você pode executar os comandos do Azure Resource Manager usando a CLI do Azure. Você também pode usar o [portal do Azure](https://portal.azure.com). Veja a [seção a seguir](#portal).    

### <a name="using-command-line-to-add-users"></a>Uso da linha de comando para adicionar usuários
Vamos usar um exemplo. Digamos que Alice é a proprietária do projeto e_Iris_ e deseja compartilhar o acesso com Bob. 

Alice clica no menu **Arquivo** e seleciona o item de menu **Prompt de comando** para iniciar o prompt de comando configurado para o projeto _iris_. Então, Alice decide qual nível de acesso deseja conceder a Bob executando os comandos a seguir.  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Reader.
# Bob now has read access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Reader --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as a Contributor.
# Bob now has read/write access to all projects under the Workspace by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <workspace ARM ID>

# find ARM ID of the project 
az ml project show --query "id"

# Add Bob to the Project as an Owner.
# Bob now has read/write access to the Project, and can add others too.
az role assignment create --assignee bob@contoso.com --role Owner --scope <project ARM ID>
```

Após a atribuição de função, diretamente ou por herança, Bob pode ver o projeto na lista de projetos do Workbench. O aplicativo pode precisar de uma reinicialização para ver o projeto. Bob pode baixar o projeto conforme descrito na [seção Roaming](#roaming) e colaborar com Alice. 

O histórico de execuções de todos os usuários que colaboram em um projeto é confirmado para o mesmo repositório Git remoto. Assim, quando Alice executa uma execução, Bob pode ver a execução na seção de histórico de execuções do projeto no aplicativo do Workbench. Bob também pode restaurar o projeto para o estado de qualquer execução incluindo execuções iniciadas por Alice. 

Compartilhar um repositório Git remoto para o projeto permite que Alice e Bob também colaborem na branch mestre. Se necessário, eles também podem criar branches pessoais e usar solicitações pull do Git e mesclagens para colaborar. 

### <a name="using-azure-portal-to-add-users"></a>Uso do portal do Azure para adicionar usuários
<a name="portal"></a>

As contas de Experimentação do Azure Machine Learning, espaços de trabalho e projetos são recursos do Azure Resource Manager. Você pode usar o link de controle de acesso no [Portal do Azure](https://portal.azure.com) para atribuir funções. 

Localize o recurso que você está procurando para adicionar usuários da exibição Todos os recursos. Clique no link de Controle de Acesso (IAM) na página. Adicionar usuários 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

