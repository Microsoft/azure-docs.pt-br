---
title: Planejador de Implantações para a recuperação de desastres do Hyper-V com Azure Site Recovery
description: Saiba mais sobre a recuperação de desastre do Hyper-V do Planejador de Implantações do Azure Site Recovery para o Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/13/2020
ms.author: mayg
ms.openlocfilehash: e4f1931aab056306ac5e9f9e9ef402ca26ec2d19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86528937"
---
# <a name="about-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Saiba mais sobre o Planejador de Implantações do Azure Site Recovery para recuperação de desastre do Hyper-V para o Azure

Este artigo é o guia do usuário do Planejador de Implantações do Azure Site Recovery para implantações de produção do Hyper-V para o Azure.

Antes de começar a proteger quaisquer máquinas virtuais (VMs) do Hyper-V usando o Site Recovery, aloque largura de banda suficiente com base em sua taxa diária de alteração de dados para atender ao Objetivo de Ponto de Recuperação (RPO) desejado e aloque espaço de armazenamento livre suficiente em cada volume do armazenamento do Hyper-V local.

Você também precisa criar o tipo e o número correto de contas de armazenamento do Azure de destino. Você cria contas de armazenamento standard ou premium, acrescentando o crescimento nos servidores de produção de origem devido ao aumento no uso ao longo do tempo. Você escolhe o tipo de armazenamento por VM, com base nas características de carga de trabalho, por exemplo, operações de E/S de leitura/gravação por segundo (IOPS) ou variação de dados, e nos limites do Azure Site Recovery. 

O planejador de implantação do Azure Site Recovery é uma ferramenta de linha de comando para ambos os cenários de recuperação de desastre do Hyper-V para o Azure e do VMware para o Azure. Você pode analisar remotamente as máquinas virtuais do Hyper-V presentes em vários hosts Hyper-V usando essa ferramenta (sem nenhum impacto na produção) para entender os requisitos de largura de banda e do Armazenamento do Azure para o êxito da replicação e do failover de teste/failover. Você pode executar a ferramenta sem instalar componentes do Azure Site Recovery no local. No entanto, para obter resultados precisos da taxa de transferência obtida, recomendamos que você execute o planejador em um Windows Server que tenha a mesma configuração de hardware de um dos servidores do Hyper-V que você usará para habilitar a proteção de recuperação de desastre no Azure. 

A ferramenta fornece os seguintes detalhes:

**Avaliação de compatibilidade**

* Avaliação de qualificação de VM com base no número de discos, no tamanho do disco, em IOPS, em variações e em algumas características da VM.

**Avaliação de largura de banda de rede necessária versus RPO**

* A largura de banda de rede estimada que é necessária para a replicação delta
* A taxa de transferência que o Azure Site Recovery pode obter do local para o Azure
* O RPO que pode ser obtido para uma determinada largura de banda
* Impacto sobre o RPO desejado se for provisionada menos largura de banda.

    
**Requisitos de infraestrutura do Azure**

* O requisito de tipo de armazenamento (conta de armazenamento standard ou premium) para cada VM
* O número total de contas de armazenamento standard e premium a serem configuradas para replicação
* Sugestões de nomes de contas de armazenamento com base na orientação do Armazenamento do Azure
* O posicionamento da conta de armazenamento para todas as VMs
* O número de núcleos do Azure a serem configurados antes do failover de teste ou do failover na assinatura
* O tamanho de VM do Azure recomendado para cada VM local

**Requisitos de infraestrutura local**
* O espaço de armazenamento livre necessário em cada volume do armazenamento do Hyper-V para a replicação inicial e a replicação delta bem-sucedidas para garantir que a replicação de VM não causará nenhum tempo de inatividade indesejado para seus aplicativos de produção
* Frequência máxima de cópia a ser definida para replicação do Hyper-V

**Diretrizes de envio em lote da replicação inicial** 
* Número de lotes de VMs a serem usados para proteção
* Lista de VMs em cada lote
* Ordem na qual cada lote deve ser protegido
* Tempo estimado para concluir a replicação inicial de cada lote

**Custo estimado de recuperação de desastre para o Azure**
* Custo total estimado de recuperação de desastre para o Azure: custos de licença de computação, armazenamento, rede e do Azure Site Recovery
* Detalhes de análise de custo por VM



>[!IMPORTANT]
>
>Como o uso provavelmente aumentará ao longo do tempo, todos os cálculos da ferramenta anterior são efetuados supondo um fator de crescimento de 30% nas características de carga de trabalho e usando um valor do 95º percentil de todas as métricas de análise (IOPS de leitura/gravação, variação e assim por diante). Esses dois elementos (fator de crescimento e cálculo de percentil) são configuráveis. Para saber mais sobre o fator de crescimento, confira a seção "Considerações sobre o fator de crescimento". Para saber mais sobre o valor de percentil, confira a seção "Valor de percentil usado para o cálculo".
>

## <a name="support-matrix"></a>Matriz de suporte

|**Categorias** | **VMware no Azure** |**Hyper-V para Azure**|**Azure para Azure**|**Hyper-V para site secundário**|**VMware para o site secundário**
--|--|--|--|--|--
Cenários com suporte |Sim|Sim|Não|Sim*|Não
Versão com suporte | vCenter 6,7, 6,5, 6,0 ou 5,5| Windows Server 2016, Windows Server 2012 R2 | NA |Windows Server 2016, Windows Server 2012 R2|NA
Configuração com suporte|vCenter, ESXi| cluster do Hyper-V, host do Hyper-V|NA|cluster do Hyper-V, host do Hyper-V|NA|
Número de servidores que podem ser analisados por instância em execução do Planejador de Implantações do Azure Site Recovery |Único (máquinas virtuais que pertencem a um vCenter Server ou um servidor ESXi podem ser criados em um momento)|Vários (as máquinas virtuais em vários hosts ou clusters de hosts podem ser analisadas de cada vez)| NA |Vários (as máquinas virtuais em vários hosts ou clusters de hosts podem ser analisadas de cada vez)| NA

*A ferramenta destina-se principalmente ao cenário de recuperação de desastre do Hyper-V para o Azure. Para a recuperação de desastre do Hyper-V para o site secundário, ela só pode ser usado para entender as recomendações do lado de origem, como a largura de banda de rede necessária, o espaço livre de armazenamento necessário em cada um dos servidores Hyper-V de origem e os números e as definições de lote da replicação inicial.  Ignore as recomendações do Azure e os custos do relatório. Além disso, a operação Obter Taxa de Transferência não é aplicável ao cenário de recuperação de desastre do Hyper-V para o site secundário.

## <a name="prerequisites"></a>Pré-requisitos
A ferramenta tem três fases principais para o Hyper-v: obter lista de VMs, análise e geração de relatórios. Também há uma quarta opção para calcular apenas a taxa de transferência. Os requisitos para o servidor no qual as diferentes fases precisam ser executadas são apresentados na tabela a seguir:

| Requisito de servidor | Descrição |
|---|---|
|Obter lista de VMs, criação de perfis e medida de taxa de transferência |<ul><li>Sistema operacional: Microsoft Windows Server 2016 ou Microsoft Windows Server 2012 R2 </li><li>Configuração de máquina: 8 vCPUs, 16 GB de RAM, 300 GB de disco rígido</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[Microsoft Visual C++ redistribuível para Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Acesso à Internet para o Azure (*. blob.core.windows.net) deste servidor, porta 443<br>[Isso é opcional. Você pode optar por fornecer a largura de banda disponível durante a geração de relatórios manualmente.</li><li>Conta de Armazenamento do Azure</li><li>Acesso de administrador no servidor</li><li>Mínimo de 100 GB de espaço livre em disco (supondo que haja 1000 VMs com uma média de três discos em cada, com criação de perfil por 30 dias)</li><li>A VM de onde você está executando a ferramenta de planejamento de implantação do Azure Site Recovery deve ser adicionada à lista TrustedHosts de todos os servidores do Hyper-V.</li><li>Os servidores Hyper-V a serem analisados devem ser adicionados à lista TrustedHosts da VM cliente na qual a ferramenta está sendo executada. [Saiba mais sobre como adicionar servidores à lista TrustedHosts](#steps-to-add-servers-into-trustedhosts-list). </li><li> A ferramenta deve ser executada com privilégios administrativos do PowerShell ou do console de linha de comando no cliente</ul></ul>|
| Geração de relatórios | Um PC Windows ou Windows Server com o Microsoft Excel 2013 ou posterior |
| Permissões de usuário | Conta de administrador para acessar o cluster do Hyper-V/host do Hyper-V durante operações de criação de perfis e de obtenção de lista de VM.<br>Todos os hosts que precisam ser analisados devem ter um domínio do administrador com as mesmas credenciais de conta ou seja, nome de usuário e senha
 |

## <a name="steps-to-add-servers-into-trustedhosts-list"></a>Etapas para adicionar servidores à lista TrustedHosts
1. A VM de onde a ferramenta deve ser implantada sempre deverá ter todos os hosts a serem analisados em sua lista TrustedHosts. Para adicionar o cliente à lista de Trustedhosts, execute o seguinte comando de um PowerShell elevado na VM. A VM pode ser um Windows Server 2012 R2 ou um Windows Server 2016. 

   ```powershell
   set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate
   ```
1. Cada host do Hyper-V a ser analisado deve ter:

    a. A máquina virtual na qual a ferramenta vai ser executado em sua lista TrustedHosts. Execute o comando a seguir em uma sessão do PowerShell com privilégios elevados no host do Hyper-V.

      ```powershell
      set-item wsman:\localhost\Client\TrustedHosts -value '<ComputerName>[,<ComputerName>]' -Concatenate
      ```

    b. Comunicação remota do PowerShell habilitada.

      ```powershell
      Enable-PSRemoting -Force
      ```

## <a name="download-and-extract-the-deployment-planner-tool"></a>Baixe e extraia a ferramenta de planejamento de implantação

1.  Baixe a versão mais recente do [planejador de implantação Azure site Recovery](https://aka.ms/asr-deployment-planner).
A ferramenta é empacotada em uma pasta .zip. A mesma ferramenta oferece suporte para os cenários de recuperação de desastre do VMware para o Azure e do Hyper-V para o Azure. Você também pode usar essa ferramenta para o cenário de recuperação de desastre do Hyper-V-para um site secundário mas ignorar a recomendação de infraestrutura do Azure do relatório.

1.  Copie a pasta .zip para o Windows Server do qual você deseja executar a ferramenta. Você pode executar a ferramenta em um Windows Server 2012 R2 ou um Windows Server 2016. O servidor deve ter acesso de rede para se conectar ao cluster do Hyper-V ou ao host do Hyper-V que contém as VMs para análise. É recomendável que você tenha a mesma configuração de hardware na VM onde a ferramenta será executada e no servidor do Hyper-V que você deseja proteger. Essa configuração garante que a taxa de transferência obtida relatada pela ferramenta corresponda à taxa de transferência real que o Azure Site Recovery pode alcançar durante a replicação. O cálculo de taxa de transferência depende da largura de banda disponível no servidor e da configuração de hardware (CPU, armazenamento e assim por diante) do servidor. A taxa de transferência é calculada desde o servidor onde a ferramenta está em execução no Azure. Se a configuração de hardware do servidor for diferente do servidor do Hyper-V, a taxa de transferência obtida relatada pela ferramenta será imprecisa.
A configuração recomendada da VM: 8 vCPUs, 16 GB de RAM, disco rígido de 300 GB.

1.  Extraia a pasta .zip.
A pasta contém vários arquivos e subpastas. O arquivo executável é ASRDeploymentPlanner.exe na pasta pai.

Exemplo: copiar o arquivo .zip para a unidade E:\ e extraí-lo. E:\ASR Deployment Planner_v2.3.zip

E:\ASR Deployment Planner_v2.3\ASRDeploymentPlanner.exe

### <a name="updating-to-the-latest-version-of-deployment-planner"></a>Atualização para a versão mais recente do Planejador de Implantações

As atualizações mais recentes são resumidas no [histórico de versão](site-recovery-deployment-planner-history.md)do planejador de implantações.

Se você tiver uma versão anterior do Planejador de Implantações, siga um destes procedimentos:
 * Se a versão mais recente não contém uma correção de criação de perfil, e a criação de perfil já está em andamento na versão atual do planejador, continue a criação de perfil.
 * Se a versão mais recente contém uma correção de criação de perfil, é recomendável parar criação de perfil na versão atual e reiniciá-la com a nova versão.


  >[!NOTE]
  >
  >Ao iniciar a criação de perfil com a nova versão, passe o mesmo caminho de diretório de saída para que a ferramenta acrescente dados de perfil aos arquivos existentes. Um conjunto completo de dados com criação de perfil será ser usado para gerar o relatório. Se você passar um diretório de saída diferente, novos arquivos serão criados, e os dados de criação de perfil antigos não serão usados para gerar o relatório.
  >
  >Cada novo planejador de implantação é uma atualização cumulativa do arquivo .zip. Você não precisa copiar os arquivos mais recentes para a pasta anterior. Você pode criar e usar uma nova pasta.

## <a name="version-history"></a>Histórico de versão
A versão mais recente da ferramenta de Planejador de Implantações do Azure Site Recovery é 2,5.
Consulte Planejador de Implantações do Azure Site Recovery página de [histórico de versão](https://social.technet.microsoft.com/wiki/contents/articles/51049.asr-deployment-planner-version-history.aspx) para as correções que são adicionadas em cada atualização.


## <a name="next-steps"></a>Próximas etapas
* [Execute o planejador de implantação](./hyper-v-deployment-planner-run.md).
