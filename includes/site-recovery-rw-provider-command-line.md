UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <Endereço IP a ser usado na transferência de dados] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Parâmetros:

* /ServerMode: obrigatório. Especifica se os servidores de configuração e o processo devem ser instalados ou somente o servidor de processo. Valores de entrada: CS, PS.
* InstallLocation: obrigatório. A pasta na qual os componentes estão instalados.
* /MySQLCredsFilePath. Obrigatório. O caminho do arquivo no qual as credenciais do servidor MySQL são armazenadas. O arquivo deve estar no seguinte formato:
* [MySQLCredentials]
* MySQLRootPassword = "<Password>"
* MySQLUserPassword = "<Password>"
* /VaultCredsFilePath. Obrigatório. O local do arquivo de credenciais do cofre
* /EnvType. Obrigatório. O tipo de instalação. Valores: VMware, NonVMware
* /PSIP e /CSIP. Obrigatório. O endereço IP do servidor de processo e do servidor de configuração.
* /PassphraseFilePath. Obrigatório. O local do arquivo da frase secreta.
* /BypassProxy. Opcional. Especifica se o servidor de gerenciamento se conecta ao Azure sem um proxy.
* /ProxySettingsFilePath. Opcional. Configurações de proxy (o proxy padrão exigir autenticação ou um proxy personalizado). O arquivo deve estar no seguinte formato:
* [ProxySettings]
* ProxyAuthentication = "Sim/Não"
* IP do Proxy = "Endereço IP>"
* ProxyPort = "<Port>"
* ProxyUserName="<User Name>"
* ProxyPassword="<Password>"
* DataTransferSecurePort. Opcional. O número da porta de dados de replicação.
* SkipSpaceCheck. Opcional. Ignorar verificação para o cache do espaço.
* AcceptThirdpartyEULA. Obrigatório. Aceita o EULA de terceiros.
* ShowThirdpartyEULA. Obrigatório. Exibe o EULA de terceiros. Se fornecido como entrada, todos os outros parâmetros serão ignorados.
