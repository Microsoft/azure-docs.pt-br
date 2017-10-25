## <a name="prepare-your-raspberry-pi"></a>Preparar seu Raspberry Pi

### <a name="install-raspbian"></a>Instalar Raspbian

Se esta for a primeira vez que você estiver usando o Raspberry Pi, você precisa instalar o sistema operacional do Raspbian usando NOOBS no cartão SD incluído no kit. O [Guia de Software do Raspberry Pi Software guia][lnk-install-raspbian] descreve como instalar um sistema operacional em seu Raspberry Pi. Este tutorial presume que você instalou o sistema operacional Raspbian em seu Raspberry Pi.

> [!NOTE]
> O cartão SD incluído no [Microsoft Azure IoT Starter Kit do Raspberry Pi 3][lnk-starter-kits] já tem NOOBS instalado. Você pode inicializar o Raspberry Pi neste cartão e optar por instalar o sistema operacional do Raspbian.

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

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/