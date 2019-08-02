---
title: Arquitetura de dispositivo de replicação de migrações para Azure | Microsoft Docs
description: Fornece uma visão geral do dispositivo de replicação de migrações para Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 4f4dc307bee4190a0e94ace493053e0cfd01150e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811434"
---
# <a name="replication-appliance"></a>Solução de replicação

Este artigo descreve o dispositivo de replicação usado pelas migrações para Azure: Avaliação de servidor quando migrar VMs do VMware, máquinas físicas e pública/privada na nuvem VMs para o Azure, usando uma migração baseada em agente. 

A ferramenta está disponível na [migrações para Azure](migrate-overview.md) hub. O hub fornece ferramentas nativas para avaliação e migração, bem como ferramentas de outros serviços do Azure e de fornecedores de software independentes (ISVs).


## <a name="appliance-overview"></a>Visão geral do dispositivo

O dispositivo de replicação é implantado como uma única máquina local, como uma VM VMware ou um servidor físico. Ele é executado:
- **Solução de replicação**: O dispositivo de replicação coordena a comunicação e gerencia a replicação de dados, para VMs do VMware locais e servidores físicos replicando no Azure.
- **Servidor de processo**: O servidor de processo, que é instalado por padrão no dispositivo de replicação e faz o seguinte:
    - **Gateway de replicação**: Ele atua como um gateway de replicação. Ele recebe dados de replicação de máquinas habilitadas para replicação. Ele otimiza os dados de replicação com caching, compactação e criptografia e envia-o para o Azure.
    - **Instalador do agente**: Executa uma instalação por push do serviço de mobilidade. Esse serviço deve ser instalado e em execução em cada computador local que você deseja replicar para migração.

## <a name="appliance-deployment"></a>Implantação de dispositivo

**Implantar como** | **Usadas para** | **Detalhes**
--- | --- |  ---
VM do VMware | Geralmente usado durante a migração de VMs VMware usando a ferramenta de migração de migrar do Azure com a migração baseada em agente. | Baixe o modelo OVA do hub de migrações para Azure e importar para o vCenter Server para criar o dispositivo de VM.
Uma máquina física | Usado durante a migração de servidores físicos locais se você não tiver uma infraestrutura do VMware, ou se você não puder criar uma VM do VMware usando um modelo OVA. | Baixe um instalador de software do hub de migrações para Azure e execute-o para configurar a máquina de dispositivo.

## <a name="appliance-deployment-requirements"></a>Requisitos de implantação do dispositivo

[Revisão](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) os requisitos de implantação.



## <a name="appliance-license"></a>Licença de dispositivo
O dispositivo é fornecido com uma licença de avaliação do Windows Server 2016, é válida por 180 dias. Se o período de avaliação estiver prestes a expiração, recomendamos que você baixe e implante um novo dispositivo ou que você ative a licença do sistema operacional do dispositivo de VM.

## <a name="replication-process"></a>Processo de replicação

1. Ao habilitar a replicação para uma VM, a replicação inicial para o armazenamento do Azure começa, usando a política de replicação especificada. 
2. O tráfego é replicado para pontos de extremidade públicos do Armazenamento do Microsoft Azure pela Internet. Não há suporte para replicação de tráfego através de uma VPN (rede virtual privada) site a site de um site local para o Azure.
3. Após a conclusão da replicação inicial, a replicação delta começa. As alterações acompanhadas para uma máquina são registradas.
4. A comunicação ocorre da seguinte maneira:
    - As VMs se comunicar com o dispositivo de replicação na porta HTTPS 443 de entrada para o gerenciamento de replicação.
    - O dispositivo de replicação coordena a replicação com o Azure pela porta HTTPS 443 de saída.
    - As VMs enviam dados de replicação para o servidor de processo (em execução no dispositivo de replicação) na porta HTTPS 9443 de entrada. Essa porta pode ser modificada.
    - O servidor de processo recebe dados de replicação, otimiza-os e criptografa-os e os envia para o armazenamento do Microsoft Azure pela porta 443 de saída.
5. Os dados de replicação registra land primeiro em uma conta de armazenamento de cache no Azure. Esses logs são processados e os dados são armazenados no Azure disco gerenciado.

![Arquitetura](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Atualizações de dispositivo

O dispositivo é atualizado manualmente do hub de migrações para Azure. É recomendável que você sempre execute a versão mais recente.

1. No Azure migrar > servidores > migrações para Azure: Avaliação de servidor, servidores de infraestrutura, clique em **servidores de configuração**.
2. Na **servidores de configuração**, um link será exibido na **versão do agente** quando uma nova versão do dispositivo de replicação está disponível. 
3. Baixar o instalador para a máquina de dispositivo de replicação e instale a atualização. O instalador detecta a versão atual em execução no dispositivo.
 
## <a name="next-steps"></a>Próximas etapas

[Saiba como](tutorial-assess-vmware.md#set-up-the-appliance-vm) para configurar o dispositivo para o VMware.
[Saiba como](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) para configurar o dispositivo para o Hyper-V.

