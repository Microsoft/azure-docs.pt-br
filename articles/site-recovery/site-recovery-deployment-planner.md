---
title: "Planejador de implantação do Azure Site Recovery para VMware ao Azure | Microsoft Docs"
description: "Este é o guia do usuário do planejador de implantação do Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/28/2017
ms.author: nisoneji
ms.openlocfilehash: 60b0641076c2fa8ed2feb5c64e7b119519f46cf4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-site-recovery-deployment-planner"></a>Planejador de implantação do Azure Site Recovery
Este artigo é o guia do usuário do Planejador de Implantação do Azure Site Recovery para implantações de produção do VMware para o Azure.

## <a name="overview"></a>Visão geral

Antes de começar a proteger VMs (máquinas virtuais) VMware usando o Site Recovery, aloque largura de banda suficiente, com base em sua taxa de alteração de dados diária, para atender ao RPO (objetivo de ponto de recuperação) desejado. Implante o número correto de servidores de configuração e servidores de processo locais.

Você também precisa criar o tipo e o número correto de contas de armazenamento do Azure de destino. Você cria contas de armazenamento standard ou premium, acrescentando o crescimento nos servidores de produção de origem devido ao aumento no uso ao longo do tempo. Você escolhe o tipo de armazenamento por VM, com base nas características de carga de trabalho (por exemplo, operações de E/S de leitura/gravação por segundo [IOPS] ou variação de dados) e nos limites do Site Recovery.

A visualização pública do planejador de implantação do Azure Site Recovery é uma ferramenta de linha de comando atualmente disponível apenas para o cenário do VMware para o Azure. Você pode criar o perfil remotamente das VMs do VMware usando essa ferramenta (sem impacto na produção) para entender os requisitos de largura de banda e do Armazenamento do Azure para o êxito da replicação e do failover de teste. Você pode executar a ferramenta sem instalar componentes do Site Recovery no local. No entanto, para obter resultados precisos da taxa de transferência obtida, recomendamos que você execute o planejador em um Windows Server que atenda aos requisitos mínimos do servidor de configuração de Site Recovery que você provavelmente precisará implantar como uma das primeiras etapas da implantação de produção.

A ferramenta fornece os seguintes detalhes:

**Avaliação de compatibilidade**

* Avaliação de qualificação de uma VM com base no número de discos, no tamanho do disco, no IOPS, variação e tipo de inicialização (EFI/BIOS)
* A largura de banda de rede estimada que é necessária para a replicação delta

**Largura de banda de rede necessária versus avaliação de RPO**

* A largura de banda de rede estimada que é necessária para a replicação delta
* A taxa de transferência que o Site Recovery pode obter do local para o Azure
* O número de VMs a serem incluídas em um lote com base na largura de banda estimada para concluir a replicação inicial em determinado período de tempo

**Requisitos de infraestrutura do Azure**

* O requisito de tipo de armazenamento (conta de armazenamento standard ou premium) para cada VM
* O número total de contas de armazenamento standard e premium a serem configuradas para replicação
* Sugestões de nomes de contas de armazenamento com base na orientação do Armazenamento do Azure
* O posicionamento da conta de armazenamento para todas as VMs
* O número de núcleos do Azure a serem configurados antes do failover de teste ou do failover na assinatura
* O tamanho de VM do Azure recomendado para cada VM local

**Requisitos de infraestrutura local**
* O número necessário de servidores de configuração e servidores de processo a serem implantados no local

>[!IMPORTANT]
>
>Como o uso provavelmente aumentará ao longo do tempo, todos os cálculos da ferramenta anterior são efetuados supondo um fator de crescimento de 30% nas características de carga de trabalho e usando um valor do 95º percentil de todas as métricas de criação de perfil (IOPS de leitura/gravação, variação e assim por diante). Esses dois elementos (fator de crescimento e cálculo de percentil) são configuráveis. Para saber mais sobre o fator de crescimento, confira a seção "Considerações sobre o fator de crescimento". Para saber mais sobre o valor de percentil, confira a seção "Valor de percentil usado para o cálculo".
>

## <a name="requirements"></a>Requisitos
A ferramenta tem duas fases principais: criação de perfil e geração de relatórios. Também há uma terceira opção para calcular apenas a taxa de transferência. Os requisitos para o servidor do qual a medida de taxa de transferência e criação de perfil é iniciada são apresentados na seguinte tabela:

| Requisito de servidor | Descrição|
|---|---|
|Medida de taxa de transferência e criação de perfil| <ul><li>Sistema operacional: Microsoft Windows Server 2012 R2<br>(a correspondência ideal são pelo menos as [recomendações de tamanho para o servidor de configuração](https://aka.ms/asr-v2a-on-prem-components))</li><li>Configuração de máquina: 8 vCPUs, 16 GB de RAM, 300 GB de disco rígido</li><li>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)</li><li>[VMware vSphere PowerCLI 6.0 R3](https://aka.ms/download_powercli)</li><li>[Microsoft Visual C++ redistribuível para Visual Studio 2012](https://aka.ms/vcplusplus-redistributable)</li><li>Acesso à Internet para o Azure neste servidor</li><li>Conta de Armazenamento do Azure</li><li>Acesso de administrador no servidor</li><li>Mínimo de 100 GB de espaço livre em disco (supondo que haja 1000 VMs com uma média de três discos em cada, com criação de perfil por 30 dias)</li><li>As configurações no nível das estatísticas do VMware vCenter devem ser definidas para o nível 2 ou superior</li><li>Permitir porta 443: o Planejador de implantação da ASR usa essa porta para se conectar ao host de ESXi/servidor do vCenter</ul></ul>|
| Geração de relatórios | Um PC Windows ou Windows Server com o Microsoft Excel 2013 ou posterior |
| Permissões de usuário | Permissão somente leitura para a conta de usuário que é usada para acessar o servidor vCenter VMware/host ESXi vSphere VMware durante a criação de perfil |

> [!NOTE]
>
>A ferramenta pode criar o perfil apenas para VMs com discos VMDK e RDM. Ela não pode criar o perfil de VMs com discos iSCSI ou NFS. O Site Recovery dá suporte a discos iSCSI e NFS para servidores VMware, mas, como o planejador de implantação não está no convidado e cria perfis apenas usando contadores de desempenho do vCenter, a ferramenta não tem visibilidade para esses tipos de disco.
>

## <a name="download-and-extract-the-public-preview"></a>Baixar e extrair a visualização pública
1. Baixe a versão mais recente da [visualização pública do planejador de implantação do Site Recovery](https://aka.ms/asr-deployment-planner).  
A ferramenta é empacotada em uma pasta .zip. A versão atual da ferramenta dá suporte apenas ao cenário do VMware para o Azure.

2. Copie a pasta .zip para o servidor Windows do qual você deseja executar a ferramenta.  
Você poderá executar a ferramenta no Windows Server 2012 R2 se o servidor tiver acesso à rede para se conectar ao servidor vCenter/host ESXi vSphere que mantém as VMs para a criação de perfil. No entanto, recomendamos que você execute a ferramenta em um servidor cuja configuração de hardware atenda à [orientação de dimensionamento do servidor de configuração](https://aka.ms/asr-v2a-on-prem-components). Se já tiver implantado os componentes do Site Recovery no local, execute a ferramenta no servidor de configuração.

 Recomendamos que você tenha a mesma configuração de hardware que o servidor de configuração (que tem um servidor de processo integrado) no servidor em que executa a ferramenta. Essa configuração garante que a taxa de transferência obtida relatada pela ferramenta corresponda à taxa de transferência real que o Site Recovery pode alcançar durante a replicação. O cálculo de taxa de transferência depende da largura de banda disponível no servidor e da configuração de hardware (CPU, armazenamento e assim por diante) do servidor. Se você executar a ferramenta de qualquer outro servidor, a taxa de transferência será calculada desse servidor para o Microsoft Azure. Além disso, como a configuração de hardware do servidor pode ser diferente daquela do servidor de configuração, a taxa de transferência obtida relatada pela ferramenta de relatórios pode ser imprecisa.

3. Extraia a pasta .zip.  
A pasta contém vários arquivos e subpastas. O arquivo executável é ASRDeploymentPlanner.exe na pasta pai.

    Exemplo:  
    Copie o arquivo .zip para a unidade E:\ e extraia-o.
   E:\ASR Deployment Planner-Preview_v1.2.zip

    E:\ASR Deployment Planner-Preview_v1.2\ ASR Deployment Planner-Preview_v1.2\ ASRDeploymentPlanner.exe

## <a name="capabilities"></a>Funcionalidades
Você pode executar a ferramenta de linha de comando (ASRDeploymentPlanner.exe) em qualquer um dos três seguintes modos:

1. Criação de perfil  
2. Geração de relatórios
3. Obter taxa de transferência

Primeiro, execute a ferramenta no modo de criação de perfil para obter a variação de dados de VM e IOPS. Em seguida, execute a ferramenta para gerar o relatório e obter os requisitos de armazenamento e largura de banda de rede.

## <a name="profiling"></a>Criação de perfil
No modo de criação de perfil, a ferramenta de planejador de implantação conecta-se ao servidor vCenter/host ESXi vSphere para coletar dados de desempenho sobre a VM.

* A criação de perfil não afeta o desempenho das VMs de produção, pois nenhuma conexão direta é feita a elas. Todos os dados de desempenho são coletados do servidor vCenter/host ESXi vSphere.
* Para garantir que haja um impacto irrelevante no servidor devido à criação de perfil, a ferramenta consulta o servidor vCenter/host ESXi vSphere uma vez a cada 15 minutos. Esse intervalo de consulta não compromete a precisão da criação de perfil, pois a ferramenta armazena dados do contador de desempenho de cada minuto.

### <a name="create-a-list-of-vms-to-profile"></a>Criar uma lista de VMs para criação de perfil
Primeiro, você precisa de uma lista de VMs para a criação de perfil. Você pode obter todos os nomes de VMs em um servidor vCenter/host ESXi vSphere usando os comandos PowerCLI vSphere VMware no procedimento a seguir. Como alternativa, você pode listar em um arquivo os nomes amigáveis ou os endereços IP das VMs para as quais deseja criar o perfil manualmente.

1. Entre na VM em que PowerCLI vSphere VMware está instalado.
2. Abra o console PowerCLI vSphere VMware.
3. Verifique se a política de execução está habilitada para o script. Se ele estiver desabilitado, inicie o console PowerCLI vSphere VMware no modo de administrador e habilite-o executando o seguinte comando:

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4. Como opção, você pode querer executar o comando a seguir se Connect-VIServer não é reconhecido como o nome do cmdlet.
 
            Add-PSSnapin VMware.VimAutomation.Core 

5. Para obter todos os nomes de VMs em um servidor vCenter/host ESXi vSphere e armazenar a lista em um arquivo .txt, execute os dois comandos listados aqui.
Substitua &lsaquo;nome do servidor&rsaquo;, &lsaquo;nome de usuário&rsaquo;, &lsaquo;senha&rsaquo; e &lsaquo;outputfile.txt&rsaquo;; por suas entradas.

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>

6. Abra o arquivo de saída no Bloco de Notas e copie os nomes de todas as VMs para as quais deseja criar o perfil para outro arquivo (por exemplo, ProfileVMList.txt), com um nome de VM por linha. Esse arquivo é usado como entrada para o parâmetro *-VMListFile* da ferramenta de linha de comando.

    ![Lista de nomes de VM no planejador de implantação](./media/site-recovery-deployment-planner/profile-vm-list.png)

### <a name="start-profiling"></a>Iniciar criação de perfil
Após obter a lista de VMs para criação de perfil, você pode executar a ferramenta no modo de criação de perfil. Aqui está a lista de parâmetros obrigatórios e opcionais da ferramenta para execução no modo de criação de perfil.

ASRDeploymentPlanner.exe -Operation StartProfiling /?

| Nome do parâmetro | Descrição |
|---|---|
| -Operation | StartProfiling |
| -Server | O nome de domínio totalmente qualificado ou o endereço IP do servidor vCenter/host ESXi vSphere cujas VMs devem ser submetidas à criação de perfil.|
| -User | O nome de usuário a ser conectado ao servidor vCenter/host ESXi vSphere. O usuário precisa ter acesso somente leitura, no mínimo.|
| -VMListFile | O arquivo que contém a lista de VMs para criação de perfil. O caminho do arquivo pode ser absoluto ou relativo. O arquivo deve conter um nome de VM/endereço IP por linha. O nome de máquina virtual especificado no arquivo deve ser igual ao nome da VM no servidor vCenter/host ESXi vSphere.<br>Por exemplo, o arquivo VMList.txt contém as seguintes VMs:<ul><li>máquina_virtual_A</li><li>10.150.29.110</li><li>máquina_virtual_B</li><ul> |
| -NoOfDaysToProfile | O número de dias pelos quais a criação de perfil deve ser executada. É recomendável executar a criação de perfil por mais de 15 dias para garantir que o padrão de carga de trabalho no ambiente durante o período especificado seja observado e usado para fornecer uma recomendação precisa. |
| -Directory | (Opcional) A UNC (convenção de nomenclatura universal) ou o caminho do diretório local para armazenar dados de criação de perfil gerados durante a criação de perfil. Se um nome de diretório não for especificado, o diretório chamado 'ProfiledData' no caminho atual será usado como diretório padrão. |
| -Password | (Opcional) A senha a ser usada para se conectar ao host ESXi vSphere/servidor vCenter. Se não especificar uma agora, você será solicitado a fornecê-la quando o comando for executado.|
| -StorageAccountName | (Opcional) O nome da conta de armazenamento que é usado para localizar a taxa de transferência possível para replicação de dados do local do Azure. A ferramenta carrega dados de teste nessa conta de armazenamento para calcular a taxa de transferência.|
| -StorageAccountKey | (Opcional) A chave de conta de armazenamento que é usada para acessar a conta de armazenamento. Acesse o portal do Azure > Contas de armazenamento > <*Nome de conta de armazenamento*> > Configurações > Chaves de Acesso > Chave1 (ou chave de acesso primário da conta de armazenamento clássico). |
| -Ambiente | (opcional) Este é o seu ambiente de conta do Armazenamento do Azure de destino. Isso pode ser um dos três valores: AzureCloud, AzureUSGovernment ou AzureChinaCloud. O padrão é AzureCloud. Use o parâmetro quando a região do Azure de destino é uma nuvem do Governo dos EUA do Azure ou Azure China. |


É recomendável que você crie o perfil de suas VMs durante pelo menos 15 a 30 dias. Durante o período de criação de perfil, ASRDeploymentPlanner.exe continua em execução. A ferramenta aceita a entrada de tempo de criação de perfil em dias. Para criar o perfil por algumas horas ou minutos e fazer um teste rápido da ferramenta, na visualização pública, você precisará converter o horário na medida equivalente de dias. Por exemplo, para criar o perfil por 30 minutos, a entrada deve ser 30/(60*24) = 0,021 dia. O tempo de criação de perfil mínimo permitido é de 30 minutos.

Durante a criação de perfil, opcionalmente, você pode passar um nome de conta de armazenamento e uma chave para obter a taxa de transferência que o Azure Site Recovery pode alcançar no momento da replicação do servidor de configuração ou do servidor de processo para o Azure. Se a chave e o nome da conta de armazenamento não forem passados durante a criação de perfil, a ferramenta não calculará a taxa de transferência possível.

Você pode executar várias instâncias da ferramenta para diversos conjuntos de VMs. Verifique se os nomes de VM não são repetidos nos conjuntos de criação de perfil. Por exemplo, se tiver criado o perfil de dez VMs (VM1 a VM10) e, após alguns dias, quiser criar o perfil de outras cinco VMs (VM11 a VM15), você poderá executar a ferramenta em outro console de linha de comando para o segundo conjunto de VMs (VM11 a VM15). Verifique se o segundo conjunto de VMs não tem nomes de VM da primeira instância de criação de perfil ou use um diretório de saída diferente para a segunda execução. Se forem usadas duas instâncias da ferramenta para a criação de perfil das mesmas VMs e for usado o mesmo diretório de saída, o relatório gerado será incorreto.

As configurações de VM são capturadas uma vez no início da operação de criação de perfil e armazenadas em um arquivo chamado VMDetailList.xml. Essas informações são usadas quando o relatório é gerado. Não é capturada alteração alguma na configuração de VM (por exemplo, um número maior de núcleos, discos ou NICs) do início ao fim da criação de perfil. Se uma configuração de VM com criação de perfil foi alterada durante a criação de perfil, na visualização pública, aqui está a solução alternativa para obter os detalhes mais recentes da VM ao gerar o relatório:

* Faça backup de VMdetailList.xml e exclua o arquivo do local atual.
* Passe os argumentos -User e -Password no momento da geração de relatórios.

O comando de criação de perfil gera vários arquivos no diretório de criação de perfil. Não exclua nenhum dos arquivos, pois isso afeta a geração de relatórios.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Exemplo 1: criar o perfil de VMs para 30 dias e obter a taxa de transferência do local para o Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Exemplo 2: criar o perfil de VMs por 15 dias

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-1-hour-for-a-quick-test-of-the-tool"></a>Exemplo 3: criar o perfil de VMs por uma hora para um teste rápido da ferramenta
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  0.04  -User vCenterUser1
```

>[!NOTE]
>
>* Se o servidor em que a ferramenta está sendo executada for reinicializado ou falhar ou se você fechar a ferramenta usando Ctrl + C, os dados de criação de perfil serão preservados. Porém, há uma possibilidade de perder os últimos 15 minutos de dados de criação de perfil. Nesse caso, execute novamente a ferramenta no modo de criação de perfil depois que o servidor for reiniciado.
>* Quando o nome da conta de armazenamento e a chave são passados, a ferramenta mede a taxa de transferência na última etapa de criação de perfil. Se a ferramenta for fechada antes da conclusão da criação de perfil, a taxa de transferência não será calculada. Para obter a taxa de transferência antes de gerar o relatório, você pode executar a operação GetThroughput no console de linha de comando. Caso contrário, o relatório gerado não conterá as informações de taxa de transferência.


## <a name="generate-a-report"></a>Gerar um relatório
A ferramenta gera um arquivo do Microsoft Excel com macros habilitadas (arquivo XLSM) como a saída de relatório, que resume todas as recomendações de implantação. O relatório é denominado DeploymentPlannerReport_<*identificador numérico exclusivo*>.xlsm e colocado no diretório especificado.

Após a conclusão da criação de perfil, você poderá executar a ferramenta no modo de geração de relatório. A tabela a seguir contém uma lista de parâmetros obrigatórios e opcionais da ferramenta para execução no modo de geração de relatórios.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Nome do parâmetro | Descrição |
|-|-|
| -Operation | GenerateReport |
| -Server |  O nome de domínio totalmente qualificado ou o endereço IP do servidor vCenter/vSphere (use o mesmo nome ou endereço IP que usou no momento da criação de perfil) em que se encontram as VMs com criação de perfil cujo relatório deve ser gerado. Observe que, se usou um servidor vCenter no momento da criação de perfil, você não pode usar um servidor vSphere para geração de relatórios e vice-versa.|
| -VMListFile | O arquivo que contém a lista de VMs com criação de perfil para as quais o relatório deve ser gerado. O caminho do arquivo pode ser absoluto ou relativo. O arquivo deve conter um nome de VM ou endereço IP por linha. Os nomes de VM que são especificados no arquivo devem ser iguais aos nomes de VM no servidor vCenter/host ESXi vSphere e corresponder ao que foi usado durante a criação de perfil.|
| -Directory | (Opcional) A UNC o ou caminho do diretório local em que os dados com perfil gerado (arquivos gerados durante a criação de perfil) são armazenados. Esses dados são necessários para gerar o relatório. Se não for especificado um nome, será usado o diretório 'ProfiledData'. |
| -GoalToCompleteIR | (Opcional) O número de horas em que a replicação inicial das VMs com criação de perfil precisa ser concluída. O relatório gerado fornece o número de VMs para as quais a replicação inicial pode ser concluída no tempo especificado. O padrão é de 72 horas. |
| -User | (Opcional) O nome de usuário a ser usado para se conectar ao servidor vCenter/vSphere. O nome é usado para buscar as informações de configuração mais recentes das VMs, como o número de discos, o número de núcleos e o número de NICs, para uso no relatório. Se o nome não for fornecido, serão usadas as informações de configuração coletadas no início da criação de perfil. |
| -Password | (Opcional) A senha a ser usada para se conectar ao host ESXi vSphere/servidor vCenter. Se a senha não for especificada como um parâmetro, você deverá fornecê-la mais tarde, quando o comando for executado. |
| -DesiredRPO | (Opcional) O objetivo de ponto de recuperação desejado, em minutos. O padrão é de 15 minutos.|
| -Bandwidth | Largura de banda em Mbps. O parâmetro a ser usado para calcular o RPO que pode ser obtido para a largura de banda especificada. |
| -StartDate | (Opcional) A data e a hora de início em MM-DD-YYYY:HH:MM (formato de 24 horas). *StartDate* deve ser especificado junto com *EndDate*. Quando StartDate é especificado, o relatório é gerado para os dados de criação de perfil que são coletados entre StartDate e EndDate. |
| -EndDate | (Opcional) A data e a hora de término em MM-DD-YYYY:HH:MM (formato de 24 horas). *EndDate* deve ser especificado junto com *StartDate*. Quando EndDate é especificado, o relatório é gerado para os dados de criação de perfil que são coletados entre StartDate e EndDate. |
| -GrowthFactor | (Opcional) O fator de crescimento, expressado como uma porcentagem. O padrão é 30%. |
| -UseManagedDisks | (Opcional) UseManagedDisks - Sim/Não. O padrão é Sim. O número de máquinas virtuais que podem ser colocadas em uma única conta de armazenamento é calculado considerando-se o Failover/Failover de teste de máquinas virtuais é feito em um disco gerenciado em vez de em um disco não gerenciado. |

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Exemplo 1: gerar um relatório com os valores padrão quando os dados de criação de perfil estiverem na unidade local
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Exemplo 2: gerar um relatório quando os dados de criação de perfil estiverem em um servidor remoto
Você deve ter acesso de leitura/gravação no diretório remoto.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Exemplo 3: para gerar um relatório com uma largura de banda específica e com o objetivo de concluir IR no tempo especificado
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Exemplo 4: gerar um relatório com um fator de crescimento de 5% em vez do padrão de 30%
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Exemplo 5: gerar um relatório com um subconjunto dos dados de criação de perfil
Por exemplo, você tem 30 dias de dados de criação de perfil e deseja gerar um relatório de apenas 20 dias.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>Exemplo 6: gerar um relatório para o RPO de cinco minutos
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

## <a name="percentile-value-used-for-the-calculation"></a>Valor de percentil usado para o cálculo
**Qual valor de percentil padrão das métricas de desempenho coletadas durante a criação de perfil a ferramenta usa ao gerar um relatório?**

A ferramenta usa como padrão os valores do 95º percentil de IOPS de leitura/gravação, IOPS de gravação e variação de dados coletados durante a criação de perfil de todas as VMs. Essa métrica garante que o pico do 100º percentil que suas VMs poderão ver devido a eventos temporários não seja usado para determinar seus requisitos de conta de armazenamento de destino e largura de banda de origem. Por exemplo, um evento temporário pode ser um trabalho de backup executado uma vez por dia, uma indexação de um banco de dados periódica ou uma atividade de geração de relatórios de análise ou outros eventos pontuais semelhantes de curta duração.

O uso de valores do 95º percentil proporciona a visão verdadeira das características de carga de trabalho reais e oferece o melhor desempenho quando as cargas de trabalho estão em execução no Azure. Não prevemos que você precise alterar esse número. Se alterar o valor (para o 90º percentil, por exemplo), você poderá atualizar o arquivo de configuração *ASRDeploymentPlanner.exe.config* na pasta padrão e salvá-lo para gerar um novo relatório nos dados de criação de perfil existentes.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>Considerações sobre o fator de crescimento
**Por que deve considerar o fator de crescimento ao planejar implantações?**

É fundamental levar em conta o crescimento em suas características de carga de trabalho, supondo um aumento potencial no uso ao longo do tempo. Depois que proteção estiver em vigor, se as características de carga de trabalho forem alteradas, você não poderá alternar para outra conta de armazenamento para proteção sem desabilitar e reabilitar a proteção.

Por exemplo, digamos que hoje a VM esteja em uma conta de replicação de armazenamento standard. Nos próximos três meses, várias alterações têm probabilidade de ocorrer:

* O número de usuários do aplicativo que é executado na VM aumentará.
* A maior variação resultante na VM exigirá que a VM vá para o armazenamento premium para que a replicação do Site Recovery possa acompanhar o ritmo.
* Consequentemente, você precisará desabilitar e reabilitar a proteção para uma conta de armazenamento premium.

É altamente recomendável que você planeje o crescimento durante o planejamento da implantação, embora o valor padrão seja 30%. Você é o especialista nas projeções de padrão de uso e crescimento de seu aplicativo e pode alterar esse número de forma adequada ao gerar um relatório. Além disso, você pode gerar vários relatórios com vários fatores de crescimento com os mesmos dados de criação de perfil e determinar quais recomendações de armazenamento de destino e largura de banda de origem funcionam melhor para você.

O relatório gerado do Microsoft Excel contém as seguintes informações:

* [Input](site-recovery-deployment-planner.md#input)
* [Recomendações](site-recovery-deployment-planner.md#recommendations-with-desired-rpo-as-input)
* [Entrada de Recommendations-Bandwidth](site-recovery-deployment-planner.md#recommendations-with-available-bandwidth-as-input)
* [VM<->Posicionamento de Armazenamento](site-recovery-deployment-planner.md#vm-storage-placement)
* [VMs compatíveis](site-recovery-deployment-planner.md#compatible-vms)
* [VMs incompatíveis](site-recovery-deployment-planner.md#incompatible-vms)

![Planejador de implantação](./media/site-recovery-deployment-planner/dp-report.png)

## <a name="get-throughput"></a>Obter taxa de transferência

Para estimar a taxa de transferência que o Site Recovery pode obter do local para o Azure durante a replicação, execute a ferramenta no modo GetThroughput. A ferramenta calcula a taxa de transferência do servidor em que a ferramenta está em execução. O ideal é que esse servidor se baseie no guia de dimensionamento do servidor de configuração. Se já tiver implantado componentes de infraestrutura locais do Site Recovery, execute a ferramenta no servidor de configuração.

Abra um console de linha de comando e acesse a pasta da ferramenta de planejamento de implantação do Site Recovery. Execute ASRDeploymentPlanner.exe com os parâmetros a seguir.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Nome do parâmetro | Descrição |
|-|-|
| -Operation | GetThroughput |
| -Directory | (Opcional) A UNC o ou caminho do diretório local em que os dados com perfil gerado (arquivos gerados durante a criação de perfil) são armazenados. Esses dados são necessários para gerar o relatório. Se não for especificado um nome de diretório, será usado o diretório 'ProfiledData'. |
| -StorageAccountName | O nome de conta de armazenamento usada para obter a largura de banda consumida para replicação de dados do local para o Azure. A ferramenta carrega dados de teste nessa conta de armazenamento para obter a largura de banda consumida. |
| -StorageAccountKey | A chave da conta de armazenamento usada para acessar a conta de armazenamento. Acesse o portal do Azure > Contas de armazenamento > <*Nome de conta de armazenamento*> > Configurações > Chaves de Acesso > Chave1 (ou chave de acesso primário da conta de armazenamento clássico). |
| -VMListFile | O arquivo que contém a lista de VMs para criação de perfil para calcular a largura de banda consumida. O caminho do arquivo pode ser absoluto ou relativo. O arquivo deve conter um nome de VM/endereço IP por linha. Os nomes de VM especificados no arquivo devem ser iguais aos nomes de VM no servidor vCenter/host ESXi vSphere.<br>Por exemplo, o arquivo VMList.txt contém as seguintes VMs:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Ambiente | (opcional) Este é o seu ambiente de conta do Armazenamento do Azure de destino. Isso pode ser um dos três valores: AzureCloud, AzureUSGovernment ou AzureChinaCloud. O padrão é AzureCloud. Use o parâmetro quando a região do Azure de destino é uma nuvem do Governo dos EUA do Azure ou Azure China. |

A ferramenta cria vários arquivos asrvhdfile<#>. vhd de 64 MB (em que "#" é o número de arquivos) no diretório especificado. A ferramenta carrega os arquivos para a conta de armazenamento para obter a taxa de transferência. Depois que a taxa de transferência é medida, a ferramenta exclui todos os arquivos da conta de armazenamento e do servidor local. Se a ferramenta for encerrada por qualquer motivo enquanto estiver calculando a taxa de transferência, não excluirá os arquivos do armazenamento ou do servidor local. Será necessário excluí-los manualmente.

A taxa de transferência é medida em um momento específico e é a taxa de transferência máxima que o Site Recovery pode atingir durante a replicação, desde que todos os outros fatores permaneçam iguais. Por exemplo, se qualquer aplicativo começar a consumir mais largura de banda na mesma rede, a taxa de transferência real variará durante a replicação. Se você estiver executando o comando GetThroughput de um servidor de configuração, a ferramenta não detectará as VMs protegidas e a replicação contínua. O resultado da taxa de transferência medida será diferente se a operação GetThroughput for executada quando as VMs protegidas tiverem alta variação de dados. É recomendável que você execute a ferramenta em vários pontos no tempo durante a criação de perfil para compreender quais níveis de taxa de transferência podem ser obtidos em vários momentos. No relatório, a ferramenta mostra a taxa de transferência medida por último.

### <a name="example"></a>Exemplo
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

>[!NOTE]
>
> Execute a ferramenta em um servidor que tenha o mesmo armazenamento e as mesmas características de CPU que o servidor de configuração.
>
> Para a replicação, defina a largura de banda recomendada para atender ao RPO 100% do tempo. Depois que você definir a largura de banda correta, se não houver um aumento na taxa de transferência obtida relatada pela ferramenta, faça o seguinte:
>
>  1. Verifique se há QoS (Qualidade de Serviço de Rede) que está limitando a taxa de transferência do Site Recovery.
>
>  2. Verifique se o cofre do Site Recovery está na região do Microsoft Azure com suporte mais próxima fisicamente para minimizar a latência de rede.
>
>  3. Verifique as características de armazenamento local para determinar se você pode melhorar o hardware (por exemplo, de HDD para SSD).
>
>  4. Altere as configurações do Site Recovery no servidor de processo para [aumentar a quantidade de largura de banda de rede usada para replicação](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

## <a name="recommendations-with-desired-rpo-as-input"></a>Recomendações com RPO desejado como entrada

### <a name="profiled-data"></a>Dados de criação de perfil

![O modo de exibição de dados de criação de perfil no planejador de implantação](./media/site-recovery-deployment-planner/profiled-data-period.png)

**Período de dados com criação de perfil**: o período durante o qual a criação de perfil foi executada. Por padrão, a ferramenta inclui todos os dados com criação de perfil no cálculo, a menos que gere o relatório para um período específico usando as opções StartDate e EndDate durante a geração do relatório.

**Nome do Servidor**: o nome ou o endereço IP do host VMware vCenter ou ESXi sobre cujas VMss é gerado o relatório.

**RPO Desejado**: o objetivo de ponto de recuperação para sua implantação. Por padrão, a largura de banda de rede necessária é calculada para valores de RPO de 15, 30 e 60 minutos. Com base na seleção, os valores afetados são atualizados na planilha. Se você tiver usado o parâmetro *DesiredRPOinMin* ao gerar o relatório, esse valor será mostrado na lista suspensa RPO Desejado.

### <a name="profiling-overview"></a>Visão geral da criação de perfil

![Resultados da criação de perfil no planejador de implantação](./media/site-recovery-deployment-planner/profiling-overview.png)

**Total de Máquinas Virtuais com Criação de Perfil**: o número total de VMs cujos dados de criação de perfil estão disponíveis. Se VMListFile tiver nomes de VMs sem criação de perfil, essas VMs não serão consideradas na geração de relatórios e serão excluídas da contagem total de VMs com criação de perfil.

**Máquinas Virtuais Compatíveis**: o número de VMs que podem ser protegidas no Azure usando o Site Recovery. É o número total de VMs compatíveis para as quais são calculados a largura de banda de rede necessária, o número de contas de armazenamento, o número de núcleos do Azure e o número de servidores de configuração e servidores de processo adicionais. Os detalhes de cada VM compatível estão disponíveis na seção "VMs compatíveis".

**Máquinas Virtuais Incompatível**: o número de VMs com criação de perfil que são incompatíveis para proteção com o Site Recovery. Os motivos da incompatibilidade são indicados na seção "VMs incompatíveis". Se VMListFile tiver nomes de VMs sem criação de perfil, essas VMs serão excluídas da contagem de VMs incompatíveis. Essas VMs são listadas como "Dados não encontrados" ao fim da seção "VMs incompatíveis".

**RPO Desejado**: seu objetivo de ponto de recuperação desejado, em minutos. O relatório é gerado para três valores de RPO: 15 (padrão), 30 e 60 minutos. A recomendação de largura de banda no relatório é alterada com base em sua seleção na lista suspensa RPO Desejado no canto superior direito da planilha. Se você tiver gerado o relatório usando o parâmetro *-DesiredRPO* com um valor personalizado, esse valor personalizado será exibido como o padrão na lista suspensa RPO Desejado.

### <a name="required-network-bandwidth-mbps"></a>Largura de banda necessária (Mbps)

![Largura de banda necessária no planejador de implantação](./media/site-recovery-deployment-planner/required-network-bandwidth.png)

**Para atender ao RPO 100% do tempo:** a largura de banda recomendada em Mbps a ser alocada para atender ao RPO desejado 100% do tempo. Essa quantidade de largura de banda deve ser dedicada para a replicação delta de estado estacionário de todas as VMs compatíveis para evitar violações de RPO.

**Para atender ao RPO 90% do tempo**: devido aos preços de banda larga ou por qualquer outro motivo, se não puder definir a largura de banda necessária para atender ao RPO desejado 100% do tempo, você poderá optar por usar uma configuração de largura de banda mais baixa que possa satisfazer ao RPO desejado 90% do tempo. Para entender as implicações da configuração dessa largura de banda inferior, o relatório fornece uma análise do tipo "e se" sobre o número e a duração de violações de RPO a serem esperadas.

**Taxa de transferência obtida** A taxa de transferência do servidor em que você executou o comando GetThroughput para a região do Microsoft Azure em que se encontra a conta de armazenamento. Esse número de taxa de transferência indica o nível estimado que você pode obter ao proteger as VMs compatíveis usando o Site Recovery, desde que as características de rede e de armazenamento do servidor de configuração ou do servidor de processo permaneçam iguais às do servidor no qual você executou a ferramenta.

Para a replicação, você deve definir a largura de banda recomendada para atender ao RPO 100% do tempo. Depois que você definir a largura de banda, se não houver um aumento na taxa de transferência obtida, conforme relatado pela ferramenta, faça o seguinte:

1. Verifique se há QoS (Qualidade de Serviço de Rede) que está limitando a taxa de transferência do Site Recovery.

2. Verifique se o cofre do Site Recovery está na região do Microsoft Azure com suporte mais próxima fisicamente para minimizar a latência de rede.

3. Verifique as características de armazenamento local para determinar se você pode melhorar o hardware (por exemplo, de HDD para SSD).

4. Altere as configurações do Site Recovery no servidor de processo para [aumentar o valor de largura de banda de rede usado para replicação](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Se você estiver executando a ferramenta em um servidor de configuração ou de processo que já tenha VMs protegidas, execute a ferramenta algumas vezes. O número da taxa de transferência obtida é alterado dependendo da quantidade de variação processada no momento.

Para todas as implantações do Site Recovery corporativo, é recomendável usar o [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Contas de armazenamento necessárias
O gráfico a seguir mostra o número total de contas de armazenamento (standard e premium) que são necessárias para proteger todas as VMs compatíveis. Para saber qual conta de armazenamento deve ser usada para cada VM, confira a seção "Posicionamento de VM-storage".

![Contas de armazenamento necessárias no planejador de implantação](./media/site-recovery-deployment-planner/required-azure-storage-accounts.png)

### <a name="required-number-of-azure-cores"></a>Número necessário de núcleos do Azure
Esse resultado é o número total de núcleos a serem configurados antes do failover ou do failover de teste de todas as VMs compatíveis. Se houver poucos núcleos disponíveis na assinatura, o Site Recovery não criará VMs no momento do failover de teste ou do failover.

![Número necessário de núcleos do Azure no planejador de implantação](./media/site-recovery-deployment-planner/required-number-of-azure-cores.png)

### <a name="required-on-premises-infrastructure"></a>Infraestrutura local necessária
Esse valor é o número total de servidores de configuração e servidores de processo adicionais a serem configurados que seriam suficientes para proteger todas as VMs compatíveis. Dependendo das [recomendações de tamanho para o servidor de configuração](https://aka.ms/asr-v2a-on-prem-components) com suporte, a ferramenta poderá recomendar servidores adicionais. A recomendação é baseada na variação por dia ou no número máximo de VMs protegidas, o que for maior (presumindo que uma média de três discos por VM), o que for atingido primeiro no servidor de configuração ou no servidor de processo adicional. Você encontrará os detalhes de variação total por dia e o número total de discos protegidos na seção "Entrada".

![Infraestrutura local necessária no planejador de implantação](./media/site-recovery-deployment-planner/required-on-premises-infrastructure.png)

### <a name="what-if-analysis"></a>Teste de hipóteses
Essa análise descreve quantas violações podem ocorrer durante o período de criação de perfil quando você define menos largura de banda para que o RPO desejado seja atingido somente 90% do tempo. Uma ou mais violações de RPO podem ocorrer em qualquer dia. O gráfico mostra o pico de RPO do dia.
Com base nesta análise, você pode decidir se o número de violações de RPO em todos os dias e o pico de ocorrências de RPO por dia são aceitáveis com a menor largura de banda especificada. Se for aceitável, você poderá alocar menos largura de banda para replicação. Caso contrário, aloque a maior largura de banda sugerida para atender ao RPO desejado 100% do tempo.

![Análise de hipóteses no planejador de implantação](./media/site-recovery-deployment-planner/what-if-analysis.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>Tamanho de lote de VMs recomendado para a replicação inicial
Nesta seção, é recomendável que o número de VMs que podem ser protegidas em paralelo conclua a replicação inicial dentro de 72 horas com a largura de banda sugerida para atender ao RPO desejado 100% do tempo definido. Esse valor é configurável. Para alterá-lo em tempo de geração de relatórios, use o parâmetro *GoalToCompleteIR*.

Aqui, o gráfico mostra um intervalo de valores de largura de banda e uma contagem de tamanho de lote de VM calculada para concluir a replicação inicial em 72 horas, com base na média detectada de tamanho de VM em todas as VMs compatíveis.

Na visualização pública, o relatório não especifica quais VMs devem ser incluídas em um lote. Você pode usar o tamanho de disco mostrado na seção "VMs compatíveis" para obter o tamanho de cada VM e selecioná-las para um lote ou pode selecionar as VMs com base em características de carga de trabalho conhecidas. O tempo de conclusão das alterações de replicação inicial proporcionalmente, com base no tamanho do disco de VM real, no espaço em disco usado e na taxa de transferência de rede disponível.

![Tamanho de lote de VM recomendado](./media/site-recovery-deployment-planner/recommended-vm-batch-size.png)

### <a name="growth-factor-and-percentile-values-used"></a>Fator de crescimento e valores de percentil usados
Esta seção na parte inferior da planilha mostra o valor de percentil usado para todos os contadores de desempenho das VMs com criação de perfil (o padrão é o 95º percentil) e o fator de crescimento em % usado em todos os cálculos (o padrão é 30%).

![Fator de crescimento e valores de percentil usados](./media/site-recovery-deployment-planner/max-iops-and-data-churn-setting.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Recomendações com largura de banda disponível como entrada

![Recomendações com largura de banda disponível como entrada](./media/site-recovery-deployment-planner/profiling-overview-bandwidth-input.png)

Pode haver uma situação em que você saiba que não é possível definir uma largura de banda de mais de x Mbps para replicação do Site Recovery. A ferramenta permite indicar a largura de banda (usando o parâmetro -Bandwidth durante a geração de relatórios) e obter o RPO possível em minutos. Com esse valor de RPO que pode ser obtido, você pode decidir se precisa configurar largura de banda adicional ou se pode ter uma solução de recuperação de desastres com este RPO.

![O RPO possível para a largura de banda de 500 Mbps](./media/site-recovery-deployment-planner/achievable-rpos.png)

## <a name="input"></a>Entrada
A planilha de Entrada fornece uma visão geral do ambiente VMware com criação de perfil.

![Visão geral do ambiente VMware com criação de perfil](./media/site-recovery-deployment-planner/Input.png)

**Data de Início** e **Data de Término**: as datas de início e de término dos dados de criação de perfil considerados para a geração de relatórios. Por padrão, a data de início é a data em que a criação de perfil começou, e a data de término é a data em que a criação de perfil é interrompida. Poderão ser os valores 'StartDate' e 'EndDate' se o relatório for gerado com esses parâmetros.

**Número total de dias de criação de perfil**: o número total de dias de criação de perfil entre as datas de início e de término para as quais o relatório é gerado.

**Número de máquinas virtuais compatíveis** é o número total de máquinas virtuais compatíveis para as quais a largura de banda de rede necessária, o número necessário de contas de Armazenamento do Azure, os núcleos do Microsoft Azure, os Servidores de Configuração e Servidores de Processo adicionais são calculados.

**Número total de discos em todas as máquinas virtuais compatíveis**: o número que é usado como uma das entradas para decidir o número de servidores de configuração e servidores de processo adicionais a serem usados na implantação.

**Número médio de discos por máquina virtual compatível**: o número médio de discos calculado em todas as VMs compatíveis.

**Tamanho médio de disco (GB)**: o tamanho médio de disco calculado em todas as VMs compatíveis.

**RPO desejado (minutos)**: o objetivo de ponto de recuperação padrão ou o valor passado para o parâmetro 'DesiredRPO' no momento da geração de relatórios para estimar a largura de banda necessária.

**Largura de banda desejada (Mbps)**: o valor passado para o parâmetro 'Bandwidth' no momento da geração de relatórios para estimar o RPO atingível.

**Variação de dados típica observada por dia (GB)**: a variação de dados média observada em todos os dias de criação de perfil. Esse número é usado como uma das entradas para decidir o número de servidores de configuração e servidores de processo adicionais a serem usados na implantação.


## <a name="vm-storage-placement"></a>Posicionamento de VM-Storage

![Posicionamento de VM-Storage](./media/site-recovery-deployment-planner/vm-storage-placement.png)

**Tipo de Armazenamento de Disco**: uma conta de armazenamento standard ou premium, que é usada para replicar todas as VMs correspondentes mencionadas na coluna **VMs para Posicionar**.

**Prefixo Sugerido**: o prefixo de três caracteres sugerido que pode ser usado para nomear a conta de armazenamento. Você pode usar seu próprio prefixo, mas sugestão da ferramenta segue a [convenção de nomenclatura de partição para contas de armazenamento](https://aka.ms/storage-performance-checklist).

**Nome de Conta Sugerido**: o nome de conta de armazenamento depois que você inclui o prefixo sugerido. Substitua o nome entre colchetes angulares (< e >) por sua entrada personalizada.

**Conta de Armazenamento de Log:**: todos os logs de replicação são armazenados em uma conta de armazenamento standard. Para VMs que são replicadas para uma conta de armazenamento premium, configure uma conta de armazenamento standard adicional para o armazenamento de log. Uma conta de armazenamento de log standard pode ser usada por várias contas de armazenamento de replicação premium. VMs replicadas para contas de armazenamento standard usam a mesma conta de armazenamento para logs.

**Nome de Conta de Log Sugerido**: o nome de conta de armazenamento de log depois que você inclui o prefixo sugerido. Substitua o nome entre colchetes angulares (< e >) por sua entrada personalizada.

**Resumo de Posicionamento**: um resumo da carga total das VMs na conta de armazenamento no momento da replicação e do failover ou failover de teste. Inclui o número total de VMs mapeadas para a conta de armazenamento, o total de IOPS de leitura/gravação em todas as VMs que está sendo colocadas nessa conta de armazenamento, o total de IOPS de gravação (replicação), o tamanho de configuração total em todos os discos e o número total de discos.

**Máquinas Virtuais para Posicionar**: uma lista de todas as VMs que devem ser posicionadas na conta de armazenamento específica para obter o melhor desempenho e uso.

## <a name="compatible-vms"></a>VMs compatíveis
![Planilha do Excel de VMs compatíveis](./media/site-recovery-deployment-planner/compatible-vms.png)

**Nome da VM**: o nome da VM ou o endereço IP que é usado em VMListFile quando um relatório é gerado. Essa coluna também lista os discos (VMDKs) que estão anexados às VMs. Para diferenciar VMs vCenter com nomes ou endereços IP duplicados, os nomes incluem o nome do host ESXi. O host ESXi listado é aquele em que a VM foi colocada quando a ferramenta realizou a descoberta durante o período de criação de perfil.

**Compatibilidade de VM**: os valores são **Sim** e **Sim**\*. **Sim**\* é para instâncias em que a VM é adequada para o [Armazenamento Premium do Azure](https://aka.ms/premium-storage-workload). Aqui, a alta variação da criação de perfil ou o disco IOPS se encaixam na categoria P20 ou P30, mas o tamanho do disco faz com que ele seja mapeado para P10 ou P20. A conta de armazenamento decide para qual tipo de disco de armazenamento premium um disco deve ser mapeado, com base em seu tamanho. Por exemplo:
* <128 GB é P10.
* 128 GB até 512 GB é P20.
* 512 GB a 1024 GB é P30.
* 1025 GB a 2048 GB é P40.
* 2049 GB a 4095 GB é P50.

Se as características de carga de trabalho de um disco o colocarem na categoria P20 ou P30, mas o tamanho o mapear para um tipo de disco de armazenamento premium inferior, a ferramenta marcará essa VM como **Sim**\*. A ferramenta também recomenda que você altere o tamanho do disco de origem para se ajustar ao tipo de disco de armazenamento premium recomendado ou altere o tipo de disco de destino após o failover.

**Tipo de armazenamento**: standard ou premium.

**Prefixo Sugerido**: o prefixo de conta de armazenamento com três caracteres.

**Conta de Armazenamento**: o nome que usa o prefixo de conta de armazenamento sugerido.

**IOPS de L/G (com Fator de Crescimento)**: IOPS de leitura/gravação de carga de trabalho de pico no disco (o padrão é o 95º percentil), incluindo o fator de crescimento futuro (o padrão é 30%). Observe que o IOPS total de leitura/gravação de uma VM nem sempre é a soma de IOPS de leitura/gravação dos discos individuais da VM, pois o IOPS de leitura/gravação de pico da VM é o pico da soma do IOPS de leitura/gravação dos discos individuais durante cada minuto do período de criação de perfil.

**Variação de Dados em Mbps (com Fator de Crescimento)**: a taxa de variação de pico no disco (o padrão é o 95º percentil), incluindo o fator de crescimento futuro (o padrão é 30%). Observe que a variação total dos dados da VM nem sempre é a soma da variação de dados dos discos individuais da VM, pois o pico da variação de dados da VM é o pico da soma da variação dos discos individuais durante cada minuto do período de criação de perfil.

**Tamanho de VM do Azure**: o tamanho ideal de máquina virtual mapeada dos Serviços de Nuvem do Azure para essa VM local. O mapeamento é baseado na memória da VM local, no número de discos/núcleos/NICs e IOPS de leitura/gravação. A recomendação é sempre o menor tamanho de VM do Azure que corresponde a todas as características de VM local.

**Número de Discos**: o número total de VMDKs (discos de máquina virtual) na VM.

**Tamanho de disco (GB)**: o tamanho total de instalação de todos os discos da VM. A ferramenta também mostra o tamanho dos discos individuais na VM.

**Núcleos**: o número de núcleos de CPUs na VM.

**Memória (MB)**: RAM na VM.

**NICs**: o número de NICs na VM.

**Tipo de inicialização**: é o tipo de inicialização da máquina virtual. Pode ser o BIOS ou EFI. Atualmente o Azure Site Recovery dá suporte apenas ao tipo de inicialização BIOS. Todas as máquinas virtuais do tipo de inicialização EFI estão listadas na planilha de VMs Incompatível.

**Tipo de sistema operacional**: o tipo de SO da VM. Ele pode ser Windows, Linux ou outros.

## <a name="incompatible-vms"></a>VMs incompatíveis

![Planilha do Excel de VMs incompatíveis](./media/site-recovery-deployment-planner/incompatible-vms.png)

**Nome da VM**: o nome da VM ou o endereço IP que é usado em VMListFile quando um relatório é gerado. Essa coluna também lista os VMDKs que estão anexados às VMs. Para diferenciar VMs vCenter com nomes ou endereços IP duplicados, os nomes incluem o nome do host ESXi. O host ESXi listado é aquele em que a VM foi colocada quando a ferramenta realizou a descoberta durante o período de criação de perfil.

**Compatibilidade de VM**: indica por que a VM específica é incompatível com o Site Recovery. Os motivos são descritos para cada disco incompatível da VM e, com base nos [limites de armazenamento](https://aka.ms/azure-storage-scalbility-performance) publicados, podem ser qualquer um dos seguintes:

* O tamanho do disco é > 4095 GB. Atualmente, o Armazenamento do Azure não dá suporte a tamanhos de disco de dados maiores que 4095 GB.
* O disco do sistema operacional é >2048 GB. Atualmente, o Armazenamento do Azure não dá suporte a tamanhos de disco do sistema operacional maiores que 2048 GB.
* O tipo de inicialização é EFI. O Azure Site Recovery atualmente dá suporte apenas a máquinas virtuais com tipo de inicialização BIOS.

* O tamanho total da VM (replicação + TFO) excede o limite de tamanho de conta de armazenamento com suporte (35 TB). Essa incompatibilidade normalmente ocorre quando um único disco na VM tem uma característica de desempenho que excede os limites máximo com suporte do Azure ou do Site Recovery para o armazenamento standard. Essa instância coloca a VM na zona de armazenamento premium. No entanto, o tamanho máximo com suporte de uma conta de armazenamento premium é de 35 TB, e uma única VM protegida não pode ser protegida em várias contas de armazenamento. Além disso, observe que quando um failover de teste é executado em uma VM protegida, ele é executado na mesma conta de armazenamento em que a replicação está em andamento. Nessa instância, configure duas vezes o tamanho do disco para que a replicação progrida e o failover de teste tenha êxito em paralelo.
* O IOPS de origem excede o limite de IOPS de armazenamento com suporte de 5000 por disco.
* O IOPS de origem excede o limite de IOPS de armazenamento com suporte de 80.000 por VM.
* A variação de dados média excede o limite de variação de dados do Site Recovery com suporte de 10 MBps para o tamanho médio de E/S do disco.
* A variação total de dados em todos os discos na VM excede o limite máximo com suporte de variação de dados do Site Recovery de 54 MBps por VM.
* O IOPS médio de gravação eficiente excede o limite de IOPS do Site Recovery de 840 com suporte para disco.
* O armazenamento de instantâneos calculado excede o limite com suporte de 10 TB para armazenamento de instantâneos.

**IOPS de L/G (com Fator de Crescimento)**: o pico de IOPS de carga de trabalho no disco (o padrão é o 95º percentil), incluindo o fator de crescimento futuro (o padrão é 30%). Observe que o IOPS total de leitura/gravação da VM nem sempre é a soma de IOPS de leitura/gravação dos discos individuais da VM, pois o IOPS de leitura/gravação de pico da VM é o pico da soma do IOPS de leitura/gravação dos discos individuais durante cada minuto do período de criação de perfil.

**Variação nos Dados em Mbps (com Fator de Crescimento)**: a taxa de variação de pico no disco (o padrão é o 95º percentil), incluindo o fator de crescimento futuro (o padrão é o 30%). Observe que a variação total dos dados da VM nem sempre é a soma da variação de dados dos discos individuais da VM, pois o pico da variação de dados da VM é o pico da soma da variação dos discos individuais durante cada minuto do período de criação de perfil.

**Número de Discos**: o número total de VMDKs na VM.

**Tamanho de disco (GB)**: o tamanho total de instalação de todos os discos da VM. A ferramenta também mostra o tamanho dos discos individuais na VM.

**Núcleos**: o número de núcleos de CPUs na VM.

**Memória (MB)**: a quantidade de RAM na VM.

**NICs**: o número de NICs na VM.

**Tipo de inicialização**: é o tipo de inicialização da máquina virtual. Pode ser o BIOS ou EFI. Atualmente o Azure Site Recovery dá suporte apenas ao tipo de inicialização BIOS. Todas as máquinas virtuais do tipo de inicialização EFI estão listadas na planilha de VMs Incompatível.

**Tipo de sistema operacional**: o tipo de SO da VM. Ele pode ser Windows, Linux ou outros.


## <a name="site-recovery-limits"></a>Limites da Recuperação de Site

**Destino de armazenamento de replicação** | **Tamanho de E/S de disco de origem médio** |**Variação nos dados média do disco de origem** | **Total de variação de dados de disco de origem por dia**
---|---|---|---
Armazenamento Standard | 8 KB | 2 Mbps | 168 GB por disco
Disco Premium P10 | 8 KB | 2 Mbps | 168 GB por disco
Disco Premium P10 | 16 KB | 4 Mbps | 336 GB por disco
Disco Premium P10 | 32 KB ou maior | 8 Mbps | 672 GB por disco
Disco Premium P20 ou P30 | 8 KB  | 5 Mbps | 421 GB por disco
Disco Premium P20 ou P30 | 16 KB ou maior |10 Mbps | 842 GB por disco

Esses são números médios, pressupondo uma sobreposição de E/S de 30%. O Site Recovery pode lidar com uma maior taxa de transferência com base na taxa de sobreposição, em tamanhos maiores de gravação e em comportamento de E/S de carga de trabalho real. Os números anteriores pressupõem uma lista de pendências típica de aproximadamente cinco minutos. Ou seja, depois que os dados são carregados, eles são processados, e um ponto de recuperação é criado dentro de cinco minutos.

Esses limites são baseados em nossos testes, mas eles não podem abranger todas as combinações possíveis de E/S de aplicativos. Os resultados reais podem variar dependendo da combinação de E/S do aplicativo. Para obter os melhores resultados, mesmo após planejar a implantação, é sempre recomendável executar amplos testes de aplicativos usando um failover de teste para obter a visão real do desempenho.

## <a name="updating-the-deployment-planner"></a>Atualizando o planejador de implantação
Para atualizar o planejador de implantação, faça o seguinte:

1. Baixe a versão mais recente do [planejador de implantação do Azure Site Recovery](https://aka.ms/asr-deployment-planner).

2. Copie a pasta .zip para um servidor no qual você deseje executá-la.

3. Extraia a pasta .zip.

4. Faça uma das opções a seguir:
 * Se a versão mais recente não contém uma correção de criação de perfil, e a criação de perfil já está em andamento na versão atual do planejador, continue a criação de perfil.
 * Se a versão mais recente contém uma correção de criação de perfil, é recomendável parar criação de perfil na versão atual e reiniciá-la com a nova versão.

  >[!NOTE]
  >
  >Ao iniciar a criação de perfil com a nova versão, passe o mesmo caminho de diretório de saída para que a ferramenta acrescente dados de perfil aos arquivos existentes. Um conjunto completo de dados com criação de perfil será ser usado para gerar o relatório. Se você passar um diretório de saída diferente, novos arquivos serão criados, e os dados de criação de perfil antigos não serão usados para gerar o relatório.
  >
  >Cada novo planejador de implantação é uma atualização cumulativa do arquivo .zip. Você não precisa copiar os arquivos mais recentes para a pasta anterior. Você pode criar e usar uma nova pasta.


## <a name="version-history"></a>Histórico de versão

### <a name="131"></a>1.3.1
Atualização: 19 de julho de 2017

O novo recurso a seguir foi adicionado:

* Suporte adicionado para discos grandes (> 1TB) na geração de relatório. Agora você pode usar o planejador de implantação para planejar a replicação para máquinas virtuais com tamanhos de disco maiores que 1 TB (até 4095 GB).
Leia mais sobre [Suporte a discos grandes no Azure Site Recovery](https://azure.microsoft.com/en-us/blog/azure-site-recovery-large-disks/)


### <a name="13"></a>1,3
Atualização: 9 de maio de 2017

O novo recurso a seguir foi adicionado:

* Foi adicionado suporte de disco gerenciado na geração de relatórios. O número de máquinas virtuais que pode ser colocado em uma única conta de armazenamento é calculado com base em se o disco gerenciado está selecionado para Failover/Teste de failover.        


### <a name="12"></a>1.2
Última atualização: 7 de abril de 2017

Adicionadas as seguintes correções:

* Verificação de tipo de inicialização (BIOS ou EFI) adicionada para cada máquina virtual a fim de determinar se a máquina virtual é compatível ou incompatível com a proteção.
* Informações de tipo de sistema operacional adicionadas para cada máquina virtual nas planilhas VMs compatíveis e VMs incompatíveis.
* Agora há suporte para a operação GetThroughput nas regiões do Microsoft Azure do governo dos EUA e da China.
* Algumas outras verificações de pré-requisitos foram adicionadas para vCenter e Servidor ESXi.
* Um relatório incorreto estava sendo gerado quando as configurações de localidade estavam definidas para outro idioma que não o inglês.


### <a name="11"></a>1,1
Atualização: 9 de março de 2017

Os seguintes problemas foram corrigidos:

* A ferramenta não poderá criar o perfil de VMs se o vCenter tiver duas ou mais VMs com o mesmo nome ou endereço IP em vários hosts ESXi.
* As ações de copiar e pesquisar são desabilitadas para as planilhas VMs Compatíveis e VMs Incompatíveis.

### <a name="10"></a>1.0
Atualização: 23 de fevereiro de 2017

A visualização pública do Planejador de Implantação 1.0 do Azure Site Recovery tem os seguintes problemas conhecidos, (a serem abordados em atualizações futuras):

* A ferramenta funciona apenas para os cenários do VMware para o Azure, não para implantações do Hyper-V para o Azure. Para o cenário de Hyper-v para o Azure, use a [ferramenta de planejador de capacidade do Hyper-V](./site-recovery-capacity-planning-for-hyper-v-replication.md).
* Não há suporte para a operação GetThroughput nas regiões do Microsoft Azure do governo dos EUA e da China.
* A ferramenta não poderá criar o de perfil VMs se o servidor vCenter tiver duas ou mais VMs com o mesmo nome ou endereço IP em vários hosts ESXi. Nesta versão, a ferramenta ignora a criação de perfil para nomes de VM ou endereços IP duplicados em VMListFile. A solução alternativa é criar um perfil das VMs usando um host ESXi em vez do servidor vCenter. Você deve executar uma instância de cada host ESXi.
