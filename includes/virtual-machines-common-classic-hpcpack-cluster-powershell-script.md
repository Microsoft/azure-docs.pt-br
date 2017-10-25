



Dependendo de seu ambiente e opções, o script pode criar toda a infraestrutura de cluster, incluindo a rede virtual do Azure, as contas de armazenamento, os serviços de nuvem, o controlador de domínio, os bancos de dados SQL locais ou remotos, o nó principal e nós de cluster adicionais. Como alternativa, o script pode usar a infraestrutura existente do Azure e criar somente os nós de cluster HPC.

Para obter informações detalhadas sobre o planejamento de um cluster Pacote HPC, confira o conteúdo de [Avaliação e planejamento de produto](https://technet.microsoft.com/library/jj899596.aspx) e de [Introdução](https://technet.microsoft.com/library/jj899590.aspx) na biblioteca de Pacote HPC 2012 R2 do TechNet.

## <a name="prerequisites"></a>Pré-requisitos
* **Assinatura do Azure**: você pode usar uma assinatura no serviço Azure Global ou no Azure China. Seus limites de assinatura afetam o número e o tipo de nós de cluster que você pode implantar. Para obter informações, consulte [Limites, cotas e restrições de serviço e assinatura do Azure](../articles/azure-subscription-service-limits.md).
* **Computador cliente do Windows com o Azure PowerShell 0.8.10 ou posterior instalado e configurado** – Confira [Introdução ao Azure PowerShell](/powershell/azureps-cmdlets-docs) para obter instruções de instalação e etapas para conectar-se à sua assinatura do Azure.
* **Script de implantação do Pacote HPC IaaS**: baixe e descompacte a versão mais recente do script no [Centro de Download da Microsoft](https://www.microsoft.com/download/details.aspx?id=44949). Verifique a versão do script executando `New-HPCIaaSCluster.ps1 –Version`. Este artigo se baseia na versão 4.5.2 do script.
* **Arquivo de configuração do script**: crie um arquivo XML que o script usa para configurar o cluster HPC. Para obter informações e exemplos, consulte as seções mais adiante neste artigo e o arquivo Manual.rtf que acompanha o script de implantação.

## <a name="syntax"></a>Sintaxe
```PowerShell
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
> [!NOTE]
> Execute o script como um administrador.
> 
> 

### <a name="parameters"></a>parâmetros
* **ConfigFile**: especifica o caminho do arquivo de configuração para descrever o cluster HPC. Confira mais sobre o arquivo de configuração neste tópico ou no arquivo Manual.rtf na pasta que contém o script.
* **AdminUserName**: especifica o nome de usuário. Se a floresta de domínio for criada pelo script, isso se tornará o nome de usuário de administrador local para todas as VMs e o nome do administrador do domínio. Se já existir a floresta de domínio, o usuário de domínio é especificado como o nome de usuário de administrador local para instalar o Pacote HPC.
* **AdminPassword**: especifica a senha do administrador. Se não estiver especificado na linha de comando, o script solicitará que você digite a senha.
* **HPCImageName** (opcional): especifica o nome de imagem da VM do Pacote HPC usado para implantar o cluster HPC. Ele deve ser uma imagem de Pacote HPC fornecida pela Microsoft no Azure Marketplace. Se não estiver especificado (recomendado na maioria dos casos), o script escolhe a [imagem de Pacote HPC 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)publicada mais recentemente. A imagem mais recente se baseia no Windows Server 2012 R2 Datacenter com Pacote HPC 2012 R2 Atualização 3 instalado.
  
  > [!NOTE]
  > A implantação falhará se você não especificar uma imagem válida do Pacote HPC.
  > 
  > 
* **LogFile** (opcional): especifica o caminho de arquivo de log de implantação. Se não for especificado, o script cria um arquivo de log no diretório temporário do computador que executa o script.
* **Force** (opcional): suprime todas as solicitações de confirmação.
* **NoCleanOnFailure** (opcional): especifica que as VMs do Azure que não forem implantadas com êxito não serão removidas. Remova essas VMs manualmente antes de executar novamente o script para continuar a implantação ou esta poderá falhar.
* **PSSessionSkipCACheck** (opcional): para cada serviço de nuvem com VMs implantadas por esse script, um certificado autoassinado é automaticamente gerado pelo Azure e todas as VMs no serviço de nuvem usam esse certificado como o certificado do Windows Remote Management (WinRM) padrão. Para implantar os recursos de HPC nessas VMs do Azure, o script por padrão instala temporariamente esses certificados no repositório de Autoridades de Certificação Confiáveis no Computador local\\ do computador cliente para suprimir o erro de segurança “AC não confiável” durante a execução do script. Os certificados são removidos quando o script termina. Se esse parâmetro for especificado, os certificados não serão instalados no computador cliente e o aviso de segurança será suprimido.
  
  > [!IMPORTANT]
  > Esse parâmetro não é recomendado para implantações de produção.
  > 
  > 

### <a name="example"></a>Exemplo
O exemplo a seguir cria um cluster Pacote HPC usando o arquivo de configuração *MyConfigFile.xml* e especifica as credenciais de administrador para instalar o cluster.

```PowerShell
.\New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### <a name="additional-considerations"></a>Considerações adicionais
* O script opcionalmente pode habilitar o envio de trabalho por meio do portal da Web Pacote HPC ou da API REST do Pacote HPC.
* O script pode opcionalmente executar scripts de pré e pós-configuração personalizados no nó principal se você quiser instalar o software adicional ou definir outras configurações.

## <a name="configuration-file"></a>Arquivo de configuração
O arquivo de configuração para o script de implantação é um arquivo XML. O arquivo de esquema HPCIaaSClusterConfig.xsd está na pasta de script de implantação do Pacote HPC IaaS. **IaaSClusterConfig** é o elemento raiz do arquivo de configuração, que contém os elementos filhos descritos em detalhes no arquivo Manual.rtf na pasta de script de implantação.

