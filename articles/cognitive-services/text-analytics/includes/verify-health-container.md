---
title: Verificar a instância do contêiner de integridade
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância de contêiner de integridade.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 83a9eeb7644d107a808494ad06a8bef91d471fe1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009834"
---
### <a name="verify-that-a-container-is-running"></a>Verificar se um contêiner está em execução

1. Selecione a guia **visão geral** e copie o endereço IP.
1. Abra uma nova guia do navegador e insira o endereço IP. Por exemplo, digite `http://<IP-address>:5000 (http://55.55.55.55:5000` ). O home page do contêiner é exibido, o que permite que você saiba que o contêiner está em execução.

    ![Exibir o home page do contêiner para verificar se ele está em execução](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecione o link de **Descrição da API de serviço** para ir para a página do Swagger do contêiner.

1. Escolha qualquer uma das APIs **post** e selecione **experimentar**. Os parâmetros são exibidos, o que inclui a entrada de exemplo.

Há várias URLs que você também pode usar para verificar se o contêiner está em execução.

|Solicitação|Finalidade|
|--|--|
|`http://localhost:5000/`|O contêiner fornece uma home page.|
|`http://localhost:5000/ready`|Solicitado com GET, isso fornece uma verificação de que o contêiner está pronto para aceitar uma consulta no modelo. Essa solicitação pode ser usada para [testes de preparação e de execução](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) do Kubernetes.|
|`http://localhost:5000/status`|Solicitado com GET, como o ponto de extremidade/Ready, isso valida que o contêiner está em execução sem incorrer em uma consulta no modelo. Essa solicitação pode ser usada para [testes de preparação e de execução](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) do Kubernetes.|
|`http://localhost:5000/swagger`|Por meio dessa URL, o contêiner fornece um conjunto completo de documentação para os pontos de extremidade e um `Try it now` recurso. Com esse recurso, é possível inserir suas configurações em um formulário HTML baseado na Web e realizar a consulta sem precisar escrever nenhum código. Após a consulta ser retornada, um exemplo de comando CURL será fornecido para demonstrar o formato do corpo e dos cabeçalhos HTTP exigidos. |
|`http://localhost:5000/demo`| Solicitado por meio de um navegador, esse recurso fornece uma visualização interativa dos resultados de consultas de exemplos de texto de entrada ou um que você fornecer.  |

Use esta URL de solicitação – `http://localhost:5000/text/analytics/v3.2-preview.1/entities/health` para enviar uma consulta ao contêiner.
