---
title: Acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure Active Directory | Microsoft Docs
description: Saiba mais sobre os conceitos e as etapas necessárias para usar o Azure AD (Azure Active Directory) para autenticar o acesso à API dos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: d80a58f1886ecc1ca2a735881fc5822f2fc0c53b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826097"
---
# <a name="access-the-azure-media-services-api-with-azure-ad-authentication"></a>Acessar a API dos Serviços de Mídia do Azure com a autenticação do Azure AD  

> [!NOTE]
> Não estão sendo adicionados novos recursos ou funcionalidades aos Serviços de Mídia v2. <br/>Confira a versão mais recente, [Serviços de Mídia v3](https://docs.microsoft.com/azure/media-services/latest/). Consulte também [diretrizes de migração da v2 para v3](../latest/migrate-from-v2-to-v3.md)

A API dos Serviços de Mídia do Azure é uma API RESTful. Você pode usá-la para executar operações em recursos de mídia usando uma API REST ou os SDKs de cliente disponíveis. Os Serviços de Mídia do Azure oferecem um SDK de cliente dos Serviços de Mídia para o Microsoft .NET. Para estar autorizado a acessar os recursos e a API dos Serviços de Mídia, primeiro você deve ser autenticado. 

Os Serviços de Mídia dão suporte à [autenticação baseada no Azure AD (Azure Active Directory)](../../active-directory/fundamentals/active-directory-whatis.md). O serviço REST de Mídia do Azure exige que o usuário ou o aplicativo que faz as solicitações da API REST tenha a função **Colaborador** ou **Proprietário** para acessar os recursos. Para obter mais informações, consulte [Introdução ao Controle de Acesso Baseado em Função no portal do Azure](../../role-based-access-control/overview.md).  

Este documento fornece uma visão geral de como acessar a API dos Serviços de Mídia usando as APIs REST ou as APIs do .NET.

> [!NOTE]
> Autorização de controle de acesso foi preterida no dia 1 de junho de 2018.

## <a name="access-control"></a>Controle de acesso

Para que a solicitação REST da Mídia do Azure seja bem-sucedida, o usuário chamador deve ter uma função Colaborador ou Proprietário na conta dos Serviços de Mídia que ele está tentando acessar.  
Somente um usuário com a função Proprietário pode conceder acesso (conta) aos recursos de mídia para novos usuários ou aplicativos. A função Colaborador pode acessar somente o recurso de mídia.
Solicitações não autorizadas falham, com o código de status 401. Se você receber esse código de erro, verifique se o usuário tem a função Colaborador ou Proprietário atribuída à conta dos Serviços de Mídia do usuário. Verifique isso no portal do Azure. Pesquise sua conta de mídia e, em seguida, clique na guia **Controle de acesso**. 

![Guia Controle de acesso](./media/media-services-use-aad-auth-to-access-ams-api/media-services-access-control.png)

## <a name="types-of-authentication"></a>Tipos de autenticação 
 
Ao usar a autenticação do Azure AD com os Serviços de Mídia do Azure, você tem duas opções de autenticação:

- **Autenticação de usuário**. Autentica uma pessoa que está usando o aplicativo para interagir com os recursos dos Serviços de Mídia. O aplicativo interativo deve primeiro solicitar ao usuário as credenciais do usuário. Um exemplo é um aplicativo de console de gerenciamento usado por usuários autorizados para monitorar trabalhos de codificação ou uma transmissão ao vivo. 
- **Autenticação de entidade de serviço**. Autentica um serviço. Os aplicativos que geralmente usam esse método de autenticação são aplicativos que executam serviços daemon, serviços de camada intermediária ou trabalhos agendados. Entre os exemplos estão aplicativos Web, aplicativos de funções, aplicativos lógicos, APIs e microsserviços.

### <a name="user-authentication"></a>Autenticação de usuário 

Os aplicativos que devem usar o método de autenticação de usuário são aplicativos nativos de gerenciamento ou monitoramento: aplicativos móveis, aplicativos do Windows e aplicativos de Console. Esse tipo de solução é útil quando você deseja proporcionar interação humana com o serviço em um dos seguintes cenários:

- Painel de monitoramento dos trabalhos de codificação.
- Painel de monitoramento dos fluxos ao vivo.
- Aplicativo de gerenciamento para usuários móveis ou da área de trabalho administrarem os recursos em uma conta dos Serviços de Mídia.

> [!NOTE]
> Esse método de autenticação não deve ser usado para aplicativos voltados para o consumidor. 

Um aplicativo nativo deve primeiro adquirir um token de acesso do Azure AD e, em seguida, usá-lo ao fazer solicitações HTTP para a API REST dos Serviços de Mídia. Adicione o token de acesso ao cabeçalho da solicitação. 

O seguinte diagrama mostra um fluxo típico de autenticação de aplicativo interativo: 

![Diagrama de aplicativos nativos](./media/media-services-use-aad-auth-to-access-ams-api/media-services-native-aad-app1.png)

No diagrama anterior, os números representam o fluxo das solicitações em ordem cronológica.

> [!NOTE]
> Quando você usa o método de autenticação de usuário, todos os aplicativos compartilham a mesma ID do cliente do aplicativo nativo (padrão) e o mesmo URI de redirecionamento do aplicativo nativo. 

1. Solicite as credenciais a um usuário.
2. Solicite um token de acesso do Azure AD com os seguintes parâmetros:  

   * Ponto de extremidade do locatário do Azure AD.

       As informações do locatário podem ser recuperadas no portal do Azure. Coloque o cursor sobre o nome do usuário conectado no canto superior direito.
   * URI de recurso dos Serviços de Mídia. 

       Esse URI é o mesmo para contas de Serviços de Mídia que estão no mesmo ambiente do Azure (por exemplo, https://rest.media.azure.net).

   * ID do cliente do aplicativo (nativo) dos Serviços de Mídia.
   * URI de redirecionamento do aplicativo (nativo) dos Serviços de Mídia.
   * URI de recurso dos Serviços de Mídia REST.
        
       O URI representa o ponto de extremidade da API REST (por exemplo, https://test03.restv2.westus.media.azure.net/api/).

     Para obter valores para esses parâmetros, consulte [Usar o portal do Azure para acessar as configurações de autenticação do Azure AD](media-services-portal-get-started-with-aad.md) usando a opção de autenticação de usuário.

3. O token de acesso do Azure AD é enviado ao cliente.
4. O cliente envia uma solicitação para a API REST da Mídia do Azure com o token de acesso do Azure AD.
5. O cliente recebe novamente os dados dos Serviços de Mídia.

Para obter informações sobre como usar a autenticação do Azure AD para se comunicar com solicitações REST usando o SDK de cliente do .NET dos Serviços de Mídia, consulte [Usar a autenticação do Azure AD para acessar a API dos Serviços de Mídia com o .NET](media-services-dotnet-get-started-with-aad.md). 

Se você não estiver usando o SDK de cliente do .NET dos Serviços de Mídia, deverá criar manualmente uma solicitação de token de acesso do Azure AD usando os parâmetros descritos na etapa 2. Para obter mais informações, consulte [Como usar a Biblioteca de Autenticação do Azure AD para obter o token do Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

### <a name="service-principal-authentication"></a>Autenticação de entidade de serviço

Os aplicativos que geralmente usam esse método de autenticação são aplicativos que executam serviços de camada intermediária e trabalhos agendados: aplicativos Web, aplicativos de funções, aplicativos lógicos, APIs e microsserviços. Esse método de autenticação também é adequado para aplicativos interativos nos quais você talvez deseje usar uma conta de serviço para gerenciar os recursos.

Quando você usa o método de autenticação de entidade de serviço para criar cenários de consumidor, a autenticação normalmente é manipulada na camada intermediária (por meio de alguma API) e não diretamente em um aplicativo móvel ou da área de trabalho. 

Para usar esse método, crie um aplicativo e uma entidade de serviço do Azure AD em seu próprio locatário. Depois de criar o aplicativo, conceda o acesso da função de Colaborador ou Proprietário ao aplicativo à conta dos Serviços de Mídia. Você pode fazer isso no portal do Azure, usando a CLI do Azure ou com um script do PowerShell. Use também um aplicativo existente do Azure AD. Registre e gerencie o aplicativo e a entidade de serviço do Azure AD [no portal do Azure](media-services-portal-get-started-with-aad.md). Também faça isso usando a [CLI do Azure](media-services-use-aad-auth-to-access-ams-api.md) ou o [PowerShell](media-services-powershell-create-and-configure-aad-app.md). 

![Aplicativos de camada intermediária](./media/media-services-use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

Depois de criar o aplicativo do Azure AD, você obtém valores para as configurações a seguir. Você precisa destes valores para autenticação:

- ID do cliente 
- Segredo do cliente 

Na figura anterior, os números representam o fluxo das solicitações em ordem cronológica:
    
1. Um aplicativo de camada intermediária (API Web ou aplicativo Web) solicita um token de acesso do Azure AD que tem os seguintes parâmetros:  

   * Ponto de extremidade do locatário do Azure AD.

       As informações do locatário podem ser recuperadas no portal do Azure. Coloque o cursor sobre o nome do usuário conectado no canto superior direito.
   * URI de recurso dos Serviços de Mídia. 

       Esse URI é o mesmo para contas de Serviços de Mídia localizadas no mesmo ambiente do Azure (por exemplo, https://rest.media.azure.net).

   * URI de recurso dos Serviços de Mídia REST.

       O URI representa o ponto de extremidade da API REST (por exemplo, https://test03.restv2.westus.media.azure.net/api/).

   * Valores do aplicativo do Azure AD: a ID do cliente e o segredo do cliente.
    
     Para obter valores para esses parâmetros, consulte [Usar o portal do Azure para acessar as configurações de autenticação do Azure AD](media-services-portal-get-started-with-aad.md) usando a opção de autenticação de entidade de serviço.

2. O token de acesso do Azure AD é enviado à camada intermediária.
4. A camada intermediária envia a solicitação à API REST da Mídia do Azure com o token do Azure AD.
5. A camada intermediária obtém novamente os dados dos Serviços de Mídia.

Para obter mais informações sobre como usar a autenticação do Azure AD para se comunicar com solicitações REST usando o SDK de cliente do .NET dos Serviços de Mídia, consulte [Usar a autenticação do Azure AD para acessar a API dos Serviços de Mídia do Azure com o .NET](media-services-dotnet-get-started-with-aad.md). 

Se você não estiver usando o SDK de cliente do .NET dos Serviços de Mídia, deverá criar manualmente uma solicitação de token do Azure AD usando os parâmetros descritos na etapa 1. Para obter mais informações, consulte [Como usar a Biblioteca de Autenticação do Azure AD para obter o token do Azure AD](../../active-directory/develop/active-directory-authentication-libraries.md).

## <a name="troubleshooting"></a>solução de problemas

Exceção: “O servidor remoto retornou um erro: (401) Não autorizado.”

Solução: Para que a solicitação REST dos Serviços de Mídia seja bem-sucedida, o usuário que chama deve ter uma função Colaborador ou Proprietário na conta dos Serviços de Mídia que está tentando acessar. Para obter mais informações, consulte a seção [Controle de acesso](media-services-use-aad-auth-to-access-ams-api.md#access-control).

## <a name="resources"></a>Recursos

Os seguintes artigos são visões gerais dos conceitos de autenticação do Azure AD: 

- [Cenários de autenticação abordados pelo Azure AD](../../active-directory/develop/authentication-scenarios.md)
- [Adicionar, atualizar ou remover um aplicativo no Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)
- [Configurar e gerenciar o Controle de Acesso Baseado em Função usando o PowerShell](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>Próximas etapas

* Use o portal do Azure para [Acessar a autenticação do Azure AD para consumir a API dos Serviços de Mídia do Azure](media-services-portal-get-started-with-aad.md).
* Use a autenticação do Azure AD para [acessar a API dos Serviços de Mídia do Azure com o .NET](media-services-dotnet-get-started-with-aad.md).

