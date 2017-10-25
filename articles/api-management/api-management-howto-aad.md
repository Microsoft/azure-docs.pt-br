---
title: Autorizar contas de desenvolvedor usando o Azure Active Directory - Gerenciamento de API do Azure | Microsoft Docs
description: "Como autorizar usuários usando o Active Directory do Azure no Gerenciamento de API."
services: api-management
documentationcenter: API Management
author: vladvino
manager: erikre
editor: 
ms.assetid: 33a69a83-94f2-4e4e-9cef-f2a5af3c9732
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: 2fa5c021d9f493004a5beab02529c70b600b9a0d
ms.contentlocale: pt-br
ms.lasthandoff: 09/02/2017

---

> [!WARNING]
> A integração do Azure Active Directory está disponível somente nas camadas [Desenvolvedor e Premium](https://azure.microsoft.com/en-us/pricing/details/api-management/).

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Como autorizar contas de desenvolvedor usando o Active Directory do Azure no Gerenciamento de API do Azure
## <a name="overview"></a>Visão geral
Este guia mostra como habilitar o acesso ao portal do desenvolvedor para os usuários do Azure Active Directory. Este guia também mostra como gerenciar grupos de usuários do Active Directory do Azure, adicionando grupos externos que contêm os usuários de um Active Directory do Azure.

> Para completar as etapas deste guia, você deve primeiramente ter um Active Directory do Azure no qual criar um aplicativo.
> 

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Como autorizar contas de desenvolvedor usando o Active Directory do Azure
Para começar, clique em **Portal do Editor** no Portal do Azure para acessar o serviço de Gerenciamento de API. Isso levará você ao portal do editor de Gerenciamento de API.

![Portal do editor][api-management-management-console]

> Se ainda não criou uma instância de serviço de Gerenciamento de API, confira [Criar uma instância de serviço de Gerenciamento de API][Create an API Management service instance] no tutorial [Introdução ao Gerenciamento de API do Azure][Get started with Azure API Management].
> 
> 

Clique em **Segurança** no menu **Gerenciamento de API** à esquerda e clique em **Identidades externas**.

![Identidades externas][api-management-security-external-identities]

Clique em **Active Directory do Azure**. Anote a **URL de redirecionamento** e alterne para seu Active Directory do Azure no Portal Clássico do Azure.

![Identidades externas][api-management-security-aad-new]

Clique no botão **Adicionar** para criar um novo aplicativo do Azure Active Directory e escolha **Adicionar um aplicativo que minha organização está desenvolvendo**.

![Adicionar novo aplicativo do Active Directory do Azure][api-management-new-aad-application-menu]

Insira um nome para o aplicativo, selecione **Aplicativo Web e/ou API Web**, e clique no botão Avançar.

![Novo aplicativo do Active Directory do Azure][api-management-new-aad-application-1]

Para **URL de Logon**, insira a URL de logon do portal do desenvolvedor. Neste exemplo, a **URL de logon** é `https://aad03.portal.current.int-azure-api.net/signin`. 

Para a **URL de ID do aplicativo**, digite o domínio padrão ou um domínio personalizado para o Active Directory do Azure e acrescente uma cadeia de caracteres exclusiva a ele. Neste exemplo, o domínio padrão de **https://contoso5api.onmicrosoft.com** é usado com o sufixo de **/api** especificado.

![Propriedades do novo aplicativo do Active Directory do Azure][api-management-new-aad-application-2]

Clique no botão de seleção para salvar e criar o aplicativo, então alterne para a guia **Configurar** para configurar o novo aplicativo.

![Novo aplicativo do Active Directory do Azure criado][api-management-new-aad-app-created]

Se vários Active Directories do Azure serão usados para esse aplicativo, clique em **Sim** para **Aplicativo é multilocatário**. O padrão é **Não**.

![Aplicativo é multilocatário][api-management-aad-app-multi-tenant]

Copie a **URL de redirecionamento** da seção **Azure Active Directory** da guia **Identidades Externas** no portal do editor e cole-a na caixa de texto **URL de resposta**. 

![URL de resposta][api-management-aad-reply-url]

Role até a parte inferior da guia Configuração, selecione a lista suspensa **Permissões de aplicativo** e verifique **Ler dados do diretório**.

![Permissões de aplicativo][api-management-aad-app-permissions]

Selecione a lista suspensa **Delegar permissões** e marque **Habilitar logon e leitura dos perfis dos usuários**.

![Permissões delegadas][api-management-aad-delegated-permissions]

> Para obter mais informações sobre o aplicativo e permissões delegadas, consulte [Acessando a API do Graph][Accessing the Graph API].
> 
> 

Copie a **ID do cliente** para a área de transferência.

![ID do cliente][api-management-aad-app-client-id]

Volte ao portal do editor e cole a **ID do cliente** copiada da configuração de aplicativo do Active Directory do Azure.

![Id do Cliente][api-management-client-id]

Alterne de volta à configuração do Azure Active Directory e clique no item **Selecionar duração** na seção de lista suspensa **Chaves** e especifique um intervalo. Neste exemplo, **1 ano** é usado.

![Chave][api-management-aad-key-before-save]

Clique em **Salvar** para salvar a configuração e exibir a chave. Copie a chave para a área de transferência.

> Anote esta chave. Depois que você fechar a janela de configuração do Active Directory do Azure, a chave não pode ser exibida novamente.
> 
> 

![Chave][api-management-aad-key-after-save]

Voltar ao portal do editor e cole a chave na caixa de texto **Segredo do Cliente** .

![Segredo do Cliente][api-management-client-secret]

**Locatários permitidos** especifica quais diretórios têm acesso às APIs da instância do serviço de Gerenciamento de API. Especifique os domínios das instâncias do Active Directory do Azure para os quais você deseja conceder acesso. Você pode separar múltiplos domínios com novas linhas, espaços ou vírgulas.

![Locatários permitidos][api-management-client-allowed-tenants]


Depois que a configuração desejada for especificada, clique em **Salvar**.

![Salvar][api-management-client-allowed-tenants-save]

Depois que as alterações são salvas, os usuários no Azure Active Directory especificado podem entrar no portal do desenvolvedor, seguindo as etapas em [Efetuar logon no portal do desenvolvedor usando uma conta do Azure Active Directory][Log in to the Developer portal using an Azure Active Directory account].

Vários domínios podem ser especificados na seção **Locatários permitidos** . Antes que qualquer usuário possa fazer logon por meio de um domínio diferente do domínio original em que o aplicativo foi registrado, um administrador global do domínio diferente deve conceder permissão para o aplicativo para acessar dados de diretório. Para conceder permissão, o administrador global deve acessar `https://<URL of your developer portal>/aadadminconsent` (por exemplo, https://contoso.portal.azure-api.net/aadadminconsent), digitar o nome de domínio doe locatário do Active Directory ao qual ele deseja conceder acesso e clicar em Enviar. No exemplo a seguir, um administrador global de `miaoaad.onmicrosoft.com` está tentando dar permissão para este portal do desenvolvedor específico. 

![Permissões][api-management-aad-consent]

Na próxima tela, o administrador global receberá uma solicitação para confirmar a concessão de permissão. 

![Permissões][api-management-permissions-form]

> Se um administrador não global tenta efetuar logon antes de permissões serem concedidas por um administrador global, a tentativa de logon falha e uma tela de erro é exibida.
> 
> 

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Como adicionar um grupo externo do Active Directory do Azure
Depois de habilitar o acesso de usuários em um Active Directory do Azure, você pode adicionar grupos do Active Directory do Azure a Gerenciamento de API para gerenciar com mais facilidade a associação dos desenvolvedores no grupo com os produtos desejados.

> Para configurar um grupo externo do Azure Active Directory, o Azure Active Directory deve ser primeiro configurado na guia Identidades, seguindo o procedimento na seção anterior. 
> 
> 

Grupos externos do Active Directory do Azure são adicionados por meio da guia **Visibilidade** do produto para o qual você deseja conceder acesso ao grupo. Clique em **Produtos**e, em seguida, clique no nome do produto desejado.

![Configurar produto][api-management-configure-product]

Alterne para a guia **Visibilidade** e clique em **Adicionar grupos do Azure Active Directory**.

![Adicionar grupos][api-management-add-groups]

Selecione o **Locatário do Azure Active Directory** da lista suspensa e digite o nome do grupo desejado na caixa de texto dos **Grupos** a serem adicionados.

![Selecionar grupo][api-management-select-group]

Este nome de grupo pode ser encontrado na lista **Grupos** para seu Active Directory do Azure, conforme mostrado no exemplo a seguir.

![Lista de grupos do Active Directory do Azure][api-management-aad-groups-list]

Clique em **Adicionar** para validar o nome do grupo e adicionar o grupo. Neste exemplo, o grupo externo **Desenvolvedores Contoso 5** é adicionado. 

![Grupo adicionado][api-management-aad-group-added]

Clique em **Salvar** para salvar a nova seleção de grupo.

Quando um grupo do Azure Active Directory tiver sido configurado por meio de um produto, ele estará disponível para ser verificado na guia **Visibilidade** para os outros produtos da instância de serviço Gerenciamento de API.

Para examinar e configurar as propriedades de grupos externos quando eles foram adicionados, clique no nome do grupo na guia **Grupos**.

![Gerenciar grupos][api-management-groups]

Aqui, você pode editar o **Nome** e a **Descrição** do grupo.

![Editar grupo][api-management-edit-group]

Os usuários do Azure Active Directory configurado podem entrar no portal do Desenvolvedor, além de ver e assinar todos os grupos para os quais têm visibilidade, seguindo as instruções na seção a seguir.

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>Como fazer logon portal do Desenvolvedor usando uma conta do Active Directory do Azure
Para fazer logon no portal do Desenvolvedor usando uma conta do Azure Active Directory configurada nas seções anteriores, abra uma nova janela de navegador usando a **URL de logon** da configuração de aplicativo do Active Directory e clique em **Azure Active Directory**.

![Portal do desenvolvedor][api-management-dev-portal-signin]

Insira as credenciais de um dos usuários no seu Active Directory do Azure e clique em **Entrar**.

![Entrar][api-management-aad-signin]

Pode ser solicitado que você preencha um formulário de registro se qualquer informação adicional for necessária. Preencha o formulário de registro e clique em **Inscrever-se**.

![Registro][api-management-complete-registration]

Agora, o usuário está conectado no portal do desenvolvedor para a instância do serviço de Gerenciamento de API.

![Registro completo][api-management-registration-complete]

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-aad-consent]: ./media/api-management-howto-aad/api-management-aad-consent.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: api-management-get-started.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account


