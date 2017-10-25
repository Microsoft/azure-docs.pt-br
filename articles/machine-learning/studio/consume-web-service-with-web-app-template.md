---
title: "Consumir um serviço Web do Machine Learning com um modelo de aplicativo Web | Microsoft Docs"
description: "Use um modelo de aplicativo Web no Azure Marketplace para consumir um serviço Web preditivo no Azure Machine Learning."
keywords: "serviço Web, operacionalização, API REST, aprendizado de máquina"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: e0d71683-61b9-4675-8df5-09ddc2f0d92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye;raymondl
ms.openlocfilehash: 1c182403409966923440f359cb2514af7b7df9f3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="consume-an-azure-machine-learning-web-service-with-a-web-app-template"></a>Consumir um serviço Web de Azure Machine Learning com um modelo de aplicativo Web

Uma vez você desenvolveu o modelo de previsão e o implantou como um serviço Web do Azure usando o Machine Learning Studio ou usando ferramentas como R ou Python, você pode acessar o modelo operacionalizado usando uma API REST.

Há várias maneiras para consumir a API REST e acessar o serviço Web. Por exemplo, é possível escrever um aplicativo em C#, R ou Python usando o código de exemplo gerado para você quando o serviço Web foi implantado (disponível no [Portal de Serviços Web do Machine Learning](https://services.azureml.net/quickstart) ou no painel de serviço Web do Machine Learning Studio). Se preferir, é possível usar a pasta de trabalho de exemplo do Microsoft Excel criada para você ao mesmo tempo.

Mas a maneira mais rápida e fácil de acessar o serviço Web é por meio dos modelos de aplicativos Web disponíveis no [Marketplace de Aplicativos Web do Azure](https://azure.microsoft.com/marketplace/web-applications/all/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="the-azure-machine-learning-web-app-templates"></a>Os Modelos de Aplicativos Web do Azure Machine Learning
Os modelos de aplicativo Web disponíveis no Azure Marketplace podem compilar um aplicativo Web personalizado que conhece os dados de entrada do seu serviço Web e os resultados esperados. Basta fornecer ao aplicativo Web o acesso ao serviço e aos dados da Web para que o modelo faça o resto.

Dois modelos estão disponíveis:

* [Modelo do Aplicativo Web do Serviço de Solicitação-Resposta do Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
* [Modelo do aplicativo Web do Serviço de Execução em Lote do Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Cada modelo cria um aplicativo ASP.NET de exemplo, usando o URI da API e a Chave para o serviço Web, e é implantado como um site no Azure. O modelo RRS (Serviço de Solicitação-Resposta) cria um aplicativo Web que permite que você envie uma única linha de dados para o serviço Web para obter um único resultado. O modelo BES (Serviço de Execução em Lote) cria um aplicativo Web que permite enviar várias linhas de dados para obter vários resultados.

Nenhuma codificação é necessária para usar esses modelos. Basta fornecer a Chave de API e o URI, e o modelo criará o aplicativo para você.

Para obter a chave de API e o URI de Solicitação de um serviço Web:

1. No [Portal de Serviços Web](https://services.azureml.net/quickstart), em um Novo serviço Web, clique em **Serviços Web** na parte superior. Ou para gerenciar um serviço Web Clássico, clique em **Serviços Web Clássicos**.
2. Clique no serviço Web que você deseja acessar.
3. Em um serviço Web Clássico, clique no ponto de extremidade que você deseja acessar.
4. Clique em **Consumir** na parte superior.
5. Copie a **Chave Primária** ou a **Secundária** e salve-a.
6. Se estiver criando um modelo RRS (Serviço de Solicitação-Resposta), copie o URI **Solicitação-Resposta** e salve-o. Se estiver criando um modelo BES (Serviço de Execução em Lotes), copie o URI **Solicitações em Lote** e salve-o.


## <a name="how-to-use-the-request-response-service-rrs-template"></a>Como usar o modelo RRS (Serviço de Solicitação-Resposta)
Siga estas etapas para usar o modelo de aplicativo Web RRS, conforme mostrado no diagrama a seguir.

![Processo para usar o modelo da Web RRS][image1]


<!--    ![API Key][image3] -->

<!-- This value will look like this:
   
        https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true
   
    ![Request URI][image4] -->

1. Acesse o [portal do Azure](https://portal.azure.com), faça **Logon**, clique em **Novo**, pesquise e selecione **Aplicativo Web de Serviço de Solicitação-Resposta do Azure ML** e clique em **Criar**. 
   
   * Dê um nome exclusivo ao seu aplicativo Web. A URL do aplicativo Web será esse nome seguido de `.azurewebsites.net.` Por exemplo, `http://carprediction.azurewebsites.net.`
   * Selecione a assinatura do Azure e os serviços nos quais seu serviço Web está sendo executado.
   * Clique em **Criar**.
     
     ![Criar Aplicativo Web][image5]

4. Quando o Azure concluir a implantação do aplicativo Web, clique na **URL** na página de configurações do aplicativo Web no Azure, ou insira a URL em um navegador da Web. Por exemplo, `http://carprediction.azurewebsites.net.`
5. Quando o aplicativo Web é executado pela primeira vez, ele solicita a **URL de Postagem da API** e a **Chave de API**.
   Insira os valores salvos anteriormente (**URI de Solicitação** e **Chave de API**, respectivamente).
     
     Clique em **Enviar**.
     
     ![Insira o URI de Postagem e a Chave de API][image6]

6. O aplicativo Web exibe sua página **Configuração de Aplicativo Web** com as configurações atuais do serviço Web. Aqui você pode alterar as configurações usadas pelo aplicativo Web.
   
   > [!NOTE]
   > Alterar as configurações aqui apenas as altera para este aplicativo Web. Ele não altera as configurações padrão do serviço Web. Por exemplo, se você alterar a **Descrição** aqui, isso não alterará a descrição mostrada no painel de serviço Web no Machine Learning Studio.
   > 
   > 
   
    Quando terminar, clique em **Salvar Alterações** e clique em **Ir para a Página Inicial**.

7. Na home page, é possível inserir valores a serem enviados para o serviço Web. Clique em **Enviar** quando terminar e o resultado será retornado.

Para retornar à página **Configuração**, vá para a página `setting.aspx` do aplicativo Web. Por exemplo: `http://carprediction.azurewebsites.net/setting.aspx.` Você será solicitado a digitar a chave de API novamente – você precisa dela para acessar a página e atualizar as configurações.

Você pode parar, reiniciar ou excluir o aplicativo Web no portal do Azure como qualquer outro aplicativo Web. Enquanto ele estiver em execução, você poderá navegar até o endereço web da página inicial e inserir novos valores.

## <a name="how-to-use-the-batch-execution-service-bes-template"></a>Como usar o modelo BES (Serviço de Execução em Lote)
É possível usar o modelo de aplicativo Web BES da mesma forma que o modelo RRS, exceto que o aplicativo Web criado permitirá que você envie várias linhas de dados e receba vários resultados.

Os valores de entrada de um serviço Web de execução em lotes podem ser provenientes do armazenamento do Azure ou de um arquivo local; os resultados são armazenados em um contêiner de armazenamento do Azure.
Assim, você precisará de um contêiner de armazenamento do Azure para armazenar os resultados retornados pelo aplicativo Web e precisará preparar os dados de entrada.

![Processo para usar o modelo da Web BES][image2]

1. Siga o mesmo procedimento para criar o aplicativo Web BES para o modelo RRS, exceto pelo fato de que você deverá acessar [Modelo de Aplicativo Web de Serviço de Execução em Lotes do Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/) para abrir o modelo BES no Azure Marketplace e clicar em **Criar Aplicativo Web**.

2. Para especificar onde você deseja que os resultados sejam armazenados, insira as informações do contêiner de destino na home page do aplicativo Web. Especifique também onde o aplicativo Web pode obter os valores de entrada, em um arquivo local ou em um contêiner de armazenamento do Azure.
   Clique em **Enviar**.
   
    ![Informações de armazenamento][image7]

O aplicativo Web exibirá uma página com o status do trabalho.
Quando tiver concluído o trabalho, você receberá o local dos resultados no armazenamento de blob do Azure. Você também tem a opção de baixar os resultados em um arquivo local.

## <a name="for-more-information"></a>Para obter mais informações
Para saber mais sobre...

* como criar uma experiência de aprendizado de máquina com o Machine Learning Studio, veja [Criar seu primeiro teste no Azure Machine Learning Studio](create-experiment.md)
* como implantar sua experiência de aprendizado de máquina como um serviço Web, veja [Implantar um serviço Web de Azure Machine Learning](publish-a-machine-learning-web-service.md)
* outras maneiras de acessar o serviço Web, veja [Como consumir um serviço Web do Azure Machine Learning](consume-web-services.md)

[image1]: media/consume-web-service-with-web-app-template/rrs-web-template-flow.png
[image2]: media/consume-web-service-with-web-app-template/bes-web-template-flow.png
[image3]: media/consume-web-service-with-web-app-template/api-key.png
[image4]: media/consume-web-service-with-web-app-template/post-uri.png
[image5]: media/consume-web-service-with-web-app-template/create-web-app.png
[image6]: media/consume-web-service-with-web-app-template/web-service-info.png
[image7]: media/consume-web-service-with-web-app-template/storage.png
