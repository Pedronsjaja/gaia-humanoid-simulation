# Evidencia do laboratorio de simulacao

O smoke agora observa os cinco topicos durante 10 segundos de relogio do
host, exige pelo menos duas mensagens novas por topico e idade final menor
ou igual a 5 s. Metricas de contagem, frequencia observada e idade ficam em
smoke-metrics.json. O workflow preserva metricas e logs mesmo em falha.
O movimento exige uma mensagem de juntas posterior ao comando, evitando
aprovar uma posicao antiga. Estas tolerancias detectam fluxo interrompido;
nao sao especificacoes de tempo real nem validacao das taxas nominais.

Prioridades: ALTO medir taxas em ambiente Linux/ROS identificado; MEDIO
comparar uma junta real com modelo identificado; BAIXO ampliar cenarios
de movimento. Equilibrio, contato, atuadores GAIA e ruido real: NAO VALIDADOS.
O suporte fixo do laboratorio nao demonstra marcha ou estabilidade.

Para entender esta implementacao: estude ros2/gaia_op3_sim/test/smoke_sim.py,
relogio de simulacao versus monotonic do host, QoS de sensores e referenciais.
Desafio: interrompa a camera em uma simulacao isolada e confirme a falha.

Referencias: [REP-103](https://www.ros.org/reps/rep-0103.html), unidades SI e
referenciais; [ROS 2 Jazzy QoS](https://docs.ros.org/en/jazzy/Concepts/Intermediate/About-Quality-of-Service-Settings.html), entrega de mensagens;
[ROBOTIS OP3](https://github.com/ROBOTIS-GIT/ROBOTIS-OP3-Common), modelo externo.
Nao transferir propriedades desse modelo ao robo GAIA sem identificacao.
