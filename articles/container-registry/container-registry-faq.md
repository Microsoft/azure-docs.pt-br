---
title: Perguntas frequentes
description: Respostas das perguntas frequentes relacionadas ao serviço de Registro de Contêiner do Azure
author: sajayantony
ms.topic: article
ms.date: 03/15/2021
ms.author: sajaya
ms.openlocfilehash: 5550c53289228f154fab485b4b7bbff17555aad7
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105045732"
---
# <a name="frequently-asked-questions-about-azure-container-registry"></a>Perguntas frequentes sobre o Registro de Contêiner do Azure

Este artigo aborda as perguntas frequentes e problemas conhecidos sobre o Registro de Contêiner do Azure.

Para obter diretrizes de solução de problemas do registro, consulte:
* [Solucionar problemas de logon do registro](container-registry-troubleshoot-login.md)
* [Solucionar problemas de rede com o registro](container-registry-troubleshoot-access.md)
* [Solucionar problemas de desempenho de registro](container-registry-troubleshoot-performance.md)

## <a name="resource-management"></a>Gerenciamento de recursos

- [Posso criar um registro de contêiner do Azure usando um modelo do Resource Manager?](#can-i-create-an-azure-container-registry-using-a-resource-manager-template)
- [Há uma verificação de vulnerabilidade de segurança para imagens no ACR?](#is-there-security-vulnerability-scanning-for-images-in-acr)
- [Como posso configurar o Kubernetes com o Registro de Contêiner do Azure?](#how-do-i-configure-kubernetes-with-azure-container-registry)
- [Como posso obter credenciais de administrador para um registro de contêiner?](#how-do-i-get-admin-credentials-for-a-container-registry)
- [Como posso obter credenciais de administrador em um modelo do Resource Manager?](#how-do-i-get-admin-credentials-in-a-resource-manager-template)
- [A exclusão da replicação falha com o status Proibido, embora a replicação seja excluída usando a CLI do Azure ou o Azure PowerShell](#delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell)
- [As regras de firewall são atualizadas com sucesso, mas não entram em vigor](#firewall-rules-are-updated-successfully-but-they-do-not-take-effect)

### <a name="can-i-create-an-azure-container-registry-using-a-resource-manager-template"></a>Posso criar um Registro de Contêiner do Azure usando um modelo do Resource Manager?

Sim. Este é [um](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry) de modelo que você pode usar para criar um registro.

### <a name="is-there-security-vulnerability-scanning-for-images-in-acr"></a>Há uma verificação de vulnerabilidade de segurança para imagens no ACR?

Sim. Confira a documentação da [Central de Segurança do Azure](../security-center/defender-for-container-registries-introduction.md), [Twistlock](https://www.twistlock.com/2016/11/07/twistlock-supports-azure-container-registry/) e [Aqua](https://blog.aquasec.com/image-vulnerability-scanning-in-azure-container-registry).

### <a name="how-do-i-configure-kubernetes-with-azure-container-registry"></a>Como posso configurar o Kubernetes com o Registro de Contêiner do Azure?

Confira a documentação do [Kubernetes](https://kubernetes.io/docs/user-guide/images/#using-azure-container-registry-acr) e as etapas do [Serviço de Kubernetes do Azure](../aks/cluster-container-registry-integration.md).

### <a name="how-do-i-get-admin-credentials-for-a-container-registry"></a>Como posso obter credenciais de administrador para um registro de contêiner?

> [!IMPORTANT]
> A conta de usuário administrador destina-se para um único usuário acessar o registro, principalmente para fins de teste. Não é recomendável compartilhar as credenciais da conta do administrador com vários usuários. Para cenários remotos, recomenda-se identidade individual para usuários e entidades de serviço. Confira [Visão geral da autenticação](container-registry-authentication.md).

Antes de obter as credenciais de administrador, verifique se o usuário administrador do registro está habilitado.

Para obter credenciais usando a CLI do Azure:

```azurecli
az acr credential show -n myRegistry
```

Usar o Microsoft Azure PowerShell:

```powershell
Invoke-AzureRmResourceAction -Action listCredentials -ResourceType Microsoft.ContainerRegistry/registries -ResourceGroupName myResourceGroup -ResourceName myRegistry
```

### <a name="how-do-i-get-admin-credentials-in-a-resource-manager-template"></a>Como posso obter credenciais de administrador em um modelo do Resource Manager?

> [!IMPORTANT]
> A conta de usuário administrador destina-se para um único usuário acessar o registro, principalmente para fins de teste. Não é recomendável compartilhar as credenciais da conta do administrador com vários usuários. Para cenários remotos, recomenda-se identidade individual para usuários e entidades de serviço. Confira [Visão geral da autenticação](container-registry-authentication.md).

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

### <a name="delete-of-replication-fails-with-forbidden-status-although-the-replication-gets-deleted-using-the-azure-cli-or-azure-powershell"></a>A exclusão da replicação falha com o status Proibido, embora a replicação seja excluída usando a CLI do Azure ou o Azure PowerShell

O erro é observado quando o usuário tem permissões em um registro, mas não tem permissões de nível de leitor na assinatura. Para resolver esse problema, atribua permissões de Leitor na assinatura ao usuário:


```azurecli  
az role assignment create --role "Reader" --assignee user@contoso.com --scope /subscriptions/<subscription_id> 
```

### <a name="firewall-rules-are-updated-successfully-but-they-do-not-take-effect"></a>As regras de firewall são atualizadas com sucesso, mas não entram em vigor

Demora um pouco para propagar as alterações das regras de firewall. Depois de alterar as configurações de firewall, aguarde alguns minutos antes de verificar essa alteração.


## <a name="registry-operations"></a>Operações de registro

- [Como posso acessar o Docker Registry HTTP API V2?](#how-do-i-access-docker-registry-http-api-v2)
- [Como posso excluir todos os manifestos que não são referenciados por tags em um repositório?](#how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository)
- [Por que o uso de cota de registro não é reduzido após a exclusão de imagens?](#why-does-the-registry-quota-usage-not-reduce-after-deleting-images)
- [Como posso validar as alterações de cota de armazenamento?](#how-do-i-validate-storage-quota-changes)
- [Como posso autenticar com o meu registro ao executar a CLI em um contêiner?](#how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container)
- [Como habilitar o TLS 1.2W?](#how-to-enable-tls-12)
- [O Registro de Contêiner do Azure é compatível com a Confiança em Conteúdo?](#does-azure-container-registry-support-content-trust)
- [Como posso conceder acesso para receber ou enviar imagens sem permissão para gerenciar o recurso de registro?](#how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource)
- [Como posso habilitar a quarentena automática de imagens de um registro?](#how-do-i-enable-automatic-image-quarantine-for-a-registry)
- [Como posso habilitar o acesso de pull anônimo?](#how-do-i-enable-anonymous-pull-access)
- [Como fazer enviar camadas não distribuíveis por push para um registro?](#how-do-i-push-non-distributable-layers-to-a-registry)

### <a name="how-do-i-access-docker-registry-http-api-v2"></a>Como posso acessar o Docker Registry HTTP API V2?

O ACR é compatível com o Docker Registry HTTP API V2. As APIs podem ser acessadas em `https://<your registry login server>/v2/`. Exemplo: `https://mycontainerregistry.azurecr.io/v2/`

### <a name="how-do-i-delete-all-manifests-that-are-not-referenced-by-any-tag-in-a-repository"></a>Como posso excluir todos os manifestos que não são referenciados por tags em um repositório?

Se você estiver no bash:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv  | xargs -I% az acr repository delete -n myRegistry -t myRepository@%
```

Para PowerShell:

```azurecli
az acr repository show-manifests -n myRegistry --repository myRepository --query "[?tags[0]==null].digest" -o tsv | %{ az acr repository delete -n myRegistry -t myRepository@$_ }
```

Observação: Você pode adicionar `-y` no comando Excluir para ignorar a confirmação.

Para obter mais informações, confira [Excluir imagens de contêiner no Registro de Contêiner do Azure](container-registry-delete.md).

### <a name="why-does-the-registry-quota-usage-not-reduce-after-deleting-images"></a>Por que o uso de cota de registro não é reduzido após a exclusão de imagens?

Essa situação pode ocorrer se as camadas subjacentes ainda estiverem sendo referenciadas por outras imagens de contêiner. Se você excluir uma imagem sem referências, o uso do registro será atualizado em alguns minutos.

### <a name="how-do-i-validate-storage-quota-changes"></a>Como posso validar as alterações de cota de armazenamento?

Crie uma imagem com uma camada de 1 GB, usando o seguinte arquivo do Docker. Isso garante que a imagem tenha uma camada que não é compartilhada por nenhuma outra imagem no registro.

```dockerfile
FROM alpine
RUN dd if=/dev/urandom of=1GB.bin  bs=32M  count=32
RUN ls -lh 1GB.bin
```

Crie e envie a imagem para o registro usando a CLI do Docker.

```bash
docker build -t myregistry.azurecr.io/1gb:latest .
docker push myregistry.azurecr.io/1gb:latest
```

Você pode ver que o uso do armazenamento aumentou no portal do Azure ou consultar o uso usando a CLI.

```azurecli
az acr show-usage -n myregistry
```

Exclua a imagem usando a CLI do Azure ou o portal do Azure e verifique o uso atualizado em alguns minutos.

```azurecli
az acr repository delete -n myregistry --image 1gb
```

### <a name="how-do-i-authenticate-with-my-registry-when-running-the-cli-in-a-container"></a>Como posso autenticar com o meu registro ao executar a CLI em um contêiner?

É necessário executar o contêiner da CLI do Azure montando o soquete do Docker:

```bash
docker run -it -v /var/run/docker.sock:/var/run/docker.sock azuresdk/azure-cli-python:dev
```

No contêiner, instale o `docker`:

```bash
apk --update add docker
```

Em seguida, autentique com seu registro:

```azurecli
az acr login -n MyRegistry
```

### <a name="how-to-enable-tls-12"></a>Como habilitar o TLS 1.2W?

Habilite o TLS 1.2 usando qualquer cliente recente do Docker (versão 18.03.0 e posterior). 

> [!IMPORTANT]
> A partir de 13 de janeiro de 2020, o Registro de Contêiner do Azure exigirá que todas as conexões seguras de servidores e aplicativos usem o TLS 1.2. O suporte para TLS 1.0 e 1.1 será desativado.

### <a name="does-azure-container-registry-support-content-trust"></a>O Registro de Contêiner do Azure é compatível com a Confiança em Conteúdo?

Sim, você pode usar imagens confiáveis no Registro de Contêiner do Azure, pois o [Docker Notary](https://docs.docker.com/notary/getting_started/) foi integrado e pode ser habilitado. Para obter detalhes, consulte [Confiança em Conteúdo no Registro de Contêiner do Azure](container-registry-content-trust.md).


####  <a name="where-is-the-file-for-the-thumbprint-located"></a>Onde está localizado o arquivo da impressão digital?

Em `~/.docker/trust/tuf/myregistry.azurecr.io/myrepository/metadata`:

* As chaves públicas e os certificados de todas as funções (exceto as funções de delegação) são armazenados no `root.json`.
* As chaves públicas e os certificados da função de delegação são armazenados no arquivo JSON da função pai (por exemplo `targets.json` para a função `targets/releases`).

Sugere-se verificar as chaves públicas e os certificados após a verificação geral do TUF realizada pelo cliente do Docker e do Notary.

### <a name="how-do-i-grant-access-to-pull-or-push-images-without-permission-to-manage-the-registry-resource"></a>Como posso conceder acesso para receber ou enviar imagens sem permissão para gerenciar o recurso de registro?

O ACR aceita [funções personalizadas](container-registry-roles.md) que fornecem diferentes níveis de permissões. Especificamente, as funções `AcrPull` e `AcrPush` permitem que os usuários recebam e/ou enviem imagens sem a permissão para gerenciar o recurso de registro no Azure.

* Portal do Azure: Registro -> Controle de Acesso (IAM) -> Adicionar (selecione `AcrPull` ou `AcrPush` para a função).
* CLI do Azure: Encontre a ID do recurso do registro executando o comando a seguir:

  ```azurecli
  az acr show -n myRegistry
  ```
  
  Em seguida, você pode atribuir a função `AcrPull` ou `AcrPush` a um usuário (o exemplo a seguir usa `AcrPull`):

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee user@example.com
  ```

  Ou, atribua a função a uma entidade de serviço identificada por sua ID de aplicativo:

  ```azurecli
  az role assignment create --scope resource_id --role AcrPull --assignee 00000000-0000-0000-0000-000000000000
  ```

O destinatário pode autenticar e acessar as imagens no registro.

* Para autenticar para um registro:
    
  ```azurecli
  az acr login -n myRegistry 
  ```

* Para listar os repositórios:

  ```azurecli
  az acr repository list -n myRegistry
  ```

* Para receber uma imagem:

  ```bash
  docker pull myregistry.azurecr.io/hello-world
  ```

Usando somente a função `AcrPull` ou `AcrPush`, o destinatário não tem permissão para gerenciar o recurso de registro no Azure. Por exemplo, `az acr list` ou `az acr show -n myRegistry` não mostra o registro.

### <a name="how-do-i-enable-automatic-image-quarantine-for-a-registry"></a>Como posso habilitar a quarentena automática de imagens de um registro?

Atualmente, a quarentena da imagem é uma versão prévia do recurso do ACR. Você pode habilitar o modo de quarentena de um registro para que apenas as imagens que foram aprovadas com sucesso na verificação de segurança fiquem visíveis para os usuários normais. Para obter detalhes, confira o [repositório GitHub do ACR](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).

### <a name="how-do-i-enable-anonymous-pull-access"></a>Como posso habilitar o acesso de pull anônimo?

A configuração de um registro de contêiner do Azure para acesso de pull anônimo (não autenticado) é atualmente um recurso de visualização, disponível nas [camadas de serviço](container-registry-skus.md)Standard e Premium. 

Para habilitar o acesso de pull anônimo, atualize um registro usando o CLI do Azure (versão 2.21.0 ou posterior) e passe o `--anonymous-pull-enabled` parâmetro para o comando [AZ ACR Update](/cli/azure/acr#az_acr_update) :

```azurecli
az acr update --name myregistry --anonymous-pull-enabled
``` 

Você pode desabilitar o acesso de pull anônimo a qualquer momento definindo `--anonymous-pull-enabled` como `false` .

> [!NOTE]
> * Antes de tentar uma operação de pull anônima, execute `docker logout` para garantir que você desmarque as credenciais existentes do Docker.
> * Somente as operações de plano de dados estão disponíveis para clientes não autenticados.
> * O registro pode limitar uma alta taxa de solicitações não autenticadas.

> [!WARNING]
> O acesso de pull anônimo atualmente se aplica a todos os repositórios no registro. Se você gerenciar o acesso ao repositório usando [tokens no escopo do repositório](container-registry-repository-scoped-permissions.md), lembre-se de que todos os usuários podem efetuar pull desses repositórios em um registro habilitado para pull anônimo. É recomendável excluir tokens quando o acesso de pull anônimo estiver habilitado.

### <a name="how-do-i-push-non-distributable-layers-to-a-registry"></a>Como fazer enviar camadas não distribuíveis por push para um registro?

Uma camada não distribuível em um manifesto contém um parâmetro de URL do qual o conteúdo pode ser obtido. Alguns casos de uso possíveis para habilitar Pushes de camada não distribuíveis são para registros restritos de rede, registros de ar-gapped com acesso restrito ou para registros sem conectividade com a Internet.

Por exemplo, se você tiver regras de NSG configuradas para que uma VM possa extrair imagens somente de seu registro de contêiner do Azure, o Docker efetuará pull de falhas para camadas externas/não distribuíveis. Por exemplo, uma imagem do Windows Server Core conteria referências de camada estrangeira ao registro de contêiner do Azure em seu manifesto e não conseguiria efetuar pull nesse cenário.

Para habilitar o envio por push de camadas não distribuíveis:

1. Edite o `daemon.json` arquivo, que está localizado em `/etc/docker/` hosts Linux e no `C:\ProgramData\docker\config\daemon.json` Windows Server. Supondo que o arquivo estava vazio anteriormente, adicione o seguinte conteúdo:

   ```json
   {
     "allow-nondistributable-artifacts": ["myregistry.azurecr.io"]
   }
   ```
   > [!NOTE]
   > O valor é uma matriz de endereços de registro, separados por vírgulas.

2. Salve e feche o arquivo.

3. Reinicie o Docker.

Quando você envia imagens por push para os registros na lista, suas camadas não distribuíveis são enviadas por push para o registro.

> [!WARNING]
> Os artefatos não distribuíveis normalmente têm restrições sobre como e onde eles podem ser distribuídos e compartilhados. Use esse recurso apenas para enviar artefatos por push a registros privados. Verifique se você está em conformidade com os termos que abrangem a redistribuição de artefatos não distribuíveis.

## <a name="diagnostics-and-health-checks"></a>Diagnóstico e verificações de integridade

- [Verificar integridade com `az acr check-health`](#check-health-with-az-acr-check-health)
- [docker pull falha com erro: net/http: solicitação cancelada enquanto aguarda conexão (Client.Timeout excedido enquanto aguarda cabeçalhos)](#docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers)
- [docker push obteve sucesso, mas o docker pull falha com erro: não autorizado: autenticação necessária](#docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required)
- [`az acr login` obteve sucesso, mas os comandos do docker falham com erro: não autorizado: autenticação necessária](#az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required)
- [Habilitar e obter os logs de depuração do daemon do docker](#enable-and-get-the-debug-logs-of-the-docker-daemon)    
- [As novas permissões de usuário podem não entrar em vigor imediatamente após a atualização](#new-user-permissions-may-not-be-effective-immediately-after-updating)
- [As informações de autenticação não são fornecidas no formato correto em chamadas diretas à API REST](#authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls)
- [Por que o portal do Azure não lista todos os meus repositórios ou tags?](#why-does-the-azure-portal-not-list-all-my-repositories-or-tags)
- [Por que a portal do Azure não busca os repositórios ou tags?](#why-does-the-azure-portal-fail-to-fetch-repositories-or-tags)
- [Por que minha solicitação de pull ou push falha com a operação não permitida?](#why-does-my-pull-or-push-request-fail-with-disallowed-operation)
- [Formato de repositório inválido ou sem suporte](#repository-format-is-invalid-or-unsupported)
- [Como posso coletar rastreamentos de http no Windows?](#how-do-i-collect-http-traces-on-windows)

### <a name="check-health-with-az-acr-check-health"></a>Verificar integridade com `az acr check-health`

Para solucionar problemas comuns de ambiente e registro, confira [Verificar a integridade de um registro de contêiner do Azure](container-registry-check-health.md).

### <a name="docker-pull-fails-with-error-nethttp-request-canceled-while-waiting-for-connection-clienttimeout-exceeded-while-awaiting-headers"></a>docker pull falha com erro: net/http: solicitação cancelada enquanto aguarda conexão (Client.Timeout excedido enquanto aguarda cabeçalhos)

 - Se esse erro for um problema transitório, a repetição obterá sucesso.
 - Se `docker pull` falhar continuamente, pode haver um problema com o daemon do Docker. Normalmente, o problema pode ser atenuado reiniciando o daemon do Docker. 
 - Se você continuar a ver esse problema após a reinicialização do daemon do Docker, o problema pode estar relacionado à conectividade de rede com o computador. Para verificar se a rede geral no computador está intata, execute o seguinte comando para testar a conectividade do ponto de extremidade. A versão mínima do `az acr` que contém esse comando de verificação de conectividade é 2.2.9. Atualize a CLI do Azure se você estiver usando uma versão mais antiga.
 
  ```azurecli
  az acr check-health -n myRegistry
  ```

 - Você sempre deve ter um mecanismo de repetição em todas as operações de cliente do Docker.

### <a name="docker-pull-is-slow"></a>O docker pull está lento
Use [essa](http://www.azurespeed.com/Azure/Download) ferramenta para testar a velocidade de download da rede do computador. Se a rede do computador estiver lenta, considere usar a VM do Azure na mesma região que o registro. Isso geralmente oferece uma velocidade de rede mais rápida.

### <a name="docker-push-is-slow"></a>O docker push está lento
Use [essa](http://www.azurespeed.com/Azure/Upload) ferramenta para testar a velocidade de carregamento da rede do computador. Se a rede do computador estiver lenta, considere usar a VM do Azure na mesma região que o registro. Isso geralmente oferece uma velocidade de rede mais rápida.

### <a name="docker-push-succeeds-but-docker-pull-fails-with-error-unauthorized-authentication-required"></a>Docker push obteve sucesso, mas o docker pull falha com erro: não autorizado: autenticação necessária

Esse erro pode ocorrer com a versão do Red Hat do daemon do Docker, onde `--signature-verification` está habilitado por padrão. Você pode verificar as opções do daemon do Docker para Red Hat Enterprise Linux (RHEL) ou Fedora, executando o seguinte comando:

```bash
grep OPTIONS /etc/sysconfig/docker
```

Por exemplo, o servidor Fedora 28 tem as seguintes opções do daemon do docker:

`OPTIONS='--selinux-enabled --log-driver=journald --live-restore'`

Com `--signature-verification=false` ausente, `docker pull` falha com um erro semelhante a:

```output
Trying to pull repository myregistry.azurecr.io/myimage ...
unauthorized: authentication required
```

Para resolver o erro:
1. Adicione a opção `--signature-verification=false` ao arquivo de configuração do daemon do Docker `/etc/sysconfig/docker`. Por exemplo:
   
   `OPTIONS='--selinux-enabled --log-driver=journald --live-restore --signature-verification=false'`
   
2. Reinicie o serviço de daemon do Docker executando o seguinte comando:
   
   ```bash
   sudo systemctl restart docker.service
   ```

Os detalhes de `--signature-verification` podem ser encontrados executando `man dockerd`.

### <a name="az-acr-login-succeeds-but-docker-fails-with-error-unauthorized-authentication-required"></a>az acr login obteve sucesso, mas o docker falha com erro: não autorizado: autenticação necessária

Use uma URL do servidor em minúsculas, por exemplo, `docker push myregistry.azurecr.io/myimage:latest`, mesmo se o nome do recurso de registro estiver em maiúsculas ou em maiúsculas e minúsculas, como `myRegistry`.

### <a name="enable-and-get-the-debug-logs-of-the-docker-daemon"></a>Habilitar e obter os logs de depuração do daemon do Docker    

Inicie `dockerd` com a opção `debug`. Primeiro, crie o arquivo de configuração do daemon do Docker (`/etc/docker/daemon.json`), se não existir, e adicione a opção `debug`:

```json
{    
    "debug": true    
}
```

Em seguida, reinicie o daemon. Por exemplo, com o Ubuntu 14.04:

```bash
sudo service docker restart
```

Os detalhes podem ser encontrados na [documentação do Docker](https://docs.docker.com/engine/admin/#enable-debugging).    

 * Os logs podem ser gerados em locais diferentes, dependendo do sistema. Por exemplo, para o Ubuntu 14.04, é `/var/log/upstart/docker.log`.    
Confira [documentação do Docker](https://docs.docker.com/engine/admin/#read-the-logs) para obter detalhes.    

 * Para o Docker for Windows, os logs são gerados em %LOCALAPPDATA%/docker/. No entanto, talvez ainda não contenha todas as informações de depuração.    

   Para acessar o log completo do daemon, algumas etapas adicionais podem ser necessárias:

    ```console
    docker run --privileged -it --rm -v /var/run/docker.sock:/var/run/docker.sock -v /usr/local/bin/docker:/usr/local/bin/docker alpine sh

    docker run --net=host --ipc=host --uts=host --pid=host -it --security-opt=seccomp=unconfined --privileged --rm -v /:/host alpine /bin/sh
    chroot /host
    ```
    Agora você tem acesso a todos os arquivos da VM em execução `dockerd`. O log está em `/var/log/docker.log`.

### <a name="new-user-permissions-may-not-be-effective-immediately-after-updating"></a>As novas permissões de usuário podem não entrar em vigor imediatamente após a atualização

Quando você concede novas permissões (novas funções) a uma entidade de serviço, a alteração pode não entrar em vigor imediatamente. Há dois motivos possíveis:

* Atraso na atribuição de função do Azure Active Directory. Normalmente é rápido, mas pode levar minutos devido ao atraso na propagação.
* Atraso na permissão do servidor de token do ACR. Pode demorar até 10 minutos. Para atenuar, você pode `docker logout` e, em seguida, autenticar novamente com o mesmo usuário após 1 minuto:

  ```bash
  docker logout myregistry.azurecr.io
  docker login myregistry.azurecr.io
  ```

Atualmente, o ACR não dá suporte à exclusão da replicação inicial pelos usuários. A solução alternativa é incluir a replicação inicial criada no modelo, mas pular sua criação adicionando `"condition": false`, como mostrado abaixo:

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

### <a name="authentication-information-is-not-given-in-the-correct-format-on-direct-rest-api-calls"></a>As informações de autenticação não são fornecidas no formato correto em chamadas diretas à API REST

Você pode encontrar um erro `InvalidAuthenticationInfo`, especialmente usando a ferramenta `curl` com a opção `-L`, `--location` (para seguir os redirecionamentos).
Por exemplo, buscar o blob usando `curl` com a opção `-L` e a autenticação básica:

```bash
curl -L -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest
```

pode resultar na resposta a seguir:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Error><Code>InvalidAuthenticationInfo</Code><Message>Authentication information is not given in the correct format. Check the value of Authorization header.
RequestId:00000000-0000-0000-0000-000000000000
Time:2019-01-01T00:00:00.0000000Z</Message></Error>
```

A causa raiz é que algumas implementações de `curl` seguem redirecionamentos com cabeçalhos da solicitação original.

Para resolver o problema, você precisa seguir os redirecionamentos manualmente, sem os cabeçalhos. Imprima os cabeçalhos de resposta com a opção `-D -` de `curl` e, em seguida, extraia: o cabeçalho `Location`:

```bash
redirect_url=$(curl -s -D - -H "Authorization: basic $credential" https://$registry.azurecr.io/v2/$repository/blobs/$digest | grep "^Location: " | cut -d " " -f2 | tr -d '\r')
curl $redirect_url
```

### <a name="why-does-the-azure-portal-not-list-all-my-repositories-or-tags"></a>Por que o portal do Azure não lista todos os meus repositórios ou tags? 

Se você estiver usando o navegador Microsoft Edge/IE, poderá ver no máximo 100 repositórios ou tags. Se o registro tiver mais de 100 repositórios ou tags, recomendamos que você use o navegador Firefox ou Chrome para listar todos eles.

### <a name="why-does-the-azure-portal-fail-to-fetch-repositories-or-tags"></a>Por que a portal do Azure não busca os repositórios ou tags?

O navegador pode não enviar a solicitação para buscar repositórios ou tags no servidor. Pode haver vários motivos, como:

* Falta de conectividade de rede
* Firewall
* Bloqueadores de AD
* Erros DNS

Entre em contato com o administrador de rede ou verifique a configuração de rede e a conectividade. Tente executar `az acr check-health -n yourRegistry` usando a CLI do Azure para verificar se o ambiente pode se conectar ao Registro de Contêiner. Além disso, você também pode tentar uma sessão privada ou anônima no navegador para evitar cache ou cookies antigos do navegador.

### <a name="why-does-my-pull-or-push-request-fail-with-disallowed-operation"></a>Por que minha solicitação de pull ou push falha com a operação não permitida?

Estes são alguns cenários em que as operações podem não ser permitidas:
* Os registros clássicos não são mais compatíveis. Atualize para uma [camada de serviço](./container-registry-skus.md) com suporte usando o [az acr update](/cli/azure/acr#az-acr-update) ou o portal do Azure.
* A imagem ou o repositório pode estar bloqueado para que não possa ser excluído ou atualizado. Você pode usar o comando [az acr show repository](./container-registry-image-lock.md) para exibir os atributos atuais.
* Algumas operações não serão permitidas se a imagem estiver em quarentena. Saiba mais sobre a [quarentena](https://github.com/Azure/acr/tree/master/docs/preview/quarantine).
* O registro pode ter atingido seu [limite de armazenamento](container-registry-skus.md#service-tier-features-and-limits).

### <a name="repository-format-is-invalid-or-unsupported"></a>Formato de repositório inválido ou sem suporte

Se você vir um erro como "formato de repositório sem suporte", "formato inválido" ou "os dados solicitados não existem" ao especificar um nome de repositório em operações de repositório, verifique a ortografia e o caso do nome. Os nomes de repositório válidos só podem incluir caracteres alfanuméricos minúsculos, pontos, traços, sublinhados e barras invertidas. 

Para obter as regras de nomenclatura completas do repositório, consulte a [especificação de distribuição da iniciativa Open container](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

### <a name="how-do-i-collect-http-traces-on-windows"></a>Como posso coletar rastreamentos de http no Windows?

#### <a name="prerequisites"></a>Pré-requisitos

- Habilite a descriptografia de HTTPS no Fiddler:  <https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS>
- Habilite o Docker para usar um proxy por meio da interface do usuário do Docker: <https://docs.docker.com/docker-for-windows/#proxies>
- Não se esqueça de reverter ao concluir.  O Docker não funciona se esse recurso estiver habilitado e se o Fiddler não estiver em execução.

#### <a name="windows-containers"></a>Contêineres do Windows

Configure o proxy do Docker para 127.0.0.1:8888

#### <a name="linux-containers"></a>Contêineres do Linux

Encontre o IP do comutador virtual da VM do Docker:

```powershell
(Get-NetIPAddress -InterfaceAlias "*Docker*" -AddressFamily IPv4).IPAddress
```

Configure o proxy do Docker para a saída do comando anterior e a porta 8888 (por exemplo 10.0.75.1:8888)

## <a name="tasks"></a>Tarefas

- [Como posso cancelar execuções em lotes?](#how-do-i-batch-cancel-runs)
- [Como posso incluir a pasta. git no comando az acr build?](#how-do-i-include-the-git-folder-in-az-acr-build-command)
- [As tarefas dão suporte a GitLab para gatilhos de origem?](#does-tasks-support-gitlab-for-source-triggers)
- [Para qual serviço de gerenciamento de repositório git as tarefas dão suporte?](#what-git-repository-management-service-does-tasks-support)

### <a name="how-do-i-batch-cancel-runs"></a>Como posso cancelar execuções em lote?

Os comandos a seguir cancelam todas as tarefas em execução no registro especificado.

```azurecli
az acr task list-runs -r $myregistry --run-status Running --query '[].runId' -o tsv \
| xargs -I% az acr task cancel-run -r $myregistry --run-id %
```

### <a name="how-do-i-include-the-git-folder-in-az-acr-build-command"></a>Como posso incluir a pasta. git no comando az acr build?

Se você passar uma pasta de origem local para o comando `az acr build`, a pasta `.git` será excluída do pacote carregado por padrão. Você pode criar um arquivo `.dockerignore` com a configuração a seguir. Ela informa o comando para restaurar todos os arquivos em `.git` no pacote carregado. 

`!.git/**`

Essa configuração também se aplica ao comando `az acr run`.

### <a name="does-tasks-support-gitlab-for-source-triggers"></a>As tarefas dão suporte a GitLab para gatilhos de origem?

No momento, não damos suporte a GitLab para gatilhos de origem.

### <a name="what-git-repository-management-service-does-tasks-support"></a>Para qual serviço de gerenciamento de repositório git as tarefas dão suporte?

| Serviço git | Contexto de origem | Compilação manual | Compilação automática por meio do gatilho de confirmação |
|---|---|---|---|
| GitHub | `https://github.com/user/myapp-repo.git#mybranch:myfolder` | Sim | Sim |
| Azure Repos | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` | Sim | Sim |
| GitLab | `https://gitlab.com/user/myapp-repo.git#mybranch:myfolder` | Sim | Não |
| BitBucket | `https://user@bitbucket.org/user/mayapp-repo.git#mybranch:myfolder` | Sim | Não |

## <a name="run-error-message-troubleshooting"></a>Executar solução de problemas de mensagem de erro

| Mensagem de erro | Guia de Solução de Problemas |
|---|---|
|Nenhum acesso foi configurado para a VM, portanto, nenhuma assinatura foi encontrada|Isso pode acontecer se você estiver usando `az login --identity` na tarefa do ACR. Esse é um erro transitório e ocorre quando a atribuição de função da identidade gerenciada não foi propagada. Aguardando alguns segundos antes de tentar novamente.|

## <a name="cicd-integration"></a>Integração de CI/CD

- [CircleCI](https://github.com/Azure/acr/blob/master/docs/integration/CircleCI.md)
- [GitHub Actions](https://github.com/Azure/acr/blob/master/docs/integration/github-actions/github-actions.md)

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais](container-registry-intro.md) sobre o Registro de Contêiner do Azure.
