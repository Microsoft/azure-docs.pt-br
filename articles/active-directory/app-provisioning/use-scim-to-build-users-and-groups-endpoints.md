---
title: Criar um ponto de extremidade SCIM para provisionamento de usuário para aplicativos de Azure Active Directory
description: Saiba como desenvolver um ponto de extremidade do SCIM, integrar sua API do SCIM com o Azure AD e provisionar automaticamente usuários e grupos em seus aplicativos de nuvem com Azure Active Directory.
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
ms.openlocfilehash: 4e933000c8e700d8bfd193b542e3855b2fca26f8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101689327"
---
# <a name="tutorial-develop-a-sample-scim-endpoint"></a>Tutorial: desenvolver um ponto de extremidade SCIM de exemplo

Ninguém quer criar um novo ponto de extremidade a partir do zero, portanto, criamos um [código de referência](https://aka.ms/scimreferencecode) para você começar a usar o [sistema para o scim (gerenciamento de identidades entre domínios)](https://aka.ms/scimoverview). Você pode colocar seu ponto de extremidade do SCIM em funcionamento sem código em apenas cinco minutos.

Este tutorial descreve como implantar o código de referência do SCIM no Azure e testá-lo usando o postmaster ou integrando com o cliente do SCIM do Azure Active Directory (AD do Azure). Este tutorial destina-se a desenvolvedores que desejam começar a usar o SCIM ou qualquer pessoa interessada em testar um ponto de extremidade SCIM.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Implante seu ponto de extremidade do SCIM no Azure.
> * Teste seu ponto de extremidade SCIM.

## <a name="deploy-your-scim-endpoint-in-azure"></a>Implantar seu ponto de extremidade do SCIM no Azure

As etapas aqui implantam o ponto de extremidade SCIM em um serviço usando o [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) e o [serviço Azure app](../../app-service/index.yml). O código de referência do SCIM também pode ser executado localmente, hospedado por um servidor local ou implantado em outro serviço externo.

1. Vá para o [código de referência](https://github.com/AzureAD/SCIMReferenceCode) do GitHub e selecione **clonar ou baixar**.

1. Selecione **abrir no desktop** ou copie o link, abra o Visual Studio e selecione **clonar ou fazer check-out do código** para inserir o link copiado e fazer uma cópia local.

1. No Visual Studio, certifique-se de entrar na conta que tem acesso aos recursos de hospedagem.

1. No Gerenciador de Soluções, abra *Microsoft. scim. sln* e clique com o botão direito do mouse no arquivo *Microsoft. scim. WebHostSample* . Selecione **Publicar**.

    ![Captura de tela que mostra o arquivo de exemplo.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish.png)

    > [!NOTE]
    > Para executar essa solução localmente, clique duas vezes no projeto e selecione **IIS Express** para iniciar o projeto como uma página da Web com uma URL de host local.

1. Selecione **Criar perfil** e verifique se o **serviço de aplicativo** e **criar novo** estão selecionados.

    ![Captura de tela que mostra a janela de publicação.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-2.png)

1. Percorra as opções da caixa de diálogo e renomeie o aplicativo para um nome de sua escolha. Esse nome é usado tanto no aplicativo quanto na URL do ponto de extremidade SCIM.

    ![Captura de tela que mostra a criação de um novo serviço de aplicativo.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-3.png)

1. Selecione o grupo de recursos a ser usado e selecione **publicar**.

    ![Captura de tela que mostra a publicação de um novo serviço de aplicativo.](media/use-scim-to-build-users-and-groups-endpoints/cloud-publish-4.png)

1. Vá para o aplicativo em **Azure app**  >  **configuração** de serviço e selecione **nova configuração de aplicativo** para adicionar a configuração de *Token__TokenIssuer* com o valor `https://sts.windows.net/<tenant_id>/` . Substitua `<tenant_id>` pela sua ID de locatário do Azure AD. Se você quiser testar o ponto de extremidade SCIM usando o [postmaster](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint), adicione uma configuração de *ASPNETCORE_ENVIRONMENT* com o valor `Development` .

   ![Captura de tela que mostra a janela Configurações do aplicativo.](media/use-scim-to-build-users-and-groups-endpoints/app-service-settings.png)

   Ao testar seu ponto de extremidade com um aplicativo empresarial no [portal do Azure](use-scim-to-provision-users-and-groups.md#integrate-your-scim-endpoint-with-the-aad-scim-client), você tem duas opções. Você pode manter o ambiente no `Development` e fornecer o token de teste do `/scim/token` ponto de extremidade, ou pode alterar o ambiente para `Production` e deixar o campo de token vazio.

É isso! Seu ponto de extremidade SCIM agora está publicado e você pode usar a URL do serviço Azure App para testar o ponto de extremidade SCIM.

## <a name="test-your-scim-endpoint"></a>Testar seu ponto de extremidade do SCIM

Solicitações para um ponto de extremidade SCIM exigem autorização. O padrão SCIM tem várias opções de autenticação e autorização, incluindo cookies, autenticação básica, autenticação de cliente TLS ou qualquer um dos métodos listados na [RFC 7644](https://tools.ietf.org/html/rfc7644#section-2).

Não se esqueça de evitar métodos que não são seguros, como nome de usuário e senha, em favor de um método mais seguro, como o OAuth. O Azure AD dá suporte a tokens de portador de vida longa (para aplicativos de galeria e não Galeria) e à concessão de autorização OAuth (para aplicativos de galeria).

> [!NOTE]
> Os métodos de autorização fornecidos no repositório são apenas para teste. Ao integrar com o Azure AD, você pode revisar as diretrizes de autorização. Consulte [provisionamento de plano para um ponto de extremidade scim](use-scim-to-provision-users-and-groups.md).

O ambiente de desenvolvimento habilita recursos que não são seguros para produção, como o código de referência para controlar o comportamento da validação do token de segurança. O código de validação do token usa um token de segurança autoassinado e a chave de assinatura é armazenada no arquivo de configuração. Consulte o parâmetro **token: IssuerSigningKey** no *appsettings.Development.jsno* arquivo.

```json
"Token": {
    "TokenAudience": "Microsoft.Security.Bearer",
    "TokenIssuer": "Microsoft.Security.Bearer",
    "IssuerSigningKey": "A1B2C3D4E5F6A1B2C3D4E5F6",
    "TokenLifetimeInMins": "120"
}
```

> [!NOTE]
> Quando você envia uma solicitação **Get** para o `/scim/token` ponto de extremidade, um token é emitido usando a chave configurada. Esse token pode ser usado como um token de portador para a autorização subsequente.

O código de validação de token padrão é configurado para usar um token do Azure AD e requer que o locatário emissor seja configurado usando o parâmetro **token: TokenIssuer** no *appsettings.jsno* arquivo.

``` json
"Token": {
    "TokenAudience": "8adf8e6e-67b2-4cf2-a259-e3dc5476c621",
    "TokenIssuer": "https://sts.windows.net/<tenant_id>/"
}
```

### <a name="use-postman-to-test-endpoints"></a>Usar o postmaster para testar pontos de extremidade

Depois de implantar o ponto de extremidade SCIM, você pode testar para garantir que ele esteja em conformidade com o SCIM RFC. Este exemplo fornece um conjunto de testes no postmaster que validam operações CRUD (criar, ler, atualizar e excluir) em usuários e grupos, filtragem, atualizações de associação de grupo e desabilitação de usuários.

Os pontos de extremidade estão no `{host}/scim/` diretório, e você pode usar solicitações HTTP padrão para interagir com eles. Para modificar a `/scim/` rota, consulte *ControllerConstant.cs* em **AzureADProvisioningSCIMreference**  >  **ScimReferenceApi**  >  **Controllers**.

> [!NOTE]
> Você só pode usar pontos de extremidade HTTP para testes locais. O serviço de provisionamento do Azure AD requer que seu ponto de extremidade dê suporte a HTTPS.

1. Baixar o [postmaster](https://www.getpostman.com/downloads/) e iniciar o aplicativo.
1. Copie e cole este link no postmaster para importar a coleção de testes: `https://aka.ms/ProvisioningPostman` .

    ![Captura de tela que mostra a importação da coleção de testes no postmaster.](media/use-scim-to-build-users-and-groups-endpoints/postman-collection.png)

1. Crie um ambiente de teste que tenha estas variáveis:

   |Ambiente|Variável|Valor|
   |-|-|-|
   |Executar o projeto localmente usando IIS Express|||
   ||**Servidor**|`localhost`|
   ||**Porta**|`:44359`*(não se esqueça do **`:`** )*|
   ||**API**|`scim`|
   |Executar o projeto localmente usando Kestrel|||
   ||**Servidor**|`localhost`|
   ||**Porta**|`:5001`*(não se esqueça do **`:`** )*|
   ||**API**|`scim`|
   |Hospedar o ponto de extremidade no Azure|||
   ||**Servidor**|*(Insira a URL do SCIM)*|
   ||**Porta**|*(deixe em branco)*|
   ||**API**|`scim`|

1. Use **Get Key** da coleção de postmaster para enviar uma solicitação **Get** para o ponto de extremidade do token e recuperar um token de segurança a ser armazenado na variável de **token** para solicitações subsequentes.

   ![Captura de tela que mostra a pasta Get Key do postmaster.](media/use-scim-to-build-users-and-groups-endpoints/postman-get-key.png)

   > [!NOTE]
   > Para tornar um ponto de extremidade SCIM seguro, você precisa de um token de segurança antes de se conectar. O tutorial usa o `{host}/scim/token` ponto de extremidade para gerar um token autoassinado.

É isso! Agora você pode executar a coleção de **postmaster** para testar a funcionalidade de ponto de extremidade SCIM.

## <a name="next-steps"></a>Próximas etapas

Para desenvolver um usuário compatível com SCIM e um ponto de extremidade de grupo com interoperabilidade para um cliente, consulte [implementação de cliente scim](http://www.simplecloud.info/#Implementations2).

> [!div class="nextstepaction"]
> [Tutorial: desenvolver e planejar o provisionamento para um ponto de extremidade scim](use-scim-to-provision-users-and-groups.md) 
>  [Tutorial: configurar o provisionamento de um aplicativo da Galeria](configure-automatic-user-provisioning-portal.md)