---
title: "Usando um repositório Git com um projeto do Azure Machine Learning Workbench | Microsoft Docs"
description: "Este artigo explica como usar um repositório Git em conjunto com um projeto do Azure Machine Learning Workbench."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: hning86
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 59b07c9834904e01256b75344ba2e6892e56438c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Usando um repositório Git com um projeto do Azure Machine Learning Workbench
Este documento fornece informações sobre como o Azure Machine Learning Workbench usa o Git para garantir reprodutibilidade em sua experiência de ciência de dados. Também são fornecidas instruções sobre como associar seu projeto com um repositório Git de nuvem.

## <a name="introduction"></a>Introdução
O Azure Machine Learning Workbench é projetado com integração com o Git desde o princípio. Ao criar um novo projeto, a pasta do projeto é automaticamente "inicializada em Git" como um repositório Git local, ao mesmo tempo que um segundo repositório Git oculto local também é criado com um branch chamado _AzureMLHistory/<project_GUID>_ a fim de manter o controle das alterações de pasta de projeto para cada execução. 

A associação do projeto do Azure ML a um repositório Git hospedado em um projeto do VSTS (Visual Studio Team Service) habilita o controle de versão automático localmente e remotamente. Essa associação permite que qualquer pessoa com acesso ao repositório remoto baixe o código-fonte mais recente em outro computador (uso de perfil móvel).  

> [!NOTE]
> O VSTS tem sua própria lista de controle de acesso que é independente do serviço de Experimentação do Azure Machine Learning. O acesso de usuário poderá variar entre um repositório Git e um espaço de trabalho do Azure ML ou projeto e precisará ser gerenciado. Portanto, se você deseja compartilhar seu projeto do Azure ML com um membro da equipe, incluindo acesso de nível de código, além de apenas compartilhar o espaço de trabalho, é necessário conceder explicitamente acesso apropriado a essa pessoa ao repositório Git do VSTS. 

Com o Git, também é possível gerenciar o controle de versão explicitamente usando o branch _mestre_ ou através da criação de outros branches no repositório. Você pode usar apenas o repositório Git local e também pode enviar por push ao repositório Git remoto, se provisionado.

Este diagrama ilustra a relação entre um repositório Git do VSTS e um projeto do Azure ML:

![Git do AML](media/using-git-ml-project/aml_git.png)

Para começar a usar um repositório Git remoto, siga estas instruções básicas.

> [!NOTE]
> Atualmente, o Azure Machine Learning só dá suporte a repositórios Git em contas do VSTS. O suporte a repositórios Git em geral (como GitHub e etc.) está planejado para o futuro.

## <a name="step-1-create-an-azure-ml-experimentation-account"></a>Etapa 1. Criar uma conta de Experimentação do Azure ML
Se ainda não tiver feito, crie uma conta de Experimentação do Azure ML e instale o aplicativo do Azure ML Workbench. Veja mais detalhes no [Guia de início rápido de instalação e criação](quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>Etapa 2. Criar um projeto de equipe ou usar um projeto de equipe existente
No [Portal do Azure](https://portal.azure.com/), crie um novo **Projeto de Equipe**.
1. Clique em **+**
2. Pesquise por **"Projeto de Equipe"**
3. Insira as informações necessárias.
    - Nome: um nome de equipe.
    - Controle de versão: **Git**
    - Assinatura: a que tem uma conta de Experimentação do Azure Machine Learning.
    - Local: idealmente que esteja em uma região próxima dos seus recursos de Experimentação do Azure Machine Learning.
4. Clique em **Criar**. 

![Criar um projeto de equipe no Portal do Azure](media/using-git-ml-project/create_vsts_team.png)


> [!TIP]
> Certifique-se de entrar com sua conta do AAD (Azure Active Directory) usada para acessar o Azure Machine Learning Workbench. Caso contrário, o novo projeto de equipe poderá ficar na ID de locatário incorreta e o Azure Machine Learning não será capaz de encontrá-lo. Nesse caso, você teria que usar a interface de linha de comando e fornecer o token do VSTS.

Depois de criar o Projeto de equipe, você estará pronto para prosseguir para a próxima etapa.

Para navegar diretamente até o Projeto de equipe recém criado, a URL é `https://<team_project_name>.visualstudio.com`.

> [!NOTE]
> Atualmente, o Azure Machine Learning dá suporte apenas a repositórios Git vazios, sem branch mestre. Na interface de linha de comando, você pode usar argumento --force para excluir primeiro o branch mestre. 

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>Etapa 3. Criar um novo projeto do Azure ML com um repositório Git remoto
Inicie o Azure ML Workbench e crie um novo projeto. Preencha a caixa de texto do repositório Git com a URL do repositório Git do VSTS que você obteve na etapa 2. Ela normalmente tem esta aparência: http://<vsts_account_name>.visualstudio.com/_git/<project_name>

![Criar projeto do Azure ML com repositório Git](media/using-git-ml-project/create_project_with_git_rep.png)

Agora um novo projeto do Azure ML é criado com a integração com repositório Git remoto habilitada e pronta para usar. A pasta do projeto é sempre inicializada em Git como um repositório Git local. E o _remoto_ do Git é definido como o repositório Git do VSTS remoto, de forma que as confirmações possam ser enviados por push para o repositório Git remoto.

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>Etapa 3.a. Associar um projeto existente do Azure ML com um repositório Git do VSTS
Opcionalmente, você também pode criar um projeto do Azure ML sem um repositório Git do VSTS e contar somente com o repositório Git local para instantâneos de histórico de execuções. E você pode associar um repositório Git do VSTS mais tarde com este projeto do Azure ML existente usando o seguinte comando:

```azurecli
# make sure you are in the project path so CLI has context of your current project
az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name
```

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>Etapa 4. Capturar instantâneo do projeto no repositório Git
Agora você pode realizar algumas execuções no projeto e fazer algumas alterações entre as execuções. Você pode fazer isso através do aplicativo da área de trabalho ou da CLI, usando o comando `az ml experiment submit`. Para obter mais detalhes, você pode seguir o [Tutorial Classificando a íris](tutorial-classifying-iris-part-1.md). Para cada execução, se houver qualquer alteração feita nos arquivos na pasta do projeto, um instantâneo da pasta do projeto inteira será confirmado e enviado por push para ao repositório Git remoto. Você pode exibir os branches e as confirmações navegando até a URL do repositório Git do VSTS.

![branch do histórico de execuções](media/using-git-ml-project/run_history_branch.png)

## <a name="step-5-restore-a-previous-project-snapshot"></a>Etapa 5. Restaurar um instantâneo de projeto anterior 
Para restaurar a pasta do projeto inteira ao estado de um instantâneo de estado de projeto de histórico de execução anterior, no AML Workbench.
1. Clique em **Execuções** na barra de atividades (ícone de relógio).
2. Na exibição de **Lista de Execuções**, clique na execução você deseja restaurar.
3. Na exibição **Detalhes da Execução**, clique em **Restaurar**.

![branch do histórico de execuções](media/using-git-ml-project/restore_project.png)

Como alternativa, você pode usar o seguinte comando na janela de CLI do Azure ML Workbench.

```azurecli
# discover the run I want to restore snapshot from:
az ml history list -o table

# restore the snapshot from a particular run
az ml project restore --run-id <run_id>
```

Ao executar esse comando, substituiremos toda a pasta do projeto pelo instantâneo tirado quando a execução específica foi inicializada. Isso significa que você **perderá todas as alterações** na pasta do projeto atual. Portanto, tome muito cuidado ao executar esse comando.

## <a name="step-6-use-the-master-branch"></a>Etapa 6. Usar o branch mestre
Uma maneira de evitar a perda acidental do estado atual do seu projeto é confirmar o projeto ao branch mestre do repositório Git. Você pode usar o Git diretamente através da linha de comando (ou outra ferramenta cliente de Git de sua preferência) para operar no branch mestre. Por exemplo:

```
# make sure you are on the master branch
git checkout master

# stage all changes
git add -A

# commit all changes locally on the master branch
git commit -m 'this is my updates so far'

# push changes into the remote VSTS Git repo master branch.
git push origin master
```

Agora você pode restaurar o projeto com segurança para um instantâneo anterior, seguindo a etapa 5, sabendo que sempre poderá voltar à confirmação que acabou de fazer no branch mestre.

## <a name="authentication"></a>Autenticação
Se você confiar apenas nas funções de histórico de execuções do Azure ML para tirar instantâneos de projeto e restaurá-los, não precisará se preocupar com a autenticação do repositório Git. Ela será resolvida pela camada do serviço Experimentação.

No entanto, se usar suas próprias ferramentas de Git para gerenciar o controle de versão, você terá que lidar adequadamente com a autenticação no repositório Git remoto no VSTS. Ou seja, você precisará configurar a autenticação com o repositório Git no computador local antes de poder emitir comandos do Git nesse repositório Git remoto. 

A maneira mais fácil de fazer isso é criar um par de chaves SSH e carregar a parte da chave pública nas configurações de segurança do repositório Git.

### <a name="generate-ssh-key"></a>Gerar chave SSH 
Primeiro vamos gerar um par de chaves SSH no computador.

#### <a name="on-windows"></a>No Windows:
Inicie o aplicativo da área de trabalho de GUI do Git no Windows e, no menu _Ajuda_, clique em _Mostrar chave SSH_.

![Chave SSH](media/using-git-ml-project/git_gui.png)

Copie a SSH na área de transferência.

#### <a name="on-macos"></a>No macOS:
Etapas rápidas no shell de comando:
```
# generate the SSH key
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# start the SSH agent in the background
$ eval "$(ssh-agent -s)"

# add newly generated SSH key to the SSH agent
$ ssh-add -K ~/.ssh/id_rsa

# display the public key so you can copy it.
$ more ~/.ssh/id_rsa.pub
```
Etapas mais detalhadas podem ser encontradas [neste artigo do GitHub](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).

### <a name="upload-public-key-to-git-repo"></a>Carregar a chave pública no repositório Git
Acesse a home page da sua conta do Visual Studio: https://<vsts_account_name>.visualstudio.com, faça logon e, em seguida, clique em Segurança em seu avatar.

Em seguida, adicione uma chave pública SSH, cole a chave pública SSH que obteve da etapa anterior e dê um nome para ela. Você pode adicionar várias chaves aqui.

Agora você pode emitir comandos do Git localmente no repositório remoto sem a necessidade de nenhuma outra autenticação explícita.

### <a name="read-more"></a>Leia mais
Siga estes dois artigos (qualquer abordagem funcionará) para obter mais detalhes sobre como habilitar a autenticação local no repositório Git remoto no VSTS.
- [Usar a autenticação de chave SSH](https://www.visualstudio.com/en-us/docs/git/use-ssh-keys-to-authenticate)
- [Usar gerenciadores de credencial do Git](https://www.visualstudio.com/en-us/docs/git/set-up-credential-managers)


## <a name="next-steps"></a>Próximas etapas
Saiba como usar o Processo de ciência de dados de equipe para organizar a estrutura do seu projeto. Consulte [Estruturar um projeto com o TDSP](how-to-use-tdsp-in-azure-ml.md)
