---
title: Como executar blocos de anotações do Jupyter em seu espaço de trabalho
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
ms.openlocfilehash: cdd8783daf9e1e1458f47e773eb3b6ccedfbae83
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624550"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Como executar Jupyter Notebooks em seu workspace

Saiba como executar seus blocos de anotações do Jupyter diretamente em seu espaço de trabalho no Azure Machine Learning Studio. Embora seja possível iniciar o [Jupyter](https://jupyter.org/) ou o [JupyterLab](https://jupyterlab.readthedocs.io), você também pode editar e executar seus notebooks sem sair do workspace.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://aka.ms/AMLFree) antes de começar.
* Um Workspace do Machine Learning. Confira [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a> Criar notebooks

No Workspace do Azure Machine Learning, crie um Jupyter Notebook e comece a trabalhar. O notebook recém-criado é armazenado no armazenamento do workspace padrão. Este notebook pode ser compartilhado com qualquer pessoa com acesso ao workspace. 

Para criar um notebook: 

1. Abra seu workspace no [Azure Machine Learning Studio](https://ml.azure.com).
1. No lado esquerdo, selecione **Notebooks**. 
1. Selecione o ícone **Criar arquivo** acima da lista **Arquivos do usuário** na seção **Meus arquivos**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Criar arquivo":::

1. Dê um nome ao arquivo. 
1. Para arquivos do bloco de anotações Jupyter, selecione **Notebook** como o tipo de arquivo.
1. Selecione um diretório de arquivos.
1. Selecione **Criar**.

Você também pode criar arquivos de texto.  Selecione **Texto** como o tipo de arquivo e adicione a extensão ao nome (por exemplo, myfile.py ou myfile. txt)  

Você também pode carregar pastas e arquivos, incluindo notebooks, com as ferramentas na parte superior da página Notebooks.  Os notebooks e a maioria dos tipos de arquivo de texto são exibidos na seção de versão prévia.  Nenhuma versão prévia está disponível para a maioria dos outros tipos de arquivo.

> [!IMPORTANT]
> O conteúdo em blocos de anotações e scripts pode potencialmente ler dados de suas sessões e acessar dados sem sua organização no Azure.  Carregar somente arquivos de fontes confiáveis. Para obter mais informações, consulte [proteger práticas recomendadas de código](concept-secure-code-best-practice.md#azure-ml-studio-notebooks).

### <a name="clone-samples"></a>Clonar amostras

Seu workspace contém uma pasta **Amostras** com notebooks criados para ajudá-lo a explorar o SDK e servir como exemplos para seus projetos de Machine Learning.  Você pode clonar esses notebooks em sua pasta no seu contêiner de armazenamento do workspace.  

Para um exemplo, veja [Tutorial: Criar seu primeiro experimento de ML](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Usar arquivos do Git e da criar versão de meus arquivos

Você pode acessar todas as operações do git usando uma janela do terminal. Todos os arquivos e pastas do Git serão armazenados no sistema de arquivos do workspace.

> [!NOTE]
> Adicione seus arquivos e pastas em qualquer lugar na pasta **~/cloudfiles/code/Users** para que eles fiquem visíveis em todos os seus ambientes do Jupyter.

Para acessar o terminal:

1. Abra seu workspace no [Azure Machine Learning Studio](https://ml.azure.com).
1. No lado esquerdo, selecione **Notebooks**.
1. Selecione qualquer notebook localizado na seção **Arquivos do usuário** no lado esquerdo.  Se você não tiver nenhum notebook, primeiro [crie um notebook](#create)
1. Selecione um destino de **computação** ou crie um novo e aguarde até que ele esteja em execução.
1. Selecione o ícone **Abrir terminal**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Abra o terminal":::

1. Se você não vir o ícone, selecione o **...** à direita do destino de computação e, em seguida, selecione **abrir terminal**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Abrir terminal de...":::


Saiba mais sobre [clonar repositórios Git em seu sistema de arquivos do workspace](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

### <a name="copy-and-paste-in-terminal"></a>Copiar e colar no terminal

> * Windows: `Ctrl-Insert` para copiar e usar `Ctrl-Shift-v` ou `Shift-Insert` para colar.
> * Mac OS: `Cmd-c` para copiar e `Cmd-v` para colar.
> * Talvez o FireFox/IE não dê suporte apropriado a permissões de área de transferência.

### <a name="share-notebooks-and-other-files"></a>Compartilhar notebooks e outros arquivos

Copie e cole o URL para compartilhar um notebook ou arquivo.  Somente outros usuários do espaço de trabalho podem acessar essa URL.  Saiba mais sobre [conceder acesso ao seu workspace](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Editar um notebook

Para editar um notebook, abra qualquer notebook localizado na seção **Arquivos de usuário** do workspace. Clique na célula que você deseja editar. 

Você pode editar o bloco de anotações sem se conectar a uma instância de computação.  Quando você quiser executar as células no bloco de anotações, selecione ou crie uma instância de computação.  Se você selecionar uma instância de computação interrompida, ela será iniciada automaticamente quando você executar a primeira célula.

Quando uma instância de computação está em execução, você também pode usar auto-completar de código, da plataforma [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), em qualquer notebook Python.

Você também pode iniciar o Jupyter ou o JupyterLab na barra de ferramentas do bloco de anotações.  O Azure Machine Learning não fornece atualizações nem corrige bugs do Jupyter ou do JupyterLab, pois são produtos de software livre fora do limite do Suporte da Microsoft.

### <a name="focus-mode"></a>Modo de foco

Use o modo de foco para expandir sua exibição atual para que você possa se concentrar nas guias ativas. O modo de foco oculta o explorador de arquivos dos blocos de anotações.

1. Na barra de ferramentas da janela do terminal, selecione **modo de foco** para ativar o modo de foco. Dependendo da largura da janela, isso pode estar localizado no item de menu **...** na barra de ferramentas.
1. No modo de foco, retorne ao modo de exibição padrão selecionando **exibição padrão**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Alternar modo de foco/exibição padrão":::


### <a name="use-intellisense"></a>Usar o IntelliSense

O [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) é um recurso de conclusão de código que inclui uma série de recursos: membros da lista, informações de parâmetro, informações rápidas e palavra completa. Essas funcionalidades ajudam você a aprender mais sobre o código que está usando, a manter o acompanhamento dos parâmetros que está digitando e a adicionar chamadas a métodos e propriedades pressionando apenas algumas teclas.  

Ao digitar o código, use Ctrl + espaço para disparar o IntelliSense.

### <a name="clean-your-notebook-preview"></a>Limpar o bloco de anotações (visualização)

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

### <a name="save-and-checkpoint-a-notebook"></a>Salvar e ponto de verificação de um notebook

Azure Machine Learning cria um arquivo de ponto de verificação quando você cria um arquivo *ipynb* .

Na barra de ferramentas do bloco de anotações, selecione o menu e, em seguida, **arquivo &gt; salvar e ponto de verificação** para salvar o bloco de anotações manualmente e ele adicionará um arquivo de ponto de verificação associado ao bloco de anotações.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Captura de tela da ferramenta salvar na barra de ferramentas do bloco de anotações":::

Cada bloco de anotações é salvo AutoSalva a cada 30 segundos. O salvamento automático atualiza apenas o arquivo *ipynb* inicial, não o arquivo de ponto de verificação.
 
Selecione **pontos de verificação** no menu do bloco de anotações para criar um ponto de verificação nomeado e reverter o bloco de anotações para um ponto de verificação salvo.

## <a name="delete-a-notebook"></a>Excluir um notebook

Você *não pode* excluir os notebooks de **Exemplos**.  Esses blocos notebooks fazem parte do estúdio e são atualizados sempre que um novo SDK é publicado.  

Você *pode* excluir notebooks de **arquivos de usuário** de qualquer uma destas maneiras:

* No estúdio, selecione o **...** no final de uma pasta ou arquivo.  Use um navegador compatível (Microsoft Edge, Chrome ou Firefox).
* Em qualquer barra de ferramentas do bloco de anotações, selecione [**abrir terminal**](#terminal)  para acessar a janela do terminal da instância de computação.
* No Jupyter ou no JupyterLab com suas ferramentas.

## <a name="run-a-notebook-or-python-script"></a>Executar um script de bloco de anotações ou Python

Para executar um bloco de anotações ou um script Python, primeiro você se conecta a uma [instância de computação](concept-compute-instance.md)em execução. Se você não tiver uma instância de computação, use estas etapas para criar uma: 

1. Selecione **+** na barra de ferramentas do bloco de anotações ou script. 
2. Dê um nome a Computação e escolha um **Tamanho da Máquina Virtual**. 
3. Selecione **Criar**.
4. A instância de computação é conectada ao arquivo automaticamente.  Agora você pode executar as células do bloco de anotações ou o script Python usando a ferramenta à esquerda da instância de computação

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

### <a name="add-new-kernels"></a>Adicionar novos kernels

O notebook localizará automaticamente todos os kernels do Jupyter instalados na instância de computação conectada.  Para adicionar um kernel à instância de computação:

1. Selecione [**abrir terminal**](#terminal) na barra de ferramentas do bloco de anotações.
1. Use a janela do terminal para criar um ambiente.  Por exemplo, o código a seguir cria `newenv` :
    ```shell
    conda create -y --name newenv
    ```
1. Ative o ambiente.  Por exemplo, depois de criar `newenv`:

    ```shell
    conda activate newenv
    ```
1. Instalar o pacote Pip e ipykernel no novo ambiente e criar um kernel para esse Conda env

    ```shell
    conda install -y pip
    conda install -y ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

> [!NOTE]
> Para o gerenciamento de pacotes em um notebook, use as funções mágicas **% Pip** ou **% Conda** para instalar pacotes automaticamente no **kernel em execução no momento**, em vez de **! Pip** ou **! Conda** que se refere a todos os pacotes (incluindo pacotes fora do kernel em execução no momento)

Qualquer um dos [Kernels do Jupyter disponíveis](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) pode ser instalado.

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

## <a name="shortcut-keys"></a>Teclas de atalho
Semelhante ao Jupyter notebooks, os notebooks Azure Machine Learning Studio têm uma interface de usuário modal. O teclado faz coisas diferentes, dependendo de em qual modo a célula do notebook está. Azure Machine Learning Studio notebooks dão suporte aos dois modos a seguir para uma determinada célula de código: modo de comando e modo de edição.

### <a name="command-mode-shortcuts"></a>Atalhos do modo de comando

Uma célula está no modo de comando quando não há um cursor de texto solicitando que você digite. Quando uma célula está no modo de comando, você pode editar o notebook como um todo, mas não pode digitar em células individuais. Insira o modo de comando pressionando `ESC` ou usando o mouse para selecionar fora da área do editor de uma célula.  A borda esquerda da célula ativa é azul e sólida e seu botão de **execução** é azul.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/command-mode.png" alt-text="Célula do bloco de anotações no modo de comando ":::

| Atalho                      | Descrição                          |
| ----------------------------- | ------------------------------------|
| Digite                         | Entrar no modo de edição             |        
| Shift + Enter                 | Executar célula, selecione abaixo         |     
| Controle/comando + Enter       | Executar célula                            |
| Alt + Enter                   | Célula de execução, Inserir célula de código abaixo    |
| Controle/comando + Alt + Enter | Executar célula, Inserir célula de redução abaixo|
| Alt + R                       | Executar tudo      |                       
| S                             | Converter célula em código    |                         
| M                             | Converter célula em redução  |                       
| Para cima/K                          | Selecionar célula acima    |               
| Para baixo/J                        | Selecionar célula abaixo    |               
| Um                             | Inserir célula de código acima  |            
| B                             | Inserir célula de código abaixo   |           
| Controle/Comando + Shift + A   | Inserir célula de redução acima    |      
| Controle/Comando + Shift + B   | Inserir célula de redução abaixo   |       
| X                             | Recortar célula selecionada    |               
| C                             | Copiar célula selecionada   |               
| Shift + V                     | Colar célula selecionada acima           |
| V                             | Colar célula selecionada abaixo    |       
| D D                           | Excluir célula selecionada|                
| O                             | Ativar/Desativar Saída         |              
| Shift + O                     | Alternar a rolagem de saída   |          
| Eu                           | Kernel de interrupção |                   
| 0 0                           | Reiniciar kernel |                     
| Shift + espaço                 | Rolar para cima  |                         
| Space                         | Rolar para baixo|
| Tab                           | Alterar o foco para o próximo item de foco (quando a captura de guias estiver desabilitada)|
| Controle/comando + S           | Salvar bloco de anotações |                      
| 1                             | Alterar para H1|                       
| 2                             | Alterar para H2|                        
| 3                             | Alterar para H3|                        
| 4                             | Alterar para H4 |                       
| 5                             | Alterar para H5 |                       
| 6                             | Alterar para H6 |                       

### <a name="edit-mode-shortcuts"></a>Atalhos do modo de edição

O modo de edição é indicado por um cursor de texto solicitando que você digite na área do editor. Quando uma célula está no modo de edição, você pode digitar na célula. Insira o modo de edição pressionando `Enter` ou usando o mouse para selecionar na área do editor de uma célula. A borda esquerda da célula ativa é verde e hachurada e seu botão de **execução** está verde. Você também verá o prompt do cursor na célula no modo de edição.

   :::image type="content" source="media/how-to-run-jupyter-notebooks/edit-mode.png" alt-text="Célula do bloco de anotações no modo de edição":::

Usando os atalhos de tecla a seguir, você pode navegar e executar o código com mais facilidade em blocos de anotações do Azure Machine Learning no modo de edição.

| Atalho                      | Descrição|                                     
| ----------------------------- | ----------------------------------------------- |
| Escape                        | Entrar no modo de comando|  
| Controle/comando + espaço       | Ativar o IntelliSense |
| Shift + Enter                 | Executar célula, selecione abaixo |                         
| Controle/comando + Enter       | Executar célula  |                                      
| Alt + Enter                   | Célula de execução, Inserir célula de código abaixo  |              
| Controle/comando + Alt + Enter | Executar célula, Inserir célula de redução abaixo  |          
| Alt + R                       | Executar todas as células     |                              
| Up                            | Mover o cursor para cima ou para a célula anterior    |             
| Down                          | Mover cursor para baixo ou para a próxima célula |                  
| Controle/comando + S           | Salvar bloco de anotações   |                                
| Controle/comando + up          | Ir para o início da célula   |                             
| Controle/comando + down        | Ir para o final da célula |                                 
| Tab                           | Preenchimento ou recuo de código (se a captura de guias estiver habilitada) |
| Controle/comando + M           | Habilitar/desabilitar interceptação de guia  |                       
| Controle/comando +]           | Recuar |                                         
| Controle/comando + [           | Desfazer recuo  |                                        
| Controle/comando + A           | Selecionar tudo|                                      
| Controle/comando + Z           | Desfazer |                                           
| Controle/Comando + Shift + Z   | Refazer |                                           
| Controle/comando + Y           | Refazer |                                           
| Controle/comando + página inicial        | Ir para o início da célula|                                
| Controle/comando + fim         | Ir para o final da célula   |                               
| Controle/comando + esquerda        | Ir uma palavra para a esquerda |                               
| Controle/comando + à direita       | Ir uma palavra para a direita |                              
| Controle/comando + Backspace   | Excluir palavra anterior |                             
| Controle/comando + excluir      | Excluir palavra seguinte |                              
| Controle/comando +/           | Ativar/desativar comentário em cu

## <a name="find-compute-details"></a>Localizar detalhes da computação

Encontre detalhes sobre suas instâncias de computação na página **Computação** no [Studio](https://ml.azure.com).

## <a name="troubleshooting"></a>Solução de problemas

* Se você não puder se conectar a um bloco de anotações, verifique se a comunicação de soquete da Web **não** está desabilitada. Para a funcionalidade de Jupyter da instância de computação funcionar, a comunicação de soquete da Web deve ser habilitada. Verifique se a sua rede permite conexões WebSocket com *. instances.azureml.net e *. instances.azureml.ms. 

* Quando a instância de computação é implantada em um espaço de trabalho de link privado, ela só pode ser acessada de dentro da rede virtual. Se você estiver usando o arquivo DNS ou hosts personalizado, adicione uma entrada para <nome da instância>. <region> . instances.azureml.ms com endereço IP privado do ponto de extremidade particular do espaço de trabalho. Para obter mais informações, consulte o artigo [DNS personalizado](https://docs.microsoft.com/azure/machine-learning/how-to-custom-dns?tabs=azure-cli) .
    
## <a name="next-steps"></a>Próximas etapas

* [Executar seu primeiro experimento](tutorial-1st-experiment-sdk-train.md)
* [Fazer backup do armazenamento de arquivos com instantâneos](../storage/files/storage-snapshots-files.md)
* [Trabalhando em ambientes seguros](https://docs.microsoft.com/azure/machine-learning/how-to-secure-training-vnet#compute-instance)
