---
title: 'Início Rápido: Registrar e expor uma API Web | Azure'
titleSuffix: Microsoft identity platform
description: Neste início rápido, registre uma API Web com a plataforma de identidade da Microsoft e configure seus escopos, expondo-os aos clientes para acesso baseado em permissões aos recursos da API.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 09/03/2020
ms.author: marsma
ms.custom: aaddev, contperf-fy21q1
ms.reviewer: aragra, lenalepa, sureshja
ms.openlocfilehash: 820bc7dfe9123db495c151cd5cd0ea5ae337619f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100103967"
---
# <a name="quickstart-configure-an-application-to-expose-a-web-api"></a>Início Rápido: Configurar um aplicativo para expor uma API Web

Neste início rápido, você registra uma API Web com a plataforma de identidade da Microsoft e a expõe para aplicativos cliente adicionando um escopo de exemplo. Ao registrar sua API Web e expô-la por meio de escopos, você pode fornecer acesso baseado em permissões a seus recursos para usuários autorizados e aplicativos cliente que acessam sua API.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura ativa – [criar uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Conclusão do [Início Rápido: Configurar um locatário](quickstart-create-new-tenant.md)

## <a name="register-the-web-api"></a>Registrar a API Web

Para fornecer acesso com escopo aos recursos em sua API Web, primeiro registre a API com a plataforma de identidade da Microsoft.

1. Execute as etapas na seção **Registrar um aplicativo** do [Início Rápido: Registre um aplicativo na plataforma de identidade da Microsoft](quickstart-register-app.md).
1. Ignore as seções **Adicionar um URI de redirecionamento** e **Definir as configurações de plataforma**. Você não precisa configurar um URI de redirecionamento para uma API Web, pois nenhum usuário está conectado interativamente.
1. Ignore a seção **Adicionar credenciais** por enquanto. Somente se sua API acessar uma API downstream ela precisará das próprias credenciais, um cenário não abordado neste artigo.

Com sua API Web registrada, você está pronto para adicionar os escopos que o código da API pode usar para fornecer permissão granular aos consumidores de sua API.

## <a name="add-a-scope"></a>Adicionar um escopo

O código em um aplicativo cliente solicita permissão para executar operações definidas por sua API Web passando um token de acesso junto com suas solicitações para o recurso protegido (a API Web). A API Web executará a operação solicitada somente se o token de acesso recebido contiver os escopos necessários para a operação.

Primeiro, siga estas etapas para criar um escopo de exemplo chamado `Employees.Read.All`:

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Se você tem acesso a vários locatários, use o filtro **Diretório + assinatura** :::image type="icon" source="./media/quickstart-configure-app-expose-web-apis/portal-01-directory-subscription-filter.png" border="false"::: no menu superior para selecionar o locatário que contém o registro do aplicativo cliente.
1. Selecione **Azure Active Directory** > **Registros de aplicativo** e o registro do aplicativo da API.
1. Selecione **Expor uma API** > **Adicionar um escopo**.

    :::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-02-expose-api.png" alt-text="Um registro de aplicativo expõe um painel de API no portal do Azure":::

1. Você será solicitado a definir um **URI da ID de aplicativo** se ainda não tiver configurado um.

   O URI da ID do aplicativo atua como o prefixo dos escopos que você fará referência no código da API e deve ser globalmente exclusivo. Você pode usar o valor padrão fornecido, que está no formato `api://<application-client-id>`, ou especificar um URI mais legível, como `https://contoso.com/api`.

1. Em seguida, especifique os atributos do escopo no painel **Adicionar um escopo**. Para este passo a passo, você pode usar os valores de exemplo ou especificar o seu próprio valor.

    | Campo | Descrição | Exemplo |
    |-------|-------------|---------|
    | **Nome do Escopo** | O nome do seu escopo. Uma convenção de nomenclatura de escopo comum é `resource.operation.constraint`. | `Employees.Read.All` |
    | **Quem pode consentir** | Se esse escopo pode ser consentido por usuários ou se é necessário ter o consentimento do administrador. Selecione **Somente administradores** para permissões com privilégios mais elevados. | **Administradores e usuários** |
    | **Nome de exibição de consentimento do administrador** | Uma breve descrição da finalidade do escopo que somente os administradores verão. | `Read-only access to Employee records` |
    | **Descrição do consentimento do administrador** | Uma descrição mais detalhada da permissão concedida pelo escopo que somente os administradores verão. | `Allow the application to have read-only access to all Employee data.` |
    | **Nome para exibição do consentimento do usuário** | Uma breve descrição da finalidade do escopo. Mostrado para os usuários somente se você definir **Quem pode consentir** como **Administradores e usuários**. | `Read-only access to your Employee records` |
    | **Descrição de consentimento do usuário** | Uma descrição mais detalhada da permissão concedida pelo escopo. Mostrado para os usuários somente se você definir **Quem pode consentir** como **Administradores e usuários**. | `Allow the application to have read-only access to your Employee data.` |

1. Defina o **Estado** como **Habilitado** e selecione **Adicionar escopo**.

1. (Opcional) Para suprimir a solicitação de consentimento dos usuários do seu aplicativo para os escopos que você definiu, você pode *pré-autorizar* o aplicativo cliente a acessar sua API Web. Pré-autorize *apenas* aplicativos cliente confiáveis, pois os usuários não terão a oportunidade de recusar o consentimento.
    1. Em **Aplicativos cliente autorizados**, selecione **Adicionar um aplicativo cliente**
    1. Insira a **ID do Aplicativo (cliente)** do aplicativo cliente que deseja pré-autorizar. Por exemplo, aquela de um aplicativo Web que você já registrou.
    1. Em **Escopos autorizados**, selecione os escopos para os quais deseja suprimir a solicitação de consentimento e escolha **Adicionar aplicativo**.

    Se você seguiu essa etapa opcional, o aplicativo cliente agora é um PCA (aplicativo cliente pré-autorizado), e os usuários não serão solicitados a dar consentimento ao entrar nele.

## <a name="add-a-scope-requiring-admin-consent"></a>Adicionar um escopo que requer o consentimento do administrador

Em seguida, adicione outro escopo de exemplo chamado `Employees.Write.All` que apenas os administradores podem consentir. Os escopos que exigem consentimento do administrador normalmente são usados para da acesso a operações com maior privilégio e, muitas vezes, por aplicativos cliente executados como serviços de back-end ou daemons que não conectam um usuário interativamente.

Para adicionar o exemplo de escopo `Employees.Write.All`, siga as etapas na seção [Adicionar um escopo](#add-a-scope) e especifique esses valores no painel **Adicionar um escopo**:

| Campo                          | Valor de exemplo                                                      |
|--------------------------------|--------------------------------------------------------------------|
| **Nome do Escopo**                 | `Employees.Write.All`                                              |
| **Quem pode consentir**            | **Somente administradores**                                                    |
| **Nome de exibição de consentimento do administrador** | `Write access to Employee records`                                 |
| **Descrição do consentimento do administrador**  | `Allow the application to have write access to all Employee data.` |
| **Nome para exibição do consentimento do usuário**  | *Nenhum (deixe em branco)*                                               |
| **Descrição de consentimento do usuário**   | *Nenhum (deixe em branco)*                                               |

## <a name="verify-the-exposed-scopes"></a>Verificar os escopos expostos

Se você tiver adicionado com êxito os dois escopos de exemplo descritos nas seções anteriores, eles aparecerão no painel **Expor uma API** do registro de aplicativo da API Web, semelhante a esta imagem:

:::image type="content" source="media/quickstart-configure-app-expose-web-apis/portal-03-scopes-list.png" alt-text="Captura de tela do painel Expor uma API mostrando dois escopos expostos.":::

Conforme mostrado na imagem, a cadeia de caracteres completa de um escopo é a concatenação do **URI da ID do Aplicativo** da sua API Web e o **Nome do escopo**.

Por exemplo, se o URI da ID do aplicativo da sua API Web for `https://contoso.com/api` e o nome do escopo for `Employees.Read.All`, o escopo completo será:

`https://contoso.com/api/Employees.Read.All`

## <a name="using-the-exposed-scopes"></a>Como usar os escopos expostos

No próximo artigo da série, você configura o registro de um aplicativo cliente com acesso à API Web e os escopos que você definiu seguindo as etapas deste artigo.

Depois que um registro de aplicativo cliente recebe permissão para acessar sua API Web, o cliente pode receber um token de acesso OAuth 2.0 pela plataforma de identidade da Microsoft. Quando o cliente chama a API Web, ele apresenta um token de acesso cuja declaração de escopo (`scp`) é definida como as permissões que você especificou no registro de aplicativo do cliente.

É possível expor escopos adicionais posteriormente conforme a necessidade. Considere que sua API Web pode expor vários escopos associados a diversas operações. O recurso pode controlar o acesso à API Web em runtime avaliando declarações de escopo (`scp`) no token de acesso OAuth 2.0 recebido.

## <a name="next-steps"></a>Próximas etapas

Agora que você expôs sua API Web configurando seus escopos, configure o registro do aplicativo cliente com permissão para acessar esses escopos.

> [!div class="nextstepaction"]
> [Configurar um registro de aplicativo para acesso à API Web](quickstart-configure-app-access-web-apis.md)

<!-- REF LINKS -->
[ms-graph-application]: /graph/api/resources/application
