---
title: Resultados de acompanhamento do corpo do Azure Kinect
description: Saiba como obter resultados de rastreamento de corpo usando o SDK de acompanhamento de corpo Kinect do Azure.
author: qm13
ms.prod: kinect-dk
ms.author: quentinm
ms.reviewer: yijwan
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, corpo, acompanhamento, conjunta
ms.openlocfilehash: 1b62022242144d5db51455a32ac04b67c3e5dd7a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276406"
---
# <a name="get-body-tracking-results"></a>Obter resultados de acompanhamento do corpo

O SDK de acompanhamento de corpo usa um objeto de rastreador de corpo para processar as capturas do Azure Kinect DK e gera resultados de acompanhamento de corpo. Ele também mantém o status global do controlador, as filas de processamento e a fila de saída. Há três etapas no uso do rastreador de corpo:

- Criar um controlador
- Capturar imagens de profundidade e de IR usando o Azure Kinect DK
- Enfileirar a captura e exibir os resultados.

## <a name="create-a-tracker"></a>Criar um controlador


A primeira etapa no uso do corpo de rastreamento é criar um controlador e exigir a passagem da estrutura [k4a_calibration_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__calibration__t.html) calibragem do sensor. A calibragem do sensor pode ser consultada usando a função [k4a_device_get_calibration ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga4e43940d8d8db48da266c7a7842c8d78.html#ga4e43940d8d8db48da266c7a7842c8d78) do SDK do sensor Kinect do Azure.

```C
k4a_calibration_t sensor_calibration;
if (K4A_RESULT_SUCCEEDED != k4a_device_get_calibration(device, device_config.depth_mode, K4A_COLOR_RESOLUTION_OFF, &sensor_calibration))
{
    printf("Get depth camera calibration failed!\n");
    return 0;
}

k4abt_tracker_t tracker = NULL;
k4abt_tracker_configuration_t tracker_config = K4ABT_TRACKER_CONFIG_DEFAULT;
if (K4A_RESULT_SUCCEEDED != k4abt_tracker_create(&sensor_calibration, tracker_config, &tracker))
{
    printf("Body tracker initialization failed!\n");
    return 0;
}
```

## <a name="capture-depth-and-ir-images"></a>Capturar profundidade e imagens de IR

A captura de imagem usando o Azure Kinect DK é abordada na página [recuperar imagens](retrieve-images.md) .

>[!NOTE]
> `K4A_DEPTH_MODE_NFOV_UNBINNED` os `K4A_DEPTH_MODE_WFOV_2X2BINNED` modos ou são recomendados para melhor desempenho e precisão. Não use os `K4A_DEPTH_MODE_OFF` modos ou `K4A_DEPTH_MODE_PASSIVE_IR` .

Os modos do Azure Kinect DK com suporte são descritos na especificação de [hardware](hardware-specification.md) do Azure Kinect dk e [k4a_depth_mode_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___enumerations_ga3507ee60c1ffe1909096e2080dd2a05d.html#ga3507ee60c1ffe1909096e2080dd2a05d) enumerações.

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

## <a name="enqueue-the-capture-and-pop-the-results"></a>Enfileirar a captura e exibir os resultados

O rastreador mantém internamente uma fila de entrada e uma fila de saída para processar de maneira assíncrona as capturas do Azure Kinect DK com mais eficiência. Use a função [k4abt_tracker_enqueue_capture ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_ga093becd9bb4a63f5f4d56f58097a7b1e.html#ga093becd9bb4a63f5f4d56f58097a7b1e) para adicionar uma nova captura à fila de entrada. Use a função [k4abt_tracker_pop_result ()](https://microsoft.github.io/Azure-Kinect-Body-Tracking/release/1.x.x/group__btfunctions_gaaf446fb1579cbbe0b6af824ee0a7458b.html#gaaf446fb1579cbbe0b6af824ee0a7458b) o pop um resultado da fila de saída. O uso do valor de tempo limite é dependente do aplicativo e controla o tempo de espera do enfileiramento.

### <a name="real-time-processing"></a>Processamento em tempo real
Use esse padrão para aplicativos de thread único que precisam de resultados em tempo real e possam acomodar quadros descartados. O `simple_3d_viewer` exemplo localizado no [GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) é um exemplo de processamento em tempo real.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, 0);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result == K4A_WAIT_RESULT_FAILED)
{
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, 0);
if (pop_frame_result == K4A_WAIT_RESULT_SUCCEEDED)
{
    // Successfully popped the body tracking result. Start your processing
    ...

    k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
}
```

### <a name="synchronous-processing"></a>Processamento síncrono
Use esse padrão para aplicativos que não precisam de resultados em tempo real ou que não podem acomodar quadros descartados.

A taxa de transferência de processamento pode ser limitada.

O `simple_sample.exe` exemplo localizado no [GitHub Azure-Kinect-Samples](https://github.com/microsoft/Azure-Kinect-Samples) é um exemplo de processamento síncrono.

```C
k4a_wait_result_t queue_capture_result = k4abt_tracker_enqueue_capture(tracker, sensor_capture, K4A_WAIT_INFINITE);
k4a_capture_release(sensor_capture); // Remember to release the sensor capture once you finish using it
if (queue_capture_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Adding capture to tracker process queue failed!\n");
    break;
}

k4abt_frame_t body_frame = NULL;
k4a_wait_result_t pop_frame_result = k4abt_tracker_pop_result(tracker, &body_frame, K4A_WAIT_INFINITE);
if (pop_frame_result != K4A_WAIT_RESULT_SUCCEEDED)
{
    // It should never hit timeout or error when K4A_WAIT_INFINITE is set.
    printf("Error! Popping body tracking result failed!\n");
    break;
}
// Successfully popped the body tracking result. Start your processing
...

k4abt_frame_release(body_frame); // Remember to release the body frame once you finish using it
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
>[Acessar dados na estrutura corporal](access-data-body-frame.md)
