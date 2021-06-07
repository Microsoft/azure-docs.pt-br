---
title: Backup/restauração completo e restauração seletiva para HSM Gerenciado do Azure
description: Este documento explica o backup/restauração completo e a restauração seletiva
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: e926dcd4b05d137c7927bdfe5221923d25d4670c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100093481"
---
# <a name="full-backup-and-restore"></a>Backup e restauração completo

> [!NOTE]
> Este recurso só está disponível para o tipo de recurso HSM Gerenciado.

O HSM Gerenciado dá suporte à criação de um backup completo de todo o conteúdo do HSM, incluindo todas as chaves, versões, atributos, marcações e atribuições de função. O backup é criptografado com chaves criptográficas associadas ao Domínio de Segurança do HSM.

O backup é uma operação do plano de dados. O chamador que inicia a operação de backup deve ter permissão para executar a ação de dados **Microsoft.KeyVault/managedHsm/backup/start/action**.

Somente as seguintes funções internas têm permissão para executar um backup completo:
- Administrador de HSM Gerenciado
- Backup do HSM Gerenciado

Você deve fornecer as seguintes informações para executar um backup completo:
- Nome ou URL do HSM
- Nome da conta de armazenamento
- Contêiner de armazenamento de blobs da conta de armazenamento
- Token SAS do contêiner de armazenamento com permissões `crdw`

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>Backup completo

O backup é uma operação de execução prolongada, mas que retorna imediatamente uma ID de Trabalho. Você pode verificar o status do processo de backup usando essa ID de Trabalho. O processo de backup cria uma pasta dentro do contêiner designada com o seguinte padrão de nomenclatura: **`mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`** , em que HSM_NAME é o nome do HSM Gerenciado cujo backup está sendo realizado e AAAA, MM, DD, HH, MM, mm, SS são o ano, o mês, o dia, a hora, os minutos e os segundos da data/hora em UTC quando o comando de backup foi recebido.

Enquanto o backup estiver em andamento, o HSM não poderá operar com a taxa de transferência total, pois algumas partições do HSM estarão ocupadas executando a operação de backup.

```azurecli-interactive
# time for 500 minutes later for SAS token expiry

end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Create a container

az storage container create --account-name  mhsmdemobackup --name mhsmdemobackupcontainer  --account-key $skey

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>Restauração completa

A restauração completa permite restaurar completamente o conteúdo do HSM com base em um backup anterior, incluindo todas as chaves, versões, atributos, marcações e atribuições de função. Tudo o que estiver armazenado no momento no HSM será apagado e ele retornará ao mesmo estado em que se encontrava quando o backup de origem foi criado.

> [!IMPORTANT]
> A restauração completa é uma operação bastante destrutiva e robusta. Portanto, é obrigatório haver concluído um backup completo nos últimos 30 minutos para poder executar a operação `restore`.

A restauração é uma operação do plano de dados. O chamador que inicia a operação de restauração deve ter permissão para executar a ação de dados **Microsoft.KeyVault/managedHsm/restore/start/action**. O HSM de origem em que o backup foi criado e o HSM de destino em que a restauração será executada **devem** ter o mesmo Domínio de Segurança. Confira mais informações [sobre o Domínio de Segurança do HSM Gerenciado](security-domain.md).

Você deve fornecer as seguintes informações para executar uma restauração completa:
- Nome ou URL do HSM
- Nome da conta de armazenamento
- Contêiner de blobs da conta de armazenamento
- Token SAS do contêiner de armazenamento com permissões `rl`
- Nome da pasta do contêiner de armazenamento em que o backup de origem está armazenado

A restauração é uma operação de execução prolongada, mas que retorna imediatamente uma ID de Trabalho. Você pode verificar o status do processo de restauração usando essa ID de Trabalho. Quando o processo de restauração estiver em andamento, o HSM entrará em modo de restauração e todos os comandos do plano de dados (exceto verificar status da restauração) serão desabilitados.

```azurecli-interactive
#### time for 500 minutes later for SAS token expiry

end=$(date -u -d "500 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="restore-hsm"></a>Restaurar HSM

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>Próximas etapas
- Confira [Gerenciar um HSM Gerenciado usando a CLI do Azure](key-management.md).
- Confira mais informações sobre o [Domínio de Segurança do HSM Gerenciado](security-domain.md)
