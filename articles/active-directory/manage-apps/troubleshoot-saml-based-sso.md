---
title: Solucionar problemas de logon único baseado em SAML no Azure Active Directory
description: Solucionar problemas com um aplicativo do Azure AD configurado para logon único baseado em SAML.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: b169616042892c379196dd1d38c2343704aa1030
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257519"
---
# <a name="troubleshoot-saml-based-single-sign-on-in-azure-active-directory"></a>Solucionar problemas de logon único baseado em SAML no Azure Active Directory
Se você encontrar um problema ao configurar um aplicativo. Verifique se seguiu todas as etapas no tutorial para o aplicativo. Nas configurações de aplicativo há uma documentação embutida sobre como configurar o aplicativo. Além disso, é possível acessar a [Lista de tutoriais sobre como integrar aplicativos SaaS com o Active Directory do Azure](../saas-apps/tutorial-list.md) para uma orientação passo a passo detalhada.

## <a name="cant-add-another-instance-of-the-application"></a>Não é possível adicionar outra instância do aplicativo
Para adicionar uma segunda instância de um aplicativo você precisará:
-   Configurar um identificador exclusivo para a segunda instância. Não será possível configurar o mesmo identificador usado para a primeira instância.
-   Configurar um certificado diferente daquele usado para a primeira instância.

Se o aplicativo não oferecer suporte a qualquer uma das opções acima. Então, não será possível configurar uma segunda instância.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Não é possível adicionar o Identificador ou a URL de resposta
Se não conseguir configurar o Identificador ou a URL de Resposta, confirme se os valores do Identificador e da URL de Resposta correspondem aos padrões pré-configurados para o aplicativo.

Para saber os padrões pré-configurado para o aplicativo:
1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.** Vá para a etapa 7. Se você já estiver na folha de configuração do aplicativo no Azure AD.
2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.
3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.
5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.
   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**
6. Selecione o aplicativo para o qual você deseja configurar o logon único.
7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.
8. Selecione **Logon com base em SAML** da lista suspensa **Modo**.
9. Vá para a caixa de texto **Identificador** ou **URL de Resposta** na **Seção Domínio e URLs.**
10. Há três maneiras de conhecer os padrões com suporte para o aplicativo:
    * Na caixa de texto, são visíveis os padrões com suporte, como um espaço reservado *Exemplo:* <https://contoso.com>.
    * Se não houver suporte para o padrão, você verá um ponto de exclamação vermelho quando tentar inserir o valor na caixa de texto. Se você passar o mouse sobre o ponto de exclamação vermelho, verá os padrões com suporte.
    * No tutorial para o aplicativo, também é possível obter informações sobre os padrões com suporte. Na seção **Configura o logon único do Azure AD**. Vá para a etapa de valores configurados na seção **Domínio e URLs**.

Se os valores não coincidirem com os padrões pré-configurados no Azure AD. Você pode:
-   Trabalhar com o fornecedor do aplicativo para obter valores que correspondam ao padrão pré-configurado no Azure AD
-   Ou, contatar a equipe do Azure Active Directory em <aadapprequest@microsoft.com> ou deixar um comentário no tutorial para solicitar a atualização dos padrões com suporte para o aplicativo

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Onde é possível configurar o formato EntityID (Identificador de Usuário)
Não será possível selecionar o formato EntityID (Identificador de Usuário) que o Azure AD envia para o aplicativo na resposta após a autenticação do usuário.

O Azure AD seleciona o formato para o atributo NameID (Identificador de Usuário) com base no valor selecionado ou no formato solicitado pelo aplicativo no AuthRequest do SAML. Para obter mais informações, visite o artigo [Single Sign-On protocolo SAML](../develop/single-sign-on-saml-protocol.md#authnrequest) na seção NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Não é possível encontrar os metadados do Azure AD para concluir a configuração com o aplicativo
Para baixar o certificado ou metadados do aplicativo Azure Active Directory, siga estas etapas:
1. Abra o [**portal do Azure**](https://portal.azure.com/) e entre como um **administrador global** ou **coadministrador.**
2. Abra a **Extensão do Azure Active Directory** clicando em **Todos os serviços** na parte superior do menu de navegação esquerdo principal.
3. Digite **“Azure Active Directory**” na caixa de pesquisa do filtro e selecione o item **Azure Active Directory**.
4. clique em **Aplicativos Empresariais** no menu de navegação esquerdo do Azure Active Directory.
5. clique em **Todos os Aplicativos** para exibir uma lista com todos os seus aplicativos.
   * Se não vir o aplicativo desejado, use o controle **Filtro** na parte superior da **Lista com Todos os Aplicativos** e defina a opção **Mostrar** como **Todos os Aplicativos.**
6. Selecione o aplicativo para o qual você precisa configurar o logon único.
7. Após o carregamento do aplicativo, clique em **Logon único** no menu de navegação esquerdo do aplicativo.
8. Vá para a seção **Certificado de Autenticação SAML** e, em seguida, clique no valor da coluna **Download**. Dependendo do aplicativo que exigir a configuração de logon único, você verá a opção para baixar o Certificado ou o XML de Metadados.

O Azure AD não fornece uma URL para obter os metadados. Os metadados apenas podem ser recuperados como um arquivo XML.

## <a name="customize-saml-claims-sent-to-an-application"></a>Personalizar as declarações SAML enviadas para um aplicativo
Para saber como personalizar as declarações de atributo SAML enviadas para seu aplicativo, consulte [Mapeamento de declarações no Azure Active Directory](../develop/active-directory-claims-mapping.md) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas
* [Série de Início Rápido no Gerenciamento de Aplicativos](view-applications-portal.md)