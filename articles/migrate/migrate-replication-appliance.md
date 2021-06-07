---
title: Dispositivo de replicação de Migrações para Azure
description: Saiba mais sobre o dispositivo de replicação de migração do Azure para a migração VMWare baseada em agente.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: ec277bcc3e361561f54e72c54526d65487c113b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96754089"
---
# <a name="replication-appliance"></a>Dispositivo de replicação

Este artigo descreve o dispositivo de replicação usado pelas [migrações para Azure:](migrate-services-overview.md#azure-migrate-server-migration-tool) ferramenta de migração de servidor ao migrar VMs VMware, máquinas físicas e VMs de nuvem privada/pública para o Azure, usando a migração baseada em agente. 


## <a name="overview"></a>Visão geral

O dispositivo de replicação é implantado quando você configura a migração baseada em agente de VMs VMware ou servidores físicos. Ele é implantado como um único computador local, seja como uma VM VMware ou um servidor físico. Ele é executado:

- **Dispositivo de replicação**: o dispositivo de replicação coordena as comunicações e gerencia a replicação de dados para VMs VMware locais e servidores físicos replicando para o Azure.
- **Servidor de processo**: o servidor de processo, que é instalado por padrão no dispositivo de replicação e faz o seguinte:
    - **Gateway de replicação**: ele atua como um gateway de replicação. Ele recebe dados de replicação de computadores habilitados para replicação. Ele otimiza os dados de replicação com caching, compactação e criptografia e os envia para o Azure.
    - **Instalador do agente**: executa uma instalação por push do serviço de mobilidade. Esse serviço deve ser instalado e executado em cada computador local que você deseja replicar para a migração.

## <a name="appliance-deployment"></a>Implantação do dispositivo

**Usadas para** | **Detalhes**
--- |  ---
**Migração baseada no agente de VM VMware** | Você baixa o modelo OVA do Hub migrações para Azure e importa para vCenter Server para criar a VM do dispositivo.
**Migração baseada em agente de máquina física** | Se você não tiver uma infra-estrutura do VMware ou se não puder criar uma VM do VMware usando um modelo OVA, baixe um instalador de software do Hub migrações para Azure e execute-o para configurar o computador do dispositivo.

> [!NOTE]
> Se você estiver implantando no Azure governamental, use o arquivo de instalação para implantar o dispositivo de replicação.

## <a name="appliance-requirements"></a>Requisitos do dispositivo

Quando você configura o dispositivo de replicação usando o modelo OVA fornecido no Hub migrações para Azure, o dispositivo executa o Windows Server 2016 e está em conformidade com os requisitos de suporte. Se você configurar o dispositivo de replicação manualmente em um servidor físico, verifique se ele está em conformidade com os requisitos.

**Componente** | **Requisito**
--- | ---
 | **Dispositivo de VM VMware**
PowerCLI | A [versão 6,0 do PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deverá ser instalada se o dispositivo de replicação estiver em execução em uma VM VMware.
Tipo de NIC | VMXNET3 (se o dispositivo for uma VM VMware)
 | **Configurações de hardware**
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | Três: o disco do sistema operacional, o disco de cache do servidor de processo e a unidade de retenção.
Espaço livre em disco (cache) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB
**Configurações de software** |
Sistema operacional | Windows Server 2016 ou Windows Server 2012 R2
Licença | O dispositivo vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias.<br/><br/> Caso o período de avaliação esteja próximo da expiração, recomendamos que você baixe e implante um novo dispositivo ou ative a licença do sistema operacional da VM do dispositivo.
Localidade do sistema operacional | Inglês (en-us)
TLS | O TLS 1,2 deve estar habilitado.
.NET Framework | .NET Framework 4,6 ou posterior deve ser instalado no computador (com criptografia forte habilitada.
MySQL | O MySQL deve ser instalado no dispositivo.<br/> MySQL deve ser instalado. Você pode instalar manualmente ou Site Recovery pode instalá-lo durante a implantação do dispositivo.
Outros aplicativos | Não execute outros aplicativos no dispositivo de replicação.
Funções do Windows Server | Não habilite essas funções: <br> - Active Directory Domain Services <br>- Serviços de Informações da Internet <br> - Hyper-V
Políticas de grupo | Não habilite estas políticas de grupo: <br> - Impedir o acesso ao prompt de comando. <br> - Impedir o acesso às ferramentas de edição do registro. <br> - Lógica de confiança para anexos de arquivo. <br> - Ativar a execução do script. <br> [Saiba mais](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | – Nenhum site da Web padrão já existente <br> – Nenhum aplicativo/site da Web pré-existente escutando na porta 443 <br>- Habilitar [autenticação anônima](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Habilitar configuração [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))
**Configurações de rede** |
Tipo de endereço IP | Estático
Portas | 443 (orquestração do canal de controle)<br>9443 (transporte de dados)
Tipo de NIC | VMXNET3

## <a name="mysql-installation"></a>Instalação do MySQL 

O MySQL deve ser instalado no computador do dispositivo de replicação. Ele pode ser instalado usando um desses métodos.

**Método** | **Detalhes**
--- | ---
Baixar e instalar manualmente | Baixe o aplicativo MySQL & Coloque-o na pasta C:\Temp\ASRSetup e instale manualmente.<br/> Quando você configurar o dispositivo, o MySQL será mostrado como já instalado.
Sem download online | Coloque o aplicativo instalador do MySQL na pasta C:\Temp\ASRSetup. Ao instalar o dispositivo e clicar para baixar e instalar o MySQL, a instalação usará o instalador adicionado.
Baixar e instalar em migrações para Azure | Quando você instalar o dispositivo e for solicitado a fornecer o MySQL, selecione **baixar e instalar**.

## <a name="url-access"></a>acesso à URL

O dispositivo de replicação precisa de acesso a essas URLs na nuvem pública do Azure.

**URL** | **Detalhes**
--- | ---
\*.backup.windowsazure.com | Usado para transferência de dados replicados e coordenação
\*.store.core.windows.net | Usado para transferência de dados replicados e coordenação
\*.blob.core.windows.net | Usado para acessar a conta de armazenamento que armazena os dados replicados
\*.hypervrecoverymanager.windowsazure.com | Usado para operações de gerenciamento de replicação e coordenação
https:\//management.azure.com | Usado para operações de gerenciamento de replicação e coordenação
*.services.visualstudio.com | Usado para fins de telemetria (é opcional)
time.windows.com | Usados para verificar a sincronização de horário entre a hora do sistema e a hora global.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | A instalação do dispositivo precisa de acesso a essas URLs. Elas são usadas pelo Azure Active Directory para o gerenciamento de identidade e controle de acesso
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Para concluir o download do MySQL. Em algumas regiões, o download pode ser redirecionado para a URL da CDN. Verifique se a URL da CDN também é permitida, se necessário.


## <a name="azure-government-url-access"></a>Acesso à URL do Azure governamental

O dispositivo de replicação precisa de acesso a essas URLs no Azure governamental.

**URL** | **Detalhes**
--- | ---
\*.backup.windowsazure.us | Usado para transferência de dados replicados e coordenação
\*.store.core.windows.net | Usado para transferência de dados replicados e coordenação
\*.blob.core.windows.net | Usado para acessar a conta de armazenamento que armazena os dados replicados
\*.hypervrecoverymanager.windowsazure.us | Usado para operações de gerenciamento de replicação e coordenação
https:\//management.usgovcloudapi.net | Usado para operações de gerenciamento de replicação e coordenação
*.services.visualstudio.com | Usado para fins de telemetria (é opcional)
time.nist.gov | Usados para verificar a sincronização de horário entre a hora do sistema e a hora global.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | A instalação do dispositivo com OVA precisa de acesso a essas URLs. Eles são usados para controle de acesso e gerenciamento de identidade por Azure Active Directory.
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Para concluir o download do MySQL. Em algumas regiões, o download pode ser redirecionado para a URL da CDN. Verifique se a URL da CDN também é permitida, se necessário.

## <a name="port-access"></a>Acesso à porta

**Dispositivo** | **Conexão**
--- | ---
VMs | O serviço de mobilidade em execução nas VMs se comunica com o dispositivo de replicação local (servidor de configuração) na porta HTTPS 443 de entrada, para o gerenciamento de replicação.<br/><br/> As VMs que estão sendo replicadas enviam dados de replicação para o servidor de processo (em execução no computador do servidor de configuração) na porta 9443 HTTPS de entrada. Essa porta pode ser modificada.
Dispositivo de replicação | O dispositivo de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443 de saída.
Servidor de processo | O servidor de processo recebe dados de replicação, otimiza-os e criptografa-os e envia-os para o armazenamento do Azure pela porta 443 de saída.<br/> Por padrão, o servidor de processo é executado no dispositivo de replicação.


## <a name="replication-process"></a>Processo de replicação

1. Ao habilitar a replicação para uma VM, a replicação inicial para o armazenamento do Azure começa, usando a política de replicação especificada. 
2. O tráfego é replicado para pontos de extremidade públicos do Armazenamento do Microsoft Azure pela Internet. Não há suporte para replicação de tráfego através de uma VPN (rede virtual privada) site a site de um site local para o Azure.
3. Depois que a replicação inicial é concluída, a replicação delta é iniciada. As alterações controladas para um computador são registradas.
4. A comunicação ocorre da seguinte maneira:
    - As VMs se comunicam com o dispositivo de replicação na porta HTTPS 443 de entrada para o gerenciamento de replicação.
    - O dispositivo de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443 de saída.
    - As VMs enviam dados de replicação para o servidor de processo (em execução no dispositivo de replicação) na porta HTTPS 9443 de entrada. Essa porta pode ser modificada.
    - O servidor de processo recebe dados de replicação, otimiza-os e criptografa-os e envia-os para o armazenamento do Azure pela porta 443 de saída.
5. Os logs de dados de replicação primeiro se esterram em uma conta de armazenamento de cache no Azure. Esses logs são processados e os dados são armazenados em um disco gerenciado do Azure.

![Diagrama mostra a arquitetura do processo de replicação.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Atualizações de dispositivo

O dispositivo é atualizado manualmente do Hub migrações para Azure. É recomendável que você sempre execute a versão mais recente.

1. No Azure migrar servidores > > migrações para Azure: avaliação de servidor, servidores de infraestrutura, clique em **servidores de configuração**.
2. Em **servidores de configuração**, um link aparece na **versão do agente** quando uma nova versão do dispositivo de replicação está disponível. 
3. Baixe o instalador no computador do dispositivo de replicação e instale a atualização. O instalador detecta a versão em execução no momento no dispositivo.
 
## <a name="next-steps"></a>Próximas etapas

- [Saiba como](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) configurar o dispositivo de replicação para migração de VM VMware baseada em agente.
- [Saiba como](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) configurar o dispositivo de replicação para servidores físicos.
