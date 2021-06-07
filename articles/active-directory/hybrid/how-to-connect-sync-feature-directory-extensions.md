---
title: 'Sincronização do Azure AD Connect: extensões do Directory | Microsoft Docs'
description: Este tópico descreve o recurso de extensões de diretório no Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/12/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25d4152783129fa1c5950d6cf6287332bf90d32a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97976870"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Sincronização do Azure AD Connect: extensões do Directory
É possível usar extensões de diretório para estender o esquema no Azure AD (Microsoft Azure Active Directory) com seus próprios atributos do Active Directory local. Esse recurso permite compilar aplicativos LOB ao consumir atributos que continuam gerenciando localmente. Esses atributos podem ser consumidos por meio de [extensões](/graph/extensibility-overview
). Você pode ver os atributos disponíveis usando o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer). Você também pode usar esse recurso para criar grupos dinâmicos no Azure AD.

No momento, nenhuma carga de trabalho de Microsoft 365 consome esses atributos.

## <a name="customize-which-attributes-to-synchronize-with-azure-ad"></a>Personalizar quais atributos sincronizar com o Azure AD

Configure quais atributos adicionais você deseja sincronizar no caminho de configurações personalizadas no assistente de instalação.

> [!NOTE]
> Em versões Azure AD Connect anteriores à 1.2.65.0, a caixa de pesquisa para **atributos disponíveis** diferencia maiúsculas de minúsculas.

![Assistente de extensão de esquema](./media/how-to-connect-sync-feature-directory-extensions/extension2.png)  

 A instalação mostra os seguintes atributos, que são candidatos válidos:

* Tipos de objeto de Usuário e de Grupo
* Atributos de valor único: String, Boolean, Integer, Binary
* Atributos de vários valores: String, Binary


>[!NOTE]
> Depois de Azure AD Connect sincronizado o atributo Active Directory com vários valores para o Azure AD como uma extensão de atributo com vários valores, é possível incluir o atributo para a declaração SAML. Mas não é possível consumir esses dados por meio de chamada à API.

A lista de atributos é lida a partir do cache de esquema criado durante a instalação do Azure AD Connect. Se você estendeu o esquema do Active Directory com atributos adicionais, será necessário [atualizar o esquema](how-to-connect-installation-wizard.md#refresh-directory-schema), antes que esses novos atributos fiquem visíveis.

Um objeto no Azure Active Directory pode ter até 100 atributos para extensões de diretório. O comprimento máximo é de 250 caracteres. Se um valor de atributo for maior, o mecanismo de sincronização irá truncá-lo.

## <a name="configuration-changes-in-azure-ad-made-by-the-wizard"></a>Alterações de configuração no Azure AD feitas pelo assistente

Durante a instalação do Azure AD Connect, é registrado um aplicativo no qual esses atributos estão disponíveis. Você pode ver esse aplicativo no Portal do Azure. Seu nome é sempre **aplicativo de extensão do esquema de locatário**.

![Aplicativo de extensão de esquema](./media/how-to-connect-sync-feature-directory-extensions/extension3new.png)

Certifique-se de selecionar **todos os aplicativos** para ver esse aplicativo.

Os atributos são prefixados com a **extensão \_ { \_ ApplicationId}**. ApplicationId tem o mesmo valor para todos os atributos em seu locatário do Azure AD. Você precisará desse valor para todos os outros cenários neste tópico.

## <a name="viewing-attributes-using-the-microsoft-graph-api"></a>Exibindo atributos usando a API de Microsoft Graph

Esses atributos agora estão disponíveis por meio da API Microsoft Graph, usando o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#).

>[!NOTE]
> Na API Microsoft Graph, você precisa solicitar que os atributos sejam retornados. Selecione explicitamente os atributos como este: `https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division` .
>
> Para obter mais informações, consulte [Microsoft Graph: usar parâmetros de consulta](/graph/query-parameters#select-parameter).

>[!NOTE]
> Não há suporte para a sincronização de valores de atributo de AADConnect para atributos de extensão que não são criados pelo AADConnect. Isso pode gerar problemas de desempenho e resultados inesperados. Somente os atributos de extensão criados conforme mostrado acima têm suporte para sincronização.

## <a name="use-the-attributes-in-dynamic-groups"></a>Usar os atributos em grupos dinâmicos

Um dos cenários mais úteis é usar esses atributos em grupos de segurança dinâmica ou Microsoft 365.

1. Crie um novo grupo no Azure AD. Dê a ele um bom nome e verifique se o **tipo de associação** é **Dynamic User**.

   ![Captura de tela com um novo grupo](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup1.png)

2. Selecione para **Adicionar consulta dinâmica**. Se você examinar as propriedades, não verá esses atributos estendidos. Você precisa adicioná-los primeiro. Clique em **obter propriedades de extensão personalizadas**, insira a ID do aplicativo e clique em **Atualizar Propriedades**.

   ![Captura de tela em que as extensões de diretório foram adicionadas](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup2.png) 

3. Abra a lista suspensa Propriedade e observe que os atributos que você adicionou agora estão visíveis.

   ![Captura de tela com novos atributos exibidos na interface do usuário](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup3.png)

   Conclua a expressão para atender às suas necessidades. Em nosso exemplo, a regra é definida como **(User.extension_9d98ed114c4840d298fad781915f27e4_division-EQ "vendas e marketing")**.

4. Depois que o grupo tiver sido criado, dê ao Azure AD algum tempo para preencher os membros e, em seguida, examine os membros.

   ![Captura de tela com membros no grupo dinâmico](./media/how-to-connect-sync-feature-directory-extensions/dynamicgroup4.png)  

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre a configuração de [sincronização do Azure AD Connect](how-to-connect-sync-whatis.md) .

Saiba mais sobre [Como integrar suas identidades locais ao Active Directory do Azure](whatis-hybrid-identity.md).
