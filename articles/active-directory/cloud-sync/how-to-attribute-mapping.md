---
title: Mapeamento de atributo no Azure AD Connect sincronização de nuvem
description: Este artigo descreve como usar o recurso de sincronização de nuvem do Azure AD Connect para mapear atributos.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdb043374cf6252da3929c8f0cda6c0a4be558b7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555203"
---
# <a name="attribute-mapping-in-azure-ad-connect-cloud-sync"></a>Mapeamento de atributo no Azure AD Connect sincronização de nuvem

Você pode usar o recurso de sincronização de nuvem do Azure Active Directory (Azure AD) Connect para mapear atributos entre seus objetos de grupo ou de usuário local e os objetos no Azure AD. Esse recurso foi adicionado à configuração de sincronização de nuvem.

Você pode personalizar (alterar, excluir ou criar) os mapeamentos de atributo padrão de acordo com suas necessidades de negócios. Para obter uma lista de atributos que são sincronizados, consulte [atributos sincronizados para Azure Active Directory](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understand-types-of-attribute-mapping"></a>Entender os tipos de mapeamento de atributos
Com o mapeamento de atributos, você controla como os atributos são populados no Azure AD. O Azure AD dá suporte a quatro tipos de mapeamento:

- **Direto**: o atributo de destino é populado com o valor de um atributo do objeto vinculado no Active Directory.
- **Constante**: o atributo de destino é populado com uma cadeia de caracteres específica que você especifica.
- **Expressão**: o atributo de destino é preenchido com base no resultado de uma expressão do tipo script. Para obter mais informações, consulte [escrevendo expressões para mapeamentos de atributo em Azure Active Directory](reference-expressions.md).
- **Nenhum**: o atributo de destino é deixado sem modificações. No entanto, se o atributo de destino já estiver vazio, ele será preenchido com o valor padrão que você especificar.

Junto com esses tipos básicos, os mapeamentos de atributo personalizados dão suporte ao conceito de uma atribuição de valor *padrão* opcional. A atribuição de valor padrão garante que um atributo de destino seja preenchido com um valor se o Azure AD ou o objeto de destino não tiver um valor. A configuração mais comum é deixar isso em branco.

## <a name="understand-properties-of-attribute-mapping"></a>Entender as propriedades do mapeamento de atributos

Junto com a propriedade Type, os mapeamentos de atributo dão suporte aos seguintes atributos:

- **Atributo de origem**: o atributo de usuário do sistema de origem (exemplo: Active Directory).
- **Atributo de destino**: o atributo de usuário no sistema de destino (exemplo: Azure Active Directory).
- **Valor padrão se NULL (opcional)**: o valor que será passado para o sistema de destino se o atributo de origem for nulo. Esse valor será provisionado somente quando um usuário for criado. Ele não será provisionado quando você estiver atualizando um usuário existente.  
- **Aplicar este mapeamento**:
  - **Sempre**: aplique esse mapeamento nas ações de criação e atualização do usuário.
  - **Somente durante a criação**: aplique esse mapeamento somente em ações de criação de usuário.

> [!NOTE]
> Este artigo descreve como usar o portal do Azure para mapear atributos.  Para obter informações sobre como usar Microsoft Graph, consulte [transformações](how-to-transformation.md).

## <a name="add-an-attribute-mapping"></a>Adicionar um mapeamento de atributo

Para usar a nova funcionalidade, siga estas etapas:

1.  No portal do Azure, selecione **Azure Active Directory**.
2.  Selecione **Azure AD Connect**.
3.  Selecione **gerenciar sincronização de nuvem**.

    ![Captura de tela que mostra o link para gerenciar a sincronização de nuvem.](media/how-to-install/install-6.png)

4. Em **configuração**, selecione sua configuração.
5. Selecione **clique para editar mapeamentos**.  Esse link abre a tela **mapeamentos de atributo** .

    ![Captura de tela que mostra o link para adicionar atributos.](media/how-to-attribute-mapping/mapping-6.png)

6.  Selecione **Adicionar atributo**.

    ![Captura de tela que mostra o botão para adicionar um atributo, juntamente com listas de atributos e tipos de mapeamento.](media/how-to-attribute-mapping/mapping-1.png)

7. Selecione o tipo de mapeamento. Para este exemplo, estamos usando **expression**.
8. Insira a expressão na caixa. Para este exemplo, estamos usando `Replace([mail], "@contoso.com", , ,"", ,)` .
9. Insira o atributo de destino. Para este exemplo, estamos usando **ExtensionAttribute15**.
10. Selecione quando aplicar esse mapeamento e, em seguida, selecione **aplicar**.

    ![Captura de tela que mostra as caixas preenchidas para criar um mapeamento de atributo.](media/how-to-attribute-mapping/mapping-2a.png)

11. De volta à tela **mapeamentos de atributo** , você deverá ver o novo mapeamento de atributo.  
12. Selecione **salvar esquema**.

    ![Captura de tela que mostra o botão salvar esquema.](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>Testar o mapeamento de atributo

Para testar o mapeamento de atributo, você pode usar o [provisionamento sob demanda](how-to-on-demand-provision.md): 

1. No portal do Azure, selecione **Azure Active Directory**.
2. Selecione **Azure AD Connect**.
3. Selecione **gerenciar provisionamento**.
4. Em **configuração**, selecione sua configuração.
5. Em **validar**, selecione o botão **provisionar um usuário** . 
6. Na tela **provisionar sob demanda** , insira o nome distinto de um usuário ou grupo e selecione o botão **provisionar** . 

   A tela mostra que o provisionamento está em andamento.

   ![Captura de tela que mostra o provisionamento em andamento.](media/how-to-attribute-mapping/mapping-4.png)

8. Após a conclusão do provisionamento, uma tela de êxito aparece com quatro marcas de seleção verdes. 

   Em **executar ação**, selecione **Exibir detalhes**. À direita, você deve ver o novo atributo sincronizado e a expressão aplicada.

   ![Captura de tela que mostra os detalhes de êxito e exportação.](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>Próximas etapas

- [O que é a sincronização em nuvem do Azure AD Connect?](what-is-cloud-sync.md)
- [Escrevendo expressões para mapeamentos de atributo](reference-expressions.md)
- [Atributos sincronizados com o Active Directory do Azure](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
