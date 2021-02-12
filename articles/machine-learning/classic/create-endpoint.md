---
title: 'ML Studio (clássico): criar pontos de extremidade de serviço Web-Azure'
description: Crie pontos de extremidade de serviço Web no Azure Machine Learning Studio (clássico). Cada ponto de extremidade no serviço Web é tratado, limitado e gerenciado de forma independente.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/15/2019
ms.openlocfilehash: 1032a90a35e60643e2ce937ed457a1fe3493d4d7
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322880"
---
# <a name="create-endpoints-for-deployed-azure-machine-learning-studio-classic-web-services"></a>Criar pontos de extremidade para serviços Web Azure Machine Learning Studio (clássico) implantados

**APLICA-SE A:**  ![Aplica-se a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clássico) ![Não se aplica a. ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


> [!NOTE]
> Este tópico descreve as técnicas aplicáveis a um Serviço Web do Machine Learning **Clássico**.

Depois que um serviço Web for implantado, criaremos um ponto de extremidade padrão para esse serviço. O ponto de extremidade padrão pode ser chamado usando sua chave de API. Você pode adicionar mais pontos de extremidade com suas próprias chaves no portal de Serviços Web.
Cada ponto de extremidade no serviço Web é tratado, limitado e gerenciado de forma independente. Cada ponto de extremidade é uma única URL com uma chave de autorização que você pode distribuir aos seus clientes.

## <a name="add-endpoints-to-a-web-service"></a>Adicionar pontos de extremidade a um serviço Web

Você pode adicionar um ponto de extremidade a um serviço Web usando o portal de Serviços Web do Azure Machine Learning. Quando o ponto de extremidade é criado, você pode consumi-lo por meio de APIs síncronas, APIs de lote e planilhas do Excel.

> [!NOTE]
> Caso você tenha adicionado mais pontos de extremidade ao serviço Web, você não poderá excluir o ponto de extremidade padrão.

1. Em Machine Learning Studio (clássico), na coluna de navegação à esquerda, clique em serviços Web.
2. Na parte inferior do painel do serviço Web, clique em **Gerenciar pontos de extremidade**. O portal de Serviços Web do Azure Machine Learning abre a página de pontos de extremidade do serviço Web.
3. Clique em **Nova**.
4. Digite um nome e uma descrição para o novo ponto de extremidade. Os nomes dos pontos de extremidade devem ter 24 caracteres ou menos e devem ser compostos de letras minúsculas ou números. Selecione o nível de log e se os dados de exemplo estão habilitados. Para obter mais informações sobre registro em log, consulte [habilitar o log para serviços web Machine Learning](web-services-logging.md).

## <a name="scale-a-web-service-by-adding-additional-endpoints"></a><a id="scaling"></a> Dimensionar um serviço Web adicionando mais pontos de extremidade

Por padrão, cada serviço Web publicado é configurado para oferecer suporte a 20 a 200 solicitações simultâneas. Azure Machine Learning Studio (clássico) otimiza automaticamente a configuração para fornecer o melhor desempenho para o serviço Web e o valor do portal é ignorado.

Se você planeja chamar a API com uma carga maior que o valor suportado de 200 para o Máximo de Chamadas Simultâneas, é preciso criar vários pontos de extremidade no mesmo serviço Web. Você pode, então, distribuir a carga aleatoriamente entre todos eles.

O dimensionamento de um serviço Web é uma tarefa comum. Entre os motivos para dimensionar estão oferecer suporte a mais de 200 solicitações simultâneas, aumentar a disponibilidade por meio de vários pontos de extremidade ou fornecer pontos de extremidade separados ao serviço Web. Você pode aumentar a escala adicionando pontos de extremidade adicionais para o mesmo serviço Web por meio do portal do [serviço web Azure Machine Learning](https://services.azureml.net/) .

Tenha em mente que usar uma contagem de simultaneidade alta pode ser prejudicial se você não estiver chamando a API com uma taxa correspondentemente alta. Você pode ver tempos limite esporádicos e/ou picos na latência se colocar uma carga relativamente baixa em uma API configurada para alta carga.

As APIs síncronas são normalmente usadas em situações onde uma baixa latência é desejada. A latência aqui indica o tempo necessário para a API concluir uma solicitação e não se responsabiliza por quaisquer atrasos na rede. Digamos que você tenha uma API com uma latência de 50 ms. Para consumir totalmente a capacidade disponível com alto nível de limitação e o Máximo de Chamadas Simultâneas = 20, você precisa chamar esta API 20 * 1000 / 50 = 400 vezes por segundo. Estendendo isso ainda mais, um Máximo de Chamadas Simultâneas de 200 permite que você chame a API 4000 vezes por segundo, supondo que a latência seja de 50 ms.

## <a name="next-steps"></a>Próximas etapas

[Como consumir um serviço web Azure Machine Learning](consume-web-services.md).