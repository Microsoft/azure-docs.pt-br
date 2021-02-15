---
title: Definir configurações do Windows Update para Gerenciamento de Atualizações de Automação do Azure
description: Este artigo informa como definir as configurações do Windows Update para trabalhar com p Gerenciamento de Atualizações de Automação do Azure.
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: a1f95ca856223628974a9519b7c4811bde43965e
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221900"
---
# <a name="configure-windows-update-settings-for-azure-automation-update-management"></a>Definir configurações do Windows Update para Gerenciamento de Atualizações de Automação do Azure

O Gerenciamento de Atualizações de Automação do Azure se baseia no [cliente do Windows Update](/windows/deployment/update/windows-update-overview) para baixar e instalar atualizações do Windows. Há configurações específicas que são usadas pelo cliente do Windows Update ao se conectar ao Windows Server Update Services (WSUS) ou ao Windows Update. Muitas dessas configurações podem ser gerenciadas com:

- Editor de Política de Grupo Local
- Política de Grupo
- PowerShell
- Editar diretamente o Registro

Gerenciamento de Atualizações respeita muitas das configurações especificadas para controlar o cliente do Windows Update. Se você usar as configurações para habilitar atualizações que não sejam do Windows, o Gerenciamento de Atualizações também gerenciará essas atualizações. Se você quiser habilitar o download de atualizações antes que ocorra uma implantação de atualização, as implantações de atualização poderão ser mais rápidas, mais eficientes e ter menos probabilidade de exceder a janela de manutenção.

Para obter recomendações adicionais sobre como configurar o WSUS em sua assinatura do Azure e manter as suas máquinas virtuais do Windows atualizadas, examine [Planejar sua implantação para atualizar as máquinas virtuais do Windows no Azure usando o WSUS](/azure/architecture/example-scenario/wsus/).

## <a name="pre-download-updates"></a>Baixar previamente atualizações

Para configurar o download automático de atualizações sem instalá-las automaticamente, use a Política de Grupo para [definir a configuração de Atualizações Automáticas](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) como 3. Essa configuração permite downloads das atualizações necessárias em segundo plano e notifica que as atualizações estão prontas para instalação. Dessa forma, o Gerenciamento de Atualizações permanece no controle das agendas, mas permite que as atualizações sejam baixadas fora da janela de manutenção do Gerenciamento de Atualizações. Esse comportamento impede erros `Maintenance window exceeded` no Gerenciamento de Atualizações.

Você pode habilitar essa configuração no PowerShell:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Definir configurações reinicialização

As chaves do registro listadas em [Configurar Atualizações Automáticas editando o registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) e [Chaves de registro usadas para gerenciar a reinicialização](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) podem fazer com que seus computadores sejam reinicializados, mesmo que você especifique **Nunca Reinicializar** nas configurações de **Implantação de Atualização**. Configure essas chaves do registro para se adequar melhor ao seu ambiente.

## <a name="enable-updates-for-other-microsoft-products"></a>Habilitar atualizações para outros produtos da Microsoft

Por padrão, o cliente do Windows Update está configurado para fornecer atualizações somente para o Windows. Se habilitar a configuração **Fornecer atualizações para outros produtos da Microsoft quando eu atualizar o Windows**, você também receberá atualizações para outros produtos, inclusive patches de segurança do Microsoft SQL Server e outro software da Microsoft. Você pode configurar essa opção se tiver baixado e copiado os [Arquivos de modelo administrativo](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) mais recentes disponíveis para o Windows 2016 e posterior.

Se você tiver computadores que executam o Windows Server 2012 R2, não será possível definir essa configuração por meio de Política de Grupo. Execute o seguinte comando do PowerShell nesses computadores:

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="make-wsus-configuration-settings"></a>Definir as configurações de WSUS

O Gerenciamento de Atualizações dá suporte às configurações do WSUS. Você pode especificar as fontes para verificar e baixar atualizações usando instruções em [Especificar o local do serviço de atualização na intranet da Microsoft](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Por padrão, o cliente do Windows Update está configurado para baixar atualizações do Windows Update. Quando você especifica um servidor do WSUS como uma fonte para seus computadores, se as atualizações não forem aprovadas no WSUS, a implantação da atualização falhará. 

Para restringir os computadores ao serviço de atualização interno, defina [Não se conectar a nenhum local da Internet do Windows Update](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations).

## <a name="next-steps"></a>Próximas etapas

Agende uma implantação de atualização seguindo as instruções em [gerenciar atualizações e patches para suas VMs](manage-updates-for-vm.md).
