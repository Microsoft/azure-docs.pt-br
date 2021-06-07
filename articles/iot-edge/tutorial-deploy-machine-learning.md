---
title: Tutorial – Implantar o Azure Machine Learning em um dispositivo usando o Azure IoT Edge
description: Neste tutorial, você cria um modelo do Azure Machine Learning e implanta-o como um módulo em um dispositivo de borda
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: b3192c3ee8458623f3724da5f875cc09032a42bf
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219409"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Tutorial: Implantar o Azure Machine Learning como um módulo do IoT Edge (versão prévia)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Use os Azure Notebooks para desenvolver um módulo de machine learning e para implantá-lo em um dispositivo que executa o Azure IoT Edge com contêineres Linux.
Use os módulos do IoT Edge para implantar um código que implementa a lógica de negócios diretamente em seus dispositivos IoT Edge. Este tutorial mostra todas as etapas necessárias para implantar um módulo do Azure Machine Learning que prevê quando um dispositivo falhará com base nos dados de temperatura do computador simulado. Para saber mais sobre o Azure Machine Learning no IoT Edge, confira a [Documentação do Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

>[!NOTE]
>Os módulos do Azure Machine Learning no Azure IoT Edge são públicos na versão prévia.

O módulo do Azure Machine Learning criado neste tutorial lê os dados ambientais gerados pelo dispositivo e rotula as mensagens como anômalas ou não.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Criar um módulo do Azure Machine Learning.
> * Efetuar push de um contêiner de módulo para um registro de contêiner do Azure.
> * Implantar um módulo do Azure Machine Learning no dispositivo do IoT Edge.
> * Exibir os dados gerados.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Um dispositivo do Azure IoT Edge:

* Você pode usar os inícios rápidos para configurar um [dispositivo Linux](quickstart-linux.md) ou um [dispositivo Windows](quickstart.md).
* O módulo do Azure Machine Learning não dá suporte a contêineres do Windows.
* O módulo do Azure Machine Learning não dá suporte a processadores ARM.

Recursos de nuvem:

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) na camada padrão ou gratuito no Azure.
* Um Workspace do Azure Machine Learning. Siga as instruções em [Usar o portal do Azure para começar com o Azure Machine Learning](../machine-learning/tutorial-1st-experiment-sdk-setup.md) para criar um desses modelos e aprender como usá-lo.
  * Anote o nome do workspace, o grupo de recursos e a ID da assinatura. Esses valores estão disponíveis na visão geral de workspace no portal do Azure. Você usará esses valores mais adiante no tutorial para conectar um arquivo do Azure Notebooks aos recursos do seu workspace.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Criar e implantar um módulo do Azure Machine Learning

Nesta seção, você converte arquivos de modelo de machine learning treinados em um contêiner do Azure Machine Learning. Todos os componentes necessários para a imagem do Docker estão no [repositório Git do Kit de Ferramentas de IA para o Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Siga estas etapas para carregar esse repositório no Microsoft Azure Notebooks para criar o contêiner e envie-o por push ao Registro de Contêiner do Azure.

1. Navegue até seus projetos do Azure Notebooks. Você pode chegar lá do seu espaço de trabalho do Azure Machine Learning no [portal do Azure](https://portal.azure.com) ou entrando em [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) com sua conta do Azure.

2. Selecione **Carregar o Repositório do GitHub**.

3. Forneça o nome do repositório do GitHub a seguir: `Azure/ai-toolkit-iot-edge`. Desmarque a caixa **Público** se você quiser manter seu projeto privado. Selecione **Importar**.

4. Depois que a importação for concluída, navegue para o novo projeto **IA-toolkit-iot-edge** e abra a pasta **tutorial de detecção de anomalias do IoT Edge**.

5. Verifique se o projeto está em execução. Se não estiver, selecione **Executar em Computação Gratuita**.

   ![Executar em computação gratuita](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Abra o arquivo **aml_config/config.json**.

7. Edite o arquivo de configuração para incluir os valores de ID de assinatura do Azure, um grupo de recursos em sua assinatura e o nome do espaço de trabalho do Azure Machine Learning. Você pode obter todos esses valores da seção **Visão geral** do seu workspace no Azure.

8. Salve o arquivo de configuração.

9. Abra o arquivo **00-anomaly-detection-tutorial.ipynb**.

10. Quando solicitado, selecione o kernel **Python 3.6** e, em seguida, selecione **Definir Kernel**.

11. Edite a primeira célula no notebook de acordo com as instruções nos comentários. Use o mesmo grupo de recursos, ID da assinatura e nome do workspace que você adicionou ao arquivo de configuração.

12. Execute as células no notebook selecionando-as e selecionando **Executar** ou pressionando `Shift + Enter`.

    >[!TIP]
    >Algumas das células no notebook do tutorial de detecção de anomalias são opcionais, pois elas criam recursos que alguns usuários podem ou não ter ainda, assim como um Hub IoT. Se colocar suas informações de recursos existentes na primeira célula, você receberá erros se executar as células que criam novos recursos, porque o Azure não criará recursos duplicados. Não há problema nisso e você pode ignorar os erros ou ignorar por completo essas seções opcionais.

Ao concluir todas as etapas no notebook, você treinou um modelo de detecção de anomalias, compilado esse modelo como uma imagem de contêiner do Docker e enviado essa imagem por push para o Registro de Contêiner do Azure. Em seguida, você testou o modelo e, finalmente, implantou-o em seu dispositivo IoT Edge.

## <a name="view-container-repository"></a>Exibir repositório de contêiner

Verifique se a imagem de contêiner foi criada com êxito e armazenada no Registro de Contêiner do Azure associado ao seu ambiente de aprendizado de máquina. O notebook que você usou na seção anterior forneceu automaticamente a imagem de contêiner e as credenciais de registro para seu dispositivo IoT Edge, mas você deve saber onde eles são armazenados para que possa encontrar as informações por conta própria mais tarde.

1. No [portal do Azure](https://portal.azure.com), navegue até o workspace do serviço do Machine Learning.

2. A seção **Visão geral** lista os detalhes do workspace e seus recursos associados. Selecione o valor de **Registro**, que deve ser o nome do workspace seguido de números aleatórios.

3. No registro de contêiner, em **Serviços**, selecione **Repositórios**. Você deve ver um repositório chamado **tempanomalydetection**, que foi criado pelo notebook que você executou na seção anterior.

4. Selecione **tempanomalydetection**. Você deve ver que o repositório tem uma tag: **1**.

   Agora que você sabe o nome do Registro, o nome do repositório e a tag, você sabe o caminho completo da imagem do contêiner. Os caminhos para imagens são semelhantes a **\<registry_name\>.azurecr.io/tempanomalydetection:1**. Você pode usar o caminho da imagem para implantar esse contêiner para dispositivos IoT Edge.

5. No registro de contêiner, em **Configurações**, selecione **Chaves de acesso**. Você deve ver um número de credenciais de acesso, incluindo o **Servidor de logon** e o **Nome de usuário** e a **Senha** de um usuário administrador.

   Essas credenciais podem ser incluídas no manifesto de implantação para permitir ao dispositivo IoT Edge acesso para efetuar pull de imagens de contêiner do Registro.

Agora você sabe onde a imagem de contêiner do Machine Learning é armazenada. A próxima seção mostra o passo a passo das etapas para exibir o contêiner em execução como um módulo em seu dispositivo IoT Edge.

## <a name="view-the-generated-data"></a>Exibir os dados gerados

Você pode exibir mensagens que são geradas por módulo do IoT Edge, e você pode exibir as mensagens que são entregues para o Hub IoT.

### <a name="view-data-on-your-iot-edge-device"></a>Exibir dados no seu dispositivo IoT Edge

No seu dispositivo IoT Edge, você pode exibir as mensagens que são enviadas de cada módulo individual.

Você talvez precise usar `sudo` para permissões elevadas para executar comandos `iotedge`. Sair e entrar novamente no seu dispositivo atualiza automaticamente suas permissões.

1. Exibir todos os módulos no seu dispositivo IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Exibir as mensagens que estão sendo enviadas de um dispositivo específico. Use o nome do módulo da saída do comando anterior.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Exibir dados que chegam ao seu Hub IoT

Você pode exibir as mensagens de dispositivo para nuvem que o hub IoT recebe usando a [extensão do Hub IoT do Azure para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

As etapas a seguir mostram como configurar o Visual Studio Code para monitorar mensagens de dispositivo para a nuvem que chegam em seu Hub IoT.

1. No gerenciador do Visual Studio Code, na seção **Hub IoT do Azure**, expanda **Dispositivos** para ver sua lista de dispositivos IoT.

2. Clique com o botão direito do mouse no nome do dispositivo do IoT Edge e selecione **Iniciar Monitoramento de Ponto de Extremidade de Evento Interno**.

3. Observe as mensagens recebidas do tempSensor a cada cinco segundos. O corpo da mensagem contém uma propriedade chamada **anomaly**, que o machinelearningmodule fornece com um valor true ou false. A propriedade **AzureMLResponse** conterá o valor "OK" se o modelo for executado com sucesso.

   ![Resposta do Azure Machine Learning no corpo da mensagem](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Se você pretende continuar no próximo artigo recomendado, pode manter os recursos e as configurações já criados e reutilizá-los. Você também pode continuar usando o mesmo dispositivo IoT Edge como um dispositivo de teste.

Caso contrário, você pode excluir as configurações locais e os recursos do Azure criados neste artigo para evitar encargos.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um módulo do IoT Edge da plataforma Azure Machine Learning. Continue com um dos outros tutoriais para saber mais sobre outras formas pelas quais o Azure IoT Edge pode ajudá-lo a transformar dados em informações de negócios na borda.

> [!div class="nextstepaction"]
> [Classificar imagens com o serviço de Visão Personalizada](tutorial-deploy-custom-vision.md)
