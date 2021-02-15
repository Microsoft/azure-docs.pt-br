---
title: Mensagens de erro específicas do RDP para VMs do Azure | Microsoft Docs
description: Entender as mensagens de erro específicas que você pode receber ao tentar usar a conexão de Área de Trabalho Remota para uma máquina virtual Windows no Azure
keywords: Erro de área de trabalho remota, erro de conexão de área de trabalho remota, não é possível conectar-se à VM, solução de problemas da área de trabalho remota
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 5feb1d64-ee6f-4907-949a-a7cffcbc6153
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 808443cb805b2dee2426198f9cd4f7ba502d8999
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2021
ms.locfileid: "98197499"
---
# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Solucionar problemas de mensagens de erro específicas ao RDP para uma VM do Windows no Azure
Você pode receber mensagens de erro específicas ao usar a conexão de Área de Trabalho Remota para uma VM (máquina virtual) do Windows no Azure. Este artigo detalha algumas das mensagens de erro mais comuns encontradas, junto com as etapas para resolvê-las. Se você estiver enfrentando problemas para se conectar à sua VM usando o RDP, mas não encontrar uma mensagem de erro específica, consulte o [guia de solução de Área de Trabalho Remota](troubleshoot-rdp-connection.md).

Para obter informações sobre mensagens de erro específicas, consulte o seguinte:

* [A sessão remota foi desconectada porque não há Servidores de Licença da Área de Trabalho Remota disponíveis para fornecer uma licença](#rdplicense).
* [A Área de Trabalho Remota não consegue localizar o "nome" do computador](#rdpname).
* [Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contatada](#rdpauth).
* [Erro de segurança do Windows: suas credenciais não funcionaram](#wincred).
* [Este computador não pode se conectar ao computador remoto](#rdpconnect).

<a id="rdplicense"></a>

## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>A sessão remota foi desconectada porque não há servidores de licença Área de Trabalho Remota disponíveis para fornecer uma licença.
Causa: o período de cortesia de licenciamento de 120 dias para a função de Servidor de Área de Trabalho Remota expirou e você precisa instalar licenças.

Como alternativa, salve uma cópia local do arquivo RDP do portal e execute este comando no prompt de comando do PowerShell para conectar-se. Essa etapa desabilita o licenciamento apenas para esta conexão:

```powershell
mstsc <File name>.RDP /admin
```

Se não precisar realmente de mais de duas conexões simultâneas de Área de Trabalho Remota à VM, você poderá usar o Gerenciador do Servidor para remover a função de Servidor de Área de Trabalho Remota.

Para saber mais, confira a postagem no blog [Azure VM fails with "No Remote Desktop License Servers available" (A VM do Azure falha com a mensagem "Nenhum Servidor de Licença da Área de Trabalho Remota disponível")](/archive/blogs/mast/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available).

<a id="rdpname"></a>

## <a name="remote-desktop-cant-find-the-computer-name"></a>A Área de Trabalho Remota não consegue localizar o “nome” do computador.
Causa: o cliente da Área de Trabalho Remota em seu computador não pode resolver o nome do computador nas configurações do arquivo RDP.

Soluções possíveis:

* Se você estiver na intranet de uma organização, certifique-se que o computador tem acesso ao servidor proxy e que pode enviar o tráfego HTTPS para ele.
* Se você estiver usando um arquivo RDP armazenado localmente, tente usar aquele gerado pelo portal. Esta etapa garante que você tenha o nome DNS correto para a máquina virtual ou para o serviço de nuvem e a porta do ponto de extremidade da VM. Aqui está um arquivo RDP de exemplo gerado pelo portal:

    ```output
    full address:s:tailspin-azdatatier.cloudapp.net:55919
    prompt for credentials:i:1
    ```

A parte do endereço desse arquivo RDP tem:

* O nome de domínio totalmente qualificado do serviço de nuvem que contém a VM ("tailspin-azdatatier.cloudapp.net" neste exemplo).
* A porta TCP externa do ponto de extremidade do tráfego de Área de Trabalho Remota (55919).

<a id="rdpauth"></a>

## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Ocorreu um erro de autenticação. A Autoridade de Segurança Local não pode ser contatada.
Causa: a VM de destino não pôde localizar a autoridade de segurança na parte do nome de usuário das suas credenciais.

Quando seu nome de usuário estiver no formato *SecurityAuthority* \\ *username* (exemplo: CORP\User1), a parte *SecurityAuthority* será o nome do computador da VM (para a autoridade de segurança local) ou um nome de domínio Active Directory.

Soluções possíveis:

* Se a conta for local para a VM, verifique se o nome da VM está escrito corretamente.
* Se a conta estiver em um domínio do Active Directory, verifique a ortografia do nome do domínio.
* Se ela for uma conta de domínio do Active Directory e o nome de domínio estiver digitado corretamente, verifique se há um controlador de domínio disponível nesse domínio. É um problema comum em redes virtuais do Azure que contêm controladores de domínio que um controlador de domínio não esteja disponível porque ele ainda não foi iniciado. Como alternativa, você pode usar uma conta de administrador local em vez de uma conta de domínio.

<a id="wincred"></a>

## <a name="windows-security-error-your-credentials-did-not-work"></a>Erro de Segurança do Windows: suas credenciais não funcionaram.
Causa: a VM de destino não pôde validar seu nome de conta e senha.

Um computador baseado em Windows pode validar as credenciais de uma conta local ou de uma conta de domínio.

* Para contas locais, use a sintaxe *ComputerName* \\ *nome de usuário* (exemplo: SQL1\Admin4798).
* Para contas de domínio, use a sintaxe de nome de usuário *DomainName* \\  (exemplo: CONTOSO\peterodman).

Se você promoveu sua VM a um controlador de domínio em uma nova floresta do Active Directory, a conta de administrador local à qual você está conectado também será convertida em uma conta equivalente com a mesma senha na nova floresta e domínio. A conta local é então excluída.

Por exemplo, se você estiver conectado à conta local DC1\DCAdmin e tiver promovido a máquina virtual como um controlador de domínio em uma nova floresta para o domínio corp.contoso.com, a conta local DC1\DCAdmin será excluída, e uma nova conta de domínio (CORP\DCAdmin) será criada com a mesma senha.

Verifique o nome da conta para garantir que ele é um nome que a máquina virtual pode verificar como uma conta válida e que a senha está correta.

Se você precisar alterar a senha da conta de administrador local, consulte [Como redefinir uma senha ou o serviço de Área de Trabalho Remota para máquinas virtuais do Windows](reset-rdp.md).

<a id="rdpconnect"></a>

## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Este computador não pode se conectar ao computador remoto.
Causa: a conta que você usou para se conectar não tem direitos de entrada na Área de Trabalho Remota.

Todo computador Windows tem um grupo local Usuários da Área de Trabalho Remota que contém as contas e os grupos que podem se conectar remotamente. Os membros do grupo local de administradores também têm acesso, mesmo que essas contas não sejam listadas no grupo local de usuários da Área de Trabalho Remota. Para computadores que ingressaram no domínio, o grupo local de administradores também contém os administradores de domínio para o domínio.

Certifique-se que a conta que você está usando para conectar-se tem direitos de entrada da Área de Trabalho Remota. Como alternativa, use uma conta de administrador local ou domínio para se conectar na Área de Trabalho Remota. Para adicionar a conta desejada ao grupo local de usuários da Área de Trabalho Remota, use o snap-in do Console de Gerenciamento Microsoft (**Ferramentas do sistema > Usuários e Grupos Locais > Grupos > Usuários da Área de Trabalho Remota**).

## <a name="next-steps"></a>Próximas etapas
Se nenhum desses erros ocorrerem e você tiver um problema desconhecido com a conexão usando o RDP, consulte o [guia de solução da Área de Trabalho Remota](troubleshoot-rdp-connection.md).

* Para obter etapas de solução de problema de acesso a aplicativos em execução, consulte [Solucionar problemas de acesso a um aplicativo em execução em uma VM do Azure](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json).
* Se você estiver enfrentando problemas ao usar o Secure Shell (SSH) para se conectar a uma VM do Linux no Azure, consulte [Solucionar problemas de conexões SSH a uma VM do Linux no Azure](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).
