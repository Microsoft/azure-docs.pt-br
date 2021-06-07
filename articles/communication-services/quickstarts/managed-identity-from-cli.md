---
title: Criar um aplicativo de identidade gerenciada Azure Active Directory do CLI do Azure
titleSuffix: An Azure Communication Services quickstart
description: Identidades gerenciadas permitem autorizar o acesso dos serviços de comunicação do Azure de aplicativos em execução em VMs do Azure, aplicativos de funções e outros recursos. Este guia de início rápido tem como foco o gerenciamento de identidades usando o CLI do Azure.
services: azure-communication-services
author: jbeauregardb
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 03/10/2021
ms.author: jbeauregardb
ms.reviewer: mikben
ms.openlocfilehash: 2ef5a3b162d62fa79ed01a156345070ee12b4862
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110671"
---
# <a name="authorize-access-with-managed-identity-to-your-communication-resource-in-your-development-environment"></a>Autorizar o acesso com identidade gerenciada ao recurso de comunicação em seu ambiente de desenvolvimento

O SDK de identidade do Azure fornece suporte à autenticação de tokens do Azure Active Directory (AD do Azure) para o SDK do Azure. As versões mais recentes dos SDKs dos serviços de comunicação do Azure para .NET, Java, Python e JavaScript se integram com a biblioteca de identidade do Azure para fornecer um meio simples e seguro de adquirir um token 2,0 do OAuth para autorização de solicitações dos serviços de comunicação do Azure.

Uma vantagem do SDK de identidade do Azure é que ele permite que você use o mesmo código para autenticar em vários serviços, independentemente de seu aplicativo estar em execução no ambiente de desenvolvimento ou no Azure. O SDK de identidade do Azure autentica uma entidade de segurança. Quando seu código está em execução no Azure, a entidade de segurança é uma identidade gerenciada para recursos do Azure. No ambiente de desenvolvimento, a identidade gerenciada não existe, portanto, o SDK autentica o usuário ou um aplicativo registrado para fins de teste.

## <a name="prerequisites"></a>Pré-requisitos

 - CLI do Azure. [Guia de instalação](/cli/azure/install-azure-cli)
 - Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free)

## <a name="setting-up"></a>Ao configurar o

Identidades gerenciadas devem ser habilitadas nos recursos do Azure que você está autorizando. Para saber como habilitar identidades gerenciadas para Recursos do Azure, confira um dos seguintes artigos:

- [Azure portal](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [PowerShell do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI do Azure](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modelo do Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [SDKs de Azure Resource Manager](../../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)
- [Serviços de app](../../app-service/overview-managed-identity.md)

## <a name="authenticate-a-registered-application-in-the-development-environment"></a>Autenticar um aplicativo registrado no ambiente de desenvolvimento

Se seu ambiente de desenvolvimento não oferecer suporte a logon único ou logon por meio de um navegador da Web, você poderá usar um aplicativo registrado para autenticar a partir do ambiente de desenvolvimento.

### <a name="creating-an-azure-active-directory-registered-application"></a>Criando um aplicativo registrado Azure Active Directory

Para criar um aplicativo registrado do CLI do Azure, você precisa estar conectado à conta do Azure onde deseja que as operações ocorram. Para fazer isso, você pode usar o `az login` comando e inserir suas credenciais no navegador. Depois de fazer logon em sua conta do Azure por meio da CLI, podemos chamar o `az ad sp create-for-rbac` comando para criar o aplicativo registrado.

Os exemplos a seguir usam o CLI do Azure para criar um novo aplicativo registrado

```azurecli
az ad sp create-for-rbac --name <application-name> 
```

O `az ad sp create-for-rbac` comando retornará uma lista de propriedades de entidade de serviço no formato JSON. Copie esses valores para que você possa usá-los para criar as variáveis de ambiente necessárias na próxima etapa.

```json
{
    "appId": "generated-app-ID",
    "displayName": "service-principal-name",
    "name": "http://service-principal-uri",
    "password": "generated-password",
    "tenant": "tenant-ID"
}
```
> [!IMPORTANT]
> As atribuições de função do Azure podem levar alguns minutos para serem propagadas.

#### <a name="set-environment-variables"></a>Definir variáveis de ambiente

O SDK de identidade do Azure lê valores de três variáveis de ambiente em tempo de execução para autenticar o aplicativo. A tabela a seguir descreve o valor a ser definido para cada variável de ambiente.

|Variável de ambiente|Valor
|-|-
|`AZURE_CLIENT_ID`|`appId` valor do JSON gerado 
|`AZURE_TENANT_ID`|`tenant` valor do JSON gerado
|`AZURE_CLIENT_SECRET`|`password` valor do JSON gerado

> [!IMPORTANT]
> Depois de definir as variáveis de ambiente, feche e abra novamente a janela do console. Se você estiver usando o Visual Studio ou outro ambiente de desenvolvimento, talvez seja necessário reiniciá-lo para registrar as novas variáveis de ambiente.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aprender sobre autenticação](../concepts/authentication.md)

Você também pode querer:

- [Saiba mais sobre a biblioteca de identidades do Azure](/dotnet/api/overview/azure/identity-readme)