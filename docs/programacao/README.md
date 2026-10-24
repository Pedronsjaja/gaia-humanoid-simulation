# Frente de programação

[Início do Gaia](../../README.md) · [Como usar GitHub](../primeiros-passos-github.md)

A programação liga os objetivos do robô aos sensores e atuadores. Comece pelo ambiente e por um teste pequeno que possa ser observado.

## Comece pela explicação da comunicação

**[Da câmera aos servos — guia ilustrado](da-camera-aos-servos.md).** Leia primeiro o resumo de um minuto e os fluxogramas. Depois consulte os conceitos ROS 2, as funções do Python atual e a associação entre juntas e servos. A arquitetura da ponte ROS/LX-225 ainda está a definir. Consulte as [referências e evidências](referencias-camera-servos.md).

| Ordem | Material | O que entregar |
| --- | --- | --- |
| 1 | [Ubuntu 24.04 e ROS 2 Jazzy](https://github.com/Pedronsjaja/gaia-ros2-ubuntu24) | Talker/listener no seu computador |
| 2 | [Laboratório OP3](../simulacao/instalacao-e-laboratorio.md) | Modelo, juntas e sensores virtuais |
| 3 | [Comandos LX-225](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/docs/uso.md) e [instalação da bancada](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/docs/instalacao.md) | Leitura de um servo real, junto da eletrônica |
| 4 | [Modelo Gaia](https://github.com/Pedronsjaja/gaia-humanoid-structure/blob/main/docs/modelo-gaia.md) | Descrição da junta validada pela estrutura |
| 5 | [Autonomia](../simulacao/autonomia.md) | Percepção, estimativa, decisão e controle |
| 6 | [CBR Humanoid](https://github.com/Pedronsjaja/gaia-humanoid-structure/blob/main/docs/competicao/README.md) | Requisitos de futebol e arbitragem rastreáveis |

## Código local e integrações em outros repositórios

| Caminho | Função |
| --- | --- |
| [ros2/gaia_op3_sim](../../ros2/gaia_op3_sim) | Laboratório do simulador |
| [simulation/op3.repos](../../simulation/op3.repos) | Revisão do modelo oficial a baixar |
| [buslinker_v2_5.py](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/buslinker_v2_5.py) / [buslinker_v3.py](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/buslinker_v3.py) | Programas completos de bancada |
| [servo_testbench.py](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/servo_testbench.py) | Fonte compartilhada de comunicação LX-225 |
| [bench_tools.py](https://github.com/Pedronsjaja/gaia-lx225-buslinker/blob/main/bench_tools.py) | Gráficos, IDs e calibração |
| [tests](https://github.com/Pedronsjaja/gaia-lx225-buslinker/tree/main/tests) | Testes de bancada sem hardware |
| [tools](https://github.com/Pedronsjaja/gaia-lx225-buslinker/tree/main/tools) | Diagnóstico e geração dos programas completos |
| [firmware](https://github.com/Pedronsjaja/gaia-humanoid-structure/tree/main/firmware) | Diagnóstico ESP32-S3 de joystick/OLED |

Os arquivos autônomos da BusLinker são gerados: altere as fontes compartilhadas e rode o gerador. Não mantenha versões divergentes manualmente.

## Como trabalhar com as outras frentes

Peça à estrutura nomes, eixos, limites e zero de cada junta. Peça à eletrônica alimentação, IDs, taxa de leitura confiável, sensores e estados de falha. Não assuma que um nome no URDF já corresponde ao servo físico.

Cada mudança deve explicar problema, comportamento esperado, teste feito e limitação conhecida. Use uma branch e abra um Pull Request; veja o [guia GitHub](../primeiros-passos-github.md).
