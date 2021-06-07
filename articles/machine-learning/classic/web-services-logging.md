---
title: 'ML Studio (clássico): habilitar log de serviço Web-Azure'
description: Saiba como habilitar o registro em log para serviços da Web Machine Learning Studio (clássico). O registro em log fornece informações adicionais para ajudar a solucionar problemas com as APIs.
services: machine-learning
author: likebupt
ms.author: keli19
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
ms.date: 06/15/2017
ms.openlocfilehash: c9ce9b7b0d739301e76abd43b265fe28195ed302
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100518276"
---
# <a name="enable-logging-for-azure-machine-learning-studio-classic-web-services"></a>Habilitar o registro em log para serviços Web Azure Machine Learning Studio (clássico)

**APLICA-SE A:**  ![Aplica-se a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


Este documento fornece informações sobre o recurso de log dos serviços Web Machine Learning Studio (clássico). O registro em log fornece informações adicionais, além de apenas um número de erro e uma mensagem, que podem ajudá-lo a solucionar suas chamadas para as APIs de Machine Learning Studio (clássicas).  

## <a name="how-to-enable-logging-for-a-web-service"></a>Como habilitar o registro em log para um serviço Web

Você habilita o registro em log no portal de [Serviços Web Azure Machine Learning Studio (clássico)](https://services.azureml.net) . 

1. Entre no portal de serviços Web do Azure Machine Learning Studio (clássico) em [https://services.azureml.net](https://services.azureml.net) . Para um serviço Web clássico, você também pode acessar o portal clicando em **nova experiência de serviços Web** na página de serviços Web Machine Learning Studio (clássico) no Studio (clássico).

   ![Novo link de Experiência dos Serviços Web](./media/web-services-logging/new-web-services-experience-link.png)

2. Na barra de menus superior, clique em **Serviços Web** para um novo serviço Web ou clique em **Serviços Web Clássicos** para um serviço Web Clássico.

   ![Selecione serviços Web Novos ou Clássicos](./media/web-services-logging/select-web-service.png)

3. Para um novo serviço Web, clique no nome de serviço Web. Para um serviço Web Clássico, clique no nome do serviço Web e clique no ponto de extremidade apropriado na próxima página.

4. Na barra de menus superior, clique em **Configurar**.

5. Defina a opção **Habilitar Log** como *Erro* (para registrar somente erros) ou *Todos* (para registro em log completo).

   ![Selecionar o nível de log](./media/web-services-logging/enable-logging.png)

6. Clique em **Save** (Salvar).

7. Para os serviços Web Clássicos, crie o contêiner **ml-diagnostics**.

   Todos os logs de serviço Web são mantidos em um contêiner de blob denominado **ml diagnóstico** na conta de armazenamento associada ao serviço Web. Para novos serviços Web, esse contêiner é criado na primeira vez que você acessa o serviço Web. Para serviços Web Clássico, você precisa criar o contêiner, se ele ainda não existe. 

   1. No [portal do Azure](https://portal.azure.com), vá para a conta de armazenamento associada ao serviço Web.

   2. Em **Serviço Blob**, clique em **Contêineres**.

   3. Se o contêiner **ml-diagnostics** não existir, clique em **+Contêiner**, dê ao contêiner o nome "ml-diagnostics" e selecione o **Tipo de acesso** como "Blob". Clique em **OK**.

      ![Criar um novo contêiner para armazenar seus logs de diagnóstico](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Para um serviço Web clássico, o painel de serviços Web no Machine Learning Studio (clássico) também tem uma opção para habilitar o registro em log. No entanto, como os logs são gerenciados por meio do portal de serviços Web, você precisa habilitar os logs por meio do portal, conforme descrito neste artigo. Se você já tiver habilitado o log no Studio (clássico), no portal de serviços Web, desabilite o registro em log e habilite-o novamente.


## <a name="the-effects-of-enabling-logging"></a>Os efeitos de habilitar o registro em log
Quando o registro em log está habilitado, o diagnóstico e os erros do ponto de extremidade do serviço Web são registrados no contêiner de blob do **ml-Diagnostics** na conta de armazenamento do Azure vinculada ao espaço de trabalho do usuário. Esse contêiner armazena todas as informações de diagnóstico para todos os pontos de extremidade do serviço da Web para todos os workspaces associados a esta conta de armazenamento.

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