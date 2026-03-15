1. Stubs e skeletons

Na Tarefa 2 foi possível observar claramente o papel do stub (cliente) e do skeleton (servidor) em um sistema RPC. O stub atua como um proxy no lado do cliente, transformando uma chamada de função local em uma mensagem serializada que é enviada pela rede. Já o skeleton no servidor recebe essa mensagem, realiza o unmarshalling dos dados e despacha a chamada para a função correta. Esses componentes existem para abstrair a comunicação de rede e permitir que o programador use chamadas remotas como se fossem locais. Sem stub e skeleton, o desenvolvedor teria que implementar manualmente toda a lógica de serialização, envio de mensagens, tratamento de erros e despacho de métodos, o que aumentaria muito a complexidade do sistema.

2. REST não é RPC

Uma diferença fundamental entre REST e RPC está na forma de modelar as operações. No RPC, o cliente chama diretamente uma ação ou procedimento, como aconteceu na Tarefa 1 com proxy.calcular("soma", 7, 3), que invoca uma função remota no servidor. Já no REST, conforme visto na Tarefa 3 com Flask, a comunicação é orientada a recursos, e as operações são feitas usando verbos HTTP sobre esses recursos. Por exemplo, em vez de chamar uma função como criarProduto(), o cliente envia um POST /produtos com os dados do produto. Isso segue o princípio da interface uniforme do REST descrito por Fielding, onde a semântica das operações está nos verbos HTTP e não em nomes de métodos.

3. Evolução de contrato

No gRPC, o arquivo .proto define um contrato tipado entre cliente e servidor. Se fosse necessário adicionar um novo campo unidade: string à mensagem RespostaCalculo, o Protobuf permite essa evolução sem quebrar clientes antigos, pois novos campos podem ser adicionados com novos números de campo. Clientes antigos simplesmente ignorariam o campo desconhecido durante o unmarshalling, mantendo a compatibilidade. No REST, como geralmente não existe um schema formal, a mudança seria feita adicionando um novo campo no JSON da resposta. Clientes que não esperam esse campo normalmente o ignoram, mas como não há verificação de tipos ou contrato explícito, existe maior risco de inconsistências ou erros de interpretação.

4. Escolha de tecnologia

Para uma startup que precisa expor uma API de pagamentos para parceiros externos e também comunicar internamente entre microsserviços, eu recomendaria tecnologias diferentes para cada caso. Para parceiros externos, o REST é mais apropriado porque utiliza HTTP padrão e JSON, que são amplamente suportados por diferentes linguagens e plataformas. Isso facilita a integração com sistemas de terceiros. Já para comunicação interna entre microsserviços, eu escolheria gRPC, pois ele utiliza Protocol Buffers (serialização binária) e HTTP/2, o que oferece maior desempenho, menor tamanho de payload e contratos tipados definidos no .proto. Isso melhora a eficiência e a confiabilidade em arquiteturas de microserviços.

5. Conexão com Labs anteriores

No Lab 04 foi discutido que transparência excessiva em sistemas distribuídos pode esconder aspectos importantes da rede. No RPC isso acontece porque uma chamada remota parece exatamente igual a uma chamada local. Isso pode levar um desenvolvedor a ignorar fatores como latência de rede, falhas de comunicação e custos de serialização. Por exemplo, um programador pode criar várias chamadas RPC dentro de um loop pensando que são operações locais rápidas, quando na verdade cada chamada envolve comunicação pela rede. Esse tipo de decisão de design pode causar problemas de desempenho ou escalabilidade em sistemas distribuídos.
