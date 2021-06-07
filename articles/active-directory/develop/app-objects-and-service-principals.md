---
title: Aplicativos & entidades de serviço no Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre a relação entre objetos de aplicativo e de entidade de serviço no Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/15/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: 358e066631304e727d18d092bd4b9a5b2a0bb89a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199604"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Objetos de entidade de serviço e aplicativo no Azure Active Directory

Este artigo descreve o registro do aplicativo, os objetos de aplicativo e as entidades de serviço no Azure Active Directory: o que eles são, como eles são usados e como eles estão relacionados entre si. Um cenário de exemplo de multilocatário também é apresentado para ilustrar a relação entre o objeto de aplicativo de um aplicativo e os objetos de entidade de serviço correspondentes.

## <a name="application-registration"></a>Registro de aplicativo
Para delegar funções de gerenciamento de acesso e identidade para o Azure AD, um aplicativo deve ser registrado com um [locatário](developer-glossary.md#tenant)do Azure AD. Ao registrar seu aplicativo com o Azure AD, você está criando uma configuração de identidade para seu aplicativo que permite que ele se integre ao Azure AD. Ao registrar um aplicativo no [portal do Azure][AZURE-Portal], você escolhe se ele é um único locatário (acessível somente em seu locatário) ou multilocatário (acessível em outros locatários) e pode, opcionalmente, definir um URI de redirecionamento (para o qual o token de acesso é enviado).

Para obter instruções detalhadas sobre como registrar um aplicativo, consulte o guia de [início rápido de registro do aplicativo](quickstart-register-app.md).

Quando você concluir o registro do aplicativo, terá uma instância globalmente exclusiva do aplicativo (o [objeto Application](#application-object)) que reside em seu diretório ou locatário inicial.  Você também tem uma ID globalmente exclusiva para seu aplicativo (a ID do aplicativo ou do cliente).  No portal, você pode adicionar segredos ou certificados e escopos para fazer seu aplicativo funcionar, personalizar a identidade visual do aplicativo na caixa de diálogo de entrada e muito mais.

Se você registrar um aplicativo no portal, um objeto de aplicativo, bem como um objeto de entidade de serviço, será criado automaticamente em seu locatário inicial.  Se você registrar/criar um aplicativo usando as APIs de Microsoft Graph, a criação do objeto de entidade de serviço será uma etapa separada.

## <a name="application-object"></a>Objeto de aplicativo
Um aplicativo do Azure AD é definido por seu único objeto de aplicativo, que reside no locatário do Azure AD em que o aplicativo foi registrado (conhecido como o locatário "inicial" do aplicativo).  Um objeto de aplicativo é usado como um modelo ou plano gráfico para criar um ou mais objetos de entidade de serviço.  Uma entidade de serviço é criada em todos os locatários em que o aplicativo é usado. Semelhante a uma classe na programação orientada a objeto, o objeto Application tem algumas propriedades estáticas que são aplicadas a todas as entidades de serviço criadas (ou instâncias de aplicativo).

O objeto Application descreve três aspectos de um aplicativo: como o serviço pode emitir tokens para acessar o aplicativo, recursos que o aplicativo pode precisar acessar e as ações que o aplicativo pode executar.

A folha **registros de aplicativo** na [portal do Azure][AZURE-Portal] é usada para listar e gerenciar os objetos de aplicativo em seu locatário inicial.

![Folha Registros de aplicativo](./media/app-objects-and-service-principals/app-registrations-blade.png)

A [entidade de aplicativo][MS-Graph-App-Entity] Microsoft Graph define o esquema para as propriedades de um objeto de aplicativo.

## <a name="service-principal-object"></a>Objeto de entidade de serviço
Para acessar os recursos que são protegidos por um locatário do Azure AD, a entidade que requer acesso deve ser representada por uma entidade de segurança. Esse requisito é verdadeiro para usuários (entidade de usuário) e aplicativos (entidade de serviço). A entidade de segurança define a política de acesso e as permissões para o usuário/aplicativo no locatário do Azure AD. Isso habilita recursos principais como a autenticação do usuário/aplicativo durante a entrada, bem como a autorização durante o acesso aos recursos.

Uma entidade de serviço é a representação local, ou instância do aplicativo, de um objeto de aplicativo global em um único locatário ou diretório. Uma entidade de serviço é uma instância concreta criada a partir do objeto de aplicativo e herda determinadas propriedades desse objeto de aplicativo. Uma entidade de serviço é criada em cada locatário em que o aplicativo é usado e faz referência ao objeto de aplicativo exclusivo globalmente.  O objeto de entidade de serviço define o que o aplicativo pode realmente fazer no locatário específico, quem pode acessar o aplicativo e quais recursos o aplicativo pode acessar.

Quando um aplicativo recebe permissão para acessar os recursos em um locatário (após o registro ou o [consentimento](developer-glossary.md#consent)), um objeto de entidade de serviço é criado. Você também pode criar objetos de entidade de serviço em um locatário usando [Azure PowerShell](howto-authenticate-service-principal-powershell.md), [CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli), [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?tabs=http), [portal do Azure][AZURE-Portal]e outras ferramentas. Ao usar o portal, uma entidade de serviço é criada automaticamente quando você registra um aplicativo.

A folha **aplicativos empresariais** no portal é usada para listar e gerenciar as entidades de serviço em um locatário. Você pode ver as permissões da entidade de serviço, as permissões consentidas pelo usuário, quais usuários fizeram esse consentimento, informações de entrada e muito mais.

![Folha aplicativos empresariais](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

A [entidade Microsoft Graph servicePrincipalName][MS-Graph-Sp-Entity] define o esquema para as propriedades de um objeto de entidade de serviço.

## <a name="relationship-between-application-objects-and-service-principals"></a>Relação entre objetos de aplicativo e entidades de serviço

O objeto Application é a representação *global* do seu aplicativo para uso em todos os locatários, e a entidade de serviço é a representação *local* para uso em um locatário específico.

O objeto de aplicativo serve como o modelo do qual as propriedades comuns e padrão são *derivadas* para uso na criação de objetos de entidade de serviço correspondentes. Portanto, um objeto de aplicativo tem uma relação de um para um com o aplicativo de software, e uma relação de um para muitos com seus objetos de entidade de serviço correspondentes.

Uma entidade de serviço deve ser criada em cada locatário no qual o aplicativo é usado, permitindo o estabelecimento de uma identidade para entrada e/ou acesso aos recursos que estão sendo protegidos pelo locatário. Um aplicativo de locatário único tem apenas uma entidade de serviço (em seu locatário inicial), criado e com consentimento para uso durante o registro do aplicativo. Um aplicativo multilocatário também tem uma entidade de serviço criada em cada locatário em que um usuário desse locatário consentiu em seu uso.

### <a name="consequences-of-modifying-and-deleting-applications"></a>Consequências de modificar e excluir aplicativos
As alterações feitas no objeto de aplicativo também são refletidas em seu objeto de entidade de serviço somente no locatário inicial do aplicativo (o locatário onde ele foi registrado). Isso significa que a exclusão de um objeto de aplicativo também excluirá seu objeto de entidade de serviço de locatário inicial.  No entanto, a restauração desse objeto de aplicativo não restaurará sua entidade de serviço correspondente. Para aplicativos multilocatário, as alterações no objeto do aplicativo não serão refletidas em objetos de entidade de serviço dos locatários de qualquer consumidor até que o acesso seja removido por meio do [Painel de Acesso do Aplicativo](https://myapps.microsoft.com) e concedido novamente.

## <a name="example"></a>Exemplo

O diagrama a seguir ilustra o relacionamento entre o objeto de aplicativo de um aplicativo e os objetos de entidade de serviço correspondentes, no contexto de um aplicativo multilocatário de exemplo chamado **aplicativo de RH**. Há três locatários do Azure AD nesse exemplo de cenário:

- **Adatum** -o locatário usado pela empresa que desenvolveu o **aplicativo de RH**
- **Contoso** -o locatário usado pela organização Contoso, que é um consumidor do aplicativo de **RH**
- **Fabrikam**: o locatário usado pela organização Fabrikam, que também consome o **aplicativo de HR**

![Relação entre objeto de aplicativo e objeto de entidade de serviço](./media/app-objects-and-service-principals/application-objects-relationship.svg)

Nesse cenário de exemplo:

| Etapa | Descrição |
|------|-------------|
| 1    | É o processo de criação do aplicativo e dos objetos de entidade de serviço no locatário inicial do aplicativo. |
| 2    | Quando os administradores da Contoso e da Fabrikam concluem o consentimento, um objeto de entidade de serviço é criado no locatário do Azure AD da empresa e recebe as permissões concedidas pelo administrador. Observe também que o aplicativo de RH pode ser configurado/projetado para permitir o consentimento pelos usuários para uso individual. |
| 3    | Os locatários do consumidor do aplicativo de RH (Contoso e Fabrikam) tem seu próprio objeto de entidade de serviço. Cada um deles representa o uso de uma instância do aplicativo em runtime, controlado pelas permissões concedidas pelo respectivo administrador. |

## <a name="next-steps"></a>Próximas etapas

- Você pode usar o [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) para consultar os objetos de aplicativo e entidade de serviço.
- Você pode acessar o objeto de aplicativo de um aplicativo usando a API Microsoft Graph, o editor de manifesto [do aplicativo portal do Azure][AZURE-Portal] ou os [cmdlets do PowerShell do Azure ad](/powershell/azure/), conforme representado por sua [entidade de aplicativo][MS-Graph-App-Entity]OData.
- Você pode acessar o objeto de entidade de serviço de um aplicativo por meio da API do Microsoft Graph ou dos [cmdlets do PowerShell do Azure ad](/powershell/azure/), conforme representado por sua [entidade de UserEntity][MS-Graph-Sp-Entity]do OData.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
