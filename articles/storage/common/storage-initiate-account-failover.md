---
title: Iniciar um failover de conta de armazenamento
titleSuffix: Azure Storage
description: Saiba como iniciar um failover de conta caso o ponto de extremidade primário para sua conta de armazenamento não esteja disponível. O failover atualiza a região secundária para torná-la a região primária de sua conta de armazenamento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/29/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 93bcbab9445d83bf17b37b6affc1d2bc70703bbf
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814322"
---
# <a name="initiate-a-storage-account-failover"></a>Iniciar um failover de conta de armazenamento

Se o ponto de extremidade primário para sua conta de armazenamento com redundância geográfica ficar indisponível por algum motivo, você poderá iniciar um failover de conta. Um failover de conta atualiza o ponto de extremidade secundário para torná-lo um ponto de extremidade primário de sua conta de armazenamento. Quando o failover estiver concluído, os clientes poderão começar a gravar na nova região primária. O failover forçado permite manter uma alta disponibilidade para seus aplicativos.

Este artigo mostra como iniciar um failover de conta para sua conta de armazenamento usando o portal do Azure, o PowerShell ou a CLI do Azure. Para saber mais sobre o failover de conta, consulte [recuperação de desastre e failover da conta de armazenamento](storage-disaster-recovery-guidance.md).

> [!WARNING]
> Um failover de conta normalmente resulta em alguma perda de dados. Para entender as implicações de um failover de conta e para preparar a perda de dados, examine [Entendendo o processo de failover da conta](storage-disaster-recovery-guidance.md#understand-the-account-failover-process).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para poder executar um failover de conta em sua conta de armazenamento, verifique se sua conta de armazenamento está configurada para replicação geográfica. Sua conta de armazenamento pode usar qualquer uma das seguintes opções de redundância:

- Armazenamento com redundância geográfica (GRS) ou armazenamento com redundância geográfica com acesso de leitura (RA-GRS)
- Armazenamento com redundância de zona geográfica (GZRS) ou armazenamento com redundância de acesso de leitura (RA-GZRS)

Para obter mais informações sobre a redundância do Armazenamento do Microsoft Azure, confira [Redundância no Armazenamento do Azure](storage-redundancy.md).

Tenha em mente que os seguintes recursos e serviços não têm suporte para o failover de conta:

- A Sincronização de Arquivos do Azure não oferece suporte ao failover de conta de armazenamento. Não deve ser realizado o failover das contas de armazenamento que contêm compartilhamentos de arquivos do Azure que estejam sendo usadas como pontos de extremidade de nuvem na Sincronização de Arquivos do Azure. Se isso for feito, a sincronização deixará de funcionar e poderá causar a perda inesperada de dados no caso de arquivos recentes em camadas.
- ADLS Gen2 contas de armazenamento (contas que têm o namespace hierárquico habilitado) não têm suporte no momento.
- Não é possível realizar failover em uma conta de armazenamento que contenha blob de blocos premium. As contas de armazenamento que dão suporte a blobs de bloco premium atualmente não são compatíveis com a redundância geográfica.
- Não é possível fazer failover de uma conta de armazenamento contendo contêineres habilitados da [política de imutabilidade do worm](../blobs/storage-blob-immutable-storage.md) . A retenção baseada em tempo desbloqueada/bloqueada ou as políticas de manutenção legal impedem o failover para manter a conformidade.

## <a name="initiate-the-failover"></a>Iniciar o failover

## <a name="portal"></a>[Portal](#tab/azure-portal)

Para iniciar um failover da conta do portal do Azure, siga estas etapas:

1. Navegue para sua conta de armazenamento.
1. Em **Configurações**, selecione **Replicação geográfica**. A imagem a seguir mostra o status de replicação geográfica e de failover de uma conta de armazenamento.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-prepare.png" alt-text="Captura de tela mostrando o status de failover e de replicação geográfica":::

1. Verifique se a conta de armazenamento está configurada para GRS (armazenamento com redundância geográfica) ou RA-GRS (armazenamento com redundância geográfica com acesso de leitura). Se não estiver, selecione **Configuração** em **Configurações** para atualizar sua conta, acrescentando redundância geográfica a ela.
1. A propriedade **Hora da Última Sincronização** indica o atraso do secundário em relação ao primário. A **Hora da Última Sincronização** fornece uma estimativa da extensão da perda de dados que você experimentará após a conclusão do failover. Para obter mais informações sobre como verificar a propriedade **hora da última sincronização** , consulte [verificar a propriedade hora da última sincronização de uma conta de armazenamento](last-sync-time-get.md).
1. Selecione **Preparar para failover**.
1. Revise a caixa de diálogo de confirmação. Quando você estiver pronto, insira **Sim** para confirmar e iniciar o failover.

    :::image type="content" source="media/storage-initiate-account-failover/portal-failover-confirm.png" alt-text="Caixa de diálogo de confirmação de que mostra a captura de tela de um failover de conta":::

## <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O recurso de failover de conta está geralmente disponível, mas ainda depende de um módulo de visualização para o PowerShell. Para usar o PowerShell para iniciar um failover de conta, você deve primeiro instalar o módulo AZ. Storage [1.1.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.1.1-preview) . Para instalar o módulo, siga estas etapas:

1. Desinstale as instalações anteriores do Azure PowerShell:

    - Remova as instalações anteriores do Azure PowerShell do Windows usando a configuração **Aplicativos e recursos** em **Configurações**.
    - Remova todos os módulos do **Azure** de `%Program Files%\WindowsPowerShell\Modules` .

1. Verifique se tem a versão mais recente do PowerShellGet instalado. Abra uma janela do Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Feche e reabra a janela do PowerShell depois de instalar o PowerShellGet.

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Instalar um módulo de visualização do armazenamento do Azure que dá suporte ao failover de conta:

    ```powershell
    Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.1.1-preview –AllowPrerelease –AllowClobber –Force
    ```

Para iniciar um failover de conta do PowerShell, execute o seguinte comando:

```powershell
Invoke-AzStorageAccountFailover -ResourceGroupName <resource-group-name> -Name <account-name>
```

## <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para usar a CLI do Azure para iniciar um failover de conta, execute os seguintes comandos:

```azurecli-interactive
az storage account show \ --name accountName \ --expand geoReplicationStats
az storage account failover \ --name accountName
```

---

## <a name="important-implications-of-account-failover"></a>Implicações importantes de failover da conta

Quando você inicia um failover de conta para sua conta de armazenamento, os registros DNS para o ponto de extremidade secundário são atualizados para que o ponto de extremidade secundário se torne o ponto de extremidade primário. Verifique se você compreende o impacto potencial para sua conta de armazenamento antes de iniciar um failover.

Para estimar a extensão de perda de dados provável antes de iniciar um failover, verifique a propriedade **hora da última sincronização** . Para obter mais informações sobre como verificar a propriedade **hora da última sincronização** , consulte [verificar a propriedade hora da última sincronização de uma conta de armazenamento](last-sync-time-get.md).

O tempo necessário para o failover após a inicialização pode variar, embora normalmente menos de uma hora.

Após o failover, o tipo de conta de armazenamento é automaticamente convertido em LRS (Armazenamento com Redundância Local) na nova região primária. Você pode reabilitar o GRS (armazenamento com redundância geográfica) ou o RA-GRS (armazenamento com redundância geográfica com acesso de leitura) para a conta. Observe que a conversão de LRS em GRS ou RA-GRS acarreta um custo adicional. Para obter informações adicionais, veja [Detalhes de preço de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

Depois de habilitar novamente o GRS para sua conta de armazenamento, a Microsoft começa a replicar os dados em sua conta para a nova região secundária. O tempo de replicação depende da quantidade de dados sendo replicados.  

## <a name="next-steps"></a>Próximas etapas

- [Recuperação de desastres e failover da conta de armazenamento](storage-disaster-recovery-guidance.md)
- [Verificar a propriedade Horário da Última Sincronização de uma conta de armazenamento](last-sync-time-get.md)
- [Uso da redundância geográfica para criar aplicativos altamente disponíveis](geo-redundant-design.md)
- [Tutorial: Criar um aplicativo altamente disponível com o armazenamento de Blobs](../blobs/storage-create-geo-redundant-storage.md)
