---
title: Registro de contêiner do Azure-perguntas frequentes
description: Respostas para perguntas frequentes relacionadas ao serviço de registro de contêiner do Azure
services: container-registry
author: sajayantony
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 07/02/2019
ms.author: sajaya
ms.openlocfilehash: 2b835765bbd40ffbd4a5117f767a7ba163e41dda
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309295"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Perguntas frequentes sobre o registro de contêiner do Azure

Este artigo aborda as perguntas frequentes e problemas conhecidos sobre o registro de contêiner do Azure.

## <a name="resource-management"></a>Gerenciamento de recursos

- [Posso criar um registro de contêiner do Azure usando um modelo do Resource Manager?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Há uma verificação de vulnerabilidade de segurança para imagens no ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Como fazer configurar o kubernetes com o registro de contêiner do Azure?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Como fazer obter credenciais de administrador para um registro de contêiner?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Como fazer obter credenciais de administrador em um modelo do Resource Manager?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [A exclusão da replicação falha com o status proibido, embora a replicação seja excluída usando o CLI do Azure ou Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [As regras de firewall são atualizadas com êxito, mas não entram em vigor](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Posso criar um registro de contêiner do Azure usando um modelo do Resource Manager?

Sim. Aqui está [um modelo](https://github.com/Azure/azure-cli/blob/master/src/command_modules/azure-cli-acr/azure/cli/command_modules/acr/template.json) que você pode usar para criar um registro.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Há uma verificação de vulnerabilidade de segurança para imagens no ACR?

Sim. Consulte a documentação em [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) e [azul-piscina](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Como fazer configurar o kubernetes com o registro de contêiner do Azure?

Consulte a documentação do [kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) e as etapas para o [serviço kubernetes do Azure](container-registry-auth-aks.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Como fazer obter credenciais de administrador para um registro de contêiner?

> [!IMPORTANT]
> A conta de usuário administrador é projetada para que um único usuário acesse o registro, principalmente para fins de teste. Não é recomendável compartilhar as credenciais da conta do administrador com vários usuários. Para cenários remotos, recomenda-se identidade individual para usuários e entidades de serviço. Consulte [visão geral da autenticação](container-registry-authentication.md).

Antes de obter as credenciais de administrador, verifique se o usuário administrador do registro está habilitado.

Para obter credenciais usando o CLI do Azure:

```azurecli
az acr credential show -n myRegistry
```

Usando o Azure PowerShell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Como fazer obter credenciais de administrador em um modelo do Resource Manager?

> [!IMPORTANT]
> A conta de usuário administrador é projetada para que um único usuário acesse o registro, principalmente para fins de teste. Não é recomendável compartilhar as credenciais da conta do administrador com vários usuários. Para cenários remotos, recomenda-se identidade individual para usuários e entidades de serviço. Consulte [visão geral da autenticação](container-registry-authentication.md).

Antes de obter as credenciais de administrador, verifique se o usuário administrador do registro está habilitado.

Para obter a primeira senha:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[0].value]"
}
```

Para obter a segunda senha:

```json
{
    "password": "[listCredentials(resourceId('Microsoft.ContainerRegistry/registries', 'myRegistry'), '2017-10-01').passwords[1].value]"
}
```

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>A exclusão da replicação falha com o status proibido, embora a replicação seja excluída usando o CLI do Azure ou Azure PowerShell

O erro é visto quando o usuário tem permissões em um registro, mas não tem permissões de nível de leitor na assinatura. Para resolver esse problema, atribua permissões de leitor na assinatura ao usuário:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>As regras de firewall são atualizadas com êxito, mas não entram em vigor

Leva algum tempo para propagar as alterações de regra de firewall. Depois de alterar as configurações de firewall, aguarde alguns minutos antes de verificar essa alteração.


## <a name="registry-operations"></a>Operações de registro

- [API HTTP de registro do Docker do Como fazer Access v2?](#how-do-i-access-docker-registry-http-api-v2)
- [Como fazer excluir todos os manifestos que não são referenciados por nenhuma marca em um repositório?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Por que o uso da cota do registro não é reduzido após a exclusão de imagens?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Como fazer validar as alterações de cota de armazenamento?](#how-do-i-validate-storage-quota-changes)
- [Como fazer autenticar com o meu registro ao executar a CLI em um contêiner?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [O registro de contêiner do Azure oferece configuração somente TLS v 1.2 e como habilitar o TLS v 1.2?](#does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12)
- [O registro de contêiner do Azure dá suporte à confiança de conteúdo?](#does-azure-container-registry-support-content-trust)
- [Como fazer conceder acesso a imagens pull ou push sem permissão para gerenciar o recurso de registro?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Como fazer habilitar a quarentena de imagem automática para um registro](#how-do-i-enable-automatic-image-quarantine-for-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>API HTTP de registro do Docker do Como fazer Access v2?

O ACR dá suporte à API HTTP v2 do registro do Docker. As APIs podem ser acessadas em `https://<your registry login server>/v2/`. Exemplo: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Como fazer excluir todos os manifestos que não são referenciados por nenhuma marca em um repositório?

Se você estiver no bash:

```bash
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Para o PowerShell:

```powershell
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Observação: Você pode adicionar `-y` no comando excluir para ignorar a confirmação.

Para obter mais informações, consulte [Excluir imagens de contêiner no registro de contêiner do Azure](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Por que o uso da cota do registro não é reduzido após a exclusão de imagens?

Essa situação pode ocorrer se as camadas subjacentes ainda estiverem sendo referenciadas por outras imagens de contêiner. Se você excluir uma imagem sem referências, o uso do registro será atualizado em alguns minutos.

### <a name="how-do-i-validate-storage-quota-changes"></a>Como fazer validar as alterações de cota de armazenamento?

Crie uma imagem com uma camada de 1 GB usando o seguinte arquivo do Docker. Isso garante que a imagem tenha uma camada que não seja compartilhada por nenhuma outra imagem no registro.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Crie e envie a imagem por push para o registro usando a CLI do Docker.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Você deve ser capaz de ver que o uso do armazenamento aumentou na portal do Azure ou pode consultar o uso usando a CLI.

```bash
az acr show-usage -n myregistry
```

Exclua a imagem usando o CLI do Azure ou o portal e verifique o uso atualizado em alguns minutos.

```bash
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Como fazer autenticar com o meu registro ao executar a CLI em um contêiner?

Você precisa executar o contêiner de CLI do Azure montando o soquete do Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

No contêiner, instale `docker`:

```bash
apk --update add docker
```

Em seguida, autentique com o registro:

```azurecli
az acr login -n MyRegistry
```

### <a name="does-azure-container-registry-offer-tls-v12-only-configuration-and-how-to-enable-tls-v12"></a>O registro de contêiner do Azure oferece configuração somente TLS v 1.2 e como habilitar o TLS v 1.2?

Sim. Habilite o TLS usando qualquer cliente do Docker recente (versão 18.03.0 e posterior). 

### <a name="does-azure-container-registry-support-content-trust"></a>O registro de contêiner do Azure dá suporte à confiança de conteúdo?

Sim, você pode usar imagens confiáveis no registro de contêiner do Azure, pois o [Docker Notary](https://docs.docker.com/notary/getting_started/) foi integrado e pode ser habilitado. Para obter detalhes, consulte [confiança de conteúdo no registro de contêiner do Azure](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Onde está localizado o arquivo para a impressão digital?

Em `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* As chaves públicas e os certificados de todas as funções (exceto as funções de delegação `root.json`) são armazenados no.
* As chaves públicas e os certificados da função de delegação são armazenados no arquivo JSON de sua função pai (por `targets.json` exemplo, `targets/releases` para a função).

É recomendável verificar as chaves públicas e os certificados após a verificação geral de TUF feita pelo cliente do Docker e do Notary.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Como fazer conceder acesso a imagens pull ou push sem permissão para gerenciar o recurso de registro?

O ACR dá suporte a [funções personalizadas](container-registry-roles.md) que fornecem diferentes níveis de permissões. Especificamente, `AcrPull` e `AcrPush` as funções permitem que os usuários recebam e/ou enviem imagens por push sem a permissão para gerenciar o recurso de registro no Azure.

* Portal do Azure: Seu registro-> Access Control (iam)-> Adicionar (selecione `AcrPull` ou `AcrPush` para a função).
* CLI do Azure: Localize a ID de recurso do Registro executando o seguinte comando:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Em seguida, você pode `AcrPull` atribuir `AcrPush` a função ou a um usuário (o exemplo `AcrPull`a seguir usa):

  ```azurecli
    az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
    ```

  Ou, atribua a função a um princípio de serviço identificado por sua ID de aplicativo:

  ```
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

O destinatário é então capaz de autenticar e acessar imagens no registro.

* Para autenticar em um registro:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Para listar repositórios:

  ```azurecli
  az acr repository list -n myRegistry
  ```

 Para efetuar pull de uma imagem:
    
  ```azurecli
  docker pull myregistry.azurecr.io/hello-world
  ```

Com o uso apenas `AcrPull` da função ou `AcrPush` , o destinatário não tem a permissão para gerenciar o recurso de registro no Azure. Por exemplo, `az acr list` ou `az acr show -n myRegistry` não mostrará o registro.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Como fazer habilitar a quarentena de imagem automática para um registro?

A quarentena da imagem é atualmente um recurso de visualização do ACR. Você pode habilitar o modo de quarentena de um registro para que somente as imagens que passaram com êxito a verificação de segurança fiquem visíveis para os usuários normais. Para obter detalhes, consulte o [repositório GitHub do ACR](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

## <a name="diagnostics-and-health-checks"></a>Diagnóstico e verificações de integridade

- [Verificar a integridade com`az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull falha com erro: net/http: solicitação cancelada ao aguardar conexão (o cliente. tempo limite excedido ao aguardar os cabeçalhos)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [o push do Docker tem êxito, mas docker pull falha com erro: não autorizado: autenticação necessária](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [Habilitar e obter os logs de depuração do daemon do Docker](#enable-and-get-the-debug-logs-of-the-docker-daemon) 
- [As novas permissões de usuário podem não ser efetivas imediatamente após a atualização](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [As informações de autenticação não são fornecidas no formato correto em chamadas à API REST diretas](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Por que o portal do Azure não listar todos os meus repositórios ou marcas?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Como fazer coletar rastreamentos http no Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Verificar a integridade com`az acr check-health`

Para solucionar problemas comuns de ambiente e do registro, consulte [verificar a integridade de um registro de contêiner do Azure](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull falha com erro: net/http: solicitação cancelada ao aguardar conexão (o cliente. tempo limite excedido ao aguardar os cabeçalhos)

 - Se esse erro for um problema transitório, a nova tentativa terá sucesso.
 - Se `docker pull` o falhar continuamente, pode haver um problema com o daemon do Docker. O problema geralmente pode ser mitigado reiniciando o daemon do Docker. 
 - Se você continuar a ver esse problema após a reinicialização do daemon do Docker, o problema poderá ser de alguns problemas de conectividade de rede com o computador. Para verificar se a rede geral no computador está íntegra, execute o seguinte comando para testar a conectividade do ponto de extremidade. A versão `az acr` mínima que contém esse comando de verificação de conectividade é 2.2.9. Atualize seu CLI do Azure se você estiver usando uma versão mais antiga.
 
   ```azurecli
    az acr check-health -n myRegistry
    ```
 - Você sempre deve ter um mecanismo de repetição em todas as operações de cliente do Docker.

### <a name="docker-pull-is-slow"></a>O Pull do Docker está lento
Use [essa](http://www.azurespeed.com/Azure/Download) ferramenta para testar a velocidade de download da rede do seu computador. Se a rede da máquina estiver lenta, considere usar a VM do Azure na mesma região que o registro. Isso geralmente proporciona uma velocidade de rede mais rápida.

### <a name="docker-push-is-slow"></a>O push do Docker está lento
Use [essa](http://www.azurespeed.com/Azure/Upload) ferramenta para testar a velocidade de carregamento da rede do seu computador. Se a rede da máquina estiver lenta, considere usar a VM do Azure na mesma região que o registro. Isso geralmente proporciona uma velocidade de rede mais rápida.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>O push do Docker tem êxito, mas docker pull falha com erro: não autorizado: autenticação necessária

Esse erro pode ocorrer com a versão do Red Hat do daemon do Docker, `--signature-verification` onde é habilitado por padrão. Você pode verificar as opções do daemon do Docker para Red Hat Enterprise Linux (RHEL) ou Fedora executando o seguinte comando:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Por exemplo, o servidor Fedora 28 tem as seguintes opções de daemon do Docker:

```
OPTIONS='--selinux-enabled --log-driver=journald --live-restore'
```

Com `--signature-verification=false` Missing, `docker pull` falha com um erro semelhante a:

```bash
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Para resolver o erro:
1. Adicione a opção `--signature-verification=false` ao arquivo `/etc/sysconfig/docker`de configuração do daemon do Docker. Por exemplo:

  ```
  OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'
  ```
2. Reinicie o serviço daemon do Docker executando o seguinte comando:

  ```bash
  sudo systemctl restart docker.service
  ```

Os detalhes `--signature-verification` de podem ser encontrados executando `man dockerd`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Habilitar e obter os logs de depuração do daemon do Docker  

Comece `dockerd` com a `debug` opção. Primeiro, crie o arquivo de configuração do daemon do`/etc/docker/daemon.json`Docker () se ele não existir e `debug` adicione a opção:

```json
{   
    "debug": true   
}
```

Em seguida, reinicie o daemon. Por exemplo, com o Ubuntu 14, 4:

```bash
sudo service docker restart
```

Os detalhes podem ser encontrados na [documentação](https://docs.docker.com/engine/admin/#enable-debugging)do Docker. 

 * Os logs podem ser gerados em locais diferentes, dependendo do seu sistema. Por exemplo, para o Ubuntu 14, 4, é `/var/log/upstart/docker.log`.   
Consulte a [documentação](https://docs.docker.com/engine/admin/#read-the-logs) do Docker para obter detalhes.    

 * Por Docker for Windows, os logs são gerados em% LOCALAPPDATA%/Docker/. No entanto, ele talvez não contenha todas as informações de depuração ainda.   

   Para acessar o log completo do daemon, você pode precisar de algumas etapas adicionais:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Agora você tem acesso a todos os arquivos da VM em execução `dockerd`. O log está em `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>As novas permissões de usuário podem não ser efetivas imediatamente após a atualização

Quando você concede novas permissões (novas funções) a uma entidade de serviço, a alteração pode não entrar em vigor imediatamente. Há dois motivos possíveis:

* Atraso na atribuição de função Azure Active Directory. Normalmente, é rápido, mas pode levar minutos devido ao atraso na propagação.
* Atraso de permissão no servidor de token ACR. Isso pode levar até 10 minutos. Para mitigar, você pode `docker logout` e autenticar novamente com o mesmo usuário após 1 minuto:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Atualmente, o ACR não dá suporte à exclusão da replicação doméstica pelos usuários. A solução alternativa é incluir a criação de replicação de página inicial no modelo, mas ignorar sua `"condition": false` elaboração adicionando conforme mostrado abaixo:

```json
{
    "name": "[concat(parameters('acrName'), '/', parameters('location'))]",
    "condition": false,
    "type": "Microsoft.ContainerRegistry/registries/replications",
    "apiVersion": "2017-10-01",
    "location": "[parameters('location')]",
    "properties": {},
    "dependsOn": [
        "[concat('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
     ]
},
```

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>As informações de autenticação não são fornecidas no formato correto em chamadas à API REST diretas

Você pode encontrar um `InvalidAuthenticationInfo` erro, especialmente usando a `curl` `--location` ferramenta com a opção `-L`(para seguir os redirecionamentos).
Por exemplo, buscar o blob usando `curl` with Option e a `-L` autenticação básica:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

pode resultar na seguinte resposta:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

A causa raiz é que algumas `curl` implementações seguem redirecionamentos com cabeçalhos da solicitação original.

Para resolver o problema, você precisa seguir os redirecionamentos manualmente sem os cabeçalhos. Imprima os cabeçalhos de resposta com `-D -` a opção `curl` de e, em seguida `Location` , extraia o cabeçalho:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Por que o portal do Azure não listar todos os meus repositórios ou marcas? 

Se você estiver usando o navegador Microsoft Edge/IE, poderá ver no máximo 100 repositórios ou marcas. Se o registro tiver mais de 100 repositórios ou marcas, recomendamos que você use o navegador Firefox ou Chrome para listar todos eles.

### <a name="how-do-i-collect-http-traces-on-windows"></a>Como fazer coletar rastreamentos http no Windows?

#### <a name="prerequisites"></a>Pré-requisitos

- Habilite a descriptografia de HTTPS no Fiddler:<https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Habilite o Docker para usar um proxy por meio da interface do usuário do Docker:<https://docs.docker.com/docker-for-windows/#proxies>
- Não se esqueça de reverter ao concluir.  O Docker não funcionará com este habilitado e o Fiddler não está em execução.

#### <a name="windows-containers"></a>Contêineres do Windows

Configurar proxy do Docker para 127.0.0.1:8888

#### <a name="linux-containers"></a>Contêineres do Linux

Localize o IP do comutador virtual da VM do Docker:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Configure o proxy do Docker para a saída do comando anterior e a porta 8888 (por exemplo 10.0.75.1:8888)

## <a name="tasks"></a>Tarefas

- [Como fazer o cancelamento da execução do lote?](#how-do-i-batch-cancel-runs)
- [Como fazer incluir a pasta. git no comando AZ ACR Build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)

### <a name="how-do-i-batch-cancel-runs"></a>Como fazer o cancelamento da execução do lote?

Os comandos a seguir cancelam todas as tarefas em execução no registro especificado.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Como fazer incluir a pasta. git no comando AZ ACR Build?

Se você passar uma pasta de origem local para `az acr build` o comando, `.git` a pasta será excluída do pacote carregado por padrão. Você pode criar um `.dockerignore` arquivo com a configuração a seguir. Ele informa o comando para restaurar todos os arquivos `.git` em no pacote carregado. 

```
!.git/**
```

Essa configuração também se aplica ao `az acr run` comando.

## <a name="cicd-integration"></a>Integração de CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [Ações do GitHub](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais](container-registry-intro.md) sobre o registro de contêiner do Azure.