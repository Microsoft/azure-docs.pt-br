---
title: Usar um Jupyter Notebook com ofertas da Microsoft
description: Saiba mais sobre como os Jupyter Notebooks podem ser usados com as ofertas da Microsoft.
ms.topic: quickstart
ms.date: 02/01/2021
ms.openlocfilehash: 5679c28d9cc8a4f1893ffad72002b66ad59861e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99507370"
---
# <a name="use-a-jupyter-notebook-with-microsoft-offerings"></a>Usar um Jupyter Notebook com ofertas da Microsoft

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Neste guia de início rápido, você aprenderá a importar um Jupyter Notebook para uso em diversas ofertas da Microsoft. 

Se você tiver Jupyter Notebooks existentes ou quiser iniciar um novo projeto, poderá usá-los com muitas das ofertas da Microsoft. Algumas opções descritas nas seções seguintes incluem: 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [Codespaces do GitHub](#use-notebooks-in-github-codespaces)
- [Azure Machine Learning](#use-notebooks-with-azure-machine-learning)
- [Azure Lab Services](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>Criar um ambiente para notebooks

Se você quiser criar um ambiente que corresponda ao da Versão Prévia desativada do Azure Notebooks, use o arquivo de script fornecido no GitHub.

1. Navegue até o [repositório do GitHub](https://github.com/microsoft/AzureNotebooks) do Azure Notebooks ou [acesse diretamente a pasta de ambiente](https://aka.ms/aznbrequirementstxt).
1. Em um prompt de comando, navegue até o diretório que você deseja usar para seus projetos.
1. Baixe o conteúdo da pasta de ambiente e siga as instruções do LEIAME para instalar as dependências do pacote do Azure Notebooks.


## <a name="use-notebooks-in-visual-studio-code"></a>Usar Notebooks no Visual Studio Code

O [VS Code](https://code.visualstudio.com/) é um editor de código gratuito que você pode usar localmente ou conectado à computação remota. Combinado à extensão do Python, ele oferece um ambiente completo para desenvolvimento em Python, incluindo uma experiência nativa avançada para trabalhar com Jupyter Notebooks. 

![Suporte ao Jupyter Notebook do VS Code](media/vs-code-jupyter-notebook.png)

Caso você tenha arquivos de projeto ou deseje criar um notebook, use o VS Code. Para obter diretrizes sobre como usar o VS Code com Jupyter Notebooks, confira os tutoriais [Como trabalhar com Jupyter Notebooks no Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support) e [Ciência de dados no Visual Studio Code](https://code.visualstudio.com/docs/python/data-science-tutorial).

Você também pode usar o [script de ambiente do Azure Notebooks](#create-an-environment-for-notebooks) com o Visual Studio Code para criar um ambiente que corresponda ao Azure Notebooks (versão prévia).

## <a name="use-notebooks-in-github-codespaces"></a>Usar Notebooks no Codespaces do GitHub

O Codespaces do GitHub fornece ambientes hospedados na nuvem em que você pode editar seus notebooks usando o Visual Studio Code ou seu navegador da Web. Ele oferece a mesma experiência incrível do Jupyter que o VS Code, mas sem necessidade de instalar nada em seu dispositivo. Se você não quer configurar um ambiente local e prefere uma solução com suporte de nuvem, criar um espaço de código é uma ótima opção. Introdução:
1. (Opcional) Reúna todos os arquivos de projeto que deseja usar com os Codespaces do GitHub.
1. [Crie um repositório do GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) para armazenar seus notebooks.   
1. [Adicione seus arquivos](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) ao repositório.
1. [Solicitar acesso à visualização do Codespaces do GitHub](https://github.com/features/codespaces)

## <a name="use-notebooks-with-azure-machine-learning"></a>Usar Notebooks com o Azure Machine Learning

O Azure Machine Learning fornece uma plataforma de machine learning de ponta a ponta para permitir que os usuários criem e implantem modelos mais rapidamente no Azure. O Azure ML permite que você execute Jupyter Notebooks em uma VM ou em um ambiente de computação de cluster compartilhado. Se você precisa de uma solução baseada em nuvem para sua carga de trabalho de ML com acompanhamento de experimento, gerenciamento de conjunto de dados e muito mais, recomendamos usar o Azure Machine Learning. Para começar a usar o Azure ML:

1. (Opcional) Reúna todos os arquivos de projeto que deseja usar com o Azure ML.
1. [Crie um Workspace](../machine-learning/how-to-manage-workspace.md) no portal do Azure.

   ![Criar um workspace](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. Abra o [Azure Studio (versão prévia)](https://ml.azure.com/).
1. Usando a barra de navegação do lado esquerdo, selecione **Notebooks**.
1. Clique no botão **Carregar arquivos** e carregue os arquivos de projeto.

Para obter informações adicionais sobre o Azure ML e a execução de Jupyter Notebooks, você pode examinar a [documentação](../machine-learning/how-to-run-jupyter-notebooks.md) ou experimentar o módulo [Introdução a Machine Learning](/learn/modules/intro-to-azure-machine-learning-service/) no Microsoft Learn.


## <a name="use-azure-lab-services"></a>Usar o Azure Lab Services

O [Azure Lab Services](https://azure.microsoft.com/services/lab-services/) permitir que educadores facilmente configurem e forneçam acesso sob demanda a VMs pré-configuradas para uma sala de aula inteira. Se você está procurando uma forma de trabalhar com Jupyter Notebooks e computação em nuvem em um ambiente de sala de aula personalizado, o Lab Services é uma ótima opção.

![image](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

Caso você tenha arquivos de projeto ou deseje criar um notebook, use o Azure Lab Services. Para obter diretrizes sobre como configurar um laboratório, confira [Configurar um laboratório para ensinar ciência de dados com Python e Jupyter Notebooks](../lab-services/class-type-jupyter-notebook.md)

## <a name="use-github"></a>Usar o GitHub

O GitHub fornece uma forma gratuita com suporte de controle de origem de armazenar notebooks (e outros arquivos), compartilhar seus notebooks com outras pessoas e trabalhar de maneira colaborativa. Se você está procurando uma forma de compartilhar seus projetos e colaborar com outras pessoas, o GitHub é uma ótima opção e pode ser combinado com o [Codespaces do GitHub](#use-notebooks-in-github-codespaces) para uma excelente experiência de desenvolvimento. Para começar a usar o GitHub

1. (Opcional) Reúna os arquivos de projeto que deseja usar com o GitHub.
1. [Crie um repositório do GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) para armazenar seus notebooks. 
1. [Adicione seus arquivos](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) ao repositório.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre o Python no Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial)
- [Saiba mais sobre o Azure Machine Learning e os Jupyter Notebooks](../machine-learning/how-to-run-jupyter-notebooks.md)
- [Saiba mais sobre o Codespaces do GitHub](https://github.com/features/codespaces)
- [Saiba mais sobre o Azure Lab Services](https://azure.microsoft.com/services/lab-services/)
- [Saiba mais sobre o GitHub](https://help.github.com/github/getting-started-with-github/)
