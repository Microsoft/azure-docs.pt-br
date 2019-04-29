---
ms.openlocfilehash: 53c683dacbb3b94e34bd8662743673c3a0490d94
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119954"
---
#### <a name="prerequisites"></a>Pré-requisitos
* Uma conta do Azure; você pode criar uma [conta gratuita](https://azure.microsoft.com/free)
* Uma [conta do Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) 

Antes de usar sua conta do Dynamics em um aplicativo lógico, autorize-o a se conectar à conta do CRM Online. Você pode fazer isso de forma fácil usando seu aplicativo lógico no portal do Azure. 

Autorize seu aplicativo lógico a se conectar à sua conta do CRM Online usando as etapas a seguir:

1. Crie um aplicativo lógico. No designer de Aplicativos Lógicos, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e então digite "dynamics" na caixa de pesquisa. Selecione um dos gatilhos ou ações:  
   ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Se você não tiver criado conexões com o Dynamics anteriormente, será solicitado a entrar usando suas credenciais do Dynamics:  
   ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Selecione **Entrar** e insira seu nome de usuário e senha. Selecione **Entrar**. 
   
    Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar aos dados da sua conta do Dynamics e usá-los. 
4. Observe que a conexão foi criada. Agora, continue com as outras etapas no seu aplicativo lógico:  
   ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

