---
title: 'Tutorial: Configurar um dispositivo do Azure IoT Edge – Machine Learning no IoT Edge'
description: Neste tutorial, você vai configurar uma máquina virtual do Azure executando o Linux como um dispositivo do Azure IoT Edge que atua como um gateway transparente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 65fd6e5b4d494f8e8486d72079b9fa97a175894b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376878"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>Tutorial: Configurar um dispositivo do Azure IoT Edge

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Neste artigo, configuramos uma máquina virtual do Azure que executa o Linux para ser um dispositivo do Azure IoT Edge que atua como um gateway transparente. Uma configuração de gateway transparente permite que os dispositivos conectem-se ao Hub IoT do Azure por meio do gateway sem saber que o gateway existe. Ao mesmo tempo, um usuário que interage com os dispositivos no Hub IoT desconhece o dispositivo de gateway intermediário. Por fim, adicionaremos análise de borda ao nosso sistema adicionando módulos do IoT Edge ao gateway transparente.

>[!NOTE]
>Os conceitos neste conjunto de tutoriais se aplicam a todas as versões do IoT Edge, mas o dispositivo de exemplo que você cria para experimentar o cenário é executado no IoT Edge versão 1.1.

As etapas deste artigo normalmente são realizadas por um desenvolvedor de nuvem.

Nesta seção do tutorial, você aprenderá a:

> [!div class="checklist"]
>
> * Criar certificados para permitir que o dispositivo de gateway se conecte com segurança aos dispositivos downstream.
> * Criar um dispositivo IoT Edge.
> * Criar uma máquina virtual do Azure para simular o dispositivo IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo faz parte de uma série para um tutorial sobre como usar o Azure Machine Learning no IoT Edge. Cada artigo da série se baseia no trabalho do artigo anterior. Se você chegou a este artigo diretamente, veja o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série.

## <a name="create-certificates"></a>Criar certificados

Para um dispositivo funcionar como gateway, ele precisa se conectar com segurança aos dispositivos downstream. Com o IoT Edge, você pode usar uma PKI (infraestrutura de chave pública) para configurar conexões seguras entre dispositivos. Neste caso, nós estamos permitindo um dispositivo IoT downstream conectar-se a um dispositivo IoT Edge atuando como um gateway transparente. Para manter uma segurança razoável, o dispositivo downstream deve confirmar a identidade do dispositivo IoT Edge. Para saber mais sobre como os dispositivos IoT Edge usam certificados, confira [Detalhes de uso do certificado do Azure IoT Edge](iot-edge-certs.md).

Nesta seção, criamos os certificados autoassinados usando uma imagem do Docker que compilamos e executamos. Optamos por usar uma imagem do Docker para concluir esta etapa pois ela reduz o número de etapas necessárias para criar os certificados no computador de desenvolvimento do Windows. Para saber o que automatizamos com a imagem do Docker, confira [Criar certificados de demonstração para testar os recursos do dispositivo IoT Edge](how-to-create-test-certificates.md).

1. Entre na VM de desenvolvimento.
1. Crie uma pasta com o caminho e o nome **c:\edgeCertificates**.

1. Se ainda não estiver em execução, inicie o **Docker for Windows** no menu Iniciar do Windows.

1. Abra o Visual Studio Code.

1. Selecione **Arquivo** > **Abrir Pasta** e escolha **C:\\origem\\IoTEdgeAndMlSample\\CreateCertificates**.

1. No painel do **Explorer**, clique com o botão direito do mouse em **dockerfile** e selecione **Compilar Imagem**.

1. Na caixa de diálogo, aceite o valor padrão para o nome e para a marca da imagem: **createcertificates: latest**.

    ![Captura de tela que mostra a criação de certificados no Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. Aguarde a conclusão do build.

    > [!NOTE]
    > Você pode ver um aviso sobre uma chave pública ausente. É seguro ignorá-lo. Da mesma forma, você verá um aviso de segurança que recomenda verificar ou redefinir permissões em sua imagem, que é seguro ignorar para essa imagem.

1. Na janela de terminal do Visual Studio Code, execute o contêiner createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. O Docker solicitará o acesso à unidade **c:\\** . Selecione **Compartilhar isso**.

1. Forneça suas credenciais quando solicitado.

1. Após o contêiner terminar de ser executado, verifique se há os seguintes arquivos em **c:\\edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Fazer upload de certificados para o Azure Key Vault

Para armazenar nossos certificados com segurança e torná-los acessíveis em vários dispositivos, faremos upload deles no Azure Key Vault. Como você pode ver na lista anterior, temos dois tipos de arquivos de certificado: PFX e PEM. Trataremos o arquivo PFX como certificados do Key Vault a serem carregados para o Key Vault. Os arquivos PEM são texto sem formatação e os trataremos como segredos do Key Vault. Usaremos a instância do Key Vault associada ao workspace do Azure Machine Learning que criamos por meio da execução dos [Jupyter Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks).

1. No [portal do Azure](https://portal.azure.com), acesse o workspace do Azure Machine Learning.

1. Na página de visão geral do workspace do Machine Learning, localize o nome do **Key Vault**.

    ![Captura de tela que mostra a cópia do nome do cofre de chaves.](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. Em seu computador de desenvolvimento, faça upload dos certificados para o Key Vault. Substitua **\<subscriptionId\>** e **\<keyvaultname\>** pelas informações do recurso.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. Caso seja solicitado, entre no Azure.

1. O script será executado por alguns minutos com a saída que lista as novas entradas do Key Vault.

    ![Captura de tela que mostra a saída de script do Key Vault.](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-an-iot-edge-device"></a>Criar um dispositivo do IoT Edge

Para conectar um dispositivo Azure IoT Edge a um hub IoT, primeiro criamos uma identidade para o dispositivo no hub. Extraímos a cadeia de conexão da identidade do dispositivo na nuvem e a usamos para configurar o runtime em seu dispositivo IoT Edge. Após um dispositivo configurado se conectar ao hub, podemos implantar módulos e enviar mensagens. Também podemos alterar a configuração do dispositivo IoT Edge físico alterando a identidade do dispositivo correspondente no Hub IoT.

Para este tutorial, criamos a identidade do dispositivo usando o Visual Studio Code. Também é possível realizar essas etapas usando o portal do Azure ou a CLI do Azure.

1. Em seu computador de desenvolvimento, abra o Visual Studio Code.

1. Expanda o quadro do **Hub IoT do Azure** na exibição do **Explorer** do Visual Studio Code.

1. Selecione as reticências e escolha **Criar dispositivo IoT Edge**.

1. Dê ao dispositivo um nome. Por conveniência, usamos o nome **aaTurbofanEdgeDevice** para que ele seja classificado na parte superior dos dispositivos listados.

1. O novo dispositivo aparece na lista de dispositivos.

    ![Captura de tela que mostra uma exibição do dispositivo no Explorer do Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>Implantar uma máquina virtual do Azure

Usamos a imagem do [Azure IoT Edge no Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) do Azure Marketplace para criar o dispositivo IoT Edge para este tutorial. A imagem do Azure IoT Edge no Ubuntu instala o runtime do IoT Edge mais recente e suas dependências na inicialização. Implantamos a VM usando:

- Um script do PowerShell, `Create-EdgeVM.ps1`.
- Um modelo do Azure Resource Manager, `IoTEdgeVMTemplate.json`.
- Um script de shell, `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Habilitar implantação programática

Para usar a imagem do Azure Marketplace em uma implantação com script, precisamos habilitar a implantação programática da imagem.

1. Entre no portal do Azure.

1. Selecione **Todos os serviços**.

1. Na barra de pesquisa, insira e selecione **Marketplace**.

1. Na barra de pesquisa do Marketplace, insira e selecione **Azure IoT Edge no Ubuntu**.

1. Selecione o hiperlink **Introdução** para implantar programaticamente.

1. Selecione o botão **Habilitar** e escolha **Salvar**.

    ![Captura de tela que mostra a habilitação da implantação programática para uma máquina virtual.](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Você verá uma notificação de êxito.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Em seguida, execute o script para criar a máquina virtual para seu dispositivo IoT Edge.

1. Abra uma janela do PowerShell e vá até o diretório **EdgeVM**.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

1. Execute o script para criar a máquina virtual.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

1. Quando solicitado, forneça valores de cada parâmetro. Para assinatura, grupo de recursos e local, recomendamos usar os mesmos valores usados para todos os recursos ao longo deste tutorial.

    * **ID de Assinatura do Azure**: encontrada no portal do Azure.
    * **Nome do Grupo de Recursos**: nome fácil de lembrar para agrupar os recursos para este tutorial.
    * **Localização**: localização do Azure em que a máquina virtual será criada. Por exemplo, westus2 ou northeurope. Para saber mais, confira todos os [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: o nome da conta do administrador que você usará para entrar na máquina virtual.
    * **AdminPassword**: a senha a ser definida para o nome do usuário administrador na máquina virtual.

1. Para que o script configure a VM, entre no Azure com as credenciais associadas à assinatura do Azure que você está usando.

1. O script confirma as informações para a criação de sua VM. Selecione **y** ou **Enter** para continuar.

1. O script é executado por vários minutos conforme executa as seguintes etapas:

    * Cria o grupo de recursos se ele ainda não existir
    * Cria a máquina virtual
    * Adiciona exceções de NSG para a VM para as portas 22 (SSH), 5671 (AMQP), 5672 (AMPQ) e 443 (TLS)
    * Instala a [CLI do Azure](/cli/azure/install-azure-cli-apt)

1. O script gera a cadeia de conexão do SSH para conectar-se à VM. Copie a cadeia de conexão para a próxima etapa.

    ![Captura de tela que mostra a cópia da cadeia de conexão SSH para uma máquina virtual.](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Conectar-se ao dispositivo do IoT Edge

As próximas seções configurarão a máquina virtual do Azure que criamos. A primeira etapa é conectar-se à máquina virtual.

1. Abra um prompt de comando e cole a cadeia de conexão SSH copiada da saída do script. Insira suas próprias informações para nome de usuário, sufixo e região de acordo com os valores fornecidos para o script do PowerShell na seção anterior.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

1. Quando for solicitado que você valide a autenticidade do host, digite **yes** e selecione **Enter**.

1. Quando solicitado, forneça a senha.

1. O Ubuntu exibe uma mensagem de boas-vindas e você verá um prompt semelhante a `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Baixar certificados do Azure Key Vault

Anteriormente neste artigo, carregamos certificados para o Key Vault a fim de disponibilizá-los para nosso dispositivo IoT Edge e nosso dispositivo folha. O dispositivo folha é um dispositivo downstream que usa o dispositivo IoT Edge como um gateway para se comunicar com o Hub IoT.

Lidaremos com o dispositivo folha posteriormente no tutorial. Nesta seção, baixe os certificados para o dispositivo IoT Edge.

1. Na sessão do SSH na máquina virtual Linux, entre no Azure com a CLI do Azure.

    ```azurecli
    az login
    ```

1. Será solicitado que você abra um navegador em uma página de [Entrada do dispositivo da Microsoft](https://microsoft.com/devicelogin) e forneça um código exclusivo. É possível executar estas etapas em seu computador local. Feche a janela do navegador quando terminar a autenticação.

1. Quando você se autenticar com êxito, a VM Linux será conectada e listará as assinaturas do Azure.

1. Defina a assinatura do Azure que deseja usar para os comandos da CLI do Azure.

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. Crie um diretório na VM para os certificados.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Baixe os certificados que você armazenou no cofre de chaves: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem e azure-iot-test-only.root.ca.cert.pem.

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Atualizar a configuração do dispositivo IoT Edge

O runtime do IoT Edge usa o arquivo /etc/iotedge/config.yaml para manter usa configuração. É necessário atualizar essas partes de informações neste arquivo:

* **Cadeia de conexão do dispositivo**: a cadeia de conexão da identidade do dispositivo no Hub IoT
* **Certificados**: os certificados a serem usados para conexões realizadas com dispositivos downstream
* **Nome do host**: o FQDN (nome de domínio totalmente qualificado) do dispositivo IoT Edge da VM

A imagem do Azure IoT Edge no Ubuntu que usamos para criar a VM do IoT Edge vem com um script de shell que atualiza o arquivo config.yaml com a cadeia de conexão.

1. No Visual Studio Code, clique com o botão direito do mouse no dispositivo IoT Edge e selecione **Copiar Cadeia de Conexão do Dispositivo**.

    ![Captura de tela que mostra a cópia da cadeia de conexão do Visual Studio Code.](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

1. Na sua sessão do SSH, execute o comando para atualizar o arquivo config.yaml com a cadeia de conexão do dispositivo.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Em seguida, atualizaremos os certificados e o nome do host editando diretamente o arquivo config.yaml.

1. Abra o arquivo config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Atualize a seção de certificados do arquivo config.yaml removendo o **#** à esquerda e definindo o caminho de maneira que o arquivo tenha a aparência do seguinte exemplo:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Verifique se a linha **certificates:** não tem nenhum espaço em branco à direita e se cada um dos certificados aninhados tem um recuo de dois espaços.

    Clicar com o botão direito do mouse em nano colará o conteúdo de sua área de transferência na posição atual do cursor. Para substituir a cadeia de caracteres, use as setas do teclado para navegar até a cadeia de caracteres que deseja substituir, exclua a cadeia de caracteres e clique com o botão direito do mouse para colar do buffer.

1. No portal do Azure, vá para sua máquina virtual. Copie o nome DNS (FQDN do computador) da seção **Visão geral**.

1. Cole o FQDN na seção de nome do host do arquivo config.yml. Verifique se o nome está escrito inteiramente em letras minúsculas.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. Salve e feche o arquivo selecionando **Ctrl + X**, **Y** e **Enter**.

1. Reinicie o daemon do IoT Edge.

    ```bash
    sudo systemctl restart iotedge
    ```

1. Verifique o status do daemon do IoT Edge. Após o comando, digite **:q** para sair.

    ```bash
    systemctl status iotedge
    ```

1. Se vir erros (texto colorido prefixado com "\[ERRO\]") no status, examine os logs do daemon para ver informações de erro detalhadas.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```
## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial faz parte de um conjunto em que cada artigo se baseia no trabalho feito nos anteriores. Aguarde para limpar os recursos até concluir o último tutorial.

## <a name="next-steps"></a>Próximas etapas

Acabamos de concluir a configuração de uma VM do Azure como gateway transparente do IoT Edge. Começamos gerando certificados de teste que carregamos para o Key Vault. Em seguida, usamos um script e um modelo do Resource Manager para implantar a VM com a imagem do runtime do Azure IoT Edge + Ubuntu Server 16.04 LTS do Azure Marketplace. Com a VM em funcionamento, nos conectamos via SSH. Em seguida, entramos no Azure e baixamos certificados do Key Vault. Fizemos várias atualizações na configuração do runtime do IoT Edge atualizando o arquivo config.yaml.

Passe para o próximo artigo para criar módulos do IoT Edge.

> [!div class="nextstepaction"]
> [Criar e implantar módulos do IoT Edge personalizados](tutorial-machine-learning-edge-06-custom-modules.md)
