---
title: Atualizar uma VM do Azure com o SUSE Linux Enterprise Server para o SUSE 15 SP1 | Microsoft Docs
description: Este artigo fornece etapas gerais sobre como usar o Sistema de Migração de Distribuição SUSE para atualizar o SUSE Linux Enterprise Server para o SUSE 15 SP1 em uma máquina virtual do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: amkarmak
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/21/2020
ms.author: arremana
ms.openlocfilehash: 7ed355f82b88f460ff4b372484a690f166a15550
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91359504"
---
# <a name="upgrade-azure-vm-with-sles-12-to-sles-15-sp1"></a>Atualizar uma VM do Azure com o SLES 12 para o SLES 15 SP1

Este artigo fornece etapas gerais sobre como atualizar o SLES (SUSE Linux Enterprise Server) 12 para o SLES 15 SP1 em uma VM (máquina virtual) do Azure. Para obter mais informações, confira [Como usar o Sistema de Migração de Distribuição SUSE](https://documentation.suse.com/suse-distribution-migration-system/1.0/single-html/distribution-migration-system/index.html) e [Guia de Atualização do SUSE Linux Enterprise Server 15 SP1](https://documentation.suse.com/sles/15-SP1/single-html/SLES-upgrade/index.html#sec-update-preparation-update).

## <a name="supported-upgrade-paths"></a>Caminhos de atualização compatíveis
A versão atual do SLES precisa ser o SLES 12 SP4 ou 12 SP5 para prosseguir para o SLES 15 SP1.

![A captura de tela sobre o caminho de atualização compatível](./media/linux-upgrade-suse-15sp1/upgrade-path.png)

## <a name="prerequisites"></a>Pré-requisitos

- Planeje a atividade de migração de acordo com a janela de tempo de inatividade aprovada. Isso porque a VM é reiniciada durante a migração.
- Antes da atividade de migração, faça um backup completo da VM.
- Se o backup não estiver configurado, faça um backup instantâneo do disco do sistema operacional.
- [Verifique se a VM é da geração V1 ou da geração V2](#check-the-generation-version-for-a-vm).

## <a name="upgrade-from-suse-12-sp4-or-sp5-to-suse-15-sp1"></a>Atualização do SUSE 12 SP4 ou SP5 para o SUSE 15 SP1

1. Instale o último pacote da VM:

    ```
    zypper clean --all
    zypper refresh
    zypper update
    ```

2. Depois que a instalação for concluída, reinicie a VM.

3. Confirme o kernel e a versão do sistema operacional. Verifique se a versão é SUSE 12 SP4 ou SUSE 12 SP5.

    ```
    uname -a
    cat /etc/os-release
    ```

4. Instale o **suse-migration-sle15-activation**. Quando o pacote **suse-migration-sle15-activation** for instalado, outro pacote **SLES15-Migration** será instalado automaticamente como um pacote de dependência. 

   ```
   zypper install suse-migration-sle15-activation
   ```

5. Após a conclusão da instalação, execute o comando `reboot` para reiniciar a VM.

6. Acesse o [portal do Azure](https://portal.azure.com), selecione a VM e **Console serial**. Você verá que o sistema será interrompido em "reinicializar: reiniciando o sistema". Esse processo deverá levar cerca de 15 a 45 minutos. Para a VM de geração 2, ela poderá travar na tela "reinicialização: reiniciando o sistema". Nesse caso, aguarde 45 minutos. Se ela não apresentar nenhum progresso, acesse a página **Visão geral** da VM no portal do Azure, interrompa a VM e reinicie-a.

     ![A captura de tela sobre as mensagens no console serial.](./media/linux-upgrade-suse-15sp1/reboot-message.png)

8. Depois que o sistema for reiniciado com o novo kernel, você verá a mensagem a seguir.

     ![A captura de tela sobre as mensagens no console serial após o sistema ser reiniciado com o novo kernel.](./media/linux-upgrade-suse-15sp1/output-message.png)
9. Confirme o kernel e a versão do sistema operacional para verificar se o sistema foi atualizado com êxito.

    ```
    uname -a
    cat /etc/os-release
    ```

## <a name="check-the-generation-version-for-a-vm"></a>Verificar a versão de geração de uma VM

Use um dos seguintes métodos para verificar a versão de geração:

- No terminal do SLES, execute o comando `dmidecode | grep -i hyper`. Se ela for uma VM da geração V1, nenhuma saída será retornada. Para as VMs da geração V2, você verá a seguinte saída:

     ![A captura de tela sobre a saída da VM da geração 2](./media/linux-upgrade-suse-15sp1/output-gen2.png)
- No [portal do Azure](https://portal.azure.com), acesse **Propriedades** da VM e marque o campo **Geração de VM**.
