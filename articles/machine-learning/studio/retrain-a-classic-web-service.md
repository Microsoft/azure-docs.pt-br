---
title: "Readaptar um serviço Web Clássico | Microsoft Docs"
description: "Aprenda como readaptar um modelo de forma programática e atualizar o serviço Web para usar o modelo treinado recentemente no Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondlaghaeian
editor: 
ms.assetid: e36e1961-9e8b-4801-80ef-46d80b140452
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 04e019501be6880fcc7e92de690a9f31195282e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="retrain-a-classic-web-service"></a>Treinar novamente um serviço Web Clássico
O Serviço Web Preditivo implantado é o ponto de extremidade de pontuação padrão. Os pontos de extremidade padrão são mantidos em sincronização com o experimento de origem e os experimentos de pontuação; portanto, o modelo treinado para o ponto de extremidade padrão não pode ser substituído. Para adaptar novamente o serviço Web, você deve adicionar um novo ponto de extremidade ao serviço Web. 

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter configurado um Teste de Treinamento e um Experimento de Previsão, como mostrado em [Readaptar os modelos do Machine Learning de forma programática](retrain-models-programmatically.md). 

> [!IMPORTANT]
> O experimento de previsão deve ser implantado como um serviço Web do Machine Learning clássico. 
> 
> 

Para obter mais informações sobre como implantar os serviços Web, veja [Implantar um serviço Web do Azure Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="add-a-new-endpoint"></a>Adicionar um novo ponto de extremidade
O serviço Web de previsão implantado contém um padrão de pontuação de ponto de extremidade que é mantido sincronizado com o treinamento original e com o modelo de treinamento de experimentos de pontuação. Para atualizar seu serviço Web com um novo modelo de treinamento, você deve criar um novo ponto de extremidade de pontuação. 

Para criar um novo ponto de extremidade de pontuação, no Serviço da Web Preditivo que possa ser atualizado com o modelo treinado:

> [!NOTE]
> Certifique-se de que você está adicionando o ponto de extremidade ao serviço Web de previsão e não ao serviço da Web de treinamento. Se você tiver implantado corretamente um serviço Web de previsão e um serviço da Web de treinamento, você verá dois serviços Web separados listados. O serviço Web de previsão deve terminar com "[exp. preditivo]".
> 
> 

Há três maneiras em que você pode adicionar um novo ponto de extremidade em um serviço Web:

1. Programaticamente
2. Use o portal dos Serviços do BizTalk do Microsoft Azure
3. Use o portal clássico do Azure

### <a name="programmatically-add-an-endpoint"></a>Adicionar um ponto de extremidade programaticamente
Você pode adicionar pontos de extremidade de pontuação usando o código de exemplo fornecido neste [repositório github](https://github.com/raymondlaghaeian/AML_EndpointMgmt/blob/master/Program.cs).

### <a name="use-the-microsoft-azure-web-services-portal-to-add-an-endpoint"></a>Usar o portal dos Serviços Web do Microsoft Azure para adicionar um ponto de extremidade
1. No Estúdio de Machine Learning, clique em Serviços Web na coluna de navegação à esquerda.
2. Na parte inferior do painel do serviço Web, clique em **Gerenciar visualização de pontos de extremidade**.
3. Clique em **Adicionar**.
4. Digite um nome e uma descrição para o novo ponto de extremidade. Selecione o nível de log e se os dados de exemplo estão habilitados. Para obter mais informações sobre registro em log, consulte [Habilitar o log de serviços Web de Machine Learning](web-services-logging.md).

### <a name="use-the-azure-classic-portal-to-add-an-endpoint"></a>Use o Portal Clássico do Azure para adicionar um ponto de extremidade
1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. No menu esquerdo, clique em **Machine Learning**.
3. Em Nome, clique em seu espaço de Em Nome, clique em seu espaço de trabalho e, em seguida, clique em **Serviços Web**.
4. Em Nome, clique em **Modelo de Censo [exp. preditivo]**.
5. Na parte inferior da página, clique em **Adicionar Ponto de Extremidade**. Para saber mais sobre a adição de pontos de extremidade, veja [Criação de pontos de extremidade](create-endpoint.md). 

## <a name="update-the-added-endpoints-trained-model"></a>Atualizar modelo treinado do ponto de extremidade adicionado
Para concluir o processo de novos treinamentos, você deve atualizar o modelo treinado do novo ponto de extremidade que você adicionou.

* Se tiver adicionado um novo ponto de extremidade usando o portal do Azure, você poderá clicar no nome do novo do ponto de extremidade no portal do Azure e, em seguida, no link **UpdateResource** para obter a URL de que precisa para atualizar o modelo do ponto de extremidade.
* Se você tiver adicionado o ponto de extremidade usando o código de exemplo, isso inclui o local da URL de Ajuda identificado pelo valor *HelpLocationURL* na saída.

Para recuperar a URL do caminho:

1. Copie e cole a URL no navegador.
2. Clique no link Atualizar Recurso.
3. Copie a URL POST da solicitação PATCH. Por exemplo:
   
     URL DO PATCH: https://management.azureml.net/workspaces/00bf70534500b34rebfa1843d6/webservices/af3er32ad393852f9b30ac9a35b/endpoints/newendpoint2

Agora você pode usar o modelo treinado para atualizar o ponto de extremidade de pontuação criado anteriormente.

O código de exemplo a seguir mostra como usar *BaseLocation*, *RelativeLocation*, *SasBlobToken* e a URL do PATCH para atualizar o ponto de extremidade.

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
                        RelativeLocation = "your endpoint relative location", //from the output, for example: “experimentoutput/8946abfd-79d6-4438-89a9-3e5d109183/8946abfd-79d6-4438-89a9-3e5d109183.ilearner”
                        SasBlobToken = "your endpoint SAS blob token" //from the output, for example: “?sv=2013-08-15&sr=c&sig=37lTTfngRwxCcf94%3D&st=2015-01-30T22%3A53%3A06Z&se=2015-01-31T22%3A58%3A06Z&sp=rl”
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

*apiKey* e *endpointUrl* podem ser obtidos do painel do ponto de extremidade para esta chamada.

O valor do parâmetro *Nome* em *Recursos* deve corresponder ao Nome do Recurso do Modelo Treinado Salvo no Experimento Preditivo. Para obter o Nome do Recurso:

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com).
2. No menu esquerdo, clique em **Machine Learning**.
3. Em Nome, clique em seu espaço de Em Nome, clique em seu espaço de trabalho e, em seguida, clique em **Serviços Web**.
4. Em Nome, clique em **Modelo de Censo [exp. preditivo]**.
5. Clique no novo ponto de extremidade adicionado.
6. No painel do ponto de extremidade, clique em **Atualizar Recurso**.
7. Na página Documentação da API de Atualizar Recurso para o serviço web, você pode encontrar o **Nome do Recurso** em **Recursos Atualizáveis**.

Se o token SAS expirar antes de você terminar de atualizar o ponto de extremidade, execute um GET com a Id do Trabalho para obter um novo token.

Quando o código é executado com êxito, o novo ponto de extremidade deve começar a usar o modelo recuperado em aproximadamente 30 segundos.

## <a name="summary"></a>Resumo
Usando as APIs de Readaptação, você pode atualizar o modelo treinado de um Serviço Web preditivo ao habilitar cenários como:

* Readaptação de modelo periódico com novos dados.
* A distribuição de um modelo para os clientes com o objetivo de permitir que eles recuperem o modelo usando seus próprios dados.

## <a name="next-steps"></a>Próximas etapas
[Solução de problemas de readaptação de um serviço Web clássico de Azure Machine Learning](troubleshooting-retraining-models.md)

