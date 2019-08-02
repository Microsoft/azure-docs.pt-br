---
title: Confiança de conteúdo no Registro de Contêiner do Azure
description: Saiba como habilitar a confiança de conteúdo do registro de contêiner do Azure e enviar e efetuar pull dessas imagens.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: danlep
ms.openlocfilehash: d2132f060076aefe6ae0eccb6d5300c78c96ece5
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356535"
---
# <a name="content-trust-in-azure-container-registry"></a>Confiança de conteúdo no Registro de Contêiner do Azure

O Registro de Contêiner do Azure implementa o modelo de [relação de confiança do conteúdo][docker-content-trust] do Docker, permitindo push e pull de imagens assinadas. Este artigo mostra como habilitar a relação de confiança do conteúdo em seus registros de contêiner.

> [!NOTE]
> A relação de confiança é um recurso do [SKU Premium](container-registry-skus.md) do Registro de Contêiner do Azure.

## <a name="how-content-trust-works"></a>Como a confiança de conteúdo funciona

Um fator importante para todos os sistemas distribuídos que foram criados tendo a segurança como foco, é verificar a *origem* e a *integridade* dos dados que entram no sistema. É essencial que os consumidores dos dados consigam verificar o editor (fonte) dos dados e garantir que ele não tenha sido modificado após a publicação (integridade). 

Por ser um editor de imagens, a confiança de conteúdo permite que você **assine** as imagens que serão enviadas ao registro. Os consumidores de suas imagens (pessoas ou sistemas que efetuam pull das imagens de seu registro) podem configurar seus clientes para enviar *apenas* imagens assinadas. Quando um consumidor de imagens efetua pull de uma imagem assinada, seu cliente do Docker verifica a integridade da imagem. Nesse modelo, os consumidores têm a garantia de que as imagens assinadas em seu registro foram de fato publicadas por você e que não foram modificadas desde sua publicação.

### <a name="trusted-images"></a>Imagens confiáveis

A confiança de conteúdo funciona com as **marcas** em um repositório. Os repositórios de imagens podem conter imagens com marcas assinadas e não assinadas. Por exemplo, você pode assinar as imagens `myimage:stable` e `myimage:latest` apenas, mas não pode assinar `myimage:dev`.

### <a name="signing-keys"></a>Chaves de assinatura

A confiança de conteúdo é gerenciada usando um conjunto de chaves de assinatura criptográficas. Essas chaves são associadas a um repositório específico em um registro. Existem vários tipos de chaves de assinatura que os clientes do Docker e seu registro usam para gerenciar a confiança das marcas em um repositório. Ao ativar a confiança de conteúdo e integrá-la ao pipeline de publicação e consumo de contêiner, você deve gerenciar essas chaves com cuidado. Para mais informações, confira [Gerenciamento de chaves](#key-management) mais adiante neste artigo e [Gerenciar chaves para a confiança de conteúdo][docker-manage-keys] na documentação do Docker.

> [!TIP]
> Essa foi uma visão geral de alto nível do modelo de confiança de conteúdo do Docker. Para ver uma discussão detalhada sobre a confiança de conteúdo, confira [Confiança de conteúdo no Docker][docker-content-trust].

## <a name="enable-registry-content-trust"></a>Habilitar a confiança de conteúdo do registro

A primeira etapa é habilitar a confiança de conteúdo no nível do registro. Depois de habilitar a confiança de conteúdo, os clientes (usuários ou serviços) poderão enviar imagens assinadas para o registro. A ativação da confiança de conteúdo em seu registro não restringirá o uso do registro apenas a consumidores que têm a confiança de conteúdo ativada. Os consumidores que não têm a confiança de conteúdo ativada poderão continuar a usar o registro normalmente. No entanto, os consumidores que ativaram a confiança de conteúdo em seus clientes verão *apenas* imagens assinadas em seu registro.

Para habilitar a confiança de conteúdo para o registro, primeiro, navegue até o registro no portal do Azure. Em **Políticas**, selecione **Confiança de Conteúdo** > **Habilitada** > **Salvar**.

![Habilitar a confiança de conteúdo para um registro no portal do Azure][content-trust-01-portal]

## <a name="enable-client-content-trust"></a>Habilitar a confiança de conteúdo do cliente

Para trabalhar com imagens confiáveis, os editores e consumidores de imagens precisam ativar a confiança de conteúdo para seus clientes do Docker. Como editor, você pode assinar as imagens que envia ao registro habilitado para a confiança de conteúdo. Como consumidor, a ativação da confiança de conteúdo limita sua exibição de um registro apenas a imagens assinadas. A confiança de conteúdo é desabilitada por padrão nos clientes do Docker, mas você pode ativá-la por sessão de shell ou por comando.

Para habilitar a confiança de conteúdo para uma sessão do shell, defina a variável de ambiente `DOCKER_CONTENT_TRUST` como **1**. Por exemplo, no shell Bash:

```bash
# Enable content trust for shell session
export DOCKER_CONTENT_TRUST=1
```

Se, em vez disso, você quiser ativar ou desativar a confiança de conteúdo para um único comando, vários comandos do Docker suportam o argumento `--disable-content-trust`. Para habilitar a confiança de conteúdo de um único comando:

```bash
# Enable content trust for single command
docker build --disable-content-trust=false -t myacr.azurecr.io/myimage:v1 .
```

Se você tiver habilitado a confiança de conteúdo para a sessão do shell e quiser desabilitá-la para um único comando:

```bash
# Disable content trust for single command
docker build --disable-content-trust -t myacr.azurecr.io/myimage:v1 .
```

## <a name="grant-image-signing-permissions"></a>Conceder permissões de assinatura de imagens

Somente os usuários ou sistemas com permissão podem enviar imagens confiáveis para seu registro. Para conceder permissão de envio de imagens confiáveis a um usuário (ou a um sistema usando uma entidade de serviço), conceda às identidades do Azure Active Directory a função `AcrImageSigner`. Isso é um acréscimo à função `AcrPush` (ou equivalente) necessária para enviar imagens por push para o registro. Para obter detalhes, confira [Funções e permissões do Registro de Contêiner do Azure](container-registry-roles.md).

Detalhes para a concessão da função `AcrImageSigner` no portal do Azure e na CLI do Azure são os seguintes.

### <a name="azure-portal"></a>Portal do Azure

Navegue até seu registro no portal do Azure e escolha **Controle de Acesso (IAM)**  > **Adicionar atribuição de função**. Em **Adicionar atribuição de função**, escolha `AcrImageSigner` em **Função** e **Selecionar** um ou mais usuários ou entidades de serviço e, em seguida, **Salvar**.

Neste exemplo, duas entidades receberam a função `AcrImageSigner`: uma entidade de serviço chamada "service-principal" e um usuário chamado "Usuário do Azure".

![Habilitar a confiança de conteúdo para um registro no portal do Azure][content-trust-02-portal]

### <a name="azure-cli"></a>CLI do Azure

Para conceder permissões de assinatura a um usuário com a CLI do Azure, atribua a função `AcrImageSigner` ao usuário no escopo do registro. O formato do comando é:

```azurecli
az role assignment create --scope <registry ID> --role AcrImageSigner --assignee <user name>
```

Por exemplo, para conceder a você mesmo a função, execute os seguintes comandos em uma sessão autenticada da CLI do Azure. Modifique o valor `REGISTRY` para refletir o nome do registro de contêiner do Azure.

```bash
# Grant signing permissions to authenticated Azure CLI user
REGISTRY=myregistry
USER=$(az account show --query user.name --output tsv)
REGISTRY_ID=$(az acr show --name $REGISTRY --query id --output tsv)

az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee $USER
```

Você também pode conceder a uma [entidade de serviço](container-registry-auth-service-principal.md) os direitos de envio de imagens confiáveis para o registro. O uso de uma entidade de serviço é útil para sistemas de compilação e outros sistemas autônomos que precisam enviar imagens confiáveis para seu registro. O formato é semelhante a conceder uma permissão de usuário, mas especifica a ID de uma entidade de serviço com o valor `--assignee`.

```azurecli
az role assignment create --scope $REGISTRY_ID --role AcrImageSigner --assignee <service principal ID>
```

A `<service principal ID>` pode ser a **appId**, **objectId** da entidade de serviço ou um de seus **servicePrincipalNames**. Para mais informações sobre como trabalhar com entidades de serviço e o Registro de Contêiner do Azure, confira [Autenticação do Registro de Contêiner do Azure com entidades de serviço](container-registry-auth-service-principal.md).

Depois de quaisquer alterações de função, execute `az acr login` para atualizar o token de identidade local para a CLI do Azure para que as novas funções entrem em vigor.

## <a name="push-a-trusted-image"></a>Enviar uma imagem confiável por push

Para enviar uma marca de imagem confiável por push ao registro de contêiner, ative a confiança de conteúdo e envie a imagem com `docker push`. Ao enviar uma marca assinada pela primeira vez, você é solicitado a criar uma frase secreta para uma chave de assinatura raiz e uma chave de assinatura do repositório. As chaves raiz e de repositório são geradas e armazenadas localmente em seu computador.

```console
$ docker push myregistry.azurecr.io/myimage:v1
The push refers to repository [myregistry.azurecr.io/myimage]
ee83fc5847cb: Pushed
v1: digest: sha256:aca41a608e5eb015f1ec6755f490f3be26b48010b178e78c00eac21ffbe246f1 size: 524
Signing and pushing trust metadata
You are about to create a new root signing key passphrase. This passphrase
will be used to protect the most sensitive key in your signing system. Please
choose a long, complex passphrase and be careful to keep the password and the
key file itself secure and backed up. It is highly recommended that you use a
password manager to generate the passphrase and keep it safe. There will be no
way to recover this key. You can find the key in your config directory.
Enter passphrase for new root key with ID 4c6c56a:
Repeat passphrase for new root key with ID 4c6c56a:
Enter passphrase for new repository key with ID bcd6d98:
Repeat passphrase for new repository key with ID bcd6d98:
Finished initializing "myregistry.azurecr.io/myimage"
Successfully signed myregistry.azurecr.io/myimage:v1
```

Após o primeiro `docker push` com a confiança de conteúdo ativada, o cliente do Docker usará a mesma chave raiz para os envios subsequentes. A cada envio subsequente para o mesmo repositório, você será solicitado a fornecer apenas a chave do repositório. Todas as vezes que você envia uma imagem confiável a um novo repositório, você é solicitado a fornecer uma frase secreta para uma nova chave de repositório.

## <a name="pull-a-trusted-image"></a>Efetuar pull de uma imagem confiável

Para efetuar pull de uma imagem confiável, habilite a confiança de conteúdo e execute o comando `docker pull` normalmente. Para efetuar pull de imagens confiáveis, a função `AcrPull` é suficiente para usuários normais. Não há necessidade de funções adicionais como a função `AcrImageSigner`. Os consumidores que têm a confiança de conteúdo habilitada só podem efetuar pull de imagens com marcas assinadas. Veja um exemplo da realização de um pull de uma marca assinada:

```console
$ docker pull myregistry.azurecr.io/myimage:signed
Pull (1 of 1): myregistry.azurecr.io/myimage:signed@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b: Pulling from myimage
8e3ba11ec2a2: Pull complete
Digest: sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Status: Downloaded newer image for myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b
Tagging myregistry.azurecr.io/myimage@sha256:0800d17e37fb4f8194495b1a188f121e5b54efb52b5d93dc9e0ed97fce49564b as myregistry.azurecr.io/myimage:signed
```

Se um cliente que tenha a confiança de conteúdo habilitada tentar efetuar pull de uma marca não assinada, a operação falhará:

```console
$ docker pull myregistry.azurecr.io/myimage:unsigned
No valid trust data for unsigned
```

### <a name="behind-the-scenes"></a>Nos bastidores

Quando você executa o `docker pull`, o cliente do Docker usa a mesma biblioteca da [CLI do Notary][docker-notary-cli] para solicitar o mapeamento resumido de marca para SHA-256 para a marca que você está efetuando pull. Após validar as assinaturas nos dados de confiança, o cliente instrui o Docker Engine a "efetuar um pull por resumo". Durante a realização do pull, o mecanismo usa a soma de verificação de SHA-256 como um endereço de conteúdo para solicitar e validar o manifesto da imagem do registro do contêiner do Azure.

## <a name="key-management"></a>Gerenciamento de chaves

Conforme declarado na saída `docker push`, ao enviar sua primeira imagem confiável, a chave raiz será a mais confidencial. Lembre-se de fazer backup da chave raiz e armazená-la em um local seguro. Por padrão, o cliente do Docker armazena as chaves de assinatura no seguinte diretório:

```sh
~/.docker/trust/private
```

Faça backup de suas chaves raiz e de repositório compactando-as em um arquivo morto e armazenando-o em um local seguro. Por exemplo, no Bash:

```bash
umask 077; tar -zcvf docker_private_keys_backup.tar.gz ~/.docker/trust/private; umask 022
```

Juntamente com as chaves raiz e de repositório geradas localmente, várias outras são geradas e armazenadas pelo Registro de Contêiner do Azure quando você envia uma imagem confiável. Para ter acesso a uma discussão detalhada sobre as várias chaves na implementação da confiança de conteúdo do Docker, incluindo orientação adicional de gerenciamento, confira [Gerenciar chaves para a confiança de conteúdo][docker-manage-keys] na documentação do Docker.

### <a name="lost-root-key"></a>Chave raiz perdida

Se perder o acesso à sua chave raiz, você perderá o acesso às marcas assinadas em todos os repositórios cujas marcas foram assinadas com essa chave. O Registro de Contêiner do Azure não consegue restaurar o acesso às marcas de imagem assinada com uma chave raiz perdida. Para remover todos os dados de confiança (assinaturas) do registro, primeiro, desabilite e habilite novamente a confiança de conteúdo do registro.

> [!WARNING]
> O ato de desabilitar e reabilitar o conteúdo de confiança no registro **excluirá todos os dados de confiança de todas as marcas assinadas em todos os repositórios do registro**. Essa ação é irreversível. O Registro de Contêiner do Azure não consegue recuperar dados de confiança excluídos. Desabilitar a confiança de conteúdo não excluirá as próprias imagens.

Para desabilitar a confiança de conteúdo do registro, primeiro, navegue até o registro no portal do Azure. Em **Políticas**, selecione **Confiança de Conteúdo** > **Desabilitada** > **Salvar**. Você já foi alertado sobre a perda de todas as assinaturas no registro. Escolha **OK** para excluir permanentemente todas as assinaturas do registro.

![Desabilitar a confiança de conteúdo de um registro no portal do Azure][content-trust-03-portal]

## <a name="next-steps"></a>Próximas etapas

* Confira [Confiança de conteúdo no Docker][docker-content-trust] para ver mais informações sobre a confiança de conteúdo. Apesar de vários pontos importantes terem sido abordados neste artigo, a confiança de conteúdo é um tópico amplo e será abordado com mais detalhes na documentação do Docker.

* Consulte a documentação do [Azure Pipelines](/azure/devops/pipelines/build/content-trust) para obter um exemplo de como usar a confiança de conteúdo ao criar e efetuar push de uma imagem do Docker.

<!-- IMAGES> -->
[content-trust-01-portal]: ./media/container-registry-content-trust/content-trust-01-portal.png
[content-trust-02-portal]: ./media/container-registry-content-trust/content-trust-02-portal.png
[content-trust-03-portal]: ./media/container-registry-content-trust/content-trust-03-portal.png

<!-- LINKS - external -->
[docker-content-trust]: https://docs.docker.com/engine/security/trust/content_trust
[docker-manage-keys]: https://docs.docker.com/engine/security/trust/trust_key_mng/
[docker-notary-cli]: https://docs.docker.com/notary/getting_started/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
