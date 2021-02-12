---
title: Editor de atributos de sincronização de nuvem Azure AD Connect
description: Este artigo descreve como usar o editor de atributos.
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
ms.openlocfilehash: c6d2adbd0fe0715cb22ac158d1804f53384f8b94
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682098"
---
# <a name="azure-ad-connect-cloud-sync-attribute-mapping"></a>Mapeamento de atributo de sincronização de nuvem Azure AD Connect

Azure AD Connect a sincronização de nuvem introduziu um novo recurso, que permitirá que você mapeie facilmente atributos entre seus objetos de usuário/grupo local e os objetos no Azure AD.  Este recurso foi adicionado à configuração de sincronização de nuvem.

Você pode personalizar mapeamentos de atributo padrão de acordo com suas necessidades comerciais. Sendo assim, você pode alterar ou excluir mapeamentos de atributos existentes ou criar mapeamentos.  Para obter uma lista de atributos que são sincronizados, consulte [atributos que são sincronizados](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md).

## <a name="understanding-attribute-mapping-types"></a>Noções básicas sobre tipos de mapeamento de atributo
Com os mapeamentos de atributos, você controla como os atributos são populados no Azure AD.
Há quatro tipos diferentes de mapeamento com suporte:

- **Direto** – o atributo de destino é populado com o valor de um atributo do objeto vinculado no AD.
- **Constante** – o atributo de destino é populado com uma cadeia de caracteres especificada por você.
- **Expressão** – o atributo de destino é preenchido com base no resultado de uma expressão de script.
  Para obter mais informações, consulte [escrevendo expressões para mapeamentos de atributos](reference-expressions.md).
- **Nenhum** – o atributo de destino é deixado inalterado. No entanto, se estiver vazio, o atributo de destino será preenchido com o Valor padrão que você especificar.

Em conjunto com esses quatro tipos básicos, os mapeamentos de atributos personalizados dão suporte ao conceito de atribuição de um valor **padrão** opcional. A atribuição de valor padrão assegura que o atributo de destino seja preenchido com um valor quando não há um no Azure AD nem no objeto de destino. A configuração mais comum é deixar isso em branco.

## <a name="understanding-attribute-mapping-properties"></a>Noções básicas de propriedades de mapeamento de atributo

Na seção anterior, você foi apresentado à propriedade do tipo de mapeamento de atributos.
Além dessa propriedade, os mapeamentos de atributos também dão suporte aos seguintes atributos:

- **Atributo de origem** -o atributo de usuário do sistema de origem (exemplo: Active Directory).
- **Atributo de destino** – o atributo de usuário no sistema de destino (exemplo: Azure Active Directory).
- **Valor padrão se nulo (opcional)** – o valor que será passado para o sistema de destino se o atributo de origem for nulo. Ele será provisionado apenas quando um usuário for criado. O "valor padrão quando nulo" não será provisionado ao atualizar um usuário existente.  
- **Aplicar esse mapeamento**
  - **Sempre** – aplicar esse mapeamento nas ações de atualização e de criação de usuário.
  - **Somente durante a criação** – aplicar esse mapeamento somente a ações de criação de usuário.

> [!NOTE]
> Este documento descreve como usar o portal do Azure para mapear atributos.  Para obter informações sobre como usar o Graph, consulte [transformações](how-to-transformation.md)

## <a name="using-attribute-mapping"></a>Usando o mapeamento de atributos

Para usar o novo recurso, siga as etapas abaixo.

1.  No portal do Azure, selecione **Azure Active Directory**.
2.  Selecione **Azure AD Connect**.
3.  Selecione **gerenciar sincronização de nuvem**.

    ![Gerenciar provisionamento](media/how-to-install/install-6.png)

4. Em **configuração**, selecione sua configuração.
5. Selecione **clique para editar mapeamentos**.  Isso abrirá a tela mapeamento de atributos.

    ![Adicionando atributos](media/how-to-attribute-mapping/mapping-6.png)

6.  Clique em **Adicionar atributo**.

    ![Tipo de mapeamento](media/how-to-attribute-mapping/mapping-1.png)

7. Selecione o **tipo de mapeamento**.  Neste exemplo, usamos a expressão.
8.  Insira a expressão na caixa.  Para este exemplo, estamos usando: `Replace([mail], "@contoso.com", , ,"", ,).`
9.  Insira o atributo de destino.  Neste exemplo, usamos ExtensionAttribute15.
10. Selecione quando aplicar isso e clique em **aplicar**

    ![Editar mapeamentos](media/how-to-attribute-mapping/mapping-2a.png)

11. De volta à tela de mapeamento de atributo, você deve ver o novo mapeamento de atributo.  
12. Clique em **salvar esquema**.

    ![Salvar esquema](media/how-to-attribute-mapping/mapping-3.png)

## <a name="test-your-attribute-mapping"></a>Testar o mapeamento de atributo

Para testar o mapeamento de atributo, você pode usar o [provisionamento sob demanda](how-to-on-demand-provision.md).  Na lista 

1. No portal do Azure, selecione **Azure Active Directory**.
2. Selecione **Azure AD Connect**.
3. Selecione **gerenciar provisionamento**.
4. Em **configuração**, selecione sua configuração.
5. Em **validar** , clique no botão **provisionar um usuário** . 
6. Na tela de provisionamento sob demanda.  Insira o **nome distinto** de um usuário ou grupo e clique no botão **provisionar** .  
7. Após a conclusão, você deverá ver uma tela de êxito e quatro caixas de seleção verdes indicando que ela foi provisionada com êxito.  

    ![Sucesso para provisionamento](media/how-to-attribute-mapping/mapping-4.png)

8. Em **executar ação** , clique em **Exibir detalhes**.  À direita, você deve ver o novo atributo sincronizado e a expressão aplicada.

  ![Executar ação](media/how-to-attribute-mapping/mapping-5.png)

## <a name="next-steps"></a>Próximas etapas

- [O que é a sincronização de nuvem do Azure AD Connect?](what-is-cloud-sync.md)
- [Escrevendo expressões para mapeamentos de atributo](reference-expressions.md)
- [Atributos sincronizados](../hybrid/reference-connect-sync-attributes-synchronized.md?context=azure%2factive-directory%2fcloud-provisioning%2fcontext%2fcp-context/hybrid/reference-connect-sync-attributes-synchronized.md)
