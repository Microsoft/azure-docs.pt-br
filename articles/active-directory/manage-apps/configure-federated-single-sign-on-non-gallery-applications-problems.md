---
title: Problema ao configurar o logon único federado para um aplicativo inexistente na galeria| Microsoft Docs
description: Aborda alguns dos problemas comuns que você pode encontrar ao configurar o logon único federado para seu aplicativo SAML personalizado que não está listado na Galeria do Aplicativo Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 99c5e4d99f45e2a642a46f7dc070fb7512ff4d73
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68422544"
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problema ao configurar o logon único federado para um aplicativo inexistente na galeria

Se você encontrar um problema ao configurar um aplicativo. Verifique se seguiu todas as etapas do artigo [Configurando logon único para aplicativos que não estão na galeria de aplicativo do Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)

## <a name="cant-add-another-instance-of-the-application"></a>Não é possível adicionar outra instância do aplicativo

Para adicionar uma segunda instância de um aplicativo você precisará:

-   Configurar um identificador exclusivo para a segunda instância. Não é possível configurar o mesmo identificador usado para a primeira instância.

-   Configurar um certificado diferente daquele usado para a primeira instância.

Se o aplicativo não tiver suporte para nenhum dos anteriores, não será possível configurar uma segunda instância.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Onde é possível configurar o formato EntityID (Identificador de Usuário)

Não é possível selecionar o formato EntityID (Identificador de Usuário) que o Azure Active Directory envia para o aplicativo na resposta, após a autenticação do usuário.

O Azure Active Directory seleciona o formato para o atributo NameID (Identificador de Usuário) com base no valor selecionado ou no formato solicitado pelo aplicativo no AuthRequest do SAML. Para obter mais informações, consulte o artigo [Protocolo SAML de Logon Único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) na seção NameIDPolicy,

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>Onde obter os metadados do aplicativo ou o certificado do Azure AD

Para baixar o certificado ou metadados do aplicativo Azure Active Directory, siga estas etapas:

1. Abra o [**Portal do Azure**](https://portal.azure.com/) e entre como um **Administrador Global** ou **Coadministrador.**

2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.

3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.

4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.

5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.

   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**

6. Selecione o aplicativo para o qual você precisa configurar o logon único.

7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.

8. Vá para a seção **Certificado de Autenticação SAML** e, em seguida, clique no valor da coluna **Download**. Dependendo do aplicativo que exigir a configuração de logon único, você verá a opção para baixar o Certificado ou o XML de Metadados.

O Azure AD não fornece uma URL para obter os metadados. Os metadados apenas podem ser recuperados como um arquivo XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Não sei como personalizar declarações SAML enviadas para um aplicativo

Para saber como personalizar as declarações de atributo SAML enviadas para seu aplicativo, consulte [Mapeamento de declarações no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
[Gerenciando aplicativos com o Azure Active Directory](what-is-application-management.md)
