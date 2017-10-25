## <a name="view-the-telemetry"></a>Exibir a Telemetria

O Raspberry Pi agora está enviando a telemetria para a solução de monitoramento remota. Você pode exibir a telemetria no painel de solução. Você também pode enviar mensagens para o Raspberry Pi a partir do painel da solução.

- Navegue para o painel da solução.
- Selecione o dispositivo na lista suspensa **Dispositivo para exibição**.
- A telemetria do Raspberry Pi é exibida no painel.

![Exibir a telemetria do Raspberry Pi][img-telemetry-display]

## <a name="act-on-the-device"></a>Atuar no dispositivo

No painel de solução, você pode invocar métodos em seu Raspberry Pi. Quando o Raspberry Pi se conecta a solução de monitoramento remota, ele envia informações sobre os métodos que ele suporta.

- No painel de solução, clique em **Dispositivos** para visitar a página **Dispositivos**. Selecione seu Raspberry Pi na **Lista de Dispositivos**. Depois, escolha **Métodos**:

    ![Listar dispositivos no painel][img-list-devices]

- Na página **Invocar Método** escolha **LightBlink** na lista suspensa **Método**.

- Escolha **InvokeMethod**. O LED conectado ao Raspberry Pi pisca várias vezes. O aplicativo no Raspberry Pi envia uma confirmação de volta para o painel da solução:

    ![Mostrar o histórico do método][img-method-history]

- Você pode ligar e desligar o LED usando o método **ChangeLightStatus** com **LightStatusValue** definido como **1** para ligar ou **0** para desligar.

> [!WARNING]
> Se você deixar a solução de monitoramento remoto em execução em sua conta do Azure, você receberá uma cobrança pelo tempo de execução. Para saber mais sobre como reduzir o consumo durante a execução da solução de monitoramento remoto, confira [Configuração de soluções pré-configuradas do Azure IoT Suite para fins de demonstração][lnk-demo-config]. Exclua a solução pré-configurada de sua conta do Azure quando terminar de usá-la.


[img-telemetry-display]: media/iot-suite-raspberry-pi-kit-view-telemetry/telemetry.png
[img-list-devices]: media/iot-suite-raspberry-pi-kit-view-telemetry/listdevices.png
[img-method-history]: media/iot-suite-raspberry-pi-kit-view-telemetry/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md