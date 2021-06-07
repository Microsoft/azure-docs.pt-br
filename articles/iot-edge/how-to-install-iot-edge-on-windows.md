---
title: Instalar o Azure IoT Edge para Linux no Windows | Microsoft Docs
description: Azure IoT Edge instruções de instalação em dispositivos Windows
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: v-tcassi
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 8b549d868aed443e19d639ba6f6df7db20e014b1
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105612093"
---
# <a name="install-and-provision-azure-iot-edge-for-linux-on-a-windows-device-preview"></a>Instalar e provisionar o Azure IoT Edge para Linux em um dispositivo Windows (versão prévia)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

O runtime do Azure IoT Edge é o que transforma um dispositivo em um dispositivo do IoT Edge. O tempo de execução pode ser implantado em dispositivos da classe PC para servidores industriais. Após um dispositivo ser configurado com o runtime do IoT Edge, você pode começar a implantar a lógica de negócios nele da nuvem. Para saber mais, confira [entender o tempo de execução de Azure IOT Edge e sua arquitetura](iot-edge-runtime.md).

Azure IoT Edge para Linux no Windows permite que você use Azure IoT Edge em dispositivos Windows usando máquinas virtuais do Linux. A versão Linux do Azure IoT Edge e os módulos do Linux implantados com ele são executados na máquina virtual. A partir daí, os aplicativos e o código do Windows e o tempo de execução de IoT Edge e os módulos podem interagir livremente entre si.

Este artigo lista as etapas para configurar o IoT Edge em um dispositivo Windows. Estas etapas implantam uma máquina virtual Linux que contém o tempo de execução de IoT Edge para executar em seu dispositivo Windows e, em seguida, provisionar o dispositivo com sua identidade de dispositivo do Hub IoT.

>[!NOTE]
>O IoT Edge para Linux no Windows está em [versão prévia pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
>Embora IoT Edge para Linux no Windows seja a experiência recomendada para usar Azure IoT Edge em um ambiente do Windows, os contêineres do Windows ainda estão disponíveis. Se você preferir usar contêineres do Windows, consulte o guia de instruções sobre como [instalar e gerenciar o Azure IOT Edge para Windows](how-to-install-iot-edge-windows-on-windows.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Azure com uma assinatura válida. Se você não tiver uma [assinatura do Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

* Um [Hub IoT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou da camada Standard no Azure.

* Um dispositivo Windows com os seguintes requisitos mínimos do sistema:

  * Windows 10 versão 1809 ou posterior; Build 17763 ou posterior
  * Edições Professional, Enterprise ou Server
  * Memória livre mínima: 2 GB
  * Mínimo de espaço livre em disco: 10 GB
  * Se você estiver criando uma nova implantação usando o Windows 10, certifique-se de habilitar o Hyper-V. Para obter mais informações, consulte como [instalar o Hyper-V no Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v.md).
  * Se você estiver criando uma nova implantação usando o Windows Server, certifique-se de instalar a função Hyper-V. Para obter mais informações, consulte como [instalar a função Hyper-V no Windows Server](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server.md).
  * Se você estiver criando uma nova implantação usando uma VM, certifique-se de configurar corretamente a virtualização aninhada. Para obter mais informações, consulte o guia de [virtualização aninhado](nested-virtualization.md) .

* Acesso ao centro de administração do Windows com a extensão de Azure IoT Edge para o centro de administração do Windows instalada:

   1. Baixe o [instalador do centro de administração do Windows](https://aka.ms/wacdownload).

   1. Execute o instalador baixado e siga os prompts do assistente de instalação para instalar o centro de administração do Windows. 

   1. Uma vez instalado, use um navegador com suporte para abrir o centro de administração do Windows. Os navegadores com suporte incluem o Microsoft Edge (Windows 10, versão 1709 ou posterior), Google Chrome e Microsoft Edge Insider.

   1. No primeiro uso do centro de administração do Windows, você será solicitado a selecionar um certificado a ser usado. Selecione o **cliente do centro de administração do Windows** como seu certificado.

   1. É hora de instalar a extensão de Azure IoT Edge. Selecione o ícone de engrenagem no canto superior direito do painel do centro de administração do Windows.

      ![Selecione o ícone de engrenagem no canto superior direito do painel para acessar as configurações.](./media/how-to-install-iot-edge-on-windows/select-gear-icon.png)

   1. No menu **configurações** , em **Gateway**, selecione **extensões**.

   1. Na guia **extensões disponíveis** , localize **Azure IOT Edge** na lista de extensões. Escolha-o e selecione o prompt de **instalação** acima da lista de extensões.

   1. Após a conclusão da instalação, você deverá ver Azure IoT Edge na lista de extensões instaladas na guia **extensões instaladas** .

## <a name="choose-your-provisioning-method"></a>Escolha o método de provisionamento

O Azure IoT Edge para Linux no Windows dá suporte aos seguintes métodos de provisionamento:

* Provisionamento manual usando a cadeia de conexão do dispositivo IoT Edge. Para usar esse método, Registre seu dispositivo e recupere uma cadeia de conexão usando as etapas em [registrar um dispositivo de IOT Edge no Hub IOT](how-to-register-device.md).
  * Escolha a opção de autenticação de chave simétrica, pois os certificados autoassinados X. 509 atualmente não têm suporte pelo IoT Edge para Linux no Windows.
* Provisionamento automático usando o DPS (serviço de provisionamento de dispositivos) e chaves simétricas. Saiba mais sobre como [criar e provisionar um dispositivo de IOT Edge com DPS e chaves simétricas](how-to-auto-provision-symmetric-keys.md).
* Provisionamento automático usando os certificados DPS e X. 509. Saiba mais sobre como [criar e provisionar um dispositivo IOT Edge com certificados DPS e X. 509](how-to-auto-provision-x509-certs.md).

O provisionamento manual é mais fácil de começar com alguns dispositivos. O serviço de provisionamento de dispositivos é útil para o provisionamento de vários dispositivos.

Se você planeja usar um dos métodos de DPS para provisionar seu dispositivo ou dispositivos, siga as etapas no artigo apropriado vinculado acima para criar uma instância do DPS, vincule sua instância do DPS ao Hub IoT e crie um registro de DPS. Você pode criar um *registro individual* para um único dispositivo ou um *registro de grupo* para um grupo de dispositivos. Para obter mais informações sobre os tipos de registro, visite os [conceitos do serviço de provisionamento de dispositivos no Hub IOT do Azure](../iot-dps/concepts-service.md#enrollment).

## <a name="create-a-new-deployment"></a>Criar uma nova implantação

Crie sua implantação do Azure IoT Edge para Linux no Windows em seu dispositivo de destino.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

Na página inicial do centro de administração do Windows, na lista de conexões, você verá uma conexão de host local que representa o PC em que você está executando o centro de administração do Windows. Quaisquer servidores, computadores ou clusters adicionais que você gerenciar também aparecerão aqui.

Você pode usar o centro de administração do Windows para fazer a instalação e o gerenciamento do Azure IoT Edge para Linux no Windows em seu dispositivo local ou em dispositivos remotos gerenciados. Neste guia, a conexão de host local funcionará como o dispositivo de destino para a implantação do Azure IoT Edge para Linux no Windows.

Se você quiser implantar em um dispositivo de destino remoto em vez de no dispositivo local e não vir o dispositivo de destino desejado na lista, siga as [instruções para adicionar o dispositivo.](/windows-server/manage/windows-admin-center/use/get-started#connecting-to-managed-nodes-and-clusters)

   ![Painel inicial do centro de administração do Windows com o dispositivo de destino listado](./media/how-to-install-iot-edge-on-windows/windows-admin-center-initial-dashboard.png)

1. Selecione **Adicionar**.

1. No painel **Adicionar ou criar recursos** , localize o bloco **Azure IOT Edge** . Selecione **criar novo** para instalar uma nova instância do Azure IOT Edge para Linux no Windows em um dispositivo.

   Se você já tiver IoT Edge para Linux no Windows em execução em um dispositivo, poderá selecionar **Adicionar** para se conectar a esse dispositivo IOT Edge existente e gerenciá-lo com o centro de administração do Windows.

   ![Selecione criar novo no bloco Azure IoT Edge no centro de administração do Windows](./media/how-to-install-iot-edge-on-windows/resource-creation-tiles.png)

1. O painel de **implantação criar um Azure IOT Edge para Linux no Windows** será aberto. No **1. Introdução** guia, verifique se o dispositivo de destino atende aos requisitos mínimos e selecione **Avançar**.

1. Examine os termos de licença, **marque aceito e selecione** **Avançar**.

1. Você pode ativar ou desativar **dados de diagnóstico opcionais** , dependendo de sua preferência.

1. Selecione **Avançar: implantar**.

   ![Selecione o botão Avançar: implantar depois de alternar dados de diagnóstico opcionais para sua preferência](./media/how-to-install-iot-edge-on-windows/select-next-deploy.png)

1. No **2. Guia implantar** , em **selecionar um dispositivo de destino**, clique em seu dispositivo listado para validá-lo atende aos requisitos mínimos. Quando seu status for confirmado como com suporte, selecione **Avançar**.

   ![Selecione seu dispositivo para verificar se há suporte](./media/how-to-install-iot-edge-on-windows/evaluate-supported-device.png)

1. Na guia **configurações do 2,2** , examine as definições de configuração de sua implantação. Quando estiver satisfeito com as configurações, selecione **Avançar**.

   ![Examinar as definições de configuração de sua implantação](./media/how-to-install-iot-edge-on-windows/default-deployment-configuration-settings.png)

   >[!NOTE]
   >Se você estiver usando uma máquina virtual do Windows, é recomendável usar um comutador padrão em vez de um comutador externo para garantir que a máquina virtual Linux criada na implantação possa obter um endereço IP.
   >
   >Usar um comutador padrão atribui a máquina virtual Linux um endereço IP interno. Esse endereço IP interno não pode ser acessado de fora da máquina virtual do Windows, mas pode ser conectado localmente enquanto estiver conectado à máquina virtual do Windows.
   >
   >Se você estiver usando o Windows Server, observe que o Azure IoT Edge para Linux no Windows não oferece suporte automaticamente à opção padrão. Para uma máquina virtual local do Windows Server, verifique se a máquina virtual Linux pode obter um endereço IP por meio do comutador externo. Para uma máquina virtual do Windows Server no Azure, configure um comutador interno antes de implantar o IoT Edge para Linux no Windows.

1. Na guia **implantação do 2,3** , você pode observar o progresso da implantação. O processo completo inclui baixar o Azure IoT Edge para Linux no pacote do Windows, instalar o pacote, configurar o dispositivo de host e configurar a máquina virtual Linux. Esse processo pode levar vários minutos para ser concluído. Uma implantação bem-sucedida está configurada abaixo.

   ![Uma implantação bem-sucedida mostrará cada etapa com uma marca de seleção verde e um rótulo ' Concluído '](./media/how-to-install-iot-edge-on-windows/successful-deployment.png)

Quando a implantação for concluída, você estará pronto para provisionar seu dispositivo. Selecione **Avançar: conectar** para prosseguir para o **3. Guia conectar** , que manipula Azure IOT Edge provisionamento de dispositivos.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Instale IoT Edge para Linux no Windows no dispositivo de destino, se ainda não tiver feito isso.

> [!NOTE]
> O processo do PowerShell a seguir descreve como criar uma implantação de host local do Azure IoT Edge para Linux no Windows. Para criar uma implantação em um dispositivo de destino remoto usando o PowerShell, você pode usar o [PowerShell remoto](/powershell/module/microsoft.powershell.core/about/about_remote) para estabelecer uma conexão com um dispositivo remoto e executar esses comandos remotamente nesse dispositivo.

1. Em uma sessão do PowerShell com privilégios elevados, execute cada um dos comandos a seguir para baixar IoT Edge para Linux no Windows.

   ```azurepowershell-interactive
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Instale o IoT Edge para Linux no Windows em seu dispositivo.

   ```azurepowershell-interactive
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   > [!NOTE]
   > Você pode especificar IoT Edge personalizado para Linux nos diretórios de instalação e VHDX do Windows adicionando os parâmetros INSTALLDIR = "<FULLY_QUALIFIED_PATH>" e VHDXDIR = "<FULLY_QUALIFIED_PATH>" ao comando de instalação acima.

1. Para que a implantação seja executada com êxito, você precisa definir a política de execução no dispositivo de destino como `AllSigned` se ainda não estiver. Você pode verificar a política de execução atual em um prompt do PowerShell com privilégios elevados usando:

   ```azurepowershell-interactive
   Get-ExecutionPolicy -List
   ```

   Se a política de execução do `local machine` não for `AllSigned` , você poderá definir a política de execução usando:

   ```azurepowershell-interactive
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Crie o IoT Edge para implantação do Linux no Windows.

   ```azurepowershell-interactive
   Deploy-Eflow
   ```

   > [!NOTE]
   > Você pode executar esse comando sem parâmetros ou, opcionalmente, personalizar a implantação com parâmetros. Você pode consultar [a referência de script do IOT Edge para Linux no Windows PowerShell](reference-iot-edge-for-linux-on-windows-scripts.md#deploy-eflow) para ver os significados de parâmetro e os valores padrão.

1. Insira ' Y ' para aceitar os termos de licença.

1. Insira ' O ' ou ' R ' para ativar ou desativar **dados de diagnóstico opcionais** , dependendo de sua preferência. Uma implantação bem-sucedida está configurada abaixo.

   ![Uma implantação bem-sucedida informará ' implantação bem-sucedida ' no final das mensagens](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment.png)

Quando a implantação for concluída, você estará pronto para provisionar seu dispositivo.

---

Para provisionar seu dispositivo, você pode seguir os links abaixo para ir para a seção para o método de provisionamento selecionado:

* [Opção 1: provisionamento manual usando a cadeia de conexão do dispositivo IoT Edge](#option-1-provisioning-manually-using-the-connection-string)
* [Opção 2: provisionamento automático usando o DPS (serviço de provisionamento de dispositivos) e chaves simétricas](#option-2-provisioning-via-dps-using-symmetric-keys)
* [Opção 3: provisionamento automático usando os certificados DPS e X. 509](#option-3-provisioning-via-dps-using-x509-certificates)

## <a name="provision-your-device"></a>Provisionar seu dispositivo

Escolha um método para provisionar seu dispositivo e siga as instruções na seção apropriada. Você pode usar o centro de administração do Windows ou uma sessão do PowerShell com privilégios elevados para provisionar seus dispositivos.

### <a name="option-1-provisioning-manually-using-the-connection-string"></a>Opção 1: provisionamento manual usando a cadeia de conexão

Esta seção aborda o provisionamento manual de seu dispositivo usando a cadeia de conexão do dispositivo Azure IoT Edge.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. No painel **provisionamento de dispositivos Azure IOT Edge** , selecione cadeia de **conexão (manual)** no menu suspenso método de provisionamento.

1. Na [portal do Azure](https://ms.portal.azure.com/), navegue até a guia **IOT Edge** do Hub IOT.

1. Clique na ID do dispositivo do seu dispositivo. Copie o campo **cadeia de conexão primária** .

1. Cole-o no campo cadeia de conexão do dispositivo no centro de administração do Windows. Em seguida, escolha **provisionamento com o método selecionado**.

   ![Escolha provisionamento com o método selecionado depois de colar a cadeia de conexão do dispositivo](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-connection-string.png)

1. Quando o provisionamento for concluído, selecione **concluir**. Você será levado de volta ao painel principal. Agora, você deve ver um novo dispositivo listado, cujo tipo é `IoT Edge Devices` . Você pode selecionar o dispositivo IoT Edge para se conectar a ele. Uma vez em sua página de **visão geral** , você pode exibir a **lista de módulos IOT Edge** e **IOT Edge status** do seu dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Na [portal do Azure](https://ms.portal.azure.com/), navegue até a guia **IOT Edge** do Hub IOT.

1. Clique na ID do dispositivo do seu dispositivo. Copie o campo **cadeia de conexão primária** .

1. Cole o texto do espaço reservado no comando a seguir e execute-o em uma sessão do PowerShell com privilégios elevados em seu dispositivo de destino.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType manual -devConnString "<CONNECTION_STRING_HERE>"
   ```

---

### <a name="option-2-provisioning-via-dps-using-symmetric-keys"></a>Opção 2: provisionamento via DPS usando chaves simétricas

Esta seção aborda o provisionamento automático de seu dispositivo usando as chaves de DPS e simétrica.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. No painel **provisionamento de dispositivos Azure IOT Edge** , selecione **chave simétrica (DPS)** no menu suspenso método de provisionamento.

1. Na [portal do Azure](https://ms.portal.azure.com/), navegue até a instância do DPS.

1. Na guia **visão geral** , copie o valor de **escopo da ID** . Cole-o no campo ID do escopo no centro de administração do Windows.

1. Na guia **gerenciar** registros na portal do Azure, selecione o registro que você criou. Copie o valor da **chave primária** nos detalhes do registro. Cole-o no campo chave simétrica no centro de administração do Windows.

1. Forneça a ID de registro do seu dispositivo no campo ID de registro no centro de administração do Windows.

1. Escolha **provisionamento com o método selecionado**.

   ![Escolha provisionamento com o método selecionado depois de preencher os campos obrigatórios para o provisionamento de chave simétrica](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-symmetric-key.png)

1. Quando o provisionamento for concluído, selecione **concluir**. Você será levado de volta ao painel principal. Agora, você deve ver um novo dispositivo listado, cujo tipo é `IoT Edge Devices` . Você pode selecionar o dispositivo IoT Edge para se conectar a ele. Uma vez em sua página de **visão geral** , você pode exibir a **lista de módulos IOT Edge** e **IOT Edge status** do seu dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Copie o seguinte comando em um editor de texto. Substitua o texto do espaço reservado pelas suas informações conforme detalhado.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType symmetric -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -symmKey <PRIMARY_KEY_HERE>
   ```

1. Na [portal do Azure](https://ms.portal.azure.com/), navegue até a instância do DPS.

1. Na guia **visão geral** , copie o valor de **escopo da ID** . Cole-o sobre o texto do espaço reservado apropriado no comando.

1. Na guia **gerenciar** registros na portal do Azure, selecione o registro que você criou. Copie o valor da **chave primária** nos detalhes do registro. Cole-o sobre o texto do espaço reservado apropriado no comando.

1. Forneça a ID de registro do dispositivo para substituir o texto do espaço reservado apropriado no comando.

1. Execute o comando em uma sessão do PowerShell com privilégios elevados no dispositivo de destino.

---

### <a name="option-3-provisioning-via-dps-using-x509-certificates"></a>Opção 3: provisionamento por meio de DPS usando certificados X. 509

Esta seção aborda o provisionamento automático de seu dispositivo usando certificados DPS e X. 509.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. No painel **provisionamento de dispositivos Azure IOT Edge** , selecione o **certificado X. 509 (DPS)** no menu suspenso método de provisionamento.

1. Na [portal do Azure](https://ms.portal.azure.com/), navegue até a instância do DPS.

1. Na guia **visão geral** , copie o valor de **escopo da ID** . Cole-o no campo ID do escopo no centro de administração do Windows.

1. Forneça a ID de registro do seu dispositivo no campo ID de registro no centro de administração do Windows.

1. Carregue seu certificado e os arquivos de chave privada.

1. Escolha **provisionamento com o método selecionado**.

   ![Escolha provisionamento com o método selecionado depois de preencher os campos obrigatórios para o provisionamento de certificado X. 509](./media/how-to-install-iot-edge-on-windows/provisioning-with-selected-method-x509-certs.png)

1. Quando o provisionamento for concluído, selecione **concluir**. Você será levado de volta ao painel principal. Agora, você deve ver um novo dispositivo listado, cujo tipo é `IoT Edge Devices` . Você pode selecionar o dispositivo IoT Edge para se conectar a ele. Uma vez em sua página de **visão geral** , você pode exibir a **lista de módulos IOT Edge** e **IOT Edge status** do seu dispositivo.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Copie o seguinte comando em um editor de texto. Substitua o texto do espaço reservado pelas suas informações conforme detalhado.

   ```azurepowershell-interactive
   Provision-EflowVm -provisioningType x509 -scopeId <ID_SCOPE_HERE> -registrationId <REGISTRATION_ID_HERE> -identityCertLocWin <ABSOLUTE_CERT_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityPkLocWin <ABSOLUTE_PRIVATE_KEY_SOURCE_PATH_ON_WINDOWS_MACHINE> -identityCertLocVm <ABSOLUTE_CERT_DEST_PATH_ON_LINUX_MACHINE -identityPkLocVm <ABSOLUTE_PRIVATE_KEY_DEST_PATH_ON_LINUX_MACHINE>
   ```

1. Na [portal do Azure](https://ms.portal.azure.com/), navegue até a instância do DPS.

1. Na guia **visão geral** , copie o valor de **escopo da ID** . Cole-o sobre o texto do espaço reservado apropriado no comando.

1. Forneça a ID de registro do dispositivo para substituir o texto do espaço reservado apropriado no comando.

1. Substitua o texto do espaço reservado apropriado pelo caminho de origem absoluto para o arquivo de certificado.

1. Substitua o texto do espaço reservado apropriado pelo caminho de origem absoluto para o arquivo de chave privada.

1. Execute o comando em uma sessão do PowerShell com privilégios elevados no dispositivo de destino.

---

## <a name="verify-successful-configuration"></a>Verificar configuração bem-sucedida

Verifique se o IoT Edge para Linux no Windows foi instalado e configurado com êxito em seu dispositivo IoT Edge.

# <a name="windows-admin-center"></a>[Windows Admin Center](#tab/windowsadmincenter)

1. Selecione o dispositivo IoT Edge na lista de dispositivos conectados no centro de administração do Windows para se conectar a ele.

1. A página Visão geral do dispositivo exibe algumas informações sobre o dispositivo:

    1. A seção lista de módulos de **IOT Edge** mostra módulos em execução no dispositivo. Quando o serviço de IoT Edge é iniciado pela primeira vez, você só deve ver o módulo **edgeAgent** em execução. O módulo edgeAgent é executado por padrão e ajuda a instalar e a iniciar quaisquer módulos adicionais que você implante em seu dispositivo.
    1. A seção **status da IOT Edge** mostra o status do serviço e deve ser relatando **ativo (em execução)**.

1. Se você precisar solucionar problemas do serviço de IoT Edge, use a ferramenta **Shell de comando** na página do dispositivo para SSH (Secure Shell) na máquina virtual e execute os comandos do Linux.

    1. Se você precisar solucionar problemas do serviço, recupere os logs de serviço.

       ```bash
       journalctl -u iotedge
       ```

    2. Use a `check` ferramenta para verificar a configuração e o status de conexão do dispositivo.

       ```bash
       sudo iotedge check
       ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Faça logon em seu IoT Edge para Linux na máquina virtual do Windows usando o seguinte comando em sua sessão do PowerShell:

   ```azurepowershell-interactive
   Ssh-EflowVm
   ```

   >[!NOTE]
   >A única conta permitida para o SSH para a máquina virtual é o usuário que a criou.

1. Depois de fazer logon, você pode verificar a lista de módulos de IoT Edge em execução usando o seguinte comando do Linux:

   ```bash
   iotedge list
   ```

1. Se você precisar solucionar problemas do serviço de IoT Edge, use os comandos do Linux a seguir.

    1. Se você precisar solucionar problemas do serviço, recupere os logs de serviço.

       ```bash
       journalctl -u iotedge
       ```

    2. Use a `check` ferramenta para verificar a configuração e o status de conexão do dispositivo.

       ```bash
       sudo iotedge check
       ```

---

## <a name="next-steps"></a>Próximas etapas

* Continue a [implantar módulos IOT Edge](how-to-deploy-modules-portal.md) para aprender a implantar módulos em seu dispositivo.
* Saiba como [gerenciar certificados em seu IOT Edge para Linux na máquina virtual do Windows](how-to-manage-device-certificates.md) e transferir arquivos do sistema operacional host para sua máquina virtual Linux.
* Saiba como [configurar seus dispositivos IOT Edge para se comunicar por meio de um servidor proxy](how-to-configure-proxy-support.md).
