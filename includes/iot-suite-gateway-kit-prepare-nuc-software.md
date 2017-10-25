## <a name="build-iot-edge"></a>Criar o Edge IoT

Este tutorial usa módulos personalizados do Edge IoT para se comunicar com a solução pré-configurada de monitoramento remoto. Portanto, você precisa criar os módulos do Edge IoT a partir do código-fonte personalizado. As seções a seguir descrevem como instalar o Edge IoT e criar o módulo personalizado do Edge IoT.

### <a name="install-iot-edge"></a>Instalar o Edge IoT

As etapas a seguir descrevem como instalar o software do Edge IoT pré-compilado no Intel NUC:

1. Configure os repositórios de pacote inteligente necessário executando os seguintes comandos no Intel NUC:

    ```bash
    smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
    smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
    ```

    Insira `y` quando o comando solicitar **Incluir este canal?**.

1. Atualize o gerenciador de pacote inteligente, executando o seguinte comando:

    ```bash
    smart update
    ```

1. Instale o pacote do Edge IoT do Azure executando o seguinte comando:

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```

1. Verifique a instalação executando o exemplo “Olá, Mundo”. Este exemplo grava uma mensagem de hello world no arquivo log.txt a cada cinco segundos. Os seguintes comandos executam o exemplo “Olá, Mundo”:

    ```bash
    cd /usr/share/azureiotgatewaysdk/samples/hello_world/
    ./hello_world hello_world.json
    ```

    Ignore qualquer mensagem de **argumento inválido** ao interromper o exemplo.

    Use o seguinte comando para exibir o conteúdo do arquivo de log:

    ```bash
    cat log.txt | more
    ```

### <a name="troubleshooting"></a>Solucionar problemas

Se você receber o erro “Nenhum pacote fornece o util-linux-dev”, tente reiniciar o Intel NUC.
