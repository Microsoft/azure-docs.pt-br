---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 11/21/2019
ms.author: alkohli
ms.openlocfilehash: 0c6845f081ccbe42e70964eaa939d58597e3b69b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185514"
---
1. Acesse a IU da Web local do seu dispositivo e entre no seu dispositivo. Verifique se o dispositivo está desbloqueado.

2. Acesse a página de **Configurações de rede**. Anote o endereço IP do dispositivo para o adaptador de rede usado para se conectar ao cliente.

3. Se estiver trabalhando com um cliente Windows remoto, inicie o **Bloco de notas** como administrador e abra o arquivo de hosts localizado em `C:\Windows\System32\Drivers\etc`.

4. Adicione a seguinte entrada ao arquivo hosts: `<Device IP address> <Blob service endpoint>`

    Você obteve o ponto de extremidade de serviço Blob da conta de armazenamento do Edge criada no portal do Azure. Você usará apenas o sufixo do ponto de extremidade de serviço Blob.

    Para referência, use a imagem a seguir. Salve o arquivo `hosts`.

    ![Modificar o arquivo de hosts no cliente Windows](media/azure-stack-edge-gateway-add-device-ip-address-blob-service-endpoint/hosts-file-1.png)