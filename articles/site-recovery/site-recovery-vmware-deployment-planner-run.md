---
title: Execute o Planejamento de Implantação de Recuperação do Site do Azure para recuperação de desastre do VMware no Azure | Microsoft Docs
description: Este artigo descreve como executar a recuperação de desastres do Azure Site Recovery Deployment Planner para VMware no Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: mayg
ms.openlocfilehash: 3a6c9e50804db573395984b8ba38838eb15b0792
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61276609"
---
# <a name="run-the-azure-site-recovery-deployment-planner-for-vmware-disaster-recovery-to-azure"></a>Execute o Azure Site Recovery Deployment Planner para recuperação de desastre do VMware no Azure
Este artigo é o guia do usuário do Planejador de Implantação do Azure Site Recovery para implantações de produção do VMware para o Azure.


## <a name="modes-of-running-deployment-planner"></a>Modos do Planejador de Implantações em execução
Você pode executar a ferramenta de linha de comando (ASRDeploymentPlanner.exe) em qualquer um dos três seguintes modos:

1.  [Criação de perfil](#profile-vmware-vms)
2.  [Geração de relatórios](#generate-report)
3.  [Obter taxa de transferência](#get-throughput)

Primeiro, execute a ferramenta no modo de criação de perfil para obter a variação de dados de VM e IOPS. Em seguida, execute a ferramenta para gerar o relatório e obter os requisitos de armazenamento e largura de banda de rede e o custo da recuperação de desastre.

## <a name="profile-vmware-vms"></a>Criar perfis de máquinas virtuais VMware
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

    ![Lista de nomes de VM no planejador de implantação
](media/site-recovery-vmware-deployment-planner-run/profile-vm-list-v2a.png)

### <a name="start-profiling"></a>Iniciar criação de perfil
Após obter a lista de VMs para criação de perfil, você pode executar a ferramenta no modo de criação de perfil. Aqui está a lista de parâmetros obrigatórios e opcionais da ferramenta para execução no modo de criação de perfil.

```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Nome do parâmetro | DESCRIÇÃO |
|---|---|
| -Operation | StartProfiling |
| -Server | O nome de domínio totalmente qualificado ou o endereço IP do servidor vCenter/host ESXi vSphere cujas VMs devem ser submetidas à criação de perfil.|
| -User | O nome de usuário a ser conectado ao servidor vCenter/host ESXi vSphere. O usuário precisa ter acesso somente leitura, no mínimo.|
| -VMListFile | O arquivo que contém a lista de VMs para criação de perfil. O caminho do arquivo pode ser absoluto ou relativo. O arquivo deve conter um nome de VM/endereço IP por linha. O nome de máquina virtual especificado no arquivo deve ser igual ao nome da VM no servidor vCenter/host ESXi vSphere.<br>Por exemplo, o arquivo VMList.txt contém as seguintes VMs:<ul><li>máquina_virtual_A</li><li>10.150.29.110</li><li>máquina_virtual_B</li><ul> |
|-NoOfMinutesToProfile|O número de minutos pelos quais a análise deve ser executada. O mínimo é 30 minutos.|
|-NoOfHoursToProfile|O número de horas pelas quais a análise deve ser executada.|
| -NoOfDaysToProfile | O número de dias pelos quais a criação de perfil deve ser executada. É recomendável executar a análise por mais de sete dias para garantir que o padrão de carga de trabalho no ambiente durante o período especificado seja observado e usado para fornecer uma recomendação precisa. |
|-Virtualization|Especifique o tipo de virtualização (Hyper-V ou VMware).|
| -Directory | (Opcional) A UNC (convenção de nomenclatura universal) ou o caminho do diretório local para armazenar dados de criação de perfil gerados durante a criação de perfil. Se um nome de diretório não for especificado, o diretório chamado 'ProfiledData' no caminho atual será usado como diretório padrão. |
| -Password | (Opcional) A senha a ser usada para se conectar ao host ESXi vSphere/servidor vCenter. Se não especificar uma agora, você será solicitado a fornecê-la quando o comando for executado.|
|-Port|(Opcional) Número de porta para se conectar ao host do vCenter/ESX. A porta padrão é 443.|
|-Protocol| (Opcional) Oprotocolo 'http' ou 'https' é especificado para cnoexão ao vCenter. O protocolo padrão é https.|
| -StorageAccountName | (Opcional) O nome da conta de armazenamento que é usado para localizar a taxa de transferência possível para replicação de dados do local do Azure. A ferramenta carrega dados de teste nessa conta de armazenamento para calcular a taxa de transferência. A conta de armazenamento deve ser do tipo v1 para fins gerais (GPv1). |
| -StorageAccountKey | (Opcional) A chave de conta de armazenamento que é usada para acessar a conta de armazenamento. Vá para o portal do Azure > Contas de armazenamento ><*Nome da conta de armazenamento*> > Configurações > Chaves de Acesso > Chave1. |
| -Ambiente | (opcional) Este é o seu ambiente de conta do Armazenamento do Azure de destino. Isso pode ser um dos três valores: AzureCloud, AzureUSGovernment ou AzureChinaCloud. O padrão é AzureCloud. Use o parâmetro quando a região do Azure de destino for o governo dos EUA ou Azure China 21Vianet. |


É recomendável que você analise suas VMs por mais de sete dias. Se o padrão de variação oscilar muito em um mês, recomendamos a análise durante a semana quando você vir a variação máxima. A melhor maneira é analisar por 31 dias para obter a melhor recomendação. Durante o período de criação de perfil, ASRDeploymentPlanner.exe continua em execução. A ferramenta aceita a entrada de tempo de criação de perfil em dias. Para um teste rápido da ferramenta ou para uma prova de conceito, você pode analisar por algumas horas ou minutos. O tempo de criação de perfil mínimo permitido é de 30 minutos.

Durante a criação de perfil, opcionalmente, você pode passar um nome de conta de armazenamento e uma chave para obter a taxa de transferência que o Azure Site Recovery pode alcançar no momento da replicação do servidor de configuração ou do servidor de processo para o Azure. Se a chave e o nome da conta de armazenamento não forem passados durante a criação de perfil, a ferramenta não calculará a taxa de transferência possível.

Você pode executar várias instâncias da ferramenta para diversos conjuntos de VMs. Verifique se os nomes de VM não são repetidos nos conjuntos de criação de perfil. Por exemplo, se tiver criado o perfil de dez VMs (VM1 a VM10) e, após alguns dias, quiser criar o perfil de outras cinco VMs (VM11 a VM15), você poderá executar a ferramenta em outro console de linha de comando para o segundo conjunto de VMs (VM11 a VM15). Verifique se o segundo conjunto de VMs não tem nomes de VM da primeira instância de criação de perfil ou use um diretório de saída diferente para a segunda execução. Se forem usadas duas instâncias da ferramenta para a criação de perfil das mesmas VMs e for usado o mesmo diretório de saída, o relatório gerado será incorreto.

Por padrão, a ferramenta é configurada para criar o perfil e gerar relatórios de até 1000 VMs. Você pode alterar o limite ao mudar o valor da chave MaxVMsSupported no arquivo *ASRDeploymentPlanner.exe.config*.
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Com as configurações padrão, para criar o perfil de, digamos, 1500 VMs, crie dois arquivos VMList.txt. Um com 1000 VMs e outro com uma lista de 500 VMs. Execute as duas instâncias do Azure Site Recovery Planejador de implantação, uma com VMList1.txt e outra com VMList2.txt. Você pode usar o mesmo caminho de diretório para armazenar os dados analisados das VMs VMList.

Já vimos que, com base na configuração de hardware, especialmente o tamanho da RAM do servidor no qual a ferramenta é executada para gerar o relatório, a operação poderá falhar com memória insuficiente. Se você tiver um bom hardware, poderá alterar o MaxVMsSupported para qualquer valor mais alto.  

Se você tiver vários servidores vCenter, precisará executar uma instância de ASRDeploymentPlanner para cada servidor vCenter para criação de perfil.

As configurações de VM são capturadas uma vez no início da operação de criação de perfil e armazenadas em um arquivo chamado VMDetailList.xml. Essas informações são usadas quando o relatório é gerado. Não é capturada alteração alguma na configuração de VM (por exemplo, um número maior de núcleos, discos ou NICs) do início ao fim da criação de perfil. Se uma configuração de VM com criação de perfil foi alterada durante a criação de perfil, na visualização pública, aqui está a solução alternativa para obter os detalhes mais recentes da VM ao gerar o relatório:

* Faça backup de VMdetailList.xml e exclua o arquivo do local atual.
* Passe os argumentos -User e -Password no momento da geração de relatórios.

O comando de criação de perfil gera vários arquivos no diretório de criação de perfil. Não exclua nenhum dos arquivos, pois isso afeta a geração de relatórios.

#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Exemplo 1: Analisar VMs para 30 dias e obter a taxa de transferência do local para o Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  30  -User vCenterUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>Exemplo 2: Analisar VMs por 15 dias

```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfDaysToProfile  15  -User vCenterUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Exemplo 3: Analisar as VMs por 60 minutos para um teste rápido da ferramenta
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60  -User vCenterUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>Exemplo 4: Analisar as VMs por duas horas para uma prova de conceito
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization VMware -Directory “E:\vCenter1_ProfiledData” -Server vCenter1.contoso.com -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -NoOfHoursToProfile 2 -User vCenterUser1
```

>[!NOTE]
>
>* Se o servidor em que a ferramenta está sendo executada for reinicializado ou falhar ou se você fechar a ferramenta usando Ctrl + C, os dados de criação de perfil serão preservados. Porém, há uma possibilidade de perder os últimos 15 minutos de dados de criação de perfil. Nesse caso, execute novamente a ferramenta no modo de criação de perfil depois que o servidor for reiniciado.
>* Quando o nome da conta de armazenamento e a chave são passados, a ferramenta mede a taxa de transferência na última etapa de criação de perfil. Se a ferramenta for fechada antes da conclusão da criação de perfil, a taxa de transferência não será calculada. Para obter a taxa de transferência antes de gerar o relatório, você pode executar a operação GetThroughput no console de linha de comando. Caso contrário, o relatório gerado não conterá as informações de taxa de transferência.


## <a name="generate-report"></a>Gerar relatório
A ferramenta gera um arquivo do Microsoft Excel com macros habilitadas (arquivo XLSM) como a saída de relatório, que resume todas as recomendações de implantação. O relatório é denominado `DeploymentPlannerReport_<unique numeric identifier>.xlsm` e colocado no diretório especificado.

>[!NOTE]
>Geração de relatórios exige um PC Windows ou o Windows Server com o Excel 2013 ou posterior. O símbolo decimal nesta máquina deve ser configurado como "." para produzir as estimativas de custo. Caso você tenha o programa de instalação "," como o símbolo decimal, vá para "Data alteração, hora ou formatos de número" no painel de controle e vá para "Configurações adicionais" para alterar o símbolo decimal para".".

Após a conclusão da criação de perfil, você poderá executar a ferramenta no modo de geração de relatório. A tabela a seguir contém uma lista de parâmetros obrigatórios e opcionais da ferramenta para execução no modo de geração de relatórios.

`ASRDeploymentPlanner.exe -Operation GenerateReport /?`

|Nome do parâmetro | DESCRIÇÃO |
|-|-|
| -Operation | GenerateReport |
| -Server |  O nome de domínio totalmente qualificado ou o endereço IP do servidor vCenter/vSphere (use o mesmo nome ou endereço IP que usou no momento da criação de perfil) em que se encontram as VMs com criação de perfil cujo relatório deve ser gerado. Observe que, se usou um servidor vCenter no momento da criação de perfil, você não pode usar um servidor vSphere para geração de relatórios e vice-versa.|
| -VMListFile | O arquivo que contém a lista de VMs com criação de perfil para as quais o relatório deve ser gerado. O caminho do arquivo pode ser absoluto ou relativo. O arquivo deve conter um nome de VM ou endereço IP por linha. Os nomes de VM que são especificados no arquivo devem ser iguais aos nomes de VM no servidor vCenter/host ESXi vSphere e corresponder ao que foi usado durante a criação de perfil.|
|-Virtualization|Especifique o tipo de virtualização (Hyper-V ou VMware).|
| -Directory | (Opcional) A UNC o ou caminho do diretório local em que os dados com perfil gerado (arquivos gerados durante a criação de perfil) são armazenados. Esses dados são necessários para gerar o relatório. Se não for especificado um nome, será usado o diretório 'ProfiledData'. |
| -GoalToCompleteIR | (Opcional) O número de horas em que a replicação inicial das VMs com criação de perfil precisa ser concluída. O relatório gerado fornece o número de VMs para as quais a replicação inicial pode ser concluída no tempo especificado. O padrão é de 72 horas. |
| -User | (Opcional) O nome de usuário a ser usado para se conectar ao servidor vCenter/vSphere. O nome é usado para buscar as informações de configuração mais recentes das VMs, como o número de discos, o número de núcleos e o número de NICs, para uso no relatório. Se o nome não for fornecido, serão usadas as informações de configuração coletadas no início da criação de perfil. |
| -Password | (Opcional) A senha a ser usada para se conectar ao host ESXi vSphere/servidor vCenter. Se a senha não for especificada como um parâmetro, você deverá fornecê-la mais tarde, quando o comando for executado. |
|-Port|(Opcional) Número de porta para se conectar ao host do vCenter/ESX. A porta padrão é 443.|
|-Protocol|(Opcional) Oprotocolo 'http' ou 'https' é especificado para cnoexão ao vCenter. O protocolo padrão é https.|
| -DesiredRPO | (Opcional) O objetivo de ponto de recuperação desejado, em minutos. O padrão é de 15 minutos.|
| -Bandwidth | Largura de banda em Mbps. O parâmetro a ser usado para calcular o RPO que pode ser obtido para a largura de banda especificada. |
| -StartDate | (Opcional) A data e a hora de início em MM-DD-YYYY:HH:MM (formato de 24 horas). *StartDate* deve ser especificado junto com *EndDate*. Quando StartDate é especificado, o relatório é gerado para os dados de criação de perfil que são coletados entre StartDate e EndDate. |
| -EndDate | (Opcional) A data e a hora de término em MM-DD-YYYY:HH:MM (formato de 24 horas). *EndDate* deve ser especificado junto com *StartDate*. Quando EndDate é especificado, o relatório é gerado para os dados de criação de perfil que são coletados entre StartDate e EndDate. |
| -GrowthFactor | (Opcional) O fator de crescimento, expressado como uma porcentagem. O padrão é 30%. |
| -UseManagedDisks | (Opcional) UseManagedDisks - Sim/Não. O padrão é Sim. O número de máquinas virtuais que podem ser colocadas em uma única conta de armazenamento é calculado considerando-se o Failover/Failover de teste de máquinas virtuais é feito em um disco gerenciado em vez de em um disco não gerenciado. |
|-SubscriptionId |(Opcional) A Guid da assinatura. Observe que esse parâmetro é necessário quando você precisa gerar o relatório de estimativa de custo com os preços mais recentes baseado na sua assinatura, a oferta associada à sua assinatura e para a região específica de destino do Azure na **moeda especificada**.|
|-TargetRegion|(Opcional) A região do Azure que é o destino da replicação. Como o Azure tem custos diferentes por região, para gerar o relatório com a região do Azure de destino específica, use este parâmetro.<br>O padrão é WestUS2 ou a última região de destino usada.<br>Consulte a lista de [regiões de destino com suporte](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Opcional) A oferta associada a determinada assinatura. O padrão é MS-AZR - 0003P (Pré-pago).|
|-Currency|(Opcional) A moeda na qual o custo é mostrado no relatório gerado. O padrão é dólar americano ($) ou a última moeda usada.<br>Consulte a lista de [moedas com suporte](site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).|

Por padrão, a ferramenta é configurada para criar o perfil e gerar relatórios de até 1000 VMs. Você pode alterar o limite ao mudar o valor da chave MaxVMsSupported no arquivo *ASRDeploymentPlanner.exe.config*.
```xml
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Exemplo 1: Gerar um relatório com os valores padrão quando os dados de criação de perfil estiverem na unidade local
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Exemplo 2: Gerar um relatório quando os dados de criação de perfil estiverem em um servidor remoto
Você deve ter acesso de leitura/gravação no diretório remoto.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “\\PS1-W2K12R2\vCenter1_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>Exemplo 3: Gerar um relatório com uma largura de banda e meta específicas para concluir o IR dentro do tempo especificado
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -Bandwidth 100 -GoalToCompleteIR 24
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Exemplo 4: Gerar um relatório com um fator de crescimento de 30% em vez do padrão de 30 por cento
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>Exemplo 5: Gerar um relatório com um subconjunto dos dados de criação de perfil
Por exemplo, você tem 30 dias de dados de criação de perfil e deseja gerar um relatório de apenas 20 dias.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minute-rpo"></a>Exemplo 6: Gerar um relatório para RPO de 5 minutos
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware -Server vCenter1.contoso.com -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>Exemplo 7: Gerar um relatório para a região do Azure do Sul da Índia com Rúpia Indiana e ID da oferta específica

Observe que a ID da assinatura é necessária para gerar o relatório de custo em uma moeda específica.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization VMware  -Directory “E:\vCenter1_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```

## <a name="percentile-value-used-for-the-calculation"></a>Valor de percentil usado para o cálculo
**Qual valor de percentil padrão das métricas de desempenho coletadas durante a criação de perfil a ferramenta usa ao gerar um relatório?**

A ferramenta usa como padrão os valores do 95º percentil de IOPS de leitura/gravação, IOPS de gravação e variação de dados coletados durante a criação de perfil de todas as VMs. Essa métrica garante que o pico do 100º percentil que suas VMs poderão ver devido a eventos temporários não seja usado para determinar seus requisitos de conta de armazenamento de destino e largura de banda de origem. Por exemplo, um evento temporário pode ser um trabalho de backup executado uma vez por dia, uma indexação de um banco de dados periódica ou uma atividade de geração de relatórios de análise ou outros eventos pontuais semelhantes de curta duração.

O uso de valores do 95º percentil proporciona a visão verdadeira das características de carga de trabalho reais e oferece o melhor desempenho quando as cargas de trabalho estão em execução no Azure. Não prevemos que você precise alterar esse número. Se alterar o valor (para o 90º percentil, por exemplo), você poderá atualizar o arquivo de configuração *ASRDeploymentPlanner.exe.config* na pasta padrão e salvá-lo para gerar um novo relatório nos dados de criação de perfil existentes.
```xml
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

* [Resumo local](site-recovery-vmware-deployment-planner-analyze-report.md#on-premises-summary)
* [Recomendações](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations)
* [VM<->Posicionamento de Armazenamento](site-recovery-vmware-deployment-planner-analyze-report.md#vm-storage-placement)
* [VMs compatíveis](site-recovery-vmware-deployment-planner-analyze-report.md#compatible-vms)
* [VMs incompatíveis](site-recovery-vmware-deployment-planner-analyze-report.md#incompatible-vms)
* [Estimativa de custo](site-recovery-vmware-deployment-planner-cost-estimation.md)

![Planejador de implantação](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

## <a name="get-throughput"></a>Obter taxa de transferência

Para estimar a taxa de transferência que o Site Recovery pode obter do local para o Azure durante a replicação, execute a ferramenta no modo GetThroughput. A ferramenta calcula a taxa de transferência do servidor em que a ferramenta está em execução. O ideal é que esse servidor se baseie no guia de dimensionamento do servidor de configuração. Se já tiver implantado componentes de infraestrutura locais do Site Recovery, execute a ferramenta no servidor de configuração.

Abra um console de linha de comando e acesse a pasta da ferramenta de planejamento de implantação do Site Recovery. Execute ASRDeploymentPlanner.exe com os parâmetros a seguir.

`ASRDeploymentPlanner.exe -Operation GetThroughput /?`

|Nome do parâmetro | DESCRIÇÃO |
|-|-|
| -Operation | GetThroughput |
|-Virtualization|Especifique o tipo de virtualização (Hyper-V ou VMware).|
| -Directory | (Opcional) A UNC o ou caminho do diretório local em que os dados com perfil gerado (arquivos gerados durante a criação de perfil) são armazenados. Esses dados são necessários para gerar o relatório. Se não for especificado um nome de diretório, será usado o diretório 'ProfiledData'. |
| -StorageAccountName | O nome de conta de armazenamento usada para obter a largura de banda consumida para replicação de dados do local para o Azure. A ferramenta carrega dados de teste nessa conta de armazenamento para obter a largura de banda consumida. A conta de armazenamento deve ser do tipo v1 para fins gerais (GPv1).|
| -StorageAccountKey | A chave da conta de armazenamento usada para acessar a conta de armazenamento. Acesse o portal do Azure > Contas de armazenamento > <*Nome de conta de armazenamento*> > Configurações > Chaves de Acesso > Chave1 (ou chave de acesso primário da conta de armazenamento clássico). |
| -VMListFile | O arquivo que contém a lista de VMs para criação de perfil para calcular a largura de banda consumida. O caminho do arquivo pode ser absoluto ou relativo. O arquivo deve conter um nome de VM/endereço IP por linha. Os nomes de VM especificados no arquivo devem ser iguais aos nomes de VM no servidor vCenter/host ESXi vSphere.<br>Por exemplo, o arquivo VMList.txt contém as seguintes VMs:<ul><li>VM_A</li><li>10.150.29.110</li><li>VM_B</li></ul>|
| -Ambiente | (opcional) Este é o seu ambiente de conta do Armazenamento do Azure de destino. Isso pode ser um dos três valores: AzureCloud, AzureUSGovernment ou AzureChinaCloud. O padrão é AzureCloud. Use o parâmetro quando a região do Azure de destino for o governo dos EUA ou Azure China 21Vianet. |

A ferramenta cria vários arquivos asrvhdfile<#>. vhd de 64 MB (em que "#" é o número de arquivos) no diretório especificado. A ferramenta carrega os arquivos para a conta de armazenamento para obter a taxa de transferência. Depois que a taxa de transferência é medida, a ferramenta exclui todos os arquivos da conta de armazenamento e do servidor local. Se a ferramenta for encerrada por qualquer motivo enquanto estiver calculando a taxa de transferência, não excluirá os arquivos do armazenamento ou do servidor local. Será necessário excluí-los manualmente.

A taxa de transferência é medida em um momento específico e é a taxa de transferência máxima que o Site Recovery pode atingir durante a replicação, desde que todos os outros fatores permaneçam iguais. Por exemplo, se qualquer aplicativo começar a consumir mais largura de banda na mesma rede, a taxa de transferência real variará durante a replicação. Se você estiver executando o comando GetThroughput de um servidor de configuração, a ferramenta não detectará as VMs protegidas e a replicação contínua. O resultado da taxa de transferência medida será diferente se a operação GetThroughput for executada quando as VMs protegidas tiverem alta variação de dados. É recomendável que você execute a ferramenta em vários pontos no tempo durante a criação de perfil para compreender quais níveis de taxa de transferência podem ser obtidos em vários momentos. No relatório, a ferramenta mostra a taxa de transferência medida por último.

### <a name="example"></a>Exemplo
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Directory  E:\vCenter1_ProfiledData -Virtualization VMware -VMListFile E:\vCenter1_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
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

## <a name="next-steps"></a>Próximas etapas
* [Analise o relatório gerado](site-recovery-vmware-deployment-planner-analyze-report.md).
