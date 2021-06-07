---
title: Tutorial – Criar uma hierarquia de dispositivos IoT Edge – Azure IoT Edge
description: Este tutorial mostra como criar uma estrutura hierárquica de dispositivos IoT Edge usando gateways.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: c1b30a1eafe9af92c1ef3f81773d213ccf96555c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103462024"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>Tutorial: Criar uma hierarquia de dispositivos IoT Edge (versão prévia)

[!INCLUDE [iot-edge-version-202011](../../includes/iot-edge-version-202011.md)]

Implante nós do Azure IoT Edge em redes organizadas em camadas hierárquicas. Cada camada em uma hierarquia é um dispositivo de gateway que processa as mensagens e as solicitações de dispositivos na camada abaixo dele.

>[!NOTE]
>Este recurso exige o IoT Edge versão 1.2, que está em versão prévia pública, que execute contêineres do Linux.

Estruture uma hierarquia de dispositivos para que apenas a camada superior tenha conectividade com a nuvem e as camadas inferiores só possam se comunicar com as camadas norte e sul adjacentes. Essa camada de rede é a base da maioria das redes industriais, que seguem o [padrão ISA-95](https://en.wikipedia.org/wiki/ANSI/ISA-95).

O objetivo deste tutorial é criar uma hierarquia de dispositivos IoT Edge que simula um ambiente de produção. No final, você implantará o [módulo Sensor de Temperatura Simulada](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.simulated-temperature-sensor) a um dispositivo de camada inferior sem acesso à Internet, baixando as imagens de contêiner por meio da hierarquia.

Para atingir essa meta, este tutorial explica como criar uma hierarquia de dispositivos IoT Edge, implantar contêineres do runtime do IoT Edge nos seus dispositivos e configurar os dispositivos localmente. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Criar e definir as relações em uma hierarquia de dispositivos IoT Edge.
> * Configurar o runtime do IoT Edge nos dispositivos da sua hierarquia.
> * Instalar certificados consistentes na hierarquia de dispositivos.
> * Adicionar cargas de trabalho aos dispositivos da hierarquia.
> * Use o [módulo de Proxy de API do IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview) para rotear o tráfego HTTP com segurança em uma porta única de dispositivos de camada inferior.

Neste tutorial, as seguintes camadas de rede são definidas:

* **Camada superior**: os dispositivos IoT Edge dessa camada podem se conectar diretamente à nuvem.

* **Camadas inferiores**: os dispositivos do IoT Edge que estão em camadas abaixo da camada superior não podem se conectar diretamente à nuvem. Eles precisam passar por um ou mais dispositivos IoT Edge intermediários para enviar e receber dados.

Para simplificar, este tutorial usará uma hierarquia de dois dispositivos, conforme ilustrado abaixo. Um dispositivo, o **dispositivo de camada superior**, representa um dispositivo na camada superior da hierarquia que pode se conectar diretamente à nuvem. Esse dispositivo também será chamado de **dispositivo pai**. O outro dispositivo, o **dispositivo de camada inferior**, representa um dispositivo na camada inferior da hierarquia que não pode se conectar diretamente à nuvem. É possível adicionar dispositivos de camada inferior para representar seu ambiente de produção, conforme necessário. Os dispositivos em camadas inferiores também serão chamados de **dispositivos filho**. A configuração de dispositivos adicionais de camada inferior seguirá a configuração do **dispositivo de camada inferior**.

![Estrutura da hierarquia do tutorial contendo dois dispositivos: o dispositivo de camada superior e o dispositivo de camada inferior](./media/tutorial-nested-iot-edge/tutorial-hierarchy-diagram.png)

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma hierarquia de dispositivos IoT Edge, será necessário:

* Um computador (Windows ou Linux) com conectividade com a Internet.
* Uma conta do Azure com uma assinatura válida. Se você não tiver uma [assinatura do Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou da camada Standard no Azure.
* A CLI do Azure v2.3.1 com a extensão de IoT do Azure v0.10.6 ou superior instalada. Este tutorial usa o [Azure Cloud Shell](../cloud-shell/overview.md). Se você não estiver familiarizado com o Azure Cloud Shell, [confira um guia de início rápido para obter mais detalhes](./quickstart-linux.md#prerequisites).
  * Execute o comando [az version](/cli/azure/reference-index?#az_version) para conferir as versões atuais de módulos e extensões da CLI do Azure.
* Um dispositivo Linux a fim de configurá-lo como um dispositivo do IoT Edge para cada dispositivo na hierarquia. Este tutorial usará dois dispositivos. Caso não tenha dispositivos disponíveis, será possível criar duas máquinas virtuais do Azure para cada dispositivo na hierarquia substituindo o texto do espaço reservado no seguinte comando e executando-o:

   ```azurecli-interactive
   az vm create \
    --resource-group <REPLACE_WITH_RESOURCE_GROUP> \
    --name <REPLACE_WITH_UNIQUE_NAMES_FOR_EACH_VM> \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password <REPLACE_WITH_PASSWORD>
   ```

* Verifique se as seguintes portas estão abertas para entrada: 8000, 443, 5671, 8883:
  * 8000: Usado para capturar imagens de contêiner do Docker por meio do proxy de API.
  * 443: Usado entre os hubs de borda pai e filho para chamadas à API REST.
  * 5671, 8883: Usado para AMQP e MQTT.

  Para obter mais informações, consulte [Como abrir portas em uma máquina virtual com o portal do Azure](../virtual-machines/windows/nsg-quickstart-portal.md).

>[!TIP]
>Caso queira obter uma visão automatizada da configuração de uma hierarquia de dispositivos do IoT Edge, será possível seguir o [exemplo com script do Azure IoT Edge para o IoT Industrial](https://aka.ms/iotedge-nested-sample). Esse cenário com script implantará máquinas virtuais do Azure como dispositivos pré-configurados para simular um ambiente de fábrica.
>
>Caso queira prosseguir com a criação passo a passo da hierarquia de exemplo, siga as etapas do tutorial abaixo.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Configurar a hierarquia de dispositivos IoT Edge

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Criar uma hierarquia de dispositivos IoT Edge

Os dispositivos do IoT Edge compõem as camadas da hierarquia. Este tutorial criará uma hierarquia de dois dispositivos do IoT Edge: um **dispositivo de camada superior** e seu respectivo filho, um **dispositivo de camada inferior**. É possível criar dispositivos filho adicionais, conforme necessário.

É possível usar o portal do Azure ou a CLI do Azure para criar dispositivos do IoT Edge.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://ms.portal.azure.com/), navegue até o seu Hub IoT.

1. No menu no painel esquerdo, em **Gerenciamento Automático de Dispositivo**, selecione **IoT Edge**.

1. Selecione **+ Adicionar um dispositivo IoT Edge**. Esse dispositivo será o dispositivo de camada superior. Portanto, insira uma identificação do dispositivo exclusiva apropriada. Selecione **Salvar**.

1. Selecione **+ Adicionar um dispositivo IoT Edge** novamente. Esse dispositivo será um dispositivo de camada inferior. Portanto, insira uma identificação exclusiva e apropriada do dispositivo.

1. Escolha **Definir um dispositivo pai**, escolha o dispositivo de camada superior na lista de dispositivos e selecione **OK**. Selecione **Salvar**.

   ![Como definir o pai para o dispositivo de camada inferior](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. No [Azure Cloud Shell](https://shell.azure.com/), insira o comando a seguir para criar um dispositivo IoT Edge no hub. Esse dispositivo será o dispositivo de camada superior. Portanto, insira uma identificação do dispositivo exclusiva apropriada:

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

   Uma criação de dispositivo realizada com êxito resultará na configuração JSON do dispositivo.

   ![Uma saída JSON de uma criação de dispositivo realizada com êxito](./media/tutorial-nested-iot-edge/json-success-output.png)

1. Insira o comando abaixo para criar um dispositivo do IoT Edge de camada inferior. É possível criar mais de um dispositivo de camada inferior caso queira obter mais camadas na hierarquia. Lembre-se de fornecer identidades exclusivas do dispositivo para cada uma delas.

   ```azurecli-interactive
   az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Insira o comando abaixo para definir cada relação pai-filho entre um **dispositivo de camada superior** e cada **dispositivo de camada inferior**. Lembre-se de executar esse comando para cada dispositivo de camada inferior na hierarquia.

   ```azurecli-interactive
   az iot hub device-identity parent set --device-id {lower_layer_device_id} --parent-device-id {top_layer_device_id} --hub-name {hub_name}
   ```

   Esse comando não tem uma saída explícita.

---

Em seguida, anote cada cadeia de conexão do dispositivo do IoT Edge. Elas serão usadas mais tarde.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://ms.portal.azure.com/), navegue até a seção **IoT Edge** do Hub IoT.

1. Clique na identificação de um dos dispositivos na lista de dispositivos.

1. Selecione **Copiar** no campo **Cadeia de Conexão Primária** e registre-o em um lugar de sua escolha.

1. Repita essa etapa para todos os outros dispositivos.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. No [Azure Cloud Shell](https://shell.azure.com/), para cada dispositivo, insira o seguinte comando para recuperar a cadeia de conexão do dispositivo e registre-o em um lugar de sua escolha:

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id {device_id} --hub-name {hub_name}
   ```

---

Caso tenha concluído as etapas acima de modo correto, será possível usar as etapas abaixo para verificar se as relações pai-filho estão corretas no portal do Azure ou na CLI do Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://ms.portal.azure.com/), navegue até a seção **IoT Edge** do Hub IoT.

1. Clique na identificação do dispositivo de um **dispositivo de camada inferior** na lista de dispositivos.

1. Na página de detalhes do dispositivo, você verá a identidade do **dispositivo de camada superior** listada ao lado do campo **Dispositivo pai**.

   [Dispositivo pai confirmado pelo dispositivo filho](./media/tutorial-nested-iot-edge/lower-layer-device-parent.png)

Também é possível alterar a relação de hierarquia para filho por meio do **dispositivo de camada superior**.

1. Clique na identificação do dispositivo de um **dispositivo de camada superior** na lista de dispositivos.

1. Selecione a guia **Gerenciar dispositivos filho** na parte superior da página.

1. Na lista de dispositivos, você verá o **dispositivo de camada inferior**.

   [Dispositivo filho confirmado pelo dispositivo pai](./media/tutorial-nested-iot-edge/top-layer-device-child.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. É possível verificar no [Azure Cloud Shell](https://shell.azure.com/) se um dos dispositivos filho estabeleceu com êxito uma relação com um dispositivo pai obtendo a confirmação da identidade do dispositivo filho pelo dispositivo pai. Isso criará uma configuração JSON do dispositivo pai, conforme ilustrado acima:

   ```azurecli-interactive
   az iot hub device-identity parent show --device-id {lower_layer_device_id} --hub-name {hub_name}
   ```

Também é possível alterar a relação de hierarquia para filho por meio de uma consulta ao **dispositivo de camada superior**.

1. Liste os dispositivos filho que o dispositivo pai conhece:

    ```azurecli-interactive
    az iot hub device-identity children list --device-id {top_layer_device_id} --hub-name {hub_name}
    ```

---

Quanto estiver satisfeito com a hierarquia estruturada de modo correto, você estará pronto para prosseguir.

### <a name="create-certificates"></a>Criar certificados

Todos os dispositivos em um [cenário de gateway](iot-edge-as-gateway.md) precisam ter um certificado compartilhado para configurar conexões seguras entre eles. Use as etapas a seguir para criar certificados de demonstração para os dois dispositivos neste cenário.

Para criar certificados de demonstração em um dispositivo Linux, você precisará clonar os scripts de geração e configurá-los para serem executados localmente no Bash.

1. Clone o repositório Git do IoT Edge, que contém scripts para gerar certificados de demonstração.

   ```bash
   git clone https://github.com/Azure/iotedge.git
   ```

1. Navegue até o diretório no qual você deseja trabalhar. Todos os arquivos de certificado e de chave serão criados nesse diretório.

1. Copie os arquivos de configuração e de script do repositório do IoT Edge clonado para o diretório de trabalho.

   ```bash
   cp <path>/iotedge/tools/CACertificates/*.cnf .
   cp <path>/iotedge/tools/CACertificates/certGen.sh .
   ```

1. Crie o Certificado de AC raiz e um certificado intermediário.

   ```bash
   ./certGen.sh create_root_and_intermediate
   ```

   Este comando de script cria vários arquivos de certificado e de chave, mas estamos usando o seguinte arquivo como o **Certificado de AC raiz** para a hierarquia de gateway:

   * `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem`  

1. Crie dois conjuntos de Certificados de Autoridade de Certificação do dispositivo IoT Edge e chaves privadas com o seguinte comando: um conjunto para o dispositivo de camada superior e um definido para o dispositivo de camada inferior. Forneça nomes que sejam fáceis de serem lembrados para os Certificados de Autoridade de Certificação, a fim de diferenciá-los um do outro.

   ```bash
   ./certGen.sh create_edge_device_ca_certificate "{top_layer_certificate_name}"
   ./certGen.sh create_edge_device_ca_certificate "{lower_layer_certificate_name}"
   ```

   Esse comando de script criará vários arquivos de certificado e chave. No entanto, estamos usando o seguinte certificado e par de chaves em cada dispositivo do IoT Edge referenciado no arquivo de configuração:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Cada dispositivo precisa ter uma cópia do Certificado de AC raiz e uma cópia do próprio Certificado de Autoridade de Certificação do dispositivo e da chave privada. Use uma unidade USB ou uma [cópia de arquivo seguro](https://www.ssh.com/ssh/scp/) para mover os certificados para cada dispositivo.

1. Depois que os certificados forem transferidos, instale a AC raiz para cada dispositivo.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   Esse comando mostrará que um certificado foi adicionado em `/etc/ssl/certs`.

   [Mensagem de instalação do certificado realizada com êxito](./media/tutorial-nested-iot-edge/certificates-success-output.png)

Caso tenha concluído as etapas acima de modo correto, será possível verificar se os certificados estão instalados em `/etc/ssl/certs` acessando esse diretório e pesquisando os certificados instalados.

1. Acesse o diretório `/etc/ssl/certs`:

   ```bash
   cd /etc/ssl/certs
   ```

1. Liste os certificados instalados e execute `grep` para o `azure`:

   ```bash
   ll | grep azure
   ```

   Você verá um hash de certificado vinculado ao Certificado de Autoridade de Certificação raiz, bem como o Certificado de Autoridade de Certificação raiz vinculado à uma cópia no diretório `usr/local/share`.

   [Resultados da pesquisa de certificados do Azure](./media/tutorial-nested-iot-edge/certificate-list-results.png)

Quanto estiver satisfeito com os certificados instalados em cada dispositivo, você estará pronto para prosseguir.

### <a name="install-iot-edge-on-the-devices"></a>Instalar o IoT Edge nos dispositivos

Instalar imagens de runtime do IoT Edge versão 1.2 fornecerá aos seus dispositivos acesso aos recursos necessários para operar como uma hierarquia de dispositivos.

Para instalar o IoT Edge, será preciso instalar a configuração apropriada do repositório, os pré-requisitos e os ativos de lançamento necessários.

1. Instale a configuração do repositório que corresponde ao sistema operacional do seu dispositivo.

   * **Ubuntu Server 18.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Ampliação do SO do Raspberry Pi**:

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

1. Copie a lista gerada para o diretório sources.list.d.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

1. Instale a chave pública do Microsoft GPG.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

1. Atualize as listas de pacotes no dispositivo.

   ```bash
   sudo apt-get update
   ```

1. Instale o mecanismo de Moby.

   ```bash
   sudo apt-get install moby-engine
   ```

1. Instale o IoT Edge e o serviço de identidade de IoT.

   ```bash
   sudo apt-get install aziot-edge
   ```

Caso tenha concluído as etapas acima de modo correto, você viu a mensagem de banner do Azure IoT Edge solicitando uma atualização do arquivo de configuração do Azure IoT Edge, `/etc/aziot/config.toml`, em cada dispositivo da hierarquia. Nesse caso, você está pronto para prosseguir.

### <a name="configure-the-iot-edge-runtime"></a>Configurar o runtime do IoT Edge

Além do provisionamento dos dispositivos, as etapas de configuração estabelecem uma comunicação confiável entre os dispositivos da hierarquia usando os certificados criados anteriormente. As etapas também começam a estabelecer uma estrutura de rede da hierarquia. O dispositivo de camada superior manterá a conectividade com a Internet, permitindo efetuar pull de imagens da nuvem para o runtime dele, enquanto os dispositivos de camada inferior serão roteados pelo dispositivo de camada superior para acessar essas imagens.

É necessário modificar vários componentes do arquivo de configuração para configurar o runtime do IoT Edge. As configurações são um pouco diferentes entre um **dispositivo de camada superior** e um **dispositivo de camada inferior**. Portanto, lembre-se de qual arquivo de configuração do dispositivo você está editando para cada etapa. A configuração do runtime do IoT Edge para seus dispositivos consiste em quatro etapas, todas realizadas com a edição do arquivo de configuração do IoT Edge:

* Provisione manualmente cada dispositivo adicionando a cadeia de conexão do dispositivo ao arquivo de configuração.

* Conclua a configuração dos certificados do dispositivo apontando o arquivo de configuração para o Certificado de Autoridade de Certificação do dispositivo, a chave privada da CA do dispositivo e o Certificado de AC raiz.

* Inicialize o sistema usando o agente do IoT Edge.

* Atualize o parâmetro **hostname** para o dispositivo de **camada superior** e atualize os parâmetros **hostname** e **parent_hostname** para os dispositivos de **camada inferior**.

Conclua estas etapas e reinicie o serviço do IoT Edge para configurar seus dispositivos.

>[!TIP]
>Ao acessar o arquivo de configuração no servidor Nano, será possível usar os botões **Ctrl + W** para pesquisar palavras-chave no arquivo.

1. Crie um arquivo de configuração em cada dispositivo com base no modelo incluído.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml

1. On each device, open the IoT Edge configuration file.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Para o dispositivo de **camada superior**, localize a seção **Nome do host**. Remova a marca de comentário da linha usando o parâmetro `hostname` e atualize o valor para ser o FQDN (nome de domínio totalmente qualificado) ou o endereço IP do dispositivo do IoT Edge. Use qualquer valor escolhido de maneira consistente nos dispositivos da sua hierarquia.

   ```toml
   hostname = "<device fqdn or IP>"
   ```

   >[!TIP]
   >Para colar o conteúdo da área de transferência no Nano, `Shift+Right Click` ou clique em `Shift+Insert`.

1. Para dispositivos do IoT Edge em **camadas inferiores**, localize a seção **Nome do host pai**. Remova a marca de comentário da linha usando o parâmetro `parent_hostname` e atualize o valor para indicar o FQDN ou o IP do dispositivo pai. Use o valor exato inserido no campo **nome do host** do dispositivo pai. Para dispositivos do IoT Edge na **camada superior**, mantenha esse parâmetro com a marca de comentário.

   ```toml
   parent_hostname = "<parent device fqdn or IP>"
   ```

   > [!NOTE]
   > Para as hierarquias com mais de uma camada inferior, atualize o campo *parent_hostname* com o FQDN do dispositivo da camada imediatamente acima.

1. Localize a seção **Certificado do pacote de confiança** do arquivo. Remova a marca de comentário da linha usando o parâmetro `trust_bundle_cert` e atualize o valor com o caminho do URI do arquivo para o Certificado de Autoridade de Certificação raiz compartilhado por todos os dispositivos na hierarquia do gateway.

   ```toml
   trust_bundle_cert = "<root CA certificate>"
   ```

1. Localize a seção **Provisionamento** do arquivo. Remova a marca de comentário das linhas para a opção **Provisionamento manual usando uma cadeia de conexão**. Atualize o valor de **connection_string** para cada dispositivo da hierarquia usando uma cadeia de conexão do dispositivo do IoT Edge.

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string: "<Device connection string>"
   ```

1. Localize a seção **Agente Padrão do Edge**.

   * Para o dispositivo de **camada superior**, atualize o valor da imagem edgeAgent para a versão prévia pública do módulo no Registro de Contêiner do Azure.
   
     ```toml
     [agent.config]
     image = "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4"
     ```

   * Para cada dispositivo do IoT Edge em **camadas inferiores**, atualize a imagem edgeAgent para indicar para o dispositivo pai seguido pela porta em que o proxy de API está escutando. Na próxima seção, você implantará um módulo de proxy de API no dispositivo pai.
   
     ```toml
     [agent.config]
     image = "<parent hostname value>:8000/azureiotedge-agent:1.2.0-rc4"
     ```

1. Localize a seção **Certificado de Autoridade de Certificação do Edge**. Remova a marca de comentário das três primeiras linhas desta seção. Em seguida, atualize os dois parâmetros para indicar o Certificado de Autoridade de Certificação do dispositivo e os arquivos de chave privada da Autoridade de Certificação do dispositivo criados na seção anterior e migrados para o dispositivo do IoT Edge. Forneça os caminhos do URI do arquivo que usam o formato `file:///<path>/<filename>`, como `file:///certs/iot-edge-device-ca-top-layer-device.key.pem`.

   ```yml
   [edge_ca]
   cert = "<File URI path to the device full chain CA certificate unique to this device.>"
   pk = "<File URI path to the device CA private key unique to this device.>"
   ```

   >[!NOTE]
   >Lembre-se de usar o caminho e o nome de arquivo do Certificado de Autoridade de Certificação de **cadeia completa** para preencher o campo `device_ca_cert`.

1. Salve e feche o arquivo.

   `CTRL + X`, `Y`, `Enter`

1. Depois de inserir as informações de provisionamento no arquivo de configuração, aplique as alterações:

   ```bash
   sudo iotedge config apply
   ```

Antes de continuar, verifique se você atualizou o arquivo de configuração de cada dispositivo na hierarquia. Você configurou um **dispositivo de camada superior** e um ou mais **dispositivos de camada inferior**, dependendo da estrutura da hierarquia.

Caso tenha concluído as etapas acima de modo correto, será possível verificar se os dispositivos estão configurados corretamente.

1. Execute as verificações de configuração e conectividade nos dispositivos:

   ```bash
   sudo iotedge check
   ```

No **dispositivo de camada superior**, você verá uma saída com várias avaliações de passagem e pelo menos um aviso. A verificação do `latest security daemon` avisará que outra versão do IoT Edge é a versão estável mais recente porque o IoT Edge versão 1.2 está em versão prévia pública. Talvez você veja avisos adicionais sobre políticas de logs e, dependendo da rede, políticas de DNS.

Em um **dispositivo de camada inferior**, você verá uma saída semelhante ao dispositivo de camada superior, porém com um aviso adicional indicando que não é possível efetuar pull do módulo EdgeAgent do upstream. Isso é aceitável, pois o módulo de Proxy de API do IoT Edge e o módulo de Registro de Contêiner do Docker, pelos quais os dispositivos de camada inferior efetuarão pull de imagens, ainda não foram implantados no **dispositivo de camada superior**.

Um exemplo de saída do comando `iotedge check` será mostrado abaixo:

[Configuração de exemplo e resultados de conectividade](./media/tutorial-nested-iot-edge/configuration-and-connectivity-check-results.png)

Quanto estiver satisfeito com as configurações corretas em cada dispositivo, você estará pronto para prosseguir.

## <a name="deploy-modules-to-the-top-layer-device"></a>Implantar módulos no dispositivo de camada superior

Os módulos servem para concluir a implantação e o runtime do IoT Edge para seus dispositivos, bem como definir ainda mais a estrutura da hierarquia. O módulo de Proxy de API do IoT Edge roteia o tráfego HTTP com segurança em uma porta única de seus dispositivos de camada inferior. O módulo de Registro do Docker permite obter um repositório de imagens do Docker que os dispositivos de camada inferior podem acessar roteando pulls de imagens para um dispositivo de camada superior.

É possível usar o portal do Azure ou a CLI do Azure para implantar módulos em seu dispositivo de camada superior.

>[!NOTE]
>As etapas restantes que deverão concluir a configuração do runtime do IoT Edge e implantar cargas de trabalho não serão executadas em seus dispositivos do IoT Edge.

# <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal do Azure](https://ms.portal.azure.com/):

1. Navegue até seu Hub IoT.

1. No menu no painel esquerdo, em **Gerenciamento Automático de Dispositivo**, selecione **IoT Edge**.

1. Clique na identificação do dispositivo de borda de **camada superior** na lista de dispositivos.

1. Na barra superior, selecione **Definir Módulos**.

1. Selecione **Configurações de Runtime** ao lado do ícone de engrenagem.

1. Em **Hub do Edge**, no campo de imagem, insira `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4`.

   ![Editar a imagem do Hub do Edge](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Adicione as seguintes variáveis de ambiente ao módulo do Hub do Edge:

    | Name | Valor |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Editar as variáveis de ambiente do Hub do Edge](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. Em **Agente do Edge**, no campo de imagem, insira `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4`. Selecione **Salvar**.

1. Adicione o módulo do registro do Docker ao dispositivo de camada superior. Selecione **+ Adicionar** e escolha **Módulo do IoT Edge** na lista suspensa. Forneça o nome `registry` para o módulo do registro do Docker e insira `registry:latest` como o URI da imagem. Em seguida, adicione variáveis de ambiente e crie opções para apontar o módulo de registro local para o registro de contêiner da Microsoft a fim de baixar bidirecionalmente imagens de contêiner fornecê-las em registry:5000.

1. Na guia Variáveis de ambiente, insira o seguinte par nome-valor da variável de ambiente:

    | Name | Valor |
    | - | - |
    | `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

1. Na guia Opções de criação de contêiner, insira o seguinte JSON:

   ```json
   {
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
         }
      }
   }
   ```

1. Em seguida, adicione o módulo de proxy de API ao dispositivo de camada superior. Selecione **+ Adicionar** e escolha **Módulo do Marketplace** na lista suspensa. Pesquise `IoT Edge API Proxy` e selecione o módulo. O proxy de API do IoT Edge usa a porta 8000 e está configurado para usar o módulo do registro chamado `registry` na porta 5000 por padrão.

1. Selecione **Examinar + criar** e **Criar** para concluir a implantação. O runtime do IoT Edge do dispositivo de camada superior, que tem acesso à Internet, efetuará pull das configurações da **versão prévia pública** e a executará para o hub do IoT Edge e o agente do IoT Edge.

   ![Implantação completa contendo o Hub do Edge, o agente do Edge, o módulo do registro e o módulo de proxy de API](./media/tutorial-nested-iot-edge/complete-top-layer-deployment.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. No [Azure Cloud Shell](https://shell.azure.com/), insira o seguinte comando para criar um arquivo deployment.json:

   ```azurecli-interactive
   code deploymentTopLayer.json
   ```

1. Copie o conteúdo do JSON abaixo no deployment.json, salve-o e feche-o.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "registry": {
                           "settings": {
                               "image": "registry:latest",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5000/tcp\":[{\"HostPort\":\"5000\"}]}}}"
                           },
                           "type": "docker",
                           "version": "1.0",
                           "env": {
                               "REGISTRY_PROXY_REMOTEURL": {
                                   "value": "https://mcr.microsoft.com"
                               } 
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       },
                       "IoTEdgeAPIProxy": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-api-proxy",
                               "createOptions": "{\"HostConfig\": {\"PortBindings\": {\"8000/tcp\": [{\"HostPort\":\"8000\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "NGINX_DEFAULT_PORT": {
                                   "value": "8000"
                               },
                               "DOCKER_REQUEST_ROUTE_ADDRESS": {
                                   "value": "registry:5000"
                               },
                               "BLOB_UPLOAD_ROUTE_ADDRESS": {
                                   "value": "AzureBlobStorageonIoTEdge:11002"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Insira o seguinte comando para criar uma implantação para o dispositivo de borda de camada superior:

   ```azurecli-interactive
   az iot edge set-modules --device-id <top_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentTopLayer.json
   ```

---

Caso tenha concluído as etapas acima de modo correto, o **dispositivo de camada superior** deverá relatar quatro módulos: módulo de Proxy de API do IoT Edge, módulo de Registro de Contêiner do Docker e módulos do sistema, conforme **especificado na implantação**. Pode levar alguns minutos para que o dispositivo receba a nova implantação e inicie os módulos. Atualize a página até ver o módulo do sensor de temperatura listado como **Relatado pelo Dispositivo**. Depois que os módulos forem relatados pelo dispositivo, você estará pronto para continuar.

## <a name="deploy-modules-to-the-lower-layer-device"></a>Implantar módulos no dispositivo de camada inferior

Os módulos também servem como cargas de trabalho de dispositivos de camada inferior. O módulo do Sensor de Temperatura Simulada criará dados de telemetria de exemplo para fornecer um fluxo funcional de dados por meio da hierarquia de dispositivos.

É possível usar o portal do Azure ou a CLI do Azure para implantar módulos em dispositivos de camada superior.

# <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal do Azure](https://ms.portal.azure.com/):

1. Navegue até seu Hub IoT.

1. No menu no painel esquerdo, em **Gerenciamento Automático de Dispositivo**, selecione **IoT Edge**.

1. Clique na identificação do dispositivo de camada inferior na lista de dispositivos IoT Edge.

1. Na barra superior, selecione **Definir Módulos**.

1. Selecione **Configurações de Runtime** ao lado do ícone de engrenagem.

1. Em **Hub do Edge**, no campo de imagem, insira `$upstream:8000/azureiotedge-hub:1.2.0-rc4`.

1. Adicione as seguintes variáveis de ambiente ao módulo do Hub do Edge:

    | Name | Valor |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Em **Agente do Edge**, no campo de imagem, insira `$upstream:8000/azureiotedge-agent:1.2.0-rc4`. Selecione **Salvar**.

1. Adicione o módulo de sensor de temperatura. Selecione **+ Adicionar** e escolha **Módulo do Marketplace** na lista suspensa. Pesquise `Simulated Temperature Sensor` e selecione o módulo.

1. Em **Módulos do IoT Edge**, selecione o módulo `Simulated Temperature Sensor` recém-adicionados e atualize o URI de imagem para que ele aponte para `$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0`.

1. Selecione **Salvar**, **Examinar + criar** e **Criar** para concluir a implantação.

   ![Implantação completa contendo o Hub do Edge, o agente do Edge e o sensor de temperatura simulada](./media/tutorial-nested-iot-edge/complete-lower-layer-deployment.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. No [Azure Cloud Shell](https://shell.azure.com/), insira o seguinte comando para criar um arquivo deployment.json:

   ```azurecli-interactive
   code deploymentLowerLayer.json
   ```

1. Copie o conteúdo do JSON abaixo no deployment.json, salve-o e feche-o.

   ```json
   {
       "modulesContent": {
           "$edgeAgent": {
               "properties.desired": {
                   "modules": {
                       "simulatedTemperatureSensor": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-simulated-temperature-sensor:1.0",
                               "createOptions": ""
                           },
                           "type": "docker",
                           "status": "running",
                           "restartPolicy": "always",
                           "version": "1.0"
                       }
                   },
                   "runtime": {
                       "settings": {
                           "minDockerVersion": "v1.25"
                       },
                       "type": "docker"
                   },
                   "schemaVersion": "1.1",
                   "systemModules": {
                       "edgeAgent": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc4",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc4",
                               "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                           },
                           "type": "docker",
                           "env": {
                               "experimentalFeatures__enabled": {
                                   "value": "true"
                               },
                               "experimentalFeatures__nestedEdgeEnabled": {
                                   "value": "true"
                               }
                           },
                           "status": "running",
                           "restartPolicy": "always"
                       }
                   }
               }
           },
           "$edgeHub": {
               "properties.desired": {
                   "routes": {
                       "route": "FROM /messages/* INTO $upstream"
                   },
                   "schemaVersion": "1.1",
                   "storeAndForwardConfiguration": {
                       "timeToLiveSecs": 7200
                   }
               }
           }
       }
   }
   ```

1. Insira o seguinte comando para criar uma implantação de configuração de módulos para o dispositivo de borda de camada inferior:

   ```azurecli-interactive
   az iot edge set-modules --device-id <lower_layer_device_id> --hub-name <iot_hub_name> --content ./deploymentLowerLayer.json

---

Notice that the image URI that we used for the simulated temperature sensor module pointed to `$upstream:8000` instead of to a container registry. We configured this device to not have direct connections to the cloud, because it's in a lower layer. To pull container images, this device requests the image from its parent device instead. At the top layer, the API proxy module routes this container request to the registry module, which handles the image pull.

If you completed the above steps correctly, your **lower layer device** should report three modules: the temperature sensor module and the system modules, as **Specified in Deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by Device**. Once the modules are reported by the device, you are ready to continue.

## Troubleshooting

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc4
```

O valor de `azureiotedge-diagnostics` é extraído do registro de contêiner que está vinculado ao módulo do Registro. Este tutorial tem ele definido por padrão como https://mcr.microsoft.com:

| Nome | Valor |
| - | - |
| `REGISTRY_PROXY_REMOTEURL` | `https://mcr.microsoft.com` |

Se você estiver usando um registro de contêiner privado, verifique se todas as imagens (por exemplo, IoTEdgeAPIProxy, edgeAgent, edgeHub e diagnostics) estão presentes no registro de contêiner.

## <a name="view-generated-data"></a>Exibir os dados gerados

O módulo **Sensor de Temperatura Simulado** que você enviou por push gera dados de ambiente de exemplo. Ele envia mensagens que incluem temperatura ambiente e umidade, temperatura do computador e pressão e um carimbo de data/hora.

Você pode exibir as mensagens que são recebidas pelo seu hub IoT usando a [Extensão do Hub IoT do Azure para o Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Você também pode ver essas mensagens por meio do [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub monitor-events -n <iothub_name> -d <lower-layer-device-name>
   ```

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua as configurações locais e os recursos do Azure criados neste artigo para evitar custos.

Para excluir os recursos:

1. Entre no [portal do Azure](https://portal.azure.com) e selecione **Grupos de recursos**.

2. Selecione o nome do grupo de recursos que contém os recursos de teste do IoT Edge. 

3. Reveja a lista de recursos contidos no grupo de recursos. Se você deseja excluir todos eles, selecione **Excluir grupo de recursos**. Se você quiser excluir apenas alguns deles, clique em cada recurso para excluí-los individualmente. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou dois dispositivos IoT Edge como gateways e definiu um deles como o dispositivo pai do outro. Em seguida, você demonstrou a extração de uma imagem de contêiner para o dispositivo filho por meio de um gateway.

Continue com os outros tutoriais para saber como o Azure IoT Edge pode criar outras soluções para seus negócios.

> [!div class="nextstepaction"]
> [Implantar um modelo do Azure Machine Learning como um módulo](tutorial-deploy-machine-learning.md)
