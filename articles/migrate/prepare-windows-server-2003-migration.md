---
title: Preparar servidores Windows Server 2003 para migração com as migrações para Azure
description: Saiba como preparar os servidores do Windows Server 2003 para migração com as migrações para Azure.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: how-to
ms.date: 05/27/2020
ms.openlocfilehash: f8d3dea970d07d951467a44661e12000ba413f72
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753732"
---
# <a name="prepare-windows-server-2003-machines-for-migration"></a>Preparar computadores Windows Server 2003 para migração

Este artigo descreve como preparar computadores que executam o Windows Server 2003 para migração para o Azure. 


> [!NOTE]
> O [suporte estendido do Windows Server 2003](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) terminou em 14 de julho de 2015.  A equipe de suporte do Azure continua ajudando a solucionar problemas que se preocupam em executar o Windows Server 2003 no Azure. No entanto, esse suporte é limitado a problemas que não exigem patches ou solução de problemas no nível do sistema operacional. Migrar seus aplicativos para instâncias do Azure que executam uma versão mais recente do Windows Server é a abordagem recomendada para garantir que você esteja efetivamente aproveitando a flexibilidade e a confiabilidade da nuvem do Azure. No entanto, se você ainda optar por migrar seu Windows Server 2003 para o Azure, poderá usar a ferramenta migrações para Azure: Server Migration se o seu Windows Server for uma VM em execução no VMware ou no Hyper-V.


- Você pode usar a migração sem agente para migrar [VMs do Hyper-V](tutorial-migrate-hyper-v.md) e [VMs do VMware](tutorial-migrate-vmware.md) para o Azure.
- Para se conectar às VMs do Azure após a migração, a Integration Services do Hyper-V deve ser instalada na VM do Azure. Os computadores com Windows Server 2003 não têm essa instalação instalada por padrão.
- Não há um link de download direto para instalar o Hyper-V Integration Services, portanto, você precisa fazer o seguinte:
    - Para VMs do Hyper-V que não estão instaladas, você extrai arquivos de instalação para Integration Services em um computador que executa o Windows Server 2012 R2/Windows Server 2012 com a função Hyper-V e, em seguida, copia o instalador para o computador Windows Server 2003. Os arquivos de instalação não estão disponíveis em computadores que executam o Windows Server 2016.
    - Para VMs VMware, você cria uma tarefa de inicialização que instala Integration Services quando a VM do Azure é iniciada após a migração.


## <a name="install-on-hyper-v-vms"></a>Instalar em VMs do Hyper-V

Antes da migração, verifique se o Hyper-V Integration Services está instalado e, em seguida, instale-o, se necessário.

1. Siga [estas instruções](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#turn-an-integration-service-on-or-off-using-hyper-v-manager) para verificar se ele está instalado.
2. Se ele não estiver instalado, entre em um computador que executa o Windows Server 2012 R2/Windows Server 2012 com a função Hyper-V.
3. Navegue até o arquivo de instalação em **C:\Windows\System32\vmguest.ISO** e monte o arquivo.
2. Copie a pasta de instalação para a máquina do Windows Server 2003 e instale o Integration Services.
4. Após a instalação, você pode deixar as configurações padrão em Integration Services. 

## <a name="install-on-vmware-vms"></a>Instalar em VMs VMware

1. Entre em um computador que executa o Windows Server 2012 R2/Windows Server 2012 com a função Hyper-V.
2. Navegue até o arquivo de instalação em **C:\Windows\System32\vmguest.ISO** e monte o arquivo.
3. Copie a pasta de instalação para a VM do VMware.
4. Na linha de comando na VM, execute ```gpedit.msc``` .
5. Abra **configuração do computador**  >  **Windows Settings**  >  **scripts de configurações do Windows (inicialização/desligamento)**.
6. Em **inicialização**  >  **Adicionar**  >  **nome do script**, digite o endereço setup.exe.
7. Após a migração para o Azure, o script é executado na primeira vez que a VM do Azure é iniciada.
8. Reinicie manualmente a VM do Azure. Há um pop-up no diagnóstico de inicialização para indicar que uma reinicialização é necessária.
9. Depois que o script for executado e o Hyper-V Integration Services estiver instalado na VM do Azure, você poderá remover o script da inicialização.
10. Após a instalação, você pode deixar as configurações padrão em Integration Services. 

## <a name="next-steps"></a>Próximas etapas

- Examine os requisitos de migração para VMs do [VMware](migrate-support-matrix-vmware-migration.md) e do [Hyper-V](migrate-support-matrix-hyper-v-migration.md) .
- Migre VMs do [VMware](server-migrate-overview.md) e do [Hyper-V](tutorial-migrate-hyper-v.md) .
