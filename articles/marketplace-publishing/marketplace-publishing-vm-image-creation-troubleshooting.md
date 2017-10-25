---
title: "Como solucionar problemas comuns durante a criação do VHD | Microsoft Docs"
description: "Respostas a perguntas comuns de solução de problemas e problemas durante a criação do VHD."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: 
editor: 
ms.assetid: e39563d8-8646-4cb7-b078-8b10ac35b494
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 09/26/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: c4e88a9fbb15dd90d619b159ae1065dfacc1907f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-common-issues-encountered-during-vhd-creation"></a>Como solucionar problemas comuns encontrados durante a criação do VHD
Este artigo é fornecido para ajudar um fornecedor e/ou coadministrador do Azure Marketplace que possa enfrentar um problema ou ter dúvidas comuns ao publicar ou gerenciar suas soluções de máquina virtual.

1. Como posso alterar o nome do host?
   
    Após a criação da VM, os usuários não poderão atualizar o nome do host.
2. Como redefinir o serviço de Área de Trabalho Remota ou sua senha de logon?
   
   * [Referência de VM do Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
   * [Referência de VM do Linux](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
3. Como posso gerar novos certificados ssh?
   
   Consulte o link: [https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)
4. Como configurar um certificado VPN aberta?
   
   Consulte o link: [https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/](https://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/)
5. Qual é a política de suporte para a execução de software de servidor da Microsoft no ambiente de máquina virtual do Microsoft Azure (uma infraestrutura como serviço)?
   
   Consulte o link: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
6. As máquinas virtuais têm algum identificador exclusivo?
   
   O Azure codifica a ID exclusiva da VM do Azure em cada VM. Consulte detalhes neste blog e na documentação.
7. Em uma VM, como posso gerenciar a extensão de script personalizado na tarefa de inicialização?
   
   Consulte o link: [https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/)
8. Como criar uma VM no Portal do Azure usando o VHD carregado no armazenamento premium?
   
   Ainda não oferecemos suporte a essa funcionalidade.
9. Um aplicativo de 32 bits tem suporte no Azure Marketplace?
   
   Consulte o link para obter detalhes sobre a política de suporte: [https://support.microsoft.com/kb/2721672](https://support.microsoft.com/kb/2721672)
10. Sempre que estou tentando criar uma imagem de minhas VHDs, recebo o erro "O .VHD já está registrado no repositório de imagens como o recurso"no PowerShell. Eu não criei qualquer imagem antes, nem encontrei qualquer imagem com esse nome no Azure. Como resolver isso?
    
    Geralmente, isso acontece se o usuário provisionou uma VM a partir desse VHD e houver um bloqueio nesse VHD. Verifique se não há uma VM alocada a partir desse VHD. Se o erro persistir, gere um tíquete de suporte usando este link ou no portal de publicação (encontre os detalhes na resposta da pergunta 11).