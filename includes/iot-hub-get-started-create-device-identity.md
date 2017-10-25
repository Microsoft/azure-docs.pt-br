## <a name="create-a-device-identity"></a>Criar uma identidade do dispositivo

Nesta seção, você use uma ferramenta do Node.js chamada [iothub-explorer][iot-hub-explorer] para criar uma identidade de dispositivo para este tutorial. As IDs de Dispositivo diferenciam maiúsculas de minúsculas.

1. Execute o seguinte em seu ambiente de linha de comando:

    `npm install -g iothub-explorer@latest`

1. Em seguida, execute o comando para entrar em seu hub. Substitua `{iot hub connection string}` pela cadeia de conexão de Hub IoT copiada anteriormente:

    `iothub-explorer login "{iot hub connection string}"`

1. Finalmente, crie uma nova identidade de dispositivo chamada `myDeviceId` com o comando:

    `iothub-explorer create myDeviceId --connection-string`

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Anote a cadeia de conexão do dispositivo no resultado. Essa cadeia de conexão é usada pelo aplicativo do dispositivo para se conectar ao seu Hub IoT como um dispositivo.

![][img-identity]

Consulte [Introdução ao Hub IoT][lnk-getstarted] para criar identidades de dispositivos por meio de programação.

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
