---
title: Configurar dispositivo IoT Edge – Machine Learning no Azure IoT Edge | Microsoft Docs
description: Configure uma Máquina Virtual do Azure executando o Linux como um dispositivo do Azure IoT Edge que atua como um gateway transparente.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/13/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a2096004a7b389f627c528a8dfb4768ac001f390
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155627"
---
# <a name="tutorial-configure-an-iot-edge-device"></a>Tutorial: Configurar um dispositivo IoT Edge

> [!NOTE]
> Este artigo faz parte de uma série para um tutorial sobre como usar o Azure Machine Learning no IoT Edge. Se você acessou este artigo diretamente, incentivamos você a começar com o [primeiro artigo](tutorial-machine-learning-edge-01-intro.md) da série para ter os melhores resultados.

Neste artigo, configuramos uma Máquina Virtual do Azure que executa o Linux para ser um dispositivo do Azure IoT Edge que atua como um gateway transparente. A configuração do gateway transparente permite que os dispositivos conectem-se ao Hub IoT do Azure por meio do gateway sem saber que o gateway existe. Ao mesmo tempo, um usuário que interage com os dispositivos no Hub IoT desconhece o dispositivo de gateway intermediário. Por fim, usamos o gateway transparente para adicionar análise de borda ao nosso sistema adicionando módulos do IoT Edge ao gateway.

As etapas deste artigo normalmente são realizadas por um desenvolvedor de nuvem.

## <a name="generate-certificates"></a>Gerar certificados

Para um dispositivo funcionar como gateway, ele precisa ser capaz de se conectar com segurança aos dispositivos downstream. Azure IoT Edge permite que você use a infraestrutura de chave pública (PKI) para configurar conexões seguras entre dispositivos. Neste caso, nós estamos permitindo um dispositivo downstream conectar-se a um dispositivo IoT Edge atuando como um gateway transparente. Para manter uma segurança razoável, o dispositivo downstream deve confirmar a identidade do dispositivo IoT Edge. Para saber mais sobre como os dispositivos IoT Edge usam certificados, confira [Detalhes de uso do certificado do Azure IoT Edge](iot-edge-certs.md).

Nesta seção, criamos os certificados autoassinados usando uma imagem do Docker que criamos e executamos. Optamos por usar uma imagem do Docker para concluir esta etapa, pois ela reduziu significativamente o número de etapas necessárias para criar os certificados no computador de desenvolvimento do Windows. Confira [Gerar certificados com o Windows](how-to-create-transparent-gateway.md#generate-certificates-with-windows) para obter detalhes sobre como produzir os certificados sem usar um contêiner. [Gerar certificados com o Linux](how-to-create-transparent-gateway.md#generate-certificates-with-linux) tem o conjunto de instruções que automatizamos com a imagem do Docker.

1. Entre na máquina virtual de desenvolvimento.

2. Abra um prompt de linha de comando e execute o seguinte comando para criar um diretório na VM.

    ```cmd
    mkdir c:\edgeCertificates
    ```

3. Inicie o **Docker for Windows** no menu Iniciar do Windows.

4. Abra o Visual Studio Code.

5. Selecione **Arquivo** > **Abrir Pasta...** e escolha **C:\\source\\IoTEdgeAndMlSample\\CreateCertificates**.

6. Clique com o botão direito do mouse no Dockerfile e escolha **Criar Imagem**.

7. Na caixa de diálogo, aceite o valor padrão para o nome e para a tag da imagem: **createcertificates:latest**.

8. Aguarde a conclusão do build.

    > [!NOTE]
    > Você pode ver um aviso para uma chave pública ausente. É seguro ignorá-lo. Da mesma forma, você verá um aviso de segurança que recomenda que você verifique/redefina permissões em sua imagem, que é seguro ignorar para essa imagem.

9. Na janela de terminal do Visual Studio Code, execute o contêiner createcertificates.

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

10. O Docker solicitará o acesso à unidade **c:\\** . Selecione **Compartilhar isso**.

11. Forneça suas credenciais quando solicitado.

12. Depois que o contêiner terminar de ser executado, verifique se há os seguintes arquivos em **c:\\edgeCertificates**:

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>Fazer upload de certificados para o Azure Key Vault

Para armazenar nossos certificados com segurança e para torná-los acessíveis em vários dispositivos, nós faremos upload deles no Azure Key Vault. Como você pode ver na lista acima, temos dois tipos de arquivos de certificado: PFX e PEM. Trataremos o PFX como Certificados do Key Vault a serem carregados para o Key Vault. Os arquivos PEM são texto sem formatação e os trataremos como Segredos do Key Vault. Usaremos o Key Vault associado ao workspace do Serviço do Azure Machine Learning que criamos por meio da execução do [Azure Notebooks](tutorial-machine-learning-edge-04-train-model.md#run-azure-notebooks).

1. No [portal do Azure](https://portal.azure.com), navegue até o workspace do Serviço do Azure Machine Learning.

2. Na página de visão geral do workspace do Serviço do Azure Machine Learning, localize o nome do **Key Vault**.

    ![Copiar o nome do cofre de chaves](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

3. Em seu computador de desenvolvimento, faça upload dos certificados para o Key Vault. Substitua **\<subscriptionId\>** e **\<keyvaultname\>** pelas informações do recurso.

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

4. Se solicitado, entre no Azure.

5. O script será executado por alguns minutos com a saída que lista as novas entradas do Key Vault.

    ![Saída do script do Key Vault](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="create-iot-edge-device"></a>Criar dispositivo IoT Edge

Para conectar um dispositivo Azure IoT Edge a um hub IoT, primeiro criamos uma identidade para o dispositivo no hub. Extraímos a cadeia de conexão da identidade do dispositivo na nuvem e a usamos para configurar o tempo de execução em seu dispositivo IoT Edge. Depois que o dispositivo tiver sido configurado e se conectar ao hub, podemos implantar módulos e enviar mensagens. Também podemos alterar a configuração do dispositivo IoT Edge físico alterando a configuração da identidade do dispositivo correspondente no hub IoT.

Para este tutorial, criamos a identidade do dispositivo usando o Visual Studio Code. Também é possível concluir essas etapas usando o [portal do Azure](how-to-register-device-portal.md) ou a [CLI do Azure](how-to-register-device-cli.md).

1. Em seu computador de desenvolvimento, abra o Visual Studio Code.

2. Abra o quadro **Dispositivos do Hub IoT do Azure** na exibição de explorador do Visual Studio Code.

3. Clique nas reticências e selecione **Criar dispositivo IoT Edge**.

4. Dê ao dispositivo um nome. Para sua conveniência, usamos o **aaTurbofanEdgeDevice** para que ele seja classificado na frente de todos os dispositivos cliente criados anteriormente por meio do agente do dispositivo para enviar os dados de teste.

5. O novo dispositivo será exibido na lista de dispositivos.

    ![Exibir o novo aaTurbofanEdgeDevice no explorador do VS Code](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-azure-virtual-machine"></a>Implantar Máquina Virtual do Azure

Usamos a imagem do [Azure IoT Edge no Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) do Azure Marketplace para criar nosso dispositivo IoT Edge para este tutorial. A imagem do Azure IoT Edge no Ubuntu instala o tempo de execução do IoT Edge mais recente e suas dependências na inicialização. Implantamos a VM usando um script do PowerShell, `Create-EdgeVM.ps1`; um modelo do Resource Manager, `IoTEdgeVMTemplate.json`; e um script de shell, `install packages.sh`.

### <a name="enable-programmatic-deployment"></a>Habilitar implantação programática

Para usar a imagem do marketplace em uma implantação com script, precisamos habilitar a implantação programática da imagem.

1. Entre no Portal do Azure.

1. Selecione **Todos os serviços**.

1. Na barra de pesquisa, insira e selecione **Marketplace**.

1. Na barra de pesquisa, insira e selecione **Azure IoT Edge no Ubuntu**.

1. Selecione o hiperlink **Deseja implantar programaticamente? Introdução**.

1. Selecione o botão **Habilitar** e, em seguida, **Salvar**.

    ![Habilitar implantação programática para VM](media/tutorial-machine-learning-edge-05-configure-edge-device/deploy-ubuntu-vm.png)

1. Você verá uma notificação de êxito.

### <a name="create-virtual-machine"></a>Criar máquina virtual

Em seguida, execute o script para criar a máquina virtual para seu dispositivo IoT Edge.

1. Abra uma janela do PowerShell e navegue até o diretório **EdgeVM**.

    ```powershell
    cd c:\source\IoTEdgeAndMlSample\EdgeVM
    ```

2. Execute o script para criar a máquina virtual.

    ```powershell
    .\Create-EdgeVm.ps1
    ```

3. Quando solicitado, forneça valores para cada parâmetro. Para assinatura, grupo de recursos e localização, recomendamos usar o mesmo que você tem para todos os recursos ao longo deste tutorial.

    * **ID de Assinatura do Azure**: encontrada no portal do Azure
    * **Nome do Grupo de Recursos**: nome fácil de lembrar para agrupar os recursos para este tutorial
    * **Localização**: localização do Azure em que a máquina virtual será criada. Por exemplo, westus2 ou northeurope. Para saber mais, confira todos os [locais do Azure](https://azure.microsoft.com/global-infrastructure/locations/).
    * **AdminUsername**: o nome da conta do administrador que você usará para entrar na máquina virtual
    * **AdminPassword**: a senha a ser definida para o AdminUsername na máquina virtual

4. Para que o script possa configurar a VM, é necessário entrar no Azure com as credenciais associadas à Assinatura do Azure que você está usando.

5. O script confirma as informações para a criação de sua VM. Selecione **y** ou **Enter** para continuar.

6. O script é executado por vários minutos conforme executa as seguintes etapas:

    * Crie o grupo de recursos se ele ainda não existir
    * Criar a máquina virtual
    * Adicione exceções deNSG para a VM para as portas 22 (SSH), 5671 (AMQP), 5672 (AMPQ) e 443 (SSL)
    * Instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest))

7. O script gera a cadeia de conexão do SSH para conectar-se à VM. Copie a cadeia de conexão para a próxima etapa.

    ![Copiar a cadeia de conexão do SSH para a VM](media/tutorial-machine-learning-edge-05-configure-edge-device/vm-ssh-connection-string.png)

## <a name="connect-to-your-iot-edge-device"></a>Conectar-se ao dispositivo do IoT Edge

As próximas seções configurarão a máquina virtual do Azure que criamos. A primeira etapa é conectar-se à máquina virtual.

1. Abra um prompt de comando e cole a cadeia de conexão SSH copiada da saída do script. Insira suas próprias informações para nome de usuário, sufixo e região de acordo com os valores fornecidos para o script do PowerShell na seção anterior.

    ```cmd
    ssh -l <username> iotedge-<suffix>.<region>.cloudapp.azure.com
    ```

2. Quando solicitado a validar a autenticidade do host, digite **yes** e selecione **Enter**.

3. Quando solicitado, forneça a senha.

4. O Ubuntu exibe uma mensagem de boas-vindas e, em seguida, você verá um prompt como `<username>@<machinename>:~$`.

## <a name="download-key-vault-certificates"></a>Baixar certificados do Azure Key Vault

Anteriormente neste artigo, carregamos certificados para o Key Vault para disponibilizá-los para o nosso dispositivo IoT Edge e para nosso dispositivo folha, que é um dispositivo downstream que usa o dispositivo IoT Edge como um gateway para se comunicar com o Hub IoT. Lidaremos com o dispositivo folha posteriormente no tutorial. Nesta seção, baixe os certificados para o dispositivo IoT Edge.

1. Na sessão do SSH na máquina virtual Linux, entre no Azure com a CLI do Azure.

    ```bash
    az login
    ```

1. Você deverá abrir um navegador para <https://microsoft.com/devicelogin> e fornecer um código exclusivo. É possível executar estas etapas em seu computador local. Feche a janela do navegador quando terminar a autenticação.

1. Quando você se autenticar com êxito, a VM Linux será conectada e listará as assinaturas do Azure.

1. Defina a assinatura do Azure que você deseja usar para comandos da CLI do Azure.

    ```bash
    az account set --subscription <subscriptionId>
    ```

1. Crie um diretório na VM para os certificados.

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. Baixe os certificados que você armazenou no cofre de chaves: new-edge-device-full-chain.cert.pem, new-edge-device.key.pem e azure-iot-test-only.root.ca.cert.pem

    ```bash
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>Atualizar a configuração do dispositivo IoT Edge

O tempo de execução do IoT Edge usa o arquivo /etc/iotedge/config.yaml para manter usa configuração. É necessário atualizar essas partes de informações neste arquivo:

* **Cadeia de conexão do dispositivo**: a cadeia de conexão da identidade deste dispositivo no Hub IoT
* **Certificados:** os certificados a serem usados para conexões realizadas com dispositivos downstream
* **Nome do host:** o FQDN (nome de domínio totalmente qualificado) do dispositivo IoT Edge da VM.

A imagem do *Azure IoT Edge no Ubuntu* que usamos para criar a VM do IoT Edge vem com um script de shell que atualiza o config.yaml com a cadeia de conexão.

1. No Visual Studio Code, clique com o botão direito do mouse no dispositivo IoT Edge e, em seguida, selecione **Copiar Cadeia de Conexão do Dispositivo**.

    ![Copiar cadeia de conexão do Visual Studio Code](media/tutorial-machine-learning-edge-05-configure-edge-device/copy-device-connection-string-command.png)

2. Na sua sessão do SSH, execute o comando para atualizar o arquivo config.yaml com a cadeia de conexão do dispositivo.

    ```bash
    sudo /etc/iotedge/configedge.sh "<your_iothub_edge_device_connection_string>"
    ```

Em seguida, atualizaremos os certificados e o nome do host editando diretamente o config.yaml.

1. Abra o arquivo config.yaml.

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

2. Atualize a seção de certificados do config.yaml removendo o `#` à esquerda e definindo o caminho para que o arquivo tenha a aparência do seguinte exemplo:

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    Verifique se “certificates:” não tem nenhum espaço em branco anterior e se cada um dos certificados é precedido por dois espaços.

    Clicar com o botão direito do mouse em nano colará o conteúdo de sua área de transferência na posição atual do cursor. Para substituir a cadeia de caracteres, use as setas do teclado para navegar até a cadeia de caracteres que você deseja substituir, excluir a cadeia de caracteres e, em seguida, clicar com o botão direito do mouse para colar do buffer.

3. No portal do Azure, navegue até sua máquina virtual. Copie o nome DNS (FQDN do computador) da seção **Visão geral**.

4. Cole o FQDN na seção de nome de host do config.yml. Verifique se o nome é escrito inteiramente em letras minúsculas.

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

5. Salve e feche o arquivo (`Ctrl + X`, `Y`, `Enter`).

6. Reinicie o daemon iotedge.

    ```bash
    sudo systemctl restart iotedge
    ```

7. Verifique o status do Daemon do IoT Edge (após o comando, digite “:q” para sair).

    ```bash
    systemctl status iotedge
    ```

8. Se vir erros (texto colorido prefixado com “\[ERRO\]”) no status, examine os logs do daemon para ver informações de erro detalhadas.

    ```bash
    journalctl -u iotedge --no-pager --no-full
    ```

## <a name="next-steps"></a>Próximas etapas

Acabamos de concluir a configuração de uma VM do Azure como Gateway Transparente do Azure IoT Edge. Começamos gerando certificados de teste, que carregamos para o Azure Key Vault. Em seguida, usamos um script e um modelo do Resource Manager para implantar a VM com a imagem “Tempo de execução do Azure IoT Edge + Ubuntu Server 16.04 LTS” do Azure Marketplace. O script usou a etapa extra de instalar a CLI do Azure ([Instar a CLI do Azure com apt](https://docs.microsoft.com/cli/azure/install-azure-cli-apt)). Com a VM em execução, conectamos por meio do SSH, entramos no Azure, baixamos certificados do Key Vault e fizemos várias atualizações na configuração do Tempo de Execução do IoT Edge atualizando o arquivo config.yaml. Para saber mais sobre como usar o IoT Edge como um gateway, confira [Como um dispositivo IoT Edge pode ser usado como um gateway](iot-edge-as-gateway.md). Para saber mais sobre como configurar um dispositivo IoT Edge como um gateway transparente, confira [Configurar um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md).

Passe para o próximo artigo para criar módulos do IoT Edge.

> [!div class="nextstepaction"]
> [Criar e implantar módulos do IoT Edge personalizados](tutorial-machine-learning-edge-06-custom-modules.md)
