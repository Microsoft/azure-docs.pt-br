---
title: Expandir o pool de hosts existente com novos hosts de sessão-Azure
description: Como expandir um pool de hosts existente com novos hosts de sessão na área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b65560a3b10d04887040c4da1e137912810b3095
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91929584"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Expandir um pool de hosts existente com novos hosts de sessão

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica) sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md).

Ao aumentar o uso em seu pool de hosts, talvez seja necessário expandir seu pool de hosts existente com novos hosts de sessão para lidar com a nova carga.

Este artigo lhe dirá como você pode expandir um pool de hosts existente com novos hosts de sessão.

## <a name="what-you-need-to-expand-the-host-pool"></a>O que você precisa para expandir o pool de hosts

Antes de começar, verifique se você criou um pool de hosts e VMs (máquinas virtuais) de host de sessão usando um dos seguintes métodos:

- [Portal do Azure](./create-host-pools-azure-marketplace.md)
- [Criar um pool de host com o PowerShell](./create-host-pools-powershell.md)

Você também precisará das seguintes informações ao criar pela primeira vez o pool de hosts e as VMs de host de sessão:

- Tamanho da VM, imagem e prefixo do nome
- Credenciais de administrador de ingresso no domínio
- Nome da rede virtual e nome da sub-rede

## <a name="add-virtual-machines-with-the-azure-portal"></a>Adicionar máquinas virtuais com o portal do Azure

Para expandir o pool de hosts adicionando máquinas virtuais:

1. Entre no portal do Azure.

2. Pesquise e selecione **Área de Trabalho Virtual do Windows**.

3. No menu no lado esquerdo da tela, selecione **pools de hosts**e, em seguida, selecione o nome do pool de hosts ao qual você deseja adicionar máquinas virtuais.

4. Selecione **hosts de sessão** no menu no lado esquerdo da tela.

5. Selecione **+ Adicionar** para começar a criar o pool de hosts.

6. Ignore a guia noções básicas e, em vez disso, selecione a guia **detalhes da VM** . Aqui você pode exibir e editar os detalhes da VM (máquina virtual) que deseja adicionar ao pool de hosts.

7. Selecione o grupo de recursos em que você deseja criar as VMs e, em seguida, selecione a região. Você pode escolher a região atual que está usando ou uma nova região.

8. Insira o número de hosts de sessão que você deseja adicionar ao seu pool de hosts em **número de VMs**. Por exemplo, se você estiver expandindo o pool de hosts em cinco hosts, insira **5**.

    >[!NOTE]
    >Embora seja possível editar a imagem e o prefixo das VMs, não recomendamos editá-las se você tiver VMs com imagens diferentes no mesmo pool de hosts. Edite a imagem e o prefixo somente se você planeja remover VMs com imagens mais antigas do pool de hosts afetados.

9. Para obter as **informações da rede virtual**, selecione a rede virtual e a sub-rede às quais você deseja que as máquinas virtuais sejam unidas. Você pode selecionar a mesma rede virtual que seus computadores existentes usam atualmente ou escolher outro que seja mais adequado para a região que você selecionou na etapa 7.

10. Para a **conta de administrador**, insira o nome de usuário e a senha de domínio Active Directory associados à rede virtual selecionada. Essas credenciais serão usadas para unir as máquinas virtuais à rede virtual.

      >[!NOTE]
      >Verifique se os nomes de seu administrador estão em conformidade com as informações fornecidas aqui. E que não há MFA habilitada na conta.

11. Selecione a guia **marca** se você tiver marcas nas quais deseja agrupar as máquinas virtuais. Caso contrário, ignore esta guia.

12. Selecione a guia **revisar + criar** . Examine suas escolhas e, se tudo estiver correto, selecione **criar**.

## <a name="next-steps"></a>Próximas etapas

Agora que você expandiu o pool de hosts existente, você pode entrar em um cliente de área de trabalho virtual do Windows para testá-los como parte de uma sessão de usuário. Você pode se conectar a uma sessão com qualquer um dos seguintes clientes:

- [Conectar-se ao cliente da Área de Trabalho do Windows](./connect-windows-7-10.md)
- [Conectar-se ao cliente Web](./connect-web.md)
- [Conectar-se ao cliente Android](./connect-android.md)
- [Conectar-se ao cliente macOS](./connect-macos.md)
- [Conectar-se ao cliente iOS](./connect-ios.md)
