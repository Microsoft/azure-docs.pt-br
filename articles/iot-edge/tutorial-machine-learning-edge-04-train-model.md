---
title: 'Tutorial: Treinar e implantar um modelo – Machine Learning no Azure IoT Edge'
description: Neste tutorial, você treinará um modelo de machine learning usando o Azure Machine Learning e, em seguida, empacotará o modelo como uma imagem de contêiner que poderá ser implantada como um módulo do Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2cc96db88d9a2aec02de5e2fc4ed18b445972e7b
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121117"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Tutorial: Treinar e implantar um modelo do Azure Machine Learning

Neste artigo, executaremos as seguintes tarefas:

* Use o Estúdio do Azure Machine Learning para treinar um modelo de machine learning.
* Empacotar o modelo treinado como uma imagem de contêiner.
* Implantar a imagem de contêiner como um módulo do Azure IoT Edge.

O Estúdio do Azure Machine Learning é um bloco básico usado para experimentar, treinar e implantar modelos de machine learning.

As etapas neste artigo normalmente são executadas por cientistas de dados.

Nesta seção do tutorial, você aprenderá a:

> [!div class="checklist"]
> * Crie Jupyter Notebooks no Workspace do Azure Machine Learning para treinar um modelo de machine learning.
> * Colocar o modelo de machine learning treinado em um contêiner.
> * Criar um módulo do Azure IoT Edge com base no modelo de machine learning conteinerizado.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo faz parte de uma série para um tutorial sobre como usar o Azure Machine Learning no IoT Edge. Cada artigo da série se baseia no trabalho do artigo anterior. Se você chegou a este artigo diretamente, acesse o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série.

## <a name="set-up-azure-machine-learning"></a>Configurar o Azure Machine Learning 

Usamos o Estúdio do Azure Machine Learning para hospedar os dois Jupyter Notebooks e arquivos de suporte. Aqui, criamos e configuramos um projeto do Azure Machine Learning. Se você não usou o Jupyter e/ou Estúdio do Azure Machine Learning, aqui estão alguns documentos de introdução:

* **Jupyter Notebooks:** [Como trabalhar com Jupyter Notebooks no Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning:** [Introdução ao Azure Machine Learning em Jupyter Notebooks](../machine-learning/tutorial-1st-experiment-sdk-setup.md)


> [!NOTE]
> Uma vez configurado, o Serviço do Azure Machine Learning pode ser acessado em qualquer computador. Durante a configuração, você deve usar a VM de desenvolvimento, que tem todos os arquivos que serão necessários.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Instalar a extensão do Azure Machine Learning para Visual Studio Code
O VS Code na VM de desenvolvimento deve ter essa extensão instalada. Se você estiver executando em uma instância diferente, reinstale a extensão conforme descrito [aqui.](../machine-learning/tutorial-setup-vscode-extension.md)

### <a name="create-an-azure-machine-learning-account"></a>Criar uma conta do Azure Machine Learning  
Para provisionar recursos e executar cargas de trabalho no Azure, você precisa entrar com suas credenciais de conta do Azure.

1. No Visual Studio Code, abra a paleta de comandos selecionando **Exibição** > **Paleta de Comandos** na barra de menus. 

1. Insira o comando `Azure: Sign In` na paleta de comandos para iniciar o processo de entrada. Siga as instruções para concluir a entrada. 

1. Crie uma instância de Computação do ML do Azure para executar sua carga de trabalho. Usando a paleta de comando, insira o comando `Azure ML: Create Compute`. 
1. Selecionar sua Assinatura do Azure
1. Selecione **+ Criar Workspace do Azure ML** e insira o nome `turbofandemo`.
1. Selecione o grupo de recursos que você está usando para esta demonstração.
1. Você deve ser capaz de ver o progresso da criação do workspace no canto inferior direito da janela do VS Code: **Criando workspace: turobofandemo** (isso pode levar um ou dois minutos). 
1. Aguarde até que o workspace seja criado com êxito. Isso deve indicar **Workspace do Azure ML turbofandemo criado**.


### <a name="upload-jupyter-notebook-files"></a>Fazer upload de arquivos do Jupyter Notebook

Vamos carregar arquivos de notebook de exemplo em um novo Workspace do Azure ML.

1. Navegue até ml.azure.com e entre.
1. Selecione seu diretório Microsoft, a assinatura do Azure e o Workspace do Azure ML recém-criado.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Selecione seu Workspace do Azure ML." :::

1. Depois de fazer logon em seu Workspace do Azure ML, navegue até a seção **Notebooks** usando o menu do lado esquerdo.
1. Selecione a guia **Meus arquivos**.

1. Selecione **Carregar** (o ícone de seta para cima) 


1. Navegue até **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecione todos os arquivos na lista e clique em **Abrir**.

1. Marque a caixa **Confiar no conteúdo desses arquivos**.

1. Selecione **Upload** para começar a carregar e, em seguida, selecione **Concluído** quando o processo for concluído.

### <a name="jupyter-notebook-files"></a>Arquivos do Jupyter Notebook

Vamos examinar os arquivos que você carregou em seu projeto do Workspace do Azure ML. As atividades nesta parte do tutorial se estendem por dois arquivos de notebook, que usam alguns arquivos de suporte.

* **01-turbofan\_regression.ipynb:** Este notebook usa o workspace do serviço do Machine Learning para criar e executar um experimento de aprendizado de máquina. Amplamente, o notebook executa as seguintes etapas:

  1. Baixa os dados da conta de Armazenamento do Azure que foi gerada pelo agente do dispositivo.
  1. Explora e prepara os dados e, em seguida, usa os dados para treinar o modelo de classificador.
  1. Avalia o modelo do experimento usando um conjunto de dados de teste (Test\_FD003.txt).
  1. Publica o melhor modelo do classificador no workspace do serviço do Machine Learning.

* **02-turbofan\_deploy\_model.ipynb:** Este notebook usa o modelo criado no notebook anterior e o utiliza para criar uma imagem de contêiner pronta para ser implantada em um dispositivo do Azure IoT Edge. O notebook realiza as seguintes etapas:

  1. Cria um script de pontuação para o modelo.
  1. Produz uma imagem de contêiner usando o modelo de classificador que foi salvo no workspace do serviço do Machine Learning.
  1. Implanta a imagem como um serviço Web na Instância de Contêiner do Azure.
  1. Usar o serviço Web para validar se o modelo e a imagem funcionam conforme o esperado. A imagem validada será implantada em nosso dispositivo do IoT Edge na parte [Criar e implantar módulos personalizados do IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md) deste tutorial.

* **Test\_FD003.txt:** este arquivo contém os dados que usaremos como nosso conjunto de teste para validar nosso classificador treinado. Optamos por usar os dados de teste, conforme fornecidos para o concurso original, como nosso conjunto de teste para manter a simplicidade.

* **RUL\_FD003.txt:** este arquivo contém a RUL (Vida Útil Restante) do último ciclo de cada dispositivo no arquivo Test\_FD003.txt. Confira os arquivos readme.txt e Damage Propagation Modeling.pdf em C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan para ver uma explicação detalhada dos dados.

* **Utils.py:** contém um conjunto de funções de utilitário de Python para trabalhar com os dados. O primeiro notebook contém uma explicação detalhada das funções.

* **README.md:** arquivo Leiame que descreve o uso de notebooks.  

## <a name="run-jupyter-notebooks"></a>Executar Jupyter Notebooks

Agora que o workspace foi criado, você pode executar os notebooks. 

1. Na página **Meus arquivos**, selecione **01-turbofan\_regression.ipynb**.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Selecione o primeiro notebook a ser executado.":::

1. Se o notebook estiver listado como **Não Confiável**, clique no widget **Não Confiável** no canto superior direito do notebook. Quando a caixa de diálogo for exibida, selecione **Confiar**.

1. Para obter os melhores resultados, leia a documentação de cada célula e a execute individualmente. Selecione **Executar** na barra de ferramentas. Mais adiante, você será mais fácil executar várias células. Você pode desconsiderar os avisos de atualização e preterimento.

    Quando uma célula está em execução, ela exibe um asterisco entre colchetes ([\*]). Quando a operação da célula for concluída, o asterisco será substituído por um número e a saída relevante poderá aparecer. As células em um notebook são criadas em sequência e apenas uma pode ser executada de cada vez.

    Você também pode usar as opções de execução no menu **Célula**, `Ctrl` + `Enter` para executar uma célula e `Shift` + `Enter` para executar uma célula e avançar para a próxima célula.

    > [!TIP]
    > Para operações de célula consistentes, evite executar o mesmo notebook em várias guias no navegador.

1. Na célula após as instruções **Definir propriedades globais**, digite os valores para a assinatura, as configurações e os recursos do Azure. Em seguida, execute a célula.

    ![Definir propriedades globais no notebook](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Na célula anterior a **Detalhes do workspace**, depois que ela tiver sido executada, procure o link que orienta você a entrar para se autenticar:

    ![Prompt de entrada para autenticação de dispositivo](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Abra o link e insira o código especificado. Esse procedimento de entrada autentica o notebook Jupyter para acessar os recursos do Azure usando a interface de linha de comando multiplataforma do Microsoft Azure.  

    ![Autenticar aplicativo na confirmação do dispositivo](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Na célula que precede **Explorar os resultados**, copie o valor da ID de execução e cole-a para a ID de execução na célula após **Reconstituir uma execução**.

   ![Copiar a ID de execução entre células](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Execute as células restantes no notebook.

1. Salve o notebook e retorne à sua página do projeto.

1. Abra **02-turbofan\_deploy\_model.ipynb** e execute cada célula. Você precisará entrar para autenticar-se na célula após **Configurar workspace**.

1. Salve o notebook e retorne à sua página do projeto.

### <a name="verify-success"></a>Verificar êxito

Para verificar se os notebooks foram concluídos com êxito, verifique se alguns itens foram criados.

1. Na guia **Meus arquivos** dos Notebooks do Azure ML, selecione **Atualizar**.

1. Verifique se os seguintes arquivos foram criados:

    | Arquivo | Descrição |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Arquivo de configuração usado para criar o Workspace do Azure Machine Learning. |
    | ./aml_config/model_config.json | Arquivo de configuração que precisaremos implantar o modelo no workspace do Machine Learning **turbofanDemo** no Azure. |
    | myenv.yml| Fornece informações sobre as dependências para o modelo de Machine Learning implantado.|

1. Verifique se os seguintes recursos do Azure foram criados. Alguns nomes de recursos são acrescentados com caracteres aleatórios.

    | Recursos do Azure | Nome |
    | --- | --- |
    | Workspace do Machine Learning | turborfanDemo |
    | Registro de Contêiner | turbofandemoxxxxxxxx |
    | Applications Insights | turbofaninsightxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxx |
    | Armazenamento | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Depuração

Você pode inserir instruções do Python no notebook para depuração, como o comando `print()` para mostrar valores. Se você vir variáveis ou objetos que não estão definidos, execute as células em que eles são declarados ou instanciados primeiro.

Talvez seja necessário excluir arquivos e recursos do Azure criados anteriormente se precisar refazer os notebooks.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial faz parte de um conjunto em que cada artigo se baseia no trabalho feito nos anteriores. Aguarde para limpar todos os recursos até concluir o tutorial final.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, usamos dois Jupyter Notebooks em execução no Estúdio do Azure ML para usar os dados dos dispositivos turbofan para treinar um classificador de RUL (vida útil restante), para salvar o classificador como um modelo, para criar uma imagem de contêiner e para implantar e testar a imagem como um serviço Web.

Prossiga para o próximo artigo para criar um dispositivo IoT Edge.

> [!div class="nextstepaction"]
> [Configurar um dispositivo IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)