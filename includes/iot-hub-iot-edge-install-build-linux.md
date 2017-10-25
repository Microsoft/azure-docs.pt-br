## <a name="install-the-prerequisites"></a>Instalar os pré-requisitos

As etapas neste tutorial presumem que você esteja executando o Ubuntu Linux.

Abra um shell e execute os seguintes comandos para instalar os pacotes de pré-requisito:

```bash
sudo apt-get update
sudo apt-get install curl build-essential libcurl4-openssl-dev git cmake pkg-config libssl-dev uuid-dev valgrind libglib2.0-dev libtool autoconf
```

No shell, execute o seguinte comando para clonar o repositório GitHub do Azure IoT Edge para seu computador local:

```bash
git clone https://github.com/Azure/iot-edge.git
```

## <a name="how-to-build-the-sample"></a>Como criar a amostra

Agora você pode criar os exemplos e o tempo de execução do IoT Edge em seu computador local:

1. Abra um shell.

1. Navegue até a pasta raiz na sua cópia local do repositório **iot-edge**.

1. Execute o script de compilação da seguinte maneira:

    ```sh
    tools/build.sh --disable-native-remote-modules
    ```

Esse script usa o utilitário **cmake** para criar uma pasta chamada **build** na pasta raiz da cópia local do repositório **iot-edge** e gerar um makefile. O script cria a solução, ignorando os testes de unidade e os testes de ponta a ponta. Se quiser compilar e executar os testes de unidade, adicione o parâmetro `--run-unittests`. Se quiser compilar e executar os testes de ponta a ponta, adicione o `--run-e2e-tests`.

> [!NOTE]
> Sempre que você executa o script **build.sh**, ele exclui e recria a pasta **build** na pasta raiz da cópia local do repositório **iot-edge**.