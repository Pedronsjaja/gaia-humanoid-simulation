# Referências e evidências — da câmera aos servos

[Voltar à aula](da-camera-aos-servos.md) · [Programação](README.md)

Consulta em **24/10/2026**. Escopo: documentar responsabilidades de Python/ROS 2 e a relação com o driver existente. Não foi escolhida a arquitetura da ponte ROS/LX-225 nem executado controle físico.

## Fontes do projeto

| Repositório e revisão inspecionada | Evidência usada | Limite da conclusão |
| --- | --- | --- |
| [Simulação — e1b648d](https://github.com/Pedronsjaja/gaia-humanoid-simulation/tree/e1b648dea6a187e726df3c955774e3ffc6ada916) | README, AGENTS, programação, autonomia, estado de engenharia, launch e YAMLs | Configuração e arquitetura existentes; não demonstra execução nesta atividade |
| [Driver — ecc1daa](https://github.com/Pedronsjaja/gaia-lx225-buslinker/tree/ecc1daac2fdf2b496e05f08c8777909a0ac87e0e) | README, AGENTS, `servo_testbench.py`, `docs/gaia_lx225.md`, índice de manuais | Assinaturas, unidades, conversões e comportamento estático; sem desempenho medido |
| [Estrutura — 0be9b46](https://github.com/Pedronsjaja/gaia-humanoid-structure/tree/0be9b46bb3b9d8d6eb4d222a02c609852ac8959a) | README, AGENTS, `docs/modelo-gaia.md`, estado de engenharia | Cadastro necessário e lacunas; não fornece calibração física pronta |
| [Ambiente — 349f484](https://github.com/Pedronsjaja/gaia-ros2-ubuntu24/tree/349f4842413fdc628d5a4003e617efeed6376ff1) | README, AGENTS e estado de engenharia | Plataforma de estudo e procedimento de validação; não confirma instalação no PC |

São repositórios complementares e mantidos pela equipe. O laboratório já possui pacote/configuração; o driver possui testes de serial simulada; a estrutura contém acervo e requisitos. A atividade recente ou a existência de testes não certificam maturidade para marcha. Nenhum código externo foi copiado ou relicenciado para esta aula; conteúdos do fabricante e ROBOTIS permanecem em suas fontes.

## Fontes oficiais

| Fonte | Problema e aplicação | O que não sustenta |
| --- | --- | --- |
| [ROS 2 Jazzy — Nodes, fonte oficial](https://github.com/ros2/ros2_documentation/blob/jazzy/source/Concepts/Basic/About-Nodes.rst) | Papel de nós e combinação de interfaces | Não exige um processo ou pacote por nó |
| [ROS 2 Jazzy — Interfaces](https://github.com/ros2/ros2_documentation/blob/jazzy/source/Concepts/Basic/About-Interfaces.rst) | Mensagens, serviços e actions; campos tipados | Não define o contrato de detecção do Gaia |
| [ROS 2 Jazzy — Actions](https://github.com/ros2/ros2_documentation/blob/jazzy/source/Concepts/Basic/About-Actions.rst) | Objetivo, feedback, resultado e cancelamento | Cancelar não garante parada elétrica |
| [ROS 2 Jazzy — Executors](https://github.com/ros2/ros2_documentation/blob/jazzy/source/Concepts/Intermediate/About-Executors.rst) | Agendamento de callbacks e concorrência | Não comprova prazo de controle no computador da equipe |
| [REP-103, fonte oficial](https://github.com/ros-infrastructure/rep/blob/master/rep-0103.rst) | Unidades SI e referenciais, incluindo óptico | Não fornece zero ou sentido da montagem real |
| [Joint Trajectory Controller — Jazzy](https://control.ros.org/jazzy/doc/ros2_controllers/joint_trajectory_controller/doc/userdoc.html) | Interfaces de trajetória e acompanhamento por action | Não implementa transporte LX-225 automaticamente |
| [Hardware Components — Jazzy](https://control.ros.org/jazzy/doc/ros2_control/hardware_interface/doc/hardware_components_userdoc.html) | Abstração de hardware e pontos de estudo de ciclo de vida/leitura/escrita | Não decide a arquitetura Gaia; ponte Python não é plugin pronto |
| [ROBOTIS OP3](https://emanual.robotis.com/docs/en/platform/op3/introduction/) | Plataforma de referência para estudar organização do humanoide | Não transfere propriedades físicas ao Gaia |
| [Hiwonder LX-225 — tabela técnica](https://www.hiwonder.com/products/lx-225) | [DATASHEET] Tabela confirma UART 115200 baud, posição 0–1000 correspondente a 0–240°, retorno de posição/tensão/temperatura | Não mede latência, capacidade contínua nem robustez comparativa da junta Gaia |
| [Hiwonder TTL/USB](https://www.hiwonder.com/products/hiwonder-ttl-usb-debugging-board) e [acervo do projeto](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/docs/datasheets/README.md) | Contexto da interface e localização dos manuais V2.5/V3 | A revisão física e suas ligações devem ser conferidas; PDFs/esquemas não foram reanalisados nesta atividade |

O acesso automatizado a páginas de `docs.ros.org` e `ros.org/reps` foi bloqueado por proteção do site. Para os conceitos utilizados, foram lidas as fontes oficiais equivalentes no GitHub (`ros2/ros2_documentation`, branch `jazzy`, e `ros-infrastructure/rep`). Isso não foi tratado como validação de uma instalação ROS.

## Método, reprodução e lacunas

Método: leitura de documentação e código fixados nas revisões acima; confronto com fontes oficiais; elaboração de diagramas e cenários didáticos. As equações de conversão são [CALCULADO], com relação de transmissão explicitamente definida. O exemplo numérico é [ASSUMIDO], sem associação a uma junta real.

Para reproduzir a inspeção, abra as revisões indicadas e localize `LX225Bus`, `read_servo`, `move`, `move_many`, `move_verified`, `deg_to_ticks`, `controllers.yaml` e `bridge.yaml`. Compare os argumentos e unidades com a aula. Para atualizar o guia após alteração de API, repita essa conferência.

Pendências: câmera e montagem reais `UNKNOWN`; calibração e limites do Gaia `UNKNOWN`; frequências, latências, jitter e erro físico `NOT_MEASURED`; decisão da arquitetura da ponte pendente. Não foram obtidos resultados de experimento físico nem simulado. A aula propõe ensaios futuros com objetivo, condições, variáveis e critérios registrados antes da execução.

## Validação desta entrega

Mudanças restritas a documentação Markdown e ilustração SVG. Conferências de links relativos, blocos de código, diagramas e correspondência de APIs são registradas no PR com o comando e resultado efetivamente obtidos. ROS, Gazebo, serial e hardware: **NÃO TESTADOS nesta atividade**. Os comandos ROS da aula são exercícios para o ambiente alvo, não resultados já observados.

Disponibilidade local: `wsl --list --verbose` retornou WSL não instalado; `Get-Command ros2, gz -ErrorAction SilentlyContinue` não encontrou os executáveis no PATH do Windows. Não foi gravado vídeo/GIF real de simulação, instalado Linux/Gazebo ou reiniciado o computador. A ilustração SVG é conceitual e foi produzida para este guia.
