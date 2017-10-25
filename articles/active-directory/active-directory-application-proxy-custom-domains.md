---
title: "Domínios Personalizados no Proxy de Aplicativo do Azure AD | Microsoft Docs"
description: "Gerencie domínios personalizados no Proxy de Aplicativo do Azure AD para que a URL do aplicativo seja a mesma, independentemente de onde os usuários a acessam."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 2fe9f895-f641-4362-8b27-7a5d08f8600f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 1dde300780c8d1f7ea9eee4c92de06bcf70a1f12
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="working-with-custom-domains-in-azure-ad-application-proxy"></a>Trabalhando com domínios personalizados no Proxy de Aplicativo do AD do Azure

Ao publicar um aplicativo por meio do Proxy de Aplicativo do Azure Active Directory, você cria uma URL externa para seus usuários acessarem quando estiverem trabalhando remotamente. Essa URL obtém o domínio padrão *seulocatário.msappproxy.net*. Por exemplo, se você tiver publicado um aplicativo chamado Despesas e seu locatário chamar-se Contoso, a URL externa será https://despesas-contoso.msappproxy.net. Se quiser usar seu próprio nome de domínio, configure um domínio personalizado para o seu aplicativo. 

É recomendável que você configure domínios personalizados para seus aplicativos sempre que possível. Alguns dos benefícios dos domínios personalizados incluem:

- Os usuários podem acessar o aplicativo com a mesma URL se estiverem trabalhando dentro ou fora da sua rede.
- Se todos os seus aplicativos têm as mesmas URLs internas e externas, os links em um aplicativo que apontam para outro continuam a funcionar até mesmo fora da rede corporativa. 
- Você controla sua identidade visual e cria as URLs que quiser. 


## <a name="configure-a-custom-domain"></a>Configurar um domínio personalizado

### <a name="prerequisites"></a>Pré-requisitos

Antes de configurar um domínio personalizado, verifique se você tem os seguintes requisitos preparados: 
- Um [domínio verificado adicionado ao Azure Active Directory](active-directory-domains-add-azure-portal.md).
- Um certificado personalizado para o domínio, no formato de um arquivo PFX. 
- Um aplicativo local [publicado por meio do Proxy de Aplicativo](application-proxy-publish-azure-portal.md).

### <a name="configure-your-custom-domain"></a>Configurar seu domínio personalizado

Quando você tiver esses três requisitos prontos, siga estas etapas para configurar o domínio personalizado:

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Navegue até **Azure Active Directory** > **Aplicativos empresariais** > **Todos os aplicativos** e escolha o aplicativo que deseja gerenciar.
3. Selecione **Proxy de Aplicativo**. 
4. No campo URL Externa, use a lista suspensa para selecionar seu domínio personalizado. Se você não vir o seu domínio na lista, ele ainda não foi verificado. 
5. Selecione **Salvar**
5. O campo **Certificado**, que estava desabilitado, fica habilitado. Selecione esse campo. 

   ![Clique para carregar um certificado](./media/active-directory-application-proxy-custom-domains/certificate.png)

   Se você já tiver carregado um certificado para este domínio, o campo de certificado exibirá as informações do certificado. 

6. Carregue o certificado PFX e digite a senha do certificado. 
7. Selecione **Salvar** para salvar as alterações. 
8. Adicione um [registro DNS](../dns/dns-operations-recordsets-portal.md) que redirecione a nova URL externa para o domínio msappproxy.net. 

>[!TIP] 
>Você só precisa carregar um certificado por domínio personalizado. Assim que carregar um certificado, você poderá escolher o domínio personalizado ao publicar um novo aplicativo e não precisará fazer configurações adicionais, exceto o registro DNS. 

## <a name="manage-certificates"></a>Gerenciar certificados

### <a name="certificate-format"></a>Formato do certificado
Não há nenhuma restrição em relação aos métodos de assinatura do certificado. A Criptografia de Curva Elíptica (ECC), o Nome Alternativo da Entidade (SAN) e outros tipos de certificado são compatíveis. 

Você pode usar um certificado curinga, desde que o curinga corresponda à URL externa desejada. 

Você também pode usar certificados autoassinados. Se você estiver usando uma autoridade de certificação privada, o CDP (ponto de distribuição do ponto de revogação de certificado) para o certificado deverá ser público.

### <a name="changing-the-domain"></a>Alterando o domínio
Todos os domínios verificados aparecem na lista suspensa de URL Externa do seu aplicativo. Para alterar o domínio, apenas atualize esse campo para o aplicativo. Se o domínio desejado não estiver na lista, [adicione-o como um domínio verificado](active-directory-domains-add-azure-portal.md). Se você selecionar um domínio que não tenha um certificado associado, siga as etapas 5 a 7 para adicionar o certificado. Em seguida, certifique-se de atualizar o registro DNS para redirecionar da nova URL externa. 

### <a name="certificate-management"></a>Gerenciamento de certificados
Você pode usar o mesmo certificado para vários aplicativos, a menos que os aplicativos compartilhem um host externo. 

Você recebe um aviso quando um certificado expira, informando para que você carregue outro certificado por meio do portal. Se o certificado for revogado, os usuários poderão ver um aviso de segurança ao acessarem o aplicativo. Nós não realizamos verificações de revogação de certificados.  Para atualizar o certificado de um determinado aplicativo, navegue até o aplicativo e siga as etapas 5 a 7 para configurar domínios personalizados em aplicativos publicados a fim de carregar um novo certificado. Se o certificado antigo não estiver sendo usado por outros aplicativos, ele será excluído automaticamente. 

Atualmente, todo o gerenciamento de certificados é feito por meio de páginas de aplicativos individuais, portanto você precisa gerenciar os certificados no contexto dos aplicativos relevantes. 

## <a name="next-steps"></a>Próximas etapas
* [Habilite o logon único](active-directory-application-proxy-sso-using-kcd.md) aos seus aplicativos publicados com a autenticação do Azure AD.
* [Habilite o acesso condicional](active-directory-application-proxy-conditional-access.md) aos seus aplicativos publicados.
* [Adicionar seu nome de domínio personalizado ao Azure AD](active-directory-domains-add-azure-portal.md)


