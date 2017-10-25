---
title: "Atualização 3 do Dispositivo de nuvem StorSimple | Microsoft Docs"
description: "Aprenda a criar, implantar e gerenciar um Dispositivo de nuvem StorSimple em uma rede virtual do Microsoft Azure. (Aplica-se à Atualização 3 do StorSimple e posteriores)."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/10/2017
ms.author: alkohli
ms.openlocfilehash: e7f58c8c1414f41d1d43e98b2faa327165f6eb75
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-manage-a-storsimple-cloud-appliance-in-azure-update-3-and-later"></a>Como implantar e gerenciar um Dispositivo de Nuvem StorSimple no Azure (Atualização 3 e posteriores)

## <a name="overview"></a>Visão geral

O Dispositivo de nuvem StorSimple Série 8000 é um recurso adicional que acompanha a sua solução Microsoft Azure StorSimple. O Dispositivo de nuvem StorSimple é executado em uma máquina virtual em uma rede virtual do Microsoft Azure e pode ser usado para fazer backup e clonar dados de seus hosts.

Este artigo descreve passo a passo o processo de implantação de um Dispositivo de Nuvem StorSimple no Azure. Depois de ler este artigo, você irá:

* Entenda como o dispositivo de nuvem difere do dispositivo físico.
* Aprenda a criar e a configurar o dispositivo de nuvem.
* Conecte-se ao dispositivo de nuvem.
* Aprenda a trabalhar com o dispositivo de nuvem.

Este tutorial se aplica a todos os Dispositivos de Nuvem StorSimple que executam a Atualização 3 e posteriores.

#### <a name="cloud-appliance-model-comparison"></a>Comparação de modelo do dispositivo de nuvem

O Dispositivo de nuvem StorSimple está disponível em dois modelos, o padrão 8010 (anteriormente conhecido como 1100) e o premium 8020 (apresentado na atualização 2). A tabela a seguir apresenta uma comparação entre os dois modelos.

| Modelo do dispositivo | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Capacidade máxima** |30 TB |64 TB |
| **VM do Azure** |Standard_A3 (4 núcleos, 7 GB de memória)| Standard_DS3 (4 núcleos, 14 GB de memória)|
| **Disponibilidade de região** |Todas as regiões do Azure |Regiões do Azure que dão suporte ao Armazenamento Premium e às VMs DS3 do Azure<br></br>Use [esta lista](https://azure.microsoft.com/regions/services/) para ver se as **Máquinas Virtuais > série DS** e o **Armazenamento > Armazenamento em disco** estão disponíveis em sua região. |
| **Tipo de armazenamento** |Usa o Armazenamento Standard do Azure para discos locais<br></br> Saiba como [criar uma conta de Armazenamento Standard](../storage/common/storage-create-storage-account.md) |Usa o Armazenamento Premium do Azure para discos locais<sup>2</sup> <br></br>Saiba como [criar uma conta de Armazenamento Premium](../storage/common/storage-premium-storage.md) |
| **Diretrizes sobre carga de trabalho** |Recuperação no nível de item de arquivos de backups |Cenários de desenvolvimento e teste de nuvem <br></br>Baixa latência e cargas de trabalho com alto desempenho<br></br>Dispositivo secundário para recuperação de desastre |

<sup>1</sup> *Conhecido anteriormente como 1100*.

<sup>2</sup> *O 8010 e o 8020 usam o Armazenamento Standard do Azure para a camada de nuvem. A diferença existe apenas na camada de local do dispositivo*.

## <a name="how-the-cloud-appliance-differs-from-the-physical-device"></a>Como o dispositivo de nuvem difere do dispositivo físico

O Dispositivo de nuvem StorSimple é uma versão somente em software do StorSimple, que é executado em um único nó em uma máquina virtual do Microsoft Azure. O dispositivo de nuvem oferece suporte a cenários de recuperação de desastre em que seu dispositivo físico não está disponível. O dispositivo de nuvem é adequado para uso em recuperação no nível do item de cenários de backups, recuperação de desastre local e desenvolvimento e teste na nuvem.

#### <a name="differences-from-the-physical-device"></a>Diferenças do dispositivo físico

A tabela abaixo mostra algumas das principais diferenças entre o Dispositivo de nuvem StorSimple e o dispositivo físico do StorSimple.

|  | Dispositivo físico | Dispositivo de nuvem |
| --- | --- | --- |
| **Localidade** |Reside no datacenter. |É executado no Azure. |
| **Interfaces de rede** |Tem seis interfaces de rede: DATA 0 a DATA 5. |Tem apenas uma interface de rede: DATA 0. |
| **Registro** |Registrado durante a etapa de configuração inicial. |O registro é uma tarefa separada. |
| **Chave de criptografia de dados do serviço** |Com a chave nova, gere no dispositivo físico e, em seguida, atualize o dispositivo de nuvem. |Não é possível gerar novamente do dispositivo de nuvem. |
| **Tipos de volume com suporte** |Dá suporte aos volumes fixados localmente e em camadas. |Oferece suporte apenas a volumes em camadas. |

## <a name="prerequisites-for-the-cloud-appliance"></a>Pré-requisitos para o dispositivo de nuvem

As seções a seguir explicam os pré-requisitos de configuração para o Dispositivo de nuvem StorSimple. Examine as considerações de segurança para usar um dispositivo de nuvem antes de implantá-lo.

[!INCLUDE [StorSimple Cloud Appliance security](../../includes/storsimple-8000-cloud-appliance-security.md)]

#### <a name="azure-requirements"></a>Requisitos do Azure

Antes de provisionar o dispositivo de nuvem, você precisa fazer as seguintes preparações no seu ambiente do Azure:

* Verifique se você tem um dispositivo físico StorSimple série 8000 (modelo 8100 ou 8600) implantado e em execução no seu datacenter. Registre este dispositivo no mesmo serviço de Gerenciador de Dispositivos do StorSimple para o qual você pretende criar um Dispositivo de Nuvem StorSimple.
* Para o dispositivo de nuvem, [configure uma rede virtual no Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md). Se usar o Armazenamento Premium, você deve criar uma rede virtual em uma região do Azure que dá suporte ao Armazenamento Premium. As regiões de armazenamento premium são regiões que correspondem à linha de armazenamento em disco [na lista dos Serviços do Azure por Região](https://azure.microsoft.com/regions/services/).
* É aconselhável usar o servidor DNS padrão fornecido pelo Azure em vez de especificar o nome do seu próprio servidor DNS. Se o nome do servidor DNS não for válido ou se o servidor DNS não conseguir resolver endereços IP corretamente, a criação do dispositivo de nuvem falhará.
* Ponto a site e site a site são opcionais, mas não obrigatórios. Se desejar, você pode configurar essas opções para cenários mais avançados.
* É possível criar [Máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-quick-create-portal.md) (servidores de host) na rede virtual que podem usar os volumes expostos pelo dispositivo de nuvem. Esses servidores devem atender aos seguintes requisitos:

  * Ser VMs do Windows ou do Linux com software Iniciador iSCSI instalado.
  * Estar em execução na mesma rede virtual que o dispositivo de nuvem.
  * Ser capaz de se conectar ao destino iSCSI do dispositivo de nuvem por meio do endereço IP interno do dispositivo de nuvem.
  * Verifique se você configurou o suporte para tráfego iSCSI e de nuvem na mesma rede virtual.

#### <a name="storsimple-requirements"></a>Requisitos do StorSimple

Faça as seguintes atualizações para o serviço do gerenciador de dispositivos StorSimple antes de criar um dispositivo de nuvem:

* Adicione [registros de controle de acesso](storsimple-8000-manage-acrs.md) às Vms que serão os servidores de host para seu dispositivo de nuvem.
* Use uma [conta de armazenamento](storsimple-8000-manage-storage-accounts.md#add-a-storage-account) na mesma região do dispositivo de nuvem. Contas de armazenamento em regiões diferentes podem resultar em baixo desempenho. Você pode usar uma conta de armazenamento Premium ou Standard com o dispositivo de nuvem. Mais informações sobre como criar uma conta do [Armazenamento Standard](../storage/common/storage-create-storage-account.md) ou uma conta do [Armazenamento Premium](../storage/common/storage-premium-storage.md)
* Ao criar um dispositivo de nuvem, use uma conta de armazenamento diferente da conta usada para os seus dados. O uso da mesma conta de armazenamento pode resultar em baixo desempenho.

Certifique-se de ter as seguintes informações antes de começar:

* Sua conta do portal do Azure com credenciais de acesso.
* Uma cópia da chave de criptografia de dados de serviço do seu dispositivo físico é registrada para o serviço do gerenciador de dispositivos StorSimple.

## <a name="create-and-configure-the-cloud-appliance"></a>Como criar e configurar o dispositivo de nuvem

Antes de executar esses procedimentos, certifique-se de que você atenda aos [pré-requisitos para o dispositivo de nuvem](#prerequisites-for-the-cloud-appliance).

Execute as seguintes etapas para criar um Dispositivo de nuvem StorSimple.

### <a name="step-1-create-a-cloud-appliance"></a>Etapa 1: criação do dispositivo de nuvem

Execute as seguintes etapas para criar o Dispositivo de nuvem StorSimple.

[!INCLUDE [Create a cloud appliance](../../includes/storsimple-8000-create-cloud-appliance-u2.md)]

Se a criação do dispositivo de nuvem falhar nesta etapa, talvez você não tenha conectividade com a Internet. Para obter mais informações, acesse [como solucionar problemas de falhas de conectividade com a Internet](#troubleshoot-internet-connectivity-errors) ao criar um dispositivo de nuvem.

### <a name="step-2-configure-and-register-the-cloud-appliance"></a>Etapa 2: configuração e registro do dispositivo de nuvem

Antes de iniciar este procedimento, verifique se você tem uma cópia da chave de criptografia de dados de serviço. A chave de criptografia de dados de serviço foi criada quando você registrou o seu primeiro dispositivo físico StorSimple com o serviço do gerenciador de dispositivos StorSimple. Você foi orientado a salvá-la em um local seguro. Se você não tiver uma cópia da chave de criptografia dos dados de serviço, deverá contatar o Suporte da Microsoft para obter assistência.

Execute as seguintes etapas para configurar e registrar o Dispositivo de nuvem StorSimple.

[!INCLUDE [Configure and register a cloud appliance](../../includes/storsimple-8000-configure-register-cloud-appliance.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Etapa 3: (opcional) Modificar as definições de configuração do dispositivo

A seção a seguir descreve as definições de configuração de dispositivo necessárias para o Dispositivo de nuvem StorSimple se você desejar usar o CHAP, o StorSimple Snapshot Manager ou alterar a senha do administrador do dispositivo.

#### <a name="configure-the-chap-initiator"></a>Configurar o iniciador CHAP

Este parâmetro contém as credenciais que o dispositivo de nuvem (destino) espera dos iniciadores (servidores) que estão tentando acessar os volumes. Os iniciadores fornecerão um nome de usuário CHAP e uma senha CHAP para identificá-los para o dispositivo durante essa autenticação. Para obter etapas detalhadas, vá para [Configurar o CHAP para seu dispositivo](storsimple-8000-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Configurar o destino CHAP

Esse parâmetro contém as credenciais usadas pelo dispositivo de nuvem quando um iniciador compatível com CHAP solicita autenticação mútua ou bidirecional. Seu dispositivo de nuvem usará um nome de usuário CHAP reverso e uma senha CHAP reversa para identificar a si mesmo para o iniciador durante esse processo de autenticação.

> [!NOTE]
> Observe que as configurações de destino de CHAP são configurações globais. Quando essas configurações são aplicadas, todos os volumes conectados ao dispositivo de nuvem usam a autenticação CHAP.

Para obter etapas detalhadas, vá para [Configurar o CHAP para seu dispositivo](storsimple-8000-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Configurar a senha do Gerenciador de Instantâneos StorSimple

O software Gerenciador de Instantâneos StorSimple software reside no host Windows e permite que os administradores gerenciem backups do seu dispositivo StorSimple na forma de instantâneos locais e de nuvem.

> [!NOTE]
> Para o dispositivo de nuvem, o seu host do Windows é uma máquina virtual do Azure.

Ao configurar um dispositivo no StorSimple Snapshot Manager, você deverá fornecer o endereço IP do dispositivo StorSimple e a senha para autenticar o dispositivo de armazenamento. Para obter etapas detalhadas, vá para [Configurar senha do StorSimple Snapshot Manager](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Alterar a senha de administrador do dispositivo

Quando você usar a interface do Windows PowerShell para acessar o dispositivo de nuvem, será solicitada a inserção de uma senha do administrador do dispositivo. Para proteger os seus dados, você deve alterar essa senha antes de usar o dispositivo de nuvem. Para obter etapas detalhadas, vá para [Configurar senha de administrador do dispositivo](../storsimple/storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-cloud-appliance"></a>Conectar-se remotamente com o dispositivo de nuvem

O acesso remoto ao seu dispositivo de nuvem por meio da interface do Windows PowerShell não está habilitado por padrão. Primeiro, você deve habilitar o gerenciamento remoto no dispositivo de nuvem e, em seguida, no cliente usado para acessar o dispositivo de nuvem.

O procedimento de duas etapas a seguir descreve como se conectar remotamente ao seu dispositivo de nuvem.

### <a name="step-1-configure-remote-management"></a>Etapa 1: configurar o gerenciamento remoto

Execute as etapas a seguir para configurar o gerenciamento remoto para o seu Dispositivo de nuvem StorSimple.

[!INCLUDE [Configure remote management via HTTP for cloud appliance](../../includes/storsimple-8000-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-cloud-appliance"></a>Etapa 2: acesso remoto ao dispositivo de nuvem

Depois de habilitar o gerenciamento remoto do dispositivo de nuvem, use o Windows PowerShell remotamente para se conectar ao dispositivo de outra máquina virtual dentro da mesma rede virtual. Por exemplo, você pode se conectar da VM host configurada e usada para conectar ao iSCSI. Na maioria das implantações, você abrirá um ponto de extremidade público para acessar sua VM host, que pode usada para acessar o dispositivo de nuvem.

> [!WARNING]
> **Para maior segurança, é altamente recomendável que você use HTTPS durante a conexão com os pontos de extremidade e, em seguida, exclua os pontos de extremidade depois de concluir sua sessão remota do PowerShell.**

Você deve seguir os procedimentos em [Conectar-se remotamente ao seu dispositivo do StorSimple](storsimple-8000-remote-connect.md) para configurar o seu dispositivo de nuvem remotamente.

## <a name="connect-directly-to-the-cloud-appliance"></a>Conecte-se diretamente com o dispositivo de nuvem

Você também pode se conectar diretamente com o dispositivo de nuvem. Você deve criar pontos de extremidade adicionais para conectar-se diretamente com o dispositivo de nuvem de outro computador fora da rede virtual ou fora do ambiente do Microsoft Azure.

Execute as seguintes etapas para criar um ponto de extremidade público no dispositivo de nuvem.

[!INCLUDE [Create public endpoints on a cloud appliance](../../includes/storsimple-8000-create-public-endpoints-cloud-appliance.md)]

É recomendável que você se conecte de outra máquina virtual dentro da mesma rede virtual porque essa prática minimiza o número de pontos de extremidade públicos em sua rede virtual. Neste caso, conecte-se à máquina virtual por meio de uma sessão de área de trabalho remota e, em seguida, configure essa máquina virtual para ser usada da mesma forma você faria com outro cliente do Windows em uma rede local. Você não precisa acrescentar o número da porta pública porque a porta já é conhecida.

## <a name="work-with-the-storsimple-cloud-appliance"></a>Como trabalhar com o Dispositivo de nuvem StorSimple

Depois de criar e configurar o Dispositivo de nuvem StorSimple, você já pode começar a trabalhar com ele. Você pode trabalhar com contêineres de volume, volumes e políticas de backup em um dispositivo de nuvem como faria em um dispositivo StorSimple físico. A única diferença é que você deve selecionar o dispositivo de nuvem na sua lista de dispositivos. Para aprender o passo a passo de várias tarefas de gerenciamento para o dispositivo de nuvem, confira o [Como usar o serviço do gerenciador de dispositivos StorSimple para gerenciar um dispositivo de nuvem](storsimple-8000-manager-service-administration.md).

As seções a seguir discutem as diferenças que você encontrará ao trabalhar com o dispositivo de nuvem.

### <a name="maintain-a-storsimple-cloud-appliance"></a>Manutenção de um Dispositivo de nuvem StorSimple

Como é um dispositivo somente de software, a manutenção do dispositivo de nuvem é mínima quando comparada à manutenção do dispositivo físico.

Você não pode atualizar um dispositivo de nuvem. Use a versão mais recente do software para criar um dispositivo de nuvem novo.


### <a name="storage-accounts-for-a-cloud-appliance"></a>Contas de armazenamento para um dispositivo de nuvem

Crie as contas de armazenamento para que possam ser usadas pelo serviço do gerenciador de dispositivos StorSimple, pelo dispositivo de nuvem e pelo dispositivo físico. Recomendamos que você use um identificador de região no nome amigável ao criar as suas contas de armazenamento. Isso ajuda a garantir que a região seja consistente em todos os componentes do sistema. É importante que todos os componentes estejam na mesma região para evitar problemas de desempenho em um dispositivo de nuvem.

Para obter um procedimento passo a passo, vá para [adicionar uma conta de armazenamento](storsimple-8000-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-cloud-appliance"></a>Como desativar um Dispositivo de nuvem StorSimple

Quando você desativa um dispositivo de nuvem, a ação exclui a VM e os recursos criados quando ela foi provisionada. Depois de desativado, o dispositivo de nuvem não poderá ser restaurado ao estado anterior. Antes de desativar o dispositivo de nuvem, pare ou exclua clientes e hosts que dependem dele.

A desativação de dispositivo de nuvem gera as seguintes consequências:

* O dispositivo de nuvem é removido.
* O disco do sistema operacional e os discos de dados criados para o dispositivo de nuvem são removidos.
* O serviço hospedado e a rede virtual criados durante o provisionamento são mantidos. Se você não os estiver usando, deverá excluí-los manualmente.
* Instantâneos de nuvem criados para o dispositivo de nuvem são mantidos.

Para obter um procedimento passo a passo, vá para [Desativar e excluir seu dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).

Assim que o dispositivo de nuvem for mostrado como desativado na folha do serviço do gerenciador de dispositivos StorSimple, você poderá excluir o dispositivo de nuvem da lista da folha **dispositivos**.

### <a name="start-stop-and-restart-a-cloud-appliance"></a>Como iniciar, parar e reiniciar um dispositivo de nuvem
Diferente do dispositivo físico do StorSimple, o Dispositivo de nuvem StorSimple não tem botões liga/desliga. No entanto, pode haver ocasiões em que você precisará parar e reiniciar o dispositivo de nuvem.

A maneira mais fácil de iniciar, parar e reiniciar um dispositivo de nuvem é por meio da folha de serviço Máquinas Virtuais. Acesse o serviço de máquina virtual. Na lista de VMs, identifique a VM correspondente ao seu dispositivo de nuvem (mesmo nome) e, então, clique no nome da VM. Ao examinar a folha de máquina virtual, você verá que o status de dispositivo de nuvem é **Executando** porque ele é iniciado por padrão após a sua criação. É possível iniciar, parar e reiniciar uma máquina virtual a qualquer momento.

[!INCLUDE [Stop and restart cloud appliance](../../includes/storsimple-8000-stop-restart-cloud-appliance.md)]

### <a name="reset-to-factory-defaults"></a>Redefinir para os padrões de fábrica
Se você quiser recomeçar com o dispositivo de nuvem, desative-o, exclua-o e, então, crie um novo.

## <a name="fail-over-to-the-cloud-appliance"></a>Como fazer failover para o dispositivo de nuvem
A recuperação de desastres (DR) é um dos principais cenários para os quais o Dispositivo de nuvem StorSimple foi projetado. Nesse cenário, o dispositivo físico StorSimple ou o datacenter inteiro podem não estar disponível. Felizmente, você pode usar um dispositivo de nuvem para restaurar as operações em um local alternativo. Durante a DR, os contêineres de volume do dispositivo de origem alteram a propriedade e são transferidos para o dispositivo de nuvem.

Os pré-requisitos para DR são:

* O dispositivo de nuvem deve estar criado e configurado.
* Todos os volumes no contêiner de volume devem estar offline.
* O contêiner de volume que você executa o failover, deve possuir um tipo de instantâneo de nuvem.

> [!NOTE]
> * Ao usar um dispositivo de nuvem como o dispositivo secundário para a DR, tenha em mente que o 8010 tem 30 TB de armazenamento standard e o 8020 tem 64 TB de armazenamento premium. O dispositivo de nuvem com capacidade superior 8020 pode ser mais adequado para um cenário de recuperação de desastres.

Para obter um procedimento passo a passo, acesse [Como fazer failover para um dispositivo de nuvem](storsimple-8000-device-failover-cloud-appliance.md).

## <a name="delete-the-cloud-appliance"></a>Como excluir o dispositivo de nuvem
Se você configurou e usou um Dispositivo de nuvem StorSimple anteriormente, mas agora deseja parar de acumular encargos de computação para seu uso, então, pare de usar o dispositivo de nuvem. Parar o dispositivo de nuvem resulta no desalocamento da VM. Esta ação interromperá o acúmulo de encargos na sua assinatura. No entanto, os encargos de armazenamento para os sistema operacional e discos de dados continuarão.

Para interromper todos os encargos, você deve excluir o dispositivo de nuvem. Para excluir os backups criados pelo dispositivo de nuvem, você pode desativar ou excluir o dispositivo. Para saber mais, confira [Desativar e excluir um dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).

[!INCLUDE [Delete a cloud appliance](../../includes/storsimple-8000-delete-cloud-appliance.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>Solucionar problemas de erros de conectividade com a Internet
A etapa da criação de um dispositivo de nuvem falhará se não houver nenhuma conectividade com a Internet. Para solucionar falhas de conectividade da Internet, execute as seguintes etapas no portal do Azure:

1. [Crie uma máquina virtual do Windows Server 2012 no Azure](/articles/virtual-machines/windows/quick-create-portal.md). Essa máquina virtual deve usar a mesma conta de armazenamento, VNet e sub-rede que o seu dispositivo de nuvem. Se você já tiver um host do Windows Server existente no Azure usando a mesma conta de armazenamento, VNet e sub-rede, você também poderá usá-lo para solucionar os problemas de conectividade com a Internet.
2. Faça logon remoto na máquina virtual criada na etapa anterior.
3. Abra uma janela de comando dentro da máquina virtual (Win + R e digite `cmd`).
4. Execute o seguinte comando no prompt.

    `nslookup windows.net`
5. Se `nslookup` falhar, então, a falha de conectividade com a Internet está impedindo que o dispositivo de nuvem se registre no serviço do gerenciador de dispositivos StorSimple.
6. Faça as alterações necessárias em sua rede virtual para garantir que o dispositivo de nuvem seja capaz de acessar os sites do Azure, como o _windows.net_.

## <a name="next-steps"></a>Próximas etapas
* Saiba como [usar o serviço do gerenciador de dispositivos StorSimple para gerenciar um dispositivo de nuvem](storsimple-8000-manager-service-administration.md).
* Entenda como [restaurar um volume do StorSimple de um conjunto de backups](storsimple-8000-restore-from-backup-set-u2.md).
