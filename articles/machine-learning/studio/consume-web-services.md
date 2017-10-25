---
title: "Ccomo consumir um serviço Web do Machine Learning do Azure | Microsoft Docs"
description: "Depois que um serviço de machine learning é implantado, o serviço Web RESTFul disponibilizado pode ser consumido como serviço de solicitação-resposta em tempo real ou como um serviço de execução em lote."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 804f8211-9437-4982-98e9-ca841b7edf56
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/02/2017
ms.author: garye
ms.openlocfilehash: fc1152f1431474b6625f389a1290a121e86fbdac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-consume-an-azure-machine-learning-web-service"></a>Como consumir um serviço Web de Machine Learning do Azure

Depois de implantar um modelo de previsão de Machine Learning do Azure como um serviço Web, você pode usar uma API REST para enviar dados e obter previsões. Você pode enviar os dados em tempo real ou em modo de lote.

Você pode encontrar mais informações sobre como criar e implantar um serviço Web de Machine Learning usando o Machine Learning Studio aqui:

* Para obter um tutorial sobre como criar um teste no Machine Learning Studio, confira a seção [Crie seu primeiro experimento](create-experiment.md).
* Para obter detalhes sobre como implantar um serviço Web, confira [Implantar um serviço Web do Machine Learning](publish-a-machine-learning-web-service.md).
* Para obter mais informações sobre o Machine Learning em geral, visite o [Centro de Documentação do Machine Learning do Azure](https://azure.microsoft.com/documentation/services/machine-learning/).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="overview"></a>Visão geral
Com o serviço Web do Azure Machine Learning, um aplicativo externo se comunica com um modelo de pontuação do fluxo de trabalho do Machine Learning em tempo real. Uma chamada do serviço Web do Machine Learning retorna resultados de previsão para um aplicativo externo. Para fazer uma chamada de serviço Web do Machine Learning, transmita uma chave de API que é criada quando você implanta uma previsão. O serviço Web do Machine Learning baseia-se em REST, uma opção popular de arquitetura para projetos de programação da Web.

O Azure Machine Learning tem dois tipos de serviços:

* Serviço de Solicitação-Resposta (RRS) – Um serviço de baixa latência e altamente escalonável que fornece uma interface para os modelos sem monitoração de estado criados e implantados no Machine Learning Studio.
* Serviço de Execução de Lote (BES) – Um serviço assíncrono que pontua um lote de registros de dados.

Para obter mais informações sobre os serviços Web do Machine Learning, confira [Implantar um serviço Web do Machine Learning](publish-a-machine-learning-web-service.md).

## <a name="get-an-azure-machine-learning-authorization-key"></a>Obtenha uma chave de autorização de Azure Machine Learning
Quando você implanta seu experimento, as chaves de API são geradas para o serviço Web. Você pode recuperar as chaves de vários locais.

### <a name="from-the-microsoft-azure-machine-learning-web-services-portal"></a>Por meio do portal de serviços Web do Microsoft Azure Machine Learning
Entre no portal [Serviços Web do Microsoft Azure Machine Learning](https://services.azureml.net).

Para recuperar a chave de API para um novo serviço Web do Machine Learning:

1. No portal de serviços Web do Azure Machine Learning, clique em **Serviços Web** no menu superior.
2. Clique no serviço Web para o qual deseja recuperar a chave.
3. No menu superior, clique em **Consumir**.
4. Copie e salve a **Chave Primária**.

Para recuperar a chave de API para um serviço Web clássico do Machine Learning:

1. No portal de serviços Web do Azure Machine Learning, clique em **Serviços Web Clássicos** no menu superior.
2. Clique no serviço Web com o qual está trabalhando.
3. Clique no ponto de extremidade para o qual deseja recuperar a chave.
4. No menu superior, clique em **Consumir**.
5. Copie e salve a **Chave Primária**.

### <a name="classic-web-service"></a>Serviço Web Clássico
 Você também pode recuperar uma chave para um serviço Web Clássico por meio do Machine Learning Studio ou do Portal Clássico do Azure.

#### <a name="machine-learning-studio"></a>Machine Learning Studio
1. No Machine Learning Studio, clique em **SERVIÇOS WEB** à esquerda.
2. Clique em um serviço Web. A **Chave de API** está na guia **PAINEL**.

#### <a name="azure-classic-portal"></a>portal clássico do Azure
1. Clique em **APRENDIZADO DE MÁQUINA** à esquerda.
2. Clique no espaço de trabalho no qual o serviço Web está localizado.
3. Clique em **SERVIÇOS WEB**.
4. Clique em um serviço Web.
5. Clique em um ponto de extremidade. A “CHAVE DE API” está mais abaixo na parte inferior direita.

## <a id="connect"></a>Conectar-se a um serviço Web do Machine Learning
Você pode se conectar a um serviço Web do Machine Learning usando qualquer linguagem de programação que dá suporte à resposta e à solicitação HTTP. Você pode exibir exemplos em C#, Python e R de uma página de ajuda do serviço Web do Machine Learning.

**Ajuda da API do Machine Learning** Uma ajuda de API do Machine Learning é criada quando você implanta um serviço Web. Confira [Passo a passo do Azure Machine Learning – Implantar serviço Web](walkthrough-5-publish-web-service.md).
A ajuda da API do Machine Learning contém detalhes sobre um serviço Web de previsão.

1. Clique no serviço Web com o qual está trabalhando.
2. Clique no ponto de extremidade para o qual deseja exibir uma página de ajuda da API.
3. No menu superior, clique em **Consumir**.
4. Clique na **página de ajuda da API** nos pontos de extremidade Solicitação de Resposta ou Execução em Lote.

**Como exibir a ajuda da API do Machine Learning para um novo serviço Web**

No portal de serviços Web do Azure Machine Learning:

1. Clique em **SERVIÇOS WEB** no menu superior.
2. Clique no serviço Web para o qual deseja recuperar a chave.

Clique em **Consumir** para obter os URIs dos serviços de Solicitação-Resposta e Execução em Lotes, bem como o código de exemplo em C#, R e Python.

Clique em **API do Swagger** para obter a documentação baseada no Swagger para as APIs chamadas dos URIs fornecidos.

### <a name="c-sample"></a>Exemplo de C#
Para se conectar a um serviço Web do Machine Learning, use um **HttpClient** passando ScoreData. ScoreData contém um FeatureVector, um vetor com n dimensões de recursos numéricos que representa o ScoreData. Autentique no serviço de Machine Learning com uma chave de API.

Para se conectar a um serviço Web do Machine Learning, o pacote Nuget **Microsoft.AspNet.WebApi.Client** deve ser instalado.

**Instalar o Nuget Microsoft.AspNet.WebApi.Client no Visual Studio**

1. Publique “Baixe o conjunto de dados de Download de UCI: Serviço Web do conjunto de dados da classe Adulto 2”.
2. Clique em **Ferramentas** > **Gerenciador de Pacotes do NuGet** > **Console do Gerenciador de Pacotes**.
3. Escolha **Install-Package Microsoft.AspNet.WebApi.Client**.

**Para executar o exemplo de código**

1. Publique o experimento “Exemplo 1: Baixe o conjunto de dados de UCI: conjunto de dados da classe Adulto 2”, parte da coleção de exemplos de Machine Learning.
2. Atribua apiKey com a chave de um serviço Web. Confira a seção acima **Obter uma chave de autorização de Azure Machine Learning** .
3. Atribua serviceUri com o URI de solicitação.

### <a name="python-sample"></a>Exemplo de Python
Para se conectar a um serviço Web do Machine Learning, use a biblioteca **urllib2** passando ScoreData. ScoreData contém um FeatureVector, um vetor com n dimensões de recursos numéricos que representa o ScoreData. Autentique no serviço de Machine Learning com uma chave de API.

**Para executar o exemplo de código**

1. Implante o experimento "Exemplo 1: Baixe o conjunto de dados de UCI: conjunto de dados da classe Adulto 2", parte da coleção de exemplos de Machine Learning.
2. Atribua apiKey com a chave de um serviço Web. Consulte a seção **Obter uma chave de autorização do Azure Machine Learning** perto do início deste artigo.
3. Atribua serviceUri com o URI de solicitação.

