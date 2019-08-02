---
author: jluk
ms.service: cloud-shell
ms.topic: persist-storage
ms.date: 9/7/2018
ms.author: juluk
ms.openlocfilehash: c28441b6fe25b3480a55b79682d5067b19e3023a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172260"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Persistir arquivos no Azure Cloud Shell
O Cloud Shell utiliza o armazenamento dos Arquivos do Azure para persistir os arquivos entre as sessões. No primeiro início, o Cloud Shell solicita a associação de um compartilhamento de arquivos novo ou existente para persistir arquivos entre as sessões.

> [!NOTE]
> O Bash e o PowerShell compartilham o mesmo compartilhamento de arquivos. Somente um compartilhamento de arquivos pode ser associado á montagem automática no Cloud Shell.

## <a name="create-new-storage"></a>Criar novo armazenamento

Ao usar as configurações básicas e seleciona apenas uma assinatura, o Cloud Shell criará três recursos em seu nome na região com suporte mais próxima de você:
* Grupo de recursos: `cloud-shell-storage-<region>`
* Conta de armazenamento: `cs<uniqueGuid>`
* Compartilhamento de arquivos:`cs-<user>-<domain>-com-<uniqueGuid>`

![A configuração da assinatura](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

O compartilhamento de arquivos é montado como `clouddrive` no seu diretório `$Home`. Isso é uma ação única e o compartilhamento de arquivos será montado automaticamente nas sessões subsequentes. 

> [!NOTE]
> Por questões de segurança, cada usuário deve provisionar a própria conta de armazenamento.  No caso do RBAC (controle de acesso baseado em função), os usuários devem ter acesso de colaborador ou superior, no nível da conta de armazenamento.

O compartilhamento de arquivos também contém uma imagem de 5 GB criada para você e que automaticamente mantém os dados em seu diretório `$Home`. Isso se aplica para Bash e PowerShell.

## <a name="use-existing-resources"></a>Usar recursos existentes

Usando a opção avançada, você pode associar recursos existentes. Ao selecionar uma região do Cloud Shell, você deve selecionar uma conta de armazenamento de apoio co-localizada na mesma região. Por exemplo, se sua região atribuída for Oeste dos EUA, você deve associar um compartilhamento de arquivos que reside no Oeste dos EUA também.

Quando aparecer o prompt de instalação de armazenamento, selecione **Mostrar configurações avançadas** para exibir opções adicionais. As opções de armazenamento preenchidas são filtradas para contas de LRS (armazenamento com redundância local), GRS (armazenamento com redundância geográfica) e ZRS (armazenamento com redundância de zona). 

> [!NOTE]
> Contas de armazenamento usando GRS ou ZRS são recomendadas para obter resiliência adicional para o compartilhamento de arquivos de backup. Qual tipo de redundância depende de suas metas e a preferência de preço. [Saiba mais sobre opções de replicação para contas do Armazenamento do Microsoft Azure](https://docs.microsoft.com/azure/storage/common/storage-redundancy).

![A configuração do grupo de recursos](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

### <a name="supported-storage-regions"></a>Regiões de armazenamento com suporte
As contas de armazenamento do Azure associadas devem residir na mesma região que o computador do Cloud Shell em que você estiver montando. Para localizar sua região atual, você poderá executar `env` em Bash e localizar a variável `ACC_LOCATION`. Os compartilhamentos de arquivos recebem uma imagem de 5 GB criada para você manter o diretório `$Home`.

Há computadores do Cloud Shell nas regiões a seguir:

|Área|Região|
|---|---|
|Américas|Leste dos EUA, Centro-Sul dos EUA, Oeste dos EUA|
|Europa|Norte da Europa, Europa Ocidental|
|Pacífico Asiático|Índia Central, Sudeste Asiático|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restringir a criação de recursos com uma política de recursos do Azure
As contas de armazenamento criadas no Cloud Shell são marcadas com `ms-resource-usage:azure-cloud-shell`. Se você deseja impedir que os usuários criem contas de armazenamento no Cloud Shell, crie uma [Política de recursos do Azure para marcas](../articles/azure-policy/json-samples.md) que seja disparada por essa marca específica.
