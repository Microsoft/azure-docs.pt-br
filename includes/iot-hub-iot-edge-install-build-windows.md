## <a name="install-the-prerequisites"></a>Instalar os pré-requisitos

1. Instale o [Visual Studio 2015 ou 2017](https://www.visualstudio.com). Você poderá usar a Community Edition gratuita se atender aos requisitos de licenciamento. Inclua o Visual C++ e o Gerenciador de Pacotes do NuGet.

1. Instale o [git](http://www.git-scm.com) e assegure-se de que você pode executar git.exe da linha de comando.

1. Instale o [CMake](https://cmake.org/download/) e assegure-se de que você pode executar cmake.exe da linha de comando. O CMake versão 3.7.2 ou posterior é recomendado. O instalador **.msi** é a opção mais fácil no Windows. Adicione CMake ao PATH para pelo menos o usuário atual quando o instalador solicitar.

1. Instalar o [Python 2.7](https://www.python.org/downloads/release/python-27). Adicione Python para a sua variável de ambiente `PATH`. Vá para **Painel de Controle** > **Sistema e Segurança** > **Sistema** > **configurações avançadas do sistema** > **Variáveis de ambiente**. Adicione `C:\Python27` ao seu caminho. 

1. Em um prompt de comando, execute o seguinte comando para clonar o repositório GitHub do Azure IoT Edge para seu computador local:

    ```cmd
    git clone https://github.com/Azure/iot-edge.git
    ```

## <a name="how-to-build-the-sample"></a>Como criar a amostra

Agora você pode criar os exemplos e o tempo de execução do IoT Edge em seu computador local:

1. Abra um **Prompt de Comando do Desenvolvedor para VS 2015** ou **Prompt de Comando do Desenvolvedor para VS 2017**, dependendo da sua versão.

1. Navegue até a pasta raiz na sua cópia local do repositório **iot-edge**.

1. Execute o script de compilação da seguinte maneira:

    ```cmd
    tools\build.cmd --disable-native-remote-modules
    ```

Este script cria um arquivo de solução do Visual Studio e compila a solução. É possível encontrar a solução do Visual Studio na pasta **build** na cópia local do repositório **iot-edge**. Se quiser compilar e executar os testes de unidade, adicione o parâmetro `--run-unittests`. Se quiser compilar e executar os testes de ponta a ponta, adicione o `--run-e2e-tests`.

> [!NOTE]
> Sempre que você executa o script **build.cmd**, ele exclui e recria a pasta **build** na pasta raiz da cópia local do repositório **iot-edge**.