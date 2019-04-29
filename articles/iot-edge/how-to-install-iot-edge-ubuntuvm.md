---
title: Executar o Azure IoT Edge em máquinas virtuais do Ubuntu | Microsoft Docs
description: Instruções de configuração do Azure IoT Edge em Máquinas Virtuais do Ubuntu 16.04 do Microsoft Azure Marketplace
author: gregman-msft
manager: arjmands
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/29/2019
ms.author: gregman
ms.openlocfilehash: 7062bd2dbd8c375b8dd3fad348e5cc26de8f36d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595125"
---
# <a name="run-azure-iot-edge-on-ubuntu-virtual-machines"></a>Executar Azure IoT Edge em Máquinas Virtuais do Ubuntu

O tempo de execução do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O tempo de execução pode ser implantado em dispositivos pequenos como um Raspberry Pi ou grandes como um servidor industrial. Após um dispositivo ser configurado com o tempo de execução do IoT Edge, você poderá começar a implantar a lógica de negócios da nuvem nele.

Para saber mais sobre como funciona o tempo de execução do IoT Edge e quais componentes estão incluídos, veja [Entender o tempo de execução do Azure IoT Edge e sua arquitetura](iot-edge-runtime.md).

Este artigo lista as etapas para executar o tempo de execução do Azure IoT Edge em uma Máquina Virtual do Ubuntu 16.04 usando o [Azure IoT Edge na oferta do Azure Marketplace do Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) pré-configurado. 

Na primeira inicialização, o Azure IoT Edge na VM do Ubuntu pré-instala a última versão do tempo de execução do Azure IoT Edge. Isso também inclui um script para definir a cadeia de conexão e reiniciar o tempo de execução, que pode ser disparado remotamente por meio do portal da VM do Azure ou da linha de comando do Azure, permitindo configurar e conectar facilmente o dispositivo do IoT Edge sem iniciar uma sessão SSH ou área de trabalho remota. Esse script aguardará para definir a cadeia de conexão até que o cliente do IoT Edge esteja totalmente instalado para que não seja necessário incorporá-lo à automação.

## <a name="deploy-from-the-azure-marketplace"></a>Implantar do Azure Marketplace
1.  Navegue até a oferta do Marketplace [Azure IoT Edge no Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm) ou pesquise "Azure IoT Edge no Ubuntu" no [Azure Marketplace](https://azuremarketplace.microsoft.com/)
2.  Selecione **OBTER AGORA** e, em seguida, **Continuar** na próxima caixa de diálogo.
3.  Quando estiver no portal do Azure, selecione **Criar** e siga o assistente para implantar a VM. 
    *   Se for a primeira vez que você irá experimentar uma VM, será mais fácil usar uma senha e habilitar o SSH no menu da porta de entrada pública. 
    *   Se você tiver uma carga de trabalho com muitos recursos, atualize o tamanho da máquina virtual, adicionando mais CPUs e/ou memória.
4.  Depois que a máquina virtual for implantada, configure-a para conectar ao Hub IoT:
    1.  Copie a cadeia de conexão do dispositivo do dispositivo do IoT Edge criado no Hub IoT (você poderá seguir a guia de instruções [Registrar um novo dispositivo do Azure IoT Edge no portal do Azure](how-to-register-device-portal.md), se não estiver familiarizado com esse processo )
    1.  Selecionar o recurso de máquina virtual recém-criada no portal do Azure e abrir a opção **executar comando**
    1.  Selecionar a opção **RunShellScript**
    1.  Execute o script abaixo através da janela de comando com a cadeia de conexão do dispositivo: `/etc/iotedge/configedge.sh “{device_connection_string}”`
    1.  Selecionar **Executar**
    1.  Aguarde alguns instantes e a tela deverá exibir uma mensagem de êxito, indicando que a cadeia de conexão foi configurada com êxito.


## <a name="deploy-from-the-azure-portal"></a>Implantar do portal do Azure
No portal do Azure, pesquise "Azure IoT Edge" e selecione **Ubuntu Server 16.04 LTS + tempo de execução do Azure IoT Edge** para iniciar o fluxo de trabalho de criação de VM. Em seguida, conclua as etapas 3 e 4 nas instruções "Implantar do Azure Marketplace" acima.

## <a name="deploy-from-azure-cli"></a>Implantar da CLI do Azure
1. Se esta for sua primeira vez implantando uma máquina virtual a partir da CLI, será necessário habilitar a implantação programática para a assinatura do Azure:
   1. Abrir a oferta do Marketplace [Azure IoT Edge no Ubuntu](https://aka.ms/azure-iot-edge-ubuntuvm)
   1. Selecione **OBTER AGORA** e **Continuar** na caixa de diálogo subsequente
   1. Selecione **Deseja implantar programaticamente? Introdução** na parte inferior da caixa de diálogo no portal
   1. Clique no botão **Habilitar** na página **Configurar Implantação Programática** e, em seguida, clique em **Salvar**
1. Se você estiver usando a CLI do Azure na área de trabalho, comece fazendo logon:

   ```azurecli-interactive
   az login
   ```
    
1. Se você tiver várias assinaturas, selecione a assinatura que gostaria de usar:
   1. Liste suas assinaturas:
    
      ```azurecli-interactive
      az account list --output table
      ```
    
   1. Copie o campo SubscriptionID da assinatura que você quer usar
   1. Execute esse comando com a ID que você acabou de copiar:
    
      ```azurecli-interactive 
      az account set -s {SubscriptionId}
      ```
    
1. Crie um novo grupo de recursos (ou especifique um existente nas próximas etapas):

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```
    
1. Crie uma nova máquina virtual:

   ```azurecli-interactive
   az vm create --resource-group IoTEdgeResources --name EdgeVM –-image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest --admin-username azureuser --generate-ssh-keys --size Standard_DS1_v2
   ```

1. Defina a cadeia de conexão do dispositivo (você poderá seguir o guia de instruções [Registrar um novo dispositivo do Azure IoT Edge com a CLI do Azure](how-to-register-device-cli.md), se você não estiver familiarizado com esse processo):

   ```azurecli-interactive
   az vm run-command invoke -g IoTEdgeResources -n EdgeVM --command-id RunShellScript --script "/etc/iotedge/configedge.sh '{device_connection_string}'"
   ```

Se você quiser SSH nessa VM após a instalação, use o publicIpAddress com o comando: `ssh azureuser@{publicIpAddress}`


## <a name="next-steps"></a>Próximas etapas

Agora que você tem um dispositivo IoT Edge provisionado com o tempo de execução instalado, é possível [implantar os módulos do IoT Edge](how-to-deploy-modules-portal.md).

Se você estiver tendo problemas com o tempo de execução do IoT Edge instalado corretamente, confira a [solução de problemas](troubleshoot.md) página.

Para atualizar uma instalação existente para a versão mais recente do IoT Edge, consulte [Atualizar o IoT Edge de segurança e o tempo de execução do IoT Edge](how-to-update-iot-edge.md).
