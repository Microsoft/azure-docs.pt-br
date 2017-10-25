1. Copie o instalador para uma pasta local (digamos, /tmp) no servidor que você deseja proteger. Execute os seguintes comandos em um terminal:
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Para instalar o Serviço de Mobilidade, execute o seguinte comando:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. Depois que a instalação for concluída, o Serviço de Mobilidade precisa se registrar no servidor de configuração. Execute o seguinte comando para registrar o Serviço de Mobilidade com o servidor de configuração.

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```

#### <a name="mobility-service-installer-command-line"></a>Linha de comando do instalador do Serviço de Mobilidade

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Parâmetro|Tipo|Descrição|Valores possíveis|
|-|-|-|-|
|-r |Obrigatório|Especifica se o Serviço de Mobilidade (MS) deve ser instalado ou se o MasterTarget (MT) deve ser instalado|MS </br> MT|
|-d |Opcional|Local onde o Serviço de Mobilidade será instalado|/usr/local/ASR|
|-v|Obrigatório|Especifica a plataforma onde o Serviço de Mobilidade está sendo instalado </br> </br>- **VMware** : use esse valor se você estiver instalando o serviço de mobilidade em uma VM em execução no *Hosts do VMware vSphere ESXi*, *Hosts do Hyper-V* e *Servidores Físicos* </br> - **Azure** : use esse valor se você estiver instalando o agente em uma VM IaaS do Azure| VMware </br> As tabelas|
|-q|Opcional|Especifica a execução do instalador no modo silencioso| N/D |


#### <a name="mobility-service-configuration-command-line"></a>Linha de comando de configuração do Serviço de Mobilidade

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parâmetro|Tipo|Descrição|Valores possíveis|
|-|-|-|-|
|-i |Obrigatório|IP do Servidor de Configuração|Qualquer endereço IP válido|
|-P |Obrigatório|Caminho completo do arquivo onde a senha de conexão é salva|Qualquer pasta válida|
