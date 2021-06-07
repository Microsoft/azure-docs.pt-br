---
title: Recuperar exemplos do IMU do Azure Kinect
description: Saiba como recuperar exemplos do Azure Kinect IMU usando o SDK do Kinect do Azure.
author: qm13
ms.author: cedmonds
ms.date: 06/26/2019
ms.prod: kinect-dk
ms.topic: conceptual
keywords: Kinect, Azure, configurar, profundidade, cor, RGB, câmera, sensor, SDK, IMU, sensor de movimento, movimento, giroscópio, giro, acelerômetro, FPS
ms.openlocfilehash: 649dd5b9de62d43d59e74d53adff1ec7de8dfd32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85276343"
---
# <a name="retrieve-azure-kinect-imu-samples"></a>Recuperar exemplos do IMU do Azure Kinect

O dispositivo Kinect do Azure fornece acesso a IMUs (unidades de movimento inércia), incluindo os tipos acelerômetro e giroscópio. Para acessar exemplos do IMUs, você deve primeiro abrir e configurar o dispositivo e, em seguida, capturar os dados do IMU. Para obter mais informações, consulte [Localizar e abrir dispositivo](find-then-open-device.md).

Os exemplos de IMU são gerados com uma frequência muito maior do que as imagens. Os exemplos são relatados ao host em uma taxa mais baixa do que são amostrados. Ao aguardar um exemplo de IMU, vários exemplos frequentemente serão disponibilizados ao mesmo tempo.

Consulte a [especificação de hardware](hardware-specification.md) do Azure Kinect DK para obter detalhes sobre a taxa de relatórios do IMU.

## <a name="configure-and-start-cameras"></a>Configurar e iniciar câmeras

> [!NOTE]
> Os sensores IMU só podem funcionar quando as câmeras Color e/ou Depth estiverem em execução. Os sensores IMU não podem trabalhar sozinhos.

Para iniciar as câmeras, use [k4a_device_start_cameras ()](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaad7a85e1e5471810262442fc4a8e217a.html#gaad7a85e1e5471810262442fc4a8e217a).

```C
k4a_device_configuration_t config = K4A_DEVICE_CONFIG_INIT_DISABLE_ALL;
config.camera_fps = K4A_FRAMES_PER_SECOND_30;
config.color_format = K4A_IMAGE_FORMAT_COLOR_MJPG;
config.color_resolution = K4A_COLOR_RESOLUTION_2160P;

if (K4A_RESULT_SUCCEEDED != k4a_device_start_cameras(device, &config))
{
    printf("Failed to start cameras\n");
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_start_imu(device))
{
    printf("Failed to start imu\n");
    goto Exit;
}
```

## <a name="access-imu-samples"></a>Acessar exemplos de IMU

 Cada [k4a_imu_sample_t](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/structk4a__imu__sample__t.html#details) contém uma leitura de acelerômetro e giroscópio capturada quase ao mesmo tempo.

Você pode obter os exemplos de IMU no mesmo thread em que obtém capturas de imagem ou em threads separados.

Para recuperar exemplos de IMU assim que estiverem disponíveis, talvez você queira chamar [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) em seu próprio thread. A API também tem uma fila interna suficiente para permitir que você verifique apenas os exemplos depois que cada captura de imagem é retornada.

Como há algumas filas internas de exemplos de IMU, você pode usar o seguinte padrão sem remover os dados:

1. Aguarde uma captura, em qualquer taxa de quadros.
2. Processar a captura.
3. Recupere todos os exemplos de IMU em fila.
4. Repetir a espera na próxima captura.

Para recuperar todos os exemplos de IMU em fila no momento, você pode chamar [`k4a_device_get_imu_sample()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga8e5913b3bb94a453c7143bbd6e399a0e.html#ga8e5913b3bb94a453c7143bbd6e399a0e) com um `timeout_in_ms` de 0 em um loop até que a função retorne `K4A_WAIT_RESULT_TIMEOUT` . `K4A_WAIT_RESULT_TIMEOUT` indica que não há amostras em fila e que nenhuma tenha chegado no tempo limite especificado.

## <a name="usage-example"></a>Exemplo de uso

```C
k4a_imu_sample_t imu_sample;

// Capture a imu sample
switch (k4a_device_get_imu_sample(device, &imu_sample, TIMEOUT_IN_MS))
{
case K4A_WAIT_RESULT_SUCCEEDED:
    break;
case K4A_WAIT_RESULT_TIMEOUT:
    printf("Timed out waiting for a imu sample\n");
    continue;
    break;
case K4A_WAIT_RESULT_FAILED:
    printf("Failed to read a imu sample\n");
    goto Exit;
}

// Access the accelerometer readings
if (imu_sample != NULL)
{
    printf(" | Accelerometer temperature:%.2f x:%.4f y:%.4f z: %.4f\n",
            imu_sample.temperature,
            imu_sample.acc_sample.xyz.x,
            imu_sample.acc_sample.xyz.y,
            imu_sample.acc_sample.xyz.z);
}

```

## <a name="next-steps"></a>Próximas etapas

Agora você sabe como trabalhar com exemplos do IMU, você também pode
>[!div class="nextstepaction"]
>[Acessar dados de entrada do microfone](access-mics.md)
