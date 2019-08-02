---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 06/20/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: 72a69359d412a7560472fbb73ec525ab5d4a4fce
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/21/2019
ms.locfileid: "67325786"
---
1. Entre no [Portal do Azure].

2. Clique em **Criar um recurso**.

3. Na caixa de pesquisa, digite **aplicativo Web**.
    
4. Na lista de resultados, selecione **aplicativo Web** do Marketplace.

5. Selecione suas **assinatura** e **grupo de recursos** (selecione um grupo de recursos _ou_ criar um novo (usando o mesmo nome que seu aplicativo)).

6. Escolha um único **nome** do seu aplicativo web.

7. Escolha o padrão **Publish** opção como **código**.

8. No **pilha de tempo de execução**, você precisa selecionar uma versão sob **ASP.NET** ou **nó**. Se você estiver criando um back-end .NET, selecione uma versão em ASP.NET. Caso contrário, se você estiver direcionando um aplicativo de nó com base em, selecione uma da versão do nó.

9. Selecione o direito **sistema operacional**, Linux ou Windows. 

10. Selecione o **região** onde gostaria que este aplicativo a ser implantado. 

11. Selecionar as devidas **plano do serviço de aplicativo** e clique em **revisão e criar**. 

12. Em **Grupo de Recursos**, selecione um grupo de recursos existente _ou_ crie um novo (usando o mesmo nome que o aplicativo).

13. Clique em **Criar**. Aguarde alguns minutos para que o serviço seja implantado com êxito antes de continuar. Observe o ícone de notificações (sino) no cabeçalho do portal para atualizações de status.

14. Depois que a implantação for concluída, clique no **detalhes de implantação** seção e, em seguida, clique no recurso do tipo **Microsoft**. Ele você navegará para o aplicativo de Web do serviço de aplicativo que você acabou de criar. 

15. Clique no **configuração** folha sob **configurações** e, no **as configurações do aplicativo**, clique no **nova configuração de aplicativo** botão.

16. No **Adicionar/Editar configuração de aplicativo** , insira **nome** como **MobileAppsManagement_EXTENSION_VERSION** e o valor como **mais recente** e Pressione Okey.

Você está pronto para usar esse aplicativo Web do serviço de aplicativo como um aplicativo móvel de recém-criado.

<!-- URLs. -->
[Portal do Azure]: https://portal.azure.com/