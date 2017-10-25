## <a name="configure-the-nodejs-simulated-device"></a>Configurar o dispositivo Node.js simulado
1. No painel de monitoramento remoto, clique em **+ Adicionar um dispositivo** e adicione um *dispositivo personalizado*. Anote o nome de host do Hub IoT, id do dispositivo e chave do dispositivo. Você precisará deles mais tarde neste tutorial ao preparar o aplicativo cliente de dispositivo remote_monitoring.js.
2. Certifique-se de que o Node.js versão 0.12.x ou posterior esteja instalado no computador de desenvolvimento. Execute `node --version` em um prompt de comando ou em um shell para verificar a versão. Para obter informações sobre como usar um gerenciador de pacotes para instalar o Node.js no Linux, confira [Instalação do Node.js por meio do gerenciador de pacotes][node-linux].
3. Após a instalação do Node.js, clone a versão mais recente do repositório [azure-iot-sdks][lnk-github-repo] em seu computador de desenvolvimento. Sempre use a ramificação **mestre** para a versão mais recente das bibliotecas e exemplos.
4. Em sua cópia local do repositório [azure-iot-sdks][lnk-github-repo], copie os dois arquivos a seguir da pasta node/device/samples em uma pasta vazia em seu computador de desenvolvimento:
   
   * packages.json
   * remote_monitoring.js
5. Abra o arquivo remote_monitoring.js e procure a seguinte definição de variável:
   
    ```
    var connectionString = "[IoT Hub device connection string]";
    ```
6. Substitua a **[cadeia de conexão de dispositivo do Hub IoT]** pela cadeia de conexão do seu dispositivo. Use os valores de seu nome de host do Hub IoT, ID do dispositivo e chave de dispositivo que você anotou na etapa 1. Uma cadeia de conexão do dispositivo tem o seguinte formato:
   
    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```
   
    Se o nome de host do Hub IoT for **contoso** e a ID de dispositivo for **mydevice**, a cadeia de conexão se parecerá com este código:
   
    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```
7. Salve o arquivo. Execute os seguintes comandos em um shell ou prompt de comando na pasta que contém os arquivos para instalar os pacotes necessários e, em seguida, execute o aplicativo de exemplo:
   
    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Observe a telemetria dinâmica em ação
O painel mostra a telemetria de temperatura e umidade dos dispositivos existentes simulados:

![O painel padrão][image1]

Se você selecionar dispositivo simulado Node.js executado na seção anterior, verá a telemetria de temperatura, umidade e temperatura externa:

![Adicionar temperatura externa ao painel][image2]

A solução de monitoramento remoto detecta automaticamente o tipo de telemetria adicional de temperatura externa e o adiciona ao gráfico no painel.

[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdk-node
[image1]: media/iot-suite-send-external-temperature/image1.png
[image2]: media/iot-suite-send-external-temperature/image2.png