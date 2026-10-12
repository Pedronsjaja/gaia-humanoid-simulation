# Instrucoes do projeto

ROS 2 Jazzy/Gazebo Harmonic em ros2/gaia_op3_sim. ROBOTIS OP3 e referencia externa fixada em simulation/op3.repos; nao representa a geometria ou dinamica medida do GAIA. Nunca executar smoke_sim.py contra hardware.

Ler README e docs antes de alterar. Preservar arquitetura, dados brutos e
autoria/licencas dos materiais externos. Nao inventar hardware, medidas ou
resultados. Marcar UNKNOWN/NOT_MEASURED e separar SIMULATED de REAL.
Fazer mudancas pequenas, com regressao para defeitos corrigidos.

Validacao: workflow .github/workflows/simulation.yml: colcon, unittest e smoke no Gazebo; local sem ROS permite apenas verificacoes parciais.

Definition of Done: verificacoes pertinentes, documentacao das alteracoes,
comandos reproduziveis e limites de validacao. Inspecao estatica nao comprova
funcionamento fisico. Ver docs/engineering-status.md para prioridades e estudo.
