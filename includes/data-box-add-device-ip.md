---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: d01cf90c42efbe611748027d0ea47ff8af3e5621
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553099"
---
1. Entre no dispositivo Data Box. Verifique se ele está desbloqueado.

    ![A captura de tela mostra o painel com o dispositivo exibido como Desbloqueado.](media/data-box-add-device-ip/data-box-connect-via-rest-1.png)

2. Acesse **Definir adaptadores de rede**. Anote o endereço IP do dispositivo para o adaptador de rede usado para se conectar ao cliente.

    ![A captura de tela mostra a opção Configurações de Rede, na qual você pode ver o endereço IP.](media/data-box-add-device-ip/data-box-connect-via-rest-2.png)

3. Vá para **Conectar e copiar** e clique em **Rest**.

    ![A captura de tela mostra o painel Conectar e copiar, no qual pode selecionar a REST como uma configuração de acesso.](media/data-box-add-device-ip/data-box-connect-via-rest-3.png)

4. Na caixa de diálogo **Acessar a conta de armazenamento e carregar dados**, copie o **Ponto de Extremidade de Serviço Blob**.

    ![A captura de tela mostra a caixa de diálogo Acessar conta de armazenamento e carregar dados, em que você pode copiar o Ponto de Extremidade de Serviço Blob.](media/data-box-add-device-ip/data-box-connect-via-rest-4.png)

5. Inicie o **Bloco de notas** como administrador e, em seguida, abra o arquivo **hosts** localizado em `C:\Windows\System32\Drivers\etc`.
6. Adicione a seguinte entrada ao arquivo **hosts**: `<device IP address> <Blob service endpoint>`
7. Para referência, use a imagem a seguir. Salve o arquivo **hosts**.

    ![A captura de tela mostra um documento do Bloco de notas com o endereço IP e o ponto de extremidade de serviço blob adicionados.](media/data-box-add-device-ip/data-box-connect-via-rest-5.png)
