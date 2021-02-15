---
title: Opções de autenticação do registro
description: Opções de autenticação para um registro de contêiner do Azure privado, incluindo a entrada com uma identidade de Azure Active Directory, o uso de entidades de serviço e o uso de credenciais de administrador opcionais.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5315c11e0f1e2c859384e3783ae4be5d709adb42
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148566"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Autenticar com um Registro de contêiner do Azure

Há várias maneiras de autenticar com um Registro de Contêiner do Azure, cada uma das quais é aplicável a um ou mais cenários de uso do registro.

As maneiras recomendadas incluem a autenticação em um registro diretamente por meio de [logon individual](#individual-login-with-azure-ad)ou seus aplicativos e orquestradores de contêiner podem executar a autenticação autônoma ou "sem periféricos" usando uma entidade de [serviço](#service-principal)Azure Active Directory (AD do Azure).

## <a name="authentication-options"></a>Opções de autenticação

A tabela a seguir lista os métodos de autenticação disponíveis e os cenários típicos. Consulte conteúdo vinculado para obter detalhes.

| Método                               | Como autenticar                                           | Cenários                                                            | RBAC do Azure (controle de acesso baseado em função do Azure)                             | Limitações                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Identidade individual do AD](#individual-login-with-azure-ad)                | `az acr login` em CLI do Azure                             | Push/pull interativo por desenvolvedores, testadores                                    | Yes                              | O token do AD deve ser renovado a cada 3 horas     |
| [Entidade de serviço do AD](#service-principal)                  | `docker login`<br/><br/>`az acr login` na CLI do Azure<br/><br/> Configurações de logon do registro em APIs ou ferramentas<br/><br/> [Segredo de pull do kubernetes](container-registry-auth-kubernetes.md)                                           | Envio autônomo por push do pipeline de CI/CD<br/><br/> Pull autônoma para o Azure ou serviços externos  | Yes                              | A expiração padrão da senha do SP é de 1 ano       |                                                           
| [Integrar com o AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Anexar registro quando o cluster AKS foi criado ou atualizado  | Pull autônomo para o cluster AKS                                                  | Não, somente acesso de pull             | Disponível somente com o cluster AKS            |
| [Identidade gerenciada para recursos do Azure](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` em CLI do Azure                                       | Push autônomo do pipeline do Azure CI/CD<br/><br/> Pull autônoma para os serviços do Azure<br/><br/>   | Yes                              | Use somente de serviços do Azure que [dão suporte a identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Usuário administrador](#admin-account)                            | `docker login`                                          | Push interativo/pull por desenvolvedor individual ou testador<br/><br/>Implantação do portal da imagem do registro para o serviço Azure App ou instâncias de contêiner do Azure                      | Não, sempre acesso de pull e envio por push  | Conta única por registro, não recomendada para vários usuários         |
| [Token de acesso no escopo do repositório](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login` na CLI do Azure   | Envio por push/pull interativo para o repositório por desenvolvedor individual ou testador<br/><br/> Envio por push/pull autônomo para o repositório por sistema individual ou dispositivo externo                  | Yes                              | Não integrado no momento com a identidade do AD  |

## <a name="individual-login-with-azure-ad"></a>Logon individual com o Azure AD

Ao trabalhar diretamente com o registro, como extrair imagens e enviar por push imagens de uma estação de trabalho de desenvolvimento para um registro que você criou, autentique usando sua identidade do Azure individual. Entre no [CLI do Azure](/cli/azure/install-azure-cli) com [AZ login](/cli/azure/reference-index#az-login)e execute o comando [AZ ACR login](/cli/azure/acr#az-acr-login) :

```azurecli
az login
az acr login --name <acrName>
```

Ao fazer logon com `az acr login`, a CLI usa o token criado quando você executou `az login` para, sem interrupções, autenticar a sessão com o registro. Para concluir o fluxo de autenticação, a CLI do Docker e o daemon do Docker devem ser instalados e executados em seu ambiente. `az acr login` usa o cliente do Docker para definir um token Azure Active Directory no `docker.config` arquivo. Depois de você fazer logon dessa maneira, suas credenciais são armazenadas em cache e os comandos `docker` subsequentes na sua sessão não requerem um nome de usuário ou senha.

> [!TIP]
> Use também `az acr login` para autenticar uma identidade individual quando desejar enviar por Push ou extrair artefatos diferentes de imagens do Docker para o registro, como [artefatos de OCI](container-registry-oci-artifacts.md).  

Para acesso ao registro, o token usado pelo `az acr login` é válido por **3 horas**, portanto, é recomendável que você sempre faça logon no registro antes de executar um `docker` comando. Se o token expirar, você poderá atualizá-lo usando o comando `az acr login` novamente para se reautenticar. 

Usar `az acr login` com identidades do Azure fornece [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/role-assignments-portal.md). Para alguns cenários, talvez você queira fazer logon em um registro com sua própria identidade individual no Azure AD ou configurar outros usuários do Azure com [funções e permissões específicas do Azure](container-registry-roles.md). Para cenários de serviço cruzado ou para lidar com as necessidades de um grupo de trabalho ou de um Workflow de desenvolvimento em que você não deseja gerenciar o acesso individual, também é possível fazer logon com uma [identidade gerenciada para recursos do Azure](container-registry-authentication-managed-identity.md).

### <a name="az-acr-login-with---expose-token"></a>AZ ACR login com--Expose-token

Em alguns casos, talvez seja necessário autenticar com `az acr login` o quando o daemon do Docker não estiver em execução no seu ambiente. Por exemplo, talvez seja necessário executar `az acr login` em um script no Azure cloud Shell, que fornece a CLI do Docker, mas não executa o daemon do Docker.

Para esse cenário, execute `az acr login` primeiro com o `--expose-token` parâmetro. Essa opção expõe um token de acesso em vez de fazer logon por meio da CLI do Docker.

```azurecli
az acr login --name <acrName> --expose-token
```

Saída exibe o token de acesso, abreviado aqui:

```console
{
  "accessToken": "eyJhbGciOiJSUzI1NiIs[...]24V7wA",
  "loginServer": "myregistry.azurecr.io"
}
``` 

Em seguida, execute `docker login` , passando `00000000-0000-0000-0000-000000000000` como o nome de usuário e usando o token de acesso como senha:

```console
docker login myregistry.azurecr.io --username 00000000-0000-0000-0000-000000000000 --password eyJhbGciOiJSUzI1NiIs[...]24V7wA
```

## <a name="service-principal"></a>Entidade de serviço

Se você atribuir uma [entidade de serviço](../active-directory/develop/app-objects-and-service-principals.md) ao registro, seu aplicativo ou serviço poderá usá-lo na autenticação remota. As entidades de serviço permitem o [controle de acesso baseado em função do Azure (RBAC do Azure)](../role-based-access-control/role-assignments-portal.md) a um registro, e você pode atribuir várias entidades de serviço a um registro. Várias entidades de serviço permitem que você defina acesso diferente para diferentes aplicativos.

As funções disponíveis para um registro de contêiner incluem:

* **AcrPull**: efetuar pull

* **AcrPush**: efetuar pull e push

* **Proprietário**: pull, push e atribuir funções a outros usuários

Para obter uma lista completa de funções, confira [Funções e permissões do Registro de Contêiner do Azure](container-registry-roles.md).

Para que os scripts da CLI criem uma entidade de serviço para autenticação com um registro de contêiner do Azure e mais diretrizes, consulte [autenticação do registro de contêiner do Azure com entidades de serviço](container-registry-auth-service-principal.md).

## <a name="admin-account"></a>Conta de administrador

Cada registro de contêiner inclui uma conta de usuário administrador, que fica desabilitada por padrão. Você pode habilitar o usuário administrador e gerenciar as credenciais dele no portal do Azure ou usando a CLI do Azure ou outras ferramentas do Azure. A conta de administrador tem permissões completas para o registro.

A conta de administrador é necessária no momento para alguns cenários para implantar uma imagem de um registro de contêiner para determinados serviços do Azure. Por exemplo, a conta de administrador é necessária quando você implanta uma imagem de contêiner no portal de um registro diretamente nas [instâncias de contêiner do Azure](../container-instances/container-instances-using-azure-container-registry.md#deploy-with-azure-portal) ou [nos aplicativos Web do Azure para contêineres](container-registry-tutorial-deploy-app.md).

> [!IMPORTANT]
> A conta do administrador destina-se para um único usuário acessar o registro, principalmente para fins de teste. Não recomendamos o compartilhamento das credenciais da conta de administrador entre vários usuários. Todos os usuários que se autenticam com a conta do administrador aparecem como um único usuário com acesso de push e de pull ao registro. Alterar ou desabilitar essa conta desabilita o acesso ao registro para todos os usuários que usam as credenciais dela. Para cenários remotos, recomenda-se identidade individual para usuários e entidades de serviço.
>

A conta do administrador é fornecida com duas senhas, que podem ser regeneradas. As duas senhas permitem manter conexões com o registro usando uma senha enquanto a outra é regenerada. Se a conta do administrador estiver habilitada, você poderá passar o nome de usuário e a senha para o comando `docker login` quando solicitado a informar a autenticação Básica no Registro. Por exemplo:

```
docker login myregistry.azurecr.io 
```

Para obter as práticas recomendadas para gerenciar as credenciais de logon, consulte a referência do comando [Docker login](https://docs.docker.com/engine/reference/commandline/login/) .

Para habilitar o usuário administrador para um registro existente, você pode usar o parâmetro `--admin-enabled` do comando [az acr update](/cli/azure/acr#az-acr-update) na CLI do Azure:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

Você pode habilitar o usuário administrador no Portal do Azure navegando em seu registro, selecionando **Chaves de acesso** em **CONFIGURAÇÕES** e, em seguida, **Habilitar** em **Usuário administrador**.

![Habilitar a interface do usuário administrador no Portal do Azure][auth-portal-01]

## <a name="next-steps"></a>Próximas etapas

* [Enviar sua primeira imagem por push usando a CLI do Azure](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png
