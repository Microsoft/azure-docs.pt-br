---
title: Execute o Planejamento de Implantação do Azure Site Recovery para recuperação de desastre do Hyper-V no Azure | Microsoft Docs
description: Este artigo descreve como executar o Planejamento de Implantação do Azure Site Recovery para recuperação de desastres do Hyper-V no Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 6528b683ec9464c2b1982d631455718e6fe6f3b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60748945"
---
# <a name="run-the-azure-site-recovery-deployment-planner-for-hyper-v-disaster-recovery-to-azure"></a>Execute o planejador de implantação do Azure Site Recovery para recuperação de desastre do Hyper-V no Azure

Você pode executar a ferramenta de linha de comando do planejador de implementação do Site Recovery (ASRDeploymentPlanner.exe) em qualquer um desses quatro modos: 
-   Obter a lista de máquinas virtuais (VMs)
-   [Perfil](#profile-hyper-v-vms)
-   Gerar um relatório
-   [Obter taxa de transferência](#get-throughput)

Primeiro, execute a ferramenta para obter a lista de VMs de um único host de Hyper-V, ou de vários. Depois, execute a ferramenta no modo de criação de perfil para obter a variação de dados de VM e IOPS. Em seguida, execute a ferramenta para gerar o relatório e obter os requisitos de armazenamento e largura de banda de rede.

## <a name="get-the-vm-list-for-profiling-hyper-v-vms"></a>Obter a lista de VMs para criação de perfil de VMs do Hyper-V
Primeiro, você precisa de uma lista de VMs para a criação de perfil. Use o modo GetVMList da ferramenta de planejamento de implantação para gerar a lista de VMs presentes em vários hosts do Hyper-V em um único comando. Depois de gerar a lista completa, remova as VMs para as quais não deseja criar um perfil do arquivo de saída. Depois, use o arquivo de saída para todas as outras operações: criação de perfil, geração de relatórios e obtenção da taxa de transferência.

Você pode gerar a lista de VMs apontando a ferramenta para um cluster do Hyper-V ou um host do Hyper-V autônomo, ou uma combinação de ambos.

### <a name="command-line-parameters"></a>Parâmetros de linha de comando
A tabela a seguir contém uma lista de parâmetros obrigatórios e opcionais da ferramenta para execução no modo GetVMList. 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```

| Nome do parâmetro | DESCRIÇÃO |
|---|---|
| -Operation | GetVMList |
| -User | O nome de usuário para se conectar ao host do Hyper-V ou ao cluster do Hyper-V. O usuário precisa ter acesso administrativo.|
| -ServerListFile | O arquivo com a lista de servidores contendo as VMs para criação de perfil. O caminho do arquivo pode ser absoluto ou relativo. Esse arquivo deve conter uma das seguintes opões em cada linha:<ul><li>Nome do host ou endereço IP do Hyper-V</li><li>Nome do cluster ou endereço IP do Hyper-V</li></ul><br>**Exemplo:** ServerList.txt contém os seguintes servidores:<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(Opcional) A UNC (convenção de nomenclatura universal) ou o caminho do diretório local para armazenar dados gerados durante essa operação. Se um nome não for especificado, o diretório chamado ProfiledData no caminho atual será usado como o diretório padrão.|
|-OutputFile| (Opcional) O arquivo com a lista de VMs obtidas dos servidores Hyper-V é salvo. Se o nome não tiver sido mencionado, os detalhes são armazenados em "VMList.txt".  Use o arquivo para iniciar a criação de perfil depois de remover as VMs que não precisam de criação de perfil.|
|-Password|(Opcional) A senha para se conectar ao host do Hyper-V. Se você não especificá-la como um parâmetro, ela será solicitada quando você executar o comando.|

### <a name="getvmlist-discovery"></a>Descoberta de GetVMList

- **Cluster Hyper-V**: Quando o nome do cluster do Hyper-V é fornecido no arquivo de lista do servidor, a ferramenta localiza todos os nós do Hyper-V do cluster e obtém as VMs presentes em cada um dos hosts do Hyper-V.
**Host do Hyper-V**: Quando o nome do host do Hyper-V é fornecido, a ferramenta primeiro verifica se ela pertence a um cluster. Em caso afirmativo, a ferramenta busca os nós que pertencem ao cluster. Em seguida, ele obterá as VMs de cada host do Hyper-V. 

Também é possível listar em um arquivo os nomes amigáveis ou os endereços IP das VMs para as quais deseja criar o perfil manualmente.

Abra o arquivo de saída no Bloco de Notas e copie os nomes de todas as VMs para as quais deseja criar o perfil para outro arquivo (por exemplo, ProfileVMList.txt). Use um nome de VM por linha. Esse arquivo é usado como entrada para o parâmetro - VMListFile da ferramenta para todas as outras operações: criação de perfil, geração de relatórios e obtenção da taxa de transferência.

### <a name="examples"></a>Exemplos

#### <a name="store-the-list-of-vms-in-a-file"></a>Armazenar a lista de VMs em um arquivo
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

#### <a name="store-the-list-of-vms-at-the-default-location--directory-path"></a>Armazenar a lista de VMs no local padrão (-caminho do diretório)
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Perfil de VMs do Hyper-V
No modo de criação de perfil, a ferramenta de planejamento de implantação conecta-se a cada um dos hosts do Hyper-V para coletar dados de desempenho sobre as VMs. 

A criação de perfil não afeta o desempenho das VMs de produção, pois nenhuma conexão direta é feita a elas. Todos os dados de desempenho são coletados do host do Hyper-V.

A ferramenta consulta o host Hyper-V uma vez a cada 15 segundos para garantir a precisão de criação de perfil. Ela armazena a média dos dados de contador de desempenho de cada minuto.

A ferramenta lida perfeitamente com a migração da VM de um nó para outro nó no cluster, e com a migração de armazenamento em um host.

### <a name="getting-the-vm-list-to-profile"></a>Obter a lista de VMs para criação de perfil
Para criar uma lista de VMs para criação de perfil, confira a operação GetVMList.

Após obter a lista de VMs para criação de perfil, você pode executar a ferramenta no modo de criação de perfil. 

### <a name="command-line-parameters"></a>Parâmetros de linha de comando
A tabela a seguir lista os parâmetros obrigatórios e opcionais da ferramenta para execução no modo de criação de perfil. A ferramenta é comum para cenários de movimentação de VMware para o Azure e do Hyper-V para o Azure. Esses parâmetros são aplicáveis ao Hyper-V. 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```

| Nome do parâmetro | DESCRIÇÃO |
|---|---|
| -Operation | StartProfiling |
| -User | O nome de usuário para se conectar ao host do Hyper-V ou ao cluster do Hyper-V. O usuário precisa ter acesso administrativo.|
| -VMListFile | O arquivo com a lista de VMs para criação de perfil. O caminho do arquivo pode ser absoluto ou relativo. Para o Hyper-V, esse é o arquivo de saída da operação GetVMList. Se você estiver preparando manualmente, o arquivo deverá conter um nome de servidor ou endereço IP, seguido pelo nome da VM (separado por uma \ por linha). O nome da VM especificado no arquivo deve ser o mesmo que o nome da VM no host do Hyper-V.<br><br>**Exemplo:** VMList.txt contém as seguintes VMs:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|O número de minutos durante os quais a criação de perfil deve ser executada. O mínimo é de 30 minutos.|
|-NoOfHoursToProfile|O número de horas durante as quais a criação de perfil deve ser executada.|
|-NoOfDaysToProfile |O número de dias durante os quais a criação de perfil deve ser executada. É recomendável que você execute a criação de perfil por mais de 7 dias. Esse tempo de duração ajuda a garantir que o padrão de carga de trabalho no ambiente durante o período especificado seja observado e usado para fornecer uma recomendação precisa.|
|-Virtualization|O tipo de virtualização (Hyper-V ou VMware).|
|-Directory|(Opcional) O caminho de diretório local ou UNC para armazenar os dados de criação de perfil gerados durante a criação de perfil. Se um nome não for especificado, o diretório chamado ProfiledData no caminho atual será usado como o diretório padrão.|
|-Password|(Opcional) A senha para se conectar ao host do Hyper-V. Se você não especificá-la como um parâmetro, ela será solicitada quando você executar o comando.|
|-StorageAccountName|(Opcional) O nome da conta de armazenamento que é usado para localizar a taxa de transferência possível para replicação de dados do local do Azure. A ferramenta carrega dados de teste nessa conta de armazenamento para calcular a taxa de transferência. A conta de armazenamento deve ser do tipo v1 para fins gerais (GPv1).|
|-StorageAccountKey|(Opcional) A chave que é usada para acessar a conta de armazenamento. Acesse o portal do Azure > **Contas de armazenamento** > *nome da conta de armazenamento* >  **Configurações** > **Chaves de acesso** > **Chave1** (ou a chave de acesso primária da conta de armazenamento clássico).|
|-Ambiente|(Opcional) O seu ambiente de destino para a conta de armazenamento do Azure. Pode ser um dos três valores: AzureCloud, AzureUSGovernment ou AzureChinaCloud. O padrão é AzureCloud. Use o parâmetro quando sua região de destino for o governo dos EUA ou Azure China 21Vianet.|

É recomendável que você analise suas VMs por mais de sete dias. Se o padrão de variação oscilar muito em um mês, recomendamos a análise durante a semana quando você vir a variação máxima. A melhor maneira é analisar por 31 dias para obter a melhor recomendação. 

Durante o período de criação de perfil, ASRDeploymentPlanner.exe continua em execução. A ferramenta aceita a entrada de tempo de criação de perfil em dias. Para um teste rápido da ferramenta ou para uma prova de conceito, você pode analisar por algumas horas ou minutos. O tempo de criação de perfil mínimo permitido é de 30 minutos. 

Durante a criação de perfil, opcionalmente, você pode passar um nome de conta de armazenamento e uma chave para obter a taxa de transferência que o Azure Site Recovery pode alcançar no momento da replicação do servidor do Hyper-V para o Azure. Se a chave e o nome da conta de armazenamento não forem passados durante a criação de perfil, a ferramenta não calculará a taxa de transferência possível.

Você pode executar várias instâncias da ferramenta para diversos conjuntos de VMs. Verifique se os nomes de VM não são repetidos nos conjuntos de criação de perfil. Por exemplo, digamos que você tenha analisado 10 VMs (VM1 até meio de VM10). Depois de alguns dias, você deseja analisar outras 5 VMs (VM11 até VM15). Você pode executar a ferramenta a partir de outro console de linha de comando para o segundo conjunto de VMs (VM11 até VM15). 

Verifique se o segundo conjunto de VMs não possui nomes VM da primeira instância de criação de perfil ou use um diretório de saída diferente para a segunda execução. Se forem usadas duas instâncias da ferramenta para a criação de perfil das mesmas VMs e for usado o mesmo diretório de saída, o relatório gerado será incorreto. 

Por padrão, a ferramenta é configurada para analisar e gerar relatórios para até 1.000 VMs. Você pode alterar o limite ao mudar o valor da chave MaxVMsSupported no arquivo ASRDeploymentPlanner.exe.config.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```
Com as configurações padrão, para analisar (por exemplo) 1.500 VMs, crie dois arquivos VMList.txt. Um possui 1.000 VMs e o outro possui 500 VMs. Execute as duas instâncias do planejador de implantações do Azure Site Recovery: uma com VMList1.txt e outra com VMList2.txt. Você pode usar o mesmo caminho de diretório para armazenar os dados analisados das VMs VMList. 

Com base na configuração de hardware (especialmente o tamanho da RAM) do servidor no qual a ferramenta é executada para gerar o relatório, a operação poderá falhar com memória insuficiente. Se você tiver um bom hardware, poderá alterar o MaxVMsSupported para qualquer valor mais alto.  

As configurações de VM são capturadas uma vez no início da operação de criação de perfil e armazenadas em um arquivo chamado VMDetailList.xml. Essas informações são usadas quando o relatório é gerado. Não é capturada alteração alguma na configuração de VM (por exemplo, um número maior de núcleos, discos ou NICs) do início ao fim da criação de perfil. Se uma configuração de VM com criação de perfil tiver sido alterada durante a criação de perfil, aqui está a solução alternativa para obter os detalhes mais recentes da VM ao gerar o relatório:

* Faça backup de VMdetailList.xml e exclua o arquivo do local atual.
* Passe os argumentos -User e -Password no momento da geração de relatórios.

O comando de criação de perfil gera vários arquivos no diretório de criação de perfil. Não exclua nenhum dos arquivos, pois isso afeta a geração de relatórios.

### <a name="examples"></a>Exemplos

#### <a name="profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>Analisar VMs para 30 dias e obter a taxa de transferência do local para o Azure
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="profile-vms-for-15-days"></a>Analisar VMs por 15 dias
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\vCenter1_ProfiledData\ProfileVMList1.txt"  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>Analisar as VMs por 60 minutos para um teste rápido da ferramenta
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="profile-vms-for-2-hours-for-a-proof-of-concept"></a>Analisar as VMs por duas horas para uma prova de conceito
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

### <a name="considerations-for-profiling"></a>Considerações para criação de perfil

Se o servidor no qual a ferramenta está sendo executada for reinicializado ou falhar ou se você fechar a ferramenta usando Ctrl + C, os dados de criação de perfil serão preservados. Porém, há uma possibilidade de perder os últimos 15 minutos de dados de criação de perfil. Em casos assim, execute novamente a ferramenta no modo de criação de perfil depois que o servidor for reiniciado.

Quando o nome da conta de armazenamento e a chave são passados, a ferramenta mede a taxa de transferência na última etapa de criação de perfil. Se a ferramenta for fechada antes da conclusão da criação de perfil, a taxa de transferência não será calculada. Para obter a taxa de transferência antes de gerar o relatório, você pode executar a operação GetThroughput no console de linha de comando. Caso contrário, o relatório gerado não conterá as informações de taxa de transferência.

O Azure Site Recovery não dá suporte para VMs que têm discos de passagem e iSCSI. A ferramenta não pode detectar e analisar discos de passagem e iSCSI anexados às VMs.

## <a name="generate-a-report"></a>Gerar um relatório
A ferramenta gera um arquivo do Microsoft Excel com macros habilitadas (arquivo XLSM) como a saída de relatório. Ela resume todas as recomendações de implantação. O relatório é denominado DeploymentPlannerReport_*identificador numérico exclusivo*.xlsm e colocado no diretório especificado.

Após a conclusão da criação de perfil, você poderá executar a ferramenta no modo de geração de relatório. 

### <a name="command-line-parameters"></a>Parâmetros de linha de comando
A tabela a seguir contém uma lista de parâmetros obrigatórios e opcionais da ferramenta para execução no modo de geração de relatórios. A ferramenta é comum para movimentação de VMware para o Azure e do Hyper-V para o Azure. Os parâmetros a seguir são aplicáveis ao Hyper-V.
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```

| Nome do parâmetro | DESCRIÇÃO |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | O arquivo que contém a lista de VMs analisadas para as quais o relatório será gerado. O caminho do arquivo pode ser absoluto ou relativo. Para o Hyper-V, esse é o arquivo de saída da operação GetVMList. Se você estiver preparando manualmente, o arquivo deverá conter um nome de servidor ou endereço IP, seguido pelo nome da VM (separado por uma \ por linha). O nome da VM especificado no arquivo deve ser o mesmo que o nome da VM no host do Hyper-V.<br><br>**Exemplo:** VMList.txt contém as seguintes VMs:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|O tipo de virtualização (Hyper-V ou VMware).|
|-Directory|(Opcional) A UNC o ou caminho do diretório local em que os dados com perfil gerado (arquivos gerados durante a criação de perfil) são armazenados. Esses dados são necessários para gerar o relatório. Se um nome não for especificado, o diretório chamado ProfiledData no caminho atual será usado como o diretório padrão.|
| -User | (Opcional) O nome de usuário para se conectar ao host do Hyper-V ou ao cluster do Hyper-V. O usuário precisa ter acesso administrativo. O usuário e a senha são usados para buscar as informações mais recentes de configuração das VMs (como o número de discos, o número de núcleos e o número de NICs) a serem usados no relatório. Se este valor não for fornecido, as informações de configuração coletadas durante a criação de perfil serão usadas.|
|-Password|(Opcional) A senha para se conectar ao host do Hyper-V. Se você não especificá-la como um parâmetro, ela será solicitada quando você executar o comando.|
| -DesiredRPO | (Opcional) O objetivo de ponto de recuperação (RPO) desejado, em minutos. O padrão é de 15 minutos.|
| -Bandwidth | (Opcional) A largura de banda em megabits por segundo. Use este parâmetro para calcular o RPO que pode ser obtido para a largura de banda especificada. |
| -StartDate | (Opcional) A data e a hora de início no formato MM-DD-YYYY:HH:MM (formato de 24 horas). StartDate deve ser especificado junto com EndDate. Quando StartDate é especificado, o relatório é gerado para os dados de criação de perfil que são coletados entre StartDate e EndDate. |
| -EndDate | (Opcional) A data e a hora de término no formato MM-DD-YYYY:HH:MM (formato de 24 horas). EndDate deve ser especificado junto com StartDate. Quando EndDate é especificado, o relatório é gerado para os dados de criação de perfil que são coletados entre StartDate e EndDate. |
| -GrowthFactor | (Opcional) O fator de crescimento, expressado como uma porcentagem. O padrão é 30%. |
| -UseManagedDisks | (Opcional) UseManagedDisks: Sim/Não. O padrão é Sim. O número de máquinas virtuais que podem ser colocadas em uma única conta de armazenamento é calculado baseado na informação do Failover/Failover de teste de máquinas virtuais ser feito em um disco gerenciado em vez de em um disco não gerenciado. |
|-SubscriptionId |(Opcional) A Guid da assinatura. Use esse parâmetro para gerar um relatório de estimativa de custo com os preços mais recentes com base em sua assinatura, a oferta associada à sua assinatura e para a região do Azure de destino na moeda especificada.|
|-TargetRegion|(Opcional) A região do Azure que é o destino da replicação. Como o Azure tem custos diferentes por região, para gerar o relatório com a região do Azure de destino específica, use este parâmetro. O padrão é WestUS2 ou a última região de destino usada. Consulte a lista de [regiões de destino com suporte](hyper-v-deployment-planner-cost-estimation.md#supported-target-regions).|
|-OfferId|(Opcional) A oferta associada à assinatura. O padrão é MS-AZR-0003P (Pré-pago).|
|-Currency|(Opcional) A moeda na qual o custo é mostrado no relatório gerado. O padrão é dólar americano ($) ou a última moeda usada. Consulte a lista de [moedas com suporte](hyper-v-deployment-planner-cost-estimation.md#supported-currencies).|

Por padrão, a ferramenta é configurada para analisar e gerar relatórios para até 1.000 VMs. Você pode alterar o limite ao mudar o valor da chave MaxVMsSupported no arquivo ASRDeploymentPlanner.exe.config.
```
<!-- Maximum number of VMs supported-->
<add key="MaxVmsSupported" value="1000"/>
```

### <a name="examples"></a>Exemplos
#### <a name="generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>Gerar um relatório com os valores padrão quando os dados de criação de perfil estiverem na unidade local
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>Gerar um relatório quando os dados de criação de perfil estiverem em um servidor remoto
Você deve ter acesso de leitura/gravação no diretório remoto.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "\\PS1-W2K12R2\Hyper-V_ProfiledData" -VMListFile "\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt"
```

#### <a name="generate-a-report-with-a-specific-bandwidth-that-you-will-provision-for-the-replication"></a>Gerar um relatório com uma largura de banda específica que será provisionada para a replicação
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -Bandwidth 100
```

#### <a name="generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>Gerar um relatório com um fator de crescimento de 30% em vez do padrão de 30 por cento 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -GrowthFactor 5
```

#### <a name="generate-a-report-with-a-subset-of-profiled-data"></a>Gerar um relatório com um subconjunto dos dados de criação de perfil
Por exemplo, você tem 30 dias de dados de criação de perfil e deseja gerar um relatório de apenas 20 dias.
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt" -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="generate-a-report-for-a-5-minute-rpo"></a>Gerar um relatório para o RPO de cinco minutos
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -DesiredRPO 5
```

#### <a name="generate-a-report-for-the-south-india-azure-region-with-indian-rupee-and-a-specific-offer-id"></a>Gerar um relatório para a região Sul da Índia do Azure com Rúpia Indiana e ID da oferta específica
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```


### <a name="percentile-value-used-for-the-calculation"></a>Valor de percentil usado para o cálculo
Quando a ferramenta gera um relatório, o padrão é o valor percentual de 95 para IOPS de leitura/gravação, IOPS de gravação e rotatividade de dados. Esses valores são coletados durante a criação de perfil de todas as VMs. Essa métrica garante que o pico de percentil de 100 que suas VMs poderão ver devido a eventos temporários não seja usado para determinar seus requisitos de conta de armazenamento de destino e largura de banda de origem. Por exemplo, um evento temporário pode ser um trabalho de backup executado uma vez por dia, uma indexação de um banco de dados periódica ou uma atividade de geração de relatórios de análise ou outros eventos pontuais de curta duração.

Uso um valor de percentil de 95 proporciona a visão verdadeira das características de carga de trabalho reais e oferece o melhor desempenho quando as cargas de trabalho estão em execução no Azure. Não prevemos que você precise alterar esse número. Se alterar o valor (para um percentil de 90, por exemplo), você poderá atualizar o arquivo de configuração ASRDeploymentPlanner.exe.config na pasta padrão e salvá-lo para gerar um novo relatório nos dados de criação de perfil existentes.
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

### <a name="considerations-for-growth-factor"></a>Considerações sobre o fator de crescimento
É fundamental levar em conta o crescimento em suas características de carga de trabalho, supondo um aumento potencial no uso ao longo do tempo. Depois que proteção estiver em vigor, se as características de carga de trabalho forem alteradas, você não poderá alternar para outra conta de armazenamento para proteção sem desabilitar e reabilitar a proteção.

Por exemplo, digamos que hoje a VM esteja em uma conta de replicação de armazenamento standard. Nos próximos três meses, as seguintes alterações têm probabilidade de ocorrer:

1. O número de usuários do aplicativo que é executado na VM aumentará.
2. A maior variação na VM exigirá que a VM vá para o armazenamento premium para que a replicação do Azure Site Recovery possa acompanhar o ritmo.
3. Você precisará desabilitar e reabilitar a proteção para uma conta de armazenamento premium.

É altamente recomendável que você planeje o crescimento durante o planejamento da implantação. Embora o valor padrão seja de 30 por cento, só você saberá o padrão de uso padrão e as projeções de crescimento do seu aplicativo. Você pode alterar este número enquanto você estiver gerando um relatório. Além disso, você pode gerar vários relatórios com vários fatores de crescimento com os mesmos dados analisados. Você pode determinar qual o armazenamento de destino e as recomendações de largura de banda de origem funcionam melhor para você. 

O relatório gerado do Microsoft Excel contém as seguintes informações:

* [Resumo local](hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [Recomendações](hyper-v-deployment-planner-analyze-report.md#recommendations)
* [Posicionamento de armazenamento de VM](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
* [VMs compatíveis](hyper-v-deployment-planner-analyze-report.md#compatible-vms)
* [VMs incompatíveis](hyper-v-deployment-planner-analyze-report.md#incompatible-vms)
* [Requisito de armazenamento local](hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement)
* [Envio em lote de IR](hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [Estimativa de custo](hyper-v-deployment-planner-cost-estimation.md)

![Relatório do planejador de implantação](media/hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>Obter taxa de transferência
Para estimar a taxa de transferência que o Azure Site Recovery pode obter do local para o Azure durante a replicação, execute a ferramenta no modo GetThroughput. A ferramenta calcula a taxa de transferência do servidor em que a ferramenta está em execução. Idealmente, esse servidor é o servidor do Hyper-V cujas VMs serão protegidas. 

### <a name="command-line-parameters"></a>Parâmetros de linha de comando 
Abra um console de linha de comando e acesse a pasta da ferramenta de planejamento de implantação do Azure Site Recovery. Execute ASRDeploymentPlanner.exe com os parâmetros a seguir.
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```

 Nome do parâmetro | DESCRIÇÃO |
|---|---|
| -Operation | GetThroughput |
|-Virtualization|O tipo de virtualização (Hyper-V ou VMware).|
|-Directory|(Opcional) A UNC o ou caminho do diretório local em que os dados com perfil gerado (arquivos gerados durante a criação de perfil) são armazenados. Esses dados são necessários para gerar o relatório. Se um nome não for especificado, o diretório chamado ProfiledData no caminho atual será usado como o diretório padrão.|
| -StorageAccountName | O nome de conta de armazenamento usada para obter a largura de banda consumida para replicação de dados do local para o Azure. A ferramenta carrega dados de teste nessa conta de armazenamento para obter a largura de banda consumida. A conta de armazenamento deve ser do tipo v1 para fins gerais (GPv1).|
| -StorageAccountKey | A chave da conta de armazenamento usada para acessar a conta de armazenamento. Vá para o portal do Azure > **Contas de armazenamento** > *nome da conta de armazenamento* > **Configurações** > **Chaves de acesso** > **Chave1**.|
| -VMListFile | O arquivo que contém a lista de VMs para criação de perfil para calcular a largura de banda consumida. O caminho do arquivo pode ser absoluto ou relativo. Para o Hyper-V, esse é o arquivo de saída da operação GetVMList. Se você estiver preparando manualmente, o arquivo deverá conter um nome de servidor ou endereço IP, seguido pelo nome da VM (separado por uma \ por linha). O nome da VM especificado no arquivo deve ser o mesmo que o nome da VM no host do Hyper-V.<br><br>**Exemplo:** VMList.txt contém as seguintes VMs:<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Ambiente|(Opcional) O seu ambiente de destino para a conta de armazenamento do Azure. Pode ser um dos três valores: AzureCloud, AzureUSGovernment ou AzureChinaCloud. O padrão é AzureCloud. Use o parâmetro quando a região do Azure de destino for o governo dos EUA ou Azure China 21Vianet.|

### <a name="example"></a>Exemplo
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory "E:\Hyper-V_ProfiledData" -VMListFile "E:\Hyper-V_ProfiledData\ProfileVMList1.txt"  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

### <a name="throughput-considerations"></a>Considerações sobre a taxa de transferência

A ferramenta cria vários arquivos asrvhdfile*número*.vhd de 64 MB (em onde *número* é o número de arquivos) no diretório especificado. A ferramenta carrega os arquivos para a conta de armazenamento para obter a taxa de transferência. Depois que a taxa de transferência é medida, a ferramenta exclui todos os arquivos da conta de armazenamento e do servidor local. Se a ferramenta for encerrada por qualquer motivo enquanto estiver calculando a taxa de transferência, não excluirá os arquivos da conta de armazenamento ou do servidor local. Você deve excluí-los manualmente.

A taxa de transferência é medida em um momento específico no tempo. É a taxa de transferência máxima que o Azure Site Recovery pode alcançar durante a replicação, se todos os outros fatores permanecem os mesmos. Por exemplo, se qualquer aplicativo começar a consumir mais largura de banda na mesma rede, a taxa de transferência real variará durante a replicação. O resultado da taxa de transferência medida será diferente se a operação GetThroughput for executada quando as VMs protegidas tiverem alta variação de dados. 

É recomendável que você execute a ferramenta em vários pontos no tempo durante a criação de perfil para compreender quais níveis de taxa de transferência podem ser obtidos em vários momentos. No relatório, a ferramenta mostra a taxa de transferência medida por último.

> [!NOTE]
> Execute a ferramenta em um servidor que tenha o mesmo armazenamento e as mesmas características de CPU que um servidor do Hyper-V.

Para a replicação, defina a largura de banda recomendada para atender ao RPO 100% do tempo. Depois que você definir a largura de banda correta, se não houver um aumento na taxa de transferência obtida relatada pela ferramenta, faça o seguinte:

1. Verifique se há um problema na Qualidade de Serviço (QoS) de rede que está limitando a taxa de transferência do Azure Site Recovery.
2. Verifique se o cofre do Azure Site Recovery está na região do Microsoft Azure com suporte mais próxima fisicamente para minimizar a latência de rede.
3. Verifique as características de armazenamento local para determinar se você pode melhorar o hardware (por exemplo, de HDD para SSD).

    
## <a name="next-steps"></a>Próximas etapas
* [Analise o relatório gerado](hyper-v-deployment-planner-analyze-report.md)
