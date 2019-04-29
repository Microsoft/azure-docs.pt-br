---
title: Sobre o Planejador de Implantação do Azure Site Recovery para recuperação de desastres de VMs do VMware para o Azure | Microsoft Docs
description: Aprenda sobre o Planejador de Implantação do Azure Site Recovery para recuperação de desastres de VMs VMware para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mayg
ms.openlocfilehash: 0597f185df35a92696ed9287d23778180319b3de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61035785"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-vmware-to-azure"></a>Sobre o Planejador de Implantação do Azure Site Recovery para VMware para o Azure
Este artigo é o guia de usuário do Planejador de Implantações do Azure Site Recovery para implantações de produção do VMware para o Azure.

## <a name="overview"></a>Visão geral

Antes de começar a proteger máquinas virtuais (VMs) do VMware usando o Azure Site Recovery, aloque largura de banda suficiente, com base em sua taxa de alteração de dados diária, para atender ao objetivo de ponto de recuperação (RPO) desejado. Implante o número correto de servidores de configuração e servidores de processo locais.

Você também precisa criar o tipo e o número correto de contas de Armazenamento do Azure de destino. Você cria contas de armazenamento standard ou premium, acrescentando o crescimento nos servidores de produção de origem devido ao aumento no uso ao longo do tempo. Você escolhe o tipo de armazenamento por VM, com base nas características de carga de trabalho (por exemplo, operações de E/S de leitura/gravação por segundo [IOPS] ou variação de dados) e nos limites do Site Recovery.

 O Planejador de Implantação do Site Recovery é uma ferramenta de linha de comando para ambos os cenários de recuperação de desastre do Hyper-V para o Azure e do VMware para o Azure. É possível criar o perfil remotamente das VMs do VMware usando essa ferramenta (sem impacto na produção) para entender os requisitos de largura de banda e do armazenamento para o êxito da replicação e do failover de teste. Você pode executar a ferramenta sem instalar componentes do Site Recovery no local. Para obter resultados precisos da taxa de transferência obtida, execute o planejador em um Windows Server que atenda aos requisitos mínimos do servidor de configuração de Site Recovery que você provavelmente precisará implantar como uma das primeiras etapas da implantação de produção.

A ferramenta fornece os seguintes detalhes:

**Avaliação de compatibilidade**

* Avaliação de qualificação de VM com base no número de discos, no tamanho do disco, no IOPS, na variação, no tipo de inicialização (EFI/BIOS) e na versão do sistema operacional

**Largura de banda de rede necessária versus avaliação de RPO**

* Largura de banda de rede estimada que é necessária para a replicação delta
* Taxa de transferência que o Site Recovery pode obter do local para o Azure
* Número de VMs a serem incluídas em um lote com base na largura de banda estimada para concluir a replicação inicial em determinado período de tempo
* O RPO que pode ser obtido para uma determinada largura de banda
* Impacto sobre o RPO desejado se menos largura de banda for provisionada

**Requisitos de infraestrutura do Azure**

* Requisito de tipo de armazenamento (conta de armazenamento standard ou premium) para cada VM
* Número total de contas de armazenamento standard e premium a serem configuradas para replicação
* Sugestões de nomes de contas de armazenamento com base na orientação do armazenamento
* Posicionamento da conta de armazenamento para todas as VMs
* Número de núcleos do Azure a serem configurados antes do failover de teste ou do failover na assinatura
* Tamanho de VM do Azure recomendado para cada VM local

**Requisitos de infraestrutura local**
* Número necessário de servidores de configuração e servidores de processo a serem implantados no local

**Custo estimado de recuperação de desastre para Azure**
* Custo total estimado de recuperação de desastre para o Azure: custos de computação, armazenamento, rede e de licença do Site Recovery
* Detalhes de análise de custo por VM


>[!IMPORTANT]
>
>Como o uso provavelmente aumentará ao longo do tempo, todos os cálculos da ferramenta anterior são efetuados supondo um fator de crescimento de 30% nas características de carga de trabalho. Os cálculos também usam o valor de 95º percentil de todas as métricas de criação de perfil, como IOPS de leitura/gravação e variação. O fator de crescimento e o cálculo de percentil são configuráveis. Para saber mais sobre o fator de crescimento, confira a seção "Considerações sobre o fator de crescimento". Para saber mais sobre o valor de percentil, confira a seção "Valor de percentil usado para o cálculo".
>

## <a name="support-matrix"></a>Matriz de suporte

| | **VMware no Azure** |**Hyper-V para Azure**|**Azure para Azure**|**Hyper-V para site secundário**|**VMware para o site secundário**
--|--|--|--|--|--
Cenários com suporte |Sim|sim|Não |Sim*|Não 
Versão com suporte | vCenter 6.7, 6.5, 6.0 ou 5.5| Windows Server 2016, Windows Server 2012 R2 | ND |Windows Server 2016, Windows Server 2012 R2|ND
Configuração com suporte|vCenter, ESXi| cluster do Hyper-V, host do Hyper-V|ND|cluster do Hyper-V, host do Hyper-V|ND|
Número de servidores que podem ser analisados por instância em execução do Planejador de Implantações do Site Recovery |Único (máquinas virtuais que pertencem a um vCenter Server ou um servidor ESXi podem ser criados em um momento)|Vários (VMs em vários hosts ou clusters de hosts podem ter o perfil criado de uma vez)| ND |Vários (VMs em vários hosts ou clusters de hosts podem ter o perfil criado de uma vez)| ND

*A ferramenta destina-se principalmente ao cenário de recuperação de desastre do Hyper-V para o Azure. Para a recuperação de desastre do Hyper-V para o site secundário, ela só pode ser usada para entender as recomendações da origem, como a largura de banda de rede necessária, o espaço livre de armazenamento necessário em cada um dos servidores Hyper-V de origem e os números e as definições de lote da replicação inicial. Ignore as recomendações do Azure e os custos do relatório. Além disso, a operação Obter Taxa de Transferência não é aplicável ao cenário de recuperação de desastre do Hyper-V para o site secundário.

## <a name="prerequisites"></a>Pré-requisitos
A ferramenta tem duas fases principais: criação de perfil e geração de relatórios. Também há uma terceira opção para calcular apenas a taxa de transferência. Os requisitos para o servidor no qual a medida de taxa de transferência e a criação de perfil são iniciadas são apresentados na tabela a seguir.

| Requisito de servidor | DESCRIÇÃO|
|---|---|
|Medida de taxa de transferência e criação de perfil| <ul><li>Sistema operacional: Windows Server 2016 ou Windows Server 2012 R2<br>(a correspondência ideal são pelo menos as [recomendações de tamanho para o servidor de configuração](https://aka.ms/asr-v2a-on-prem-components))</li><li>Configuração do computador: 8 vCPUs, 16 GB de RAM, HDD de 300 GB</li><li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Pacotes Redistribuíveis do Visual C++ para Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Acesso à Internet para o Azure neste servidor</li><li>Conta de Armazenamento do Azure</li><li>Acesso de administrador no servidor</li><li>Mínimo de 100 GB de espaço livre em disco (supondo que haja 1.000 VMs com uma média de três discos em cada, com criação de perfil por 30 dias)</li><li>As configurações no nível das estatísticas do VMware vCenter podem ser do nível 1 ou superior</li><li>Permitir porta do vCenter (padrão 443): O Planejador de Implantações do Azure Site Recovery usa essa porta para conectar o host do vCenter Server/ESXi</ul></ul>|
| Geração de relatórios | Um computador Windows ou Windows Server com o Excel 2013 ou posterior.<li>[.NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Pacotes Redistribuíveis do Visual C++ para Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>O [VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli) só será necessário quando você passar - opção de usuário no comando de geração de relatório para buscar as informações mais recentes sobre configuração das VMs. O Planejador de Implantações conecta-se ao vCenter Server. Permitir que a porta do vCenter (443 padrão) se conecte ao servidor do vCenter.</li>|
| Permissões de usuário | Permissão somente leitura para a conta de usuário que é usada para acessar o servidor vCenter VMware/host ESXi vSphere VMware durante a criação de perfil |

> [!NOTE]
>
>A ferramenta pode criar o perfil apenas para VMs com discos VMDK e RDM. Ela não pode criar o perfil de VMs com discos iSCSI ou NFS. O Site Recovery dá suporte a discos iSCSI e NFS para servidores VMware. Como o planejador de implantação não está no convidado e cria perfis apenas usando contadores de desempenho do vCenter, a ferramenta não tem visibilidade para esses tipos de disco.
>

## <a name="download-and-extract-the-deployment-planner-tool"></a>Baixe e extraia a ferramenta de planejamento de implantação
1. Baixe a versão mais recente do [Planejador de Implantação do Site Recovery](https://aka.ms/asr-deployment-planner).
A ferramenta é empacotada em uma pasta .zip. A versão atual da ferramenta dá suporte apenas ao cenário do VMware para o Azure.

2. Copie a pasta .zip para o servidor Windows do qual você deseja executar a ferramenta.
Você poderá executar a ferramenta no Windows Server 2012 R2 se o servidor tiver acesso à rede para se conectar ao servidor vCenter/host ESXi vSphere que mantém as VMs para a criação de perfil. No entanto, é recomendável que você execute a ferramenta em um servidor cuja configuração de hardware atenda à [orientação de dimensionamento do servidor de configuração](https://aka.ms/asr-v2a-on-prem-components). Se já tiver implantado os componentes do Site Recovery no local, execute a ferramenta no servidor de configuração.

    Recomendamos que você tenha a mesma configuração de hardware que o servidor de configuração (que tem um servidor de processo integrado) no servidor em que executa a ferramenta. Essa configuração garante que a taxa de transferência obtida relatada pela ferramenta corresponda à taxa de transferência real que o Site Recovery pode alcançar durante a replicação. O cálculo de taxa de transferência depende da largura de banda disponível no servidor e da configuração de hardware (como CPU e armazenamento) do servidor. Se você executar a ferramenta de qualquer outro servidor, a taxa de transferência será calculada a partir desse servidor para o Azure. Além disso, como a configuração de hardware do servidor pode ser diferente daquela do servidor de configuração, a taxa de transferência obtida relatada pela ferramenta de relatórios pode ser imprecisa.

3. Extraia a pasta .zip.
A pasta contém vários arquivos e subpastas. O arquivo executável é ASRDeploymentPlanner.exe na pasta pai.

    Exemplo: Copie o arquivo .zip para a unidade E:\ e extraia-o.
    E:\ASR Deployment Planner_v2.3.zip

    E:\ASR Deployment Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="update-to-the-latest-version-of-deployment-planner"></a>Atualizar para a versão mais recente do Planejador de Implantações
Se você tiver uma versão anterior do Planejador de Implantações, siga um destes procedimentos:
 * Se a versão mais recente não contém uma correção de criação de perfil, e a criação de perfil já está em andamento na versão atual do planejador, continue a criação de perfil.
 * Se a versão mais recente contém uma correção de criação de perfil, é recomendável parar a criação de perfil na versão atual e reiniciá-la com a nova versão.


 >[!NOTE]
 >
 >Ao iniciar a criação de perfil com a nova versão, passe o mesmo caminho de diretório de saída para que a ferramenta acrescente dados de perfil aos arquivos existentes. Um conjunto completo de dados da criação de perfil será usado para gerar o relatório. Se você passar um diretório de saída diferente, novos arquivos serão criados, e os dados de criação de perfil antigos não serão usados para gerar o relatório.
 >
 >Cada nova versão do Planejador de Implantações é uma atualização cumulativa do arquivo .zip. Você não precisa copiar os arquivos mais recentes para a pasta anterior. Você pode criar e usar uma nova pasta.


## <a name="version-history"></a>Histórico de versão
A versão mais recente da ferramenta Planejador de implantação do Site Recovery é 2.4.
Consulte a página [Histórico de versões do Planejador de Implantações do Site Recovery](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) para obter as correções adicionadas em cada atualização.

## <a name="next-steps"></a>Próximas etapas
[Executar o Planejador de implantação do Site Recovery](site-recovery-vmware-deployment-planner-run.md)
