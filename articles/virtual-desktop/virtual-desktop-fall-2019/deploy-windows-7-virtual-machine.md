---
title: Implantar máquina virtual do Windows 7 na Área de Trabalho Virtual do Windows (clássico) – Azure
description: Como configurar e implantar uma máquina virtual do Windows 7 na Área de Trabalho Virtual do Windows (clássica).
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 2659e14490cf7d0aab32d8783a744478027f0a58
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106444931"
---
# <a name="deploy-a-windows-7-virtual-machine-on-windows-virtual-desktop-classic"></a>Implantar uma máquina virtual do Windows 7 na Área de Trabalho Virtual do Windows (clássico)

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows (clássica), que não é compatível com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver tentando gerenciar objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager introduzidos na versão atual da Área de Trabalho Virtual do Windows, confira [este artigo](../deploy-windows-7-virtual-machine.md).

O processo para implantar uma VM (máquina virtual) do Windows 7 na Área de Trabalho Virtual do Windows é um pouco diferente do processo para VMs que executam versões posteriores do Windows. Este guia explicará como implantar o Windows 7.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, siga as instruções em [Criar um pool de host com o PowerShell](create-host-pools-powershell-2019.md) para criar um pool de host. Depois disso, siga as instruções em [Criar pools de hosts no Azure Marketplace](create-host-pools-azure-marketplace-2019.md#optional-assign-additional-users-to-the-desktop-application-group) para atribuir um ou mais usuários ao grupo de aplicativos da área de trabalho.

## <a name="configure-a-windows-7-virtual-machine"></a>Configurar uma máquina virtual do Windows 7

Depois de concluir os pré-requisitos, você estará pronto para configurar sua VM do Windows 7 para implantação na Área de Trabalho Virtual do Windows.

Para configurar uma VM do Windows 7 na Área de Trabalho Virtual do Windows:

1. Entre no portal do Azure e pesquise a imagem do Windows 7 Enterprise ou carregue a sua imagem personalizada do Windows 7 Enterprise (x64).
2. Implante uma ou várias máquinas virtuais com o Windows 7 Enterprise como sistema operacional de host dela. Verifique se as máquinas virtuais permitem protocolo RDP (RDP) (a porta TCP/3389).
3. Conecte-se ao host do Windows 7 Enterprise usando o RDP e faça a autenticação com as credenciais que você definiu ao configurar a implantação.
4. Adicione a conta usada ao conectar-se ao host com RDP ao grupo "Usuário da Área de Trabalho Remota". Se você não fizer isso, talvez não consiga se conectar à VM depois de ingressá-la em seu domínio do Active Directory.
5. Acesse o Windows Update em sua VM.
6. Instale todas as atualizações do Windows na categoria Importante.
7. Instale todas as atualizações do Windows na categoria Opcional (excluindo os pacotes de idiomas). Isso instala a atualização do protocolo RDP 8.0 ([KB2592687](https://www.microsoft.com/download/details.aspx?id=35387)) que você precisa para concluir estas instruções.
8. Abra o Editor de Política de Grupo Local e navegue até **Configuração do Computador** > **Modelos Administrativos** > **Componentes do Windows** > **Serviços de Área de Trabalho Remota** > **Host da Sessão da Área de Trabalho Remota** > **Ambiente de Sessão Remota**.
9. Habilite a política do protocolo RDP 8.0.
10. Ingresse essa VM ao seu domínio do Active Directory.
11. Reinicie a máquina virtual executando o seguinte comando:

     ```cmd
     shutdown /r /t 0
     ```

12. Siga as instruções [aqui](/powershell/module/windowsvirtualdesktop/export-rdsregistrationinfo/) para obter um token de registro.
13. [Baixe o Agente da Área de Trabalho Virtual do Windows para Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3JZCm).
14. [Baixe o Gerenciador de Agentes da Área de Trabalho Virtual do Windows para Windows 7](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE3K2e3).
15. Abra o instalador do Agente da Área de Trabalho Virtual do Windows e siga as instruções. Quando solicitado, forneça a chave de registro que você criou na etapa 12.
16. Abra o Gerenciador de Agentes da Área de Trabalho Virtual do Windows e siga as instruções.
17. Opcionalmente, bloqueie a porta TCP/3389 para remover o acesso direto do protocolo RDP à VM.
18. Opcionalmente, confirme se o .NET Framework tem pelo menos a versão 4.7.2. Isso será especialmente importante se você estiver criando uma imagem personalizada.

## <a name="next-steps"></a>Próximas etapas

Sua implantação da Área de Trabalho Virtual do Windows agora está pronta para uso. [Baixe a versão mais recente do cliente da Área de Trabalho Virtual do Windows](https://aka.ms/wvd/clients/windows) para começar.

Para obter uma lista de problemas conhecidos e instruções de solução de problemas para o Windows 7 sobre a Área de Trabalho Virtual do Windows, confira nosso artigo de solução de problemas em [Solucionar problemas de máquinas virtuais do Windows 7 na Área de Trabalho Virtual do Windows](troubleshoot-windows-7-vm.md).
