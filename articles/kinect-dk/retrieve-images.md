---
title: Recuperar dados de imagem do Azure Kinect
description: Saiba como recuperar dados de imagem do Azure Kinect usando o SDK do sensor do Kinect.
author: cdedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, recuperar, sensor, câmera, SDK, profundidade, RGB, imagens, cor, captura, resolução, buffer
ms.openlocfilehash: fed5c7340d287d9103ba35f0fd3d80c0fff6e3ec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87538907"
---
# <a name="retrieve-azure-kinect-image-data"></a>Recuperar dados de imagem do Azure Kinect

Esta página fornece detalhes sobre como recuperar imagens do Kinect do Azure. O artigo demonstra como capturar e acessar imagens coordenadas entre a cor e a profundidade do dispositivo. Para acessar imagens, você deve primeiro abrir e configurar o dispositivo e, em seguida, pode capturar imagens.
Antes de configurar e capturar uma imagem, você deve [encontrar e abrir o dispositivo](find-then-open-device.md).

Você também pode consultar o [exemplo de streaming do SDK](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/streaming) que demonstra como usar as funções neste artigo.

As seguintes funções são cobertas:

- [`k4a_device_start_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a)
- [`k4a_device_get_capture()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4dac757a33657f4d3dbf1ae8b21c158a.html#ga4dac757a33657f4d3dbf1ae8b21c158a)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_image_get_buffer()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e)
- [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f)
- [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6)
- [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8)
  
## <a name="configure-and-start-the-device"></a>Configurar e iniciar o dispositivo

As duas câmeras disponíveis em seu dispositivo Kinect dão suporte a vários modos, resoluções e formatos de saída. Para obter uma lista completa, consulte as [especificações de hardware](hardware-specification.md)do kit de desenvolvimento do Azure Kinect.

A configuração de streaming é definida usando valores na [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) estrutura.

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;
config.depth_mode = K4A_DEPTH_MODE_NFOV_UNBINNED;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start device\n");
    goto Exit;
}
```

Depois que as câmeras forem iniciadas, elas continuarão a capturar dados até que [`k4a_device_stop_cameras()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4fa0e0a011a7105309ad97f081a5d6b8.html#ga4fa0e0a011a7105309ad97f081a5d6b8) o seja chamado ou o dispositivo seja fechado.

## <a name="stabilization"></a>Estabilização

Ao iniciar dispositivos usando o recurso de sincronização de vários dispositivos, é altamente recomendável fazer isso usando uma configuração de exposição fixa.
Com um conjunto de exposição manual, pode levar até oito capturas do dispositivo antes que as imagens e a taxa de quadros estabilizem. Com a exposição automática, pode levar até 20 capturas antes que as imagens e a taxa de quadros estabilizem.

## <a name="get-a-capture-from-the-device"></a>Obter uma captura do dispositivo

As imagens são capturadas do dispositivo de maneira correlacionada. Cada imagem capturada contém uma imagem de profundidade, uma imagem de IR, uma imagem colorida ou uma combinação de imagens.

Por padrão, a API retornará apenas uma captura depois de ter recebido todas as imagens solicitadas para o modo de streaming. Você pode configurar a API para retornar capturas parciais com apenas imagens de profundidade ou cores assim que elas estiverem disponíveis, limpando o [`synchronized_images_only`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t_a8208974f05d89fc1362c6a0900bdef4d.html#a8208974f05d89fc1362c6a0900bdef4d) parâmetro do [`k4a_device_configuration_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__device__configuration__t.html) .

```C
// Capture a depth frame
switch (k4a_device_get_capture(device, &capture, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a capture\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a capture\n");
    goto Exit;
}
```

Depois que a API retornar com êxito uma captura, você deverá chamar [`k4a_capture_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga0ed6f74ec403c3eac1b8ef3afb32cee6.html#ga0ed6f74ec403c3eac1b8ef3afb32cee6) quando tiver concluído o uso do objeto de captura.

## <a name="get-an-image-from-the-capture"></a>Obter uma imagem da captura

Para recuperar uma imagem capturada, chame a função apropriada para cada tipo de imagem. Um destes:

- [`k4a_capture_get_color_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga683e440b5f22215a2de58d7fa140488c.html#ga683e440b5f22215a2de58d7fa140488c)
- [`k4a_capture_get_depth_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gafa03513da96bf6b8c254fc11a04ee6d6.html#gafa03513da96bf6b8c254fc11a04ee6d6)
- [`k4a_capture_get_ir_image()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga1531c3fa76a7c174b8f2eab24de91794.html#ga1531c3fa76a7c174b8f2eab24de91794)

Você deve chamar [`k4a_image_release()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga27c81863b13fafc3934a32935a014e9f.html#ga27c81863b13fafc3934a32935a014e9f) em qualquer [`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) identificador retornado por essas funções quando terminar de usar a imagem.

## <a name="access-image-buffers"></a>Acessar buffers de imagem

[`k4a_image_t`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__image__t.html) tem muitas funções de acessador para obter propriedades da imagem.

Para acessar o buffer de memória da imagem, use [k4a_image_get_buffer](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga2ef070cf4e543cd0f726478af332546e.html#ga2ef070cf4e543cd0f726478af332546e).

O exemplo a seguir demonstra como acessar uma imagem de profundidade capturada. Esse mesmo princípio se aplica a outros tipos de imagem. No entanto, certifique-se de substituir a variável de tipo de imagem pelo tipo de imagem correto, como IR ou cor.

```C
// Access the depth16 image
k4a_image_t image = k4a_capture_get_depth_image(capture);
if (image != NULL)
{
    printf(" | Depth16 res:%4dx%4d stride:%5d\n",
            k4a_image_get_height_pixels(image),
            k4a_image_get_width_pixels(image),
            k4a_image_get_stride_bytes(image));

    // Release the image
    k4a_image_release(image);
}

// Release the capture
k4a_capture_release(capture);
```

## <a name="next-steps"></a>Próximas etapas

Agora você sabe como capturar e coordenar as imagens de câmeras entre a cor e a profundidade, usando seu dispositivo Kinect do Azure. Você também pode:

>[!div class="nextstepaction"]
>[Recuperar exemplos de IMU](retrieve-imu-samples.md)

>[!div class="nextstepaction"]
>[Acessar microfones](access-mics.md)
