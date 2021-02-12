---
title: Proxy e Tableau do Aplicativo do Azure Active Directory | Microsoft Docs
description: Saiba como usar o Proxy de aplicativo do Azure Active Directory (AD do Azure) para fornecer acesso remoto para sua implantação Tableau.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6720a5ad963bc73e11ef7b46150e946521928c01
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258618"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Proxy e Tableau do Aplicativo do Azure Active Directory 

O Proxy de Aplicativo do Active Directory do Azure e o Tableau fizeram uma parceria para garantir que você possa usar facilmente o Proxy de Aplicativo para fornecer acesso remoto à sua implantação do Tableau. Este artigo explica como configurar esse cenário.  

## <a name="prerequisites"></a>Pré-requisitos 

O cenário neste artigo pressupõe que você tenha:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) configurado. 

- Um[conector do Proxy de Aplicativos](application-proxy-add-on-premises-application.md)instalado 

 
## <a name="enabling-application-proxy-for-tableau"></a>Habilitando o Proxy de Aplicativo para o Tableau 

O Proxy de Aplicativo dá suporte para Fluxo de Concessão do OAuth 2.0, que é necessário para que o Tableau funcione corretamente. Isso significa que não há mais nenhuma etapa especial necessária para habilitar esse aplicativo, além de configurá-lo seguindo as etapas de publicação abaixo.


## <a name="publish-your-applications-in-azure"></a>Publicar seus aplicativos no Azure 

Para publicar Tableau, você precisa publicar um aplicativo no Portal do Azure.

Para:

- Instruções detalhadas das etapas 1 a 8, consulte [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](application-proxy-add-on-premises-application.md). 
- Obter informações sobre como encontrar os valores de Tableau para os campos de Proxy de aplicativo, consulte a documentação de Tableau.  

**Para publicar seu aplicativo**: 


1. Entre no [portal do Azure](https://portal.azure.com) como um administrador de aplicativos. 

2. Selecione **Azure Active Directory > Proxy de Aplicativo**. 

3. Selecione **Adicionar** na parte superior da folha. 

4. Selecione **Aplicativo local**. 

5. Preencha os campos obrigatórios com informações sobre seu novo aplicativo. Use as diretrizes a seguir para as configurações: 

    - **URL interna**: Esse aplicativo deve ter um URL interno que seja o próprio URL do Tableau. Por exemplo, `https://adventure-works.tableau.com`. 

    - **Método de pré-autenticação**: Azure Active Directory (recomendado, mas não obrigatório). 

6. Selecione **Adicionar** na parte superior da folha. Seu aplicativo é adicionado e abre o menu de início rápido. 

7. No menu de início rápido, selecione **Atribuir um usuário para teste** e adicione pelo menos um usuário para o aplicativo. Verifique se essa conta de teste tem acesso ao aplicativo local. 

8. Selecione **Atribuir** para salvar a atribuição do usuário de teste. 

9. (Opcional) Na folha de gerenciamento do aplicativo, selecione **Logon único**. Escolha **Delegação restrita de Kerberos** no menu suspenso e preencha os campos obrigatórios com base na sua configuração do Tableau. Clique em **Salvar**. 

 

## <a name="testing"></a>Testando 

Seu aplicativo agora está pronto para teste. Acesse o URL externo que você usou para publicar o Tableau e faça o login como um usuário atribuído aos dois aplicativos.



## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Proxy de Aplicativo do Azure AD, consulte [Como fornecer acesso remoto seguro a aplicativos locais](application-proxy.md).

