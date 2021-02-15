---
title: Usar funções de calibragem Kinect do Azure
description: Saiba como usar as funções de calibragem para o Azure Kinect DK.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, sistema de coordenadas, calibragem, funções, câmera, intrínseca, extrínsecos, projeto, desprojeto, transformação, RGB-d, nuvem de ponto
ms.openlocfilehash: a501765222154bde4ed35e878a9d18042893fca2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276342"
---
# <a name="use-azure-kinect-calibration-functions"></a>Usar funções de calibragem Kinect do Azure

As funções de calibragem permitem a transformação de pontos entre os sistemas de coordenadas de cada sensor no dispositivo Kinect do Azure. Aplicativos que exigem conversão de imagens inteiras podem aproveitar as operações aceleradas disponíveis em [funções de transformação](use-image-transformation.md).

## <a name="retrieve-calibration-data"></a>Recuperar dados de calibragem

É necessário recuperar a calibragem do dispositivo para executar as transformações do sistema de coordenadas. Os dados de calibragem são armazenados no tipo de dados [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) . Ele é obtido do dispositivo por meio da função [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78). Os dados de calibragem não são específicos apenas para cada dispositivo, mas também para o modo de operação das câmeras. Portanto [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) requer os `depth_mode` `color_resolution` parâmetros e como entrada.

### <a name="opencv-compatibility"></a>Compatibilidade do OpenCV

Os parâmetros de calibragem são compatíveis com [OpenCV](https://opencv.org/). Para obter mais informações sobre os parâmetros individuais de calibragem da câmera, consulte também [documentação do OpenCV](https://docs.opencv.org/3.2.0/d9/d0c/group__calib3d.html#gga7041b2a9c8f9f8ee93a2796981bc5546a204766e24f2e413e7a7c9f8b9e93f16c). Consulte também o [exemplo de compatibilidade OpenCV](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility) do SDK que demonstra a conversão entre o tipo de [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html#details) e as estruturas de dados OpenCV correspondentes.

## <a name="coordinate-transformation-functions"></a>Coordenar funções de transformação

A figura a seguir mostra os diferentes sistemas de coordenadas do Azure Kinect, bem como as funções a serem convertidas entre eles. Nós omitemos os sistemas de coordenadas 3D do giroscópio e do acelerômetro para manter a figura simples.

   ![Transformação de coordenadas](./media/how-to-guides/coordinate-transformation.png)

Comentário sobre distorção de lentes: coordenadas 2D sempre se referem à imagem distorcida no SDK. O [exemplo de distorção](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort) do SDK demonstra a distorção de imagem. Em geral, os pontos 3D nunca serão afetados pela distorção de lentes.

### <a name="convert-between-3d-coordinate-systems"></a>Converter entre sistemas de coordenadas 3D

A função [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e) converte um ponto 3D do sistema de coordenadas de origem em um ponto 3D do sistema de coordenadas de destino usando a calibragem extrínsecos da câmera. A origem e o destino podem ser definidos para qualquer um dos quatro sistemas de coordenadas 3D, ou seja, câmera colorida, câmera de profundidade, giroscópio ou acelerômetro. Se a origem e o destino forem idênticos, o ponto 3D de entrada não modificado será retornado como saída.

### <a name="convert-between-2d-and-3d-coordinate-systems"></a>Converter entre sistemas de coordenadas 2D e 3D

A função [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) converte um ponto 3D do sistema de coordenadas de origem em uma coordenada de pixel 2D da câmera de destino. Essa função é geralmente conhecida como função de projeto. Embora a origem possa ser definida para qualquer um dos quatro sistemas de coordenadas 3D, o destino deve ser a intensidade ou a câmera colorida. Se a origem e o destino forem diferentes, o ponto 3D de entrada será convertido no sistema de coordenadas 3D da câmera de destino usando [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e). Depois que o ponto 3D é representado no sistema de coordenadas da câmera de destino, as coordenadas de pixels 2D correspondentes são computadas usando a calibragem intrínseca da câmera de destino. Se um ponto 3D ficar fora da área visível da câmera de destino, o valor válido será definido como 0.

A função [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) converte uma coordenada de pixel 2D da câmera de origem em um ponto 3D do sistema de coordenadas da câmera de destino. A origem deve ser uma câmera de cor ou profundidade. O destino pode ser definido como qualquer um dos quatro sistemas de coordenadas 3D. Além da coordenada de pixels 2D, o valor de profundidade do pixel (em milímetros) na imagem da câmera de origem é necessário como uma entrada para a função, uma maneira de derivar o valor de profundidade na geometria da câmera colorida é usar a função [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). A função computa o raio 3D que leva do ponto focal da câmera de origem por meio da coordenada de pixel especificada usando a calibragem intrínseca da câmera de origem. Em seguida, o valor de profundidade é usado para localizar o local exato do ponto 3D nesse raio. Essa operação é geralmente conhecida como função unproject. Se as câmeras de origem e de destino forem diferentes, a função transformará o ponto 3D no sistema de coordenadas do destino via [k4a_calibration_3d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaacd1eab997ef964b5e436afa5295726e.html#gaacd1eab997ef964b5e436afa5295726e). Se uma coordenada de pixel 2D cair da área visível da câmera de origem, o valor válido será definido como 0.

### <a name="converting-between-2d-coordinate-systems"></a>Convertendo entre sistemas de coordenadas 2D

A função [k4a_calibration_2d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3b6bf6dedbfe67468e2f895dcce68ed4.html#ga3b6bf6dedbfe67468e2f895dcce68ed4) converte uma coordenada de pixel 2D da câmera de origem em uma coordenada de pixel 2D da câmera de destino. A origem e o destino devem ser definidos para a câmera colorida ou de profundidade. A função requer o valor de profundidade do pixel (em milímetros) na imagem da câmera de origem como uma entrada, uma maneira de derivar o valor de profundidade na geometria da câmera colorida é usar a função [k4a_transformation_depth_image_to_color_camera ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafacffb5f781a9c2df30d4a16241cd514.html#gafacffb5f781a9c2df30d4a16241cd514). Ele chama [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880de) para converter em um ponto 3D do sistema de câmera de origem. Em seguida, ele chama [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) para converter em uma coordenada de pixel 2D da imagem da câmera de destino. O valor válido é definido como 0, se [k4a_calibration_2d_to_3d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga664602bdb48dab38117a6c1d14b880de.html#ga664602bdb48dab38117a6c1d14b880ded) ou [k4a_calibration_3d_to_2d ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ed8b51d727425caa942aab190fc2ba9.html#ga2ed8b51d727425caa942aab190fc2ba9) retornar um resultado inválido.

## <a name="related-samples"></a>Exemplos relacionados

- [Exemplo de compatibilidade do OpenCV](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/opencv_compatibility)
- [Exemplo de distorção](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/undistort)
- [Exemplo de nuvem de ponto rápido](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/fastpointcloud)

## <a name="next-steps"></a>Próximas etapas

Agora você sabe sobre calibragens de câmera, você também pode aprender a
>[!div class="nextstepaction"]
>[Capturar sincronização do dispositivo](capture-device-synchronization.md)

Além disso, você pode examinar

[Sistemas de coordenadas](coordinate-systems.md)
