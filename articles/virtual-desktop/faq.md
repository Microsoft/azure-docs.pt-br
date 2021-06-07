---
title: Perguntas frequentes sobre área de trabalho virtual do Windows-Azure
description: Perguntas frequentes e práticas recomendadas para a área de trabalho virtual do Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: ffea2d84f1a5149670976beef3b9af847ae31a35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582129"
---
# <a name="windows-virtual-desktop-faq"></a>Perguntas frequentes da Área de Trabalho Virtual do Windows

Este artigo responde às perguntas frequentes e explica as práticas recomendadas para a área de trabalho virtual do Windows.

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>Quais são as permissões de administrador mínimas necessárias para gerenciar objetos?

Se você quiser criar pools de hosts e outros objetos, deverá ser atribuída a função Colaborador na assinatura ou no grupo de recursos com o qual você está trabalhando.

Você deve ser atribuído à função de administrador de acesso do usuário em um grupo de aplicativos para publicar grupos de aplicativos para usuários ou grupos de usuários.

Para restringir um administrador a gerenciar apenas sessões de usuário, como enviar mensagens para usuários, desconectar usuários e assim por diante, você pode criar funções personalizadas. Por exemplo:

```powershell
"actions": [
"Microsoft.Resources/deployments/operations/read",
"Microsoft.Resources/tags/read",
"Microsoft.Authorization/roleAssignments/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
],
"notActions": [],
"dataActions": [],
"notDataActions": []
}
```

## <a name="does-windows-virtual-desktop-support-split-azure-active-directory-models"></a>O Windows Virtual Desktop dá suporte a modelos de Azure Active Directory divididos?

Quando um usuário é atribuído a um grupo de aplicativos, o serviço faz uma simples atribuição de função do Azure. Como resultado, o Azure Active Directory do usuário (AD) e o Azure AD do grupo de aplicativos devem estar no mesmo local. Todos os objetos de serviço, como pools de hosts, grupos de aplicativos e espaços de trabalho, também devem estar no mesmo Azure AD que o usuário.

Você pode criar VMs (máquinas virtuais) em um Azure AD diferente, contanto que sincronize o Active Directory com o Azure AD do usuário na mesma rede virtual (VNET).

## <a name="what-are-location-restrictions"></a>O que são restrições de local?

Todos os recursos de serviço têm um local associado a eles. O local de um pool de hosts determina a qual geografia os metadados de serviço para o pool de hosts são armazenados. Um grupo de aplicativos não pode existir sem um pool de hosts. Se você adicionar aplicativos a um grupo de aplicativos do RemoteApp, também precisará de um host de sessão para determinar os aplicativos do menu iniciar. Para qualquer ação de grupo de aplicativos, você também precisará de um acesso de dados relacionado no pool de hosts. Para garantir que os dados não sejam transferidos entre vários locais, o local do grupo de aplicativos deve ser o mesmo que o pool de hosts.

Os espaços de trabalho também devem estar no mesmo local que seus grupos de aplicativos. Sempre que o espaço de trabalho é atualizado, o grupo de aplicativos relacionado é atualizado junto com ele. Assim como os grupos de aplicativos, o serviço requer que todos os espaços de trabalho estejam associados aos grupos de aplicativos criados no mesmo local.

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>Como expandir as propriedades de um objeto no PowerShell?

Ao executar um cmdlet do PowerShell, você verá apenas o nome do recurso e o local.

Por exemplo:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

Para ver todas as propriedades de um recurso, adicione um `format-list` ou `fl` ao final do cmdlet.

Por exemplo:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

Para ver propriedades específicas, adicione os nomes de propriedade específicos após `format-list` ou `fl` .

Por exemplo:

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-windows-virtual-desktop-support-guest-users"></a>O Windows Virtual Desktop dá suporte a usuários convidados?

A área de trabalho virtual do Windows não dá suporte a contas de usuário convidado do Azure AD. Por exemplo, digamos que um grupo de usuários convidados tenha licenças Microsoft 365 E3 por usuário, Windows E3 por usuário ou WIN VDA em sua própria empresa, mas que sejam usuários convidados no Azure AD de uma empresa diferente. A outra empresa gerenciaria os objetos de usuário dos usuários convidados no Azure AD e Active Directory como contas locais.

Você não pode usar suas próprias licenças para o benefício de terceiros. Além disso, a área de trabalho virtual do Windows não suporta a conta da Microsoft (MSA) no momento.

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>Por que não vejo o endereço IP do cliente na tabela WVDConnections?

Atualmente, não temos uma maneira confiável de coletar os endereços IP do cliente Web, portanto, não incluímos esse valor na tabela.

## <a name="how-does-windows-virtual-desktop-handle-backups"></a>Como a área de trabalho virtual do Windows lida com backups?

Há várias opções no Azure para lidar com o backup. Você pode usar o backup do Azure, Site Recovery e instantâneos.

## <a name="does-windows-virtual-desktop-support-third-party-collaboration-apps"></a>A área de trabalho virtual do Windows dá suporte a aplicativos de colaboração de terceiros?

A área de trabalho virtual do Windows está atualmente otimizada para equipes. A Microsoft atualmente não dá suporte a aplicativos de colaboração de terceiros como o zoom. As organizações de terceiros são responsáveis por fornecer diretrizes de compatibilidade para seus clientes. A área de trabalho virtual do Windows também não dá suporte ao Skype for Business.

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>Posso mudar do pool para pools de hosts pessoais?

Depois de criar um pool de hosts, você não pode alterar seu tipo. No entanto, você pode mover quaisquer VMs registradas para um pool de hosts para um tipo diferente de pool de hosts.

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>Qual é o maior tamanho de perfil que o FSLogix pode manipular?

As limitações ou cotas no FSLogix dependem da malha de armazenamento usada para armazenar arquivos VHD (X) do perfil do usuário.

A tabela a seguir fornece um exemplo de quantos IOPS um perfil FSLogix precisa para dar suporte a cada usuário. Os requisitos podem variar amplamente dependendo do usuário, dos aplicativos e da atividade em cada perfil.

| Recurso | Requisito |
|---|---|
| IOPS de estado estável | 10 |
| Entrar/sair do IOPS | 50 |

O exemplo nesta tabela é de um único usuário, mas pode ser usado para estimar os requisitos para o número total de usuários em seu ambiente. Por exemplo, você precisaria de cerca de 1.000 IOPS para 100 usuários e cerca de 5.000 IOPS durante o logon e a saída.

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>Há um limite de escala para pools de hosts criados no portal do Azure?

Esses fatores podem afetar o limite de escala para pools de hosts:

- O modelo do Azure é limitado a 800 objetos. Para saber mais, confira [assinatura e limites de serviço, cotas e restrições do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits). Cada VM também cria cerca de seis objetos, de modo que isso significa que você pode criar em torno de 132 VMs sempre que executar o modelo.

- Há restrições quanto à quantidade de núcleos que você pode criar por região e por assinatura. Por exemplo, se você tiver uma assinatura Contrato Enterprise, poderá criar núcleos de 350. Você precisará dividir 350 pelo número padrão de núcleos por VM ou seu próprio limite de núcleos para determinar quantas VMs você pode criar cada vez que executar o modelo. Saiba mais em [limites de máquinas virtuais-Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager).

- O nome do prefixo da VM e o número de VMs têm menos de 15 caracteres. Para saber mais, consulte [regras e restrições de nomenclatura para recursos do Azure](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute).

## <a name="can-i-manage-windows-virtual-desktop-environments-with-azure-lighthouse"></a>Posso gerenciar ambientes de área de trabalho virtual do Windows com o Azure Lighthouse?

O Azure Lighthouse não dá suporte completo ao gerenciamento de ambientes de área de trabalho virtual do Windows. Como o Lighthouse atualmente não dá suporte ao gerenciamento de usuários de locatários do Azure AD, os clientes do Lighthouse ainda precisam entrar no Azure AD que os clientes usam para gerenciar usuários.

Você também não pode usar as assinaturas do CSP sandbox com o serviço de área de trabalho virtual do Windows. Para saber mais, consulte [conta da área restrita da integração](/partner-center/develop/set-up-api-access-in-partner-center#integration-sandbox-account).

Por fim, se você habilitou o provedor de recursos da conta do proprietário do CSP, as contas de cliente do CSP não poderão modificar o provedor de recursos.

## <a name="how-often-should-i-turn-my-vms-on-to-prevent-registration-issues"></a>Com que frequência devo ativar minhas VMs para evitar problemas de registro?

Depois de registrar uma VM em um pool de hosts no serviço de área de trabalho virtual do Windows, o agente atualiza regularmente o token da VM sempre que a VM está ativa. O certificado para o token de registro é válido por 90 dias. Por causa desse limite de 90 dias, recomendamos que você inicie suas VMs a cada 90 dias. Ativar sua VM dentro desse limite de tempo impedirá que seu token de registro expire ou se torne inválido. Se você tiver iniciado sua VM após 90 dias e estiver enfrentando problemas de registro, siga as instruções no [Guia de solução de problemas do agente de área de trabalho virtual do Windows](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved) para remover a VM do pool de hosts, reinstalar o agente e registrá-lo novamente no pool.

## <a name="can-i-set-availability-options-when-creating-host-pools"></a>Posso definir opções de disponibilidade ao criar pools de hosts?

Sim. Os pools de hosts da área de trabalho virtual do Windows têm uma opção para selecionar um conjunto de disponibilidade ou zonas de disponibilidade quando você cria uma VM. Essas opções de disponibilidade são as mesmas que as usadas pelo Azure COMPUTE. Se você selecionar uma zona para a VM criada em um pool de hosts, a configuração se aplicará automaticamente a todas as VMs que você criar nessa zona. Se preferir distribuir suas VMs do pool de hosts entre várias zonas, você precisará seguir as instruções em [Adicionar máquinas virtuais com o portal do Azure](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal) para selecionar manualmente uma nova zona para cada nova VM que você criar.

## <a name="which-availability-option-is-best-for-me"></a>Qual é a melhor opção de disponibilidade para mim?

A opção de disponibilidade que você deve usar para suas VMs depende do local de sua imagem e de seus campos de disco gerenciado. A tabela a seguir explica o relacionamento que cada configuração tem com essas variáveis para ajudá-lo a descobrir qual é a melhor opção para sua implantação. 

| Opção de disponibilidade | Local da imagem | Botão de opção usar disco gerenciado (botão de opção) |
|---|---|---|
| Nenhum | Galeria | Desabilitado com "Sim" como padrão |
| Nenhum | Armazenamento de Blobs | Habilitado com "não" como padrão |
| Zona de disponibilidade | Galeria (opção de armazenamento de BLOBs desabilitada) | Desabilitado com "Sim" como padrão |
| Conjunto de disponibilidade com SKU gerenciado (disco gerenciado) | Galeria | Desabilitado com "Sim" como padrão |
| Conjunto de disponibilidade com SKU gerenciado (disco gerenciado) | Armazenamento de Blobs | Habilitado com "não" como padrão |
| Conjunto de disponibilidade com SKU gerenciado (disco gerenciado) | Armazenamento de BLOBs (opção de galeria desabilitada) | Desabilitado com "não" como padrão |
| Conjunto de disponibilidade (criado recentemente pelo usuário) | Galeria | Desabilitado com "Sim" como padrão |
| Conjunto de disponibilidade (criado recentemente pelo usuário) | Armazenamento de Blobs | Habilitado com "não" como padrão |
