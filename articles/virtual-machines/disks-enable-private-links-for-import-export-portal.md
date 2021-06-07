---
title: Portal do Azure – Restringir o acesso de importação/exportação aos discos gerenciados com Links Privados
description: Habilite Links Privados para os seus discos gerenciados com o portal do Azure. Permitindo que você exporte e importe discos com segurança em sua rede virtual.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b80100216003e91fde54b5e555bafb755c942810
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98682912"
---
# <a name="use-the-azure-portal-to-restrict-importexport-access-for-managed-disks-with-private-links"></a>Usar o portal do Azure para restringir o acesso de importação/exportação aos discos gerenciados com Links Privados

O suporte dos Links Privados para discos gerenciados permite que você restrinja a exportação e a importação de discos gerenciados para que elas ocorram somente dentro da sua rede virtual do Azure. Gere um URI de SAS (Assinatura de Acesso Compartilhado) com limite de tempo para discos gerenciados e instantâneos desanexados a fim de exportar os dados para outra região para expansão regional, recuperação de desastre e a fim de ler os dados para análise forense. Use também o URI de SAS para carregar diretamente o VHD em um disco vazio do local. O tráfego de rede entre os clientes na rede virtual e os discos gerenciados atravessa somente a rede virtual e um link privado na rede de backbone da Microsoft, eliminando a exposição à Internet pública.

Crie um recurso de acesso a disco e vincule-o à sua rede virtual na mesma assinatura criando um ponto de extremidade privado. Você precisará associar um disco ou um instantâneo a um acesso a disco para exportar e importar os dados por meio de Links Privados. Além disso, você precisará definir a propriedade NetworkAccessPolicy do disco ou do instantâneo como `AllowPrivate`. 

Você pode definir a propriedade NetworkAccessPolicy como `DenyAll` para impedir que qualquer pessoa gere o URI de SAS para um disco ou um instantâneo. O valor padrão da propriedade NetworkAccessPolicy é `AllowAll`.

## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="create-a-disk-access-resource"></a>Criar um recurso de acesso a disco

1. Entre no portal do Azure e procure **Acesso a Disco** com [este link](https://aka.ms/disksprivatelinks).

    > [!IMPORTANT]
    > Use o [link fornecido](https://aka.ms/disksprivatelinks) para procurar a folha Acesso a Disco. No momento, ela não está visível no portal público sem o uso do link.

1. Selecione **+ Adicionar** para criar um recurso de acesso a disco.
1. Na folha Criar, selecione sua assinatura e um grupo de recursos, insira um nome e selecione uma região.
1. Selecione **Examinar + criar**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="Captura de tela da folha de criação de acesso a disco. Preencha o nome desejado, selecione uma região, escolha um grupo de recursos e prossiga":::

Quando o recurso for criado, acesse-o diretamente.

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="Captura de tela do botão Ir para recurso no portal":::

## <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

Agora que você tem um recurso de acesso a disco, use-o para lidar com o acesso às exportações/importações do disco. Isso é feito por meio de pontos de extremidade privados. Por isso, você precisará criar um ponto de extremidade privado e configurá-lo para acesso a disco.

1. No recurso de acesso a disco, selecione **Conexões de ponto de extremidade privado**.
1. Selecione **+ Ponto de extremidade privado**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="Captura de tela da folha de visão geral do recurso de acesso a disco. As conexões do ponto de extremidade privado estão realçadas.":::

1. Selecionar um grupo de recursos
1. Preencha o nome e selecione a mesma região em que o recurso de acesso a disco foi criado.
1. Selecione **Avançar: Recurso >**

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="Captura de tela do fluxo de trabalho de criação do ponto de extremidade privado, primeira folha. Se você não selecionar a região apropriada, poderá ter problemas mais tarde.":::

1. Na folha **Recurso**, selecione **Conectar-se a um recurso do Azure em meu diretório**.
1. Em **Tipo de recurso** selecione **Microsoft.Compute/diskAccesses**
1. Em **Recurso**, selecione o recurso de acesso a disco que você criou anteriormente
1. Mantenha o **Sub-recurso de destino** como **discos**
1. Selecione **Avançar: Configuração >** .

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="Captura de tela do fluxo de trabalho de criação do ponto de extremidade privado, segunda folha. Com todos os valores realçados (Tipo de recurso, Recurso, Sub-recurso de destino)":::

1. Selecione a rede virtual para a qual deseja limitar a exportação de disco; as outras redes virtuais não poderão exportar o disco.

    > [!NOTE]
    > Se houver um NSG (grupo de segurança de rede) habilitado para a sub-rede selecionada, ele será desabilitado para os pontos de extremidade privados somente nessa sub-rede. Os outros recursos na sub-rede ainda terão a imposição do NSG.

1. Selecione a sub-rede apropriada
1. Selecione **Examinar + criar**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="Captura de tela do fluxo de trabalho de criação do ponto de extremidade privado, terceira folha. Rede virtual e sub-rede enfatizadas.":::

## <a name="enable-private-endpoint-on-your-disk"></a>Habilitar o ponto de extremidade privado no seu disco

1. Procure o disco que deseja configurar
1. Selecione **Rede**
1. Selecione **Ponto de extremidade privado (por meio do acesso a disco)** e escolha o acesso a disco criado anteriormente.
1. Clique em **Salvar**.

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="Captura de tela da folha da rede do disco gerenciado. Realce da seleção de ponto de extremidade privado, bem como o acesso a disco selecionado. Salvar isso configura o disco para esse acesso.":::

Agora você concluiu a configuração de Links Privados que podem ser usados ao importar/exportar seu disco gerenciado.

## <a name="next-steps"></a>Próximas etapas

- [Perguntas frequentes sobre os Links Privados](./faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Exportar/copiar instantâneos gerenciados como VHD para uma conta de armazenamento em outa região com o PowerShell](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)