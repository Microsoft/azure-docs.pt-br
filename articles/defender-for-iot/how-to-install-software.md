---
title: Instalação do defender para IoT
description: Saiba como instalar um sensor e o console de gerenciamento local do Azure defender para IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/2/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 49a0129ff26d4a12392066aa6304317d71fdb0f1
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247583"
---
# <a name="defender-for-iot-installation"></a>Instalação do defender para IoT

Este artigo descreve como instalar os seguintes elementos do Azure defender para IoT:

- **Sensor**: os sensores do defender para IOT coletam o tráfego de rede do ICS usando o monitoramento passivo (sem agente). Passivo e não intrusivo, os sensores não afetam nenhum impacto em redes e dispositivos IoT. O sensor se conecta a uma porta de SPAN ou toque de rede e imediatamente começa a monitorar a rede. As detecções são exibidas no console do sensor. Lá, você pode exibir, investigar e analisá-los em um mapa de rede, inventário de dispositivos e uma ampla gama de relatórios. Os exemplos incluem relatórios de avaliação de risco, consultas de Data Mining e vetores de ataque. Leia mais sobre os recursos do sensor no [Guia do usuário do sensor do defender for IOT (download direto)](./getting-started.md).

- **Console de gerenciamento local**: o console de gerenciamento local permite que você realize o gerenciamento de dispositivos, gerenciamento de riscos e gerenciamento de vulnerabilidades. Você também pode usá-lo para realizar o monitoramento de ameaças e a resposta a incidentes em sua empresa. Ele fornece uma exibição unificada de todos os dispositivos de rede, IoT de chave e alertas e indicadores de risco detectados em instalações em que os sensores são implantados. Use o console de gerenciamento local para exibir e gerenciar sensores em redes gapped.

Este artigo aborda as seguintes informações de instalação:

  - **Hardware:** Detalhes do dispositivo físico Dell e HPE.

  - **Software:** Instalação de software do console de gerenciamento local e do sensor.

  - **Dispositivos virtuais:** Detalhes da máquina virtual e instalação de software.

Após a instalação, conecte o sensor à sua rede.

## <a name="about-defender-for-iot-appliances"></a>Sobre o defender para dispositivos IoT 

As seções a seguir fornecem informações sobre o defender para dispositivos de sensor de IoT e o dispositivo para o console de gerenciamento local do defender para IoT.

### <a name="physical-appliances"></a>Dispositivos físicos

O sensor do dispositivo defender para IoT conecta-se a uma porta SPAN ou toque na rede e imediatamente começa a coletar o tráfego de rede do ICS usando o monitoramento passivo (sem agente). Esse processo tem um impacto zero sobre a existência de redes e dispositivos porque ele não é colocado no caminho de dados e não examina ativamente os dispositivos.

Os seguintes dispositivos de montagem em rack estão disponíveis:

| **Tipo de implantação** | **Corporativo** | **Empresa** | **SMB** |  |
|--|--|--|--|--|
| **Modelo** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **Portas de monitoramento** | até 15 RJ45 ou 8 aceitar | até 9 RJ45 ou 6 aceitar | até 8 RJ45 ou 6 aceitar | 4 RJ45 |
| **Largura de \* banda máxima* _ | 3 GB/s | 1 GB/s | 1 GB/s | 100 MB/s |
| _ *Máximo de dispositivos protegidos** | 30,000 | 10.000 | 15,000 | 1,000 |

* A capacidade de largura de banda máxima pode variar dependendo da distribuição de protocolo.

### <a name="virtual-appliances"></a>Dispositivos virtuais

Os seguintes dispositivos virtuais estão disponíveis:

| **Tipo de implantação** | **Empresa** | **SMB** | **Linha** |
|--|--|--|--|
| **Descrição** | Dispositivo virtual para implantações corporativas | Dispositivo virtual para implantações SMB | Dispositivo virtual para implantações de linha |
| **Largura de \* banda máxima* _ | 150 MB/s | 15 MB/s | 3 MB/s |
| _ *Máximo de dispositivos protegidos** | 3\.000 | 300 | 100 |
| **Tipo de implantação** | Enterprise | SMB | Linha |
| **Descrição** | Dispositivo virtual para implantações corporativas | Dispositivo virtual para implantações SMB | Dispositivo virtual para implantações de linha |

* A capacidade de largura de banda máxima pode variar dependendo da distribuição de protocolo.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Especificações de hardware para o console de gerenciamento local

 | Item | DESCRIÇÃO |
 |----|--|
 **Descrição** | Em uma arquitetura de várias camadas, o console de gerenciamento local oferece visibilidade e controle em sites geograficamente distribuídos. Ele se integra com as pilhas de segurança do SOC, incluindo SIEMs, sistemas de emissão de tíquete, firewalls de última geração, plataformas de acesso remoto seguro e a área restrita de malware do ICS do defender para IoT. |
 **Tipo de implantação** | Enterprise |
 **Tipo de dispositivo**  | Dell R340, VM |
 **Número de sensores gerenciados** | Ilimitado |

## <a name="prepare-for-the-installation"></a>Preparar para a instalação

### <a name="access-the-iso-installation-image"></a>Acessar a imagem de instalação ISO

A imagem de instalação pode ser acessada no portal do defender para IoT.

Para acessar o arquivo:

1. Entre em sua conta do defender for IoT.

2. Acesse o **sensor de rede** ou a página **do console de gerenciamento local** e selecione uma versão para baixar.

### <a name="install-from-dvd"></a>Instalar do DVD

Antes da instalação, verifique se você tem:

- Uma unidade de DVD portátil com o conector USB.

- Uma imagem do instalador ISO.

Para instalar:

1. Grave a imagem em um DVD ou prepare um disco em uma chave. Conecte uma unidade de DVD portátil ao seu computador, clique com o botão direito do mouse na imagem ISO e selecione **gravar no disco**.

1. Conecte o DVD ou disco em uma chave e configure o dispositivo para inicialização de DVD ou disco em uma chave.

### <a name="install-from-disk-on-a-key"></a>Instalar do disco em uma chave

Antes da instalação, verifique se você tem:

  - Rufus instalado.
  
  - Um disco na chave com USB versão 3,0 e posterior. O tamanho mínimo é 4 GB.

  - Um arquivo de imagem do instalador ISO.

O disco em uma chave será apagado nesse processo.

Para preparar um disco em uma chave:

1. Execute Rufus e selecione **sensor ISO**.

2. Conecte o disco em uma chave ao painel frontal.

3. Defina o BIOS do servidor para inicializar a partir do USB.

## <a name="dell-poweredger340xl-installation"></a>Instalação do Dell PowerEdgeR340XL

Antes de instalar o software no dispositivo Dell, você precisa ajustar a configuração do BIOS do dispositivo:

  - O painel [frontal do Dell PowerEdge R340](#dell-poweredge-r340-front-panel) e [Dell PowerEdge R340 Backpanel](#dell-poweredge-r340-back-panel) contém a descrição dos painéis front e back, juntamente com as informações necessárias para a instalação, como drivers e portas.

  - A [configuração de BIOS da Dell](#dell-bios-configuration) fornece informações sobre como se conectar à interface de gerenciamento de dispositivos Dell e configurar o BIOS.

  - A [instalação de software (Dell R340)](#software-installation-dell-r340) descreve o procedimento necessário para instalar o software do sensor do defender para IOT.

### <a name="dell-poweredge-r340xl-requirements"></a>Requisitos do Dell PowerEdge R340XL 

Para instalar o dispositivo Dell PowerEdge R340XL, você precisa:

- Licença Enterprise para iDrac (controlador de acesso remoto da Dell)

- XML de configuração do BIOS

- Versões do firmware do servidor:

  - Versão do BIOS 2.1.6

  - iDrac versão 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Painel frontal do Dell PowerEdge R340

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Painel frontal do Dell PowerEdge R340.":::

 1. Painel de controle à esquerda 
 2. Unidade óptica (opcional) 
 3. Painel de controle à direita 
 4. Marca de informações 
 5. Unidades  

### <a name="dell-poweredge-r340-back-panel"></a>Painel de fundo do Dell PowerEdge R340

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Painel de fundo do Dell PowerEdge R340.":::

1. Porta serial 
2. Porta NIC (GB 1) 
3. Porta NIC (GB 1) 
4. PCIe de meia altura 
5. Slot de placa de expansão PCIe de altura total 
6. Fonte de alimentação 1 
7. Unidade de alimentação de energia 2 
8. Identificação do sistema 
9. Botão porta do cabo do indicador de status do sistema (CMA) 
10. Porta USB 3,0 (2) 
11. porta de rede dedicada iDRAC9 
12. Porta VGA 

### <a name="dell-bios-configuration"></a>Configuração de BIOS da Dell

A configuração de BIOS da Dell é necessária para ajustar o dispositivo Dell para trabalhar com o software.

A configuração do BIOS é executada por meio de uma configuração predefinida. O arquivo pode ser acessado no [centro de ajuda](https://help.cyberx-labs.com/).

Importe o arquivo de configuração para o dispositivo Dell. Antes de usar o arquivo de configuração, você precisa estabelecer a comunicação entre o dispositivo Dell e o computador de gerenciamento.

O dispositivo Dell é gerenciado por um iDRAC integrado com LC (controlador de ciclo de vida). A LC é inserida em todos os servidores Dell PowerEdge e fornece funcionalidade que ajuda você a implantar, atualizar, monitorar e manter seus dispositivos Dell PowerEdge.

Para estabelecer a comunicação entre o dispositivo Dell e o computador de gerenciamento, você precisa definir o endereço IP iDRAC e o endereço IP do computador de gerenciamento na mesma sub-rede.

Quando a conexão é estabelecida, o BIOS é configurável.

Para configurar o BIOS da Dell:

1. [Configurar o endereço IP iDRAC](#configure-idrac-ip-address)

2. [Importar o arquivo de configuração do BIOS](#import-the-bios-configuration-file)

#### <a name="configure-idrac-ip-address"></a>Configurar o endereço IP do iDRAC

1. Ligue o sensor.

2. Se o sistema operacional já estiver instalado, selecione a tecla F2 para entrar na configuração do BIOS.

3. Selecione **configurações de iDRAC**.

4. Selecione **rede**.

   > [!NOTE]
   > Durante a instalação, você deve configurar o endereço IP iDRAC padrão e a senha mencionados nas etapas a seguir. Após a instalação, você altera essas definições.

5. Altere o endereço IPv4 estático para **10.100.100.250**.

6. Altere a máscara de sub-rede estática para **255.255.255.0**.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Captura de tela que mostra a máscara de sub-rede estática.":::

7. Selecione **back**-  >  **Finish**.

#### <a name="import-the-bios-configuration-file"></a>Importar o arquivo de configuração do BIOS

Este artigo descreve como configurar o BIOS usando o arquivo de configuração.

1. Conecte um PC com um endereço IP pré-configurado estático **10.100.100.200** à porta **iDRAC** .

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Captura de tela da porta de endereço IP pré-configurada.":::

2. Abra um navegador e insira **10.100.100.250** para se conectar à interface da Web do iDRAC.

3. Entre com os privilégios de administrador padrão da Dell:

   - Nome de usuário: **raiz**

   - Senha: **Calvin**

4. As credenciais do dispositivo são:

   - Nome de usuário: **xxx**

   - Senha: **xxx**

     A operação importar perfil do servidor foi iniciada.

     > [!NOTE]
     > Antes de importar o arquivo, verifique se:
     > - Você é o único usuário atualmente conectado ao iDRAC.
     > - O sistema não está no menu BIOS.

5. Acesse o  >  **perfil de configuração do servidor** de configuração. Defina os seguintes parâmetros:

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="Captura de tela que mostra a configuração do seu perfil de servidor.":::

   | Parâmetro | Configuração |
   |--|--|
   | Tipo de local | Selecione **local**. |
   | Caminho do Arquivo | Selecione **escolher arquivo** e adicionar o arquivo XML de configuração. |
   | Importar componentes | Selecione **BIOS, NIC, RAID**. |
   | Tempo de espera máximo | Selecione **20 minutos**. |

6. Selecione **Importar**.

7. Para monitorar o processo, acesse   >  **fila de trabalhos** de manutenção.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="Captura de tela que mostra a fila de trabalhos.":::

#### <a name="manually-configuring-bios"></a>Configurando manualmente o BIOS 

Você precisa configurar manualmente o BIOS do dispositivo se:

- Você não comprou seu dispositivo da seta.

- Você tem um dispositivo, mas não tem acesso ao arquivo de configuração XML.

Depois de acessar o BIOS, vá para **configurações do dispositivo**.

Para configurar manualmente:

1. Acesse o BIOS do dispositivo diretamente usando um teclado e uma tela ou use iDRAC.

   - Se o dispositivo não for um dispositivo defender para IoT, abra um navegador e vá para o endereço IP que foi configurado antes. Entre com os privilégios de administrador padrão da Dell. Use **root** para o nome de usuário e **Calvin** para a senha.

   - Se o dispositivo for um dispositivo defender para IoT, entre usando **xxx** para o nome de usuário e **xxx** para a senha.

2. Depois de acessar o BIOS, vá para **configurações do dispositivo**.

3. Escolha a configuração controlada por RAID selecionando **controlador RAID integrado 1: utilitário de \<PERC H330 Adapter\> configuração do Dell PERC**.

4. Selecione **Gerenciamento de configuração**.

5. Selecione **criar disco virtual**.

6. No campo **selecionar nível de RAID** , selecione **RAID5**. No campo **nome do disco virtual** , insira **raiz** e selecione **discos físicos**.

7. Selecione **marcar tudo** e, em seguida, selecionar **aplicar alterações**

8. Selecione **OK**.

9. Role para baixo e selecione **criar disco virtual**.

10. Marque a caixa de seleção **confirmar** e selecione **Sim**.

11. Selecione **OK**.

12. Retorne à tela principal e selecione **BIOS do sistema**.

13. Selecione **configurações de inicialização**.

14. Para a opção **modo de inicialização** , selecione **BIOS**.

15. Selecione **voltar** e, em seguida, selecione **concluir** para sair das configurações do BIOS.

### <a name="software-installation-dell-r340"></a>Instalação de software (Dell R340)

O processo de instalação leva cerca de 20 minutos. Após a instalação, o sistema é reiniciado várias vezes.

Para instalar:

1. Verifique se a mídia de versão está montada no dispositivo de uma das seguintes maneiras:

   - Conecte o CD ou disco externo em uma chave com a versão.

   - Monte a imagem ISO usando iDRAC. Depois de entrar no iDRAC, selecione o console virtual e, em seguida, selecione **mídia virtual**.

2. Na seção **mapear CD/DVD** , selecione **escolher arquivo**.

3. Escolha o arquivo de imagem ISO da versão para esta versão na caixa de diálogo que é aberta.

4. Selecione o botão **mapear dispositivo** .

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Captura de tela que mostra um dispositivo mapeado.":::

5. A mídia está montada. Selecione **Fechar**.

6. Inicie o dispositivo. Quando você estiver usando o iDRAC, poderá reiniciar os servidores selecionando o botão **controle Consul** . Em seguida, nas **macros de teclado**, selecione o botão **aplicar** , que iniciará a sequência Ctrl + Alt + Delete.

7. Selecione **Inglês**.

8. Selecione **sensor-Release- \<version\> Enterprise**.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Captura de tela que mostra a seleção de versão.":::   

9. Defina o perfil do dispositivo e as propriedades da rede:

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Captura de tela que mostra o perfil do dispositivo.":::   

   | Parâmetro | Configuração |
   |--|--|
   | **Perfil de hardware** | **porte** |
   | **Interface de gerenciamento** | **eno1** |
   | **Parâmetros de rede (fornecidos pelo cliente)** | - |
   |**Endereço IP da rede de gerenciamento:** | - |
   | **máscara de sub-rede:** | - |
   | **nome do host do dispositivo:** | - |
   | **DNS** | - |
   | **Endereço IP do gateway padrão:** | - |
   | **interfaces de entrada:** |  O sistema gera a lista de interfaces de entrada para você. Para espelhar as interfaces de entrada, copie todos os itens apresentados na lista com um separador de vírgula. Observe que não há necessidade de configurar a interface de ponte. Esta opção é usada somente para casos de uso especiais. |

10. Após cerca de 10 minutos, os dois conjuntos de credenciais são exibidos. Um é para um usuário do **CyberX** e um para um usuário de **suporte** .  

11. Salve a ID do dispositivo e as senhas. Você precisará dessas credenciais para acessar a plataforma na primeira vez que usá-la.

12. Selecione **Enter** para continuar.

## <a name="hpe-proliant-dl20-installation"></a>Instalação do HPE ProLiant DL20

Este artigo descreve o processo de instalação do HPE ProLiant DL20, que inclui as seguintes etapas:

  - Habilite o acesso remoto e atualize a senha de administrador padrão.
  - Defina as configurações de BIOS e RAID.
  - Instale o software.

### <a name="about-the-installation"></a>Sobre a instalação

  - Os dispositivos empresariais e SMB podem ser instalados. O processo de instalação é idêntico para ambos os tipos de dispositivo, exceto para a configuração de matriz.
  - Um usuário administrativo padrão é fornecido. Recomendamos que você altere a senha durante o processo de configuração de rede.
  - Durante o processo de configuração de rede, você configurará a porta iLO na porta de rede 1.
  - O processo de instalação leva cerca de 20 minutos. Após a instalação, o sistema é reiniciado várias vezes.

### <a name="hpe-proliant-dl20-front-panel"></a>Painel frontal do HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="HPE ProLiant DL20 Front Panel.":::

### <a name="hpe-proliant-dl20-back-panel"></a>Painel de fundo do HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="O painel de fundo do HPE ProLiant DL20.":::

### <a name="enable-remote-access-and-update-the-password"></a>Habilitar o acesso remoto e atualizar a senha

Use o procedimento a seguir para configurar as opções de rede e atualizar a senha padrão.

Para habilitar e atualizar a senha:

1. Conecte uma tela e um teclado ao dispositivo HP, ligue o dispositivo e pressione **F9**.

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Captura de tela que mostra a janela HPE ProLiant.":::

2. Vá para **sistema utilitários** configuração do  >  **sistema**  >  **ilo 5 utilitário de configuração**  >  **Opções de rede**.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Captura de tela que mostra a janela de configuração do sistema.":::

    1.  Selecione **porta de rede compartilhada-Lom** no campo **adaptador de interface de rede** .
    
    1.  Desabilite o DHCP.
    
    1.  Insira o endereço IP, a máscara de sub-rede e o endereço IP do gateway.

3. Selecione **F10: salvar**.

4. Selecione **ESC** para voltar ao utilitário de **configuração do ilo 5** e selecione gerenciamento de **usuário**.

5. Selecione **editar/remover usuário**. O administrador é o único definido pelo usuário padrão. 

6. Altere a senha padrão e selecione **F10: Save**.

### <a name="configure-the-hpe-bios"></a>Configurar o BIOS do HPE

O procedimento a seguir descreve como configurar o BIOS do HPE para os dispositivos empresariais e SMB.

Para configurar o BIOS do HPE:

1. Selecione **utilitários do sistema** configuração  >  do **sistema**  >  **BIOS/configuração de plataforma (RBSU)**.

2. No formulário **configuração de BIOS/plataforma (RBSU)** , selecione **Opções de inicialização**.

3. Altere o **modo de inicialização** para modo de **BIOS herdado** e, em seguida, selecione **F10: Save**.

4. Selecione **ESC** duas vezes para fechar o formulário **configuração do sistema** .

#### <a name="for-the-enterprise-appliance"></a>Para o dispositivo empresarial

1. Selecione **RAID 1: HPE Smart Array P408i-a Sr Gen 10**  >  **array Configuration**  >  **Create array**.

2. No formulário **criar matriz** , selecione todas as opções. Três opções estão disponíveis para o dispositivo **empresarial** .

#### <a name="for-the-smb-appliance"></a>Para o dispositivo SMB

1. Selecione **RAID 1: HPE Smart Array P208i-a Sr Gen 10**  >  **array Configuration**  >  **Create array**.

2. Selecione **prosseguir para o próximo formulário**.

3. No formulário **definir nível de RAID** , defina o nível como **RAID 5** para implantações corporativas e **RAID 1** para implantações SMB.

4. Selecione **prosseguir para o próximo formulário**.

5. No formulário **rótulo da unidade lógica** , digite **unidade lógica 1**.

6. Selecione **enviar alterações**.

7. No formulário **Enviar** , selecione **voltar ao menu principal**.

8. Selecione **F10: Save** e pressione **ESC** duas vezes.

9. Na janela **utilitários do sistema** , selecione o **menu de inicialização única**.

10. No formulário de **menu de inicialização única** , selecione **BIOS herdado One-Time menu de inicialização**.

11. A **inicialização em janelas herdadas** e de **substituição de inicialização** é exibida. Escolha uma opção de substituição de inicialização; por exemplo, para um CD-ROM, USB, HDD ou shell UEFI.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Captura de tela que mostra a primeira janela de substituição de inicialização.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Captura de tela que mostra a segunda janela de substituição de inicialização.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Instalação de software (HPE ProLiant DL20 Appliance)

O processo de instalação leva cerca de 20 minutos. Após a instalação, o sistema é reiniciado várias vezes.

Para instalar o software:

1. Conecte a tela e o teclado ao dispositivo e conecte-se à CLI.

2. Conecte um CD ou disco externo na chave com a imagem ISO que você baixou da página **atualizações** no portal do defender para IOT.

3. Inicie o dispositivo.

4. Selecione **Inglês**.

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Seleção de inglês na janela da CLI.":::

5. Selecione **sensor-Release- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Captura da tela para selecionar uma versão.":::

6. No assistente de instalação, defina o perfil do dispositivo e as propriedades da rede:

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Captura de tela que mostra o assistente de instalação.":::

    | Parâmetro | Configuração |
    | ----------| ------------- |
    | **Perfil de hardware** | Selecione **Enterprise** ou **Office** para implantações SMB. |
    | **Interface de gerenciamento** | **eno2** |
    | **Parâmetros de rede padrão (geralmente os parâmetros são fornecidos pelo cliente)** | **Endereço IP da rede de gerenciamento:** <br/> <br/>**nome do host do dispositivo:** <br/>**DNS** <br/>**o endereço IP do gateway padrão:**|
    | **interfaces de entrada:** | O sistema gera a lista de interfaces de entrada para você.<br/><br/>Para espelhar as interfaces de entrada, copie todos os itens apresentados na lista com um separador de vírgula: **eno5, eno3, eno1, eno6, eno4**<br/><br/>**Para HPE DL20: não liste eno1, enp1s0f4u4 (interfaces iLo)**<br/><br/>**Ponte**: não é necessário configurar a interface de ponte. Esta opção é usada somente para casos de uso especiais. Pressione **Enter** para continuar. |

7. Após cerca de 10 minutos, os dois conjuntos de credenciais são exibidos. Um é para um usuário do **CyberX** e um para um usuário de **suporte** .

8. Salve a ID e as senhas do dispositivo. Você precisará das credenciais para acessar a plataforma pela primeira vez.

9. Selecione **Enter** para continuar.

## <a name="hpe-proliant-dl360-installation"></a>Instalação do HPE ProLiant DL360

  - Um usuário administrativo padrão é fornecido. Recomendamos que você altere a senha durante a configuração de rede.

  - Durante a configuração de rede, você configurará a porta iLO.

  - O processo de instalação leva cerca de 20 minutos. Após a instalação, o sistema é reiniciado várias vezes.

### <a name="hpe-proliant-dl360-front-panel"></a>Painel frontal do HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="HPE ProLiant DL360 Front Panel.":::

### <a name="hpe-proliant-dl360-back-panel"></a>Painel de fundo do HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="Painel de fundo do HPE ProLiant DL360.":::

### <a name="enable-remote-access-and-update-the-password"></a>Habilitar o acesso remoto e atualizar a senha

Consulte as seções anteriores para a instalação do HPE ProLiant DL20:

  - "Habilitar acesso remoto e atualizar a senha"

  - "Configurar o BIOS do HPE"

A configuração empresarial é idêntica.

> [!Note]
> No formulário matriz, verifique se você selecionou todas as opções.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>instalação remota do iLO (de uma unidade virtual)

Este procedimento descreve a instalação do iLO de uma unidade virtual.

Para instalar:

1. Entre no console do iLO e clique com o botão direito do mouse na tela dos servidores.

2. Selecione **console HTML5**.

3. No console do, selecione o ícone de CD e escolha a opção CD/DVD.

4. Selecione **arquivo ISO local**.

5. Na caixa de diálogo, escolha o arquivo ISO relevante.

6. Vá para o ícone à esquerda, selecione **energia** e selecione **Redefinir**.

7. O dispositivo será reiniciado e executará o processo de instalação do sensor.

### <a name="software-installation-hpe-dl360"></a>Instalação de software (HPE DL360)

O processo de instalação leva cerca de 20 minutos. Após a instalação, o sistema é reiniciado várias vezes.

Para instalar:

1. Conecte a tela e o teclado ao dispositivo e conecte-se à CLI.

2. Conecte um CD ou disco externo em uma chave com a imagem ISO que você baixou da página **atualizações** no portal do defender para IOT.

3. Inicie o dispositivo.

4. Selecione **Inglês**.

5. Selecione **sensor-Release- <version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Captura de tela que mostra a seleção da versão.":::

6. No assistente de instalação, defina o perfil do dispositivo e as propriedades da rede.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Captura de tela que mostra o assistente de instalação.":::

    | Parâmetro | Configuração |
    | ----------| ------------- |
    | **Perfil de hardware** | Selecione **corporativo**. |
    | **Interface de gerenciamento** | **eno2** |
    | **Parâmetros de rede padrão (fornecidos pelo cliente)** | **Endereço IP da rede de gerenciamento:** <br/>**máscara de sub-rede:** <br/>**nome do host do dispositivo:** <br/>**DNS** <br/>**o endereço IP do gateway padrão:**|
    | **interfaces de entrada:**  | O sistema gera uma lista de interfaces de entrada para você.<br/><br/>Para espelhar as interfaces de entrada, copie todos os itens apresentados na lista com um separador de vírgula.<br/><br/>Observe que não há necessidade de configurar a interface de ponte. Esta opção é usada somente para casos de uso especiais. |

7. Após cerca de 10 minutos, os dois conjuntos de credenciais são exibidos. Um é para um usuário do **CyberX** e um para um usuário de **suporte** .

8. Salve a ID e as senhas do dispositivo. Você precisará dessas credenciais para acessar a plataforma pela primeira vez.

9. Selecione **Enter** para continuar.

## <a name="sensor-installation-for-the-virtual-appliance"></a>Instalação do sensor para a solução de virtualização

Você pode implantar a máquina virtual para o sensor do defender for IoT nas seguintes arquiteturas:


| Arquitetura | Especificações | Uso | Comentários |
|---|---|---|---|
| **Empresa** | CPU: 8<br/>Memória: 32G RAM<br/>HDD: 1800 GB | Ambiente de produção | Padrão e mais comum |
| **Pequenas empresas** | CPU: 4 <br/>Memória: 8G RAM<br/>HDD: 500 GB | Ambientes de teste ou de produção pequena | -  |
| **Office** | CPU: 4<br/>Memória: 8G RAM<br/>HDD: 100 GB | Pequenos ambientes de teste | -  |

### <a name="prerequisites"></a>Pré-requisitos

O console de gerenciamento local dá suporte às opções de implantação do VMware e do Hyper-V. Antes de começar a instalação, verifique se você tem os seguintes itens:

  - VMware (ESXi 5,5 ou posterior) ou hipervisor do Hyper-V (Windows 10 pro ou Enterprise) instalado e operacional

  - Recursos de hardware disponíveis para a máquina virtual

  - Arquivo de instalação ISO para o sensor do Azure defender para IoT

Verifique se o hipervisor está em execução.

### <a name="create-the-virtual-machine-esxi"></a>Criar a máquina virtual (ESXi)

1. Entre no ESXi, escolha o **repositório** de armazenamento relevante e selecione o **navegador de repositório de armazenamento**.

2. **Carregue** a imagem e selecione **fechar**.

3. Vá para **máquinas virtuais** e, em seguida, selecione **criar/registrar VM**.

4. Selecione **criar nova máquina virtual** e, em seguida, selecione **Avançar**.

5. Adicione um nome de sensor e escolha:

   - Compatibilidade: **&lt; última versão &gt; do ESXi**

   - Família do SO convidado: **Linux**

   - Versão do SO convidado: **Ubuntu Linux (64 bits)**

6. Selecione **Avançar**.

7. Escolha o repositório de armazenamento relevante e selecione **Avançar**.

8. Altere os parâmetros de hardware virtual de acordo com a arquitetura necessária.

9. Para **unidade 1 de CD/DVD**, selecione o **arquivo ISO do datastore** e escolha o arquivo ISO que você carregou anteriormente.

10. Selecione **Avançar** > **Concluir**.

### <a name="create-the-virtual-machine-hyper-v"></a>Criar a máquina virtual (Hyper-V)

Este procedimento descreve como criar uma máquina virtual usando o Hyper-V.

Para criar uma máquina virtual:

1. Crie um disco virtual no Gerenciador do Hyper-V.

2. Selecione **Format = VHDX**.

3. Selecione **tipo = expansão dinâmica**.

4. Insira o nome e o local do VHD.

5. Insira o tamanho necessário (de acordo com a arquitetura).   

6. Examine o resumo e selecione **Concluir**.

7. No menu **ações** , crie uma nova máquina virtual.

8. Insira um nome para a máquina virtual.

9. Selecione **especificar**  >  **geração** de geração 1.

10. Especifique a alocação de memória (de acordo com a arquitetura) e marque a caixa de seleção para memória dinâmica.

11. Configure o adaptador de rede de acordo com a topologia de rede do servidor.

12. Conecte o VHDX criado anteriormente à máquina virtual.

13. Examine o resumo e selecione **Concluir**.

14. Clique com o botão direito do mouse na nova máquina virtual e selecione **configurações**.

15. Selecione **adicionar hardware** e adicionar um novo adaptador de rede.

16. Selecione o comutador virtual que se conectará à rede de gerenciamento do sensor.

17. Aloque recursos de CPU (de acordo com a arquitetura).

18. Conecte a imagem ISO do console de gerenciamento a uma unidade de DVD virtual.

19. Iniciar a máquina virtual.

20. No menu **ações** , selecione **conectar** para continuar a instalação do software.

### <a name="software-installation-esxi-and-hyper-v"></a>Instalação de software (ESXi e Hyper-V)

Esta seção descreve a instalação do software ESXi e do Hyper-V.

Para instalar:

1. Abra o console da máquina virtual.

2. A VM será iniciada a partir da imagem ISO e a tela de seleção de idioma será exibida. Selecione **Inglês**.

3. Selecione a arquitetura necessária.

4. Defina o perfil do dispositivo e as propriedades da rede:

    | Parâmetro | Configuração |
    | ----------| ------------- |
    | **Perfil de hardware** | &lt;arquitetura necessária&gt; |
    | **Interface de gerenciamento** | **ens192** |
    | **Parâmetros de rede (fornecidos pelo cliente)** | **Endereço IP da rede de gerenciamento:** <br/>**máscara de sub-rede:** <br/>**nome do host do dispositivo:** <br/>**DNS** <br/>**gateway padrão:** <br/>**interfaces de entrada:**|
    | **interfaces de ponte:** | Não é necessário configurar a interface de ponte. Essa opção é apenas para casos de uso especiais. |

5. Digite **Y** para aceitar as configurações.

6. As credenciais de entrada são geradas e apresentadas automaticamente. Copie o nome de usuário e a senha em um local seguro, pois eles são necessários para a entrada e a administração.

   - **Suporte**: o usuário administrativo para gerenciamento de usuários.

   - **CyberX**: o equivalente de raiz para acessar o dispositivo.

7. O dispositivo é reiniciado.

8. Acesse o console de gerenciamento por meio do endereço IP configurado anteriormente: `https://ip_address` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Captura de tela que mostra o acesso ao console de gerenciamento.":::

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Dispositivo virtual: instalação do console de gerenciamento local

A VM do console de gerenciamento local dá suporte às seguintes arquiteturas:

| Arquitetura | Especificações | Uso | 
|--|--|--|
| Enterprise <br/>(Padrão e mais comum) | CPU: 8 <br/>Memória: 32G RAM<br/> HDD: 1,8 TB | Ambientes de produção grandes | 
| Enterprise | CPU: 4 <br/> Memória: 8G RAM<br/> HDD: 500 GB | Ambientes de produção grandes |
| Enterprise | CPU: 4 <br/>Memória: 8G RAM <br/> HDD: 100 GB | Pequenos ambientes de teste | 
   
### <a name="prerequisites"></a>Pré-requisitos

O console de gerenciamento local dá suporte às opções de implantação do VMware e do Hyper-V. Antes de começar a instalação, verifique o seguinte:

- VMware (ESXi 5,5 ou posterior) ou hipervisor do Hyper-V (Windows 10 pro ou Enterprise) está instalado e operacional.

- Os recursos de hardware estão disponíveis para a máquina virtual.

- Você tem o arquivo de instalação ISO para o console de gerenciamento local.
    
- O hipervisor está em execução.

### <a name="create-the-virtual-machine-esxi"></a>Criar a máquina virtual (ESXi)

Para criar uma máquina virtual (ESXi):

1. Entre no ESXi, escolha o **repositório** de armazenamento relevante e selecione o **navegador de repositório de armazenamento**.

2. Carregue a imagem e selecione **fechar**.

3. Vá para **máquinas virtuais**.

4. Selecione **criar/registrar VM**.

5. Selecione **criar nova máquina virtual** e selecione **Avançar**.

6. Adicione um nome de sensor e escolha:

   - Compatibilidade \<latest ESXi version>

   - Família do SO convidado: Linux

   - Versão do SO convidado: Ubuntu Linux (64 bits)

7. Selecione **Avançar**.

8. Escolha repositório de armazenamento relevante e selecione **Avançar**.

9. Altere os parâmetros de hardware virtual de acordo com a arquitetura necessária.

10. Para **unidade 1 de CD/DVD**, selecione o **arquivo ISO do datastore** e escolha o arquivo ISO que você carregou anteriormente.

11. Selecione **Avançar** > **Concluir**.

### <a name="create-the-virtual-machine-hyper-v"></a>Criar a máquina virtual (Hyper-V)

Para criar uma máquina virtual usando o Hyper-V:

1. Crie um disco virtual no Gerenciador do Hyper-V.

2. Selecione o formato **VHDX**.

3. Selecione **Avançar**.

4. Selecione o tipo **expansão dinâmica**.

5. Selecione **Avançar**.

6. Insira o nome e o local do VHD.

7. Selecione **Avançar**.

8. Insira o tamanho necessário (de acordo com a arquitetura).

9. Selecione **Avançar**.

10. Examine o resumo e selecione **Concluir**.

11. No menu **ações** , crie uma nova máquina virtual.

12. Selecione **Avançar**.

13. Insira um nome para a máquina virtual.

14. Selecione **Avançar**.

15. Selecione **geração** e defina-a como **geração 1**.

16. Selecione **Avançar**.

17. Especifique a alocação de memória (de acordo com a arquitetura) e marque a caixa de seleção para memória dinâmica.

18. Selecione **Avançar**.

19. Configure o adaptador de rede de acordo com a topologia de rede do servidor.

20. Selecione **Avançar**.

21. Conecte o VHDX criado anteriormente à máquina virtual.

22. Selecione **Avançar**.

23. Examine o resumo e selecione **Concluir**.

24. Clique com o botão direito do mouse na nova máquina virtual e selecione **configurações**.

25. Selecione **adicionar hardware** e adicionar um novo adaptador para o **adaptador de rede**.

26. Para o **comutador virtual**, selecione a opção que se conectará à rede de gerenciamento de sensor.

27. Aloque recursos de CPU (de acordo com a arquitetura).

28. Conecte a imagem ISO do console de gerenciamento a uma unidade de DVD virtual.

29. Iniciar a máquina virtual.

30. No menu **ações** , selecione **conectar** para continuar a instalação do software.

### <a name="software-installation-esxi-and-hyper-v"></a>Instalação de software (ESXi e Hyper-V)

Iniciar a máquina virtual iniciará o processo de instalação da imagem ISO.

Para instalar o software:

1. Selecione **Inglês**.

2. Selecione a arquitetura necessária para sua implantação.

3. Defina a interface de rede para a rede de gerenciamento de sensor: interface, IP, sub-rede, servidor DNS e gateway padrão.

4. As credenciais de entrada são geradas e apresentadas automaticamente. Mantenha essas credenciais em um local seguro, pois elas são necessárias para a entrada e a administração.

  - **Suporte**: o usuário administrativo para gerenciamento de usuários.

  - **CyberX**: o equivalente de raiz para acessar o dispositivo.

5. O dispositivo é reiniciado.

6. Acesse o console de gerenciamento por meio do endereço IP configurado anteriormente: `<https://ip_address>` .

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Captura de tela que mostra a entrada do console de gerenciamento.":::

## <a name="post-installation-validation"></a>Validação pós-instalação

Para validar a instalação de um dispositivo físico, você precisa executar vários testes. O mesmo processo de validação se aplica a todos os tipos de dispositivo.

Execute a validação usando a GUI ou a CLI. A validação está disponível para o **suporte** ao usuário e o usuário **CyberX**.

A validação pós-instalação deve incluir os seguintes testes:

  - **Teste de sanidade**: Verifique se o sistema está em execução.

  - **Versão**: Verifique se a versão está correta.

  - **ifconfig**: Verifique se todas as interfaces de entrada configuradas durante o processo de instalação estão em execução.

### <a name="checking-system-health-by-using-the-gui"></a>Verificando a integridade do sistema usando a GUI

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Captura de tela que mostra a verificação de integridade do sistema.":::

#### <a name="sanity"></a>Sanity

- **Dispositivo**: executa a verificação de integridade do dispositivo. Você pode executar a mesma verificação usando o comando da CLI `system-sanity` .

- **Versão**: exibe a versão do dispositivo.

- **Propriedades de rede**: exibe os parâmetros de rede do sensor.

#### <a name="redis"></a>Redis

- **Memória**: fornece a imagem geral do uso de memória, como a quantidade de memória usada e o quanto permanecia.

- **Chave mais longa**: exibe as chaves mais longas que podem causar o uso extensivo da memória.

#### <a name="system"></a>Sistema

- **Log principal**: fornece as últimas 500 linhas do log principal, permitindo que você exiba as linhas de log recentes sem exportar todo o log do sistema.

- **Gerenciador de tarefas**: traduz as tarefas que aparecem na tabela de processos para as seguintes camadas: 
  
  - Camada persistente (Redis) 
  - Camada de caixa (SQL)

- **Estatísticas de rede**: exibe suas estatísticas de rede.

- **Superior**: mostra a tabela de processos. É um comando do Linux que fornece uma exibição dinâmica em tempo real do sistema em execução.

- **Verificação de memória de backup**: fornece o status da memória de backup, verificando o seguinte:
  - O local da pasta de backup 
  - O tamanho da pasta de backup
  - As limitações da pasta de backup
  - Quando o último backup ocorreu
  - Quanto espaço há para os arquivos de backup adicionais

- **ifconfig**: exibe os parâmetros para as interfaces físicas do dispositivo.

- **CyberX ando**: exibe o tráfego de rede e a largura de banda usando os testes de seis segundos.

- **Erros do núcleo, log**: exibe erros do arquivo de log principal.

Para acessar a ferramenta:

1. Entre no sensor com as credenciais do usuário de **suporte** .

2. Selecione **estatísticas do sistema** na janela **configurações do sistema** .

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>Verificando a integridade do sistema usando a CLI

**Teste 1: sanidade**

Verifique se o sistema está em execução:

1. Conecte-se à CLI com o terminal do Linux (por exemplo, disparador) e **suporte** ao usuário.

2. Insira `system sanity`.

3. Verifique se todos os serviços estão verdes (em execução).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Captura de tela que mostra os serviços em execução.":::

4. Verifique se o **sistema está ativo! (Prod)** aparece na parte inferior.

**Teste 2: verificação de versão**

Verifique se a versão correta é usada:

1. Conecte-se à CLI com o terminal do Linux (por exemplo, disparador) e **suporte** ao usuário.

2. Insira `system version`.

3. Verifique se a versão correta é exibida.

**Teste 3: validação de rede**

Verifique se todas as interfaces de entrada configuradas durante o processo de instalação estão em execução:

1. Conecte-se à CLI com o terminal do Linux (por exemplo, disparador) e **suporte** ao usuário.

2. Digite `network list` (o equivalente ao comando do Linux `ifconfig` ).

3. Valide se as interfaces de entrada necessárias são exibidas. Por exemplo, se duas NICs de cobre quádruplas estiverem instaladas, deverá haver 10 interfaces na lista.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Captura de tela que mostra a lista de interfaces.":::

**Teste 4: acesso de gerenciamento à interface do usuário**

Verifique se você pode acessar a GUI da Web do console:

1. Conecte um laptop com um cabo Ethernet à porta de gerenciamento (**Gb1**).

2. Defina o endereço NIC do laptop para estar no mesmo intervalo que o dispositivo.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Captura de tela que mostra o acesso de gerenciamento à interface do usuário.":::

3. Execute ping no endereço IP do dispositivo do laptop para verificar a conectividade (padrão: 10.100.10.1).

4. Abra o navegador Chrome no laptop e insira o endereço IP do dispositivo.

5. Na janela **sua conexão não é privada** , selecione **avançado** e prossiga.

6. O teste é bem-sucedido quando a tela de entrada do defender para IoT é exibida.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Captura de tela que mostra o acesso ao console de gerenciamento.":::

## <a name="troubleshooting"></a>Solução de problemas

### <a name="you-cant-connect-by-using-a-web-interface"></a>Você não pode se conectar usando uma interface da Web

1. Verifique se o computador que você está tentando conectar está na mesma rede que o dispositivo.

2. Verifique se a rede de GUI está conectada à porta de gerenciamento.

3. Execute ping no endereço IP do dispositivo. Se não houver ping:

   1. Conecte um monitor e um teclado ao dispositivo.

   1. Use o usuário e a senha de **suporte** para entrar.

   1. Use o comando `network list` para ver o endereço IP atual.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Captura de tela que mostra a lista de redes.":::

4. Se os parâmetros de rede estiverem configurados incorretamente, use o seguinte procedimento para alterá-los:

   1. Use o comando `network edit-settings` .

   1. Para alterar o endereço IP da rede de gerenciamento, selecione **Y**.

   1. Para alterar a máscara de sub-rede, selecione **Y**.

   1. Para alterar o DNS, selecione **Y**.

   1. Para alterar o endereço IP do gateway padrão, selecione **Y**.

   1. Para a alteração da interface de entrada (somente sensor), selecione **N**.

   1. Para aplicar as configurações, selecione **Y**.

5. Após a reinicialização, conecte-se com as credenciais de usuário de suporte e use o `network list` comando para verificar se os parâmetros foram alterados.

6. Tente executar o ping e se conectar novamente a partir da GUI.

### <a name="the-appliance-isnt-responding"></a>O dispositivo não está respondendo

1. Conecte um monitor e um teclado ao dispositivo ou use a saída para se conectar remotamente à CLI.

2. Use as credenciais do usuário de **suporte** para entrar.

3. Use o `system sanity` comando e verifique se todos os processos estão em execução.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Captura de tela que mostra o comando de sanidade do sistema.":::

Para quaisquer outros problemas, entre em contato com [suporte da Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>Apêndice A: porta de espelhamento no vSwitch (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>Configurar uma porta SPAN em um vSwitch existente

Um vSwitch não tem recursos de espelhamento, mas você pode usar uma solução alternativa simples para implementar uma porta SPAN.

Para configurar uma porta SPAN:

1. Abra as propriedades do vSwitch.

2. Selecione **Adicionar**.

3. Selecione a **máquina virtual**  >  **Avançar**.

4. Insira um rótulo de rede **span Network**, selecione **VLAN ID**  >  **All** e, em seguida, selecione **Next**.

5. Selecione **Concluir**.

6. Selecione **span Network** > **Editar*.

7. Selecione **segurança** e verifique se a política do **modo promíscuo** está definida como **aceitar** modo.

8. Selecione **OK** e, em seguida, selecione **fechar** para fechar as propriedades do vSwitch.

9. Abra as propriedades da **VM XSense** .

10. Para o **adaptador de rede 2**, selecione a rede **span** .

11. Selecione **OK**.

12. Conecte-se ao sensor e verifique se o espelhamento funciona.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>Apêndice B: sensores de acesso do console de gerenciamento local

Você pode aprimorar a segurança do sistema impedindo o acesso direto do usuário ao sensor. Em vez disso, use o túnel de proxy para permitir que os usuários acessem o sensor do console de gerenciamento local com uma única regra de firewall. Essa técnica restringe a possibilidade de acesso não autorizado ao ambiente de rede além do sensor. A experiência do usuário ao entrar no sensor permanece a mesma.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Captura de tela que mostra o acesso ao sensor.":::

Para habilitar o túnel:

1. Entre na CLI do console de gerenciamento local com **CyberX** ou **dê suporte** a credenciais de usuário.

2. Insira `sudo cyberx-management-tunnel-enable`.

3. Selecione **Enter**.

4. Insira `--port 10000`.

### <a name="next-steps"></a>Próximas etapas

[Configurar sua rede](how-to-set-up-your-network.md)