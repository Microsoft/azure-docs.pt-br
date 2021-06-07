---
title: Localize e abra o dispositivo Kinect do Azure
description: Saiba como localizar e abrir um dispositivo Kinect do Azure usando o SDK do Azure Kinect senor.
author: cedmonds
ms.author: cedmonds
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, sensor, SDK, profundidade, RGB, dispositivo, localizar, abrir
ms.openlocfilehash: 67fc93b924d5d663bb43098969c54d1975bd5895
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "85276335"
---
# <a name="find-then-open-the-azure-kinect-device"></a>Localize e abra o dispositivo Kinect do Azure

Este artigo descreve como você pode encontrar e, em seguida, abrir o Azure Kinect DK. O artigo explica como lidar com o caso em que há vários dispositivos conectados à sua máquina.

Você também pode consultar o [exemplo enumerador de SDK](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/enumerate) que demonstra como usar as funções neste artigo.

As seguintes funções são cobertas:
 * [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc)
 * [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113)
 * [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22)
 * [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423)

## <a name="discover-the-number-of-connected-devices"></a>Descobrir o número de dispositivos conectados

Primeiro, obtenha a contagem de dispositivos do Azure Kinect conectados no momento usando o [`k4a_device_get_installed_count()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_gaf7d19df0f73f8e4dfaa21e1b4b719ecc.html#gaf7d19df0f73f8e4dfaa21e1b4b719ecc) .

```C
uint32_t device_count = k4a_device_get_installed_count();

printf("Found %d connected devices:\n", device_count);
```

## <a name="open-a-device"></a>Abrir um dispositivo

Para obter informações sobre um dispositivo ou ler dados dele, primeiro você precisa abrir um identificador para o dispositivo usando o [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) .

```C
k4a_device_t device = NULL;

for (uint8_t deviceIndex = 0; deviceIndex < device_count; deviceIndex++)
{
    if (K4A_RESULT_SUCCEEDED != k4a_device_open(deviceIndex, &device))
    {
        printf("%d: Failed to open device\n", deviceIndex);
        continue;
    }

    ...

    k4a_device_close(device);
}
```

O `index` parâmetro de [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) indica qual dispositivo deve ser aberto se houver mais de um conectado. Se você esperar que apenas um único dispositivo seja conectado, poderá passar um argumento de `K4A_DEVICE_DEFAULT` ou 0 para indicar o primeiro dispositivo.

Sempre que você abrir um dispositivo, precisará chamar [`k4a_device_close()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga7a3931d9a690b3971caaac83b43f9423.html#ga7a3931d9a690b3971caaac83b43f9423) quando terminar de usar o identificador. Nenhum outro identificador pode ser aberto no mesmo dispositivo até que você tenha fechado o identificador.

## <a name="identify-a-specific-device"></a>Identificar um dispositivo específico

O pedido dispositivos enumerar por índice não será alterado até que os dispositivos sejam anexados ou desanexados. Para identificar um dispositivo físico, você deve usar o número de série do dispositivo.

Para ler o número de série do dispositivo, use a [`k4a_device_get_serialnum()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga798489af207ff1c99f2285ff6b08bc22.html#ga798489af207ff1c99f2285ff6b08bc22) função depois de abrir um identificador.

Este exemplo demonstra como alocar a quantidade certa de memória para armazenar o número de série.

```C
char *serial_number = NULL;
size_t serial_number_length = 0;

if (K4A_BUFFER_RESULT_TOO_SMALL != k4a_device_get_serialnum(device, NULL, &serial_number_length))
{
    printf("%d: Failed to get serial number length\n", deviceIndex);
    k4a_device_close(device);
    device = NULL;
    continue;
}

serial_number = malloc(serial_number_length);
if (serial_number == NULL)
{
    printf("%d: Failed to allocate memory for serial number (%zu bytes)\n", deviceIndex, serial_number_length);
    k4a_device_close(device);
    device = NULL;
    continue;
}

if (K4A_BUFFER_RESULT_SUCCEEDED != k4a_device_get_serialnum(device, serial_number, &serial_number_length))
{
    printf("%d: Failed to get serial number\n", deviceIndex);
    free(serial_number);
    serial_number = NULL;
    k4a_device_close(device);
    device = NULL;
    continue;
}

printf("%d: Device \"%s\"\n", deviceIndex, serial_number);
```

## <a name="open-the-default-device"></a>Abrir o dispositivo padrão

Na maioria dos aplicativos, haverá apenas um único Kinect do Azure DK anexado ao mesmo computador. Se você só precisa se conectar a um único dispositivo esperado, você pode chamar [`k4a_device_open()`](https://microsoft.github.io/Azure-Kinect-Sensor-SDK/master/group___functions_ga3d4eb5dfbf4d576d4978b66ea419f113.html#ga3d4eb5dfbf4d576d4978b66ea419f113) com `index` do `K4A_DEVICE_DEFAULT` para abrir o primeiro dispositivo.

```C
k4a_device_t device = NULL;
uint32_t device_count = k4a_device_get_installed_count();

if (device_count != 1)
{
    printf("Unexpected number of devices found (%d)\n", device_count);
    goto Exit;
}

if (K4A_RESULT_SUCCEEDED != k4a_device_open(K4A_DEVICE_DEFAULT, &device))
{
    printf("Failed to open device\n");
    goto Exit;
}
```

## <a name="next-steps"></a>Próximas etapas

>[!div class="nextstepaction"]
>[Recuperar imagens](retrieve-images.md)

>[!div class="nextstepaction"]
>[Recuperar exemplos de IMU](retrieve-imu-samples.md)

