---
title: Problemas conhecidos – Gêmeos Digitais do Azure
description: Receba ajuda para reconhecer e atenuar os problemas conhecidos dos Gêmeos Digitais do Azure.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 641b44a5e21e6646c07e6e1511e1c4ff01707f79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434093"
---
# <a name="known-issues-in-azure-digital-twins"></a>Problemas conhecidos nos Gêmeos Digitais do Azure

Este artigo apresenta informações sobre os problemas conhecidos dos Gêmeos Digitais do Azure.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"Erro de cliente 400: solicitação inadequada" no Cloud Shell

**Descrição do problema:** comandos do Cloud Shell em execução em *https://shell.azure.com* podem falhar intermitentemente com o erro "Erro de cliente 400: solicitação inadequada para URL: http://localhost:50342/oauth2/token", seguido por rastreamento de pilha completo.

| Isso me afeta? | Causa | Resolução |
| --- | --- | --- |
| Nos&nbsp;Gêmeos&nbsp;Digitais&nbsp;do Azure, isso afeta os seguintes grupos de comandos:<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | Isso é resultado de um problema conhecido no Cloud Shell: [*Falha ao obter token do Cloud Shell de maneira intermitente com erro de cliente 400: solicitação incorreta*](https://github.com/Azure/azure-cli/issues/11749).<br><br>Isso é um problema com os tokens de autenticação de instâncias dos Gêmeos Digitais do Azure e a autenticação padrão baseada em [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) do Cloud Shell. <br><br>Isso não afeta os comandos dos Gêmeos Digitais do Azure dos grupos `az dt` ou `az dt endpoint`, pois eles usam outro tipo de token de autenticação (com base no Azure Resource Manager), que não tem conflito com a autenticação de identidade gerenciada do Cloud Shell. | Uma forma de resolver isso é executar novamente o comando `az login` no Cloud Shell e seguir as etapas de logon subsequentes. Dessa forma, sua sessão deixará de usar a autenticação de identidade gerenciada, o que evita o problema raiz. Depois disso, você poderá executar novamente o comando.<br><br>Outra opção é abrir o painel do Cloud Shell no portal do Azure e realizar seu trabalho nele.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Imagem do ícone do Cloud Shell na barra de ícones do portal do Azure" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>Por fim, outra solução é [instalar a CLI do Azure](/cli/azure/install-azure-cli) no computador para executar os comandos dela localmente. A CLI local não enfrenta esse problema. |


## <a name="missing-role-assignment-after-scripted-setup"></a>Ausência de atribuição de função após configuração com script

**Descrição do problema:** alguns usuários podem enfrentar problemas com a parte de atribuição de função de [*Instruções: configurar uma instância e uma autenticação (com script)* ](how-to-set-up-instance-scripted.md). O script não indica a falha, mas a função de *proprietário de dados dos Gêmeos Digitais do Azure* não é atribuída ao usuário, o que impede a criação de outros recursos posteriormente.

| Isso me afeta? | Causa | Resolução |
| --- | --- | --- |
| Para determinar se a atribuição da função foi feita após a execução do script, siga as instruções na seção [*Verificar atribuição de função de usuário*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) do artigo de instalação. Se o seu usuário não aparece com essa função, o problema afeta você. | Para os usuários conectados com uma [MSA (conta Microsoft)](https://account.microsoft.com/account) pessoal, a ID principal que o identifica em comandos como esse pode ser diferente do email de entrada do usuário, por isso, talvez o script não descubra e atribua a função corretamente. | Para resolver, você pode atribuir a função manualmente usando as [instruções da CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions) ou as [instruções de portal do Azure](how-to-set-up-instance-portal.md#set-up-user-access-permissions). |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Problema com a autenticação interativa do navegador no Azure.Identity 1.2.0

**Descrição do problema:** quando você cria código de autenticação em aplicativos dos Gêmeos Digitais do Azure usando a versão **1.2.0** da **biblioteca [Azure.Identity](/dotnet/api/azure.identity)** , podem surgir problemas relacionados ao método [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential). O erro mostra a mensagem "Azure.Identity.AuthenticationFailedException" quando você tenta se autenticar em uma janela de navegador. Talvez a janela do navegador não seja iniciada ou pareça ter autenticado o usuário, enquanto o aplicativo cliente ainda apresenta o erro.

| Isso me afeta? | Causa | Resolução |
| --- | --- | --- |
| O&nbsp;método&nbsp;afetado&nbsp;é&nbsp;usado&nbsp;nos&nbsp;seguintes&nbsp;artigos:<br><br>[*Tutorial: Codificar um aplicativo cliente*](tutorial-code.md)<br><br>[*Instruções: escrever código de autenticação de aplicativo*](how-to-authenticate-client.md)<br><br>[*Instruções: usar as APIs e os SDKs dos Gêmeos Digitais do Azure*](how-to-use-apis-sdks.md) | Alguns usuários tiveram esse problema com a versão **1.2.0** da biblioteca `Azure.Identity`. | Para resolver, atualize seus aplicativos para usar uma [versão posterior](https://www.nuget.org/packages/Azure.Identity) do `Azure.Identity`. Depois da atualização da versão da biblioteca, o navegador deve carregar e autenticar conforme o esperado. |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Problema com a autenticação de credencial do Azure padrão no Azure.Identity 1.3.0

**Descrição do problema:** ao criar código de autenticação usando a versão **1.3.0** da **biblioteca [Azure.Identity](/dotnet/api/azure.identity)** , alguns usuários tiveram problemas com o método [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) usado em várias amostras na documentação dos Gêmeos Digitais do Azure. O erro aparece como a mensagem "Azure.Identity.AuthenticationFailedException: falha na autenticação de SharedTokenCacheCredential" quando o código tenta autenticar.

| Isso me afeta? | Causa | Resolução |
| --- | --- | --- |
| `DefaultAzureCredential` é usado na maioria dos exemplos da documentação do serviço que incluem autenticação. Se você está criando código de autenticação usando o `DefaultAzureCredential` com a versão 1.3.0 da biblioteca `Azure.Identity` e viu essa mensagem de erro, isso afeta você. | A causa provável é algum erro de configuração no `Azure.Identity`. | Uma estratégia para resolver isso é excluir o `SharedTokenCacheCredential` da credencial, conforme descrito neste [problema do DefaultAzureCredential](https://github.com/Azure/azure-sdk/issues/1970) que está aberto no momento em relação ao `Azure.Identity`.<br>Outra opção é alterar o aplicativo para usar uma versão anterior do `Azure.Identity`, como a [versão 1.2.3](https://www.nuget.org/packages/Azure.Identity/1.2.3). Isso não tem impacto funcional nos Gêmeos Digitais do Azure, portanto, também é uma solução aceita. |

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre segurança e permissões nos Gêmeos Digitais do Azure:
* [*Conceitos: segurança para soluções dos Gêmeos Digitais do Azure*](concepts-security.md)