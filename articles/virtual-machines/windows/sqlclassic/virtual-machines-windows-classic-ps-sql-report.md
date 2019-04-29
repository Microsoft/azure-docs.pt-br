---
title: Usar o PowerShell para criar uma VM com um servidor de relatório no modo nativo | Microsoft Docs
description: 'Este tópico descreve e fornece orientação para a implantação e a configuração de um servidor de relatório em modo nativo do SQL Server Reporting Services em uma Máquina Virtual do Azure. '
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 553af55b-d02e-4e32-904c-682bfa20fa0f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: 6339b49d0bc9c635457f305dad7b1a075327a1dd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60609949"
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Use o PowerShell para criar uma VM do Azure com um servidor de relatório em modo nativo
> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Resource Manager e clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos.

Este tópico descreve e fornece orientação para a implantação e a configuração de um servidor de relatório em modo nativo do SQL Server Reporting Services em uma Máquina Virtual do Azure. As etapas neste documento usam uma combinação de etapas manuais para criar a máquina virtual e um script do Windows PowerShell para configurar o Reporting Services na VM. O script de configuração inclui a abertura de uma porta de firewall para HTTP ou HTTPs.

> [!NOTE]
> Se você não precisar de **HTTPS** no servidor de relatório, **ignorar a etapa 2**.
> 
> Depois de criar a VM na etapa 1, vá até a seção Usar o script para configurar o servidor de relatório e HTTP. Após a execução do script, o servidor de relatório estará pronto para ser usado.

## <a name="prerequisites-and-assumptions"></a>Pré-requisitos e suposições
* **Assinatura do Azure**: verifique o número de núcleos disponíveis em sua Assinatura do Azure. Se você criar o tamanho recomendado de VM, **A3**, precisará de **4** núcleos disponíveis. Se você usar um tamanho de VM **A2**, precisará de **2** núcleos disponíveis.
  
  * Para verificar o limite de núcleos de sua assinatura, no portal do Azure, clique em CONFIGURAÇÕES no painel esquerdo e clique em USO no menu superior.
  * Para aumentar a cota de núcleos, entre em contato com o [Suporte do Azure](https://azure.microsoft.com/support/options/). Para saber mais sobre o tamanho da VM, consulte [Tamanhos de máquinas virtuais do Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* **Script do Windows PowerShell**: o tópico supõe que você tenha conhecimento prático básico do Windows PowerShell. Para saber mais sobre como usar o Windows PowerShell, consulte o seguinte:
  
  * [Iniciando o Windows PowerShell no Windows Server](https://docs.microsoft.com/powershell/scripting/setup/starting-windows-powershell)
  * [Introdução ao Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Etapa 1: provisionar uma máquina virtual do Azure
1. Navegue até o portal do Azure.
2. Clique em **Máquinas Virtuais** no painel esquerdo.
   
    ![máquinas virtuais do microsoft azure](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. Clique em **Novo**.
   
    ![botão novo](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. Clique em **Da Galeria**.
   
    ![nova vm da galeria](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. Clique em **SQL Server 2014 RTM Standard – Windows Server 2012 R2** e clique na seta para continuar.
   
    ![Próximo](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Se você precisar do recurso de assinaturas voltadas para dados do Reporting Services, escolha **SQL Server 2014 RTM Enterprise – Windows Server 2012 R2**. Para saber mais sobre as edições do SQL Server e o suporte dos recursos, consulte [Recursos com Suporte das Edições do SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).
6. Na página **Configuração da máquina virtual** , edite os seguintes campos:
   
   * Se houver mais de uma **DATA DE LANÇAMENTO DA VERSÃO**, selecione a versão mais recente.
   * **Nome da máquina virtual**: o nome da máquina também é usado na próxima página de configuração como o nome DNS do Serviço de Nuvem padrão. O nome DNS deve ser exclusivo em todo o serviço do Azure. Considere a configuração da VM com um nome de computador que descreva a utilização da VM. Por exemplo, ssrsnativecloud.
   * **Camada**: Standard
   * **Tamanho:A3** é o tamanho recomendado da VM para as cargas de trabalho do SQL Server. Se uma VM for usada apenas como um servidor de relatório, o tamanho de VM A2 será suficiente, a menos que o servidor de relatório enfrente uma grande carga de trabalho. Para saber mais sobre preços da VM, consulte [Preços das Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/).
   * **Novo Nome de Usuário**: o nome fornecido é criado como um administrador na VM.
   * **Nova Senha** e **Confirmar**. Essa senha será usada para a nova conta de administrador, portanto, recomendamos o uso de uma senha forte.
   * Clique em **Avançar**. ![next](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. Na próxima página edite os campos a seguir:
   
   * **Serviço de Nuvem**: selecione **Criar um novo Serviço de Nuvem**.
   * **Nome DNS do Serviço de Nuvem**: é o nome DNS público do Serviço de Nuvem associado à VM. O nome padrão é o nome que você digitou para a VM. Se em etapas posteriores do tópico você criar um certificado SSL confiável e o nome DNS for usado para o valor de "**Emitido para**" do certificado.
   * **Região/Grupo de Afinidade/Rede Virtual**: escolha a região mais próxima de seus usuários finais.
   * **Conta de Armazenamento**: Use uma conta de armazenamento gerada automaticamente.
   * **Conjunto de disponibilidade**: Nenhuma.
   * **PONTOS DE EXTREMIDADE**: mantenha os pontos de extremidade **Área de Trabalho Remota** e **PowerShell** e adicione o ponto de extremidade HTTP ou HTTPS, dependendo de seu ambiente.
     
     * **HTTP**: as portas pública e privada padrão são **80**. Se você usar uma porta privada diferente de 80, modifique **$HTTPport = 80** no script http.
     * **HTTPS**: as portas pública e privada padrão são **443**. Uma prática recomendada de segurança é alterar a porta privada e configurar o firewall e o servidor de relatório para usar a porta privada. Para saber mais sobre os pontos de extremidade, consulte [Como Configurar a Comunicação com uma Máquina Virtual](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Se você usar uma porta diferente da 443, altere o parâmetro **$HTTPsport = 443** no script HTTPS.
   * Clique em Próximo. ![Próximo](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. Na última página do assistente, mantenha o padrão **Instalar o agente de VM** selecionado. As etapas neste tópico não utilizam o agente de VM, mas se você planeja manter essa VM, o agente de VM e as extensões permitirão o aprimoramento da VM.  Para saber mais sobre o agente de VM, consulte [Agente de VM e Extensões – Parte 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). Uma das extensões padrão instaladas e em execução é a “BGINFO”, que exibe na área de trabalho da VM informações sobre o sistema, por exemplo, o IP interno e o espaço disponível na unidade.
9. Clique em Concluído. ![Ok](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. O **Status** da VM é exibido como **Iniciando (Provisionando)** durante o processo de provisionamento. Em seguida, é exibido como **Executando** quando a VM é provisionada e está pronta para ser usada.

## <a name="step-2-create-a-server-certificate"></a>Etapa 2: criar um certificado do servidor
> [!NOTE]
> Se você não exigir o HTTPS no servidor de relatório, poderá **ignorar a etapa 2** e ir para a seção **Usar o script para configurar o servidor de relatório e HTTP**. Use o script HTTP para configurar rapidamente o servidor de relatório e deixá-lo pronto para uso.

Para usar HTTPS na VM, será necessário um certificado SSL confiável. Dependendo do cenário, você poderá usar um dos dois métodos a seguir:

* Um certificado SSL válido emitido por uma Autoridade de Certificação (CA) e de confiança da Microsoft. Exige-se que os certificados raiz de CA sejam distribuídos por meio do Microsoft Root Certificate Program. Para saber mais sobre esse programa, consulte [SSL Root Certificate Program (CAs membros) do Windows](https://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) e do [Windows Phone 8 e Introdução ao Microsoft Root Certificate Program](https://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).
* Um certificado autoassinado. Os certificados autoassinados não são recomendados para ambientes de produção.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Para usar um certificado criado por uma Autoridade de Certificação (CA) confiável
1. **Solicite um certificado do servidor para o site em uma autoridade de certificação**. 
   
    Você pode usar o Assistente de Certificado de Servidor Web para gerar um arquivo de solicitação de certificado (Certreq.txt) e enviá-lo a uma autoridade de certificação ou para gerar uma solicitação para uma autoridade de certificação online. Por exemplo, os Serviços de Certificados da Microsoft no Windows Server 2012. Dependendo do nível de garantia de identificação oferecido por seu certificado de servidor, talvez demore alguns dias até vários meses para que a autoridade de certificação aprove sua solicitação e envie um arquivo de certificado. 
   
    Para saber mais sobre como solicitar certificados de servidor, consulte o seguinte: 
   
   * Use [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
   * Ferramentas de segurança para administrar o Windows Server 2012.
     
     [Ferramentas de segurança para administrar o Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > O campo **emitido para** do certificado SSL confiável deve ser igual ao **NOME DNS do Serviço de Nuvem** usado para a nova VM.

2. **Instale o certificado do servidor no servidor Web**. Nesse caso, o servidor Web é a VM que hospeda o servidor de relatório, e o site é criado em etapas posteriores durante a configuração do Reporting Services. Para saber mais sobre como instalar o certificado do servidor no servidor Web usando o snap-in do MMC de Certificados, consulte [Instalar um certificado de servidor](https://technet.microsoft.com/library/cc740068).
   
    Se você quiser usar o script incluído neste tópico para configurar o servidor de relatório, o valor de **impressão digital** dos certificados será exigido como um parâmetro do script. Consulte a próxima seção para obter detalhes sobre como obter a impressão digital do certificado.
3. Atribua o certificado do servidor ao servidor de relatório. A atribuição será concluída na próxima seção, após a configuração do servidor de relatório.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Para usar o certificado autoassinado de máquinas virtuais
Um certificado autoassinado foi criado na VM quando a VM foi provisionada. O certificado tem o mesmo nome que o nome DNS da VM. Para evitar erros de certificado, é necessário que o certificado seja de confiança na própria VM e também de todos os usuários do site.

1. Para confiar na CA raiz do certificado na VM Local, adicione o certificado às **Autoridades de Certificação Raiz Confiáveis**. A seguir, um resumo dos métodos exigidos. Para obter etapas detalhadas sobre como confiar na CA, consulte [Instalar um Certificado do Servidor](https://technet.microsoft.com/library/cc740068).
   
   1. No portal do Azure, selecione a VM e clique em conectar. Dependendo da configuração do navegador, talvez seja necessário salvar um arquivo .rdp para conectar-se à VM.
      
       ![conectar-se à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)  Use o nome de usuário da VM, o nome de usuário e a senha que você configurou na criação da VM. 
      
       Por exemplo, na imagem a seguir, o nome da VM é **ssrsnativecloud** e o nome de usuário é **testuser**.
      
       ![logon inclui o nome da vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Execute mmc.exe. Para obter mais informações, confira [Como: exibir certificados com o Snap-in do MMC](https://msdn.microsoft.com/library/ms788967.aspx).
   3. No menu **Arquivo** do aplicativo do console, adicione o snap-in **Certificados**, selecione **Conta de Computador** quando solicitado e clique em **Próximo**.
   4. Selecione **Computador Local** para gerenciar e clique em **Concluir**.
   5. Clique em **Ok**, expanda os nós **Certificados - Pessoal** e clique em **Certificados**. O certificado recebe o nome com base no nome DNS da VM e termina com **cloudapp.net**. Clique com o botão direito do mouse no nome do certificado e clique em **Copiar**.
   6. Expanda o nó **Autoridades de Certificação Raiz Confiáveis**, clique com botão direito do mouse em **Certificados** e clique em **Colar**.
   7. Para validar, clique duas vezes no nome do certificado em **Autoridades de Certificação Raiz Confiáveis** , verifique se não há erros e veja seu certificado. Se você quiser usar o script HTTPS incluído neste tópico para configurar o servidor de relatório, o valor da **Impressão digital** dos certificados será exigido como um parâmetro do script. **Para obter o valor da impressão digital**, preencha o seguinte. Também há um exemplo do PowerShell para recuperar a impressão digital na seção [Usar o script para configurar o servidor de relatório e HTTPS](#use-script-to-configure-the-report-server-and-https).
      
      1. Clique duas vezes no nome do certificado, por exemplo, ssrsnativecloud.cloudapp.net.
      2. Clique na guia **Detalhes** .
      3. Clique em **Impressão digital**. O valor da impressão digital é exibido no campo detalhes, por exemplo, ‎a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c fb 2f.
      4. Copie a impressão digital e salve o valor para mais tarde ou edite o script agora.
      5. (*) Antes de executar o script, remova os espaços entre os pares de valores. Por exemplo, a impressão digital observada anteriormente agora seria ‎a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
      6. Atribua o certificado do servidor ao servidor de relatório. A atribuição será concluída na próxima seção, após a configuração do servidor de relatório.

Se você estiver usando um certificado SSL autoassinado, o nome no certificado já corresponderá ao nome de host da VM. Portanto, o DNS da máquina já estará registrado globalmente e poderá ser acessado de qualquer cliente.

## <a name="step-3-configure-the-report-server"></a>Etapa 3: configurar servidor de relatório
Esta seção descreve a configuração da VM como um servidor de relatório em modo nativo do Reporting Services. Você pode usar um dos métodos a seguir para configurar o servidor de relatório:

* Usar o script para configurar o servidor de relatório
* Usar o Gerenciador de Configuração para configurar o servidor de relatório.

Para obter etapas mais detalhadas, consulte a seção [Conectar a Máquina Virtual e Iniciar o Gerenciador de Configuração do Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Observação de autenticação:** a autenticação do Windows é o método de autenticação recomendado e é a autenticação padrão do Reporting Services. Somente os usuários configurados na VM podem acessar o Reporting Services e podem receber as funções do Reporting Services.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Usar o script para configurar o servidor de relatório e HTTP
Para usar o script do Windows PowerShell a fim de configurar o servidor de relatório, conclua as etapas a seguir. A configuração inclui HTTP, não HTTPS:

1. No portal do Azure, selecione a VM e clique em conectar. Dependendo da configuração do navegador, talvez seja necessário salvar um arquivo .rdp para conectar-se à VM.
   
    ![conectar-se à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)  Use o nome de usuário da VM, o nome de usuário e a senha que você configurou na criação da VM. 
   
    Por exemplo, na imagem a seguir, o nome da VM é **ssrsnativecloud** e o nome de usuário é **testuser**.
   
    ![logon inclui o nome da vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Na VM, abra **ISE do Windows PowerShell** com privilégios administrativos. O ISE do PowerShell está instalado por padrão no Windows Server 2012. Recomendamos o uso do ISE, em vez de uma janela padrão do Windows PowerShell, para que você possa colar o script no ISE, modificar e executar o script.
3. No ISE do Windows PowerShell, clique no menu **Exibir** e clique em **Mostrar Painel de Script**.
4. Copie o script a seguir e cole-o no painel de script do ISE do Windows PowerShell.
   
        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
   
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
   
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Report Server Configuration Steps
   
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
   
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
   
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
5. Se você criou a VM com uma porta HTTP diferente de 80, modifique o parâmetro $HTTPport = 80.
6. Atualmente, o script está configurado para o Reporting Services. Se você quiser executar o script do Reporting Services, modifique a parte da versão do caminho até o namespace para "v11", na instrução Get-WmiObject.
7. Execute o script.

**Validação**: para verificar se a funcionalidade básica do servidor de relatório está funcionando, confira a seção [Verificar a configuração](#verify-the-configuration) mais adiante neste tópico.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Usar o script para configurar o servidor de relatório e HTTPS
Para usar o Windows PowerShell a fim de configurar o servidor de relatório, conclua as etapas a seguir. A configuração inclui HTTPS, não HTTP.

1. No portal do Azure, selecione a VM e clique em conectar. Dependendo da configuração do navegador, talvez seja necessário salvar um arquivo .rdp para conectar-se à VM.
   
    ![conectar-se à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)  Use o nome de usuário da VM, o nome de usuário e a senha que você configurou na criação da VM. 
   
    Por exemplo, na imagem a seguir, o nome da VM é **ssrsnativecloud** e o nome de usuário é **testuser**.
   
    ![logon inclui o nome da vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. Na VM, abra **ISE do Windows PowerShell** com privilégios administrativos. O ISE do PowerShell está instalado por padrão no Windows Server 2012. Recomendamos o uso do ISE, em vez de uma janela padrão do Windows PowerShell, para que você possa colar o script no ISE, modificar e executar o script.
3. Para habilitar a execução de scripts, execute o seguinte comando do Windows PowerShell:
   
        Set-ExecutionPolicy RemoteSigned
   
    Em seguida, você pode executar o seguinte procedimento para verificar a política:
   
        Get-ExecutionPolicy
4. No ISE do **Windows PowerShell**, clique no menu **Exibir** e clique em **Mostrar Painel de Script**.
5. Copie o script a seguir e cole-o no painel de script do ISE do Windows PowerShell.
   
        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
   
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
   
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Reporting Services Report Server Configuration Steps
   
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
   
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
   
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## 3. Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
   
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
   
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
6. Modifique o parâmetro **$certificatehash** no script:
   
   * Esse é um parâmetro **obrigatório** . Se você não tiver salvo o valor do certificado nas etapas anteriores, use um dos métodos a seguir para copiar o valor de hash do certificado da impressão digital dos certificados:
     
       Na VM, abra o ISE do Windows PowerShell e execute o seguinte comando:
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       A saída parecerá com o seguinte: Se o script retornar uma linha em branco, a VM não terá um certificado configurado, por exemplo. Consulte a seção [Para usar o Certificado Autoassinado das Máquinas Virtuais](#to-use-the-virtual-machines-self-signed-certificate).
     
     OU
   * Na VM, execute mmc.exe e adicione o snap-in **Certificados** .
   * Sob o nó **Autoridades de Certificação Raiz Confiáveis** , clique duas vezes no nome do certificado. Se você estiver usando o certificado autoassinado da VM, o certificado receberá o nome com base no nome DNS da VM e terminará com **cloudapp.net**.
   * Clique na guia **Detalhes** .
   * Clique em **Impressão digital**. O valor da impressão digital é exibido no campo detalhes, por exemplo, af 11 60 b6 4b 28 8d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
   * **Antes de executar o script**, remova os espaços entre os pares de valores. Por exemplo, af1160b64b288d890a8212ff6ba9c3664f319048
7. Modifique o parâmetro **$httpsport** : 
   
   * Se você usou a porta 443 para o ponto de extremidade HTTPS, não será necessário atualizar esse parâmetro no script. Caso contrário, use o valor de porta selecionado quando você configurou o ponto de extremidade HTTPS privado na VM.
8. Modifique o parâmetro **$DNSName** : 
   
   * O script é configurado para obter um certificado curinga $DNSName = "+". Se você não quiser configurar uma associação de certificado curinga, comente $DNSName ="+"e habilite a linha a seguir, a referência completa de $DNSNAme, ##$DNSName="$server.cloudapp.net".
     
       Altere o valor de $DNSName se não quiser usar o nome DNS da máquina virtual para o Reporting Services. Se você usar o parâmetro, o certificado também deverá usar esse nome, e será necessário registrar o nome globalmente em um servidor DNS.
9. Atualmente, o script está configurado para o Reporting Services. Se você quiser executar o script do Reporting Services, modifique a parte da versão do caminho até o namespace para "v11", na instrução Get-WmiObject.
10. Execute o script.

**Validação**: para verificar se a funcionalidade básica do servidor de relatório está funcionando, confira a seção Verificar a configuração mais adiante neste tópico. Para verificar a associação do certificado, abra um prompt de comando com privilégios administrativos e execute o seguinte comando:

    netsh http show sslcert

O resultado incluirá o seguinte:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Usar o Gerenciador de Configuração para configurar o servidor de relatório
Se você não quiser executar o script do PowerShell para configurar o servidor de relatório, execute as etapas nesta seção para usar o gerenciador de configuração em modo nativo do Reporting Services para configurar o servidor de relatório.

1. No portal do Azure, selecione a VM e clique em conectar. Use o nome de usuário e a senha configurados durante a criação da VM.
   
    ![conectar-se à máquina virtual do azure](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Execute a atualização do Windows e instale as atualizações da VM. Se for necessário reinicializar a VM, reinicie e reconecte-se à VM no portal do Azure.
3. No menu Iniciar da VM, digite **Reporting Services** e abra o **Gerenciador de Configuração do Reporting Services**.
4. Mantenha os valores padrão para **Nome do Servidor** e **Instância do Servidor de Relatório**. Clique em **Conectar**.
5. No painel esquerdo, clique em **URL do Serviço Web**.
6. Por padrão, o RS está configurado para a porta HTTP 80 com IP "Todos Atribuídos". Para adicionar HTTPS:
   
   1. Em **Certificado SSL**: selecione o certificado que você deseja usar, por exemplo, [nome da VM].cloudapp.net. Se nenhum certificado estiver listado, confira a seção **Etapa 2: criar um certificado do servidor** para obter informações sobre como instalar e confiar no certificado na VM.
   2. Em **Porta SSL**: escolha 443. Se você tiver configurado o ponto de extremidade HTTPS privado na VM com uma porta privada diferente, use esse valor aqui.
   3. Clique em **Aplicar** e aguarde a conclusão da operação.
7. No painel esquerdo, clique em **Banco de Dados**.
   
   1. Clique em **Alterar Banco de Dado**s.
   2. Clique em **Criar um novo banco de dados do servidor de relatório** e clique em **Próximo**.
   3. Mantenha o **Nome do Servidor** padrão: como o nome da VM e mantenha o **Tipo de Autenticação** padrão como **Usuário Atual** – **Segurança Integrada**. Clique em **Avançar**.
   4. Mantenha o **Nome do Banco de Dados** padrão como **ReportServer** e clique em **Próximo**.
   5. Mantenha o **Tipo de Autenticação** padrão como **Credenciais do Serviço** e clique em **Próximo**.
   6. Clique em **Próximo** on the **Resumo** .
   7. Quando a configuração estiver concluída, clique em **Concluir**.
8. No painel esquerdo, clique em **URL do Gerenciador de Relatórios**. Mantenha o **Diretório Virtual** padrão como **Relatórios** e clique em **Aplicar**.
9. Clique em **Sair** para fechar o Gerenciador de Configuração do Reporting Services.

## <a name="step-4-open-windows-firewall-port"></a>Etapa 4: abrir a porta do Firewall do Windows
> [!NOTE]
> Se você tiver usado um dos scripts para configurar o servidor de relatório, ignore esta seção. O script incluiu uma etapa para abrir a porta do firewall. A porta padrão era 80 para HTTP e 443 para HTTPS.
> 
> 

Para se conectar remotamente ao Gerenciador de Relatórios ou ao Servidor de Relatório na máquina virtual, será necessário ter um ponto de extremidade TCP na VM. É necessário abrir a mesma porta no firewall da VM. O ponto de extremidade foi criado durante o provisionamento da VM.

Esta seção fornece informações básicas sobre como abrir a porta do firewall. Para saber mais, consulte [Configurar um firewall para acesso ao servidor de relatório](https://technet.microsoft.com/library/bb934283.aspx)

> [!NOTE]
> Se você tiver usado o script para configurar o servidor de relatório, ignore esta seção. O script incluiu uma etapa para abrir a porta do firewall.
> 
> 

Se você tiver configurado uma porta privada para HTTPS diferente de 443, modifique o script a seguir adequadamente. Para abrir a porta **443** no Firewall do Windows, faça o seguinte:

1. Abra uma janela do Windows PowerShell com privilégios administrativos.
2. Se você tiver usado uma porta diferente de 443 ao configurar o ponto de extremidade HTTPS na VM, atualize a porta no comando a seguir e execute o comando:
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. Após a conclusão do comando, **Ok** será exibido no prompt de comando.

Para verificar se a porta está aberta, abra uma janela do Windows PowerShell e execute o seguinte comando:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Verificar a configuração
Para verificar se a funcionalidade básica do servidor de relatório está funcionando, abra seu navegador com privilégios administrativos e navegue até as seguintes URLs do servidor de relatório e do gerenciador de relatório:

* Na VM, navegue até a URL do servidor de relatório:
  
        http://localhost/reportserver
* Na VM, navegue até a URL do gerenciador de relatório:
  
        http://localhost/Reports
* No computador local, navegue até o Gerenciador de relatório **remoto** na VM. Atualize o nome DNS no exemplo a seguir, conforme apropriado. Quando receber uma solicitação por uma senha, use as credenciais de administrador que você criou durante o provisionamento da VM. O nome de usuário está no formato [Domínio]\[nome de usuário], em que o domínio é o nome de computador da VM, por exemplo, ssrsnativecloud\testuser. Se você não estiver usando HTTP**S**, remova o **s** da URL. Consulte a próxima seção para saber mais sobre como criar usuários adicionais na VM.
  
        https://ssrsnativecloud.cloudapp.net/Reports
* No computador local, navegue até a URL do servidor de relatório remoto. Atualize o nome DNS no exemplo a seguir, conforme apropriado. Se você não estiver usando HTTPS, remova o s da URL.
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Criar usuários e atribuir funções
Após a configuração e verificação do servidor de relatório, uma tarefa administrativa comum é criar um ou mais usuários e atribuir usuários às funções do Reporting Services. Para saber mais, consulte o seguinte: 

* [Criar uma conta de usuário local](https://technet.microsoft.com/library/cc770642.aspx)
* [Conceder ao Usuário Acesso a um Servidor de Relatório (Gerenciador de Relatórios)](https://msdn.microsoft.com/library/ms156034.aspx))
* [Criar e gerenciar atribuições de função](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Para criar e publicar relatórios na máquina virtual do Azure
A tabela a seguir resume algumas opções disponíveis para publicação de relatórios existentes de um computador local para o servidor de relatório hospedado na Máquina Virtual do Microsoft Azure:

* **Script RS.exe**: use o script RS.exe para copiar itens de relatório de um servidor de relatório existente para sua Máquina Virtual do Microsoft Azure. Para saber mais, consulte a seção "Modo nativo para Modo nativo – Máquina Virtual do Microsoft Azure" em [Exemplo de Script rs.exe do Reporting Services para Migrar o Conteúdo entre os Servidores de Relatório](https://msdn.microsoft.com/library/dn531017.aspx).
* **Construtor de Relatórios**: a máquina virtual inclui a versão de um clique do Construtor de Relatórios do Microsoft SQL Server. Para iniciar o Construtor de relatórios pela primeira vez na máquina virtual:
  
  1. Inicie o navegador com privilégios administrativos.
  2. Navegue até o gerenciador de relatórios na máquina virtual e clique em **Construtor de Relatórios** na faixa de opções.
     
     Para saber mais, consulte [Instalando, Desinstalando e Dando Suporte ao Construtor de Relatórios](https://technet.microsoft.com/library/dd207038.aspx).
* **SQL Server Data Tools: VM**:  se você criou a VM com o SQL Server 2012, o SQL Server Data Tools estará instalado na máquina virtual e poderá ser usado para criar **Projetos do Servidor de Relatório** e relatórios na máquina virtual. O SQL Server Data Tools pode publicar os relatórios no servidor de relatório na máquina virtual.
  
    Se você tiver criado a VM com o SQL Server 2014, instale o SQL Server Data Tools - BI para Visual Studio. Para saber mais, consulte o seguinte: 
  
  * [Microsoft SQL Server Data Tools - Business Intelligence para Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)
  * [Microsoft SQL Server Data Tools - Business Intelligence para Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)
  * [SQL Server Data Tools e SQL Server Business Intelligence (SSDT-BI)](https://docs.microsoft.com/sql/ssdt/previous-releases-of-sql-server-data-tools-ssdt-and-ssdt-bi)
* **SQL Server Data Tools: remoto**:  no computador local, crie um projeto do Reporting Services no SQL Server Data Tools que contenha os relatórios do Reporting Services. Configure o projeto para conectar-se à URL do serviço Web.
  
    ![propriedades de projeto ssdt para projeto SSRS](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **Usar script**: use o script para copiar o conteúdo do servidor de relatório. Para saber mais, consulte [Exemplo de Script rs.exe do Reporting Services para Migrar o Conteúdo entre os Servidores de Relatório](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Minimizar o custo se você não estiver usando a VM
> [!NOTE]
> Para minimizar os encargos de suas Máquinas Virtuais do Azure quando elas não estiverem em uso, finalize a VM no portal do Azure. Se você usar as opções de energia do Windows em uma VM para desligá-la, ainda receberá a cobrança do mesmo valor para a máquina virtual. Para reduzir os encargos, é necessário finalizar a VM no portal do Azure. Se você não precisar mais da VM, lembre-se de excluí-la e também os arquivos .vhd associados para evitar os encargos de armazenamento. Para saber mais, consulte a seção de perguntas frequentes em [Detalhes de Preços das Máquinas Virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>Mais informações
### <a name="resources"></a>Recursos
* Para obter um conteúdo semelhante relacionado a uma implantação de servidor único do Business Intelligence do SQL Server e do SharePoint 2013, consulte [Usar o Windows PowerShell para Criar uma VM do Azure com o BI do SQL Server e o SharePoint 2013](https://blogs.technet.microsoft.com/ptsblog/2013/10/24/use-powershell-to-create-a-windows-azure-vm-with-sql-server-bi-and-sharepoint-2013/).
* Para obter informações gerais relacionadas às implantações do Business Intelligence do SQL Server nas Máquinas Virtuais do Azure, consulte [Business Intelligence do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-classic-ps-sql-bi.md).
* Para saber mais sobre o custo dos encargos de computação do Azure, consulte a guia Máquinas Virtuais da [Calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Conteúdo da comunidade
* Para obter instruções detalhadas sobre como criar um servidor de relatório em modo nativo do Reporting Services sem usar script, consulte [Hospedando o serviço Relatórios SQL na máquina virtual Azure](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Links para outros recursos para SQL Server em VMs do Azure
[Visão geral do SQL Server em máquinas virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

