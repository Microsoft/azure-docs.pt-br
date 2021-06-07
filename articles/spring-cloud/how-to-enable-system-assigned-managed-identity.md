---
title: Habilitar identidade gerenciada atribuída pelo sistema para o aplicativo Azure Spring Cloud
description: Como habilitar a identidade gerenciada atribuída pelo sistema para o aplicativo.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 2fbdd46b872c4c70999355d457045f4aac8aa34c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877710"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>Como habilitar uma identidade gerenciada atribuída pelo sistema para o aplicativo Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

Identidades gerenciadas para recursos do Azure fornecem uma identidade gerenciada automaticamente no Azure Active Directory para um recurso do Azure, como seu aplicativo de nuvem Spring do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código.

Este artigo mostra como habilitar e desabilitar identidades gerenciadas atribuídas pelo sistema para um aplicativo de nuvem do Azure Spring, usando o portal do Azure e a CLI (disponível na versão 0.2.4).

## <a name="prerequisites"></a>Pré-requisitos
Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, consulte a [seção visão geral](../active-directory/managed-identities-azure-resources/overview.md).
Você precisará de uma instância do Azure Spring Cloud implantada. Siga o guia de [início rápido para implantar usando o CLI do Azure](spring-cloud-quickstart.md).

## <a name="add-a-system-assigned-identity"></a>Adicionar uma identidade atribuída pelo sistema
A criação de um aplicativo com uma identidade atribuída pelo sistema requer a definição de uma propriedade adicional no aplicativo.

### <a name="using-azure-portal"></a>Usando o Portal do Azure
Para configurar uma identidade gerenciada no [portal do Azure](https://portal.azure.com/), primeiro crie um aplicativo e, em seguida, habilite o recurso.

1. Crie um aplicativo no portal, como você faria normalmente. Navegue até ele no portal.
2. Role para baixo até o grupo **configurações** no painel de navegação esquerdo.
3. Selecionar **Identidade**.
4. Na guia **Sistema atribuído**, alterne o **Status** para *Ligado*. Clique em **Save** (Salvar).

 ![Identidade gerenciada no portal](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>Usando a CLI do Azure
Você pode habilitar a identidade gerenciada atribuída pelo sistema durante a criação do aplicativo ou em um aplicativo existente.

**Habilitar a identidade gerenciada atribuída pelo sistema durante a criação de um aplicativo**

O exemplo a seguir cria um aplicativo chamado *app_name* com uma identidade gerenciada atribuída pelo sistema, conforme solicitado pelo `--assign-identity` parâmetro.

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**Habilitar identidade gerenciada atribuída pelo sistema em um aplicativo existente** Use `az spring-cloud app identity assign` o comando para habilitar a identidade atribuída pelo sistema em um aplicativo existente.

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>Obter tokens para recursos do Azure
Um aplicativo pode usar sua identidade gerenciada para obter tokens para acessar outros recursos protegidos pelo Azure Active Directory, como Azure Key Vault. Esses tokens representam o aplicativo que está acessando o recurso, não qualquer usuário específico do aplicativo.

Talvez seja necessário [Configurar o recurso de destino para permitir o acesso do seu aplicativo](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Por exemplo, se você solicitar um token para acessar Key Vault, certifique-se de ter adicionado uma política de acesso que inclui a identidade do seu aplicativo. Caso contrário, as chamadas para o Key Vault serão rejeitadas, mesmo se elas incluírem o token. Para saber mais sobre os recursos que oferecem suporte a tokens do Azure Active Directory, veja [Serviços do Azure que dão suporte à autenticação do Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

O Azure Spring Cloud compartilha o mesmo ponto de extremidade para aquisição de token com a máquina virtual do Azure. É recomendável usar o SDK do Java ou os iniciadores do Spring boot para adquirir um token.  Veja [como usar o token de VM](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) para vários exemplos de código e script e orientação sobre tópicos importantes, como tratamento de expiração de token e erros http.

Recomendado: Use o SDK do Java ou iniciadores do Spring boot para obter tokens.  Consulte os exemplos nas [próximas etapas](#next-steps).

## <a name="disable-system-assigned-identity-from-an-app"></a>Desabilitar a identidade atribuída pelo sistema de um aplicativo
A remoção de uma identidade atribuída pelo sistema também a excluirá do Azure AD. Excluir o recurso de aplicativo remove automaticamente as identidades atribuídas pelo sistema do Azure AD.

### <a name="using-azure-portal"></a>Usando o Portal do Azure
Para remover a identidade gerenciada atribuída pelo sistema de um aplicativo que não precisa mais dela:

1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta associada à assinatura do Azure que contém a instância do Azure Spring Cloud.
1. Navegue até a Máquina Virtual desejada e selecione **Identidade**.
1. Em **status atribuído pelo sistema** / , selecione **desativado** e, em seguida, clique em **salvar**:

 ![Identidade gerenciada](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>Usando a CLI do Azure
Para remover a identidade gerenciada atribuída pelo sistema de um aplicativo que não precisa mais dela, use o seguinte comando:
```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>Próximas etapas

* [Acesse Azure Key Vault com identidades gerenciadas no iniciador do Spring boot](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [Saiba mais sobre identidades gerenciadas para recursos do Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Como usar identidades gerenciadas com o SDK do Java](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
