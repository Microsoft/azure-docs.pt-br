---
title: 'Entrada/saída do serviço Web: referência de módulo'
description: Saiba como usar os módulos de serviço Web no designer de Azure Machine Learning para gerenciar entradas e saídas.
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/13/2020
ms.openlocfilehash: 475ad3de8e0a7636a14949d4fcd8a5ec2812ad5e
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421117"
---
# <a name="web-service-input-and-web-service-output-modules"></a>Módulos de entrada do serviço Web e saída do serviço Web

Este artigo descreve os módulos de entrada e saída do serviço Web no designer de Azure Machine Learning.

O módulo entrada do serviço Web só pode se conectar a uma porta de entrada com o tipo **DataFrameDirectory**. O módulo saída do serviço Web só pode ser conectado de uma porta de saída com o tipo **DataFrameDirectory**. Você pode encontrar os dois módulos na árvore de módulo, sob a categoria **serviço da Web** . 

O módulo entrada do serviço Web indica onde os dados do usuário entram no pipeline. O módulo saída do serviço Web indica onde os dados do usuário são retornados em um pipeline de inferência em tempo real.

## <a name="how-to-use-web-service-input-and-output"></a>Como usar entrada e saída do serviço Web

Quando você [cria um pipeline de inferência em tempo real](../tutorial-designer-automobile-price-deploy.md#create-a-real-time-inference-pipeline) do seu pipeline de treinamento, os módulos de entrada do serviço Web e saída do serviço Web serão adicionados automaticamente para mostrar onde os dados do usuário entram no pipeline e onde os dados são retornados. 

> [!NOTE]
> Gerar automaticamente um pipeline de inferência em tempo real é um processo de melhor esforço baseado em regras. Não há garantia de exatidão. 

Você pode adicionar ou remover manualmente os módulos entrada do serviço Web e saída do serviço Web para atender às suas necessidades. Certifique-se de que seu pipeline de inferência em tempo real tenha pelo menos um módulo de entrada do serviço Web e um módulo de saída do serviço Web. Se você tiver vários módulos de entrada de serviço Web ou de saída de serviço Web, verifique se eles têm nomes exclusivos. Você pode inserir o nome no painel direito do módulo.

Você também pode criar manualmente um pipeline de inferência em tempo real adicionando módulos de entrada do serviço Web e de saída do serviço Web ao seu pipeline não enviado.

> [!NOTE]
> O tipo de pipeline será determinado na primeira vez que você o enviar. Certifique-se de adicionar os módulos entrada de serviço Web e saída de serviço Web antes de enviar pela primeira vez.

O exemplo a seguir mostra como criar manualmente o pipeline de inferência em tempo real no módulo executar script Python. 

![Exemplo](media/module/web-service-input-output-example.png)
   
Depois que você enviar o pipeline e a execução for concluída com êxito, você poderá implantar o ponto de extremidade em tempo real.
   
> [!NOTE]
>  No exemplo anterior, **inserir dados manualmente** fornece o esquema de dados para entrada do serviço Web e é necessário para implantar o ponto de extremidade em tempo real. Em geral, você sempre deve conectar um módulo ou conjunto de dados à porta em que a **entrada do serviço Web** está conectada para fornecer o esquema de dados.
   
## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre como [implantar o ponto de extremidade em tempo real](../tutorial-designer-automobile-price-deploy.md#deploy-the-real-time-endpoint).

Confira o [conjunto de módulos disponíveis](module-reference.md) no Azure Machine Learning.