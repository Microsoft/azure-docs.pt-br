---
title: "Registro em log de serviços Web do Machine Learning | Microsoft Azure"
description: "Saiba como habilitar o registro em log de serviços Web de Machine Learning. O registro em log fornece informações adicionais para ajudar a solucionar problemas com as APIs."
services: machine-learning
documentationcenter: 
author: raymondlaghaeian
manager: jhubbard
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.author: raymondl;garye
ms.openlocfilehash: 31497bcae5889590a2261b716574ec4eae21852d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-logging-for-machine-learning-web-services"></a>Habilitar o log de serviços Web de Machine Learning
Este documento fornece informações sobre o recurso de logs de serviços Web do Machine Learning. Os logs fornecem informações adicionais, além de apenas um número de erro e uma mensagem, o que pode ajudar a solucionar suas chamadas para as APIs de Machine Learning.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Como habilitar o registro em log para um serviço Web

Você hara habilita os logs no portal de [serviços Web do Azure Machine Learning](https://services.azureml.net). 

1. Entre no portal de Serviços Web do Azure Machine Learning em [https://services.azureml.net](https://services.azureml.net). Para um serviço Web Clássico, você também pode acessar o portal clicando em **Nova Experiência de Serviços Web** na página de Serviços Web do Machine Learning no Machine Learning Studio.

   ![Novo link de Experiência dos Serviços Web](./media/web-services-logging/new-web-services-experience-link.png)

2. Na barra de menus superior, clique em **Serviços Web** para um novo serviço Web ou clique em **Serviços Web Clássicos** para um serviço Web Clássico.

   ![Selecione serviços Web Novos ou Clássicos](./media/web-services-logging/select-web-service.png)

3. Para um novo serviço Web, clique no nome de serviço Web. Para um serviço Web Clássico, clique no nome do serviço Web e clique no ponto de extremidade apropriado na próxima página.

4. Na barra de menus superior, clique em **Configurar**.

5. Defina a opção **Habilitar Log** como *Erro* (para registrar somente erros) ou *Todos* (para registro em log completo).

   ![Selecionar o nível de log](./media/web-services-logging/enable-logging.png)

6. Clique em **Salvar**.

7. Para os serviços Web Clássicos, crie o contêiner **ml-diagnostics**.

   Todos os logs de serviço Web são mantidos em um contêiner de blob denominado **ml diagnóstico** na conta de armazenamento associada ao serviço Web. Para novos serviços Web, esse contêiner é criado na primeira vez que você acessa o serviço Web. Para serviços Web Clássico, você precisa criar o contêiner, se ele ainda não existe. 

   1. No [portal do Azure](https://portal.azure.com), vá para a conta de armazenamento associada ao serviço Web.

   2. Em **Serviço Blob**, clique em **Contêineres**.

   3. Se o contêiner **ml-diagnostics** não existir, clique em **+Contêiner**, dê ao contêiner o nome "ml-diagnostics" e selecione o **Tipo de acesso** como "Blob". Clique em **OK**.

      ![Selecionar o nível de log](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Para um serviço Web Clássico, o Painel de Serviços Web no Machine Learning Studio também tem uma opção para habilitar o log. No entanto, como os logs são gerenciados por meio do portal de serviços Web, você precisa habilitar os logs por meio do portal, conforme descrito neste artigo. Se você tiver habilitado os logs no Studio, no Portal de Serviços Web, desabilite os logs e habilite-os novamente.


## <a name="the-effects-of-enabling-logging"></a>Os efeitos de habilitar o registro em log
Quando o log estiver habilitado, os diagnósticos e erros do ponto de extremidade de serviço Web serão registrados no contêiner de blobs **ml-diagnostics** na Conta de Armazenamento do Azure vinculada ao espaço de trabalho do usuário. Esse contêiner armazena todas as informações de diagnóstico para todos os pontos de extremidade do serviço da Web para todos os espaços de trabalho associados a esta conta de armazenamento.

Os logs podem ser exibidos usando qualquer uma das várias ferramentas disponíveis para explorar uma Conta de Armazenamento do Azure. A maneira mais fácil possível de navegar para a conta de armazenamento no portal do Azure: clique em **Contêineres** e clique no contêiner **ml diagnostics**.  

## <a name="log-blob-detail-information"></a>Informações detalhadas do log blob
Cada blob no contêiner contém as informações de diagnóstico para um das seguintes ações:

* Uma execução do método Batch-Execution  
* Uma execução do método Request-Response  
* Inicialização de um contêiner de Request-Response

O nome de cada blob tem um prefixo da seguinte forma: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Sendo que _Tipo de log_ assume um dos seguintes valores:  

* lote  
* pontuação/solicitações  
* pontuação/init  

