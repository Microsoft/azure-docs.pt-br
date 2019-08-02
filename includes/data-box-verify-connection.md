---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/07/2018
ms.author: alkohli
ms.openlocfilehash: 8a09a52db40f4f52219bce3e703e275b0f310c1a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172589"
---
Siga estas etapas para se conectar à conta de armazenamento e verificar a conexão.

1. No Gerenciador de Armazenamento, abra a caixa de diálogo **Conectar-se ao Armazenamento do Azure**. Na caixa de diálogo **Conectar-se ao Armazenamento do Azure**, selecione **Usar um nome e uma chave da conta de armazenamento**.

    ![Painel do Data Box](media/data-box-verify-connection/data-box-connect-via-rest-9.png)

2. Cole o **Nome da conta** e a **Chave de conta** (valor da chave 1 da página **Conectar e copiar** na interface do usuário da Web local). Selecione o domínio dos pontos de extremidade do Armazenamento como **Outros (inserir abaixo)** e, em seguida, forneça o ponto de extremidade de serviço Blob, conforme mostrado abaixo. Marque a opção **Usar HTTP** somente se a transferência for feita via *HTTP*. Se estiver usando *HTTPS*, deixe a opção desmarcada. Selecione **Avançar**.

    ![Painel do Data Box](media/data-box-verify-connection/data-box-connect-via-rest-11.png)    

3. Na caixa de diálogo **Resumo da Conexão**, examine as informações fornecidas. Selecione **Conectar**.

    ![Painel do Data Box](media/data-box-verify-connection/data-box-connect-via-rest-12.png)

4. A conta adicionada com êxito é exibida no painel esquerdo do Gerenciador de Armazenamento com (Externa, Outros) acrescentado ao nome. Clique em **Contêineres de Blobs** para exibir o contêiner.

    ![Painel do Data Box](media/data-box-verify-connection/data-box-connect-via-rest-17.png)
