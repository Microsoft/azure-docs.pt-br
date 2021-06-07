---
title: incluir arquivo
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 03/25/2021
ms.openlocfilehash: 8898a762e8a1e7a2d5c104f99d12032c676a5ca4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630051"
---
## <a name="generalize-the-image"></a>Generalizar a imagem

Todas as imagens do Azure Marketplace devem ser reutilizáveis de uma forma genérica. Para obter isso, o VHD do sistema operacional deve ser generalizado, uma operação que remove todos os identificadores específicos de instância e drivers de software de uma VM.

### <a name="for-windows"></a>Para Windows

Os discos do sistema operacional Windows são generalizados com a ferramenta [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Se, mais tarde, você atualizar ou reconfigurar o sistema operacional, deverá executar novamente o Sysprep.

> [!WARNING]
> Depois de executar o Sysprep, desligue a VM até que ela seja implantada, pois as atualizações podem ser executadas automaticamente. Esse desligamento evitará atualizações subsequentes de alterações específicas da instância no sistema operacional ou serviços instalados. Para obter mais informações sobre como executar o sysprep, confira [Etapas para generalizar um VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Para Linux

O processo a seguir generalizará uma VM do Linux e a reimplantará como uma VM separada. Para detalhes, confira [Como criar uma imagem de uma máquina virtual ou um VHD](../../virtual-machines/linux/capture-image.md). Você pode parar quando chegar à seção chamada "criar uma VM com base na imagem capturada".

1. Remova o agente Linux do Azure.
    1. Conecte-se à VM do Linux usando um cliente SSH.
    2. Na janela SSH, digite este comando: `sudo waagent –deprovision+user` .
    3. Digite Y para continuar (Você pode adicionar o parâmetro -force ao comando anterior para evitar essa etapa de confirmação).
    4. Após a conclusão do comando, digite **Exit** para fechar o cliente SSH.
2. Parar máquina virtual.
    1. No portal do Azure, selecione seu RG (grupo de recursos) e desaloque a VM.
    2. Sua VM agora está generalizada e você pode criar uma nova VM usando esse disco de VM.

### <a name="capture-image"></a>Capturar a imagem

Quando sua VM estiver pronta, você poderá capturá-la em uma galeria de imagens compartilhadas do Azure. Siga as etapas abaixo para capturar:

1. No [portal do Azure](https://ms.portal.azure.com/), vá para a página da sua máquina virtual.
2. Selecione **capturar**.
3. Em **compartilhar imagem com a Galeria de imagens compartilhadas**, selecione **Sim, compartilhe-a em uma galeria como uma versão de imagem**.
4. Em **estado do sistema operacional** , selecione generalizado.
5. Selecione uma galeria de imagens de destino ou **crie uma nova**.
6. Selecione uma definição de imagem de destino ou **crie uma nova**.
7. Forneça um **número de versão** para a imagem.
8. Selecione **Examinar + criar** para examinar suas opções.
9. Depois que a validação for aprovada, selecione **criar**.

A assinatura do Azure que contém o SIG deve estar no mesmo locatário que a conta de editor para publicar. Além disso, a conta do Publicador deve ter um proprietário de acesso ao SIG. 

Para conceder acesso:

1. Vá para a Galeria de imagens compartilhadas.
2. Selecione **controle de acesso** (iam) no painel esquerdo.
3. Selecione **Adicionar** e **Adicionar atribuição de função**.
4. Selecione uma **função** ou **proprietário**.
5. Em **atribuir acesso para** selecionar **usuário, grupo ou entidade de serviço**.
6. Selecione o email do Azure da pessoa que publicará a imagem.
7. Selecione **Salvar**.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Exibe a janela Adicionar atribuição de função.":::

> [!NOTE]
> Você não precisa gerar URIs SAS, já que agora você pode publicar uma imagem SIG no Partner Center. No entanto, se você ainda precisar consultar as etapas de geração de URI SAS, consulte [como gerar um URI de SAS para uma imagem de VM](../azure-vm-get-sas-uri.md).
