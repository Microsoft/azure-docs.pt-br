---
title: Selecione um domínio para um projeto Visão Personalizada-Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Este artigo mostrará como selecionar um domínio para seu projeto no Serviço de Visão Personalizada.
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 2259d0114d91af2e688df66ff8dc4e8d293c2b03
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624818"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Selecionar um domínio para um projeto Visão Personalizada

Na guia Configurações do seu projeto Visão Personalizada, você pode selecionar um domínio para seu projeto. Escolha o domínio mais próximo de seu cenário. Se estiver acessando Visão Personalizada por meio de uma biblioteca de cliente ou API REST, você precisará especificar uma ID de domínio ao criar o projeto. Você pode obter uma lista de IDs de domínio com [domínios Get](https://westus2.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeab)ou usar a tabela a seguir.

## <a name="image-classification"></a>Classificação de imagens

|Domínio|Finalidade|
|---|---|
|__Geral__| Otimizado para uma ampla gama de tarefas de classificação de imagens. Se nenhum dos outros domínios for apropriado ou se você não tiver certeza de qual domínio escolher, selecione o domínio geral. ID: `ee85a74c-405e-4adc-bb47-ffa8ca0c9f31`|
|__Geral [a1]__| Otimizado para maior precisão com o tempo de inferência comparável como domínio geral. Recomendado para conjuntos de grandes maiores ou para cenários de usuário mais difíceis. Este domínio requer mais tempo de treinamento. ID: `a8e3c40f-fb4a-466f-832a-5e457ae4a344`|
|__Alimentos__|Otimizado para fotografias de pratos como você os veria no menu de um restaurante. Caso queira classificar fotografias de frutas ou legumes individuais, use o domínio Alimentos. ID: `c151d5b5-dd07-472a-acc8-15d29dea8518`|
|__Pontos de referência__|Otimizado para pontos de referência reconhecidos, tanto naturais quanto artificiais. Este domínio funciona melhor quando o ponto de referência está claramente visível na fotografia. Este domínio funciona mesmo quando o ponto de referência está um pouco obstruído devido a pessoas na frente dele. ID: `ca455789-012d-4b50-9fec-5bb63841c793`|
|__Varejo__|Otimizado para imagens encontradas em um catálogo ou site de compras. Se você quiser classificação de alta precisão entre Dresses, Pants e camisas, use este domínio. ID: `b30a91ae-e3c1-4f73-a81e-c270bff27c39`|
|__Domínios compactos__| Otimizado para as restrições de classificação em tempo real em dispositivos de borda.|

## <a name="object-detection"></a>Detecção de objetos

|Domínio|Finalidade|
|---|---|
|__Geral__| Otimizado para uma ampla variedade de tarefas de detecção de objetos. Se nenhum dos outros domínios for apropriado ou se você não tiver certeza de qual domínio escolher, selecione o domínio geral. ID: `da2e3a8a-40a5-4171-82f4-58522f70fbc1`|
|__Geral [a1]__| Otimizado para maior precisão com o tempo de inferência comparável como domínio geral. Recomendado para necessidades de localização de região mais precisas, conjuntos de grandes maiores ou cenários de usuário mais difíceis. Este domínio requer mais tempo de treinamento e os resultados não são determinísticos: Espere uma diferença de mapa +-1% com os mesmos dados de treinamento fornecidos. ID: `9c616dff-2e7d-ea11-af59-1866da359ce6`|
|__Logotipo__|Otimizado para localizar logotipos de marcas em imagens. ID: `1d8ffafe-ec40-4fb2-8f90-72b3b6cecea4`|
|__Produtos em prateleiras__|Otimizado para detectar e classificar produtos em prateleiras. ID: `3780a898-81c3-4516-81ae-3a139614e1f3`|
|__Domínios compactos__| Otimizado para as restrições de detecção de objetos em tempo real em dispositivos de borda.|

## <a name="compact-domains"></a>Domínios compactos

Os modelos gerados por domínios compactos podem ser exportados para serem executados localmente. Na API de visualização pública do Visão Personalizada 3,4, você pode obter uma lista das plataformas exportáveis para domínios de compactação chamando a API getdomains.

O desempenho do modelo varia de acordo com o domínio selecionado. Na tabela a seguir, relatamos o tamanho do modelo e o tempo de inferência na CPU da área de trabalho Intel e NVidia GPU \[ 1 \] . Esses números não incluem pré-processamento e tempo de pré-processamento.

|Tarefa|Domínio|ID|Tamanho do modelo|Tempo de inferência de CPU|Tempo de inferência de GPU|
|---|---|---|---|---|---|
|classificação|Geral (compacto)|`0732100f-1a38-4e49-a514-c9b44c697ab5`|6 MB|10 ms|5 ms|
|classificação|Geral (Compact) [S1]|`a1db07ca-a19a-4830-bae8-e004a42dc863`|43 MB|50 ms|5 ms|
|Detecção de objetos|Geral (compacto)|`a27d5ca5-bb19-49d8-a70a-fec086c47f5b`|45 MB|35 MS|5 ms|
|Detecção de objetos|Geral (Compact) [S1]|`7ec2ac80-887b-48a6-8df9-8b1357765430`|14 MB|27 MS|7 ms|

>[!NOTE]
>O domínio __geral (compacto)__ para detecção de objeto requer lógica de processamento especial. Para obter detalhes, consulte um exemplo de script no pacote zip exportado. Se você precisar de um modelo sem a lógica de pré-processamento, use __geral (Compact) [S1]__.

>[!IMPORTANT]
>Não há nenhuma garantia de que os modelos exportados forneçam exatamente o mesmo resultado que a API de previsão na nuvem. A pequena diferença na plataforma em execução ou na implementação de pré-processamento pode causar maior diferença nas saídas do modelo. Para obter os detalhes da lógica de pré-processamento, consulte [este documento](quickstarts/image-classification.md).

\[1 \] Intel Xeon E5-2690 CPU e NVIDIA Tesla M60
