---
title: Executar blocos de anotações do Jupyter em seu espaço de trabalho
titleSuffix: Azure Machine Learning
description: Saiba como executar um notebook Jupyter sem sair de seu espaço de trabalho no Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 06ae46eb96db39f44cd052e6e9b0d1a19f898007
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100091492"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>Executar blocos de anotações do Jupyter em seu espaço de trabalho

Saiba como executar seus blocos de anotações do Jupyter diretamente em seu espaço de trabalho no Azure Machine Learning Studio. Embora seja possível iniciar o [Jupyter](https://jupyter.org/) ou o [JupyterLab](https://jupyterlab.readthedocs.io), você também pode editar e executar seus notebooks sem sair do workspace.

Para obter informações sobre como criar e gerenciar arquivos, incluindo blocos de anotações, consulte [criar e gerenciar arquivos em seu espaço de trabalho](how-to-manage-files.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um Workspace do Machine Learning. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

## <a name="edit-a-notebook"></a>Editar um notebook

Para editar um notebook, abra qualquer notebook localizado na seção **Arquivos de usuário** do workspace. Clique na célula que você deseja editar.  Se você não tiver nenhum bloco de anotações nesta seção, consulte [criar e gerenciar arquivos em seu espaço de trabalho](how-to-manage-files.md).

Você pode editar o bloco de anotações sem se conectar a uma instância de computação.  Quando você quiser executar as células no bloco de anotações, selecione ou crie uma instância de computação.  Se você selecionar uma instância de computação interrompida, ela será iniciada automaticamente quando você executar a primeira célula.

Quando uma instância de computação está em execução, você também pode usar auto-completar de código, da plataforma [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), em qualquer notebook Python.

Você também pode iniciar o Jupyter ou o JupyterLab na barra de ferramentas do bloco de anotações.  O Azure Machine Learning não fornece atualizações nem corrige bugs do Jupyter ou do JupyterLab, pois são produtos de software livre fora do limite do Suporte da Microsoft.

## <a name="focus-mode"></a>Modo de foco

Use o modo de foco para expandir sua exibição atual para que você possa se concentrar nas guias ativas. O modo de foco oculta o explorador de arquivos dos blocos de anotações.

1. Na barra de ferramentas da janela do terminal, selecione **modo de foco** para ativar o modo de foco. Dependendo da largura da janela, isso pode estar localizado no item de menu **...** na barra de ferramentas.
1. No modo de foco, retorne ao modo de exibição padrão selecionando **exibição padrão**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Alternar modo de foco/exibição padrão":::

## <a name="use-intellisense"></a>Usar o IntelliSense

O [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) é um recurso de conclusão de código que inclui uma série de recursos: membros da lista, informações de parâmetro, informações rápidas e palavra completa. Essas funcionalidades ajudam você a aprender mais sobre o código que está usando, a manter o acompanhamento dos parâmetros que está digitando e a adicionar chamadas a métodos e propriedades pressionando apenas algumas teclas.  

Ao digitar o código, use Ctrl + espaço para disparar o IntelliSense.

## <a name="clean-your-notebook-preview"></a>Limpar o bloco de anotações (visualização)

> [!IMPORTANT]
> O recurso de coleta está atualmente em visualização pública.
> A versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ao longo da criação de um notebook, você normalmente acaba com as células usadas para a exploração ou a depuração de dados. O recurso de *coleta* ajudará você a produzir um notebook limpo sem essas células estranhas.

1. Execute todas as células do bloco de anotações.
1. Selecione a célula que contém o código que você deseja que o novo bloco de anotações execute. Por exemplo, o código que envia um experimento ou talvez o código que registra um modelo.
1. Selecione o ícone de **coleta** que aparece na barra de ferramentas da célula.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Captura de tela: selecione o ícone reunir":::
1. Insira o nome para o novo bloco de anotações "reunido".  

O novo bloco de anotações contém apenas células de código, com todas as células necessárias para produzir os mesmos resultados que a célula que você selecionou para coleta.

## <a name="save-and-checkpoint-a-notebook"></a>Salvar e ponto de verificação de um notebook

Azure Machine Learning cria um arquivo de ponto de verificação quando você cria um arquivo *ipynb* .

Na barra de ferramentas do bloco de anotações, selecione o menu e, em seguida, **arquivo &gt; salvar e ponto de verificação** para salvar o bloco de anotações manualmente e ele adicionará um arquivo de ponto de verificação associado ao bloco de anotações.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Captura de tela da ferramenta salvar na barra de ferramentas do bloco de anotações":::

Cada bloco de anotações é salvo AutoSalva a cada 30 segundos. O salvamento automático atualiza apenas o arquivo *ipynb* inicial, não o arquivo de ponto de verificação.
 
Selecione **pontos de verificação** no menu do bloco de anotações para criar um ponto de verificação nomeado e reverter o bloco de anotações para um ponto de verificação salvo.

## <a name="export-a-notebook"></a>Exportar um bloco de anotações

Na barra de ferramentas do bloco de anotações, selecione o menu e, em seguida, **exporte como** para exportar o bloco de anotações como qualquer um dos tipos com suporte:

* Notebook
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="Exportar um bloco de anotações para o computador":::

O arquivo exportado é salvo em seu computador.

## <a name="run-a-notebook-or-python-script"></a>Executar um script de bloco de anotações ou Python

Para executar um bloco de anotações ou um script Python, primeiro você se conecta a uma [instância de computação](concept-compute-instance.md)em execução.

* Se você não tiver uma instância de computação, use estas etapas para criar uma:

    1. Na barra de ferramentas do bloco de anotações ou script, à direita da lista suspensa de computação, selecione **+ nova computação**. Dependendo do tamanho da tela, isso pode estar localizado em um menu **...** .
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="Criar uma nova computação":::
    1. Dê um nome a Computação e escolha um **Tamanho da Máquina Virtual**. 
    1. Selecione **Criar**.
    1. A instância de computação é conectada ao arquivo automaticamente.  Agora você pode executar as células do bloco de anotações ou o script Python usando a ferramenta à esquerda da instância de computação.

* Se você tiver uma instância de computação interrompida, selecione  **Iniciar computação** à direita da lista suspensa de computação. Dependendo do tamanho da tela, isso pode estar localizado em um menu **...** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="Iniciar instância de computação":::

Somente você pode ver e usar as instâncias de computação que criar.  Seus **Arquivos de usuário** são armazenados separadamente da VM e compartilhados entre todas as instâncias de computação no workspace.

### <a name="view-logs-and-output"></a>Exibir logs e saída

Use [widgets de bloco de anotações](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) para exibir o progresso da execução e dos logs. Um widget é assíncrono e fornece atualizações até que o treinamento seja concluído. Os widgets do Azure Machine Learning também têm suporte no Jupyter e no JupterLab.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="Captura de tela: widget Jupyter Notebook ":::

## <a name="explore-variables-in-the-notebook"></a>Explorar variáveis no bloco de anotações

Na barra de ferramentas do bloco de anotações, use a ferramenta **Gerenciador de variáveis** para mostrar o nome, o tipo, o comprimento e os valores de exemplo para todas as variáveis que foram criadas no bloco de anotações.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="Captura de tela: ferramenta de Gerenciador de variáveis":::

Selecione a ferramenta para mostrar a janela do Gerenciador de variáveis.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="Captura de tela: janela do Gerenciador de variáveis":::

## <a name="navigate-with-a-toc"></a>Navegar com um sumário

Na barra de ferramentas do bloco  **de** anotações, use a ferramenta Sumário para exibir ou ocultar o Sumário.  Inicie uma célula de redução com um título para adicioná-la ao Sumário. Clique em uma entrada na tabela para rolar até essa célula no bloco de anotações.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="Captura de tela: Sumário no bloco de anotações":::

## <a name="change-the-notebook-environment"></a>Alterar o ambiente do notebook

A barra de ferramentas do bloco de anotações permite que você altere o ambiente no qual seu notebook é executado.  

Essas ações não alteraram o estado do notebook ou os valores de nenhuma variável no notebook:

|Ação  |Result  |
|---------|---------| --------|
|Parar o kernel     |  Para qualquer célula em execução. A execução de uma célula reiniciará automaticamente o kernel. |
|Navegar para outra seção do workspace     |     As células em execução são interrompidas. |

Essas ações redefinirão o estado do notebook e redefinirão todas as variáveis no notebook.

|Ação  |Result  |
|---------|---------| --------|
| Altere o Kernel | O Notebook usa o novo kernel |
| Alternar computação    |     O Notebook usa automaticamente a nova computação. |
| Redefinir computação | Inicia novamente quando você tenta executar uma célula |
| Parar a computação     |    Nenhuma célula será executada  |
| Abrir o notebook no Jupyter ou JupyterLab     |    Notebook aberto em uma nova guia.  |

## <a name="add-new-kernels"></a>Adicionar novos kernels

[Use o terminal ](how-to-access-terminal.md#add-new-kernels) para criar e adicionar novos kernels à instância de computação. O notebook localizará automaticamente todos os kernels do Jupyter instalados na instância de computação conectada.

Use a lista suspensa kernel à direita para alterar para qualquer um dos kernels instalados.  


### <a name="status-indicators"></a>Indicadores de status

Um indicador ao lado do menu suspenso **Computação** mostra o status.  O status também é mostrado no menu suspenso em si.  

|Color |Status da computação |
|---------|---------| 
| Verde | Execução de computação |
| Vermelho |Falha na computação | 
| Preto | Computação parada |
|  Azul-claro |Criar, iniciar, reiniciar, configurar computação |
|  Cinza |Excluir, parar computação |

Um indicador ao lado do menu suspenso **Kernel** mostra seu status.

|Color |Status do kernel |
|---------|---------|
|  Verde |Kernel conectado, ocioso, ocupado|
|  Cinza |Kernel não conectado |

## <a name="find-compute-details"></a>Localizar detalhes da computação

Encontre detalhes sobre suas instâncias de computação na página **Computação** no [Studio](https://ml.azure.com).

## <a name="troubleshooting"></a>Solução de problemas

* Se você não puder se conectar a um bloco de anotações, verifique se a comunicação de soquete da Web **não** está desabilitada. Para a funcionalidade de Jupyter da instância de computação funcionar, a comunicação de soquete da Web deve ser habilitada. Verifique se a sua rede permite conexões WebSocket com *. instances.azureml.net e *. instances.azureml.ms. 

* Quando a instância de computação é implantada em um espaço de trabalho de link privado, ela só pode ser acessada de dentro da rede virtual. Se você estiver usando o arquivo DNS ou hosts personalizado, adicione uma entrada para <nome da instância>. <region> . instances.azureml.ms com endereço IP privado do ponto de extremidade particular do espaço de trabalho. Para obter mais informações, consulte o artigo [DNS personalizado](https://docs.microsoft.com/azure/machine-learning/how-to-custom-dns?tabs=azure-cli) .
    
## <a name="next-steps"></a>Próximas etapas

* [Executar seu primeiro experimento](tutorial-1st-experiment-sdk-train.md)
* [Fazer backup do armazenamento de arquivos com instantâneos](../storage/files/storage-snapshots-files.md)
* [Trabalhando em ambientes seguros](https://docs.microsoft.com/azure/machine-learning/how-to-secure-training-vnet#compute-instance)
