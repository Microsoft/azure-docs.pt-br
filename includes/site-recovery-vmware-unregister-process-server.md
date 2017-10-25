As etapas para cancelar o registro de um servidor de processo difere dependendo de seu status de conexão com o servidor de configuração.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Cancelar o registro de um servidor de processo que está em um estado conectado

1. Remoto para o servidor de processo como administrador.
2. Inicie o **painel de controle** e abra **programas > desinstalar um programa**
3. Desinstalar um programa pelo nome **servidor/processo de configuração do Microsoft Azure Site Recovery**
4. Após a conclusão da etapa 3, você pode desinstalar **dependências de servidor do Microsoft Azure Site Recovery/processo de configuração**

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Cancelar o registro de um servidor de processo que está em um estado desconectado

> [!WARNING]
> Use as etapas a seguir deve ser usado se não houver nenhuma maneira de reviver a máquina virtual na qual o servidor de processo foi instalado.

1. Faça logon para o servidor de configuração como um administrador.
2. Abra um prompt de comando administrativo e navegue até o diretório `%ProgramData%\ASR\home\svsystems\bin`.
3. Agora execute o comando.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. Isso descartará os detalhes do servidor de processo do sistema.
