---
title: Acessar o registro restrito à rede usando o serviço confiável do Azure
description: Habilitar uma instância de serviço do Azure confiável para acessar com segurança um registro de contêiner de rede restrita para extrair ou enviar imagens por push
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 2e6b6ee3736f98f53ebb0aa43d707d42ba4cc058
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527265"
---
# <a name="allow-trusted-services-to-securely-access-a-network-restricted-container-registry-preview"></a>Permitir que os serviços confiáveis acessem com segurança um registro de contêiner de rede restrita (versão prévia)

O registro de contêiner do Azure pode permitir a seleção de serviços confiáveis do Azure para acessar um registro configurado com regras de acesso à rede. Quando os serviços confiáveis são permitidos, uma instância de serviço confiável pode ignorar com segurança as regras de rede do registro e executar operações, como imagens de pull ou push. A identidade gerenciada da instância de serviço é usada para acesso e deve ser atribuída a uma função do Azure e autenticar com o registro.

Use o Azure Cloud Shell ou uma instalação local do CLI do Azure para executar os exemplos de comando neste artigo. Se você quiser usá-lo localmente, a versão 2,18 ou posterior será necessária. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

Permitir o acesso ao registro por serviços confiáveis do Azure é um recurso de **Visualização** .

## <a name="limitations"></a>Limitações

* Você deve usar uma identidade gerenciada atribuída pelo sistema habilitada em um [serviço confiável](#trusted-services) para acessar um registro de contêiner de rede restrita. Atualmente, não há suporte para identidades gerenciadas atribuídas pelo usuário.
* A permissão de serviços confiáveis não se aplica a um registro de contêiner configurado com um [ponto de extremidade de serviço](container-registry-vnet.md). O recurso afeta apenas registros que são restritos com um [ponto de extremidade privado](container-registry-private-link.md) ou que têm [regras de acesso de IP público](container-registry-access-selected-networks.md) aplicadas. 

## <a name="about-trusted-services"></a>Sobre os serviços confiáveis

O registro de contêiner do Azure tem um modelo de segurança em camadas que dá suporte a várias configurações de rede que restringem o acesso a um registro, incluindo:

* [Ponto de extremidade privado com o link privado do Azure](container-registry-private-link.md). Quando configurado, o ponto de extremidade privado de um registro é acessível somente para recursos dentro da rede virtual, usando endereços IP privados.  
* [Regras de firewall do registro](container-registry-access-selected-networks.md), que permitem o acesso ao ponto de extremidade público do registro somente de endereços IP públicos ou intervalos de endereços específicos. Você também pode configurar o firewall para bloquear todo o acesso ao ponto de extremidade público ao usar pontos de extremidades privados.

Quando implantado em uma rede virtual ou configurado com regras de firewall, um registro nega acesso por padrão a usuários ou serviços de fora dessas fontes. 

Vários serviços multilocatários do Azure operam de redes que não podem ser incluídas nessas configurações de rede do registro, impedindo-as de extrair ou enviar imagens por push ao registro. Ao designar determinadas instâncias de serviço como "confiáveis", um proprietário do registro pode permitir a seleção de recursos do Azure para ignorar com segurança as configurações de rede do registro para efetuar pull ou enviar imagens por push. 

### <a name="trusted-services"></a>Serviços confiáveis

As instâncias dos serviços a seguir podem acessar um registro de contêiner de rede restrita se a configuração **permitir serviços confiáveis** do registro estiver habilitada (o padrão). Mais serviços serão adicionados ao longo do tempo.

|Serviço confiável  |Cenários de uso com suporte  |
|---------|---------|
|Tarefas do ACR     | [Acessar um registro diferente de uma tarefa ACR](container-registry-tasks-cross-registry-authentication.md)       |
|Machine Learning | [Implantar](../machine-learning/how-to-deploy-custom-docker-image.md) ou [treinar](../machine-learning/how-to-train-with-custom-image.md) um modelo em um espaço de trabalho Machine Learning usando uma imagem de contêiner personalizada do Docker |
|Registro de Contêiner do Azure | [Importar imagens de outro registro de contêiner do Azure](container-registry-import-images.md#import-from-an-azure-container-registry-in-the-same-ad-tenant) | 

> [!NOTE]
> Atualmente, habilitar a configuração permitir serviços confiáveis não permite instâncias de outros serviços gerenciados do Azure, incluindo o serviço de aplicativo, as instâncias de contêiner do Azure e a central de segurança do Azure para acessar um registro de contêiner de rede restrita.

## <a name="allow-trusted-services---cli"></a>Permitir serviços confiáveis-CLI

Por padrão, a configuração permitir serviços confiáveis está habilitada em um novo registro de contêiner do Azure. Desabilite ou habilite a configuração executando o comando [AZ ACR Update](/cli/azure/acr#az-acr-update) .

Para desabilitar:

```azurecli
az acr update --name myregistry --allow-trusted-services false
```

Para habilitar a configuração em um registro existente ou em um registro em que ele já está desabilitado:

```azurecli
az acr update --name myregistry --allow-trusted-services true
```

## <a name="allow-trusted-services---portal"></a>Permitir serviços confiáveis-Portal

Por padrão, a configuração permitir serviços confiáveis está habilitada em um novo registro de contêiner do Azure. 

Para desabilitar ou reabilitar a configuração no Portal:

1. No portal, navegue até o registro de contêiner.
1. Em **Configurações**, selecione **Rede**. 
1. Em **permitir acesso à rede pública**, selecione **redes selecionadas** ou **desabilitada**.
1. Realize um dos seguintes procedimentos:
    * Para desabilitar o acesso por serviços confiáveis, em **exceção de firewall**, desmarque **permitir que os serviços confiáveis da Microsoft acessem este registro de contêiner**. 
    * Para permitir serviços confiáveis, em **exceção de firewall**, marque **permitir que serviços da Microsoft confiáveis acessem este registro de contêiner**.
1. Selecione **Salvar**.

## <a name="trusted-services-workflow"></a>Fluxo de trabalho de serviços confiáveis

Aqui está um fluxo de trabalho típico para permitir que uma instância de um serviço confiável acesse um registro de contêiner de rede restrita.

1. Habilite uma [identidade gerenciada atribuída pelo sistema para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) em uma instância de um dos [serviços confiáveis](#trusted-services) para o registro de contêiner do Azure.
1. Atribua a identidade de uma [função do Azure](container-registry-roles.md) ao registro. Por exemplo, atribua a função ACRPull para efetuar pull de imagens de contêiner.
1. No registro de rede restrita, defina a configuração para permitir o acesso por serviços confiáveis.
1. Use as credenciais da identidade para autenticar com o registro de rede restrita. 
1. Efetuar pull de imagens do registro ou executar outras operações permitidas pela função.

### <a name="example-acr-tasks"></a>Exemplo: tarefas ACR

O exemplo a seguir demonstra o uso de tarefas ACR como um serviço confiável. Consulte [autenticação entre registros em uma tarefa ACR usando uma identidade gerenciada pelo Azure](container-registry-tasks-cross-registry-authentication.md) para obter detalhes da tarefa.

1. Crie ou atualize um registro de contêiner do Azure e [envie por push uma imagem de base de exemplo](container-registry-tasks-cross-registry-authentication.md#prepare-base-registry) para o registro. Esse registro é o *registro base* para o cenário.
1. Em um segundo registro de contêiner do Azure, [defina](container-registry-tasks-cross-registry-authentication.md#define-task-steps-in-yaml-file) e [crie](container-registry-tasks-cross-registry-authentication.md#option-2-create-task-with-system-assigned-identity) uma tarefa ACR para efetuar pull de uma imagem do registro base. Habilite uma identidade gerenciada atribuída pelo sistema ao criar a tarefa.
1. Atribua à identidade da tarefa [uma função do Azure para acessar o registro base](container-registry-tasks-authentication-managed-identity.md#3-grant-the-identity-permissions-to-access-other-azure-resources). Por exemplo, atribua a função AcrPull, que tem permissões para efetuar pull de imagens.
1. [Adicione credenciais de identidade gerenciadas](container-registry-tasks-authentication-managed-identity.md#4-optional-add-credentials-to-the-task) à tarefa.
1. Para confirmar se a tarefa ignora as restrições de rede, [desabilite o acesso público](container-registry-access-selected-networks.md#disable-public-network-access) no registro base.
1. Execute a tarefa. Se o registro base e a tarefa estiverem configurados corretamente, a tarefa será executada com êxito, pois o registro base permite o acesso.

Para testar a desabilitação do acesso por serviços confiáveis:

1. No registro base, desabilite a configuração para permitir o acesso por serviços confiáveis.
1. Execute a tarefa novamente. Nesse caso, a execução da tarefa falha, porque o registro base não permite mais o acesso pela tarefa.

## <a name="next-steps"></a>Próximas etapas

* Para restringir o acesso a um registro usando um ponto de extremidade privado em uma rede virtual, confira [Configurar o Link Privado do Azure para um registro de contêiner do Azure](container-registry-private-link.md).
* Para configurar as regras de firewall do registro, consulte [configurar regras de rede IP pública](container-registry-access-selected-networks.md).
