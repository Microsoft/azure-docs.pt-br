---
title: Solucionar problemas de logon no registro
description: Sintomas, causas e resolução de problemas comuns ao fazer logon em um registro de contêiner do Azure
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 5deb1717cf3886d8ea9c021d92afa358946b16dc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99052071"
---
# <a name="troubleshoot-registry-login"></a>Solucionar problemas de logon do registro

Este artigo ajuda você a solucionar problemas que podem ocorrer ao fazer logon em um registro de contêiner do Azure. 

## <a name="symptoms"></a>Sintomas

Pode incluir um ou mais dos seguintes:

* Não é possível fazer logon no registro usando `docker login` , `az acr login` ou ambos
* Não é possível fazer logon no registro e você recebe um erro `unauthorized: authentication required` ou `unauthorized: Application not registered with AAD`
* Não é possível fazer logon no registro e você recebe CLI do Azure erro `Could not connect to the registry login server`
* Não é possível enviar por Push ou efetuar pull de imagens e você recebe o erro do Docker `unauthorized: authentication required`
* Não é possível acessar o registro do serviço kubernetes do Azure, Azure DevOps ou outro serviço do Azure
* Não é possível acessar o registro e você recebe um erro `Error response from daemon: login attempt failed with status: 403 Forbidden` -consulte [solucionar problemas de rede com o registro](container-registry-troubleshoot-access.md)
* Não é possível acessar ou exibir as configurações do registro no portal do Azure ou gerenciar o registro usando o CLI do Azure

## <a name="causes"></a>Causas

* O Docker não está configurado corretamente em seu ambiente- [solução](#check-docker-configuration)
* O registro não existe ou o nome está incorreto- [solução](#specify-correct-registry-name)
* As credenciais do registro não são válidas- [solução](#confirm-credentials-to-access-registry)
* As credenciais não são autorizadas para operações de push, pull ou Azure Resource Manager- [solução](#confirm-credentials-are-authorized-to-access-registry)
* As credenciais estão expiradas- [solução](#check-that-credentials-arent-expired)

## <a name="further-diagnosis"></a>Diagnóstico adicional 

Execute o comando [AZ ACR check-Health](/cli/azure/acr#az-acr-check-health) para obter mais informações sobre a integridade do ambiente do registro e, opcionalmente, o acesso a um registro de destino. Por exemplo, diagnostique erros de configuração do Docker ou problemas de logon do Azure Active Directory. 

Consulte [verificar a integridade de um registro de contêiner do Azure](container-registry-check-health.md) para obter exemplos de comando. Se forem relatados erros, examine a [referência de erro](container-registry-health-error-reference.md) e as seções a seguir para obter as soluções recomendadas.

Se você estiver tendo problemas ao usar o serviço wih do Azure kubernetes, execute o comando [AZ AKs check-ACR](/cli/azure/aks#az_aks_check_acr) para validar se o registro está acessível no cluster AKs.

> [!NOTE]
> Alguns erros de autenticação ou autorização também podem ocorrer se houver configurações de firewall ou de rede que impeçam o acesso ao registro. Consulte [solucionar problemas de rede com o registro](container-registry-troubleshoot-access.md).

## <a name="potential-solutions"></a>Possíveis soluções

### <a name="check-docker-configuration"></a>Verificar configuração do Docker

A maioria dos fluxos de autenticação do registro de contêiner do Azure requer uma instalação local do Docker para que você possa autenticar com o registro para operações como enviar e extrair imagens. Confirme se o cliente e o daemon da CLI do Docker (mecanismo do Docker) estão em execução no seu ambiente. Você precisa de cliente do Docker versão 18, 3 ou posterior.

Links relacionados:

* [Visão geral da autenticação](container-registry-authentication.md#authentication-options)
* [Perguntas frequentes do registro de contêiner](container-registry-faq.md)

### <a name="specify-correct-registry-name"></a>Especifique o nome correto do registro

Ao usar `docker login` , forneça o nome do servidor de logon completo do registro, como *myregistry.azurecr.Io*. Certifique-se de usar apenas letras minúsculas. Exemplo:

```console
docker login myregistry.azurecr.io
```

Ao usar [AZ ACR login](/cli/azure/acr#az-acr-login) com uma identidade Azure Active Directory, primeiro [entre no CLI do Azure](/cli/azure/authenticate-azure-cli)e, em seguida, especifique o nome do recurso do Azure do registro. O nome do recurso é o nome fornecido quando o registro foi criado, como *myregistry* (sem um sufixo de domínio). Exemplo:

```azurecli
az acr login --name myregistry
```

Links relacionados:

* [az acr login obteve sucesso, mas o docker falha com erro: não autorizado: autenticação necessária](container-registry-faq.md#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required )

### <a name="confirm-credentials-to-access-registry"></a>Confirmar credenciais para acessar o registro

Verifique a validade das credenciais usadas para o seu cenário ou foram fornecidas por um proprietário do registro. Alguns problemas possíveis:

* Se estiver usando uma entidade de serviço Active Directory, certifique-se de usar as credenciais corretas no locatário Active Directory:
  * Nome de usuário-ID do aplicativo da entidade de serviço (também chamada de *ID do cliente*)
  * Senha-senha da entidade de serviço (também chamada de *segredo do cliente*)
* Se estiver usando um serviço do Azure, como o serviço kubernetes do Azure ou o Azure DevOps para acessar o registro, confirme a configuração do registro para seu serviço. 
* Se você executou `az acr login` com a `--expose-token` opção, que habilita o logon do registro sem usar o daemon do Docker, certifique-se de autenticar com o nome de usuário `00000000-0000-0000-0000-000000000000` .
* Se o registro estiver configurado para [acesso de pull anônimo](container-registry-faq.md#how-do-i-enable-anonymous-pull-access), as credenciais existentes do Docker armazenadas de um logon anterior do Docker poderão impedir o acesso anônimo. Execute `docker logout` antes de tentar uma operação de pull anônima no registro.

Links relacionados:

* [Visão geral da autenticação](container-registry-authentication.md#authentication-options)
* [Logon individual com o Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)
* [Fazer logon com entidade de serviço](container-registry-auth-service-principal.md)
* [Fazer logon com identidade gerenciada](container-registry-authentication-managed-identity.md)
* [Logon com o token no escopo do repositório](container-registry-repository-scoped-permissions.md)
* [Fazer logon com a conta de administrador](container-registry-authentication.md#admin-account)
* [Códigos de erro de autorização e autenticação do Azure AD](../active-directory/develop/reference-aadsts-error-codes.md)
* referência de [logon do ACR AZ](/cli/azure/acr#az-acr-login)

### <a name="confirm-credentials-are-authorized-to-access-registry"></a>Confirmar que as credenciais estão autorizadas a acessar o registro

Confirme as permissões de registro que estão associadas às credenciais, como a `AcrPull` função do Azure para efetuar pull de imagens do registro ou a `AcrPush` função para enviar imagens por push. 

O acesso a um registro no portal ou gerenciamento de registro usando o CLI do Azure requer pelo menos a `Reader` função ou permissões equivalentes para executar Azure Resource Manager operações.

Se suas permissões foram alteradas recentemente para permitir o acesso ao registro por meio do portal, talvez seja necessário tentar uma sessão Incognito ou privada em seu navegador para evitar qualquer cookie ou cache de navegador obsoleto.

Você ou um proprietário do registro deve ter privilégios suficientes na assinatura para adicionar ou remover atribuições de função.

Links relacionados:

* [Funções e permissões do Azure-registro de contêiner do Azure](container-registry-roles.md)
* [Logon com o token no escopo do repositório](container-registry-repository-scoped-permissions.md)
* [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](../role-based-access-control/role-assignments-portal.md)
* [Use o portal para criar um aplicativo do Azure AD e uma entidade de serviço que possa acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md)
* [Criar um novo segredo do aplicativo](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)
* [Autenticação do Azure AD e códigos de autorização](../active-directory/develop/reference-aadsts-error-codes.md)

### <a name="check-that-credentials-arent-expired"></a>Verifique se as credenciais não expiraram

Os tokens e as credenciais de Active Directory podem expirar após os períodos definidos, impedindo o acesso ao registro. Para habilitar o acesso, talvez as credenciais precisem ser redefinidas ou regeneradas.

* Se estiver usando uma identidade do AD individual, uma identidade gerenciada ou uma entidade de serviço para o logon do registro, o token do AD expirará após 3 horas. Faça logon novamente no registro.  
* Se você estiver usando uma entidade de serviço do AD com um segredo do cliente expirado, um proprietário da assinatura ou administrador da conta precisará redefinir as credenciais ou gerar uma nova entidade de serviço.
* Se estiver usando um [token no escopo do repositório](container-registry-repository-scoped-permissions.md), um proprietário do registro poderá precisar redefinir uma senha ou gerar um novo token.

Links relacionados:

* [Redefinir credenciais da entidade de serviço](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset)
* [Regenerar senhas de token](container-registry-repository-scoped-permissions.md#regenerate-token-passwords)
* [Logon individual com o Azure AD](container-registry-authentication.md#individual-login-with-azure-ad)

## <a name="advanced-troubleshooting"></a>Solução de problemas avançada

Se a [coleção de logs de recursos](container-registry-diagnostics-audit-logs.md) estiver habilitada no registro, examine o log ContainterRegistryLoginEvents. Esse log armazena os eventos de autenticação e o status, incluindo a identidade de entrada e o endereço IP. Consulte o log para obter [falhas de autenticação do registro](container-registry-diagnostics-audit-logs.md#registry-authentication-failures). 

Links relacionados:

* [Logs para avaliação de diagnóstico e auditoria](container-registry-diagnostics-audit-logs.md)
* [Perguntas frequentes do registro de contêiner](container-registry-faq.md)
* [Melhores práticas para o Registro de Contêiner do Azure](container-registry-best-practices.md)

## <a name="next-steps"></a>Próximas etapas

Se você não resolver o problema aqui, consulte as opções a seguir.

* Outros tópicos de solução de problemas de registro incluem:
  * [Solucionar problemas de rede com o registro](container-registry-troubleshoot-access.md)
  * [Solucionar problemas de desempenho de registro](container-registry-troubleshoot-performance.md)
* Opções de [suporte da Comunidade](https://azure.microsoft.com/support/community/)
* [P e R da Microsoft](/answers/products/)
* [Abrir um tíquete de suporte](https://azure.microsoft.com/support/create-ticket/) -com base nas informações fornecidas, um diagnóstico rápido pode ser executado para falhas de autenticação no registro