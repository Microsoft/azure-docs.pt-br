---
title: Problemas conhecidos – gêmeos digital do Azure
description: Obtenha ajuda para reconhecer e atenuar problemas conhecidos com o Azure digital gêmeos.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 118b869cb97a7dd3ce65566a994a27d2a65a7402
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184927"
---
# <a name="known-issues-in-azure-digital-twins"></a>Problemas conhecidos no Azure digital gêmeos

Este artigo fornece informações sobre problemas conhecidos associados ao Azure digital gêmeos.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"erro de cliente 400: solicitação inadequada" em Cloud Shell

**Descrição do problema:** Os comandos no Cloud Shell em execução em *https://shell.azure.com* podem falhar intermitentemente com o erro "erro de cliente 400: solicitação inadequada para URL: http://localhost:50342/oauth2/token ", seguido por rastreamento de pilha completo.

| Isso me afeta? | Causa | Resolução |
| --- | --- | --- |
| No &nbsp; Azure &nbsp; digital &nbsp; gêmeos, isso afeta os seguintes grupos de comandos:<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | Esse é o resultado de um problema conhecido no Cloud Shell: [*obter o token do Cloud Shell falha intermitentemente com 400 erro de cliente: solicitação inválida*](https://github.com/Azure/azure-cli/issues/11749).<br><br>Isso apresenta um problema com os tokens de autenticação da instância do gêmeos digital do Azure e a autenticação baseada em [identidade gerenciada](../active-directory/managed-identities-azure-resources/overview.md) padrão do Cloud Shell. <br><br>Isso não afeta os comandos do Azure digital gêmeos `az dt` dos `az dt endpoint` grupos de comandos ou, pois eles usam um tipo diferente de token de autenticação (com base em Azure Resource Manager), que não tem um problema com a autenticação de identidade gerenciada do Cloud Shell. | Uma maneira de resolver isso é executar novamente o `az login` comando em Cloud Shell e concluir as etapas de logon subsequentes. Isso mudará sua sessão da autenticação de identidade gerenciada, o que evita o problema raiz. Depois disso, você deve ser capaz de executar novamente o comando.<br><br>Como alternativa, você pode abrir o painel de Cloud Shell no portal do Azure e concluir seu Cloud Shell trabalhar a partir daí.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Imagem do ícone de Cloud Shell na barra de ícones de portal do Azure" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>Por fim, outra solução é [instalar o CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) em seu computador para que você possa executar CLI do Azure comandos localmente. A CLI local não enfrenta esse problema. |


## <a name="missing-role-assignment-after-scripted-setup"></a>Atribuição de função ausente após a instalação com script

**Descrição do problema:** Alguns usuários podem enfrentar problemas com a parte de atribuição de função de [*como: configurar uma instância e autenticação (com script)*](how-to-set-up-instance-scripted.md). O script não indica falha, mas a função de *proprietário de dados do Azure digital gêmeos* não foi atribuída com êxito ao usuário, e esse problema afetará a capacidade de criar outros recursos em trânsito.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

| Isso me afeta? | Causa | Resolução |
| --- | --- | --- |
| Para determinar se a atribuição de função foi configurada com êxito após a execução do script, siga as instruções na seção [*verificar atribuição de função de usuário*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) do artigo de instalação. Se o usuário não for exibido com essa função, esse problema afetará você. | Para usuários conectados com um [MSA (conta Microsoft pessoal)](https://account.microsoft.com/account), a ID principal do usuário que o identifica em comandos como esse pode ser diferente do email de entrada do usuário, dificultando a descoberta e o uso do script para atribuir a função corretamente. | Para resolver o, você pode configurar sua atribuição de função manualmente usando as [instruções da CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions) ou [instruções de portal do Azure](how-to-set-up-instance-portal.md#set-up-user-access-permissions). |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Problema com a autenticação interativa do navegador no Azure. Identity 1.2.0

**Descrição do problema:** Ao gravar o código de autenticação em seus aplicativos de gêmeos digital do Azure usando a versão **1.2.0** da biblioteca do **[Azure. Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)**, você pode enfrentar problemas com o método [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) . Isso apresenta uma resposta de erro de "Azure. Identity. AuthenticationFailedException" ao tentar autenticar em uma janela do navegador. A janela do navegador pode falhar na inicialização completa ou parecer autenticar o usuário com êxito, enquanto o aplicativo cliente ainda falha com o erro.

| Isso me afeta? | Causa | Resolução |
| --- | --- | --- |
| O &nbsp; &nbsp; método afetado &nbsp; é &nbsp; usado &nbsp; nos &nbsp; &nbsp; seguintes artigos:<br><br>[*Tutorial: Codificar um aplicativo cliente*](tutorial-code.md)<br><br>[*Como: escrever código de autenticação do aplicativo*](how-to-authenticate-client.md)<br><br>[*Como: usar as APIs e SDKs do gêmeos digital do Azure*](how-to-use-apis-sdks.md) | Alguns usuários tiveram esse problema com a versão **1.2.0** da `Azure.Identity` biblioteca. | Para resolver, atualize seus aplicativos para usar uma [versão posterior](https://www.nuget.org/packages/Azure.Identity) do `Azure.Identity` . Depois de atualizar a versão da biblioteca, o navegador deve carregar e autenticar conforme o esperado. |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Problema com a autenticação de credencial do Azure padrão no Azure. Identity 1.3.0

**Descrição do problema:** Ao escrever o código de autenticação usando a versão **1.3.0** da biblioteca do **[Azure. Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)**, alguns usuários tiveram problemas com o método [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet?view=azure-dotnet&preserve-view=true) usado em muitos exemplos em vários documentos do Azure digital gêmeos. Isso apresenta uma resposta de erro de "Azure. Identity. AuthenticationFailedException: falha na autenticação do SharedTokenCacheCredential" quando o código tenta autenticar.

| Isso me afeta? | Causa | Resolução |
| --- | --- | --- |
| `DefaultAzureCredential` é usado na maioria dos exemplos de documentação para esse serviço que incluem a autenticação do. Se você estiver gravando o código `DefaultAzureCredential` de autenticação usando com a versão 1.3.0 da `Azure.Identity` biblioteca e vendo essa mensagem de erro, isso afetará você. | Isso é provavelmente um resultado de algum problema de configuração com o `Azure.Identity` . | Uma estratégia para resolver isso é excluir `SharedTokenCacheCredential` de sua credencial, conforme descrito neste [problema de DefaultAzureCredential](https://github.com/Azure/azure-sdk/issues/1970) que está aberto no momento `Azure.Identity` .<br>Outra opção é alterar seu aplicativo para usar uma versão anterior do `Azure.Identity` , como a [versão 1.2.3](https://www.nuget.org/packages/Azure.Identity/1.2.3). Isso não tem impacto funcional no Azure digital gêmeos e, portanto, também é uma solução aceita. |

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre segurança e permissões no Azure digital gêmeos:
* [*Conceitos: segurança para soluções de gêmeos digitais do Azure*](concepts-security.md)