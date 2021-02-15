---
title: Integração do git para Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como o Azure Machine Learning se integra a um repositório git local para acompanhar o repositório, a ramificação e as informações de confirmação atuais como parte de uma execução de treinamento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 11/16/2020
ms.openlocfilehash: 7c10d3066dc7b9ee0994de8c327b286bf8c917e7
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100099465"
---
# <a name="git-integration-for-azure-machine-learning"></a>Integração do git para Azure Machine Learning

O [git](https://git-scm.com/) é um sistema de controle de versão popular que permite que você compartilhe e colabore em seus projetos. 

Azure Machine Learning dá suporte total a repositórios git para o acompanhamento do trabalho – você pode clonar repositórios diretamente no seu sistema de arquivos de espaço de trabalho compartilhado, usar o Git em sua Workstation local ou usar o Git de um pipeline de CI/CD.

Ao enviar um trabalho para Azure Machine Learning, se os arquivos de origem forem armazenados em um repositório git local, as informações sobre o repositório serão rastreadas como parte do processo de treinamento.

Como Azure Machine Learning rastreia informações de um repositório git local, ele não está vinculado a nenhum repositório central específico. Seu repositório pode ser clonado do GitHub, GitLab, bitbucket, Azure DevOps ou qualquer outro serviço compatível com o git.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Clonar repositórios Git em seu sistema de arquivos de workspace
Azure Machine Learning fornece um sistema de arquivos compartilhado para todos os usuários no espaço de trabalho.
Para clonar um repositório git nesse compartilhamento de arquivos, recomendamos que você crie uma instância de computação & [abrir um terminal](how-to-access-terminal.md).
Depois que o terminal for aberto, você terá acesso a um cliente git completo e poderá clonar e trabalhar com o Git por meio da experiência da CLI do git.

Recomendamos que você clone o repositório no diretório de usuários para que outros não façam colisões diretamente em seu Branch de trabalho.

É possível clonar qualquer repositório git que você possa autenticar (GitHub, Azure Repos, BitBucket, etc.)

Para obter mais informações sobre a clonagem, consulte o guia sobre [como usar a CLI do git](https://guides.github.com/introduction/git-handbook/).

## <a name="authenticate-your-git-account-with-ssh"></a>Autenticar sua conta do git com o SSH
### <a name="generate-a-new-ssh-key"></a>Gerar uma nova chave SSH
1) [Abra a janela do terminal](./how-to-access-terminal.md) na guia Azure Machine Learning notebook.

2) Cole o texto abaixo, substituindo em seu endereço de email.

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

Isso cria uma nova chave SSH, usando o email fornecido como um rótulo.

```
> Generating public/private rsa key pair.
```

3) Quando for solicitado a "inserir um arquivo no qual salvar a chave", pressione Enter. Isso aceita o local do arquivo padrão.

4) Verifique se o local padrão é '/Home/azureuser/.ssh ' e pressione Enter. Caso contrário, especifique o local '/Home/azureuser/.ssh '.

> [!TIP]
> Verifique se a chave SSH foi salva em '/Home/azureuser/.ssh '. Esse arquivo é salvo na instância de computação só pode ser acessado pelo proprietário da instância de computação

```
> Enter a file in which to save the key (/home/azureuser/.ssh/id_rsa): [Press enter]
```

5) No prompt, digite uma senha segura. Recomendamos que você adicione uma senha à chave SSH para maior segurança

```
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

### <a name="add-the-public-key-to-git-account"></a>Adicionar a chave pública à conta do git
1) Na janela do terminal, copie o conteúdo do arquivo de chave pública. Se você renomeou a chave, substitua id_rsa. pub pelo nome do arquivo de chave pública.

```bash
cat ~/.ssh/id_rsa.pub
```
> [!TIP]
> **Copiar e colar no terminal**
> * Windows: `Ctrl-Insert` para copiar e usar `Ctrl-Shift-v` ou `Shift-Insert` para colar.
> * Mac OS: `Cmd-c` para copiar e `Cmd-v` para colar.
> * Talvez o FireFox/IE não dê suporte apropriado a permissões de área de transferência.

2) Selecione e copie a saída da chave na área de transferência.

+ [GitHub](https://docs.github.com/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account)

+ [GitLab](https://docs.gitlab.com/ee/ssh/#adding-an-ssh-key-to-your-gitlab-account)

+ [DevOps do Azure](/azure/devops/repos/git/use-ssh-keys-to-authenticate?view=azure-devops#step-2--add-the-public-key-to-azure-devops-servicestfs&preserve-view=true)  Comece na **etapa 2**.

+ [BitBucket](https://support.atlassian.com/bitbucket-cloud/docs/set-up-an-ssh-key/#SetupanSSHkey-ssh2). Comece na **etapa 4**.

### <a name="clone-the-git-repository-with-ssh"></a>Clonar o repositório Git com SSH

1) Copie a URL de clone de git do SSH do repositório git.

2) Cole a URL no `git clone` comando a seguir para usar a URL do repositório Git do SSH. Esta será uma aparência semelhante a:

```bash
git clone git@example.com:GitUser/azureml-example.git
Cloning into 'azureml-example'...
```

Você verá uma resposta como:

```bash
The authenticity of host 'example.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,192.30.255.112' (RSA) to the list of known hosts.
```

O SSH pode exibir a impressão digital SSH do servidor e solicitar que você a Verifique. Você deve verificar se a impressão digital exibida corresponde a uma das impressões digitais na página chaves públicas SSH.

O SSH exibe essa impressão digital quando se conecta a um host desconhecido para protegê [-lo contra ataques man-in-the-Middle](/previous-versions/windows/it-pro/windows-2000-server/cc959354(v=technet.10)). Depois de aceitar a impressão digital do host, o SSH não solicitará novamente a não ser que a impressão digital seja alterada.

3) Quando for perguntado se deseja continuar conectando, digite `yes` . O Git clonará o repositório e configurará a origem remota para se conectar com o SSH para futuros comandos git.

## <a name="track-code-that-comes-from-git-repositories"></a>Acompanhar o código proveniente de repositórios git

Quando você envia uma execução de treinamento do SDK do Python ou da CLI do Machine Learning, os arquivos necessários para treinar o modelo são carregados no espaço de trabalho. Se o `git` comando estiver disponível no ambiente de desenvolvimento, o processo de carregamento o usará para verificar se os arquivos estão armazenados em um repositório git. Nesse caso, as informações do repositório git também são carregadas como parte da execução do treinamento. Essas informações são armazenadas nas seguintes propriedades para a execução de treinamento:

| Propriedade | Comando git usado para obter o valor | Descrição |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | O URI do qual o repositório foi clonado. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | O URI do qual o repositório foi clonado. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | A ramificação ativa quando a execução foi enviada. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | A ramificação ativa quando a execução foi enviada. |
| `azureml.git.commit` | `git rev-parse HEAD` | O hash de confirmação do código que foi enviado para a execução. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | O hash de confirmação do código que foi enviado para a execução. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, se a ramificação/confirmação estiver suja; caso contrário, `false` . |

Essas informações são enviadas para execuções que usam um estimador, pipeline de Machine Learning ou execução de script.

Se os arquivos de treinamento não estiverem localizados em um repositório git em seu ambiente de desenvolvimento, ou `git` se o comando não estiver disponível, nenhuma informação relacionada ao git será controlada.

> [!TIP]
> Para verificar se o comando git está disponível no ambiente de desenvolvimento, abra uma sessão do Shell, um prompt de comando, o PowerShell ou outra interface de linha de comando e digite o seguinte comando:
>
> ```
> git --version
> ```
>
> Se instalado e, no caminho, você receberá uma resposta semelhante a `git version 2.4.1` . Para obter mais informações sobre como instalar o Git em seu ambiente de desenvolvimento, consulte o [site do git](https://git-scm.com/).

## <a name="view-the-logged-information"></a>Exibir as informações registradas

As informações do git são armazenadas nas propriedades de uma execução de treinamento. Você pode exibir essas informações usando o portal do Azure, o SDK do Python e a CLI. 

### <a name="azure-portal"></a>Portal do Azure

1. No [portal do estúdio](https://ml.azure.com), selecione seu espaço de trabalho.
1. Selecione __experimentos__ e, em seguida, selecione um de seus experimentos.
1. Selecione uma das execuções na coluna __número da execução__ .
1. Selecione __saídas + logs__ e, em seguida, expanda os __logs__ e as entradas do __azureml__ . Selecione o link que começa com o __### \_ Azure__.

As informações registradas em log contêm texto semelhante ao JSON a seguir:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>SDK do Python

Depois de enviar uma execução de treinamento, um objeto [Run](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py) é retornado. O `properties` atributo desse objeto contém as informações do git registradas. Por exemplo, o código a seguir recupera o hash de confirmação:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

O `az ml run` comando da CLI pode ser usado para recuperar as propriedades de uma execução. Por exemplo, o comando a seguir retorna as propriedades da última execução no experimento chamado `train-on-amlcompute` :

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Para obter mais informações, consulte a documentação de referência de [execução do AZ ml](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest) .

## <a name="next-steps"></a>Próximas etapas

* [Usar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md)