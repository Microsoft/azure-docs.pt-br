---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: 67b9c0ba2566206b0e70db51844b21e5d5d3c261
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172499"
---
Depois que os registros do nome de domínio forem propagados, você deverá ser capaz de usar o navegador para verificar se o nome de domínio personalizado pode ser usado para acessar seu aplicativo Web no Serviço de Aplicativo do Azure.

> [!NOTE]
> Pode levar algum tempo para o CNAME propagar por meio do sistema DNS. Você pode usar um serviço como <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> para verificar se o CNAME está disponível.
> 
> 

Se ainda não tiver adicionado o aplicativo Web como um ponto de extremidade do Gerenciador de Tráfego, você deverá fazer isso antes da resolução de nome funcionar, porque o nome de domínio personalizado é encaminhado para o Gerenciador de Tráfego. Em seguida, o Gerenciador de Tráfego encaminha para o aplicativo Web. Use as informações em [Adicionar ou Excluir Pontos de Extremidade](../articles/traffic-manager/traffic-manager-endpoints.md) para adicionar o aplicativo Web como um ponto de extremidade no perfil do Gerenciador de Tráfego.

> [!NOTE]
> Se seu aplicativo Web não estiver listado, ao adicionar um ponto de extremidade, verifique se ele está configurado como **Padrão** no modo do plano de Serviço de Aplicativo. Você deve usar o modo **Padrão** para o aplicativo Web para trabalhar com o Gerenciador de Tráfego.
> 
> 

1. No seu navegador, abra o [Portal do Azure](https://portal.azure.com).
2. Na guia **Aplicativos Web**, clique no nome do seu aplicativo Web, selecione **Configuraçõe**s e selecione **Domínios personalizados**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. Na folha **Domínios personalizados**, clique em **Adicionar nome do host**.
4. Use as caixas de texto **Nome do host** para inserir o nome de domínio do Gerenciador de Tráfego a associar a este aplicativo Web.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)
5. Clique em **Validar** para salvar a configuração de nome de domínio.
6. Ao clicar em **Validar** , o Azure iniciará o fluxo de trabalho de verificação de domínio. Isso verificará a propriedade do domínio, bem como a disponibilidade do nome do host e o sucesso ou o erro detalhado do relatório com diretrizes prescritivas sobre como corrigir o erro.    
7. Após a validação bem-sucedida, o botão **Adicionar nome do host** ficará ativo e você poderá atribuir o nome do host. Agora, navegue até o nome de domínio personalizado em um navegador. Agora você verá seu aplicativo em execução usando seu nome de domínio personalizado. 
   
   Depois que a configuração estiver concluída, o nome do domínio personalizado será listado na seção **nomes de domínio** de seu aplicativo Web.

Neste ponto, é possível inserir o nome de domínio do Gerenciador de Tráfego no navegador e verificar se é conduzido com êxito ao aplicativo Web.

