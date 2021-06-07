---
title: 'Início rápido: provisionar e ativar um HSM Gerenciado do Azure'
description: Guia de início rápido mostrando como provisionar e ativar um HSM Gerenciado usando a CLI do Azure
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 86d0a336a7d3f5d12ed8e53de802616f839f9eba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91756804"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>Início Rápido: Provisionar e ativar um HSM gerenciado usando a CLI do Azure

O HSM Gerenciado do Azure Key Vault é um serviço de nuvem em conformidade com os padrões de um único locatário, altamente disponível e totalmente gerenciado que permite proteger chaves criptográficas para seus aplicativos de nuvem usando HSMs validados pelo **FIPS 140-2 Nível 3**. Para obter mais informações sobre o HSM gerenciado, você pode examinar [Visão geral](overview.md). 

Neste guia de início rápido, você cria e ativa um HSM gerenciado com a CLI do Azure. Depois de concluído, você armazenará um segredo.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas deste artigo, você precisará ter os seguintes itens:

* Uma assinatura do Microsoft Azure. Se você não tiver uma, pode se inscrever e fazer uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* A CLI do Azure versão 2.12.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
* Um HSM Gerenciado na sua assinatura. Confira [Início Rápido: Provisione e ative um HSM Gerenciado usando a CLI do Azure](quick-create-cli.md) para provisionar e ativar um HSM Gerenciado.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Entrar no Azure

Para entrar no Azure usando a CLI, você pode digitar:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. O exemplo a seguir cria um grupo de recursos chamado *ContosoResourceGroup* na localização *eastus2*.

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location eastus2
```

## <a name="create-a-managed-hsm"></a>Criar um HSM Gerenciado

A criação de um HSM gerenciado é um processo de duas etapas:
1. Provisionar um recurso de HSM gerenciado.
1. Ativar o HSM gerenciado baixando o domínio de segurança.

### <a name="provision-a-managed-hsm"></a>Provisionar um HSM gerenciado

Use o comando `az keyvault create` para criar um HSM gerenciado. Esse script tem três parâmetros obrigatórios: um nome de grupo de recursos, um nome de HSM e a localização geográfica.

Você precisa fornecer as seguintes entradas para criar um recurso do HSM Gerenciado:
- Um grupo de recursos em que ele será colocado na assinatura.
- Localização do Azure.
- Uma lista de administradores iniciais.

O exemplo a seguir cria um HSM chamado **ContosoMHSM**, no grupo de recursos **ContosoResourceGroup**, que reside na localização **Leste dos EUA 2**, com **o usuário conectado no momento** sendo o único administrador.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> O comando Criar pode levar alguns minutos. Quando ele retornar com sucesso, você estará pronto para ativar o HSM.

A saída deste comando mostra as propriedades do HSM Gerenciado que você criou. As duas propriedades mais importantes são:

* **nome**: no exemplo, o nome é ContosoMHSM. Você usará esse nome para outros comandos do Key Vault.
* **hsmUri**: No exemplo, o URI é 'https://contosohsm.managedhsm.azure.net '. Os aplicativos que usam o HSM por meio da API REST devem usar essa URI.

Sua conta do Azure agora está autorizada a executar qualquer operação neste HSM Gerenciado. Até o momento, ninguém mais tem autorização.

### <a name="activate-your-managed-hsm"></a>Ativar o HSM gerenciado

Todos os comandos de plano de dados são desabilitados até que o HSM seja ativado. Você não poderá criar chaves nem atribuir funções. Somente os administradores designados que foram atribuídos durante o comando create podem ativar o HSM. Para ativar o HSM, você deve baixar o [Domínio de Segurança](security-domain.md).

Para ativar seu HSM, você precisa:
- Pelo menos três pares de chave RSA (máximo de 10)
- Especifique o número mínimo de chaves necessárias para descriptografar o domínio de segurança (quorum)

Para ativar o HSM, você envia pelo menos três (máximo 10) chaves públicas RSA para o HSM. O HSM criptografa o domínio de segurança com essas chaves e o envia de volta. Depois que o download do domínio de segurança for concluído com êxito, o HSM estará pronto para uso. Você também precisa especificar quorum, que é o número mínimo de chaves privadas necessárias para descriptografar o domínio de segurança.

O exemplo a seguir mostra como usar `openssl` para gerar três certificados autoassinados.

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Crie e armazene os pares de chaves RSA e o arquivo de domínio de segurança gerados nesta etapa com segurança.

Use o comando `az keyvault security-domain download` para baixar o domínio de segurança e ativar o HSM gerenciado. O exemplo a seguir usa três pares de chaves RSA (somente as chaves públicas são necessárias para esse comando) e define o quorum como 2.

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

Armazene o arquivo de domínio de segurança e os pares de chaves RSA com segurança. Você precisará deles para a recuperação de desastres ou para criar outro HSM gerenciado que compartilhe o mesmo domínio de segurança para que eles possam compartilhar chaves.

Depois de baixar com êxito o domínio de segurança, seu HSM estará em estado ativo e pronto para uso.

## <a name="clean-up-resources"></a>Limpar os recursos

Outros guias de início rápido e tutoriais da coleção aproveitam esse guia de início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.

Quando não forem mais necessários, você poderá usar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos e todos os recursos relacionados. Você pode excluir os recursos da seguinte maneira:

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um Key Vault e armazenou um segredo nele. Para saber mais sobre o Key Vault e como integrá-lo a seus aplicativos, confira os artigos abaixo.

- Leia uma [Visão geral do HSM gerenciado](overview.md)
- Saiba mais sobre [Gerenciar chaves em um HSM gerenciado](key-management.md)
- Examine as [Melhores práticas do HSM Gerenciado](best-practices.md)
