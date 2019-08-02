---
title: Treinar e implantar um modelo – Machine Learning no Azure IoT Edge | Microsoft Docs
description: Treine um modelo de machine learning usando o Azure Machine Learning e, em seguida, empacote o modelo como uma imagem de contêiner que pode ser implantada como um módulo do Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: acf0b1984eb3e68858be6ed68731612448e672f4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432703"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Tutorial: Treinar e implantar um modelo do Azure Machine Learning

> [!NOTE]
> Este artigo faz parte de uma série para um tutorial sobre como usar o Azure Machine Learning no IoT Edge. Se você acessou este artigo diretamente, incentivamos você a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para ter os melhores resultados.

Neste artigo, usamos o Azure Notebooks primeiro para treinar um modelo de machine learning usando o Azure Machine Learning e, em seguida, empacotamos o modelo como uma imagem de contêiner que pode ser implantada como um módulo do Azure IoT Edge. O Azure Notebooks tira proveito de um workspace do Serviço do Azure Machine Learning, que é um bloco básico usado para testar, treinar e implantar modelos de machine learning.

As atividades nesta parte do tutorial são divididas em dois notebooks.

* **01-turbofan\_regression.ipynb:** Este notebook percorre as etapas para treinar e publicar um modelo usando o Azure Machine Learning. De modo geral, as etapas envolvidas são:

  1. Baixar, preparar e explorar os dados de treinamento
  2. Usar o workspace do serviço para criar e executar um experimento de aprendizado de máquina
  3. Avaliar os resultados do modelo do experimento
  4. Publicar o melhor modelo no workspace do serviço

* **02-turbofan\_deploy\_model.ipynb:** Este notebook usa o modelo criado no notebook anterior e o utiliza para criar uma imagem de contêiner pronta para ser implantada em um dispositivo do Azure IoT Edge.

  1. Criar um script de pontuação para o modelo
  2. Criar e publicar a imagem
  3. Implantar a imagem como um serviço Web na Instância de Contêiner do Azure
  4. Usar o serviço Web para validar se o modelo e a imagem funcionam conforme o esperado

As etapas neste artigo normalmente são executadas por cientistas de dados.

## <a name="set-up-azure-notebooks"></a>Configurar o Azure Notebooks

Usamos o Azure Notebooks para hospedar os dois Jupyter Notebooks e os arquivos de suporte. Aqui, criamos e configuramos um projeto do Azure Notebooks. Se você ainda não tiver usado o Jupyter e/ou o Azure Notebooks, veja alguns documentos de introdução:

* **Início Rápido:** [Criar e compartilhar um notebook](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Tutorial:** [criar e executar um Jupyter Notebook com Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Assim como ocorreu com a máquina virtual de desenvolvedor antes, usar o Azure Notebooks garante um ambiente uniforme para o exercício.

> [!NOTE]
> Após configurado, o serviço do Azure Notebooks pode ser acessado de qualquer computador. Durante a configuração, você deve usar a máquina virtual de desenvolvimento, que tem todos os arquivos que serão necessários.

### <a name="create-an-azure-notebooks-account"></a>Criar uma conta do Azure Notebooks

Contas do Azure Notebooks são independentes das assinaturas do Azure. Para usar o Azure Notebooks, você precisará criar uma conta.

1. Navegue até o [Azure Notebooks](https://notebooks.azure.com).

2. Clique em **Entrar** no canto superior direito da página.

3. Entre usando sua conta corporativa ou de estudante (Azure Active Directory) ou sua conta pessoal (conta Microsoft).

4. Se nunca tiver usado o Azure Notebooks, você precisará permitir acesso ao aplicativo do Azure Notebooks.

5. Crie uma ID de usuário para o Azure Notebooks.

### <a name="upload-jupyter-notebooks-files"></a>Fazer upload de arquivos do Jupyter Notebooks

Nesta etapa, criamos um novo projeto do Azure Notebooks e fazemos upload de arquivos nele. Especificamente, os arquivos que carregamos são:

* **01-turbofan\_regression.ipynb**: arquivo do Jupyter Notebook que percorre o processo de baixar os dados gerados pelo dispositivo da conta de armazenamento do Azure; explorar e preparar os dados para treinar o classificador; treinar o modelo; testar os dados usando o conjunto de dados de teste encontrado no arquivo Test\_FD003.txt e, por fim, salvar o modelo do classificador no workspace do serviço do Machine Learning.

* **02-turbofan\_deploy\_model.ipynb:** Jupyter Notebook que orienta quanto ao processo de usar o modelo de classificador salvo no workspace do serviço do Machine Learning para produzir uma imagem de contêiner. Após a imagem ser criada, o notebook orienta você no processo de implantação da imagem como um serviço Web para que você possa validar que ele está funcionando conforme o esperado. A imagem validada será implantada em nosso dispositivo do Edge na parte [Criar e implantar módulos personalizados do IoT Edge](tutorial-machine-learning-edge-06-custom-modules.md) deste tutorial.

* **Test\_FD003.txt:** este arquivo contém os dados que usaremos como nosso conjunto de teste para validar nosso classificador treinado. Optamos por usar os dados de teste conforme fornecidos para o concurso original como nosso conjunto de teste para manter a simplicidade do exemplo.

* **RUL\_FD003.txt:** este arquivo contém a RUL do último ciclo de cada dispositivo no arquivo Test\_FD003.txt. Confira os arquivos **readme.txt** e **Damage Propagation Modeling.pdf** em C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan para ver uma explicação detalhada dos dados.

* **Utils.py:** contém um conjunto de funções de utilitário de Python para trabalhar com os dados. O primeiro notebook contém uma explicação detalhada das funções.

* **README.md:** arquivo Leiame que descreve o uso de notebooks.

Criar um novo projeto e fazer upload dos arquivos no notebook.

1. Selecione **Meus Projetos** na barra de menus superior.

1. Selecione **+ Novo Projeto**. Forneça um nome e uma ID. Não é necessário que o projeto seja público ou tenha um arquivo leiame.

1. Selecione **Upload** e escolha **Do Computador**.

1. Selecione **Escolher arquivos**.

1. Navegue até **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Selecione todos os arquivos e clique em **Abrir**.

1. Selecione **Upload** para começar a carregar e, em seguida, selecione **Concluído** quando o processo for concluído.

## <a name="run-azure-notebooks"></a>Executar Azure Notebooks

Agora que o projeto foi criado, execute o notebook **01-turbofan\_regression.ipynb**.

1. Na página do projeto turbofan, selecione **01-turbofan\_regression.ipynb**.

    ![Selecione o primeiro notebook a ser executado](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. Se solicitado, escolha o Kernel Python 3.6 na caixa de diálogo e selecione **Definir Kernel**.

3. Se o notebook estiver listado como **Não Confiável**, clique no widget **Não Confiável** no canto superior direito do notebook. Quando a caixa de diálogo for exibida, selecione **Confiar**.

4. Siga as instruções no notebook.

    * `Ctrl + Enter` executa uma célula.
    * `Shift + Enter` executa uma célula e navega para a próxima célula.
    * Quando uma célula está em execução, ela tem um asterisco entre colchetes, como **[\*]** . Quando ela for concluída, o asterisco será substituído por um número e a saída relevante poderá aparecer abaixo. Uma vez que as células costumam se basear no trabalho de células anteriores, somente uma célula pode ser executada por vez.

5. Quando terminar de executar o notebook **01-turbofan\_regression.ipynb**, volte para a página do projeto.

6. Abra **02-turbofan\_deploy\_model.ipynb** e repita as etapas nesta seção para executar o segundo notebook.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, usamos dois Jupyter Notebooks em execução no Azure Notebooks para usar os dados dos dispositivos turbofan para treinar um classificador de RUL (vida útil restante), para salvar o classificador como um modelo, para criar uma imagem de contêiner e para implantar e testar a imagem como um serviço Web.

Prossiga para o próximo artigo para criar um dispositivo IoT Edge.

> [!div class="nextstepaction"]
> [Configurar um dispositivo IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
