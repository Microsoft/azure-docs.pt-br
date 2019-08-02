---
title: Preparar VMs VMware executando o Linux para a migração para o Azure com a migração do servidor de migrar do Azure | Microsoft Docs
description: Descreve como preparar uma VM do Linux para a migração para o Azure com a migração do servidor de migrar do Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/14/2018
ms.author: raynew
ms.openlocfilehash: efc410699ef6f4722857c812b38473c8f54b911b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811785"
---
# <a name="prepare-linux-vmware-vms-for-migration-to-azure"></a>Preparar VMs do VMware Linux para a migração para o Azure 

Este artigo descreve como preparar VMs VMware executando quando você quiser migrá-los para o Azure usando o Linux [migrações para Azure](migrate-overview.md). 

> [!NOTE]
> Migração de servidor de migrar do Azure está atualmente em visualização pública. Você pode usar a versão de GA existente das migrações para Azure para descobrir e avaliar as VMs para a migração, mas a migração real não é suportada na versão de GA existente.

Prepare as máquinas Linux, da seguinte maneira:

1. Instale o Integration Services do Linux do Hyper-V. Versões mais recentes das distribuições do Linux podem ter instalado por padrão).
2. Recompile a imagem de inicialização do Linux para que ele contenha os drivers necessários do Hyper-V e para que a VM será inicializada no Azure (necessário para algumas distribuições).
3. Habilite o log de console serial para solução de problemas. [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console).
4. Atualize o arquivo de mapa do dispositivo com o nome do dispositivo para associações de volume, para usar identificadores de dispositivo persistentes.
5. Atualize entradas fstab para usar os identificadores de volume persistente.
6. Remova as regras udev que reservar nomes de interface com base no endereço MAC etc.
7. Interfaces de rede de atualização para receber endereços IP do DHCP.
8. Certifique-se de ssh está habilitado. Verifique se o serviço sshd é definido para iniciar automaticamente na reinicialização.
9. Certifique-se de que entrada ssh solicitações de conexão não são bloqueadas pelo firewall do sistema operacional ou as regras de tabela IP.

[Saiba mais](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console) sobre como fazer essas alterações nas distribuições mais populares do Linux.

## <a name="sample-script"></a>Script de exemplo

Esse script (preparar-para-azure.sh) fornece um exemplo para preparar máquinas Linux. O script pode não funcionar para todas as distribuições e ambientes, mas é um ponto de partida útil.

O script mostra como: 

- Gerar novamente a imagem de inicialização do Linux com os drivers necessários, se necessário.
- Atualize entradas fstab para usar os identificadores de volume persistente.
- Redirecione os logs do console para a porta serial.
- Habilitar o console serial de acesso do Azure
- Remover as regras de udev de rede
- Injete uma execução de script de inicialização que é executado quando a VM é inicializada. Ele verifica se a máquina está em execução no Azure. Se for, ele atualiza a configuração de rede na VM e define a primeira interface ethernet para usar o DHCP para adquirir um endereço IP.

### <a name="before-you-start"></a>Antes de começar

- O script de exemplo contém etapas de exemplo. Ele não deve ser executado em sistemas de produção. Ele pode danificar ou corromper a máquina virtual no qual ele é executado.
- É recomendável que você executá-lo em uma VM de teste. Antes de iniciar, reserve um backup de VM ou um instantâneo para que você possa restaurar a VM se necessário. 
- O script funciona quando a VM estiver executando uma das distribuições Linux:
    - Red Hat Enterprise Linux 6.5 e versões posteriores, 7.1 +
    - Cent OS 6.5 + 7.1 +
    - SUSE Linux Enterprise Server 12 SP1,SP2, SP3
    - Ubuntu 14.04, 16.04, 18.04
    - Debian 7, 8

### <a name="run-the-script"></a>Execute o script

1. Copie o script para o Linux usando sftp ou um cliente scp como FileZilla ou WinScp VM de teste.
2. SSH no computador Linux usando uma conta de administrador.
3. Navegue até o diretório do script.
4. Para tornar o script em um arquivo executável, execute **sudo chmod 777 prepare-para-azure.sh**.
5. Execute o script com **./prepare-for-azure.sh**.

Aqui está como o script é executado:

![Script do Linux](./media/how-to-prepare-linux-for-migration/script1.png)
![script do Linux](./media/how-to-prepare-linux-for-migration/script2.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script3.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script4.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script5.png)
![script do Linux ](./media/how-to-prepare-linux-for-migration/script6.png)
 ![Script do Linux](./media/how-to-prepare-linux-for-migration/script7.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script8.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script9.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script10.png)
![script do Linux ](./media/how-to-prepare-linux-for-migration/script11.png)
 ![Script do Linux](./media/how-to-prepare-linux-for-migration/script12.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script13.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script14.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script15.png)
![script do Linux ](./media/how-to-prepare-linux-for-migration/script16.png)
 ![Script do Linux](./media/how-to-prepare-linux-for-migration/script17.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script18.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script19.png)
![Linux script](./media/how-to-prepare-linux-for-migration/script20.png)
![script do Linux ](./media/how-to-prepare-linux-for-migration/script21.png)
 ![Script do Linux](./media/how-to-prepare-linux-for-migration/script22.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script23.png)
![script Linux](./media/how-to-prepare-linux-for-migration/script24.png)
![script do Linux](./media/how-to-prepare-linux-for-migration/script25.png)



## <a name="next-steps"></a>Próximas etapas

- Saiba como usar o [mapeamento de dependência de máquina](how-to-create-group-machine-dependencies.md) para criar grupos com mais confiança.
- [Saiba mais](concepts-assessment-calculation.md) sobre como as avaliações são calculadas.
