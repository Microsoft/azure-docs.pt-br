<!--author=alkohli last changed: 12/01/15-->


#### <a name="to-configure-and-register-the-device"></a>Para configurar e registrar o dispositivo
1. Acesse a interface do Windows PowerShell no console serial do dispositivo StorSimple. Consulte [Usar o PuTTY para se conectar ao console serial do dispositivo](#use-putty-to-connect-to-the-device-serial-console) para obter instruções. **Siga o procedimento corretamente ou você não conseguirá acessar o console.**
2. Na sessão que é aberta, pressione Enter uma vez para obter um prompt de comando. 
3. Você deverá escolher o idioma que deseja definir para seu dispositivo. Especifique o idioma e pressione Enter. 
   
    ![O StorSimple configura e registra o dispositivo 1](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice1-include.png)
4. No menu do console serial apresentado, escolha a opção 1 para efetuar logon com acesso completo. 
   
    ![Dispositivo de registro do StorSimple 2](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice2-include.png)
   
     Conclua as etapas de 5 a 12 para definir as configurações de rede necessárias e mínimas para seu dispositivo. **Essas etapas de configuração devem ser executadas no controlador ativo do dispositivo.** O menu do console serial indica o estado do controlador na mensagem de faixa. Se você não estiver conectado ao controlador ativo, desconecte e conecte-se ao controlador ativo.
5. No prompt de comando, digite sua senha. A senha do dispositivo padrão é **Senha1**.
6. Digite o seguinte comando:
   
     `Invoke-HcsSetupWizard` 
7. Um assistente de instalação aparecerá para ajudá-lo a configurar as definições da rede para o dispositivo. Forneça as seguintes informações: 
   
   * Endereço IP para a interface de rede DADOS 0
   * Máscara da sub-rede
   * Gateway
   * Endereço IP do servidor DNS Primário
   * Endereço IP do servidor NTP Primário
     
     > [!NOTE]
     > Você terá que aguardar alguns minutos para que a máscara da sub-rede e as configurações de DNS sejam aplicadas. Se você receber uma mensagem de erro "O dispositivo não está pronto.", verifique a conexão de rede física no adaptador de rede DADOS 0 de seu controlador ativo.
     > 
     > 
8. (Opcional) configure seu servidor proxy da Web. Embora a configuração do proxy da Web seja opcional, **saiba que se você usar um proxy da Web, poderá apenas configurá-lo aqui**. Para obter mais informações, visite [Configurar proxy da Web para seu dispositivo](../articles/storsimple/storsimple-configure-web-proxy.md). Se você tiver algum problema durante esta etapa, consulte a guia de solução de problemas para [Erros durante a configuração do proxy da web](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-the-optional-web-proxy-settings).

     > [!NOTE]
     > Você pode pressionar Ctrl + C a qualquer momento para sair do assistente de instalação. As configurações aplicadas antes de você dar esse comando serão mantidas.

1. Por motivos de segurança, a senha de administrador do dispositivo expira após a primeira sessão e você precisará alterá-la nas sessões subsequentes. Quando solicitado, forneça uma senha de administrador do dispositivo. Uma senha de administrador do dispositivo válida deve ter entre 8 e 15 caracteres. A senha deve conter uma combinação de caracteres com letras maiúsculas e minúsculas, números e caracteres especiais.
2. A senha do Gerenciador de Instantâneos StorSimple também é definida aqui. Use essa senha ao autenticar um dispositivo com o host do Windows executando o Gerenciador de Instantâneos StorSimple. Quando solicitado, forneça uma senha de 14 a 15 caracteres. A senha deve conter uma combinação de três das seguintes opções: caracteres com letras minúsculas e maiúscula, numéricos e especiais. 
   
   ![Dispositivo de registro do StorSimple 4](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice4-include.png)
   
   Você pode redefinir a senha do Gerenciador de Instantâneos StorSimple na interface de serviço Gerenciador StorSimple. Para obter etapas detalhadas, acesse [Alterar as senhas do StorSimple usando o serviço StorSimple Manager](../articles/storsimple/storsimple-change-passwords.md).
   
   Para solucionar qualquer tipo de problemas nesta etapa, consulte a guia de solução de problemas para [Erros relacionados à senhas](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords).
3. A etapa final do assistente de instalação registra seu dispositivo no serviço Gerenciador StorSimple. Para isso, será necessária a chave de registro do serviço obtida na etapa 2. Depois de fornecer a chave de registro, talvez seja necessário aguardar de 2 a 3 minutos antes do dispositivo ser registrado.
   
   Para solucionar quaisquer falhas possíveis de registro de dispositivo, consulte [Erros durante o registro de dispositivo](../articles/storsimple/storsimple-troubleshoot-deployment.md#errors-during-device-registration). Para obter uma solução de problemas detalhada, também é possível consultar [Exemplo passo a passo de solução de problemas](../articles/storsimple/storsimple-troubleshoot-deployment.md#step-by-step-storsimple-troubleshooting-example).
4. Depois do dispositivo ser registrado, uma chave de Criptografia de Dados do Serviço será exibida. Copie essa chave e salve-a em um local seguro.
   
   > [!WARNING]
   > Essa chave será necessária com a chave de registro do serviço para registrar dispositivos adicionais no serviço Gerenciador StorSimple. Consulte a [segurança do StorSimple](../articles/storsimple/storsimple-security.md) para obter mais informações sobre essa chave.
   > 
   > 
   
    ![Dispositivo de registro do StorSimple 6](./media/storsimple-configure-and-register-device/HCS_RegisterYourDevice6-include.png)
   
    Para copiar o texto da janela do console serial, basta selecionar o texto. Então, você conseguirá colá-lo na área de transferência ou em qualquer editor de texto. NÃO use Ctrl + C para copiar a chave de criptografia de dados do serviço. Usar Ctrl + C fará com que você saia do assistente de instalação. Como resultado, a senha de administrador do dispositivo e a senha do Gerenciador de Instantâneos StorSimple não serão alteradas e o dispositivo voltará para as senhas padrão.
5. Saia do console serial.
6. Volte para o portal clássico do Azure e realize as seguintes etapas:
   
   1. Clique duas vezes no serviço Gerenciador StorSimple para acessar a página **Início Rápido** .
   2. Clique em **Exibir dispositivos conectados**.
   3. Na página **Dispositivos** , verifique se o dispositivo conectou com êxito o serviço pesquisando o status. O status do dispositivo deve ser **Online**. Se o status do dispositivo for **Offline**, aguarde alguns minutos para o dispositivo ficar online.
   
   ![Página dos Dispositivos StorSimple](./media/storsimple-configure-and-register-device/HCS_DevicesPageM-include.png) 
   
   > [!IMPORTANT]
   > Depois que o dispositivo estiver online, conecte os cabos de rede que você tinha desconectado no início desta etapa.
   > 
   > 

Após o registro bem-sucedido do dispositivo, se ele não ficar online, você poderá executar o `Test-HcsmConnection -Verbose` para garantir que a conectividade de rede esteja íntegra. Para consultar o uso detalhado desse cmdlet, acesse [referência do cmdlet para Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx).

![Vídeo disponível](./media/storsimple-configure-and-register-device/Video_icon.png) **Vídeo disponível**

Para assistir a um vídeo que demonstra como configurar e registrar seu dispositivo por meio do Windows PowerShell para StorSimple, clique [aqui](https://azure.microsoft.com/documentation/videos/initialize-the-storsimple-appliance/).

