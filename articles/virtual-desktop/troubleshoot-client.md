---
title: Solucionar problemas Área de Trabalho Remota área de trabalho virtual do Windows cliente-Azure
description: Como resolver problemas ao configurar conexões de cliente em um ambiente de locatário da Área de Trabalho Virtual do Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 097c97d16cf62793d03ac42662267e0553383bc1
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539623"
---
# <a name="troubleshoot-the-remote-desktop-client"></a>Solucionar problemas do cliente do Área de Trabalho Remota

Este artigo descreve problemas comuns com o cliente Área de Trabalho Remota e como corrigi-los.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Área de Trabalho Remota cliente para Windows 7 ou Windows 10 para de responder ou não pode ser aberto

A partir da versão 1.2.790, você pode redefinir os dados do usuário da página sobre ou usando um comando.

Use o comando a seguir para remover os dados do usuário, restaurar as configurações padrão e cancelar a assinatura de todos os espaços de trabalho.

```cmd
msrdcw.exe /reset [/f]
```

Se você estiver usando uma versão anterior do cliente Área de Trabalho Remota, recomendamos desinstalar e reinstalar o cliente.

## <a name="web-client-wont-open"></a>O cliente Web não será aberto

Primeiro, teste sua conexão com a Internet abrindo outro site em seu navegador; por exemplo, [www.Bing.com](https://www.bing.com).

Use **nslookup** para confirmar se o DNS pode resolver o FQDN:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Tente se conectar com outro cliente, como Área de Trabalho Remota Client para Windows 7 ou Windows 10, e verifique se você pode abrir o cliente Web.

### <a name="cant-open-other-websites-while-connected-to-the-web-client"></a>Não é possível abrir outros sites enquanto estiver conectado ao cliente Web

Se você não puder abrir outros sites enquanto estiver conectado ao cliente Web, pode haver problemas de conexão de rede ou uma interrupção de rede. Recomendamos que você entre em contato com o suporte de rede.

### <a name="nslookup-cant-resolve-the-name"></a>Nslookup não pode resolver o nome

Se nslookup não puder resolver o nome, poderá haver problemas de conexão de rede ou uma interrupção de rede. Recomendamos que você entre em contato com o suporte de rede.

### <a name="your-client-cant-connect-but-other-clients-on-your-network-can-connect"></a>O cliente não pode se conectar, mas outros clientes em sua rede podem se conectar

Se seu navegador começar a funcionar ou parar de trabalhar enquanto você estiver usando o cliente Web, siga estas instruções para solucionar o problema:

1. Reinicie o navegador.
2. Limpar cookies do navegador. Consulte [como excluir arquivos de cookie no Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Limpe o cache do navegador. Consulte [Limpar cache do navegador para seu navegador](https://binged.it/2RKyfdU).
4. Abra o navegador em modo privado.

## <a name="client-doesnt-show-my-resources"></a>O cliente não mostra meus recursos

Primeiro, verifique a conta de Azure Active Directory que você está usando. Se já tiver entrado com uma conta do Azure Active Directory diferente da que deseja usar para a Área de Trabalho Virtual do Windows, você deverá sair ou usar uma janela de navegador privada.

Se você estiver usando a área de trabalho virtual do Windows (clássico), use o link do cliente Web neste [artigo](./virtual-desktop-fall-2019/connect-web-2019.md) para se conectar aos seus recursos.

Se isso não funcionar, verifique se o grupo de aplicativos está associado a um espaço de trabalho.

## <a name="web-client-stops-responding-or-disconnects"></a>O cliente Web para de responder ou desconexões

Tente se conectar usando outro navegador ou cliente.

### <a name="other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Outros navegadores e clientes também têm mau funcionamento ou falha ao abrir

Se os problemas continuarem mesmo depois de você ter alternado os navegadores, o problema poderá não ser com o navegador, mas com sua rede. Recomendamos que você entre em contato com o suporte de rede.

## <a name="web-client-keeps-prompting-for-credentials"></a>O cliente Web continua solicitando credenciais

Se o cliente Web continuar solicitando credenciais, siga estas instruções:

1. Confirme se a URL do cliente Web está correta.
2. Confirme se as credenciais que você está usando são para o ambiente de área de trabalho virtual do Windows vinculado à URL.
3. Limpar cookies do navegador. Para obter mais informações, consulte [como excluir arquivos de cookie no Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Limpe o cache do navegador. Para obter mais informações, consulte [limpar o cache do navegador para seu navegador](https://binged.it/2RKyfdU).
5. Abra seu navegador em modo privado.

## <a name="windows-client-blocks-windows-virtual-desktop-classic-feed"></a>O Windows Client bloqueia o feed da área de trabalho virtual do Windows (clássico)

Se o feed do cliente do Windows não mostrar aplicativos da área de trabalho virtual do Windows (clássico), siga estas instruções:

1. Verifique se a política de acesso condicional inclui as IDs de aplicativo associadas à área de trabalho virtual do Windows (clássico).
2. Verifique se a política de acesso condicional bloqueia todo o acesso, exceto as IDs de aplicativo da área de trabalho virtual do Windows (clássica). Nesse caso, você precisará adicionar a ID do aplicativo **9cdead84-A844-4324-93f2-b2e6bb768d07** à política para permitir que o cliente descubra os feeds.

Se não for possível localizar a ID do aplicativo 9cdead84-A844-4324-93f2-b2e6bb768d07 na lista, você precisará registrar o provedor de recursos da área de trabalho virtual do Windows. Para registrar o provedor de recursos:

1. Entre no portal do Azure.
2. Acesse **assinatura** e selecione sua assinatura.
3. No menu no lado esquerdo da página, selecione **provedor de recursos**.
4. Localize e selecione **Microsoft. DesktopVirtualization** e, em seguida, selecione **registrar novamente**.

## <a name="next-steps"></a>Próximas etapas

- Confira uma visão geral da solução de problemas da Área de Trabalho Virtual do Windows e das faixas de escalonamento em [Visão geral da solução de problemas, comentários e suporte](troubleshoot-set-up-overview.md).
- Para solucionar problemas ao criar um ambiente de Área de Trabalho Virtual do Windows e pool de host em um ambiente da Área de Trabalho Virtual do Windows, consulte [Criação de ambiente e pool de host](troubleshoot-set-up-issues.md).
- Veja como solucionar problemas ao configurar uma VM (máquina virtual) na Área de Trabalho Virtual do Windows em [Configuração da máquina virtual do host da sessão](troubleshoot-vm-configuration.md).
- Para solucionar problemas relacionados ao agente de área de trabalho virtual do Windows ou conectividade de sessão, consulte [solucionar problemas comuns do agente de área de trabalho virtual do Windows](troubleshoot-agent.md).
- Veja como solucionar problemas ao usar o PowerShell com a Área de Trabalho Virtual do Windows em [PowerShell da Área de Trabalho Virtual do Windows](troubleshoot-powershell.md).
- Acompanhe um tutorial de solução de problemas em [Tutorial: Solucionar problemas de implantações de modelos do Resource Manager](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
