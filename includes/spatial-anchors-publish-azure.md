---
author: msftradford
ms.service: spatial-anchors
ms.topic: include
ms.date: 11/20/2020
ms.author: parkerra
ms.openlocfilehash: 80685dee7907b81832c94044d1feb8fcf2e41bde
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185218"
---
### <a name="open-the-publish-wizard"></a>Abra o assistente de publicação

No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto **SharingService** e selecione **Publicar**.

O assistente de publicação inicia. 

Selecione **Serviço de Aplicativo** > **Publicar** para abrir o painel **Criar Serviço de Aplicativo**.

### <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no portal do Azure.

No painel **Criar Serviço de Aplicativo**, selecione **Adicionar uma conta** e, depois, entre com sua assinatura do Azure. Se você já estiver conectado, selecione a conta que deseja na lista suspensa.

   > [!NOTE]
   > Se você já estiver conectado, não selecione **Criar** ainda.
   >

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

[!INCLUDE [resource group intro text](resource-group.md)]

Ao lado de **Grupo de recursos**, selecione **Novo**.

Nomeie o grupo de recursos como **myResourceGroup** e selecione **Ok**.

### <a name="create-an-app-service-plan"></a>Criar um plano de Serviço de Aplicativo

[!INCLUDE [app-service-plan](app-service-plan.md)]

Ao lado de **Plano de Hospedagem**, selecione **Novo**.

No painel **Configurar Plano de Hospedagem**, use essas configurações:

| Configuração | Valor sugerido | Descrição |
|-|-|-|
|Plano do Serviço de Aplicativo| MySharingServicePlan | Nome do plano do Serviço de Aplicativo |
| Location | Oeste dos EUA | O datacenter em que o aplicativo Web está hospedado |
| Tamanho | Grátis | O [tipo de preço](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) que determina os recursos de hospedagem |

Selecione **OK**.

### <a name="create-and-publish-the-web-app"></a>Publicar e publicar o aplicativo Web

Em **Nome do Aplicativo**, insira um nome de aplicativo exclusivo. Os caracteres válidos são a – z, 0 – 9 e traços (-) ou aceite o nome exclusivo gerado automaticamente. A URL do aplicativo Web é `https://<app_name>.azurewebsites.net`, em que `<app_name>` é o nome do aplicativo.

Clique em **Criar** para começar a criar os recursos do Azure.

   Após a conclusão do assistente, ele publicará o aplicativo Web ASP.NET Core no Azure e, em seguida, iniciará o aplicativo no seu navegador padrão.

  ![Captura de tela de um aplicativo Web ASP.NET publicado no Azure.](./media/spatial-anchors-azure/web-app-running-live.png)

O nome do aplicativo usado nesta seção é usado como o prefixo de URL no formato `https://<app_name>.azurewebsites.net`. Copie essa URL em um editor de texto para uso posterior.
