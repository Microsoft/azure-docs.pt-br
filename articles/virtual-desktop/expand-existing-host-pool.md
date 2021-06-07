---
title: Expandir o pool de host existente com novos hosts de sessão – Azure
description: Como expandir um pool de host existente com novos hosts de sessão na Área de Trabalho Virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9c73d7434a002a5efc7d058095eb9743a7f3ebf8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446818"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Expandir um pool de host existente com novos hosts de sessão

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md).

Ao aumentar o uso no seu pool de host, talvez seja necessário expandir o pool de host existente com novos hosts de sessão para lidar com a nova carga.

Este artigo descreve como você pode expandir um pool de host existente com novos hosts de sessão.

## <a name="what-you-need-to-expand-the-host-pool"></a>Do que você precisa para expandir o pool de host

Antes de começar, verifique se você criou um pool de host e VMs (máquinas virtuais) de host da sessão usando um dos seguintes métodos:

- [Portal do Azure](./create-host-pools-azure-marketplace.md)
- [Criar um pool de host com o PowerShell](./create-host-pools-powershell.md)

Você também precisará das seguintes informações de quando criou pela primeira vez o pool de host e as VMs de host da sessão:

- Tamanho da VM, imagem e prefixo do nome
- Credenciais de administrador de ingresso no domínio
- Nome da rede virtual e nome da sub-rede

## <a name="add-virtual-machines-with-the-azure-portal"></a>Adicionar máquinas virtuais com o portal do Azure

Para expandir o pool de host adicionando máquinas virtuais:

1. Entre no portal do Azure.

2. Pesquise e selecione **Área de Trabalho Virtual do Windows**.

3. No menu no lado esquerdo da tela, selecione **Pools de Host** e selecione o nome do pool de host ao qual você deseja adicionar máquinas virtuais.

4. Selecione **Hosts da sessão** no menu no lado esquerdo da tela.

5. Selecione **+Adicionar** para começar a criar o pool de host.

6. Ignore a guia Básico e selecione a guia **Detalhes da VM**. Aqui você pode ver e editar os detalhes da VM (máquina virtual) que deseja adicionar ao pool de host.

7. Selecione o grupo de recursos em que você deseja criar as VMs e selecione a região. Você pode escolher a região atual que está usando ou uma nova região.

8. Insira o número de hosts de sessão que você deseja adicionar ao pool de host em **Número de VMs**. Por exemplo, se você estiver expandindo o pool de host em cinco hosts, insira **5**.

    >[!NOTE]
    >Embora seja possível editar a imagem e o prefixo das VMs, não recomendamos editá-las se você tiver VMs com imagens diferentes no mesmo pool de host. Edite a imagem e o prefixo somente se você planeja remover VMs com imagens mais antigas do pool de host afetado.

9. Para obter as **informações da rede virtual**, selecione a rede virtual e a sub-rede às quais você deseja que as máquinas virtuais sejam unidas. Você pode selecionar a mesma rede virtual que os computadores existentes usam no momento ou escolher outra que seja mais adequada para a região que você selecionou na etapa 7.

10. Para a **Conta de administrador**, insira o nome de usuário e a senha de domínio Active Directory associados à rede virtual selecionada. Essas credenciais serão usadas para unir as máquinas virtuais à rede virtual.

      >[!NOTE]
      >Verifique se os nomes de administrador estão em conformidade com as informações fornecidas aqui. e se não há MFA habilitada na conta.

11. Selecione a guia **Marca** se você tiver marcas nas quais deseja agrupar as máquinas virtuais. Caso contrário, ignore essa guia.

12. Selecione a guia **Examinar + Criar**. Examine suas escolhas e, se tudo estiver correto, selecione **Criar**.

## <a name="next-steps"></a>Próximas etapas

Agora que você expandiu o pool de host existente, pode entrar em um cliente da Área de Trabalho Virtual do Windows para testá-lo como parte de uma sessão de usuário. Você pode se conectar a uma sessão com qualquer um dos seguintes clientes:

- [Conectar-se ao cliente da Área de Trabalho do Windows](./connect-windows-7-10.md)
- [Conectar-se ao cliente Web](./connect-web.md)
- [Conectar-se ao cliente Android](./connect-android.md)
- [Conectar-se ao cliente macOS](./connect-macos.md)
- [Conectar-se ao cliente iOS](./connect-ios.md)
