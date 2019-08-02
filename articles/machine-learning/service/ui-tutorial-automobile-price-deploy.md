---
title: 'Tutorial: Implantar um modelo de machine learning com a interface visual'
titleSuffix: Azure Machine Learning service
description: Saiba como criar uma solução de análise preditiva na interface visual do Serviço do Azure Machine Learning. Treine, pontue e implante um modelo de machine learning usando módulos do tipo "arrastar e soltar". Este tutorial é a segunda parte de uma série de duas partes sobre a previsão de preços de automóveis usando a regressão linear.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/11/2019
ms.openlocfilehash: dd28fb51a4fc3fbf3dfc893f2f5f159ccafdb4b3
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839299"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-visual-interface"></a>Tutorial: Implantar um modelo de machine learning com a interface visual

Para que outras pessoas possam usar o modelo preditivo desenvolvido na [parte um do tutorial](ui-tutorial-automobile-price-train-score.md), implante-o como um serviço Web do Azure. Até agora, você fez experiências de treinamento do modelo. Agora é hora de gerar novas previsões com base na entrada do usuário. Nesta parte do tutorial, você:

> [!div class="checklist"]
> * Preparará o modelo para implantação
> * Implantar um serviço Web
> * Testará um serviço Web
> * Gerenciará um serviço Web
> * Consumir o serviço Web

## <a name="prerequisites"></a>Pré-requisitos

Conclua a [parte um do tutorial](ui-tutorial-automobile-price-train-score.md) para aprender a treinar e pontuar um modelo de machine learning na interface visual.

## <a name="prepare-for-deployment"></a>Preparar para a implantação

Antes de implantar seu experimento como um serviço Web, primeiro você precisa converter seu *teste de treinamento* em um *teste de previsão*.

1. Selecione **Criar Teste de Previsão*** na parte inferior da tela do teste.

    ![GIF animado mostrando a conversão automática de um teste de treinamento em um teste preditivo](./media/ui-tutorial-automobile-price-deploy/deploy-web-service.gif)

    Quando você seleciona **Criar Teste de Previsão**, várias coisas acontecem:
    
    * O modelo treinado é armazenado como um módulo de **Modelo Treinado** na paleta de módulos. Você pode encontrá-lo em **Modelos Treinados**.
    * Os módulos que foram usados para o treinamento são removidos; especificamente:
      * Treinar Modelo
      * Dividir Dados
      * Avaliar Modelo
    * O modelo treinado salvo é adicionado de volta ao teste.
    * Os módulos **Entrada de serviço Web** e **Saída de serviço Web** são adicionados. Esses módulos identificam o local em que os dados do usuário serão inseridos no modelo e o local em que os dados são retornados.

    O **teste de treinamento** ainda é salvo nas novas guias na parte superior da tela do teste.

1. **Execute** o teste.

1. Selecione a saída do módulo **Pontuar Modelo** e selecione **Exibir Resultados** para verificar se o modelo ainda está funcionando. Você pode ver que os dados originais são exibidos, juntamente com o preço previsto ("Rótulos Pontuados").

Agora, o teste deverá ser semelhante ao seguinte:  

![Captura de tela mostrando a configuração esperada do teste após sua preparação para implantação](./media/ui-tutorial-automobile-price-deploy/predictive-graph.png)

## <a name="deploy-the-web-service"></a>Implantar o serviço Web

1. Selecione **Implantar Serviço Web** abaixo da tela.

1. Selecione o **Destino de Computação** no qual deseja executar o serviço Web.

    Atualmente, a interface visual só dá suporte à implantação em destinos de computação do AKS (Serviço de Kubernetes do Azure). Escolha um dos destinos de computação do AKS disponíveis em seu workspace do serviço do Machine Learning ou configure um novo ambiente do AKS usando as etapas da caixa de diálogo exibida.

    ![Captura de tela mostrando uma configuração possível para um novo destino de computação](./media/ui-tutorial-automobile-price-deploy/deploy-compute.png)

1. Selecione **Implantar Serviço Web**. Você verá a notificação a seguir quando a implantação for concluída. A implantação poderá levar alguns minutos.

    ![Captura de tela mostrando a mensagem de confirmação para uma implantação bem-sucedida.](./media/ui-tutorial-automobile-price-deploy/deploy-succeed.png)

## <a name="test-the-web-service"></a>Testar o serviço Web

Você pode testar e gerenciar seus serviços Web da interface visual navegando até a guia **Serviços Web**.

1. Acesse a seção do serviço Web. Você verá o serviço Web implantado com o nome **Tutorial – Prever o Preço de Automóveis [Teste Preditivo]** .

     ![Captura de tela mostrando a guia do serviço Web com o serviço Web recém-criado realçado](./media/ui-tutorial-automobile-price-deploy/web-services.png)

1. Selecione o nome do serviço Web para exibir detalhes adicionais.

     ![Captura de tela mostrando os detalhes adicionais disponíveis na exibição do serviço Web](./media/ui-tutorial-automobile-price-deploy/web-service-details.png)

1. Selecione **Testar**.

    ![Captura de tela mostrando a página de teste do serviço Web](./media/ui-tutorial-automobile-price-deploy/web-service-test.png)

1. Insira os dados de teste ou use os dados de exemplo preenchidos automaticamente e selecione **Testar**.

    A solicitação de teste é enviada para o serviço Web e os resultados são mostrados na página. Embora um valor seja gerado para os dados de entrada, ele não é usado para gerar o valor da previsão.

## <a name="consume-the-web-service"></a>Consumir o serviço Web

Agora, os usuários podem enviar solicitações à API para seu serviço Web do Azure e receber resultados para prever o preço de seus novos automóveis.

**Solicitação/resposta** -o usuário envia uma ou mais linhas de dados de automóveis para o serviço usando um protocolo HTTP. O serviço responde com um ou mais conjuntos de resultados.

Encontre as chamadas REST de exemplo na guia **Consumir** da página de detalhes do serviço Web.

   ![Captura de tela mostrando uma chamada REST de exemplo que os usuários podem encontrar na guia Consumir](./media/ui-tutorial-automobile-price-deploy/web-service-consume.png)

Navegue até a guia **Documentação da API** para encontrar mais detalhes sobre a API.

  ![Captura de tela mostrando mais detalhes da API que os usuários podem encontrar na guia Documentação da API](./media/ui-tutorial-automobile-price-deploy/web-service-api.png)

## <a name="manage-models-and-deployments"></a>Gerenciar modelos e implantações

Os modelos e as implantações de serviço Web criados na interface visual também podem ser gerenciados no workspace do Serviço do Azure Machine Learning.

1. Abra seu workspace na [portal do Azure](https://portal.azure.com/).  

1. No workspace, selecione **Modelos**. Em seguida, selecione o teste que você criou.

    ![Captura de tela mostrando como navegar para os testes no portal do Azure](./media/ui-tutorial-automobile-price-deploy/portal-models.png)

    Nessa página, você verá mais detalhes sobre o modelo.

    ![Captura de tela mostrando uma visão geral das estatísticas do teste no portal do Azure](./media/ui-tutorial-automobile-price-deploy/model-details.png)

1. Selecione **Implantações**, e serão listados todos os serviços Web que usam o modelo. Selecione o nome do serviço Web, e ele acessará a página de detalhes do serviço Web. Nessa página, você pode obter informações mais detalhadas do serviço Web.

    ![Captura de tela do relatório de execução detalhado](./media/ui-tutorial-automobile-price-deploy/deployment-details.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu as principais etapas na criação, na implantação e no consumo de um modelo de machine learning na interface visual. Para saber mais sobre como você pode usar a interface visual para resolver outros tipos de problemas, confira os outros testes de exemplo.

> [!div class="nextstepaction"]
> [Amostra de classificação de risco de crédito](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
