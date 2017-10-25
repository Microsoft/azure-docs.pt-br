1. Copie o instalador para uma pasta local (digamos, C:\Temp) no servidor que você deseja proteger. Execute os comandos a seguir como administrador em um prompt de comando:

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Para instalar o Serviço de Mobilidade, execute o seguinte comando:

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```
3. Agora o agente deve ser registrado com o servidor de configuração.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe”  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argumentos de linha de comando do instalador do Serviço de Mobilidade

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| Parâmetro|Tipo|Descrição|Valores possíveis|
|-|-|-|-|
|/Role|Obrigatório|Especifica se o Serviço de Mobilidade (MS) deve ser instalado ou se o MasterTarget (MT) deve ser instalado|MS </br> MT|
|/InstallLocation|Opcional|Local onde o Serviço de Mobilidade está instalado|Qualquer pasta no computador|
|/Platform|Obrigatório|Especifica a plataforma onde o Serviço de Mobilidade está sendo instalado </br> </br>- **VMware** : use esse valor se você estiver instalando o serviço de mobilidade em uma VM em execução no *Hosts do VMware vSphere ESXi*, *Hosts do Hyper-V* e *Servidores Físicos* </br> - **Azure** : use esse valor se você estiver instalando o agente em uma VM IaaS do Azure| VMware </br> As tabelas|
|/Silent|Opcional|Especifica a execução do instalador no modo silencioso| ND|

>[!TIP]
> Os logs de instalação podem ser encontrados em %ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log

#### <a name="mobility-service-registration-command-line-arguments"></a>Argumentos da linha de comando de registro do Serviço de Mobilidade

```
Usage :
UnifiedAgentConfigurator.exe”  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | Parâmetro|Tipo|Descrição|Valores possíveis|
  |-|-|-|-|
  |/CSEndPoint |Obrigatório|Endereço IP do servidor de configuração| Qualquer endereço IP válido|
  |/PassphraseFilePath|Obrigatório|Local da frase secreta |Qualquer caminho de arquivo UNC ou local válido|


>[!TIP]
> Os logs de AgentConfiguration podem ser encontrados em %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log
