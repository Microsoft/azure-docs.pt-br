---
title: Excluir pool de hosts da área de trabalho virtual do Windows – Azure
description: Como excluir um pool de hosts na área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dfc9858bea468389d8ce90677f048e5d1fd3bb82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88007582"
---
# <a name="delete-a-host-pool"></a>Excluir um pool de host

Todos os pools de host criados na área de trabalho virtual do Windows são anexados a hosts de sessão e grupos de aplicativos. Para excluir um pool de hosts, você precisa excluir seus grupos de aplicativos e hosts de sessão associados. A exclusão de um grupo de aplicativos é bem simples, mas a exclusão de um host de sessão é mais complicada. Ao excluir um host de sessão, você precisa verificar se ele não tem nenhuma sessão de usuário ativa. Todas as sessões de usuário no host de sessão devem ser desconectadas para impedir que os usuários percam dados.

## <a name="delete-a-host-pool-with-powershell"></a>Excluir um pool de hosts com o PowerShell

Para excluir um pool de hosts usando o PowerShell, primeiro você precisa excluir todos os grupos de aplicativos no pool de hosts. Para excluir todos os grupos de aplicativos, execute o seguinte cmdlet do PowerShell:

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

Em seguida, execute este cmdlet para excluir o pool de hosts:

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

Esse cmdlet Remove todas as sessões de usuário existentes no host de sessão do pool de hosts. Ele também cancela o registro do host de sessão do pool de hosts. Todas as VMs (máquinas virtuais) relacionadas ainda existirão na sua assinatura.

## <a name="delete-a-host-pool-with-the-azure-portal"></a>Excluir um pool de hosts com o portal do Azure

Para excluir um pool de hosts no portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Pesquise e selecione **Área de Trabalho Virtual do Windows**.

3. Selecione **pools de host** no menu no lado esquerdo da página e, em seguida, selecione o nome do pool de hosts que você deseja excluir.

4. No menu no lado esquerdo da página, selecione **grupos de aplicativos**.

5. Selecione todos os grupos de aplicativos no pool de hosts que você vai excluir e, em seguida, selecione **remover**.

6. Depois de remover os grupos de aplicativos, vá para o menu no lado esquerdo da página e selecione **visão geral**.

7. Selecione **Remover**.

8. Se houver hosts de sessão no pool de hosts que você está excluindo, você verá uma mensagem solicitando sua permissão para continuar. Selecione **Sim** na barra superior.

9. O portal do Azure agora removerá todos os hosts de sessão e excluirá o pool de hosts. As VMs relacionadas ao host de sessão não serão excluídas e permanecerão em sua assinatura.

## <a name="next-steps"></a>Próximas etapas

Para saber como criar um pool de hosts, confira estes artigos:

- [Criar um pool de host com o portal do Azure](create-host-pools-azure-marketplace.md)
- [Criar um pool de host com o PowerShell](create-host-pools-powershell.md)

Para saber como definir as configurações do pool de hosts, confira estes artigos:

- [Personalizar propriedades de protocolo RDP para um pool de hosts](customize-rdp-properties.md)
- [Configurar o método de balanceamento de carga da Área de Trabalho Virtual do Windows](configure-host-pool-load-balancing.md)
- [Configurar o tipo de atribuição do pool de host da área de trabalho pessoal](configure-host-pool-personal-desktop-assignment-type.md)