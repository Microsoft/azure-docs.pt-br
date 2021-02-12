---
title: Posicionamento da câmera de análise espacial
titleSuffix: Azure Cognitive Services
description: Saiba como configurar uma câmera para uso com análise espacial
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 849afe54125588cb7664ee221e4f229e83be8f96
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185471"
---
# <a name="camera-placement-guide"></a>Guia de posicionamento da câmera

Este artigo fornece recomendações de posicionamento da câmera para análise espacial (visualização pública). Ele inclui diretrizes gerais, bem como recomendações específicas para altura, ângulo e distância de ponto focal para todas as operações incluídas. 

> [!NOTE]
> Este guia foi projetado para a câmera M3045-V do Axis. Esta câmera usará a resolução 1920 x 1080, o campo horizontal de 106 graus de exibição, o campo vertical de 59 graus de exibição e um comprimento focal de 2,8 mm fixo. Os princípios a seguir serão aplicados a todas as câmeras, mas as diretrizes específicas sobre a altura da câmera e a distância do ponto focal da câmera deverão ser ajustadas para uso com outras câmeras. 

## <a name="general-guidelines"></a>Diretrizes gerais

Considere as seguintes diretrizes gerais ao posicionar câmeras para análise espacial:

* **Altura da iluminação.** Coloque as câmeras abaixo dos acessórios de iluminação para que os acessórios não bloqueiem as câmeras.
* **Se há obstruções.** Para evitar a obstrução de exibições de câmera, anote as obstruções, como polos, pôsteres, prateleiras, paredes e câmeras de LP existentes.
* **Iluminação de luz ambiental.** A iluminação traseira externa afeta a qualidade da imagem da câmera. Para evitar sérias condições de iluminação, Evite direcionar câmeras em portas de vidro e janelas voltadas para o público.
* **Regras e regulamentos de privacidade local.** As normas locais podem restringir as câmeras que podem ser capturadas. Certifique-se de entender as regras e regulamentos locais antes de colocar câmeras.
* **Criando estrutura.** O HVAC, os antiincêndios e a fiação existente podem limitar a montagem rígida de câmeras.
* **Gerenciamento de cabos.** Verifique se você pode rotear um cabo Ethernet de locais de montagem de câmera planejados para a opção PoE (Power over Internet).

## <a name="height-focal-point-distance-and-angle"></a>Altura, distância do ponto focal e ângulo

Você precisa considerar três coisas ao decidir como instalar uma câmera para análise espacial:
- Altura da câmera
- Distância do ponto de câmera para o focal
- O ângulo da câmera em relação ao plano de chão

Também é importante saber a direção que a maioria das pessoas movimentam (direção de movimentação da pessoa) em relação ao campo da câmera de exibição, se possível. Essa direção é importante para o desempenho do sistema.

![Imagem de uma pessoa percorrendo uma direção](./media/spatial-analysis/person-walking-direction.png)

A ilustração a seguir mostra a exibição de elevação para direção de movimentação de pessoas.

![Exibição de elevação e de plano](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>Altura da câmera

Em geral, as câmeras devem ser montadas a 12-14 pés a partir do zero. Para detecção de máscara facial, recomendamos que as câmeras sejam montadas em 8-12 pés a partir do zero. Ao planejar a montagem da câmera nesse intervalo, considere as obstruções (por exemplo: prateleiras, luzes deslocadas, sinais de cima e telas) que podem afetar a exibição da câmera e, em seguida, ajuste a altura conforme necessário.

## <a name="camera-to-focal-point-distance"></a>Distância do ponto de câmera para o focal

A _distância da câmera para o ponto focal_ é a distância linear do ponto focal (ou do centro da imagem da câmera) à câmera medida no chão.

![De câmera para ponto focal](./media/spatial-analysis/camera-focal-point.png)

Essa distância é medida no plano de chão.

![Como a distância do ponto de câmera para focalidade é medida a partir do chão](./media/spatial-analysis/camera-focal-point-floor-plane.png)

Acima, ele tem a seguinte aparência:

![Como a distância do ponto de câmera a focalidade é medida acima](./media/spatial-analysis/camera-focal-point-above.png)

Use a tabela abaixo para determinar a distância da câmera do ponto focal com base em alturas de montagem específicas. Essas distâncias são para o posicionamento ideal. Observe que a tabela fornece orientação abaixo da recomendação de 12 '-14 ', já que alguns tetos podem limitar a altura. Para detecção de máscara facial, a distância recomendada da câmera-para-Focal Point (min/max) é 4 '-10 ' para a altura da câmera entre 8 ' e 12 '.

| Altura da câmera | A distância da câmera para o ponto focal (mín/máx.) |  
| ------------- | ---------------------------------------- |  
| 8            | 10 '-13 '                                  |  
| 254           | 7 '-13 '                                   |  
| 12           | 10 '-17 '                                  |  
| 140           | 11 '-18 '                                  |  
| 16           | 12 '-22 '                                  |  
| 20,00           | 15 '-30 '                                  |  

A ilustração a seguir simula as exibições da câmera das distâncias mais próximas e mais distantes da câmera para o ponto focal.

| Mais próximo                                      | Mais distante                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![Distância mais próxima de câmera para ponto focal](./media/spatial-analysis/focal-point-closest.png) | ![Distância mais distante da câmera para o ponto focal](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>Intervalos de montagem do ângulo da câmera

Esta seção descreve os intervalos de montagem de ângulo de câmera aceitáveis. Esses intervalos de montagem mostram o intervalo aceitável para posicionamento ideal.

### <a name="line-configuration"></a>Configuração de linha

A tabela a seguir mostra as recomendações para câmeras configuradas para a operação **cognitivaservices. Vision. spatialanalysis-personcrossingline** . Para detecção de máscara facial, +/-30 graus é o ângulo de montagem da câmera ideal para a altura da câmera entre 8 e 12 '.

| Altura da câmera | Distância do ponto de câmera para o focal | Ângulo de montagem de câmera ideal (mín/máx.) |  
| ------------- | ------------------------------ | ------------------------------------------ |  
| 8            | 99                             | +/-40 °                                     |  
| 254           | 254                            | +/-30 °                                     |  
| 12           | 13                            | +/-20 °                                     |  
| 16           | anos                            | +/-10 °                                     |  
| 20,00           | 22                            | +/-10 °                                     |  

A ilustração a seguir simula as exibições de câmera usando as recomendações de ângulo de montagem mais à esquerda (-) e mais à direita (+) para usar **cognitivaservices. Vision. spatialanalysis-personcrossingline** para fazer a contagem de entrada de forma de porta.

| Exibição mais à esquerda                | Exibição mais à direita                |  
| ---------------------------- | ----------------------------- |  
| ![Ângulo da câmera mais à esquerda](./media/spatial-analysis/camera-angle-left.png) | ![Ângulo da câmera mais à direita](./media/spatial-analysis/camera-angle-right.png) |  

A ilustração a seguir mostra o posicionamento da câmera e os ângulos de montagem de uma exibição vista.

![Visão geral](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>Configuração de zona

Recomendamos que você coloque câmeras em 10 pés ou mais acima para garantir que a área coberta seja grande o suficiente. 

Quando a zona está ao lado de um obstáculo como uma parede ou prateleira, monte câmeras na distância especificada do destino dentro do intervalo de ângulo de 120 graus aceitável, conforme mostrado na ilustração a seguir.

![Ângulo de câmera aceitável](./media/spatial-analysis/camera-angle-acceptable.png)

A ilustração a seguir fornece simulações para as exibições da câmera esquerda e direita de uma área ao lado de uma prateleira.

| Modo de exibição à esquerda        | Modo de exibição à direita        |  
| ---------------- | ----------------- |  
| ![Modo de exibição à esquerda](./media/spatial-analysis/end-cap-left.png) | ![Modo de exibição à direita](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>Filas

As habilidades **cognitivaservices. Vision. spatialanalysis-personcount**, **cognitivaservices. Vision. spatialanalysis-persondistance** e **cognitivaservices. Vision. spatialanalysis-personcrossingpolygon** podem ser usadas para monitorar filas. Para qualidade de dados de fila ideal, as barreiras de correia remanejáveis são preferenciais para minimizar o oclusão das pessoas na fila e garantir que o local das filas seja consistente ao longo do tempo.

![Fila de correia remanejável](./media/spatial-analysis/retractable-belt-queue.png)

Esse tipo de barreira é preferível a barreiras opacas para a formação da fila para maximizar a precisão das informações do sistema.

Há dois tipos de filas: linear e ziguezague-ziguezague.

A ilustração a seguir mostra as recomendações para filas lineares:

![Recomendação de fila linear](./media/spatial-analysis/camera-angle-linear-queue.png)

A ilustração a seguir fornece simulações para as exibições de câmera esquerda e direita de filas lineares. Observe que você pode montar a câmera no lado oposto da fila.

| Modo de exibição à esquerda                          | Modo de exibição à direita                          |  
| ---------------------------------- | ----------------------------------- |  
| ![Ângulo esquerdo da fila linear](./media/spatial-analysis/camera-angle-linear-left.png) | ![Ângulo direito para fila linear](./media/spatial-analysis/camera-angle-linear-right.png) |  

Para filas ziguezague-ziguezague, é melhor evitar colocar a câmera diretamente voltada para a direção da linha da fila, conforme mostrado na ilustração a seguir. Observe que cada uma das quatro posições de câmera de exemplo na ilustração fornece a exibição ideal com um desvio aceitável de +/-15 graus em cada direção.

As ilustrações a seguir simulam a exibição de uma câmera colocada nos locais ideais para uma fila ziguezague-ziguezague.

| Exibição 1        | Exibição 2        |  
| ------------- | ------------- |  
| ![Exibição 1](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![Exibição 2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| Exibição 3 |  Exibição 4 |  
| ---- | ----  |
| ![Exibição 3](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![Exibição 4](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>Filas orgânicas

Linhas de fila orgânicas formam orgânicamente. Esse estilo de fila será aceitável se as filas não formarem mais de 2-3 pessoas e os formulários de linha dentro da definição de zona. Se o comprimento da fila for normalmente mais de 2-3 pessoas, é recomendável usar uma barreira de correia remanejável para ajudar a orientar a direção da fila e garantir os formulários de linha dentro da definição de zona.

## <a name="next-steps"></a>Próximas etapas

* [Implantar um aplicativo Web de contagem de pessoas](spatial-analysis-web-app.md)
* [Configurar operações de análise espacial](./spatial-analysis-operations.md)
* [Registro em log e solução de problemas](spatial-analysis-logging.md)
* [Guia de posicionamento de zona e linha](spatial-analysis-zone-line-placement.md)
