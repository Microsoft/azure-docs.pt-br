---
title: Posicionamento da câmera de Análise Espacial
titleSuffix: Azure Cognitive Services
description: Saiba como configurar uma câmera para uso com Análise Espacial
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: d58e4303078733eb1014171271d27907ff7a95b0
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286259"
---
# <a name="camera-placement-guide"></a>Guia de posicionamento de câmera

Este artigo fornece recomendações de posicionamento da câmera para Análise Espacial (versão prévia pública). Ele inclui diretrizes gerais, bem como recomendações específicas para altura, ângulo e distância da câmera ao ponto focal para todas as operações incluídas. 

> [!NOTE]
> Este guia foi criado para a câmera Axis M3045-V. Essa câmera usará resolução 1920x1080, campo de visão horizontal de 106 graus, campo de visão vertical de 59 graus e uma distância focal fixa de 2,8 mm. Os princípios abaixo se aplicam a todas as câmeras, mas as diretrizes específicas sobre a altura da câmera e a distância da câmera ao ponto focal precisarão ser ajustadas para uso com outras câmeras. 

## <a name="general-guidelines"></a>Diretrizes gerais

Considere as seguintes diretrizes gerais ao posicionar câmeras para Análise Espacial:

* **Altura da iluminação.** Coloque as câmeras abaixo dos acessórios de iluminação para que eles não as bloqueiem.
* **Obstruções.** Para evitar obstruir a visão das câmeras, observe as obstruções, como postes, sinalização, prateleiras, paredes e câmeras LP existentes.
* **Retroiluminação do ambiente.** A retroiluminação externa afeta a qualidade da imagem da câmera. Para evitar condições severas de retroiluminação, evite direcionar as câmeras para janelas externas e portas de vidro.
* **Regras e regulamentos locais de privacidade.** Os regulamentos locais podem restringir o que pode ser captado pelas câmeras. Compreenda as regras e os regulamentações locais antes de posicionar as câmeras.
* **Criação da estrutura.** O HVAC, os sprinklers e a fiação existente podem limitar a montagem rígida de câmeras.
* **Gerenciamento de cabos.** Não se esqueça de rotear um cabo ethernet dos locais planejados de montagem da câmera para o switch PoE (Power Over Internet).

## <a name="height-focal-point-distance-and-angle"></a>Altura, distância do ponto focal e ângulo

Você precisa considerar três coisas ao decidir como instalar uma câmera para Análise Espacial:
- Altura da câmera
- Distância da câmera ao ponto focal
- O ângulo da câmera em relação à planta

Também é importante saber a direção que a maioria das pessoas caminha (direção da pessoa caminhando) em relação ao campo de visão da câmera, se possível. Essa direção é importante para o desempenho do sistema.

![Imagem de uma pessoa caminhando em uma direção](./media/spatial-analysis/person-walking-direction.png)

A ilustração a seguir mostra a exibição de elevação para a direção de caminhada da pessoa.

![Exibição de plano e elevação](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>Altura da câmera

Geralmente, as câmeras devem ser montadas a 12-14 pés do chão. Para detecção de máscara facial, recomendamos que as câmeras sejam montadas em 8-12 pés do chão. Ao planejar a montagem da câmera nesse intervalo, considere as obstruções (por exemplo: prateleiras, luzes suspensas, sinalização suspensa e monitores) que possam afetar a exibição da câmera e ajuste a altura conforme necessário.

## <a name="camera-to-focal-point-distance"></a>Distância da câmera ao ponto focal

A _distância da câmera ao ponto focal_ é a distância linear do ponto focal (ou do centro da imagem da câmera) à câmera medida no chão.

![Distância da câmera ao ponto focal](./media/spatial-analysis/camera-focal-point.png)

Essa distância é medida na planta.

![Como a distância da câmera ao ponto focal é medida do chão](./media/spatial-analysis/camera-focal-point-floor-plane.png)

De cima, ela é assim:

![Como a distância da câmera ao ponto focal é medida de cima](./media/spatial-analysis/camera-focal-point-above.png)

Use a tabela abaixo para determinar a distância do ponto focal da câmera com base em alturas de montagem específicas. Essas distâncias são para um posicionamento ideal. Observe que a tabela fornece diretrizes abaixo da recomendação de 12'-14', pois alguns tetos podem limitar a altura. Para detecção de máscara facial, a distância recomendada da câmera ao ponto focal (mín./máx.) é de 4'-10" para altura da câmera entre 8' e 12'.

| Altura da câmera | Distância da câmera ao ponto focal (mín./máx.) |  
| ------------- | ---------------------------------------- |  
| 8'            | 4,6'-8'                                  |  
| 10'           | 5,8'-10'                                 |  
| 12'           | 7'-12'                                   |  
| 14'           | 8'-14"                                  |  
| 16'           | 9,2'-16'                                 |  
| 20'           | 11,5'-20'                                |  

A ilustração a seguir simula exibições de câmeras das distâncias mais próximas e mais distantes entre a câmera e o ponto focal.

| Mais próximas                                      | Mais distantes                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![Distância mais próxima da câmera ao ponto focal](./media/spatial-analysis/focal-point-closest.png) | ![Distância mais distante da câmera ao ponto focal](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>Intervalos de montagem do ângulo da câmera

Esta seção descreve os intervalos aceitáveis de montagem de ângulo de câmera. Esses intervalos de montagem mostram o intervalo aceitável para o posicionamento ideal.

### <a name="line-configuration"></a>Configuração da linha

Para a operação **cognitiveservices.vision.spatialanalysis-personcrossingline**, +/-5° é o ângulo de montagem da câmera ideal para maximizar a precisão.

Para detecção de máscara facial, o ângulo de montagem da câmera ideal é de +/-30 graus para a altura da câmera entre 8' e 12'.

A ilustração a seguir simula exibições de câmera usando as recomendações de ângulo de montagem mais à esquerda (-) e mais à direita (+) para usar **cognitiveservices.vision.spatialanalysis-personcrossingline**, a fim de fazer a contagem de entradas em uma porta.

| Exibição mais à esquerda                | Exibição mais à direita                |  
| ---------------------------- | ----------------------------- |  
| ![Ângulo da câmera mais à esquerda](./media/spatial-analysis/camera-angle-left.png) | ![Ângulo da câmera mais à direita](./media/spatial-analysis/camera-angle-right.png) |  

A ilustração a seguir mostra o posicionamento da câmera e os ângulos de montagem com base em uma visão geral.

![Visão geral](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>Configuração da zona

Recomendamos que você coloque as câmeras a 10 pés ou mais acima do solo para garantir que a área coberta seja grande o bastante. 

Quando a zona estiver próxima a um obstáculo como uma parede ou prateleira, monte as câmeras na distância especificada do alvo dentro da faixa de ângulo aceitável de 120 graus, conforme mostrado na ilustração a seguir.

![Ângulo de câmera aceitável](./media/spatial-analysis/camera-angle-acceptable.png)

A ilustração a seguir fornece simulações para as exibições da câmera à esquerda e à direita de uma área próxima a uma prateleira.

| Exibição à esquerda        | Exibição à direita        |  
| ---------------- | ----------------- |  
| ![Exibição à esquerda](./media/spatial-analysis/end-cap-left.png) | ![Exibição à direita](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>Filas

As habilidades **cognitiveservices.vision.spatialanalysis-personcount**, **cognitiveservices.vision.spatialanalysis-persondistance** e **cognitiveservices.vision.spatialanalysis-personcrossingpolygon** poderão ser usadas para monitorar filas. Para obter a qualidade ideal dos dados da fila, as barreiras de correia retrátil são preferenciais para minimizar a oclusão das pessoas na fila e garantir que a localização das filas seja consistente ao longo do tempo.

![Fila de correia retrátil](./media/spatial-analysis/retractable-belt-queue.png)

Esse tipo de barreira é preferível às barreiras opacas para a formação de filas, a fim de maximizar a precisão dos insights do sistema.

Há dois tipos de filas: linear e zigue-zague.

A seguinte ilustração mostra as recomendações para filas lineares:

![Recomendação de fila linear](./media/spatial-analysis/camera-angle-linear-queue.png)

A ilustração a seguir fornece simulações para as exibições da câmera à esquerda e à direita de filas lineares. Observe que você pode montar a câmera no lado oposto da fila.

| Exibição à esquerda                          | Exibição à direita                          |  
| ---------------------------------- | ----------------------------------- |  
| ![Ângulo esquerdo da fila linear](./media/spatial-analysis/camera-angle-linear-left.png) | ![Ângulo direito da fila linear](./media/spatial-analysis/camera-angle-linear-right.png) |  

Para filas zigue-zague, evite colocar a câmera diretamente voltada para a direção da linha da fila, conforme mostrado na ilustração a seguir. Observe que cada um dos quatro exemplos de posições de câmera na ilustração fornece a exibição ideal com um desvio aceitável de +/- 15 graus em cada direção.

As ilustrações a seguir simulam a exibição de uma câmera posicionada nos locais ideais para uma fila em zigue-zague.

| Exibição 1        | Exibição 2        |  
| ------------- | ------------- |  
| ![Exibição 1](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![Exibição 2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| Exibição 3 |  Exibição 4 |  
| ---- | ----  |
| ![Exibição 3](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![Exibição 4](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>Filas orgânicas

As filas orgânicas se formam organicamente. Este estilo de fila será aceitável se as filas não formarem mais de duas a três pessoas e a linha se formar na definição da zona. Se o comprimento da fila for normalmente superior a duas ou três pessoas, recomendamos o uso de uma barreira de correia retrátil para ajudar a orientar a direção da fila e garantir que a linha se forme dentro da definição da zona.

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo Web de contagem de pessoas](spatial-analysis-web-app.md)
* [Configurar operações de análise espacial](./spatial-analysis-operations.md)
* [Registro em log e solução de problemas](spatial-analysis-logging.md)
* [Guia de posicionamento de zona e de linha](spatial-analysis-zone-line-placement.md)
