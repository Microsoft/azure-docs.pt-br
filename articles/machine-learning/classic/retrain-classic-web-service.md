---
title: 'ML Studio (clássico): treinar novamente o serviço Web clássico-Azure'
description: Saiba como treinar novamente um modelo e atualizar um serviço Web clássico para usar o modelo treinado recentemente no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: peterclu
ms.author: peterlu
ms.custom: seodec18, previous-ms.author=yahajiza, previous-author=YasinMSFT, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: 90c968ee953e80238775639964cb09a25741b33d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517562"
---
# <a name="retrain-and-deploy-a-classic-studio-classic-web-service"></a>Readaptação e implantação de um serviço Web clássico do Studio (clássico)

**aplica-se a:** ![ Marca de seleção verde. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ X indicando não.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Treinar novamente os modelos de machine learning é uma forma de fazer com que eles permaneçam precisos e baseados nos dados mais relevantes disponíveis. Este artigo mostrará como readaptar um serviço Web clássico do Studio (clássico). Para obter um guia sobre como treinar novamente um novo serviço Web de estúdio (clássico), [Veja este artigo de instruções.](retrain-machine-learning-model.md)

## <a name="prerequisites"></a>Pré-requisitos

O artigo pressupõe que você já tenha um teste de novo treinamento e um teste preditivo. As etapas são explicadas em [Treinar novamente e implantar um modelo de machine learning.](./retrain-machine-learning-model.md) No entanto, em vez de implantar seu modelo de machine learning como um novo serviço Web, você implantará o teste preditivo como um serviço Web clássico.
     
## <a name="add-a-new-endpoint"></a>Adicionar um novo ponto de extremidade

O serviço Web preditivo implantado contém um padrão de pontuação de ponto de extremidade que é mantido sincronizado com o treinamento original e com o modelo de treinamento de teste de pontuação. Para atualizar seu serviço Web com um novo modelo de treinamento, você deve criar um novo ponto de extremidade de pontuação.

Há duas maneiras de adicionar um novo ponto de extremidade a um serviço Web:

* Programaticamente
* Usar o portal dos serviços Web do Azure

> [!NOTE]
> Certifique-se de que você está adicionando o ponto de extremidade ao serviço Web de previsão e não ao serviço da Web de treinamento. Se você tiver implantado corretamente um serviço Web de previsão e um serviço da Web de treinamento, você verá dois serviços Web separados listados. O serviço Web de previsão deve terminar com "[predictive exp.]".
>

### <a name="programmatically-add-an-endpoint"></a>Adicionar um ponto de extremidade programaticamente

Você pode adicionar pontos de extremidade de pontuação usando o código de exemplo fornecido neste [repositório GitHub](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint).

### <a name="use-the-azure-web-services-portal-to-add-an-endpoint"></a>Usar o portal dos Serviços Web do Azure para adicionar um ponto de extremidade

1. Em Machine Learning Studio (clássico), na coluna de navegação à esquerda, clique em serviços Web.
1. Na parte inferior do painel do serviço Web, clique em **gerenciar visualização de pontos de extremidade**.
1. Clique em **Adicionar**.
1. Digite um nome e uma descrição para o novo ponto de extremidade. Selecione o nível de log e se os dados de exemplo estão habilitados. Para obter mais informações sobre registro em log, consulte [habilitar o log para serviços web Machine Learning](web-services-logging.md).

## <a name="update-the-added-endpoints-trained-model"></a>Atualizar o modelo treinado do ponto de extremidade adicionado

### <a name="retrieve-patch-url"></a>Recuperar a URL DO PATCH

Siga estas etapas para obter a URL DO PATCH correta usando o portal da Web:

1. Entre no portal [Serviços Web do Azure Machine Learning](https://services.azureml.net/).
1. Clique em **Serviços Web** ou **Serviços Web Clássicos** na parte superior.
1. Clique no serviço Web de pontuação com o qual você está trabalhando (se você não tiver modificado o nome padrão do serviço Web, ele terminará em "[Pontuação Exp.]").
1. Clique em **+ novo**.
1. Depois do ponto de extremidade ser adicionado, clique no nome dele.
1. No URL do **Patch**, clique em **Ajuda da API** para abrir a página de ajuda de aplicação de patch.

> [!NOTE]
> Se você adicionou o ponto de extremidade ao serviço Web de treinamento em vez do serviço Web de previsão, receberá o seguinte erro ao clicar no link **Atualizar recurso** : "Desculpe, mas esse recurso não tem suporte ou está disponível neste contexto. Este serviço Web não tem recursos atualizáveis. Pedimos desculpas pelo inconveniente e estamos trabalhando para melhorar esse fluxo de trabalho.”
>

A página de ajuda do PATCH contém a URL do PATCH que você deve usar e fornece o código de exemplo que você pode usar para chamar.

![URL de patch.](./media/retrain-classic/ml-help-page-patch-url.png)

### <a name="update-the-endpoint"></a>Atualizar o ponto de extremidade

Agora você pode usar o modelo treinado para atualizar o ponto de extremidade de pontuação criado anteriormente.

O código de exemplo a seguir mostra como usar *BaseLocation*, *RelativeLocation*, *SasBlobToken* e a URL do PATCH para atualizar o ponto de extremidade.

```csharp
private async Task OverwriteModel()
{
    var resourceLocations = new
    {
        Resources = new[]
        {
            new
            {
                Name = "Census Model [trained model]",
                Location = new AzureBlobDataReference()
                {
                    BaseLocation = "https://esintussouthsus.blob.core.windows.net/",
                    RelativeLocation = "your endpoint relative location", //from the output, for example: "experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner"
                    SasBlobToken = "your endpoint SAS blob token" //from the output, for example: "?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl"
                }
            }
        }
    };

    using (var client = new HttpClient())
    {
        client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", apiKey);

        using (var request = new HttpRequestMessage(new HttpMethod("PATCH"), endpointUrl))
        {
            request.Content = new StringContent(JsonConvert.SerializeObject(resourceLocations), System.Text.Encoding.UTF8, "application/json");
            HttpResponseMessage response = await client.SendAsync(request);

            if (!response.IsSuccessStatusCode)
            {
                await WriteFailedResponse(response);
            }

            // Do what you want with a successful response here.
        }
    }
}
```

*apiKey* e *endpointUrl* podem ser obtidos do painel do ponto de extremidade para esta chamada.

O valor do parâmetro *Name* nos *recursos* deve corresponder ao nome do recurso do modelo treinado salvo no experimento de previsão. Para obter o Nome do Recurso:

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu à esquerda, clique em **Machine Learning**.
1. Em Nome, clique em seu workspace e, em seguida, clique em **Serviços Web**.
1. Em Nome, clique em **Modelo de Censo [exp. preditivo]**.
1. Clique no novo ponto de extremidade adicionado.
1. No painel do ponto de extremidade, clique em **Atualizar Recurso**.
1. Na página Documentação da API de Atualizar Recurso para o serviço web, você pode encontrar o **Nome do Recurso** em **Recursos Atualizáveis**.

Se o token SAS expirar antes de você terminar de atualizar o ponto de extremidade, execute um GET com a ID do Trabalho para obter um novo token.

Quando o código é executado com êxito, o novo ponto de extremidade deve começar a usar o modelo recuperado em aproximadamente 30 segundos.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como gerenciar serviços Web ou acompanhar várias execuções de testes, confira os seguintes artigos:

* [Explorar o portal dos Serviços Web](manage-new-webservice.md)
* [Gerenciar iterações de teste](manage-experiment-iterations.md)