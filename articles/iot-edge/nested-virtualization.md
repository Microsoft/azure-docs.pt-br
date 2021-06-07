---
title: Virtualização aninhada para Azure IoT Edge para Linux no Windows | Microsoft Docs
description: Saiba mais sobre como navegar na virtualização aninhada no Azure IoT Edge para Linux no Windows.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 0e0021ec3564ca079f9ab02fe5ed3f0cfa5a1560
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608973"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Virtualização aninhada para Azure IoT Edge para Linux no Windows
Há duas formas de virtualização aninhada compatíveis com o Azure IoT Edge para Linux no Windows. Os usuários podem optar por implantar por meio de uma VM local ou de uma VM do Azure. Este artigo fornecerá aos usuários a clareza de qual opção é melhor para seu cenário e fornecerá informações sobre os requisitos de configuração.

> [!NOTE]
>
> Certifique-se de habilitar uma [opção netowrking](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) para virtualização aninhada. A falha ao fazer isso resultará em erros de instalação do EFLOW. 

## <a name="deployment-on-local-vm"></a>Implantação na VM local
Essa é a abordagem de linha de base para qualquer VM do Windows que hospede Azure IoT Edge para Linux no Windows. Nesse caso, a virtualização aninhada precisa ser habilitada antes de iniciar a implantação. Leia [executar o Hyper-V em uma máquina virtual com virtualização aninhada](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) para obter mais informações sobre como configurar esse cenário.

Se você estiver usando o Windows Server, certifique [-se de instalar a função Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="deployment-on-azure-vms"></a>Implantação em VMs do Azure
Se você optar por implantar em VMs do Azure, observe que não há nenhum comutador externo por design. Azure IoT Edge para Linux no Windows também não é compatível com uma VM do Azure que executa o SKU do servidor, a menos que um script específico seja executado, o que abre um comutador padrão. Para obter mais informações sobre como exibir um comutador padrão, consulte a [seção Windows Server](#windows-server) abaixo. 

> [!NOTE]
>
> Todas as VMs do Azure que devem hospedar o EFLOW deve ser uma VM que [dá suporte à virtualização aninhada](../virtual-machines/acu.md)


## <a name="limited-use-of-external-switch"></a>Uso limitado de comutador externo
Em qualquer cenário em que a VM não puder obter um endereço IP por meio de um comutador externo, a funcionalidade de implantação usará automaticamente a opção padrão interna para a implantação. Isso significa que o gerenciamento do Azure IoT Edge para VM Linux só pode ser executado no próprio dispositivo de destino (ou seja, conectar-se ao Azure IoT Edge para VM do Linux por meio da extensão SSH do WAC PowerShell só é possível no localhost).

## <a name="windows-server"></a>Windows Server
Para usuários do Windows Server, observe que o Azure IoT Edge para Linux no Windows não oferece suporte automaticamente à opção padrão.

* Consequências da VM local: Verifique se a VM EFLOW pode obter um endereço IP por meio do comutador externo.

* Consequências da VM do Azure: como não há um comutador externo em VMs do Azure, não é possível implantar o EFLOW antes de configurar um comutador interno no servidor.

Não há nenhuma opção padrão em SKUs de servidor por padrão (independentemente de a SKU do servidor ser executada em uma VM do Azure ou não). Ao implantar em uma VM do Azure onde o comutador externo não pode ser usado, o comutador padrão precisa ser configurado e configurado manualmente antes de Azure IoT Edge para implantação do Linux no Windows. Nossa funcionalidade de implantação aborda isso, pois requer a configuração de IP para o comutador interno, uma configuração de NAT e Instalando e configurando um servidor DHCP. Nossa funcionalidade de implantação na visualização pública declara que não emendar com essas configurações para não prejudicar as configurações de rede em implantações produtivas.

* Informações relevantes sobre como configurar a opção padrão manualmente podem ser encontradas aqui: [como habilitar a virtualização aninhada em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/nested-virtualization)
* A documentação sobre como configurar um servidor DHCP para esse cenário pode ser encontrada aqui: [implantar o DHCP usando o Windows PowerShell](https://docs.microsoft.com/windows-server/networking/technologies/dhcp/dhcp-deploy-wps)
