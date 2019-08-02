---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: e4b366075cb16f62a0e16b5b06da6fb19ffefdb9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172587"
---
1. Entre no dispositivo Data Box. Verifique se ele está desbloqueado.

    ![Painel do Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Acesse **Definir adaptadores de rede**. Anote o endereço IP do dispositivo para o adaptador de rede usado para se conectar ao cliente.

    ![Painel do Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Vá para **Conectar e copiar** e clique em **Rest**.

    ![Painel do Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Na caixa de diálogo **Acessar a conta de armazenamento e carregar dados**, copie o **Ponto de Extremidade de Serviço Blob**.

    ![Painel do Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Inicie o **Bloco de notas** como administrador e, em seguida, abra o arquivo **hosts** localizado em `C:\Windows\System32\Drivers\etc`.
6. Adicione a seguinte entrada ao arquivo **hosts**: `<device IP address> <Blob service endpoint>`
7. Para referência, use a imagem a seguir. Salve o arquivo **hosts**.

    ![Painel do Data Box](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
