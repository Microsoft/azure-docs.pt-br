---
title: Máquinas virtuais Windows 7 Windows desktop virtual (clássico)-Azure
description: Como resolver problemas de VMs (máquinas virtuais) do Windows 7 em um ambiente de área de trabalho virtual do Windows (clássico).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 1d71b4472b16372927db56b20eca4fcbde0cc625
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88005407"
---
# <a name="troubleshoot-windows-7-virtual-machines-in-windows-virtual-desktop-classic"></a>Solucionar problemas de máquinas virtuais do Windows 7 na área de trabalho virtual do Windows (clássico)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager.

Use este artigo para solucionar problemas que você está tendo ao configurar as VMs (máquinas virtuais) do host de sessão de área de trabalho virtual do Windows.

## <a name="known-issues"></a>Problemas conhecidos

O Windows 7 em áreas de trabalho virtuais do Windows não oferece suporte aos seguintes recursos:

- Aplicativos virtualizados (RemoteApps)
- Redirecionamento de fuso horário
- Dimensionamento automático de DPI

A área de trabalho virtual do Windows só pode virtualizar áreas de trabalho completas para o Windows 7.

Embora não haja suporte para o dimensionamento automático de DPI, você pode alterar manualmente a resolução em sua máquina virtual clicando com o botão direito do mouse no ícone no cliente Área de Trabalho Remota e selecionando **resolução**.

## <a name="error-cant-access-the-remote-desktop-user-group"></a>Erro: não é possível acessar o grupo de usuários Área de Trabalho Remota

Se a área de trabalho virtual do Windows não conseguir localizar você ou as credenciais de seus usuários no grupo Área de Trabalho Remota usuário, você poderá ver uma das seguintes mensagens de erro:

- "Este usuário não é membro do grupo de usuários do Área de Trabalho Remota"
- "Você deve receber permissões para entrar por meio do Serviços de Área de Trabalho Remota"

Para corrigir esse erro, adicione o usuário ao grupo de usuários Área de Trabalho Remota:

1. Abra o portal do Azure.
2. Selecione a máquina virtual na qual você viu a mensagem de erro.
3. Selecione **executar um comando**.
4. Execute o seguinte comando com `<username>` substituído pelo nome do usuário que você deseja adicionar:

   ```cmd
   net localgroup "Remote Desktop Users" <username> /add
   ```