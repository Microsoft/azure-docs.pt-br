---
title: Provisionamento sob demanda no Azure AD Connect sincronização de nuvem
description: Este artigo descreve como usar o recurso de sincronização de nuvem do Azure AD Connect para testar alterações de configuração.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048b78c7d59b3358dbffe2e3e6eedf41decabb8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554268"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Provisionamento sob demanda no Azure AD Connect sincronização de nuvem

Você pode usar o recurso de sincronização de nuvem do Azure Active Directory (Azure AD) Connect para testar alterações de configuração aplicando essas alterações a um único usuário. Esse provisionamento sob demanda ajuda a validar e verificar se as alterações feitas na configuração foram aplicadas corretamente e estão sendo sincronizadas corretamente com o Azure AD.  

> [!IMPORTANT] 
> Quando você usa o provisionamento sob demanda, os filtros de escopo não são aplicados ao usuário que você selecionou. Você pode usar o provisionamento sob demanda em usuários que estão fora das unidades da organização que você especificou.

## <a name="validate-a-user"></a>Validar um usuário
Para usar o provisionamento sob demanda, siga estas etapas:

1.  No portal do Azure, selecione **Azure Active Directory**.
2.  Selecione **Azure AD Connect**.
3.  Selecione **gerenciar sincronização de nuvem**.

    ![Captura de tela que mostra o link para gerenciar a sincronização de nuvem.](media/how-to-install/install-6.png)
4. Em **configuração**, selecione sua configuração.
5. Em **validar**, selecione o botão **provisionar um usuário** . 

   ![Captura de tela que mostra o botão para provisionar um usuário.](media/how-to-on-demand-provision/on-demand-2.png)

6. Na tela **provisionar sob demanda** , insira o nome distinto de um usuário e selecione o botão **provisionar** .  
 
   ![Captura de tela que mostra um nome de usuário e um botão de provisionamento.](media/how-to-on-demand-provision/on-demand-3.png)
7. Após a conclusão do provisionamento, uma tela de êxito aparece com quatro marcas de seleção verdes. Todos os erros aparecem à esquerda.

   ![Captura de tela que mostra o provisionamento bem-sucedido.](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>Obter detalhes sobre o provisionamento
Agora você pode examinar as informações do usuário e determinar se as alterações feitas na configuração foram aplicadas. O restante deste artigo descreve as seções individuais que aparecem nos detalhes de um usuário sincronizado com êxito.

### <a name="import-user"></a>Importar usuário
A seção **Importar usuário** fornece informações sobre o usuário que foi importado do Active Directory. Essa é a aparência do usuário antes do provisionamento no Azure AD. Selecione o link **Exibir detalhes** para exibir essas informações.

![Captura de tela do botão para exibir detalhes sobre um usuário importado.](media/how-to-on-demand-provision/on-demand-5.png)

Usando essas informações, você pode ver os vários atributos (e seus valores) que foram importados. Se você criou um mapeamento de atributo personalizado, poderá ver o valor aqui.

![Captura de tela que mostra os detalhes do usuário.](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>Determinar se o usuário está no escopo
A seção **determinar se o usuário está no escopo** fornece informações sobre se o usuário que foi importado para o Azure ad está no escopo. Selecione o link **Exibir detalhes** para exibir essas informações.

![Captura de tela do botão para exibir detalhes sobre o escopo do usuário.](media/how-to-on-demand-provision/on-demand-7.png)

Usando essas informações, você pode ver se o usuário está no escopo.

![Captura de tela que mostra detalhes do escopo do usuário.](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>Corresponder o usuário entre o sistema de origem e de destino
A seção **corresponder usuário entre o sistema de origem e de destino** fornece informações sobre se o usuário já existe no Azure AD e se uma junção deve ocorrer em vez de provisionar um novo usuário. Selecione o link **Exibir detalhes** para exibir essas informações.

![Captura de tela do botão para exibir detalhes sobre um usuário correspondente.](media/how-to-on-demand-provision/on-demand-8.png)

Usando essas informações, você pode ver se uma correspondência foi encontrada ou se um novo usuário vai ser criado.

![Captura de tela que mostra informações do usuário.](media/how-to-on-demand-provision/on-demand-11.png)

Os detalhes de correspondência mostram uma mensagem com uma das três seguintes operações:
- **Criar**: um usuário é criado no Azure AD.
- **Atualização**: um usuário é atualizado com base em uma alteração feita na configuração.
- **Excluir**: um usuário é removido do Azure AD.

Dependendo do tipo de operação que você executou, a mensagem irá variar.

### <a name="perform-action"></a>Executar ação
A seção **executar ação** fornece informações sobre o usuário que foi provisionado ou exportado para o Azure ad após a aplicação da configuração. Essa é a aparência do usuário após o provisionamento no Azure AD. Selecione o link **Exibir detalhes** para exibir essas informações.

![Captura de tela do botão para exibir detalhes sobre uma ação executada.](media/how-to-on-demand-provision/on-demand-9.png)

Usando essas informações, você pode ver os valores dos atributos depois que a configuração foi aplicada. Eles parecem semelhantes ao que foi importado ou são diferentes? A configuração foi aplicada com êxito?  

Esse processo permite que você rastreie a transformação do atributo à medida que ele se move pela nuvem e para seu locatário do Azure AD.

![Captura de tela que mostra detalhes do atributo rastreado.](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>Próximas etapas 

- [O que é a sincronização em nuvem do Azure AD Connect?](what-is-cloud-sync.md)
- [Instalar a sincronização de nuvem do Azure AD Connect](how-to-install.md)
 