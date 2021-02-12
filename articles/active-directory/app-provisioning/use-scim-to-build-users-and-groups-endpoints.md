---
title: Criar um ponto de extremidade SCIM para provisionamento de usuário para aplicativos de Azure Active Directory
description: O sistema para SCIM (Sistema de Gerenciamento de Usuários entre Domínios) padroniza o provisionamento automático de usuários. Saiba como desenvolver um ponto de extremidade do SCIM, integrar sua API do SCIM com Azure Active Directory e começar a automatizar o provisionamento de usuários e grupos em seus aplicativos de nuvem com Azure Active Directory.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 6b7451b0d664995a6b647f7926d856b0db6090d8
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99256095"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Tutorial: desenvolver um ponto de extremidade SCIM de exemplo

Ninguém quer criar um novo ponto de extremidade a partir do zero, portanto, criamos um [código de referência](https://aka.ms/scimreferencecode) para você começar a usar o [scim](https://aka.ms/scimoverview). Este tutorial descreve como implantar o código de referência do SCIM no Azure e testá-lo usando o postmaster ou integrando-se com o cliente SCIM do Azure AD. Você pode colocar seu ponto de extremidade do SCIM em funcionamento sem código em apenas 5 minutos. Este tutorial destina-se a desenvolvedores que estão procurando começar a usar o SCIM ou outros interessados em testar um ponto de extremidade SICM. 

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Implantar seu ponto de extremidade do SCIM no Azure
> * Testar seu ponto de extremidade do SCIM

## <a name="deploy-your-scim-endpoint-in-azure"></a>Implantar seu ponto de extremidade do SCIM no Azure

As etapas fornecidas aqui implantam o ponto de extremidade SCIM em um serviço usando o [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) e os [serviços Azure apps](https://docs.microsoft.com/azure/app-service/). O código de referência do SCIM também pode ser executado localmente, hospedado por um servidor local ou implantado em outro serviço externo. 

### <a name="open-solution-and-deploy-to-azure-app-service"></a>Abrir solução e implantar no serviço Azure App

1. Vá para o [código de referência](https://github.com/AzureAD/SCIMReferenceCode) do GitHub e selecione **clonar ou baixar**.

1. Escolha **abrir na área de trabalho** ou, em seguida, copie o link, abra o **Visual Studio** e selecione **clonar ou confira o código** para inserir o link copiado e fazer uma cópia local.

1. No **Visual Studio**, não se esqueça de entrar na conta que tem acesso aos seus recursos de hospedagem.

1. No **Gerenciador de soluções**, abra *Microsoft. scim. sln* e clique com o botão direito do mouse no arquivo *Microsoft. scim. WebHostSample* . Selecione **Publicar**.

    ![publicação na nuvem](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Para executar essa solução localmente, clique duas vezes no projeto e selecione **IIS Express** para iniciar o projeto como uma página da Web com uma URL de host local.

1. Selecione **Criar perfil** e certifique-se de que o **serviço de aplicativo** e **criar novo** estão selecionados.

    ![publicação na nuvem 2](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Percorra as opções da caixa de diálogo e renomeie o aplicativo para um nome de sua escolha. Esse nome é usado tanto no aplicativo quanto na URL do ponto de extremidade SCIM.

    ![publicação na nuvem 3](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Selecione o grupo de recursos a ser usado e escolha **publicar**.

1. Navegue até o aplicativo na configuração de **serviços Azure app**  >   e selecione **nova configuração de aplicativo** para adicionar a configuração de *Token__TokenIssuer* com o valor `https://sts.windows.net/<tenant_id>/` . Substitua `<tenant_id>` por seu Tenant_id do Azure AD e, se você estiver procurando testar o ponto de extremidade scim usando o [postmaster](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint), adicione também uma configuração de *ASPNETCORE_ENVIRONMENT* com o valor `Development` . 

   ![configurações de appservice](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Ao testar seu ponto de extremidade com um aplicativo empresarial na portal do Azure, opte por manter o ambiente como `Development` e forneça o token gerado do `/scim/token` ponto de extremidade para testar ou alterar o ambiente para `Production` e deixar o campo de token vazio no aplicativo empresarial na [portal do Azure](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client). 

É isso! Seu ponto de extremidade SCIM agora está publicado e permite que você use a URL do serviço Azure App para testar o ponto de extremidade SCIM.

## <a name="test-your-scim-endpoint"></a>Testar seu ponto de extremidade do SCIM

As solicitações para um ponto de extremidade SCIM exigem autorização e o padrão SCIM deixa várias opções de autenticação e autorização, como cookies, autenticação básica, autenticação de cliente TLS ou qualquer um dos métodos listados no [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Não se esqueça de evitar métodos inseguros, como nome de usuário/senha, em favor de um método mais seguro, como o OAuth. O Azure AD dá suporte a tokens de portador de vida longa (para aplicativos da galeria e não da Galeria) e à concessão de autorização OAuth (para aplicativos publicados na galeria do aplicativo).

> [!NOTE]
> Os métodos de autorização fornecidos no repositório são apenas para teste. Ao integrar com o Azure AD, você pode revisar as diretrizes de autorização, consulte [provisionamento de plano para um ponto de extremidade scim](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#authorization-for-provisioning-connectors-in-the-application-gallery). 

O ambiente de desenvolvimento permite que recursos não seguros para produção, como o código de referência, controlem o comportamento da validação do token de segurança. O código de validação do token é configurado para usar um token de segurança autoassinado e a chave de assinatura é armazenada no arquivo de configuração, consulte o parâmetro **token: IssuerSigningKey** no *appsettings.Development.jsno* arquivo.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Ao enviar uma solicitação **Get** para o `/scim/token` ponto de extremidade, um token é emitido usando a chave configurada e pode ser usado como um token de portador para a autorização subsequente.

O código de validação de token padrão é configurado para usar um token emitido por Azure Active Directory e requer que o locatário emissor seja configurado usando o parâmetro **token: TokenIssuer** no *appsettings.jsno* arquivo.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Usar o postmaster para testar pontos de extremidade

Depois que o ponto de extremidade SCIM for implantado, você poderá testar para garantir que ele seja compatível com RFC SCIM. Este exemplo fornece um conjunto de testes no **postmaster** para validar as operações CRUD em usuários e grupos, filtrar, atualizar a associação de grupo e desabilitar os usuários.

Os pontos de extremidade estão localizados no `{host}/scim/` diretório e podem ser interagindo com o uso de solicitações HTTP padrão. Para modificar a `/scim/` rota, consulte *ControllerConstant.cs* em **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**  >  **Controllers**.

> [!NOTE]
> Você só pode usar pontos de extremidade HTTP para testes locais, uma vez que o serviço de provisionamento do Azure AD requer o suporte a HTTPS no ponto de fim.

#### <a name="open-postman-and-run-tests"></a>Abrir o postmaster e executar testes

1. Baixar o [postmaster](https://www.getpostman.com/downloads/) e iniciar o aplicativo.
1. Copie o link [https://aka.ms/ProvisioningPostman](https://aka.ms/ProvisioningPostman) e cole-o no postmaster para importar a coleção de testes.

    ![coleção do postmaster](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Crie um ambiente de teste com as variáveis abaixo:

   |Ambiente|Variável|Valor|
   |-|-|-|
   |Executar o projeto localmente usando IIS Express|||
   ||**Servidor**|`localhost`|
   ||**Porta**|`:44359`*(não se esqueça de **:**)*|
   ||**API**|`scim`|
   |Executar o projeto localmente usando Kestrel|||
   ||**Servidor**|`localhost`|
   ||**Porta**|`:5001`*(não se esqueça de **:**)*|
   ||**API**|`scim`|
   |Hospedando o ponto de extremidade no Azure|||
   ||**Servidor**|*(Insira a URL do SCIM)*|
   ||**Porta**|*(deixe em branco)*|
   ||**API**|`scim`|

1. Use **Get Key** da coleção de postmaster para enviar uma solicitação **Get** para o ponto de extremidade do token e recuperar um token de segurança a ser armazenado na variável de **token** para solicitações subsequentes. 

   ![obter chave do postmaster](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Para tornar um ponto de extremidade SCIM seguro, você precisa de um token de segurança antes de se conectar e o tutorial usa o `{host}/scim/token` ponto de extremidade para gerar um token autoassinado.

É isso! Agora você pode executar a coleção de **postmaster** para testar a funcionalidade de ponto de extremidade SCIM.

## <a name="next-steps"></a>Próximas etapas

Para desenvolver um usuário compatível com SCIM e um ponto de extremidade de grupo com interoperabilidade para um cliente, consulte a [implementação do cliente scim](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Tutorial: desenvolver e planejar o provisionamento para um ponto de extremidade scim](use-scim-to-provision-users-and-groups.md) 
>  [Tutorial: configurar o provisionamento de um aplicativo da Galeria](configure-automatic-user-provisioning-portal.md)