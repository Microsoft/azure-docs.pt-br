---
title: Usar transformações de imagem do SDK do sensor Kinect do Azure
description: Saiba como usar as funções de transformação de imagem do SDK do sensor do Azure Kinect.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.topic: conceptual
ms.date: 06/26/2019
keywords: Kinect, Azure, sensor, SDK, sistema de coordenadas, calibragem, projeto, desprojeto, transformação, RGB-d, nuvem de ponto
ms.openlocfilehash: df7f2aa13c0e9c0241494e96e720b30f3ff1d8a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276330"
---
# <a name="use-azure-kinect-sensor-sdk-image-transformations"></a>Usar transformações de imagem do SDK do sensor Kinect do Azure

Siga as funções específicas para usar e transformar imagens entre os sistemas de câmera coordenada no Azure Kinect DK.

## <a name="k4a_transformation-functions"></a>funções de k4a_transformation

 Todas as funções prefixadas com *k4a_transformation* operam em imagens inteiras. Eles exigem o identificador de transformação [k4a_transformation_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__transformation__t.html) obtido via [k4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10) e não são alocados via [k4a_transformation_destroy ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7d3ecaae66f26c1a89da9042b1bc6d44.html#ga7d3ecaae66f26c1a89da9042b1bc6d44). Você também pode consultar o exemplo de [transformação](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation) do SDK que demonstra como usar as três funções neste tópico.

As seguintes funções são cobertas:

* [`k4a_transformation_depth_image_to_color_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514)
* [`k4a_transformation_depth_image_to_color_camera_custom()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb)
* [`k4a_transformation_color_image_to_depth_camera()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29)
* [`k4a_transformation_depth_image_to_point_cloud()`](
https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70)

### <a name="k4a_transformation_depth_image_to_color_camera"></a>k4a_transformation_depth_image_to_color_camera

#### <a name="overview"></a>Visão geral

 A função [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) transforma o mapa de profundidade do ponto de vista da câmera de profundidade no ponto de vista da câmera colorida. Essa função foi projetada para produzir as chamadas imagens RGB-D, em que D representa um canal de imagem adicional registrando o valor de profundidade. Como visto na figura abaixo, a imagem colorida e a saída de [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) parecem como se tivessem sido tiradas do mesmo ponto de vista, ou seja, o ponto de vista da câmera colorida.

   ![Transformação de imagem](./media/how-to-guides/image-transformation.png)

#### <a name="implementation"></a>Implementação

 Essa função de transformação é mais complexa do que simplesmente chamar [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) para cada pixel. Ele distorce uma malha de triângulo da geometria da câmera de profundidade para a geometria da câmera colorida. A malha de triângulo é usada para evitar a geração de buracos na imagem de profundidade transformada. Um buffer Z garante que o occlusions seja manipulado corretamente. A aceleração de GPU está habilitada para essa função por padrão.

#### <a name="parameters"></a>Parâmetros

 Os parâmetros de entrada são a alça de transformação e uma imagem de profundidade. A resolução da imagem de profundidade deve corresponder à ```depth_mode``` especificada na criação da alça de transformação. Por exemplo, se o identificador de transformação foi criado usando o ```K4A_DEPTH_MODE_WFOV_UNBINNED``` modo 1024x1024, a resolução da imagem de profundidade deve ser 1024x1024 pixels. A saída é uma imagem de profundidade transformada que precisa ser alocada pelo usuário por meio da chamada de [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). A resolução da imagem de profundidade transformada deve corresponder à ```color_resolution``` especificada na criação da alça de transformação. Por exemplo, se a resolução de cor foi definida como `K4A_COLOR_RESOLUTION_1080P` , a resolução da imagem de saída deve ser 1920 x 1080 pixels. O stride da imagem de saída é definido `width * sizeof(uint16_t)` como, pois a imagem armazena valores de profundidade de 16 bits.

### <a name="k4a_transformation_depth_image_to_color_camera_custom"></a>k4a_transformation_depth_image_to_color_camera_custom

#### <a name="overview"></a>Visão geral

 A função [k4a_transformation_depth_image_to_color_camera_custom ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gac00dd00e7612a86382e3d0a130f276bb.html#gac00dd00e7612a86382e3d0a130f276bb) transforma o mapa de profundidade e uma imagem personalizada do ponto de vista da câmera de profundidade no ponto de vista da câmera colorida. Como uma extensão de [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514), essa função é projetada para produzir uma imagem personalizada correspondente para a qual cada pixel corresponde às coordenadas de pixel correspondentes da câmera colorida adicionais à imagem de profundidade transformada.

#### <a name="implementation"></a>Implementação

 Essa função de transformação produz a imagem de profundidade transformada da mesma maneira que [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Para transformar a imagem personalizada, essa função fornece opções de uso de interpolação linear ou interpolação de vizinho mais próximo. O uso de interpolação linear pode criar novos valores na imagem personalizada transformada. Usar a interpolação de vizinho mais próximo impedirá que os valores não presentes na imagem original apareçam na imagem de saída, mas resultarão em menos imagens suaves. A imagem personalizada deve ser um canal único de 8 ou 16 bits. A aceleração de GPU está habilitada para essa função por padrão.

#### <a name="parameters"></a>Parâmetros

 Os parâmetros de entrada são o identificador de transformação, uma imagem de profundidade, uma imagem personalizada e o tipo de interpolação. A imagem de profundidade e a resolução de imagem personalizada devem corresponder ao `depth_mode` especificado na criação da alça de transformação. Por exemplo, se o identificador de transformação foi criado usando o `K4A_DEPTH_MODE_WFOV_UNBINNED` modo 1024x1024, a resolução da imagem de profundidade e da imagem personalizada deve ser 1024x1024 pixels. O `interpolation_type` deve ser `K4A_TRANSFORMATION_INTERPOLATION_TYPE_LINEAR` ou `K4A_TRANSFORMATION_INTERPOLATION_TYPE_NEAREST` . A saída é uma imagem de profundidade transformada e uma imagem personalizada transformada que precisa ser alocada pelo usuário por meio da chamada de [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). A resolução da imagem de profundidade transformada e da imagem personalizada transformada deve corresponder ao `color_resolution` especificado na criação do identificador de transformação. Por exemplo, se a resolução de cor foi definida como `K4A_COLOR_RESOLUTION_1080P` , a resolução da imagem de saída deve ser 1920 x 1080 pixels. O stride da imagem de profundidade de saída é definido `width * sizeof(uint16_t)` como, pois a imagem armazena valores de profundidade de 16 bits. A imagem personalizada de entrada e a imagem personalizada transformada devem ser do formato `K4A_IMAGE_FORMAT_CUSTOM8` ou o `K4A_IMAGE_FORMAT_CUSTOM16` Stride da imagem correspondente deve ser definido adequadamente. 

### <a name="k4a_transformation_color_image_to_depth_camera"></a>k4a_transformation_color_image_to_depth_camera

#### <a name="overview"></a>Visão geral

 A função [k4a_transformation_color_image_to_depth_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf3a941f07bb0185cd7a72699a648fc29.html#gaf3a941f07bb0185cd7a72699a648fc29) transforma a imagem colorida do ponto de vista da câmera colorida no ponto de vista da câmera de profundidade (veja a figura acima). Ele pode ser usado para gerar imagens RGB-D.

#### <a name="implementation"></a>Implementação

 Para cada pixel do mapa de profundidade, a função usa o valor de profundidade do pixel para calcular a coordenada de subpixel correspondente na imagem colorida. Em seguida, pesquisamos o valor de cor nessa coordenada na imagem colorida. A interpolação bilinear é executada na imagem colorida para obter o valor de cor na precisão de subpixel. Um pixel que não tem uma leitura de profundidade associada é atribuído a um valor de BGRA de `[0,0,0,0]` na imagem de saída. A aceleração de GPU está habilitada para essa função por padrão. Como esse método produz buracos na imagem colorida transformada e não trata occlusions, é recomendável usar a função [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514) em vez disso.

#### <a name="parameters"></a>Parâmetros

Os parâmetros de entrada são a alça de transformação, uma imagem de profundidade e uma imagem colorida. As resoluções de imagens de profundidade e cor devem corresponder ao depth_mode e color_resolution especificados na criação do identificador de transformação. A saída é uma imagem de cor transformada que precisa ser alocada pelo usuário por meio da chamada de [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). A resolução da imagem de cor transformada deve corresponder ao depth_resolution especificado na criação do identificador de transformação. A imagem de saída armazena os valores de 4 8 bits que representam BGRA para cada pixel. Portanto, o stride da imagem é ```width * 4 * sizeof(uint8_t)``` . A ordem dos dados é de pixel intercalada, ou seja, valor azul-pixel 0, valor verde-pixel 0, valor vermelho-pixel 0, valor alfa-pixel 0, valor azul-pixel 1 e assim por diante.

### <a name="k4a_transformation_depth_image_to_point_cloud"></a>k4a_transformation_depth_image_to_point_cloud

#### <a name="overview"></a>Visão geral

A função [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70) converte um mapa de profundidade 2D tirado por uma câmera em uma nuvem de ponto 3D no sistema de coordenadas da mesma câmera. A câmera pode ser, portanto, a intensidade ou a câmera colorida.

#### <a name="implementation"></a>Implementação

 A função fornece resultados equivalentes à execução de [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) para cada pixel, mas é computacionalmente mais eficiente. Ao chamar [k4a_transformation_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga853a1a5b6d521bbdf523a69e890c4f10.html#ga853a1a5b6d521bbdf523a69e890c4f10), pré-calcular um chamado tabela de pesquisa XY que armazena os fatores de escala x e y para cada pixel de imagem. Ao chamar [k4a_transformation_depth_image_to_point_cloud ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7385eb4beb9d8892e8a88cf4feb3be70.html#ga7385eb4beb9d8892e8a88cf4feb3be70), obtemos a coordenada 3D x de um pixel multiplicando o fator de escala X do pixel pela coordenada Z do pixel. De forma análoga, a coordenada Y 3D é calculada por multiplicação com o fator de escala Y. O [exemplo de nuvem de ponto rápido](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud) do SDK demonstra como a tabela XY é computada. Os usuários podem seguir o código de exemplo para implementar sua própria versão dessa função, por exemplo, para acelerar seu pipeline de GPU.

#### <a name="parameters"></a>Parâmetros

 Os parâmetros de entrada são o identificador de transformação, um especificador de câmera e uma imagem de profundidade. Se o especificador de câmera for definido como profundidade, a resolução da imagem de profundidade deverá corresponder ao depth_mode especificado na criação da alça de transformação. Caso contrário, se o especificador for definido como a câmera colorida, a resolução deverá corresponder à resolução do color_resolution escolhido. O parâmetro de saída é uma imagem XYZ que precisa ser alocada pelo usuário por meio da chamada de [k4a_image_create ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga859554581bb97a620ff8e92a893e71ef.html#ga859554581bb97a620ff8e92a893e71ef). A resolução de imagem XYZ deve corresponder à resolução do mapa de profundidade de entrada. Armazenamos três valores de coordenadas de 16 bits assinados em milímetros para cada pixel. Portanto, o stride de imagem XYZ é definido como `width * 3 * sizeof(int16_t)` . A ordem dos dados é um pixel intercalado, ou seja, a coordenada X – pixel 0, Y-Coordinate – pixel 0, coordenada Z – pixel 0, X-Coordinate – pixel 1 e assim por diante. Se um pixel não puder ser convertido em 3D, a função atribuirá os valores `[0,0,0]` ao pixel.

## <a name="samples"></a>Exemplos

[Exemplo de transformação](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/transformation)

## <a name="next-steps"></a>Próximas etapas

Agora você sabe como usar as funções de transformação de imagem do SDK do sensor Kinect do Azure, você também pode aprender sobre

>[!div class="nextstepaction"]
>[Funções de calibração do SDK do sensor do Azure Kinect](use-calibration-functions.md)

Além disso, você pode examinar

[Sistemas de coordenadas](coordinate-systems.md)
