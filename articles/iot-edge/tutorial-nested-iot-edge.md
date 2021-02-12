---
title: Tutorial – Criar uma hierarquia de dispositivos IoT Edge – Azure IoT Edge
description: Este tutorial mostra como criar uma estrutura hierárquica de dispositivos IoT Edge usando gateways.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 11/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 05d6607c091361ecee3fcd5527025b6f9fb59051
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573200"
---
# <a name="tutorial-create-a-hierarchy-of-iot-edge-devices-preview"></a>Tutorial: Criar uma hierarquia de dispositivos IoT Edge (versão prévia)

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
> * Usar um módulo de proxy de API para rotear com segurança o tráfego HTTP em uma só porta dos seus dispositivos de camada inferior.

Neste tutorial, as seguintes camadas de rede são definidas:

* **Camada superior**: os dispositivos IoT Edge dessa camada podem se conectar diretamente à nuvem.

* **Camada inferior**: os dispositivos IoT Edge dessa camada não podem se conectar diretamente à nuvem. Eles precisam passar por um ou mais dispositivos IoT Edge intermediários para enviar e receber dados.

Este tutorial usa uma hierarquia de dois dispositivos para simplificar. Um dispositivo, **topLayerDevice**, representa um dispositivo na camada superior da hierarquia, que pode se conectar diretamente à nuvem. Esse dispositivo também será chamado de **dispositivo pai**. O outro dispositivo, **lowerLayerDevice**, representa um dispositivo na camada inferior da hierarquia, que não pode se conectar diretamente à nuvem. Esse dispositivo também será chamado de **dispositivo filho**. Adicione mais dispositivos de camada inferior para representar seu ambiente de produção. A configuração de qualquer dispositivo de camada inferior adicional seguirá a configuração do **lowerLayerDevice**.

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma hierarquia de dispositivos IoT Edge, será necessário:

* Um computador (Windows ou Linux) com conectividade com a Internet.
* Uma conta do Azure com uma assinatura válida. Se você não tiver uma [assinatura do Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou da camada Standard no Azure.
* A CLI do Azure v2.3.1 com a extensão de IoT do Azure v0.10.6 ou superior instalada. Este tutorial usa o [Azure Cloud Shell](../cloud-shell/overview.md). Se você não estiver familiarizado com o Azure Cloud Shell, [confira um guia de início rápido para obter mais detalhes](./quickstart-linux.md#prerequisites).
* Dois dispositivos Linux a serem configurados como dispositivos IoT Edge. Se você não tiver dispositivos disponíveis, crie duas máquinas virtuais do Azure substituindo o texto do espaço reservado no seguinte comando e executando-o duas vezes:

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

Experimente também esse cenário seguindo o [exemplo do Azure IoT Edge para IoT Industrial](https://aka.ms/iotedge-nested-sample) com script, que implanta máquinas virtuais do Azure como dispositivos pré-configurados para simular um ambiente de fábrica.

## <a name="configure-your-iot-edge-device-hierarchy"></a>Configurar a hierarquia de dispositivos IoT Edge

### <a name="create-a-hierarchy-of-iot-edge-devices"></a>Criar uma hierarquia de dispositivos IoT Edge

A primeira etapa, criar seus dispositivos IoT Edge, pode ser realizada por meio do portal do Azure ou da CLI do Azure. Este tutorial criará uma hierarquia de dois dispositivos IoT Edge: **topLayerDevice** e o filho **lowerLayerDevice**.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal do Azure](https://ms.portal.azure.com/), navegue até o seu Hub IoT.

1. No menu no painel esquerdo, em **Gerenciamento Automático de Dispositivo**, selecione **IoT Edge**.

1. Selecione **+ Adicionar um dispositivo IoT Edge**. Esse dispositivo será o dispositivo de camada superior. Portanto, insira uma identificação do dispositivo exclusiva apropriada. Selecione **Salvar**.

1. Selecione **+ Adicionar um dispositivo IoT Edge** novamente. Esse dispositivo será o dispositivo de borda de camada inferior. Portanto, insira uma identificação do dispositivo exclusiva apropriada.

1. Escolha **Definir um dispositivo pai**, escolha o dispositivo de camada superior na lista de dispositivos e selecione **OK**. Selecione **Salvar**.

   ![Como definir o pai para o dispositivo de camada inferior](./media/tutorial-nested-iot-edge/set-parent-device.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. No [Azure Cloud Shell](https://shell.azure.com/), insira o comando a seguir para criar um dispositivo IoT Edge no hub. Esse dispositivo será o dispositivo de camada superior. Portanto, insira uma identificação do dispositivo exclusiva apropriada:

   ```azurecli-interactive
   az iot hub device-identity create --device-id {top_layer_device_id} --edge-enabled --hub-name {hub_name}
   ```

1. Insira o seguinte comando para criar seu dispositivo IoT Edge filho e a relação pai-filho entre os dispositivos:

    ```azurecli-interactive
    az iot hub device-identity create --device-id {lower_layer_device_id} --edge-enabled --pd {top_layer_device_id} --hub-name {iothub_name}
    ```

---

Anote a cadeia de conexão de cada dispositivo IoT Edge. Elas serão usadas mais tarde.

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
   ./certGen.sh create_edge_device_ca_certificate "top-layer-device"
   ./certGen.sh create_edge_device_ca_certificate "lower-layer-device"
   ```

   Este comando de script cria vários arquivos de certificado e de chave, mas estamos usando o seguinte certificado e par de chaves em cada dispositivo IoT Edge e referenciados no arquivo config.yaml:

   * `<WRKDIR>/certs/iot-edge-device-<CA cert name>-full-chain.cert.pem`
   * `<WRKDIR>/private/iot-edge-device-<CA cert name>.key.pem`

Cada dispositivo precisa ter uma cópia do Certificado de AC raiz e uma cópia do próprio Certificado de Autoridade de Certificação do dispositivo e da chave privada. Use uma unidade USB ou uma [cópia de arquivo seguro](https://www.ssh.com/ssh/scp/) para mover os certificados para cada dispositivo.

1. Depois que os certificados forem transferidos, instale a AC raiz para cada dispositivo.

   ```bash
   sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
   sudo update-ca-certificates
   ```

   Este comando deverá gerar um certificado que tenha sido adicionado em /etc/ssl/certs.

### <a name="install-iot-edge-on-the-devices"></a>Instalar o IoT Edge nos dispositivos

Instale o IoT Edge seguindo estas etapas nos dois dispositivos.

1. Instale a configuração do repositório que corresponde ao sistema operacional do seu dispositivo.

   * **Ubuntu Server 16.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
     ```

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

1. Instale o hsmlib e o daemon do IoT Edge. Para ver os ativos de outras distribuições do Linux, [acesse a versão do GitHub](https://github.com/Azure/azure-iotedge/releases/tag/1.2.0-rc1). <!-- Update with proper image links on release -->

   ```bash
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/libiothsm-std_1.2.0_rc1-1-1_debian9_amd64.deb -o libiothsm-std.deb
   curl -L https://github.com/Azure/azure-iotedge/releases/download/1.2.0-rc1/iotedge_1.2.0_rc1-1_debian9_amd64.deb -o iotedge.deb
   sudo dpkg -i ./libiothsm-std.deb
   sudo dpkg -i ./iotedge.deb
   ```

### <a name="configure-the-iot-edge-runtime"></a>Configurar o runtime do IoT Edge

Configure o runtime do IoT Edge seguindo estas etapas nos dois dispositivos. A configuração do runtime do IoT Edge para seus dispositivos consiste em quatro etapas, todas realizadas com a edição do arquivo de configuração do IoT Edge:

1. Provisione manualmente cada dispositivo adicionando a cadeia de conexão do dispositivo ao arquivo de configuração.

1. Conclua a configuração dos certificados do dispositivo apontando o arquivo de configuração para o Certificado de Autoridade de Certificação do dispositivo, a chave privada da CA do dispositivo e o Certificado de AC raiz.

1. Inicialize o sistema usando o agente do IoT Edge.

1. Atualize o parâmetro **hostname** para o dispositivo de **camada superior** e atualize os parâmetros **hostname** e **parent_hostname** para os dispositivos de **camada inferior**.

Conclua estas etapas e reinicie o serviço do IoT Edge para configurar seus dispositivos.

1. Em cada dispositivo, abra o arquivo de configuração do IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Encontre as configurações de provisionamento do arquivo e remova a marca de comentário da seção **Configuração de provisionamento manual usando uma cadeia de conexão**.

1. Atualizar o valor de **device_connection_string** com a cadeia de caracteres de conexão do dispositivo IoT Edge. Verifique se alguma outra seção de provisionamento foi comentada. Verifique se a linha **provisionamento:** não tem nenhum espaço em branco precedente e se os itens aninhados são recuados em dois espaços.

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
     dynamic_reprovisioning: false
   ```

   >[!TIP]
   >Para colar o conteúdo da área de transferência no Nano, `Shift+Right Click` ou clique em `Shift+Insert`.

1. Encontre a seção **certificados**. Remova a marca de comentário e atualize os três campos do certificado para que eles apontem para os certificados criados na seção anterior e movidos para o dispositivo IoT Edge. Forneça os caminhos de URI de arquivo, que usam o formato `file:///<path>/<filename>`.

   ```yml
   certificates:
     device_ca_cert: "<File URI path to the device CA certificate unique to this device.>"
     device_ca_pk: "<File URI path to the device CA private key unique to this device.>"
     trusted_ca_certs: "<File URI path to the root CA certificate shared by all devices in the gateway hierarchy.>"
   ```

1. Para o dispositivo de **camada superior**, encontre o parâmetro **hostname**. Atualize o valor para que ele seja o FQDN (nome de domínio totalmente qualificado) ou o endereço IP do dispositivo IoT Edge. Use qualquer valor escolhido de maneira consistente nos dispositivos da sua hierarquia.

   ```yml
   hostname: <device fqdn or IP>
   ```

1. Para os dispositivos IoT Edge em **camadas inferiores**, atualize o arquivo de configuração para que ele aponte para o FQDN ou o IP do dispositivo pai, correspondendo a qualquer um que ele seja no campo **hostname** do dispositivo pai. Para os dispositivos IoT Edge na **camada superior**, mantenha esse parâmetro comentado.

   ```yml
   parent_hostname: <parent device fqdn or IP>
   ```

   > [!NOTE]
   > Para as hierarquias com mais de uma camada inferior, atualize o campo *parent_hostname* com o FQDN do dispositivo da camada imediatamente acima.

1. Para o dispositivo de **camada superior**, encontre a seção YAML **agente** e atualize o valor da imagem para a versão correta do agente do IoT Edge. Nesse caso, apontaremos o agente do IoT Edge da camada superior para o Registro de Contêiner do Azure com a versão prévia pública da imagem do agente do IoT Edge disponível.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Para os dispositivos IoT Edge em **camadas inferiores**, atualize o nome de domínio do valor da imagem para que ele aponte para o FQDN ou o IP do dispositivo pai, seguido da porta do proxy de API, 8000. Você adicionará o módulo de proxy de API na próxima seção.

   ```yml
   agent:
     name: "edgeAgent"
     type: "docker"
     env: {}
     config:
       image: "<parent_device_fqdn_or_ip>:8000/azureiotedge-agent:1.2.0-rc2"
       auth: {}
   ```

1. Salve e feche o arquivo.

   `CTRL + X`, `Y`, `Enter`

1. Depois de inserir as informações de provisionamento no arquivo de configuração, reinicie o daemon:

   ```bash
   sudo systemctl restart iotedge
   ```

## <a name="deploy-modules-to-the-top-layer-device"></a>Implantar módulos no dispositivo de camada superior

As etapas restantes necessárias para concluir a configuração do runtime do IoT Edge e implantar cargas de trabalho são realizadas na nuvem por meio do portal do Azure ou da CLI do Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal do Azure](https://ms.portal.azure.com/):

1. Navegue até seu Hub IoT.

1. No menu no painel esquerdo, em **Gerenciamento Automático de Dispositivo**, selecione **IoT Edge**.

1. Clique na identificação do dispositivo de borda de **camada superior** na lista de dispositivos.

1. Na barra superior, selecione **Definir Módulos**.

1. Selecione **Configurações de Runtime** ao lado do ícone de engrenagem.

1. Em **Hub do Edge**, no campo de imagem, insira `mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2`.

   ![Editar a imagem do Hub do Edge](./media/tutorial-nested-iot-edge/edge-hub-image.png)

1. Adicione as seguintes variáveis de ambiente ao módulo do Hub do Edge:

    | Name | Valor |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

   ![Editar as variáveis de ambiente do Hub do Edge](./media/tutorial-nested-iot-edge/edge-hub-environment-variables.png)

1. Em **Agente do Edge**, no campo de imagem, insira `mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2`. Selecione **Salvar**.

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
                       "dockerContainerRegistry": {
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
                               "image": "mcr.microsoft.com/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "mcr.microsoft.com/azureiotedge-hub:1.2.0-rc2",
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

## <a name="deploy-modules-to-the-lower-layer-device"></a>Implantar módulos no dispositivo de camada inferior

Use o portal do Azure e a CLI do Azure para implantar cargas de trabalho da nuvem nos seus dispositivos de **camada inferior**.

# <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal do Azure](https://ms.portal.azure.com/):

1. Navegue até seu Hub IoT.

1. No menu no painel esquerdo, em **Gerenciamento Automático de Dispositivo**, selecione **IoT Edge**.

1. Clique na identificação do dispositivo de camada inferior na lista de dispositivos IoT Edge.

1. Na barra superior, selecione **Definir Módulos**.

1. Selecione **Configurações de Runtime** ao lado do ícone de engrenagem.

1. Em **Hub do Edge**, no campo de imagem, insira `$upstream:8000/azureiotedge-hub:1.2.0-rc2`.

1. Adicione as seguintes variáveis de ambiente ao módulo do Hub do Edge:

    | Name | Valor |
    | - | - |
    | `experimentalFeatures__enabled` | `true` |
    | `experimentalFeatures__nestedEdgeEnabled` | `true` |

1. Em **Agente do Edge**, no campo de imagem, insira `$upstream:8000/azureiotedge-agent:1.2.0-rc2`. Selecione **Salvar**.

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
                               "image": "$upstream:8000/azureiotedge-agent:1.2.0-rc2",
                               "createOptions": ""
                           },
                           "type": "docker"
                       },
                       "edgeHub": {
                           "settings": {
                               "image": "$upstream:8000/azureiotedge-hub:1.2.0-rc2",
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

On the device details page for your lower layer IoT Edge device, you should now see the temperature sensor module listed along the system modules as **Specified in deployment**. It may take a few minutes for the device to receive its new deployment, request the container image, and start the module. Refresh the page until you see the temperature sensor module listed as **Reported by device**.

## IotEdge check

Run the `iotedge check` command to verify the configuration and to troubleshoot errors.

You can run `iotedge check` in a nested hierarchy, even if the child machines don't have direct internet access.

When you run `iotedge check` from the lower layer, the program tries to pull the image from the parent through port 443.

In this tutorial, we use port 8000, so we need to specify it:

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:8000/azureiotedge-diagnostics:1.2.0-rc2
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
