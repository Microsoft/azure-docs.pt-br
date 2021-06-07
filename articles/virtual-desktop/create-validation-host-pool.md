---
title: Atualizações de serviço de pool de hosts da Área de Trabalho Virtual do Windows – Azure
description: Como criar um pool de host de validação para monitorar atualizações de serviço antes de distribuir atualizações para a produção.
author: Heidilohr
ms.topic: tutorial
ms.date: 12/15/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 66279c2ea798a7edb21795f368011b00bdf6297e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106447804"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>Tutorial: Criar um pool de hosts para validar as atualizações de serviço

>[!IMPORTANT]
>Este conteúdo se aplica à Área de Trabalho Virtual do Windows com objetos da Área de Trabalho Virtual do Windows do Azure Resource Manager. Se você estiver usando a Área de Trabalho Virtual do Windows (clássica), sem objetos do Azure Resource Manager, confira [este artigo](./virtual-desktop-fall-2019/create-validation-host-pool-2019.md).

Os pools de host são uma coleção de uma ou mais máquinas virtuais idênticas no ambiente de Área de Trabalho Virtual do Windows. É altamente recomendável criar um pool de host de validação em que as atualizações de serviço são aplicadas primeiro. Isso permitirá monitorar as atualizações de serviço antes que o serviço as aplique ao ambiente padrão ou de não validação. Sem um pool de host de validação, você não poderá descobrir alterações que introduzam erros, o que poderá resultar em tempo de inatividade para os usuários em seu ambiente padrão.

Para garantir que seus aplicativos funcionem com as atualizações mais recentes, o pool de host de validação deverá ser o mais semelhante possível aos pools de host do ambiente de não validação. Os usuários devem se conectar com frequência ao pool de host de validação como fazem com o pool de host padrão. Se você tiver testes automatizados em seu pool de host, deverá incluí-los no pool de host de validação.

Você pode depurar problemas no pool de host de validação com o [recurso de diagnóstico](diagnostics-role-service.md) ou com os [artigos de para solucionar problemas da Área de Trabalho Virtual do Windows](troubleshoot-set-up-overview.md).

>[!NOTE]
> É recomendável deixar o pool de host de validação em vigor para testar todas as atualizações futuras.

>[!IMPORTANT]
>Atualmente, a Área de Trabalho Virtual do Windows com integração ao Gerenciamento de Recursos do Azure tem problemas para habilitar e desabilitar ambientes de validação. Atualizaremos este artigo quando resolvermos o problema.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, siga as instruções em [Configurar o módulo do PowerShell da Área de Trabalho Virtual do Windows](powershell-module.md) para configurar o módulo do PowerShell e entrar no Azure.

## <a name="create-your-host-pool"></a>Criar seu pool de host

Você pode criar um pool de host seguindo as instruções em qualquer um dos seguintes artigos:
- [Tutorial: Criar um pool de host com o Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Criar um pool de host com o PowerShell](create-host-pools-powershell.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>Definir seu pool de host como um pool de host de validação

Execute os seguintes cmdlets do PowerShell para definir o novo pool de host como um pool de host de validação. Substitua os valores entre colchetes pelos valores relevantes para sua sessão:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -ValidationEnvironment:$true
```

Execute o seguinte cmdlet do PowerShell para confirmar que a propriedade de validação foi definida. Substitua os valores entre colchetes pelos valores relevantes para sua sessão.

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | Format-List
```

Os resultados do cmdlet devem ser semelhantes a esta saída:

```powershell
    HostPoolName        : hostpoolname
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnvironment : True
```

## <a name="enable-your-validation-environment-with-the-azure-portal"></a>Habilitar seu ambiente de validação com o portal do Azure

Use também o portal do Azure para habilitar seu ambiente de validação.

Para usar o portal do Azure e configurar o pool de host de validação:

1. Entre no Portal do Azure em <https://portal.azure.com>.
2. Pesquise e selecione **Área de Trabalho Virtual do Windows**.
3. Na página Área de Trabalho Virtual do Windows, selecione **Pools de host**.
4. Selecione o nome do pool de host que deseja editar.
5. Selecione **Propriedades**.
6. No campo do ambiente de validação, escolha **Sim** para habilitar o ambiente de validação.
7. Selecione **Salvar**. Isso aplicará as novas configurações.

## <a name="update-schedule"></a>Agenda de atualização

As atualizações de serviço acontecem mensalmente. Se houver grandes problemas, atualizações críticas serão fornecidas a um ritmo mais frequente.

Se houver atualizações de serviço, você deverá ter pelo menos um pequeno grupo de usuários entrando a cada dia para validar o ambiente. Recomendamos que você visite regularmente nosso site [TechCommunity](https://techcommunity.microsoft.com/t5/forums/searchpage/tab/message?filter=location&q=wvdupdate&location=forum-board:WindowsVirtualDesktop&sort_by=-topicPostDate&collapse_discussion=true) e siga as postagens com o WVDUPdate para se manter informado sobre as atualizações de serviço.

## <a name="next-steps"></a>Próximas etapas

Agora que você criou um pool de hosts de validação, você pode aprender a usar a Integridade do Serviço do Azure para monitorar a implantação de Área de Trabalho Virtual do Windows.

> [!div class="nextstepaction"]
> [Configurar alertas de serviço](./set-up-service-alerts.md)
