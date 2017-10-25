---
title: "Artigo de tutorial para recursos de versão prévia do Azure Machine Learning – interface de linha de comando | Microsoft Docs"
description: "Este tutorial percorre todas as etapas necessárias para concluir uma classificação de íris de ponta a ponta usando a interface de linha de comando."
services: machine-learning
author: ahgyger
ms.author: ahgyger, ritbhat
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: c2a3b9702afd99c29b64133a05515a1b5f395130
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-classifying-iris-using-the-command-line-interface"></a>Tutorial: classificando a íris usando a interface de linha de comando
Os serviços do Azure Machine Learning (versão prévia) são uma solução integrada de análise avançada e de ciência de dados de ponta a ponta para cientistas de dados profissionais prepararem dados, desenvolverem testes e implantarem modelos em escala de nuvem.

Neste tutorial, você aprenderá a usar as ferramentas da CLI (interface de linha de comando) nos recursos de versão prévia do Azure Machine Learning para: 
> [!div class="checklist"]
> * Configurar uma conta de experimentação e criar um espaço de trabalho
> * Criar um projeto
> * Enviar um teste para vários destinos de computação
> * Promover e registrar um modelo treinado
> * Implantar um serviço Web para pontuar novos dados

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
- Você precisa ter acesso a uma assinatura do Azure e permissões para criar recursos nessa assinatura. 
- Você precisa instalar o aplicativo Azure Machine Learing Workbench seguindo o [Guia de início rápido para instalação e criação](quickstart-installation.md). 

  >[!NOTE]
  >Você só precisa instalar o Azure Machine Learning Workbench localmente. Basta seguir as etapas nas seções intituladas Instalar o Azure Machine Learning Workbench, pois as etapas para criar a conta e criar um novo projeto serão executadas pela linha de comando neste artigo.
 
## <a name="getting-started"></a>Introdução
A CLI (interface de linha de comando) do Azure Machine Learning permite que você execute todas as tarefas necessárias para um fluxo de trabalho de ciência de dados de ponta a ponta. Você pode acessar as ferramentas da CLI das seguintes maneiras:

### <a name="option-1-launch-azure-ml-cli-from-azure-ml-workbench-log-in-dialog-box"></a>Opção 1. Inicie a CLI do Azure ML por meio da caixa de diálogo de logon do Azure ML Workbench
Quando você iniciar o Azure ML Workbench e fizer logon pela primeira vez, e se você ainda não tiver acesso a uma conta de experimentação, será exibida a tela a seguir:

![nenhuma conta encontrada](media/tutorial-iris-azure-cli/no_account_found.png)

Clique no link **Janela de linha de comando** na caixa de diálogo para iniciar a janela de linha de comando.

### <a name="option-2-launch-azure-ml-cli-from-azure-ml-workbench-app"></a>Opção 2. Inicie a CLI do Azure ML por meio do aplicativo Azure ML Workbench
Se você já tiver acesso a uma conta de experimentação, será possível fazer logon com êxito. E, em seguida, você poderá abrir a janela de linha de comando clicando no menu **Arquivo** --> **Abrir prompt de comando**.

### <a name="option-3-enable-azure-ml-cli-in-an-arbitrary-command-line-window"></a>Opção 3. habilitar a CLI do Azure ML em uma janela de linha de comando qualquer
Você também pode habilitar a CLI do Azure ML em qualquer janela de linha de comando. Simplesmente inicie uma janela de comando e digite os seguintes comandos:

```sh
# Windows Command Prompt
set PATH=%LOCALAPPDATA%\amlworkbench\Python;%LOCALAPPDATA%\amlworkbench\Python\Scripts;%PATH%

# Windows PowerShell
$env:Path = $env:LOCALAPPDATA+"\amlworkbench\Python;"+$env:LOCALAPPDATA+"\amlworkbench\Python\Scripts;"+$env:Path

# macOS Bash Shell
PATH=$HOME/Library/Caches/AmlWorkbench/Python/bin:$PATH
```
Para que a alteração se torne permanente, você pode usar `SETX` no Windows. Para macOS, você pode usar `setenv`.

>[!TIP]
>Você pode habilitar a CLI do Azure na sua janela de terminal favorita, definindo as variáveis de ambiente acima.

## <a name="step-1-log-in-to-azure"></a>Etapa 1. Fazer logon no Azure
A primeira etapa é abrir a CLI por meio do aplicativo AMLWorkbench (Arquivo > Abrir prompt de comando). Isso garante que o ambiente Python certo seja usado e que os comandos da CLI do ML estejam disponíveis. 

Em seguida, precisamos definir o contexto certo em sua CLI para acessar e gerenciar recursos do Azure.
 
```bash
az login
az account set -s d128f140-94e6-1206-80a7-954b9d27d007
```

> [!TIP]
> Para obter uma lista de todas as suas assinaturas, execute: 
>```
>az account list -o table
>```

## <a name="step-2-create-a-new-azure-machine-learning-experimentation-account-and-workspace"></a>Etapa 2. Criar uma nova conta de experimentação do Azure Machine Learning e um novo espaço de trabalho
Vamos começar criando uma nova conta de experimentação e um novo espaço de trabalho. Consulte [Azure Machine Learning concepts](overview-general-concepts.md) (Conceitos do Azure Machine Learning) para obter mais detalhes sobre as contas de experimentação e os espaços de trabalho. 

> [!NOTE]
> As contas de experimentação exigem uma conta de armazenamento, que é usada para armazenar as saídas de execução de teste. O nome da conta de armazenamento deve ser exclusivo globalmente no Azure, porque há uma URL associada a ele. O nome da conta de experimentação é usado para criar, em seu nome, uma nova conta de armazenamento. Use um nome exclusivo ou você receberá um erro como _"O nome amlsampleexp da conta de armazenamento já está em uso”._ Como alternativa, você pode usar o argumento `--storage` para usar uma conta de armazenamento existente.

```bash
az group create --name amlsample --location eastus2
az ml account experimentation create --name amlsampleexp --resource-group amlsample
az ml account experimentation create --name amlsampleexp --resource-group amlsample --storage /subscriptions/6d48cffb-b787-47bd-8d20-1696afa33b67/resourceGroups/existing/providers/Microsoft.Storage/storageAccounts/mystorageacct
az ml workspace create --name amlsamplew --account amlsampleexp --resource-group amlsample
```

## <a name="step-2a-optional-share-a-workspace-with-co-worker"></a>Etapa 2.a (opcional) Compartilhar um espaço de trabalho com um colega de trabalho
Aqui, exploraremos como compartilhar o acesso a um espaço de trabalho com um colega de trabalho. As etapas para compartilhar o acesso a uma conta de experimentação ou a um projeto serão as mesmas. Apenas a forma de obter a ID de recurso do Azure precisará ser atualizada.

```bash
az ml workspace show --name amlsamplew --account amlsampleexp --resource-group amlsample 
az role assignment create --assignee ahgyger@microsoft.com --role owner --scope "/subscriptions/d128f140-94e6-4175-87a7-954b9d27db16/resourceGroups/amlsample/providers/Microsoft.MachineLearningExperimentation/accounts/amlsampleexp/workspaces/amlsamplew"
```

> [!TIP]
> Você precisa usar o endereço de email real do colega de trabalho, não um alias. 

## <a name="step-3-create-a-new-project"></a>Etapa 3. Criar um novo projeto
Nossa próxima etapa será criar um novo projeto. Há várias maneiras de começar um novo projeto.

### <a name="create-a-new-blank-project"></a>Criar um novo projeto em branco

```bash
az ml project create --name 9_25_1 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\9_25\
```

### <a name="create-a-new-project-with-template-files"></a>Criar um novo projeto com arquivos de modelo
Os arquivos de modelo não são exemplos, mas oferecem um quadro para o novo projeto. O projeto será populado previamente com dois arquivos: `train.py` e `score.py`.

```bash
az ml project create --name 9_25_1 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --template
```

### <a name="create-a-new-project-associated-with-a-cloud-git-repository"></a>Criar um novo projeto associado a um repositório Git de nuvem
É possível criar um novo projeto associado a um repositório Git de nuvem. Sempre que um teste for enviado, será criado um instantâneo do conteúdo do projeto como uma confirmação do Git em seu branch de histórico de execução. Consulte [Using Git repository with an Azure Machine Learning Workbench project](using-git-ml-project.md) (Usando o repositório Git com um projeto do Azure Machine Learning Workbench) para obter mais detalhes.

> [!NOTE]
> O Azure Machine Learning dá suporte apenas a um projeto de equipe (VSTS) vazio com o Git como controle de versão.

> [!TIP]
> Se estiver recebendo um erro "A URL do repositório pode ser inválida ou o usuário pode não ter acesso", você poderá criar um token de segurança no VSTS (Segurança, adicionar tokens de acesso pessoal) e usar o argumento __vststoken__ ao criar seu projeto. 

```bash
az ml project create --name 9_25_2 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --repo https://ahgyger.visualstudio.com/AMLWorkbench/_git/9_25 --vststoken m2fholwwrcn7u4nrdqfxx007u5rztjfhgofnemvuvtue6pbwo3sa
```

### <a name="sample_create"></a>Criar um novo projeto usando um exemplo online
Neste exemplo, vamos usar um modelo de um projeto de hub do Git e usá-lo ao criar nosso novo projeto. 

```bash
# List the project samples
az ml project sample list

# Create a new project from a sample
az ml project create --name 9_25_3 --workspace amlsamplew --account amlsampleexp --resource-group amlsample --path c:\Users\ahgyger\Documents\AMLworkbench_Demo\ --repo https://ahgyger.visualstudio.com/AMLWorkbench/_git/9_25 --template-url https://github.com/MicrosoftDocs/MachineLearningSamples-Iris
```

Quando o projeto for criado, acesse o diretório dele antes de passar para a próxima etapa.

```bash
cd c:\Users\ahgyger\Documents\AMLworkbench_Demo\9_25\9_25_1
```

## <a name="step-4-run-the-training-experiment"></a>Etapa 4 Executar o teste de treinamento 
As etapas a seguir consideram que você tem um projeto com o exemplo de íris (consulte [Criar um novo projeto usando um exemplo online](#sample_create)).

### <a name="prepare-your-environment"></a>Prepare o seu ambiente 
Para o exemplo de íris, precisamos instalar matplotlib.

```bash
pip install matplotlib
```

###  <a name="submit-the-experiment"></a>Enviar o teste

```bash
# Execute the file
az ml experiment submit --run-configuration local iris_sklearn.py
```

### <a name="iterate-on-your-experiment-with-descending-regularization-rates"></a>Iterar no teste com decrescente taxas de regularização descendentes
Com um pouco de criatividade é simples criar um script Python que envia testes com taxas de regularização diferentes. (Talvez seja necessário editar o arquivo para apontar para o caminho do projeto correto).

```bash
python run.py
```

## <a name="step-5-view-run-history"></a>Etapa 5. Exibir histórico de execuções
O comando a seguir lista todas as execuções anteriores realizadas. 

```bash
az ml history list -o table
```
Executar o comando acima exibirá uma lista de todas as execuções que pertencem a este projeto. Veja que as métricas de taxa de precisão e de regularização também são listadas. Isso facilita a identificação da melhor execução na lista.

## <a name="step-5a-view-attachment-created-by-a-given-run"></a>Etapa 5.a Exibir o anexo criado por uma determinada execução 
Para exibir o anexo associado a uma determinada execução, podemos usar o comando de informações do histórico de execução.

```bash
az ml history info --run 9_16_4_1505589545267 --artifact driver_log
```

Para baixar os artefatos de uma execução, você pode usar o comando abaixo:

```bash
# Stream a given attachment 
az ml history info --run <run id> --artifact <artifact location>
```

## <a name="step-6-promote-artifacts-of-a-run"></a>Etapa 6. Promover os artefatos de uma execução 
Uma das execuções que fizemos tem um AUC melhor, então vamos usá-la para criar um serviço Web de pontuação a ser implantado na produção. Para fazer isso, primeiro é necessário promover os artefatos em um ativo.

```bash
az ml history promote --run 9_25_1505346632975 --artifact-path outputs/model.pkl --name model.pkl
```

Isso cria uma pasta __ativos__ no diretório do projeto que contém o pickle.link. Esse arquivo de link é usado para acompanhar a versão do arquivo promovido.

## <a name="step-7-download-the-files-to-be-operationalized"></a>Etapa 7. Baixar os arquivos a serem operacionalizados
Agora precisamos baixar os arquivos promovidos, para que possamos usá-los para criar o serviço Web de previsão. 

```bash
az ml asset download --link-file assets\pickle.link -d asset_download
```

## <a name="step-8-setup-your-model-management-environment"></a>Etapa 8. Configurar o ambiente de gerenciamento de modelos 
É possível criar um ambiente para implantar os serviços Web. Podemos executar o serviço Web no computador local usando o Docker. Ou implantá-lo em um cluster do ACS para operações de grande escala. 

```bash
# Create new environment
az ml env setup -l eastus2 -n amlsamplesenv
# Once setup is complete, set your environment for current context
az ml env set -g amlsamplesenvrg -n amlsamplesenv
```

## <a name="step-9-create-a-model-management-account"></a>Etapa 9. Criar uma conta de gerenciamento de modelos 
Uma conta de gerenciamento de modelos é necessária para implantar e acompanhar os modelos em produção. 

```bash
az ml account modelmanagement create -n amlsamplesacct -g amlsamplesenvrg -l eastus2
```

## <a name="step-10-create-a-web-service"></a>Etapa 10. Criar um serviço Web
Em seguida, criamos um serviço Web que retorna uma previsão usando o modelo implantado. 

```bash
az ml service create realtime -m modelfilename -f score.py -r python –n amlsamplews
```

## <a name="step-10-run-the-web-service"></a>Etapa 10. Executar o serviço Web
Usando a ID do serviço Web da saída da etapa anterior, podemos chamar o serviço Web e testá-lo. 

```
# Get web service usage infomration
az ml service usage realtime -i <web service id>

# Call the web service with the run command:
az ml service run realtime -i <web service id> -d <input data>
```

## <a name="deleting-all-the-resources"></a>Excluindo todos os recursos 
Vamos concluir este tutorial excluindo todos os recursos que criamos, a não ser que você deseje continuar trabalhando neles! 

Para fazer isso, basta excluir o grupo de recursos que contém todos os nossos recursos. 

```bash
az group delete --name amlsample
az group delete --name amlsamplesenvrg
```

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a usar os recursos de versão prévia do Azure Machine Learning para 
> [!div class="checklist"]
> * Configurar uma conta de experimentação e criar um espaço de trabalho
> * Criar projetos
> * Enviar testes para vários destino de computação
> * Promover e registrar um modelo treinado
> * Criar uma conta de gerenciamento de modelos para gerenciamento de modelos
> * Criar um ambiente para implantar um serviço Web
> * Implantar um serviço Web e pontuar com novos dados

