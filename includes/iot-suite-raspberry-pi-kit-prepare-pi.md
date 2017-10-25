## <a name="prepare-your-raspberry-pi"></a>Preparar seu Raspberry Pi

### <a name="install-raspbian"></a>Instalar Raspbian

Se esta for a primeira vez que você estiver usando o Raspberry Pi, você precisa instalar o sistema operacional do Raspbian usando NOOBS no cartão SD incluído no kit. O [Guia de Software do Raspberry Pi Software guia][lnk-install-raspbian] descreve como instalar um sistema operacional em seu Raspberry Pi. Este tutorial presume que você instalou o sistema operacional Raspbian em seu Raspberry Pi.

> [!NOTE]
> O cartão SD incluído no [Microsoft Azure IoT Starter Kit do Raspberry Pi 3][lnk-starter-kits] já tem NOOBS instalado. Você pode inicializar o Raspberry Pi neste cartão e optar por instalar o sistema operacional do Raspbian.

### <a name="set-up-the-hardware"></a>Configurar o hardware

Este tutorial usa o sensor BME280 incluído no [Microsoft Azure IoT Starter Kit do Raspberry Pi 3][lnk-starter-kits] para gerar dados de telemetria. Ele usa um LED para indicar quando o Raspberry Pi processa uma invocação de método no painel da solução.

Os componentes da placa universal são:

- LED vermelho
- Resistor de 220 Ohm (vermelho, vermelho, marrom)
- Sensor BME280

O diagrama a seguir mostra como conectar seu hardware:

![Configuração de hardware para Raspberry Pi][img-connection-diagram]

A tabela a seguir resume as conexões do Raspberry Pi para os componentes na placa universal:

| Raspberry Pi            | Placa universal             |Cor         |
| ----------------------- | ---------------------- | ------------- |
| GND (Pino 14)            | Pin do LED - ve (18A)      | Roxo          |
| GPCLK0 (pino 7)          | Resistor (25A)         | Laranja          |
| SPI_CE0 (Pino 24)        | CS (39A)               | Azul          |
| SPI_SCLK (Pino 23)       | SCK (36A)              | Amarelo        |
| SPI_MISO (Pino 21)       | SDO (37A)              | Branco         |
| SPI_MOSI (Pino 19)       | SDI (38A)              | Verde         |
| GND (pino 6)             | GND (35A)              | Preto         |
| 3.3 V (Pino 1)           | 3Vo (34A)              | Vermelho           |

Para concluir a configuração de hardware, você precisa:

- Conectar seu Raspberry Pi à fonte de alimentação incluída no kit.
- Conectar seu Raspberry Pi à sua rede usando o cabo Ethernet incluído em seu kit. Como alternativa, você pode configurar [Conectividade sem fio][lnk-pi-wireless] para o Raspberry Pi.

Agora você concluiu a configuração de hardware do seu Raspberry Pi.

### <a name="sign-in-and-access-the-terminal"></a>Entre e acesse o terminal

Você tem duas opções para acessar um ambiente de terminal no seu Raspberry Pi:

- Se você tiver um teclado e um monitor conectado ao seu Raspberry Pi, você pode usar a GUI do Raspbian para acessar uma janela de terminal.

- Acesse a linha de comando em seu Raspberry Pi usando o SSH em seu computador desktop.

#### <a name="use-a-terminal-window-in-the-gui"></a>Use uma janela de terminal na GUI

As credenciais padrões para Raspbian são o nome de usuário **pi** e a senha **raspberry**. Na barra de tarefas na GUI, você pode iniciar o utilitário **Terminal** usando o ícone que se parece com um monitor.

#### <a name="sign-in-with-ssh"></a>Entre com o SSH

Você pode usar o SSH para acesso de linha de comando para o Raspberry Pi. O artigo [SSH (Secure Shell)][lnk-pi-ssh] descreve como configurar SSH em seu Raspberry Pi e como conectar-se a partir do [Windows][lnk-ssh-windows] ou [Linux e Mac OS][lnk-ssh-linux].

Entre com o nome de usuário **pi** e a senha **raspberry**.

#### <a name="optional-share-a-folder-on-your-raspberry-pi"></a>Opcional: Compartilhar uma pasta em seu Raspberry Pi

Opcionalmente, você talvez queira compartilhar uma pasta em seu Raspberry Pi com seu ambiente de área de trabalho. Compartilhar uma pasta permite que você use seu editor de texto preferido de área de trabalho (como [Visual Studio Code](https://code.visualstudio.com/) ou [Sublime Text](http://www.sublimetext.com/)) para editar arquivos em seu Raspberry Pi em vez de usar `nano` ou `vi`.

Para compartilhar uma pasta com o Windows, configure um servidor Samba no Raspberry Pi. Como alternativa, use o servidor interno [SFTP](https://www.raspberrypi.org/documentation/remote-access/) com um cliente SFTP em sua área de trabalho.

### <a name="enable-spi"></a>Habilitar SPI

Antes de executar o aplicativo de exemplo, você deve habilitar o barramento Serial Peripheral Interface (SPI) no Raspberry Pi. O Raspberry Pi se comunica com o dispositivo do sensor BME280 pelo barramento da SPI. Abra o arquivo de configuração executando o seguinte comando:

```sh
sudo nano /boot/config.txt
```

Localize a linha:

`#dtparam=spi=on`

- Para remover os comentários da linha, exclua o `#` no início.
- Salve suas alterações (**Ctrl-O**, **Enter**) e saia do editor (**Ctrl-X**).
- Para habilitar o SPI, reinicialize o Raspberry Pi. Reinicializar desconecta o terminal, você precisa entrar novamente quando reinicia o Raspberry Pi:

  ```sh
  sudo reboot
  ```


[img-connection-diagram]: media/iot-suite-raspberry-pi-kit-prepare-pi/rpi2_remote_monitoring.png

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/