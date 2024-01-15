# Respostas

---

6) Considere o código, apresentado abaixo, de um programa cliente que comunica com um servidor que atende pedidos no
   socket de domínio Unix localizado em /run/tp2-server.sock Não contando com a falta das diretivas de #include nem com
   a falta do tratamento de erros, indique os erros notórios no código apresentado, justificando devidamente porque
   motivo são erros.

   > O servidor implementado só será capaz de atender a um pedido de cada vez, tendo o mesmo de ser re-executado após
   > esse atendimento, uma vez que após processar o pedido, o servidor fecha a ligação com o cliente e termina a sua
   > execução. Para que o servidor possa atender a vários pedidos, teria de ser implementado um ciclo que permitisse que
   > o servidor continuasse a receber pedidos após o atendimento de cada um deles. Outro erro é o facto de ser utilizado
   > `sun_port` da struct `sockaddr_un` que não é um membro dessa struct mas sim da struct `sockaddr_in` e o seu nome
   > seria `sin_port`.

7) A especificação de um serviço systemd pode incluir opcionalmente a diretiva ExecStop, geralmente omissa, para
   especificar um comando explícito para terminar o serviço. No entanto, mesmo sem esta diretiva, o comando systemctl
   stop nome_do_serviço consegue geralmente terminá-lo. Como?

   > O comando `systemctl stop nome_do_serviço` envia um sinal `SIGTERM` ao processo do serviço, que por sua vez
   > termina a sua execução. Se por algum motivo o serviço não terminar a sua execução após o envio deste sinal, o
   > comando `systemctl stop nome_do_serviço` envia um sinal `SIGKILL` ao processo do serviço, que termina
   > imediatamente a sua execução.

8) Na secção de instalação do ficheiro que descreve um serviço em systemd, explique porque razão é preferível usar
   WantedBy, em vez de RequiredBy, para associar esse serviço a um target.

   > O uso de `WantedBy` em vez de `RequiredBy` deve-se ao facto de o uso de `WantedBy` tornar o sistema mais robusto à
   > possibilidade de falhas. Isto porque, se um serviço tiver `RequiredBy`, o sistema não arranca se esse serviço
   > falhar, enquanto que se um serviço tiver `WantedBy`, o sistema arranca mesmo que esse serviço falhe.

9) O código fonte do kernel Linux pode ser compilado com diversas opções de configuração. Uma dessas opções está
   documentada com o seguinte texto:

   «*This changes the kernel so it can modify itself when it is run under a hypervisor, potentially improving
   performance significantly over full virtualization. However, when run without a hypervisor the kernel is
   theoretically slower and slightly larger.*»

   Que nome se dá ao tipo de solução de virtualização que está implícito neste texto? Na sua resposta, refira
   as partes relevantes do texto acima que permitem identificar o tipo de solução/otimização em causa.

   > O texto acima refere-se à paravirtualização, é possível identificar isso pelo facto de o texto referir que o
   > kernel é modificado quando é executado sob um hypervisor, "This changes the kernel so it can modify itself when it
   is
   > run under a hypervisor, potentially improving performance significantly over full virtualization.", o que é uma das
   > características da paravirtualização.

10) Descreva sucintamente 5 a 7 passos que considera relevantes na compreensão do que acontece num sistema Linux desde
    que se executa o comando docker run -it ubuntu:latest até que o respetivo contentor está em execução. NOTA: Existem
    múltiplas respostas válidas.

> 1. Verificar se a imagem `ubuntu:latest` existe localmente, se não existir, fazer o download da mesma.
> 2. Criar um contentor com base na imagem `ubuntu:latest`.
> 3. Atribuição de recursos ao contentor.
> 4. Configuração da rede do contentor.
> 5. Inicialização do contentor.
> 6. Entrada no terminal interativo do contentor.

11) Um ficheiro docker-compose.yml, para especificação de uma solução composta com o nome tp2, contém, entre outros
    elementos, a especificação de um serviço com o nome svc, associado a uma rede tp2net de tipo bridge. Ao levantar a
    solução com up, este serviço poderá ter associado mais do que 1 contentor? Se sim, como? Se não, o que poderia ser
    feito para que isso fosse possível? Em qualquer caso, que nome ou nomes podem ser usados na rede tp2net para chegar
    a esse(s) contentor(es)?

> Este serviço poderia ter associado mais do que um contentor se o seu `scale` fosse maior que 1, por exemplo, ou se
> na especificação do serviço fosse definido um `deploy` com `replicas` maior que 1. Para chegar a esses contentores
> poderia ser usado o nome "tp2-svc-x", sendo "x" o número do contentor.

12) Considere o Dockerfile apresentado ao lado, os ficheiros package.json e app.js, com uma aplicação para Node.js, o
    ficheiro README.md, e uma pasta images com ficheiros de suporte à interface de utilizador da aplicação. Pretende-se
    que no sistema de ficheiros das instâncias da imagem todos os ficheiros e pastas abaixo de /home/node/webapp tenham
    como dono o utilizador node. Considera-se que há grande probabilidade de alterar o ficheiro app.js, o porto da
    aplicação e os ficheiros da pasta images. Pretende-se ainda, prioritariamente, otimizar a reutilização da cache de
    imagens intermédias e, dentro do possível, minimizar o número de camadas de overlay.

       ```dockerfile
       FROM node:alpine
       WORKDIR /home/node/webapp
       COPY README.md .
       COPY images/* ./images
       USER node
       ENV PORT=80
       COPY package.json app.js .
       RUN chown -R node.node /home/node
       RUN npm install
       EXPOSE $PORT
       CMD ["node", "app,js"]
       ```

    - Identifique problemas no Dockerfile que contrariam os objetivos enunciados atrás.
    
    > A criação de camadas a mais no Dockerfile, pelo uso de vários comandos `RUN`, quando a sua execução poderia ser
    feita numa só camada. Outro problema é o facto de a pasta errada ser o alvo da mudança de dono, neste caso a pasta
    correta seria `/home/node/webapp/`. E por fim o facto de o `COPY` do `package.json` e do `app.js` ser feito na mesma
    camada, sendo que existe uma grande probabilidade de o `app.js` ser alterado, o que faria com que o ficherio 
    `package.json` fosse copiado novamente.
    
    - Corrija o Dockerfile de modo a concretizar os objetivos enunciados.

    ```dockerfile
    FROM node:alpine
    WORKDIR /home/node/webapp
    COPY README.md package.json ./
    COPY app.js ./
    COPY images/* ./images
    USER node
    RUN chown -R node.node /home/node/webapp && \
        npm install
    EXPOSE 80
    CMD ["node", "app,js"]
    ```