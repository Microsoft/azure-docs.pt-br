---
title: Como instalar o HANA no SAP HANA no Azure (Instâncias Grandes) | Microsoft Docs
description: Como instalar o HANA no SAP HANA no Azure (Instâncias Grandes).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ce82a2972d9cc349e527811e32c974996327e70b
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709737"
---
# <a name="install-hana-on-sap-hana-on-azure-large-instances"></a>Instalar o HANA no SAP HANA no Azure (Instâncias Grandes)

Para instalar o HANA no SAP HANA no Azure (Instâncias Grandes), primeiro, você precisa fazer o seguinte:
- Forneça à Microsoft todos os dados para implantar uma Instância Grande do SAP HANA.
- Receba a Instância Grande do SAP HANA da Microsoft.
- Crie uma rede virtual do Azure conectada à sua rede local.
- Conecte o circuito do ExpressRoute do HANA em Instâncias Grandes à mesma rede virtual do Azure.
- Instale uma VM do Azure usada como uma jumpbox para o HANA em Instâncias Grandes.
- Certifique-se que você possa se conectar a partir da jumpbox à unidade de Instância Grande do HANA e vice-versa.
- Verifique se todos os pacotes e patches necessários estão instalados.
- Leia as notas SAP e a documentação sobre a instalação do HANA no sistema operacional que você está usando. Verifique se a versão do HANA escolhida é compatível com a versão do sistema operacional.

A próxima seção mostra um exemplo de como baixar os pacotes de instalação do HANA para a máquina de virtual da jumpbox. Nesse caso, o sistema operacional é o Windows.

## <a name="download-the-sap-hana-installation-bits"></a>Baixar os bits de instalação do SAP HANA
As unidades de Instância Grande do HANA não estão diretamente conectadas à Internet. Você não pode baixar diretamente os pacotes de instalação do SAP para a máquina virtual de Instância Grande do HANA. Em vez disso, baixe os pacotes na máquina virtual da jumpbox.

Você precisa de um usuário S do SAP ou outro usuário, o que permite que você acesse o SAP Marketplace.

1. Entre e vá até o [SAP Service Marketplace](https://support.sap.com/en/index.html). Selecione **Baixar Software** > **Instalações e Atualização** > **Por Índice Alfabético**. Em seguida, selecione **Em H – SAP HANA Platform Edition** > **SAP HANA Platform Edition 2.0** > **Instalação**. Baixe os arquivos mostrados na captura de tela a seguir.

   ![Captura de tela dos arquivos a serem baixados](./media/hana-installation/image16_download_hana.PNG)

2. Nesse exemplo, baixamos os pacotes de instalação do SAP HANA 2.0. Na máquina virtual de jumpbox do Azure, expanda os arquivos autoextraíveis para o diretório, conforme mostrado abaixo.

   ![Captura de tela de arquivo autoextraível](./media/hana-installation/image17_extract_hana.PNG)

3. Conforme os arquivos são extraídos, copie o diretório criado pela extração (nesse caso, 51052030). Copie o diretório do volume /hana/shared da unidade de Instância Grande do HANA em um diretório que você criou.

   > [!Important]
   > Não copie os pacotes de instalação na raiz ou no LUN de inicialização, pois o espaço é limitado e precisa ser usado por outros processos também.


## <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>Instalar o SAP HANA na unidade de Instância Grande do HANA
Para instalar o SAP HANA, entre como a raiz do usuário. Somente a raiz tem permissões suficientes para instalar o SAP HANA. Defina permissões no diretório copiado em hana/shared.

```
chmod –R 744 <Installation bits folder>
```

Se deseja instalar o SAP HANA usando a instalação da interface gráfica do usuário, o pacote gtk2 precisa ser instalado no HANA em Instâncias Grandes. Para verificar se está instalado, execute o seguinte comando:

```
rpm –qa | grep gtk2
```

(Nas próximas etapas, demonstramos a instalação do SAP HANA com a interface gráfica do usuário.)

Vá até o diretório de instalação e navegue até o subdiretório HDB_LCM_LINUX_X86_64. 

Fora desse diretório, inicie:

```
./hdblcmgui 
```
Nesse ponto, você percorre uma sequência de telas em que você fornecerá os dados para a instalação. Nesse exemplo, estamos instalando o servidor de banco de dados e os componentes de cliente do SAP HANA. Portanto, nossa seleção é **Banco de dados do SAP HANA**.

![Captura de tela do Gerenciamento de ciclo de vida do SAP HANA, com o Banco de dados do HANA SAP selecionado](./media/hana-installation/image18_hana_selection.PNG)

Na próxima tela, selecione **Instalar Novo Sistema**.

![Captura de tela do Gerenciamento de ciclo de vida do SAP HANA, com Instalar Novo Sistema selecionado](./media/hana-installation/image19_select_new.PNG)

Em seguida, selecione entre os vários componentes adicionais que você pode instalar.

![Captura de tela do Gerenciamento de ciclo de vida do SAP HANA, com lista de componentes adicionais](./media/hana-installation/image20_select_components.PNG)

Aqui, escolhemos o SAP HANA Client e o SAP HANA Studio. Também instalamos uma instância de expansão. Depois, escolha **Sistema de Host Único**. 

![Captura de tela do Gerenciamento de ciclo de vida do SAP HANA, com Sistema de Host Único selecionado](./media/hana-installation/image21_single_host.PNG)

Depois, forneça alguns dados.

![Captura de tela do Gerenciamento de ciclo de vida do SAP HANA, com campos de propriedades do sistema para definir](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> Como o SID (ID do Sistema) do HANA, você deve fornecer o mesmo SID fornecido à Microsoft quando solicitou a implantação da Instância Grande do HANA. A escolha de um SID diferente causa falha na instalação devido a problemas de permissão de acesso em diferentes volumes.

Para o caminho de instalação, use o diretório /hana/shared. Na próxima etapa, forneça os locais dos arquivos de dados e dos arquivos de log do HANA.


![Captura de tela do Gerenciamento de ciclo de vida do SAP HANA, com os campos de dados e área de log](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> O SID especificado quando você definiu as propriedades do sistema (duas telas atrás) deve corresponder ao SID dos pontos de montagem. Se houver uma incompatibilidade, volte e ajuste o SID para o valor existente nos pontos de montagem.

Na próxima etapa, examine o nome do host e, finalmente, corrija-o. 

![Captura de tela do Gerenciamento de ciclo de vida do SAP HANA, com nome do host](./media/hana-installation/image24_review_host_name.PNG)

Na próxima etapa, você também precisa recuperar os dados fornecidos à Microsoft quando solicitou a implantação da Instância Grande do HANA. 

![Captura de tela do Gerenciamento de ciclo de vida do SAP HANA, com campos do administrador do sistema para definir](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> Forneça as mesmas **ID de Usuário do Administrador do Sistema** e **ID do Grupo de Usuários** fornecidas à Microsoft ao solicitar a implantação da unidade. Caso contrário, a instalação do SAP HANA na unidade de Instância Grande do HANA falhará.

As duas próximas telas não são mostradas aqui. Elas permitem que você forneça a senha para o usuário do SISTEMA do banco de dados do SAP HANA e a senha do usuário sapadm. A segunda é usada para o Agente de host do SAP que é instalado como parte da instância de banco de dados do SAP HANA.

Depois de definir a senha, uma tela de confirmação é exibida. Verifique todos os dados listados e continue com a instalação. Você acessa uma tela de progresso que documenta o progresso da instalação, como esta:

![Captura de tela do Gerenciamento de ciclo de vida do SAP HANA, com indicadores de progresso da instalação](./media/hana-installation/image27_show_progress.PNG)

Quando a instalação for concluída, você verá uma tela como esta:

![Captura de tela do Gerenciamento de ciclo de vida do SAP HANA indicando que a instalação foi concluída](./media/hana-installation/image28_install_finished.PNG)

A instância do SAP HANA deve estar pronta para uso. Você deve conseguir se conectar a ele no SAP HANA Studio. Além disso, verifique se há atualizações e aplique-as.


## <a name="next-steps"></a>Próximas etapas

- [Alta disponibilidade e recuperação de desastre do SAP HANA em Instâncias Grandes do Azure](hana-overview-high-availability-disaster-recovery.md)

