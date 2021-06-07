---
title: O que fazer se houver uma interrupção no serviço do Azure que afete o Azure Key Vault | Microsoft Docs
description: Saiba o que fazer se houver uma interrupção no serviço do Azure que afete o HSM Gerenciado.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 8c284e9993002f6e05e41ca00d00b388feef8f82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375996"
---
# <a name="managed-hsm-disaster-recovery"></a>Recuperação de desastre do HSM Gerenciado

Talvez você queira criar uma réplica exata do HSM para o caso do original ser perdido ou ficar indisponível devido a qualquer uma das seguintes razões:

- Foi excluído e, em seguida, limpo.
- Uma falha catastrófica na região resultou na destruição de todas as partições-membro.

Você poderá recriar a instância do HSM na mesma região ou em uma diferente se tiver o seguinte:
- O [Domínio de Segurança](security-domain.md) do HSM de origem.
- As chaves privadas (pelo menos o número do quorum) que criptografam o domínio de segurança.
- O [backup](backup-restore.md) completo mais recente do HSM de origem.

Aqui estão as etapas do procedimento de recuperação de desastre:

1. Crie uma instância do HSM.
1. Ative a "Recuperação do Domínio de Segurança". Um novo par de chaves RSA (chave de Troca do Domínio de Segurança) será gerado para a transferência do Domínio de Segurança e enviado como resposta, que será baixada como uma SecurityDomainExchangeKey (chave pública).
1. Crie e carregue o "Arquivo de Transferência do Domínio de Segurança". Você precisará das chaves privadas que criptografam o domínio de segurança. As chaves privadas são usadas localmente e jamais são transferidas em nenhum lugar desse processo.
1. Faça um backup do novo HSM. É necessário um backup antes de qualquer operação de restauração, mesmo quando o HSM está vazio. Os backups facilitam a reversão.
1. Restaurar o backup recente do HSM de origem

Estas etapas permitirão que você replique manualmente o conteúdo do HSM para outra região. O nome do HSM (e o URI do ponto de extremidade de serviço) será diferente. Portanto, talvez seja necessário alterar a configuração do aplicativo para usar essas chaves em outra localização.

## <a name="create-a-new-managed-hsm"></a>Criar um HSM Gerenciado

Use o comando `az keyvault create` para criar um HSM Gerenciado. Esse script tem três parâmetros obrigatórios: um nome de grupo de recursos, um nome de HSM e a localização geográfica.

Você deve fornecer as seguintes entradas para criar um recurso do HSM Gerenciado:

- O nome do HSM.
- O grupo de recursos em que ele será colocado na assinatura.
- A localização do Azure.
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

## <a name="activate-the-security-domain-recovery-mode"></a>Ativar o modo de recuperação do Domínio de Segurança

No processo de criação normal, inicializamos e baixamos um novo Domínio de Segurança neste ponto. No entanto, como estamos executando um procedimento de recuperação de desastre, solicitamos ao HSM que entre no Modo de Recuperação de Domínio de Segurança e baixe uma Chave de Troca do Domínio de Segurança. A Chave de Troca do Domínio de Segurança é uma chave pública RSA que será usada para criptografar o domínio de segurança antes de carregá-lo no HSM. A chave privada correspondente é protegida dentro do HSM, para manter a proteção do conteúdo do Domínio de Segurança durante a transferência.

```azurecli-interactive
az keyvault security-domain init-recovery --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM2-SDE.cer
```

## <a name="upload-security-domain-to-destination-hsm"></a>Carregar o Domínio de segurança no HSM de destino

Para esta etapa, você precisará do seguinte:
- A Chave de Troca do Domínio de Segurança que você baixou na etapa anterior.
- O Domínio de Segurança do HSM de origem.
- Ao menor o número do quorum de chaves privadas que foram usadas para criptografar o Domínio de segurança.

O comando `az keyvault security-domain upload` executa as seguintes operações:

- Descriptografa o Domínio de Segurança do HSM de origem com as chaves privadas que você fornece; 
- Cria um blob de Upload do Domínio de Segurança criptografado com a chave de Troca de Domínio de Segurança que baixamos na etapa anterior; e
- carrega o blob de Upload do Domínio de Segurança no HSM para concluir a recuperação do Domínio de Segurança

No exemplo a seguir, usamos o Domínio de Segurança do **ContosoMHSM**, 2 das chaves privadas correspondentes e carregamos no **ContosoMHSM2**, que está aguardando para receber um Domínio de Segurança. 

```azurecli-interactive
az keyvault security-domain upload --hsm-name ContosoMHSM2 --sd-exchange-key ContosoMHSM-SDE.cer --sd-file ContosoMHSM-SD.json --sd-wrapping-keys cert_0.key cert_1.key
```

Agora, tanto o HSM de origem (ContosoMHSM) quanto o HSM de destino (ContosoMHSM2) têm o mesmo Domínio de Segurança. Agora nós podemos restaurar um backup completo do HSM de origem no HSM de destino.

## <a name="create-a-backup-as-a-restore-point-of-your-new-hsm"></a>Criar um backup (como um ponto de restauração) do seu novo HSM

É sempre uma boa ideia fazer um backup completo antes de executar uma restauração completa do HSM, para que você tenha um ponto de restauração caso algo dê errado na restauração.

Para criar um backup do HSM, você precisará do seguinte:
- Uma conta de armazenamento em que o backup será armazenado
- Um contêiner de armazenamento de blobs nessa conta de armazenamento em que o processo de backup criará uma pasta para armazenar o backup criptografado

Usamos o comando `az keyvault backup` para o backup do HSM no contêiner de armazenamento **mhsmbackupcontainer**, que está na conta de armazenamento **ContosoBackup** para o exemplo abaixo. Criamos um token SAS que expira em 30 minutos e o fornecemos ao HSM Gerenciado para gravar o backup.

```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
az storage container create --account-name  mhsmdemobackup --name mhsmbackupcontainer  --account-key $skey
sas=$(az storage container generate-sas -n mhsmbackupcontainer --account-name ContosoBackup --permissions crdw --expiry $end --account-key $skey -o tsv)
az keyvault backup start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas

```

## <a name="restore-backup-from-source-hsm"></a>Restaurar o backup do HSM de origem

Para esta etapa, você precisará do seguinte:

- A conta de armazenamento e o contêiner de blob em que estão armazenados os backups do HSM de origem.
- O nome da pasta da qual você deseja restaurar o backup. Se você criar backups regularmente, haverá muitas pastas dentro desse contêiner.


```azurecli-interactive
end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')
skey=$(az storage account keys list --query '[0].value' -o tsv --account-name ContosoBackup)
sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name ContosoBackup --permissions rl --expiry $end --account-key $skey -o tsv)
az keyvault restore start --hsm-name ContosoMHSM2 --storage-account-name ContosoBackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-ContosoMHSM-2020083120161860
```

Agora você concluiu um processo de recuperação de desastre completo. O conteúdo do HSM de origem de quando o backup foi feito é copiado ao HSM de destino, incluindo todas as chaves, versões, atributos, marcações e atribuições de função.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre Domínios de Segurança conferindo [Sobre o Domínio de Segurança do HSM Gerenciado](security-domain.md)
- Siga as [Melhores práticas do HSM Gerenciado](best-practices.md)
