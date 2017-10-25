## <a name="view-the-telemetry"></a>Exibir a Telemetria

O Raspberry Pi agora está enviando a telemetria para a solução de monitoramento remota. Você pode exibir a telemetria no painel de solução. Você também pode enviar mensagens para o Raspberry Pi a partir do painel da solução.

- Navegue para o painel da solução.
- Selecione o dispositivo na lista suspensa **Dispositivo para exibição**.
- A telemetria do Raspberry Pi é exibida no painel.

![Exibir a telemetria do Raspberry Pi][img-telemetry-display]

## <a name="initiate-the-firmware-update"></a>Iniciar a atualização do firmware

O processo de atualização de firmware baixa e instala uma versão atualizada do aplicativo cliente do dispositivo no Raspberry Pi. Para obter mais informações sobre o processo de atualização de firmware, consulte a descrição do padrão de atualização de firmware em [Visão geral do gerenciamento de dispositivos com o Hub IoT][lnk-update-pattern].

Você pode iniciar o processo de atualização de firmware invocando um método no dispositivo. Esse método é assíncrono e retorna assim que o processo de atualização é iniciado. O dispositivo usa propriedades relatadas para notificar a solução sobre o progresso da atualização.

Você invoca métodos em seu Raspberry Pi a partir do painel de solução. Quando o Raspberry Pi se conecta à solução de monitoramento remota pela primeira vez, ele envia informações sobre os métodos que ele suporta. 

[img-telemetry-display]: media/iot-suite-raspberry-pi-kit-view-telemetry-advanced/telemetry.png
[lnk-update-pattern]: ../articles/iot-hub/iot-hub-device-management-overview.md
