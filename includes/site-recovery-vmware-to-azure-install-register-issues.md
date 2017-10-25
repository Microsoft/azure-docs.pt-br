
### <a name="installation-failures"></a>Falhas de instalação
| **Mensagem de erro de exemplo** | **Ação recomendada** |
|--------------------------|------------------------|
|ERRO   Falha ao carregar Contas. Erro: System.IO.IOException: não é possível ler dados da conexão de transporte ao instalar e registrar o servidor CS.| Verifique se TLS 1.0 está habilitado no computador. |

### <a name="registration-failures"></a>Falhas de registro
As falhas de registro podem ser depuradas examinando os logs da pasta **%ProgramData%\ASRLogs**.

| **Mensagem de erro de exemplo** | **Ação recomendada** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>Mensagem: ACS50008: token SAML é inválido.<br>ID de rastreamento: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>ID de correlação: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Carimbo de data/hora: **2016-12-12 14:50:08Z<br>** | Verifique se o horário no relógio do sistema não está mais de 15 minutos antes/depois do horário local. Execute novamente o instalador para concluir o registro.|
|**09:35:27** : DRRegistrationException ao tentar obter todo o cofre de recuperação de desastre para o certificado selecionado: : Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException lançado, Exception.Message: ACS50008: o token SAML é inválido.<br>ID de rastreamento: e5ad1af1-2d39-4970-8eef-096e325c9950<br>ID de correlação: abe9deb8-3e64-464d-8375-36db9816427a<br>Carimbo de data/hora: **2016-05-19 01:35:39Z**<br> | Verifique se o horário no relógio do sistema não está mais de 15 minutos antes/depois do horário local. Execute novamente o instalador para concluir o registro.|
|06:28:45: Falha ao criar certificado<br>06:28:45: A instalação não pode continuar. Não é possível criar um certificado necessário para autenticar a Recuperação de Site. Executar a instalação novamente | Verifique se você está executando a instalação como um administrador local. |
