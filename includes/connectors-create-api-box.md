---
ms.openlocfilehash: d1db175ecc2cf61de0debc15d198d6367aae8bdd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119969"
---
### <a name="prerequisites"></a>Pré-requisitos
* Uma conta [box](http://box.com)  

Antes de usar a conta box em um aplicativo lógico, você deve autorizar o aplicativo lógico a se conectar à sua conta box. Felizmente, você pode fazer isso facilmente dentro do seu aplicativo lógico no Portal do Azure.  

Aqui estão as etapas para autorizar seu aplicativo lógico a se conectar à sua conta box:  

1. Para criar uma conexão com o box, no designer do Aplicativo lógico, selecione **Mostrar APIs gerenciadas da Microsoft** na lista suspensa e, em seguida, digite *box* na caixa de pesquisa. Selecione o gatilho ou ação que gostaria de usar:   
   ![etapa de criação de conexão com o box](./media/connectors-create-api-box/box-1.png)  
2. Se você não tiver criado nenhuma conexão com o box antes, suas credenciais do box serão solicitadas. Essas credenciais serão usadas para autorizar seu aplicativo lógico a se conectar e acessar os dados da sua conta do box:  
   ![etapa de criação de conexão com o box](./media/connectors-create-api-box/box-2.png)  
3. Forneça seu nome de usuário do box e a senha para autorizar seu Aplicativo lógico:  
   ![etapa de criação de conexão com o box](./media/connectors-create-api-box/box-3.png)  
4. Permitir que nós nos conectemos ao box:  
   ![etapa de criação de conexão com o box](./media/connectors-create-api-box/box-4.png)  
5. Observe que a conexão foi criada e agora você pode continuar com as outras etapas no seu aplicativo lógico:   
   ![etapa de criação de conexão com o box](./media/connectors-create-api-box/box-5.png)  

