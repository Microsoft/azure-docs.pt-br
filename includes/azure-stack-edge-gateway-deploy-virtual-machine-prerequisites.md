---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 71d5a910e36762d096763c4f45a13cbdad47414d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730602"
---
Antes de implantar VMs em seu dispositivo Azure Stack Edge, você deve configurar seu cliente para se conectar ao dispositivo via Azure Resource Manager sobre Azure PowerShell. Para obter instruções detalhadas, consulte [conectar-se a Azure Resource Manager em seu dispositivo do Azure Stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).

Certifique-se de que você pode usar as etapas a seguir para acessar o dispositivo do cliente. Você já fez essa configuração quando se conectou ao Azure Resource Manager e agora está verificando se a configuração foi bem-sucedida. 

1. Verifique se a comunicação do Azure Resource Manager está funcionando executando o seguinte comando:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Para chamar as APIs de dispositivo local para autenticar, digite: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Para se conectar via Azure Resource Manager, forneça o nome de usuário *EdgeARMuser* e sua senha.

1. Se você tiver configurado a computação para kubernetes, poderá ignorar esta etapa. Caso contrário, verifique se você habilitou uma interface de rede para computação fazendo o seguinte: 

   a. Na interface do usuário local, vá para configurações de **computação** .  
   b. Selecione a interface de rede que você deseja usar para criar um comutador virtual. As VMs que você criar serão anexadas a um comutador virtual anexado a essa porta e à rede associada. Certifique-se de escolher uma rede que corresponda ao endereço IP que você usará para a VM.  

    ![Captura de tela do painel de configurações de rede de configuração de computação.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

   c. Em **habilitar para computação** na interface de rede, selecione **Sim**. Azure Stack Edge criará e gerenciará um comutador virtual que corresponde a esse adaptador de rede. Não insira IPs específicos para kubernetes no momento. Pode levar vários minutos para habilitar a computação.

    > [!NOTE]
    > Se você estiver criando VMs de GPU, selecione um adaptador de rede que está conectado à Internet. Isso permite que você instale uma extensão de GPU em seu dispositivo.


