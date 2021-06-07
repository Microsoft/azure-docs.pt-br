---
title: Configurar um laboratório para o ArcMap\ArcGIS desktop com o Azure Lab Services | Microsoft Docs
description: Saiba como configurar um laboratório para classes usando o ArcGIS.
author: nicolela
ms.topic: article
ms.date: 02/04/2021
ms.author: nicolela
ms.openlocfilehash: 530597a72b19afa1e80b5c7640b105d86479b1c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740096"
---
# <a name="set-up-a-lab-for-arcmaparcgis-desktop"></a>Configurar um laboratório para o ArcMap\ArcGIS desktop

[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) é um tipo de GIS (sistema de informações geográficas).  O ArcGIS é usado para make\analyze mapas e trabalhar com dados geográficos fornecidos pelo ESRI ( [Environment Systems Research Institute](https://www.esri.com/en-us/home) ).  Embora o ArcGIS Desktop inclua vários aplicativos, este artigo mostra como configurar os laboratórios para usar o ArcMap.  [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) é usado para fazer, editar e analisar mapas 2D.

## <a name="lab-configuration"></a>Configuração do laboratório

Para começar a configurar um laboratório para usar o ArcMap, você precisa de uma assinatura do Azure e uma conta de laboratório.  Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Depois de obter uma assinatura do Azure, você poderá criar uma nova conta de laboratório no Azure Lab Services.  Para obter mais informações sobre como criar uma nova conta de laboratório, consulte [Configurar uma conta de laboratório](tutorial-setup-lab-account.md).  Você também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Configurações de conta do laboratório

Habilite as configurações de conta do laboratório conforme descrito na tabela a seguir.  Para obter mais informações sobre como habilitar as imagens do Azure Marketplace, consulte [especificar as imagens do Azure Marketplace disponíveis para os criadores de laboratório](./specify-marketplace-images.md).

| Configuração da conta de laboratório | Instruções |
| ------------------- | ------------ |
|Imagem do Marketplace| Habilite a imagem do Windows 10 pro ou Windows 10 pro N para uso em sua conta de laboratório.|

### <a name="licensing-server"></a>Servidor de licenciamento

Um tipo de licenciamento que o ArcGIS Desktop oferece é [licenças de uso simultâneas](https://desktop.arcgis.com/en/license-manager/latest/license-manager-basics.htm).  Isso requer que você instale o Gerenciador de licenças do ArcGIS em seu servidor de licença.  O Gerenciador de licenças controla o número de cópias de software que podem ser executadas ao mesmo tempo.  Para obter mais informações sobre como configurar o Gerenciador de licenças em seu servidor, consulte o [Guia do Gerenciador de licenças](https://desktop.arcgis.com/en/license-manager/latest/welcome.htm).

O servidor de licença normalmente está localizado na sua rede local ou hospedado em uma máquina virtual do Azure em uma rede virtual do Azure.  Depois que o servidor de licença estiver configurado, você precisará [emparelhar a rede virtual](./how-to-connect-peer-virtual-network.md) com sua [conta de laboratório](./tutorial-setup-lab-account.md).  Você precisa fazer o emparelhamento de rede antes de criar o laboratório para que suas VMs de laboratório possam acessar o servidor de licença e vice-versa.

Para obter mais informações, consulte [configurar um servidor de licença como um recurso compartilhado](how-to-create-a-lab-with-shared-resource.md).

### <a name="lab-settings"></a>Configurações do laboratório

O tamanho da VM (máquina virtual) que recomendamos usar para o ArcGIS Desktop depende dos aplicativos, das extensões e das versões específicas que os alunos usarão.  O tamanho da VM também depende das cargas de trabalho que os alunos devem executar.  Consulte [ArcGIS Desktop System Requirements](https://desktop.arcgis.com/en/system-requirements/latest/arcgis-desktop-system-requirements.htm) para ajudar a identificar o tamanho da VM.  Depois de identificar o tamanho potencial da VM, recomendamos que você teste as cargas de trabalho dos alunos para garantir o desempenho adequado.

Neste artigo, é recomendável usar o [tamanho **médio** da VM](administrator-guide.md#vm-sizing) para a versão [10.7.1 do ArcMap](https://desktop.arcgis.com/en/system-requirements/10.7/arcgis-desktop-system-requirements.htm), supondo que nenhuma outra extensão de área de trabalho do ArcGIS seja usada.  No entanto, dependendo das necessidades de sua classe, você pode exigir um tamanho de VM **grande** ou até mesmo um **Small\Medium GPU (visualização)** .  Por exemplo, a [extensão de analista espacial](https://desktop.arcgis.com/en/arcmap/latest/tools/spatial-analyst-toolbox/gpu-processing-with-spatial-analyst.htm) incluída com o ArcGIS Desktop dá suporte a uma GPU para melhorar o desempenho, mas não requer o uso de uma GPU.

| Configuração de laboratório | Valor e descrição |
| ------------ | ------------------ |
|Tamanho da Máquina Virtual| **Média**.  Mais adequado para bancos de dados relacionais, cache na memória e análise.|  

### <a name="template-machine"></a>Computador de modelo

As etapas nesta seção mostram como configurar a VM do modelo:

1.  Inicie a VM de modelo e conecte-se ao computador usando o RDP.

2.  Baixe e instale os componentes da área de trabalho do ArcGIS usando instruções do ESRI.  Essas etapas incluem a atribuição do License Manager para licenciamento de uso simultâneo: 
    - [Introdução à instalação e configuração do ArcGIS Desktop](https://desktop.arcgis.com/en/arcmap/latest/get-started/installation-guide/introduction.htm)

3.  Configure o armazenamento de backup externo para alunos.  Os alunos podem salvar os arquivos diretamente em sua VM atribuída, pois todas as alterações que eles fazem são salvas entre as sessões.  No entanto, recomendamos que os alunos cometam seu trabalho no armazenamento externo de sua VM por alguns motivos:
    - Para permitir que os alunos acessem seu trabalho após o fim da aula e do laboratório.  
    - Caso o aluno obtenha a VM em um estado inadequado e sua imagem precisa ser [redefinida](how-to-set-virtual-machine-passwords.md#reset-vms).

    Com o ArcGIS, cada aluno deve fazer o backup dos seguintes arquivos no final de cada sessão de trabalho:

    - arquivo MXD, que armazena as informações de layout de um projeto.
    - Arquivos geodatabase, que armazenam todos os dados produzidos por ArcGIS.
    - Quaisquer outros dados que o aluno possa usar, como arquivos de varredura, forma, GeoTIFF, etc.

    É recomendável usar o OneDrive para armazenamento de backup.  Para configurar o OneDrive na VM de modelo, siga as etapas no artigo [instalar e configurar o onedrive](how-to-prepare-windows-template.md#install-and-configure-onedrive). 

4.  Por fim, [publique](how-to-create-manage-template.md#publish-the-template-vm) a VM de modelo para criar a VM dos alunos.

### <a name="auto-shutdown-and-disconnect-settings"></a>Configurações de desligamento e desconexão automática

[As configurações de desligamento automático e desconexão](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) de um laboratório ajudam a garantir que a VM de um aluno seja desligada quando não estiver sendo usada.  Essas configurações devem ser definidas de acordo com os tipos de cargas de trabalho que os alunos executarão para que sua VM não seja desligada no meio de seu funcionamento.  Por exemplo, a configuração **Desconectar usuários quando as máquinas virtuais estão ociosas** desconecta o aluno de sua sessão RDP depois que nenhuma entrada de mouse ou teclado foi detectada por um período de tempo especificado.  Essa configuração deve permitir tempo suficiente para cargas de trabalho em que o aluno não está usando ativamente o mouse ou o teclado, como para executar consultas longas ou aguardar a renderização.

Para ArcGIS, recomendamos os seguintes valores para essas configurações:
- Desconectar usuários quando as máquinas virtuais estiverem ociosas
    - 30 minutos após o estado ocioso ser detectado
- Desligar as máquinas virtuais quando os usuários se desconectarem
    - 15 minutos após a desconexão do usuário

## <a name="cost"></a>Cost

Vamos abordar uma possível estimativa de custo para essa classe. Essa estimativa não inclui o custo da execução do servidor de licença. Usaremos uma classe de 25 alunos. Há 20 horas de tempo de classe agendada. Além disso, cada aluno recebe uma cota de 10 horas de casa ou atribuições fora do tempo de classe agendado. O tamanho da máquina virtual que escolhemos era **médio**, que é 42 unidades de laboratório.

25 alunos \* (20 horas agendadas + 10 horas de cota) \* 42 unidades de laboratório * 0, 1 USD por hora = 315, 0 USD

>[!IMPORTANT]
> A estimativa de custo é apenas para fins de exemplo.  Para obter detalhes atuais sobre preços, consulte [preços de Azure Lab Services](https://azure.microsoft.com/pricing/details/lab-services/).  

## <a name="next-steps"></a>Próximas etapas

As próximas etapas são comuns à configuração de qualquer laboratório.

- [Criar e gerenciar um modelo](how-to-create-manage-template.md)
- [Adicionar usuários](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Definir cota](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um agendamento](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de registro de email para alunos](how-to-configure-student-usage.md#send-invitations-to-users)