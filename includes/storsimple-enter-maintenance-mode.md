<!--author=SharS last changed: 12/01/15-->

#### <a name="to-enter-maintenance-mode"></a>Para entrar no modo de Manutenção
1. No menu do console serial, escolha a opção 1, **Efetuar login com acesso total**.
2. Digite a senha. A senha padrão é **Senha1**.
3. No prompt de comando, digite
   
     `Enter-HcsMaintenanceMode`
4. Você verá uma mensagem de aviso informando que o modo de Manutenção interromperá todas as solicitações de E/S e a conexão com o portal clássico do Azure, e você será solicitado a confirmar. Digite **Y** para entrar no modo de Manutenção.
   
    Ambos os controladores serão reiniciados. Quando a reinicialização estiver concluída, outra mensagem será exibida indicando que o dispositivo está em modo de Manutenção.

