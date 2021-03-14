---
title: Criar uma oferta de máquina virtual do Azure no Azure Marketplace usando sua própria imagem
description: Saiba como publicar uma oferta de máquina virtual no Azure Marketplace usando sua própria imagem.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 03/10/2021
ms.openlocfilehash: 4711ea76af83594ec529cfda13a308fbe6646398
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200453"
---
# <a name="how-to-create-a-virtual-machine-using-your-own-image"></a>Como criar uma máquina virtual usando sua própria imagem

Este artigo descreve como criar e implantar uma imagem de VM (máquina virtual) fornecida pelo usuário.

> [!NOTE]
> Antes de iniciar este procedimento, examine os [requisitos técnicos](marketplace-virtual-machines.md#technical-requirements) das ofertas de VM do Azure, incluindo os requisitos de VHD (disco rígido virtual).

Para usar uma imagem de base aprovada em vez disso, siga as instruções em [criar uma imagem de VM de uma base aprovada](azure-vm-create-using-approved-base.md).

## <a name="configure-the-vm"></a>Configurar a VM

Esta seção descreve como dimensionar, atualizar e generalizar uma VM do Azure. Essas etapas são necessárias para preparar a VM para ser implantada no Azure Marketplace.

### <a name="size-the-vhds"></a>Dimensionar os VHDs

[!INCLUDE [Discussion of VHD sizing](includes/vhd-size.md)]

### <a name="install-the-most-current-updates"></a>Instalar as atualizações mais atuais

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-more-security-checks"></a>Executar mais verificações de segurança

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Executar configuração personalizada e tarefas agendadas

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

### <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens do Azure Marketplace devem ser reutilizáveis de uma forma genérica. Para obter isso, o VHD do sistema operacional deve ser generalizado, uma operação que remove todos os identificadores específicos de instância e drivers de software de uma VM.

## <a name="bring-your-image-into-azure"></a>Traga sua imagem para o Azure

Há três maneiras de trazer sua imagem para o Azure:

1. Carregue o VHD em uma galeria de imagens compartilhada (SIG).
1. Carregue o VHD em uma conta de armazenamento do Azure.
1. Extraia o VHD de uma imagem gerenciada (se estiver usando serviços de criação de imagens).

As três seções a seguir descrevem essas opções.

### <a name="option-1-upload-the-vhd-as-shared-image-gallery"></a>Opção 1: carregar o VHD como Galeria de imagens compartilhadas

1. Carregar VHD (s) na conta de armazenamento.
2. Na portal do Azure, procure **implantar um modelo personalizado**.
3. Selecione **Criar seu próprio modelo no editor**.
4. Copie o seguinte modelo de Azure Resource Manager (ARM).

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sourceStorageAccountResourceId": {
          "type": "string",
          "metadata": {
            "description": "Resource ID of the source storage account that the blob vhd resides in."
          }
        },
        "sourceBlobUri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk0Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "sourceBlobDataDisk1Uri": {
          "type": "string",
          "metadata": {
            "description": "Blob Uri of the vhd blob (must be in the storage account provided.)"
          }
        },
        "galleryName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Shared Image Gallery."
          }
        },
        "galleryImageDefinitionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Definition."
          }
        },
        "galleryImageVersionName": {
          "type": "string",
          "metadata": {
            "description": "Name of the Image Version - should follow <MajorVersion>.<MinorVersion>.<Patch>."
          }
        }
      },
      "resources": [
        {
          "type": "Microsoft.Compute/galleries/images/versions",
          "name": "[concat(parameters('galleryName'), '/', parameters('galleryImageDefinitionName'), '/', parameters('galleryImageVersionName'))]",
          "apiVersion": "2020-09-30",
          "location": "[resourceGroup().location]",
          "properties": {
            "storageProfile": {
              "osDiskImage": {
                "source": {
                  "id": "[parameters('sourceStorageAccountResourceId')]",
                  "uri": "[parameters('sourceBlobUri')]"
                }
              },
    
              "dataDiskImages": [
                {
                  "lun": 0,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk0Uri')]"
                  }
                },
                {
                  "lun": 1,
                  "source": {
                    "id": "[parameters('sourceStorageAccountResourceId')]",
                    "uri": "[parameters('sourceBlobDataDisk1Uri')]"
                  }
                }
              ]
            }
          }
        }
      ]
    }
    
    ```

5. Cole o modelo no editor.

    :::image type="content" source="media/create-vm/vm-sample-code-screen.png" alt-text="Tela de código de exemplo para VM.":::

1. Selecione **Salvar**.
1. Use os parâmetros nesta tabela para preencher os campos da tela a seguir.

| Parâmetros | Descrição |
| --- | --- |
| sourceStorageAccountResourceId | ID de recurso da conta de armazenamento de origem na qual o VHD de blob reside.<br><br>Para obter a ID do recurso, acesse sua **conta de armazenamento** em **portal do Azure**, vá para **Propriedades** e copie o valor **ResourceId** . |
| sourceBlobUri | URI de blob do blob VHD de disco do sistema operacional (deve estar na conta de armazenamento fornecida).<br><br>Para obter a URL do blob, acesse sua **conta de armazenamento** em **portal do Azure**, vá para o **blob** e copie o valor da **URL** . |
| sourceBlobDataDisk0Uri | URI do blob do blob de VHD do disco de dados (deve estar na conta de armazenamento fornecida). Se você não tiver um disco de dados, remova esse parâmetro do modelo.<br><br>Para obter a URL do blob, acesse sua **conta de armazenamento** em **portal do Azure**, vá para o **blob** e copie o valor da **URL** . |
| sourceBlobDataDisk1Uri | O URI de blob do blob de VHD do disco de dados adicional (deve estar na conta de armazenamento fornecida). Se você não tiver um disco de dados adicional, remova esse parâmetro do modelo.<br><br>Para obter a URL do blob, acesse sua **conta de armazenamento** em **portal do Azure**, vá para o **blob** e copie o valor da **URL** . |
| Galeria | Nome da Galeria de imagens compartilhadas |
| galleryImageDefinitionName | Nome da definição da imagem |
| galleryImageVersionName | Nome da versão da imagem a ser criada, neste formato: `<MajorVersion>.<MinorVersion>.<Patch>` |
|

:::image type="content" source="media/create-vm/custom-deployment-window.png" alt-text="Mostra a janela de implantação personalizada.":::

8. Selecione **Examinar + criar**. Após a conclusão da validação, selecione **criar**.

> [!TIP]
> A conta do Publicador deve ter acesso de "proprietário" para publicar a imagem SIG. Se necessário, siga as etapas abaixo para conceder acesso:
>
> 1. Vá para a Galeria de imagens compartilhadas (SIG).
> 2. Selecione **controle de acesso** (iam) no painel esquerdo.
> 3. Selecione **Adicionar** e **Adicionar atribuição de função**.
> 4. Para **função**, selecione **proprietário**.
> 5. Para **atribuir acesso a**, selecione **usuário, grupo ou entidade de serviço**.
> 6. Insira o email do Azure da pessoa que publicará a imagem.
> 7. Selecione **Salvar**.<br><br>
> :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="A janela Adicionar atribuição de função é mostrada.":::

### <a name="option-2-upload-the-vhd-to-a-storage-account"></a>Opção 2: carregar o VHD em uma conta de armazenamento

Configure e prepare a VM a ser carregada conforme descrito em [preparar um VHD do Windows ou VHDX para carregar no Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) ou [criar e carregar um VHD do Linux](../virtual-machines/linux/create-upload-generic.md).

### <a name="option-3-extract-the-vhd-from-managed-image-if-using-image-building-services"></a>Opção 3: extrair o VHD da imagem gerenciada (se estiver usando serviços de criação de imagens)

Se você estiver usando um serviço de criação de imagem como o [empacotador](https://www.packer.io/), talvez seja necessário extrair o VHD da imagem. Não há uma maneira direta de fazer isso. Você precisará criar uma VM e extrair o VHD do disco da VM.

## <a name="create-the-vm-on-the-azure-portal"></a>Criar a VM no portal do Azure

Siga estas etapas para criar a imagem de VM de base no [portal do Azure](https://ms.portal.azure.com/).

1. Entre no [portal do Azure](https://ms.portal.azure.com/).
2. Selecione **Máquinas virtuais**.
3. Selecione **+ Adicionar** para abrir a tela **criar uma máquina virtual** .
4. Selecione a imagem na lista suspensa ou selecione **procurar todas as imagens públicas e privadas** para pesquisar ou procurar todas as imagens de máquina virtual disponíveis.
5. Para criar uma VM de **Gen 2** , vá para a guia **avançado** e selecione a opção **Gen 2** .

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selecione Gen 1 ou Gen 2.":::

6. Selecione o tamanho da VM a ser implantada.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selecione um tamanho de VM recomendado para a imagem selecionada.":::

7. Forneça os outros detalhes necessários para criar a VM.
8. Selecione **Examinar + criar** para examinar suas opções. Quando a mensagem **validação aprovada** for exibida, selecione **criar**.

O Azure inicia o provisionamento da máquina virtual especificada. Acompanhe seu progresso selecionando a guia **máquinas virtuais** no menu à esquerda. Após a criação do status das alterações da máquina virtual para **execução**.

## <a name="connect-to-your-vm"></a>Conectar-se à sua VM

Consulte a documentação a seguir para se conectar à sua VM do [Windows](../virtual-machines/windows/connect-logon.md) ou [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) .

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Próximas etapas

- [Teste sua imagem de VM](azure-vm-image-test.md) para garantir que ela atenda aos requisitos de publicação do Azure Marketplace. Isso é opcional.
- Se você não quiser testar a imagem da VM, entre no [Partner Center](https://partner.microsoft.com/) e publique a imagem SIG (opção #1).
- Se você seguiu a opção #2 ou #3, [gere o URI de SAS](azure-vm-get-sas-uri.md).
- Se você encontrou dificuldades para criar seu novo VHD baseado no Azure, consulte [perguntas frequentes sobre a VM para o Azure Marketplace](azure-vm-create-faq.md).
