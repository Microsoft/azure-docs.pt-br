---
title: Habilitar a virtualização aninhada em uma VM de modelo no Azure Lab Services (script) | Microsoft Docs
description: Saiba como criar uma VM de modelo com várias VMs no usando um script.  Em outras palavras, habilite a virtualização aninhada em uma VM de modelo no Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5ae50bd11ab9a8adb769920f6d473a2ff2ce9342
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91251488"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Habilitar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services usando um script

A virtualização aninhada permite que você crie um ambiente de várias VMs dentro da máquina virtual de modelo de um laboratório. A publicação do modelo fornecerá cada usuário no laboratório com uma máquina virtual configurada com várias VMs dentro dela.  Para obter mais informações sobre virtualização e Azure Lab Services aninhados, consulte [habilitar a virtualização aninhada em uma máquina virtual de modelo no Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

As etapas neste artigo se concentram na configuração da virtualização aninhada para o Windows Server 2016, o Windows Server 2019 ou o Windows 10. Você usará um script para configurar a máquina de modelo com o Hyper-V.  As etapas a seguir irão orientá-lo sobre como usar os [scripts do Hyper-V dos serviços de laboratório](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV).

>[!IMPORTANT]
>Selecione **Grande (virtualização aninhada)** ou **Médio (virtualização aninhada)** para o tamanho da máquina virtual ao criar o laboratório.  Caso contrário, a virtualização aninhada não funcionará.  

## <a name="run-script"></a>Executar Script

1. Se você estiver usando o Internet Explorer, talvez seja necessário adicionar `https://github.com` à lista de sites confiáveis.
    1. Abra o Internet Explorer.
    1. Selecione o ícone de engrenagem e escolha **Opções da Internet**.  
    1. Quando a caixa de diálogo **Opções da Internet** for exibida, selecione **segurança**, selecione **sites confiáveis**, clique no botão **sites** .
    1. Quando a caixa de diálogo **sites confiáveis** for exibida, adicione `https://github.com` à lista de sites confiáveis e selecione **Fechar**.

        ![Sites confiáveis](./media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Baixe os arquivos do repositório git conforme descrito nas etapas a seguir.
    1. Vá para  [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/) .
    1. Clique no botão **clonar ou baixar** .
    1. Clique em **baixar zip**.
    1. Extrair o arquivo ZIP

    >[!TIP]
    >Você também pode clonar o repositório git em [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git) .

1. Inicie o **PowerShell** no modo de **administrador** .
1. Na janela do PowerShell, navegue até a pasta com o script baixado. Se você estiver navegando da pasta superior dos arquivos de repositório, o script estará localizado em `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\` .
1. Talvez seja necessário alterar a política de execução para executar o script com êxito. Execute o comando a seguir:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Executar o script:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > O script pode exigir que o computador seja reiniciado. Siga as instruções do script e execute novamente o script até que o **script concluído** seja visto na saída.
1. Não se esqueça de redefinir a política de execução. Execute o comando a seguir:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Conclusão

Agora seu computador de modelo está pronto para criar máquinas virtuais do Hyper-V. Consulte [criar uma máquina virtual no Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v) para obter instruções sobre como criar máquinas virtuais do Hyper-v. Além disso, consulte [o centro de avaliação da Microsoft](https://www.microsoft.com/evalcenter/) para conferir o software e os sistemas operacionais disponíveis.  

## <a name="next-steps"></a>Próximas etapas

As próximas etapas são comuns à configuração de qualquer laboratório.

- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de registro de email para alunos](how-to-configure-student-usage.md#send-invitations-to-users)