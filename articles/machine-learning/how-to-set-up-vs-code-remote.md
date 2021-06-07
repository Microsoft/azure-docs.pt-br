---
title: Conectar-se à instância de computação no Visual Studio Code (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como se conectar a uma instância de computação Azure Machine Learning no Visual Studio Code para executar cargas de trabalho de desenvolvimento Jupyter Notebook e remotas interativas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 11/16/2020
ms.openlocfilehash: 3f3794ed4748eda44e863ff4627dedc0a34691af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98070092"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Conectar-se a uma instância de computação Azure Machine Learning no Visual Studio Code (visualização)

Neste artigo, você aprenderá a se conectar a uma instância de computação Azure Machine Learning usando Visual Studio Code.

Uma [instância de computação Azure Machine Learning](concept-compute-instance.md) é uma estação de trabalho baseada em nuvem totalmente gerenciada para cientistas de dados e fornece recursos de gerenciamento e preparação empresarial para os administradores de ti.

Há duas maneiras pelas quais você pode se conectar a uma instância de computação do Visual Studio Code:

* Servidor de Jupyter Notebook remoto. Essa opção permite que você defina uma instância de computação como um servidor de Jupyter Notebook remoto.
* [Visual Studio Code desenvolvimento remoto](https://code.visualstudio.com/docs/remote/remote-overview). Visual Studio Code desenvolvimento remoto permite que você use um contêiner, computador remoto ou o subsistema do Windows para Linux (WSL) como um ambiente de desenvolvimento repleto de recursos.

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Configurar instância de computação como servidor de bloco de anotações remoto

Para configurar uma instância de computação como um servidor de Jupyter Notebook remoto, você precisará de alguns pré-requisitos:

* Extensão de Visual Studio Code de Azure Machine Learning. Para obter mais informações, consulte o [Guia de configuração da extensão do Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).
* Espaço de trabalho Azure Machine Learning. [Use a extensão Azure Machine Learning Visual Studio Code para criar um novo espaço de trabalho](how-to-manage-resources-vscode.md#create-a-workspace) , se você ainda não tiver um.

Para se conectar a uma instância de computação:

1. Abra uma Jupyter Notebook no Visual Studio Code.
1. Quando a experiência do notebook integrado for carregada, selecione **servidor Jupyter**.

    > [!div class="mx-imgBorder"]
    > ![Iniciar Azure Machine Learning lista suspensa do servidor de Jupyter Notebook remoto](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Como alternativa, você também usa a paleta de comandos:

    1. Abra a paleta de comandos selecionando **Exibição > Paleta de Comandos** na barra de menus.
    1. Digite na caixa de texto `Azure ML: Connect to Compute instance Jupyter server` .

1. Escolha `Azure ML Compute Instances` na lista de opções do servidor Jupyter.
1. Selecione sua assinatura na lista de assinaturas. Se você tiver configurado anteriormente o espaço de trabalho de Azure Machine Learning padrão, essa etapa será ignorada.
1. Selecione o workspace.
1. Selecione a instância de computação na lista. Se você não tiver uma, selecione **criar nova instância de computação do ml do Azure** e siga os prompts para criar uma.
1. Para que as alterações entrem em vigor, você precisa recarregar Visual Studio Code.
1. Abra um Jupyter Notebook e execute uma célula.

> [!IMPORTANT]
> Você **deve** executar uma célula para estabelecer a conexão.

Neste ponto, você pode continuar a executar células em seu Jupyter Notebook.

> [!TIP]
> Você também pode trabalhar com arquivos de script do Python (. py) que contêm células de código do tipo Jupyter. Para obter mais informações, consulte a [documentação interativa do Visual Studio Code Python](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="configure-compute-instance-remote-development"></a>Configurar o desenvolvimento remoto da instância de computação

Para uma experiência de desenvolvimento remoto com recursos completos, você precisará de alguns pré-requisitos:

* [Visual Studio Code extensão SSH remota](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh).
* Instância de computação habilitada para SSH. Para obter mais informações, [consulte o guia criar uma instância de computação](how-to-create-manage-compute-instance.md).

> [!NOTE]
> Em plataformas Windows, você deve [instalar um cliente SSH compatível com OpenSSH](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) se ainda não houver um. Não há suporte para o percurso no Windows, pois o comando ssh deve estar no caminho.

### <a name="get-the-ip-and-ssh-port-for-your-compute-instance"></a>Obter o IP e a porta SSH para sua instância de computação

1. Acesse o Azure Machine Learning Studio em https://ml.azure.com/ .
2. Selecione seu [espaço de trabalho](concept-workspace.md).
1. Clique na guia **instâncias de computação** .
1. Na coluna **URI do aplicativo** , clique no link **SSH** da instância de computação que você deseja usar como uma computação remota. 
1. Na caixa de diálogo, anote o endereço IP e a porta SSH. 
1. Salve sua chave privada no diretório ~/.ssh/no computador local; por exemplo, abra um editor para um novo arquivo e cole a chave em: 

   **Linux**:

   ```sh
   vi ~/.ssh/id_azmlcitest_rsa  
   ```

   **Windows**:

   ```cmd
   notepad C:\Users\<username>\.ssh\id_azmlcitest_rsa
   ```

   A chave privada terá uma aparência semelhante a esta:

   ```text
   -----BEGIN RSA PRIVATE KEY-----

   MIIEpAIBAAKCAQEAr99EPm0P4CaTPT2KtBt+kpN3rmsNNE5dS0vmGWxIXq4vAWXD
   ..... 
   ewMtLnDgXWYJo0IyQ91ynOdxbFoVOuuGNdDoBykUZPQfeHDONy2Raw==

   -----END RSA PRIVATE KEY-----
   ```

1. Altere as permissões no arquivo para garantir que apenas você possa ler o arquivo.  

   ```sh
   chmod 600 ~/.ssh/id_azmlcitest_rsa
   ```

### <a name="add-instance-as-a-host"></a>Adicionar instância como um host

Abra o arquivo `~/.ssh/config` (Linux) ou `C:\Users<username>.ssh\config` (Windows) em um editor e adicione uma nova entrada semelhante ao conteúdo abaixo:

```
Host azmlci1 

    HostName 13.69.56.51 

    Port 50000 

    User azureuser 

    IdentityFile ~/.ssh/id_azmlcitest_rsa
```

Aqui estão alguns detalhes sobre os campos:

|Campo|Descrição|
|----|---------|
|Host|Use qualquer abreviação que desejar para a instância de computação |
|HostName|Este é o endereço IP da instância de computação |
|Porta|Esta é a porta mostrada na caixa de diálogo SSH acima |
|Usuário|Isso precisa ser `azureuser` |
|IdentityFile|Deve apontar para o arquivo em que você salvou a chave privada |

Agora, você deve ser capaz de usar o ssh em sua instância de computação usando a abreviação usada acima, `ssh azmlci1` .

### <a name="connect-vs-code-to-the-instance"></a>Conectar VS Code à instância

1. Clique no ícone Remote-SSH na barra de atividades Visual Studio Code para mostrar as configurações de SSH.

1. Clique com o botão direito do mouse na configuração do host SSH que você acabou de criar.

1. Selecione **conectar ao host na janela atual**. 

Daqui em diante, você está trabalhando totalmente na instância de computação e agora pode editar, depurar, usar o Git, usar extensões, etc.--assim como você pode com seu Visual Studio Code local.

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou o Visual Studio Code remoto, é possível usar uma instância de computação como computação remota do Visual Studio Code para [depurar interativamente seu código](how-to-debug-visual-studio-code.md).

[Tutorial: Treinar seu primeiro modelo de ML](tutorial-1st-experiment-sdk-train.md) mostra como usar uma instância de computação com um notebook integrado.
