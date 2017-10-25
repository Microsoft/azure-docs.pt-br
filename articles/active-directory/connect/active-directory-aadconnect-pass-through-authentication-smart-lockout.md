---
title: "Azure AD Connect: autenticação de passagem – bloqueio inteligente | Microsoft Docs"
description: "Este artigo descreve como a autenticação de passagem do Azure AD (Azure Active Directory) protege suas contas locais contra ataques de senha de força bruta na nuvem."
services: active-directory
keywords: "Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários para o Azure AD, SSO, Logon único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: billmath
ms.openlocfilehash: 7e05c469260a445578c80cdf77fab2d5ffb48022
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Autenticação de passagem do Azure Active Directory: bloqueio inteligente

## <a name="overview"></a>Visão geral

O Azure AD protege contra ataques de senha de força bruta e impede que usuários reais sejam impedidos de acessar seus aplicativos de SaaS e do Office 365. Essa capacidade, chamada **Bloqueio Inteligente**, tem suporte quando você usa a Autenticação de Passagem como seu método de entrada. O Bloqueio Inteligente fica habilitado por padrão para todos os locatários e protege suas contas de usuário o tempo todo; não é necessário ativá-lo.

O Bloqueio Inteligente funciona controlando tentativas de logon com falha e, depois de um determinado **Limite de bloqueio**, iniciando uma **Duração de bloqueio**. Qualquer tentativa de logon do invasor durante a Duração de bloqueio é rejeitada. Se o ataque persistir, tentativas posteriores de logon com falha, após o final da Duração de bloqueio, levarão a Durações de bloqueio mais longas.

>[!NOTE]
>O Limite de bloqueio padrão é de 10 tentativas com falha e a Duração de bloqueio padrão é de 60 segundos.

O Bloqueio Inteligente também faz a distinção entre entradas de usuários reais e de invasores e bloqueia somente os invasores na maioria dos casos. Essa funcionalidade impede que invasores bloqueiem usuários reais de forma mal-intencionada. Usamos o comportamento de entrada anterior, os dispositivos e navegadores dos usuários, entre outros sinais, para distinguir entre invasores e usuários originais. Estamos aperfeiçoando constantemente nossos algoritmos.

Como a Autenticação de Passagem encaminha solicitações de validação de senha para o AD (Active Directory) local, você precisa impedir que invasores bloqueiem contas de usuários do AD. Como tem suas próprias políticas de Bloqueio de conta do AD (especificamente, as políticas de [**Limite de bloqueio de conta**](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) e [**Redefinir contador de bloqueios de conta após**](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx)), você precisa configurar os valores de Limite de bloqueio e de Duração de bloqueio do Azure AD adequadamente para filtrar os ataques na nuvem antes que eles alcancem seu AD local.

>[!NOTE]
>O recurso de Bloqueio inteligente é gratuito e está _ativado_ por padrão para todos os clientes. Entretanto, modificar os valores de Limite de Bloqueio e de Duração de Bloqueio do Azure AD usando a API do Graph precisa que seu locatário tenha pelo menos uma licença Azure AD Premium P2. Você não precisa de uma licença do Azure AD Premium P2 _por usuário_ para obter o recurso de bloqueio inteligente com Autenticação de Passagem.

Para garantir que as contas do AD locais dos seus usuários também sejam protegidas, você precisa garantir que:

1.  O Limite de bloqueio do Azure AD seja _menor_ que o Limite de bloqueio da Conta do AD. É recomendável que você defina os valores de forma que o Limite de bloqueio da Conta do AD seja pelo menos duas ou três vezes o Limite de bloqueio do Azure AD.
2.  A Duração de bloqueio do Azure AD (representada em segundos) seja _maior_ que o valor de Redefinir contador de bloqueios de conta após do AD (representado em minutos).

## <a name="verify-your-ad-account-lockout-policies"></a>Verifique as políticas de bloqueio de conta do AD

Use as instruções a seguir para verificar suas políticas de bloqueio de conta do AD:

1.  Abra a ferramenta de Gerenciamento de Política de Grupo.
2.  Edite a política de grupo aplicada a todos os usuários, por exemplo, a Política de Domínio Padrão.
3.  Navegue até Configuração do Computador\Políticas\Configurações do Windows\Configurações de Conta\Políticas de Conta\Política de Bloqueio da Conta.
4.  Verifique os valores de Limite de bloqueio de conta e Redefinir contador de bloqueios de conta após.

![Políticas de bloqueio de conta do AD](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-needs-premium-license"></a>Utilize a API do Graph para gerenciar os valores de Bloqueio Inteligente do locatário (é necessário licença Premium)

>[!IMPORTANT]
>Modificar os valores de Limite de bloqueio e de Duração de bloqueio do Azure AD usando a API do Graph é um recurso do Azure AD Premium P2. Também é necessário que você seja um Administrador Global em seu locatário.

Você pode usar o [Explorador do Graph](https://developer.microsoft.com/graph/graph-explorer) para ler, definir e atualizar os valores do Bloqueio Inteligente do Azure AD. Mas você também pode fazer essas operações de forma programática.

### <a name="read-smart-lockout-values"></a>Ler valores de Bloqueio Inteligente

Siga estas etapas para ler os valores do Bloqueio Inteligente de seu locatário:

1. Entre no Explorador do Graph como um Administrador Global de seu locatário. Se solicitado, conceda acesso para as permissões solicitadas.
2. Clique em "Modificar permissões" e selecione a permissão "Directory.ReadWrite.All".
3. Configure a solicitação da API do Graph da seguinte maneira: defina a versão como "BETA", o tipo de solicitação como "GET" e a URL como `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Clique em "Executar Consulta" para ver os valores do Bloqueio Inteligente de seu locatário. Se não tiver configurado os valores de seu locatário antes, você verá um conjunto vazio.

### <a name="set-smart-lockout-values"></a>Definir valores de Bloqueio Inteligente

Siga estas etapas para definir os valores de do Bloqueio Inteligente de seu locatário (somente pela primeira vez):

1. Entre no Explorador do Graph como um Administrador Global de seu locatário. Se solicitado, conceda acesso para as permissões solicitadas.
2. Clique em "Modificar permissões" e selecione a permissão "Directory.ReadWrite.All".
3. Configure a solicitação da API do Graph da seguinte maneira: defina a versão como "BETA", o tipo de solicitação como "POST" e a URL como `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Copie e cole a seguinte solicitação JSON no campo "Corpo da Solicitação".
5. Clique em "Executar Consulta" para ver os valores do Bloqueio Inteligente de seu locatário.

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>Se não estiver usando os valores de **BannedPasswordList** e **EnableBannedPasswordCheck**, você poderá deixá-los como vazio ("") e "false", respectivamente.

Verifique se você definiu os valores do Bloqueio Inteligente de seu locatário corretamente usando [estas etapas](#read-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Atualizar valores de Bloqueio Inteligente

Siga estas etapas para atualizar os valores do Bloqueio Inteligente de seu locatário (se já tiver configurado esses valores antes):

1. Entre no Explorador do Graph como um Administrador Global de seu locatário. Se solicitado, conceda acesso para as permissões solicitadas.
2. Clique em "Modificar permissões" e selecione a permissão "Directory.ReadWrite.All".
3. [Siga estas etapas para ler os valores do Bloqueio Inteligente de seu locatário](#read-smart-lockout-values). Copie o valor de `id` (um GUID) do item com "displayName" como "PasswordRuleSettings".
4. Configure a solicitação da API do Graph da seguinte maneira: defina a versão como "BETA", o tipo de solicitação como "PATCH" e a URL como `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>` – use o GUID da Etapa 3 para `<id>`.
5. Copie e cole a seguinte solicitação JSON no campo "Corpo da Solicitação". Altere os valores do Bloqueio Inteligente conforme apropriado.
6. Clique em "Executar Consulta" para atualizar os valores do Bloqueio Inteligente de seu locatário.

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

Verifique se você atualizou os valores do Bloqueio Inteligente de seu locatário corretamente usando [estas etapas](#read-smart-lockout-values).

## <a name="next-steps"></a>Próximas etapas
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.
