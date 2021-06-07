---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 10feccb53b28181d260e7738ab99bdc2e3c9340c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553091"
---
Siga estas etapas para se conectar à conta de armazenamento e verificar a conexão.

1. No Gerenciador de Armazenamento, abra a caixa de diálogo **Conectar-se ao Armazenamento do Azure**. Na caixa de diálogo **Conectar-se ao Armazenamento do Azure**, selecione **Usar um nome e uma chave da conta de armazenamento**.

    ![A captura de tela mostra a caixa de diálogo Conectar-se ao Armazenamento do Azure com a opção Usar um nome e uma chave da conta de armazenamento selecionada.](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Cole o **Nome da conta** e a **Chave de conta** (valor da chave 1 da página **Conectar e copiar** na interface do usuário da Web local). Selecione o domínio dos pontos de extremidade do Armazenamento como **Outros (inserir abaixo)** e, em seguida, forneça o ponto de extremidade de serviço Blob, conforme mostrado abaixo. Marque a opção **Usar HTTP** somente se a transferência for feita via *HTTP*. Se estiver usando *HTTPS*, deixe a opção desmarcada. Selecione **Avançar**.

    ![A captura de tela mostra a caixa de diálogo Conectar ao Nome e à Chave com os valores inseridos.](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Na caixa de diálogo **Resumo da Conexão**, examine as informações fornecidas. Selecione **Conectar**.

    ![A captura de tela mostra a caixa de diálogo Resumo da Conexão com a opção Conectar selecionada.](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. A conta adicionada com êxito é exibida no painel esquerdo do Gerenciador de Armazenamento com (Externa, Outros) acrescentado ao nome. Clique em **Contêineres de Blobs** para exibir o contêiner.

    ![A captura de tela mostra o menu do Gerenciador com a opção Contêineres de Blobs selecionada.](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
