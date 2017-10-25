---
title: "Artigo de início rápido para Ferramentas do Visual Studio Code para o Machine Learning no Azure | Microsoft Docs"
description: "Este artigo descreve como começar a usar as ferramentas do Visual Studio Code para o Machine Learning, desde a criação de um experimento e o treinamento de um modelo até a operacionalização de um serviço Web."
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: get-started-article
ms.date: 09/12/2017
ms.openlocfilehash: 680c1afab1af31cfef51b1c82d2db49f452ba6ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="visual-studio-code-tools-for-ai"></a>Ferramentas do Visual Studio Code para IA
Ferramentas do Visual Studio para IA é uma extensão de desenvolvimento para criar, testar e implantar soluções de IA e de aprendizagem profunda. Ele apresenta uma integração perfeita com o Azure Machine Learning, especialmente uma exibição de histórico de execução, detalhando o desempenho de métricas personalizadas e treinamentos anteriores. Ele oferece uma exibição de gerenciador de amostras, que permite procurar e inicializar um novo projeto com o [Kit de Ferramentas Cognitivas Microsoft (anteriormente conhecido como CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org) e outras estruturas de aprendizagem profunda. Por fim, ele fornece um gerenciador para destinos de computação, o que habilita você a enviar trabalhos para treinar modelos em ambientes remotos como Máquinas Virtuais do Azure ou servidores Linux com GPU. 
 
## <a name="getting-started"></a>Introdução 
Para começar, será necessário baixar e instalar o [Visual Studio Code](https://code.visualstudio.com/Download). Depois que você tiver aberto o Visual Studio Code, siga as etapas a seguir:
1. Clique no ícone de extensão na barra de atividade. 
2. Pesquise por "Ferramentas do Visual Studio Code para IA". 
3. Clique no botão **Instalar**. 
4. Após a instalação, clique no botão **Recarregar**. 

Depois que o Visual Studio Code for recarregado, a extensão será ativada. [Saiba mais sobre como instalar extensões](https://code.visualstudio.com/docs/editor/extension-gallery).

## <a name="exploring-project-samples"></a>Explorando amostras de projeto
As Ferramentas do Visual Studio Code para IA acompanham um gerenciador de amostras. O gerenciador de amostras torna fácil descobrir amostras e testá-las com apenas alguns cliques. Para abrir o gerenciador, faça o seguinte:   
1. Abra a paleta de comandos (Exibir > **Paleta de Comandos** ou **Ctrl + Shift + P**).
2. Insira "Amostra de IA". 
3. Você obterá uma recomendação para "IA: abrir gerenciador de amostras do Azure ML", selecione-a e pressione enter. 

Como alternativa, você pode clicar no ícone do gerenciador de amostras.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Criar um novo projeto do gerenciador de amostras 
Você pode procurar diferentes amostras e obter mais informações sobre elas. Procuraremos a amostra "Classificando a Íris" até localizá-la. Para criar um novo projeto com base nessa amostra, faça o seguinte:
1. Clique no botão instalar na amostra de projeto, observe os comandos que estão sendo solicitados, guiando você pelas etapas de criação de um novo projeto. 
2. Escolha um nome para o projeto, por exemplo, "Íris".
3. Escolha um caminho de pasta para criar o projeto e pressione enter. 
4. Selecione um espaço de trabalho existente e pressione enter.

O projeto será criado.

> [!TIP]
> Você precisará estar conectado para acessar o recurso do Azure. Do terminal inserido, digite "az login" e siga as instruções. 

## <a name="submitting-experiment-with-the-new-project"></a>Enviando um experimento com o novo projeto
Se o novo projeto está aberto no Visual Studio Code, podemos enviar um trabalho para o nosso destino de computação diferente (local e VM com o Docker).
As Ferramentas do Visual Studio Code para IA fornecem várias maneiras de enviar um experimento. 
1. Menu de contexto (clique com o botão direito do mouse) – **IA: Enviar Trabalho**.
2. Na paleta de comandos: "IA: Enviar trabalho".
3. Alternativamente, você pode executar o comando diretamente usando a CLI do Azure, os Comandos do Machine Learning ou o terminal inserido.

Abra iris_sklearn.py, clique com o botão direito do mouse e selecione **IA: Enviar Trabalho**.
1. Selecione a plataforma "Azure Machine Learning".
2. Selecione a configuração de execução: "Docker-Python."

> [!NOTE]
> Se for a primeira vez que você enviar um trabalho, você receberá uma mensagem "Nenhuma configuração de Machine Learning encontrada, criando...". Um arquivo JSON é aberto, salve-o (**Ctrl + S**).

Depois que o trabalho é enviado, o terminal inserido exibe o progresso das execuções. 

## <a name="view-list-of-jobs"></a>Exibir lista de trabalhos
Depois que os trabalhos forem enviados, você poderá listá-los do histórico de execuções.
1. Abra a paleta de comandos (Exibir > **Paleta de Comandos** ou **Ctrl + Shift + P**).
2. Digite "Lista de IA."
3. Você receberá uma recomendação para "IA: Listar Trabalhos," selecione-a e pressione enter.
4. Selecione a plataforma "Azure Machine Learning".

A exibição Lista de Trabalhos é aberta e mostra todas as execuções e algumas informações relacionadas.

## <a name="view-job-details"></a>Exibir detalhes do trabalho
Com a exibição Lista de Trabalhos ainda aberta, clique na primeira execução na lista.
Para aprofundar-se nos resultados de um trabalho, clique na **ID do trabalho** na parte superior para ver informações detalhadas. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Como configurar o Azure Machine Learning para funcionar com um IDE](./how-to-configure-your-IDE.md)
