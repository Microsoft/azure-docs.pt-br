---
title: Configurações de firewall para o Azure Lab Services
description: Saiba como determinar o endereço IP público e o intervalo de números da porta de máquinas virtuais em um laboratório para que as informações possam ser adicionadas às regras de firewall.
author: emaher
ms.author: enewman
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: 067a2c9672b87974557f650cef07b0394e7d5a63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "85445841"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Configurações de firewall para o Azure Lab Services

Cada organização ou escola vai configurar sua própria rede de uma forma que melhor atenda às suas próprias necessidades.  Às vezes, isso inclui definir regras de firewall que bloqueiam conexões de protocolo RDP ou SSH (Secure Shell) para computadores fora da rede da entidade.  Já que o Azure Lab Services é executado na nuvem pública, um pouco de configuração adicional talvez seja necessária para permitir que os alunos acessem a VM deles ao se conectarem da rede do campus.

Cada laboratório usa apenas um endereço IP público e várias portas.  Todas as VMs, tanto a VM de modelo quanto as VMs de aluno, usarão esse endereço IP público.  O endereço IP público não será alterado durante a vida útil do laboratório.  No entanto, cada VM terá um número da porta diferente.  Os números de porta variam de 49152 a 65535.  A combinação de endereço IP público e número da porta é usada para conectar o instrutor e os alunos à VM correta.  Este artigo explicará como encontrar o endereço IP público específico usado por um laboratório.  Essas informações podem ser usadas para atualizar as regras de firewall de entrada e saída para que os alunos possam acessar as respectivas VMs.

>[!IMPORTANT]
>Cada laboratório terá um endereço IP público diferente.

## <a name="find-public-ip-for-a-lab"></a>Localizar IP público para um laboratório

Os endereços IP públicos de cada laboratório são listados na página **Todos os laboratórios** da conta do laboratório do Lab Services.  Para obter instruções sobre como encontrar a página **Todos os laboratórios**, confira [Exibir laboratórios em uma conta de laboratório](manage-labs.md#view-labs-in-a-lab-account).  

> [!div class="mx-imgBorder"]
> ![Página Todos os laboratórios](./media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Você não verá o endereço IP público se o computador de modelo do seu laboratório ainda não estiver publicado.

## <a name="conclusion"></a>Conclusão

Agora sabemos o endereço IP público do laboratório.  As regras de entrada e de saída podem ser criadas para o firewall da organização para o endereço IP público e o intervalo de portas 49152-65535.  Depois que as regras são atualizadas, os alunos podem acessar as respectivas VMs sem que o firewall de rede bloqueie o acesso.

## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Permitir que o criador do laboratório escolha a localização do laboratório](allow-lab-creator-pick-lab-location.md)
- [Conectar a rede do seu laboratório a uma rede virtual par](how-to-connect-peer-virtual-network.md)
- [Anexar uma galeria de imagens compartilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicionar um usuário como um proprietário do laboratório](how-to-add-user-lab-owner.md)
- [Exibir configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Definir outras configurações para um laboratório](how-to-configure-lab-accounts.md)
