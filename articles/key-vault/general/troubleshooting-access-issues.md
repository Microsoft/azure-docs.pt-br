---
title: Solucionar problemas de política de acesso do Azure Key Vault
description: Solucionar problemas de política de acesso do Azure Key Vault
author: sebansal
ms.author: sebansal
ms.date: 08/10/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: c5fab8b856ff9c82a0de887dc9c322dbf541348b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791400"
---
# <a name="troubleshooting-azure-key-vault-access-policy-issues"></a>Solucionar problemas de política de acesso do Azure Key Vault

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="i-am-not-able-to-list-or-get-secretskeyscertificate-i-am-seeing-something-went-wrong-error"></a>Não consigo listar ou obter segredos/chaves/certificado. Estou vendo "algo deu errado..." Erro.
Se tiver problemas ao listar/obter/criar ou acessar o segredo, verifique se você tem a política de acesso definida para realizar essa operação: [Políticas de acesso ao Key Vault](./assign-access-policy-cli.md)

### <a name="how-can-i-identify-how-and-when-key-vaults-are-accessed"></a>Como identificar como e quando os cofres de chaves são acessados?

Depois de criar um ou mais cofres de chaves, provavelmente você desejará monitorar como e quando os cofres de chaves serão acessados e por quem. Você pode fazer o monitoramento habilitando o registro em log para o Azure Key Vault. Para ver um guia passo a passo de como habilitar o registro em log, [leia mais](./logging.md).

### <a name="how-can-i-monitor-vault-availability-service-latency-periods-or-other-performance-metrics-for-key-vault"></a>Como posso monitorar a disponibilidade do cofre, os períodos de latência do serviço ou outras métricas de desempenho do cofre de chaves?

Conforme você começar a escalar o serviço, o número de solicitações enviadas para o cofre de chaves aumentará. Essa demanda tem o potencial de aumentar a latência de suas solicitações e, em casos extremos, fazer com que suas solicitações sejam limitadas, o que afetará o desempenho do serviço. Você pode monitorar as métricas de desempenho do cofre de chaves e receber alertas relacionados a limites específicos. Para ver um guia passo a passo de como configurar o monitoramento, [leia mais](./alert.md).

### <a name="i-am-not-able-to-modify-access-policy-how-can-it-be-enabled"></a>Não consigo modificar a política de acesso, como ela pode ser habilitada?
O usuário precisa ter permissões suficientes do AAD para modificar a política de acesso. Nesse caso, o usuário precisaria ter uma função de colaborador mais alta.

### <a name="i-am-seeing-unknown-policy-error-what-does-that-mean"></a>Estou vendo o erro "Política Desconhecida". O que isso significa?
Há duas situações diferentes em que podemos ver a política de acesso na seção desconhecida:
* Pode ter existido um usuário anterior que tinha acesso e, por algum motivo, esse usuário não existe mais.
* Se a política de acesso for adicionada por meio do PowerShell e a política de acesso for adicionada para o objectid do aplicativo em vez da entidade de serviço.

### <a name="how-can-i-assign-access-control-per-key-vault-object"></a>Como posso atribuir o controle de acesso por objeto do cofre de chaves? 

A disponibilidade do recurso de controle de acesso por segredo/chave/certificado será notificada aqui; [leia mais](https://feedback.azure.com/forums/906355-azure-key-vault/suggestions/32213176-per-secret-key-certificate-access-control)

### <a name="how-can-i-provide-key-vault-authenticate-using-access-control-policy"></a>Como fornecer a autenticação do cofre de chaves usando a política de controle de acesso?

A maneira mais simples de autenticar um aplicativo baseado em nuvem no Key Vault é com uma identidade gerenciada; confira [Autenticar-se no Azure Key Vault](authentication.md) para obter detalhes.
Caso você esteja criando um aplicativo local, fazendo o desenvolvimento local ou, de outro modo, não possa usar uma identidade gerenciada, registre uma entidade de serviço manualmente e forneça acesso ao cofre de chaves usando uma política de controle de acesso. Confira [Atribuir uma política de controle de acesso](assign-access-policy-portal.md).

### <a name="how-can-i-give-the-ad-group-access-to-the-key-vault"></a>Como conceder ao grupo do AD acesso ao cofre de chaves?

Conceda ao grupo do AD permissões para o cofre de chaves usando o comando `az keyvault set-policy` da CLI do Azure ou o cmdlet Set-AzKeyVaultAccessPolicy do Azure PowerShell. Confira [Atribuir uma política de acesso – CLI](assign-access-policy-cli.md) e [Atribuir uma política de acesso – PowerShell](assign-access-policy-powershell.md).

O aplicativo também precisa de pelo menos uma função de IAM (Gerenciamento de Identidades e Acesso) atribuída ao cofre de chaves. Caso contrário, ele não poderá fazer logon e falhará ao acessar a assinatura, devido a direitos insuficientes. Os grupos do Azure AD com identidades gerenciadas podem exigir até oito horas para atualizar os tokens e entrar em vigor.

### <a name="how-can-i-redeploy-key-vault-with-arm-template-without-deleting-existing-access-policies"></a>Como reimplantar o Key Vault com o modelo do ARM sem excluir as políticas de acesso existentes?

Atualmente, a reimplantação do Key Vault exclui as políticas de acesso no Key Vault e as substitui pela política de acesso no modelo do ARM. Não há opção incremental para políticas de acesso do Key Vault. Para preservar as políticas de acesso no Key Vault, você precisa ler as políticas de acesso existentes nele e popular o modelo do ARM com essas políticas para evitar interrupções de acesso.

Outra opção que pode ajudar nesse cenário é usar o Azure RBAC e funções como uma alternativa às políticas de acesso. Com o Azure RBAC, você pode implantar novamente o cofre de chaves sem especificar a política novamente. Você pode ler mais sobre essa solução [aqui](./rbac-guide.md).

### <a name="recommended-troubleshooting-steps-for-following-error-types"></a>Etapas de solução de problemas recomendadas para os seguintes tipos de erro

* HTTP 401: Solicitação não autenticada – [Etapas de solução de problemas](rest-error-codes.md#http-401-unauthenticated-request)
* HTTP 403: Permissões insuficientes – [Etapas de solução de problemas](rest-error-codes.md#http-403-insufficient-permissions)
* HTTP 429: Número excessivo de solicitações – [Etapas de solução de problemas](rest-error-codes.md#http-429-too-many-requests)
* Verifique se você tem permissão para excluir acesso ao key Vault: Confira [Atribuir uma política de acesso – CLI](assign-access-policy-cli.md), [Atribuir uma política de acesso – PowerShell](assign-access-policy-powershell.md) ou [Atribuir uma política de acesso – Portal](assign-access-policy-portal.md).
* Se você tiver problemas com a autenticação no cofre de chaves no código, use [SDK de autenticação](https://azure.github.io/azure-sdk/posts/2020-02-25/defaultazurecredentials.html)

### <a name="what-are-the-best-practices-i-should-implement-when-key-vault-is-getting-throttled"></a>Quais são as melhores práticas que devo implementar quando o cofre de chaves estiver sendo limitado?
Siga as melhores práticas documentadas [aqui](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

## <a name="next-steps"></a>Próximas etapas

Saiba como solucionar erros de autenticação no Key Vault: [Guia de solução de problemas do Key Vault](rest-error-codes.md).