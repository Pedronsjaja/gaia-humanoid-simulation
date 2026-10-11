<div align="center">

<h1>🤖 Gaia &#183; Simula&#231;&#227;o</h1>
<p><strong>Do primeiro comando de junta ao estudo de autonomia no futebol.</strong></p>
<p><kbd>ROS 2 Jazzy &#183; Gazebo Harmonic &#183; ROBOTIS OP3</kbd></p>

<p><a href="https://github.com/Pedronsjaja/gaia-humanoid-simulation/stargazers"><img alt="Estrelas no GitHub" src="https://img.shields.io/github/stars/Pedronsjaja/gaia-humanoid-simulation?style=flat-square&amp;color=147d68"/></a> <a href="https://github.com/Pedronsjaja/gaia-humanoid-simulation/commits/main"><img alt="Último commit" src="https://img.shields.io/github/last-commit/Pedronsjaja/gaia-humanoid-simulation?style=flat-square&amp;color=235789"/></a></p>
<p><a href="#inicio-rapido">Comece aqui</a> · <a href="#guia-do-projeto">Documentação</a> · <a href="https://github.com/Pedronsjaja/gaia-humanoid-simulation/tree/main">Arquivos</a></p>

</div>

<a id="inicio-rapido"></a>
## 🚀 Comece aqui

**[Abrir o laboratório](docs/simulacao/instalacao-e-laboratorio.md)**

| Modelo de referência | Aprendizado | Próximos passos |
| --- | --- | --- |
| ROBOTIS OP3 em revisão fixa. | Controladores, câmera e IMU no simulador. | Plano de autonomia com limites documentados. |

### Caminho do projeto

```mermaid
flowchart LR
  N0["Preparar Ubuntu"]
  N1["Instalar laboratório"]
  N0 --> N1
  N2["Explorar OP3"]
  N1 --> N2
  N3["Estudar autonomia"]
  N2 --> N3
```

<a id="guia-do-projeto"></a>
## 📖 Documentação

Laboratório **ROS 2 Jazzy + Gazebo Harmonic**, baseado no modelo oficial **ROBOTIS OP3**, para a equipe Gaia estudar juntas, sensores e os programas necessários à autonomia no futebol.

## Roteiro da primeira aula

1. Prepare o Ubuntu pelo [guia de ambiente](https://github.com/Pedronsjaja/gaia-ros2-ubuntu24): dual boot ou máquina virtual.
2. Leia [como navegar no GitHub](docs/primeiros-passos-github.md).
3. Siga [instalar e executar o laboratório](docs/simulacao/instalacao-e-laboratorio.md).
4. Carregue o OP3, confira os controladores, comande a cabeça e observe câmera/IMU.
5. Estude os [objetivos e etapas de autonomia](docs/simulacao/autonomia.md).

| Assunto | Guia |
| --- | --- |
| Entender ROS, Gazebo, URDF e RViz | [Introdução à simulação](docs/simulacao/README.md) |
| Instalar, inserir o robô e fazer exercícios | [Tutorial do laboratório](docs/simulacao/instalacao-e-laboratorio.md) |
| Organização do software | [Programação](docs/programacao/README.md) |
| Percepção, equilíbrio, decisão e futebol | [Plano de autonomia](docs/simulacao/autonomia.md) |
| Bibliotecas ROBOTIS e versões fixadas | [Referências](docs/simulacao/referencias.md) |
| Testes executados e limites | [Registro de validação](docs/simulacao/validacao.md) |
| Dados mecânicos para criar o modelo Gaia | [Estrutura → modelo Gaia](https://github.com/Pedronsjaja/gaia-humanoid-structure/blob/main/docs/modelo-gaia.md) |
| Regras que limitam sensores e projeto | [CBR Humanoid](https://github.com/Pedronsjaja/gaia-humanoid-structure/blob/main/docs/competicao/README.md) |

## O que existe

- Pacote [gaia_op3_sim](ros2/gaia_op3_sim/): cenário, controladores e sensores virtuais.
- [Manifesto OP3](simulation/op3.repos): baixa o modelo ROBOTIS em revisão fixa.
- [Automação Linux](.github/workflows/simulation.yml): compilação, testes do modelo e ensaio de sensores/movimento.

O laboratório prende a base do OP3 para aprender comandos sem depender de equilíbrio. Marcha, futebol autônomo e interface ROS/LX-225 ainda são etapas de desenvolvimento. O modelo OP3 não representa automaticamente as massas, limites e atuadores reais do Gaia.

## Organização

```text
docs/simulacao/         Aulas, autonomia e referências
docs/programacao/      Trilha de software
ros2/gaia_op3_sim/      Pacote ROS 2 do laboratório
simulation/op3.repos   Modelo oficial e revisão
.github/workflows/     Verificações automáticas
```

Este conteúdo foi separado do projeto original; consulte a [procedência da migração](docs/migracao.md).

## Repositórios da equipe Gaia

| Preciso de… | Repositório |
| --- | --- |
| Servo LX-225, BusLinker, drivers e testes de bancada | [LX-225 / BusLinker](https://github.com/Pedronsjaja/gaia-lx225-buslinker) |
| Gazebo, OP3, ROS 2 e autonomia | [Simulação](https://github.com/Pedronsjaja/gaia-humanoid-simulation) |
| CAD, suportes, montagem e requisitos físicos da CBR | [Estrutura](https://github.com/Pedronsjaja/gaia-humanoid-structure) |
| Instalar Ubuntu 24.04 e ROS 2 Jazzy | [Ambiente Linux / ROS 2](https://github.com/Pedronsjaja/gaia-ros2-ubuntu24) |

Cada repositório tem seus próprios arquivos, Issues e histórico. Abra dúvidas na área correspondente; inclua links quando uma mudança depender de outra área.

