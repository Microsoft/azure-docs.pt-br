---
title: Guia de migração do Azure Stack Edge Pro FPGA para um dispositivo físico de GPU
description: Este guia contém instruções para migrar cargas de trabalho de um dispositivo Azure Stack Edge Pro FPGA para um dispositivo Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/11/2021
ms.author: alkohli
ms.openlocfilehash: 430e34a1ca631be00ef46170affd4b56c79894a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566397"
---
# <a name="migrate-workloads-from-an-azure-stack-edge-pro-fpga-to-an-azure-stack-edge-pro-gpu"></a>Migrar cargas de trabalho de um Azure Stack Edge Pro FPGA para um Azure Stack Edge Pro GPU

Este artigo descreve como migrar cargas de trabalho e dados de um dispositivo Azure Stack Edge Pro FPGA para um dispositivo Azure Stack Edge Pro GPU. O processo de migração começa com uma comparação dos dois dispositivos, um plano de migração e uma análise das considerações sobre a migração. O procedimento de migração fornece etapas detalhadas que termina com a verificação e a limpeza do dispositivo.

[!INCLUDE [Azure Stack Edge Pro FPGA end-of-life](../../includes/azure-stack-edge-fpga-eol.md)]

## <a name="about-migration"></a>Sobre a migração

A migração de dados é o processo de mover cargas de trabalho e dados do aplicativo de uma localização de armazenamento para outro. Isso implica fazer uma cópia exata dos dados atuais de uma organização de um dispositivo de armazenamento para outro (preferencialmente sem interromper ou desabilitar aplicativos ativos) e, depois, redirecionar toda a atividade de E/S (entrada/saída) para o novo dispositivo. 

Este guia de migração fornece um passo a passo das etapas necessárias para migrar dados de um dispositivo Azure Stack Edge Pro FPGA para um dispositivo Azure Stack Edge Pro GPU. Este documento destina-se a profissionais de TI (tecnologia da informação) e profissionais do conhecimento responsáveis por implantar, operar e gerenciar dispositivos do Azure Stack Edge no datacenter.

Neste artigo, o dispositivo Azure Stack Edge Pro FPGA é conhecido como o dispositivo de *origem* e o dispositivo Azure Stack Edge Pro GPU é o dispositivo de *destino*. 

## <a name="comparison-summary"></a>Resumo de comparação

Esta seção fornece um resumo comparativo das funcionalidades dos dispositivos Azure Stack Edge Pro GPU versus Azure Stack Edge Pro FPGA. O hardware nos dispositivos de origem e de destino é praticamente idêntico; pode diferir apenas em relação ao cartão de aceleração de hardware e à capacidade de armazenamento.<!--Please verify: These components MAY, but need not necessarily, differ?-->

|    Funcionalidade  | Azure Stack Edge Pro GPU (dispositivo de destino)  | Azure Stack Edge Pro FPGA (dispositivo de origem)|
|----------------|-----------------------|------------------------|
| Hardware       | Aceleração de hardware: 1 ou 2 GPUs NVIDIA T4 <br> A computação, a memória, o adaptador de rede, a unidade de alimentação e as especificações de cabo de alimentação são idênticas ao dispositivo com FPGA.  | Aceleração de hardware: FPGA Intel Arria 10 <br> A computação, a memória, o adaptador de rede, a unidade de alimentação e as especificações de cabo de alimentação são idênticas ao dispositivo com GPU.          |
| Armazenamento utilizável | 4,19 TB <br> Depois de reservar espaço para resiliência de paridade e uso interno | 12,5 TB <br> Depois de reservar espaço para uso interno |
| Segurança       | Certificados |                                                     |
| Cargas de trabalho      | Cargas de trabalho do IoT Edge <br> Cargas de trabalho de VM <br> Cargas de trabalho do Kubernetes| Cargas de trabalho do IoT Edge |
| Preços        | [Preços](https://azure.microsoft.com/pricing/details/azure-stack/edge/) | [Preços](https://azure.microsoft.com/pricing/details/azure-stack/edge/)|

## <a name="migration-plan"></a>Plano de migração

Para criar o seu plano de migração, considere as seguintes informações:

- Desenvolva uma agenda para a migração. 
- Ao migrar dados, talvez você encontre um tempo de inatividade. Recomendamos que você agende a migração durante uma janela de manutenção de tempo de inatividade, pois esse processo é disruptivo. Configure e restaure as configurações nesse tempo de inatividade, conforme descrito posteriormente neste documento.
- Entenda o tempo total de inatividade e comunique-o a todos os stakeholders.
- Identifique os dados locais que precisam ser migrados do dispositivo de origem. Como precaução, verifique se todos os dados no armazenamento existente têm um backup recente. 


## <a name="migration-considerations"></a>Considerações sobre migração 

Antes de continuar com a migração, considere as seguintes informações: 

- Um dispositivo Azure Stack Edge Pro GPU não pode ser ativado em um recurso Azure Stack Edge Pro FPGA. Crie um recurso para o dispositivo Azure Stack Edge Pro GPU, conforme descrito em [Criar um pedido do Azure Stack Edge Pro GPU](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource).
- Os modelos de machine learning implantados no dispositivo de origem que usavam o FPGA precisarão ser alterados para o dispositivo de destino com a GPU. Para obter ajuda com os modelos, você pode entrar em contato com o Suporte da Microsoft. Os modelos personalizados implantados no dispositivo de origem que não usaram o FPGA (usaram somente a CPU) devem funcionar no estado em que se encontram no dispositivo de destino (usando a CPU).
- Os módulos do IoT Edge implantados no dispositivo de origem podem exigir alterações antes que possam ser implantados com êxito no dispositivo de destino. 
- O dispositivo de origem dá suporte a protocolos NFS 3.0 e 4.1. O dispositivo de destino só dá suporte ao protocolo NFS 3.0.
- O dispositivo de origem dá suporte aos protocolos SMB e NFS. O dispositivo de destino dá suporte ao armazenamento por meio do protocolo REST usando contas de armazenamento, além dos protocolos SMB e NFS para compartilhamentos.
- O acesso de compartilhamento no dispositivo de origem ocorre por meio do endereço IP, enquanto o acesso de compartilhamento no dispositivo de destino ocorre por meio do nome do dispositivo.

## <a name="migration-steps-at-a-glance"></a>Visão geral das etapas de migração

Esta tabela resume o fluxo geral da migração, descrevendo as etapas necessárias para a migração e a localização na qual executar essas etapas.

| Nesta fase | Executar esta etapa| Neste dispositivo |
|---------------|-------------|----------------|
| Preparar o dispositivo de origem*       | 1. Registrar dados de configuração <br>2. Fazer backup de dados do compartilhamento <br>3. Preparar cargas de trabalho do IoT Edge| Dispositivo de origem  |
| Preparar dispositivo de destino*       |1. Criar um pedido <br>2. Configurar e ativar| Dispositivo de destino  |
| Migrar dados       | 1. Migrar dados de compartilhamentos <br>2. Reimplantar cargas de trabalho do IoT Edge| Dispositivo de destino  |
| Verificar os dados            |Verificar os dados migrados |Dispositivo de destino  |
| Limpar, retornar              |Apagar dados e retornar| Dispositivo de origem  |

**Os dispositivos de origem e de destino podem ser preparados em paralelo.*

## <a name="prepare-source-device"></a>Preparar o dispositivo de origem

A preparação inclui que você identifique os compartilhamentos de nuvem do Edge, os compartilhamentos locais do Edge e os módulos de IoT Edge implantados no dispositivo. 

### <a name="1-record-configuration-data"></a>1. Registrar dados de configuração

Siga estas etapas no seu dispositivo de origem por meio da IU local.

Registre os dados de configuração no dispositivo de *origem*. Use a [Lista de verificação da implantação](azure-stack-edge-gpu-deploy-checklist.md) para ajudar você a registrar a configuração do dispositivo. Durante a migração, você usará essas informações de configuração para configurar o novo dispositivo de destino. 

### <a name="2-back-up-share-data"></a>2. Fazer backup de dados do compartilhamento

Os dados do dispositivo podem ser de um dos seguintes tipos:

- Dados em compartilhamentos de nuvem do Edge
- Dados em compartilhamentos locais

#### <a name="data-in-edge-cloud-shares"></a>Dados em compartilhamentos de nuvem do Edge

A nuvem do Edge compartilha dados do nível do seu dispositivo com o Azure. Siga estas etapas no seu dispositivo de *origem* por meio do portal do Azure. 

- Faça uma lista de todos os compartilhamentos de nuvem do Edge e os usuários que você tem no dispositivo de origem.
- Faça uma lista de todos os agendamentos da largura de banda que você tem. Você recriará esses agendamentos da largura de banda no seu dispositivo de destino.
- Dependendo da largura de banda da rede disponível, configure agendamentos da largura de banda no seu dispositivo para maximizar os dados em camadas para a nuvem. Isso minimiza os dados locais no dispositivo.
- Verifique se os compartilhamentos estão totalmente em camadas na nuvem. As camadas podem ser confirmadas verificando o status do compartilhamento no portal do Azure.  

#### <a name="data-in-edge-local-shares"></a>Dados nos compartilhamentos locais do Edge

Os dados nos compartilhamentos locais do Edge permanecem no dispositivo. Siga estas etapas no seu dispositivo de *origem* por meio do portal do Azure. 

- Faça uma lista dos compartilhamentos locais do Edge no dispositivo.
- Como você fará uma só migração dos dados, crie uma cópia dos dados de compartilhamento local do Edge em outro servidor local. Você pode usar ferramentas de cópia como `robocopy` (SMB) ou `rsync` (NFS) para copiar os dados. Opcionalmente, você pode já ter implantado uma solução de proteção de dados de terceiros para fazer backup dos dados nos seus compartilhamentos locais. As seguintes soluções de terceiros têm suporte para uso com dispositivos Azure Stack Edge Pro FPGA:

    | Software de terceiros           | Referência à solução                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Para obter detalhes, entre em contato com Cohesity.          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Para obter detalhes, entre em contato com Commvault.          |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Para obter detalhes, entre em contato com Veritas.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Para obter detalhes, entre em contato com Veeam. |


### <a name="3-prepare-iot-edge-workloads"></a>3. Preparar cargas de trabalho do IoT Edge

- Se você tiver implantado módulos do IoT Edge e estiver usando a aceleração FPGA, talvez seja necessário modificar os módulos antes que eles sejam executados no dispositivo de GPU. Siga as instruções em [Modificar módulos do IoT Edge](azure-stack-edge-gpu-modify-fpga-modules-gpu.md). 

<!--- If you have deployed IoT Edge workloads, the configuration data is shared on a share on the device. Back up the data in these shares.-->


## <a name="prepare-target-device"></a>Preparar dispositivo de destino

### <a name="1-create-new-order"></a>1. Criar pedido

Você precisa criar um pedido (e um recurso) para o dispositivo de *destino*. O dispositivo de destino precisa ser ativado no recurso de GPU e não no recurso de FPGA.

Para fazer um pedido, selecione [Criar um recurso do Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource) no portal do Azure.


### <a name="2-set-up-activate"></a>2. Configurar, ativar

Você precisa configurar e ativar o dispositivo de *destino* para o recurso que você criou anteriormente. 

Siga estas etapas para configurar o dispositivo de *destino* por meio do portal do Azure:

1. Reúna as informações necessárias na [Lista de verificação de implantação](azure-stack-edge-gpu-deploy-checklist.md). Você pode usar as informações que salvou da configuração do dispositivo de origem. 
1. [Desembalar](azure-stack-edge-gpu-deploy-install.md#unpack-the-device), [montar em rack](azure-stack-edge-gpu-deploy-install.md#rack-the-device) e [cabear o dispositivo](azure-stack-edge-gpu-deploy-install.md#cable-the-device). 
1. [Conecte-se à IU local do dispositivo](azure-stack-edge-gpu-deploy-connect.md).
1. Configure a rede usando um conjunto diferente de endereços IP (se estiver usando IPs estáticos) do que aqueles que você usou para o dispositivo antigo. Confira como [definir configurações de rede](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
1. Atribua o mesmo nome do dispositivo que o dispositivo antigo e forneça um domínio DNS. Confira como [definir a configuração do dispositivo](azure-stack-edge-gpu-deploy-set-up-device-update-time.md).
1. Configurar certificados no novo dispositivo. Confira como [configurar certificados](azure-stack-edge-gpu-deploy-configure-certificates.md).
1. Obtenha a chave de ativação do portal do Azure e ative o novo dispositivo. Confira como [ativar o dispositivo](azure-stack-edge-gpu-deploy-activate.md).

Agora você está pronto para restaurar os dados de compartilhamento e implantar as cargas de trabalho que estavam sendo executados no dispositivo antigo.

## <a name="migrate-data"></a>Migrar dados

Agora você copiará dados do dispositivo de origem para os compartilhamentos de nuvem do Edge e os compartilhamentos locais do Edge no seu dispositivo de *destino*.

### <a name="1-from-edge-cloud-shares"></a>1. De compartilhamentos de nuvem do Edge

Siga estas etapas para sincronizar os dados nos compartilhamentos de nuvem do Edge no seu dispositivo de destino:

1. [Adicione compartilhamentos](azure-stack-edge-j-series-manage-shares.md#add-a-share) correspondentes aos nomes de compartilhamento criados no dispositivo de origem. Ao criar os compartilhamentos, verifique se **Selecionar contêiner de blobs** está definido como **Usar existente** e escolha o contêiner usado com o dispositivo anterior.
1. [Adicione usuários](azure-stack-edge-j-series-manage-users.md#add-a-user) que tinham acesso ao dispositivo anterior.
1. [Atualize os dados do compartilhamento](azure-stack-edge-j-series-manage-shares.md#refresh-shares) do Azure. A atualização do compartilhamento efetuará pull de todos os dados de nuvem do contêiner existente para os compartilhamentos.
1. Recrie os agendamentos da largura de banda a serem associados aos seus compartilhamentos. Confira [Adicionar um agendamento da largura de banda](azure-stack-edge-j-series-manage-bandwidth-schedules.md#add-a-schedule) para obter etapas detalhadas.


### <a name="2-from-edge-local-shares"></a>2. De compartilhamentos locais do Edge

Você pode ter implantado uma solução de backup de terceiros para proteger os dados de compartilhamentos locais para as suas cargas de trabalho de IoT. Agora, você precisará restaurar esses dados.

Depois que o dispositivo de substituição estiver totalmente configurado, habilite o dispositivo para o armazenamento local. 

Siga estas etapas para recuperar os dados de compartilhamentos locais:

1. [Configure a computação no dispositivo](azure-stack-edge-gpu-deploy-configure-compute.md).
1. Adicione todos os compartilhamentos locais no dispositivo de destino. Confira as etapas detalhadas em [Adicionar um compartilhamento local](azure-stack-edge-gpu-manage-shares.md#add-a-local-share).
1. O acesso aos compartilhamentos SMB no dispositivo de origem usará os endereços IP, enquanto no dispositivo de destino, você usará o nome do dispositivo. Confira [Conectar-se a um compartilhamento SMB no Azure Stack Edge Pro GPU](./azure-stack-edge-gpu-deploy-add-shares.md#connect-to-an-smb-share). Para se conectar a compartilhamentos NFS no dispositivo de destino, você precisará usar os novos endereços IP associados ao dispositivo. Confira [Conectar-se a um compartilhamento NFS no Azure Stack Edge Pro GPU](./azure-stack-edge-gpu-deploy-add-shares.md#connect-to-an-nfs-share). 

    Se você copiou os dados de compartilhamento para um servidor intermediário via SMB ou NFS, copie os dados do servidor intermediário para os compartilhamentos no dispositivo de destino. Se os dispositivos de origem e de destino estiverem *online*, você também poderá copiar os dados diretamente do dispositivo de origem.

    Se você usar um software de terceiros para fazer backup dos dados nos compartilhamentos locais, precisará executar o procedimento de recuperação fornecido pela solução de proteção de dados escolhida. Confira as referências na tabela a seguir.

    | Software de terceiros           | Referência à solução                               |
    |--------------------------------|---------------------------------------------------------|
    | Cohesity                       | [https://www.cohesity.com/solution/cloud/azure/](https://www.cohesity.com/solution/cloud/azure/) <br> Para obter detalhes, entre em contato com Cohesity.          |
    | Commvault                      | [https://www.commvault.com/azure](https://www.commvault.com/azure) <br> Para obter detalhes, entre em contato com Commvault. |
    | Veritas                        | [http://veritas.com/azure](http://veritas.com/azure) <br> Para obter detalhes, entre em contato com Veritas.   |
    | Veeam                          | [https://www.veeam.com/kb4041](https://www.veeam.com/kb4041) <br> Para obter detalhes, entre em contato com Veeam. |

### <a name="3-redeploy-iot-edge-workloads"></a>3. Reimplantar cargas de trabalho do IoT Edge

Depois que os módulos do IoT Edge estiverem preparados, você precisará implantar as cargas de trabalho do IoT Edge no seu dispositivo de destino. Se você encontrar erros na implantação dos módulos do IoT Edge, confira:

- [Problemas comuns e resoluções para o Azure IoT Edge](../iot-edge/troubleshoot-common-errors.md). 
- [Erros de runtime do IoT Edge](azure-stack-edge-gpu-troubleshoot.md#troubleshoot-iot-edge-errors).

## <a name="verify-data"></a>Verificar os dados

Após a migração, verifique se todos os dados foram migrados e se as cargas de trabalho foram implantadas no dispositivo de destino.

## <a name="erase-data-return"></a>Apagar dados, retornar

Após a conclusão da migração de dados, apague os dados locais e retorne o dispositivo de origem. Siga as etapas em [Retornar o seu dispositivo do Azure Stack Edge Pro](azure-stack-edge-return-device.md).


## <a name="next-steps"></a>Próximas etapas

[Saiba como implantar cargas de trabalho do IoT Edge no dispositivo Azure Stack Edge Pro GPU](azure-stack-edge-gpu-deploy-compute-module-simple.md)