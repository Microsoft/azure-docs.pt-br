---
title: Proxy de Aplicativo Azure AD e Qlik Sense | Microsoft Docs
description: Ative o Proxy de Aplicativo no Portal do Azure e instale os Conectores para o proxy reverso.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/06/2018
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a03f3c57d3fbc7efe20055343eab3db33aa8194
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259077"
---
# <a name="application-proxy-and-qlik-sense"></a>Proxy de Aplicativo e Qlik Sense 
O Proxy de Aplicativo do Azure Active Directory e a Qlik Sense fizeram uma parceria para garantir que você possa facilmente usar o Proxy de Aplicativo para fornecer acesso remoto para sua implantação do Qlik Sense.  

## <a name="prerequisites"></a>Pré-requisitos 
O restante deste cenário pressupõe que você tenha feito o seguinte:
 
- Configurado o [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- [Instalado um conector de Proxy de Aplicativo](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publicar seus aplicativos no Azure 
Para publicar o QlikSense, você precisará publicar dois aplicativos no Azure.  

### <a name="application-1"></a>Aplicativo nº 1: 
Siga estas etapas para publicar seu aplicativo. Para um passo a passo mais detalhado das etapas 1 a 8, veja [Publicar aplicativos usando o Proxy de Aplicativo do Azure AD](application-proxy-add-on-premises-application.md). 


1. Entre no Portal do Azure como um administrador global. 
2. Selecione **Azure Active Directory**  >  **aplicativos empresariais**. 
3. Selecione **Adicionar** na parte superior da folha. 
4. Selecione **Aplicativo local**. 
5. Preencha os campos obrigatórios com informações sobre seu novo aplicativo. Use as diretrizes a seguir para as configurações: 
   - **URL interna**: este aplicativo deve ter uma URL interna que é a própria URL QlikSense. Por exemplo, **https&#58;//demo.qlikemm.com:4244** 
   - **Método de Pré-autenticação**: Azure Active Directory (recomendado, mas não obrigatório) 
1. Selecione **Adicionar** na parte inferior da folha. Seu aplicativo é adicionado e abre o menu de início rápido. 
2. No menu de início rápido, selecione **Atribuir um usuário para teste** e adicione pelo menos um usuário para o aplicativo. Verifique se essa conta de teste tem acesso ao aplicativo local. 
3. Selecione **Atribuir** para salvar a atribuição do usuário de teste. 
4. (Opcional) Na folha de gerenciamento do aplicativo, selecione Logon único. Escolha **Delegação restrita de Kerberos** no menu suspenso e preencha os campos obrigatórios, com base na sua configuração do Qlik. Clique em **Salvar**. 

### <a name="application-2"></a>Aplicativo nº 2: 
Siga as mesmas etapas do aplicativo nº 1, com as seguintes exceções: 

**Etapa nº 5**: a URL interna agora deve ser a URL do QlikSense com a porta de autenticação usada pelo aplicativo. O padrão é **4244** para HTTPS e **4248** para versões de http para QlikSense anteriores a 2018 de abril. O padrão para versões QlikSense depois de abril de 2018 é **443** para HTTPS e **80** para http.  Exemplo: **https&#58;//demo.qlik.com:4244**</br></br>
**Etapa #10:** Não configurar o SSO e deixar o **logon único desabilitado**
 
 
## <a name="testing"></a>Testando 
Seu aplicativo agora está pronto para teste. Acesse a URL externa usada para publicar o QlikSense no aplicativo nº 1 e faça logon como um usuário atribuído aos dois aplicativos.  

## <a name="additional-references"></a>Referências adicionais
Para obter mais informações sobre como publicar o Qlik Sense Sense com o proxy de aplicativo, consulte os artigos da Comunidade Qlik Sense a seguir: 
- [Azure AD com autenticação integrada do Windows usando uma delegação restrita de Kerberos com sensor Qlik Sense](https://community.qlik.com/docs/DOC-20183)
- [Integração do Qlik Sense Sense com o Azure Proxy de Aplicativo do AD](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Próximas etapas

- [Publique aplicativos com proxy de aplicativo](application-proxy-add-on-premises-application.md)
- [Trabalhando com conectores de Proxy de aplicativo](application-proxy-connector-groups.md)

