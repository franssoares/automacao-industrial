# Diretrizes de Contribuição — DCA3607 Automação Industrial

## Sumário
- [Estratégia de Branches](#estratégia-de-branches)
- [Fluxo de Trabalho](#fluxo-de-trabalho)
- [Exemplos de Commits](#exemplos-de-commits)
- [Setup de Desenvolvimento](#setup-de-desenvolvimento)
  - [Pré-requisitos](#pré-requisitos)
  - [Configuração do Ambiente](#configuração-do-ambiente)
- [Padrões de Código (IEC 61131-3)](#padrões-de-código-iec-61131-3)
- [Diretrizes Específicas](#diretrizes-específicas)
  - [Versionamento de Arquivos .EXP](#versionamento-de-arquivos-exp)
  - [Modelagem Formal Obrigatória](#modelagem-formal-obrigatória)
  - [Segurança e Intertravamentos](#segurança-e-intertravamentos)
  - [Validação em Simulação e Bancada](#validação-em-simulação-e-bancada)

---

## Estratégia de Branches

A branch `main` é protegida. Commits diretos na `main` são estritamente proibidos — todo código e documentação devem ser validados em ambiente de simulação ou bancada didática antes da integração.

Todas as alterações devem ser submetidas via **Pull Request (PR)** apontando para a branch `main`.

Utilize o padrão **GitFlow** para a nomenclatura de branches:

| Prefixo | Finalidade | Exemplo |
| :--- | :--- | :--- |
| `feature/` | Implementação de nova lógica, POU ou atividade de CLP | `feature/semaforo-pedestres` |
| `bugfix/` | Correção de falhas lógicas, corrida de estados ou erros de endereçamento | `bugfix/reset-temporizador-t2` |
| `docs/` | Criação ou atualização de documentação técnica e diagramas | `docs/atualiza-afd-latex` |
| `refactor/` | Reorganização de código Ladder/SFC sem alteração funcional | `refactor/modulariza-fb-partida` |
| `style/` | Ajustes visuais em telas de IHM (*Visualizações*) | `style/layout-ihm-semaforo` |
| `test/` | Inclusão de rotinas de teste ou validação em bancada | `test/rotina-validacao-tb131` |

---

## Fluxo de Trabalho

1. **Atualize sua branch `main` local:**
   ```bash
   git checkout main
   git pull origin main
   ```

2. **Crie sua branch de trabalho a partir da `main`:**
   ```bash
   git checkout -b <tipo>/<descricao-curta>
   ```

3. **Desenvolva, teste e exporte os arquivos:**
   - Faça as alterações no software **MasterTool IEC**.
   - Exporte os POUs, configurações e visualizações atualizados em formato `.EXP` para a pasta correspondente da atividade (ex: `3-sistema_semaforo/codesys_ext/`).

4. **Realize commits atômicos seguindo o padrão Conventional Commits:**
   ```bash
   git add <arquivos>
   git commit -m "<tipo>(<escopo>): <descricao no imperativo>"
   ```
   **Tipos válidos:** `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`.

5. **Envie sua branch remota e abra o Pull Request:**
   ```bash
   git push -u origin <tipo>/<descricao-curta>
   ```
   Descreva no PR a atividade realizada, o hardware testado e anexe capturas de tela comprovando a compilação sem erros (0 erros / 0 avisos).

---

## Exemplos de Commits

- `feat(ladder): implementa temporizadores independentes T1 a T4 no PLC_PRG`
- `fix(io): corrige mapeamento de bobina %QX1.3 da lâmpada amarela`
- `docs(readme): adiciona tabela de variáveis e guia de simulação do semáforo`
- `docs(afd): formaliza modelo de 4 estados e diagrama TikZ em LaTeX`
- `style(ihm): alinha posições das lâmpadas na tela VISUALIZACAO1`
- `refactor(pou): padroniza nomes de variáveis conforme norma IEC 61131-3`
- `test(tb131): valida tempos de ciclo na bancada do kit didático`

---

## Setup de Desenvolvimento

### Pré-requisitos
- **MasterTool IEC:** Plataforma de desenvolvimento CLP baseada no 3S CoDeSys v2.3, customizada pela Altus.
- **Bibliotecas da Família Duo:** `DU35x.lib`, `Standard.lib`, `SysLibCallback.lib`, `SysLibTargetVisu.lib`, `Iecsfc.lib`.
- **Compilador LaTeX (Opcional):** Para compilação dos relatórios formais em `.tex` (`pdflatex` / `tikz`).
- **Hardware de Bancada (quando presencial):**
  - Kit didático **Training Box DUO TB131**.
  - Controlador **Altus Duo DU350 ou DU351**.
  - Cabo serial RS-232 com conversor USB-Serial configurado para a porta COM adequada.

### Configuração do Ambiente
1. **Clonar o Repositório:**
   ```bash
   git clone https://github.com/franssoares/automacao-industrial.git
   cd automacao-industrial
   ```

2. **Organização das Atividades:**
   Cada atividade prática possui seu próprio diretório estruturado:
   ```
   <numero>-<nome_atividade>/
   ├── assets/            # Capturas de tela (Ladder, IHM, árvore do projeto)
   ├── codesys_ext/           # Arquivos exportados (.EXP) e bibliotecas
   ├── docs/              # Modelagens formais (.tex, PDFs)
   └── README.md          # Documentação técnica completa da atividade
   ```

3. **Importação no MasterTool IEC:**
   - Crie um novo projeto selecionando o target **DU350/DU351**.
   - No menu `Projeto` $\to$ `Importar...`, carregue os arquivos `.EXP` da pasta `codesys_ext/` da atividade em desenvolvimento.

---

## Padrões de Código (IEC 61131-3)

- **Tipagem Rigorosa:** Declare explicitamente o tipo de dado de cada variável (`BOOL`, `INT`, `TIME`, etc.). Não deixe variáveis com tipos implícitos.
- **Endereçamento Padronizado:**
  - `%IXp.b`: Entradas digitais físicas (ex: `%IX0.0` para botão de partida).
  - `%QXp.b`: Saídas digitais físicas (ex: `%QX1.2` para lâmpada verde).
  - `%MXp.b`: Marcadores e memórias internas de estado (ex: `%MX0.0` a `%MX0.3` para estados $S_0$ a $S_3$).
- **Uso de Instruções Biestáveis (SET / RESET):**
  - Para máquinas de estados finitos em Ladder, garanta que a ativação de um novo estado execute o `SET` do estado sucessor e o `RESET` do estado predecessor.
  - Na partida do sistema, utilize detector de borda de subida (`R_TRIG`) para gerar pulso de inicialização de uma varredura, forçando o `RESET` de todos os demais estados.
- **Evitar Bobinas Duplicadas:** Nunca associe a mesma variável a múltiplas bobinas normais `( )` em redes distintas. Utilize lógica `OR` em paralelo se uma mesma saída depender de múltiplos estados.

---

## Diretrizes Específicas

### Versionamento de Arquivos .EXP
Como os arquivos de projeto `.pro` do CoDeSys v2.3 são binários proprietários (dificultando o rastreamento de *diffs* no Git), **é obrigatório exportar e versionar todos os módulos em formato `.EXP`** (texto puro) em cada commit de alteração lógica.

### Modelagem Formal Obrigatória
Nenhuma atividade prática deve ser implementada em Ladder sem sua prévia modelagem formal:
- Autômatos Finitos Determinísticos (AFD) para sequências simples e semáforos.
- Redes de Petri / Grafcet para processos com concorrência e paralelismo.

### Segurança e Intertravamentos
- Sistemas que controlam elementos conflitantes (ex: semáforos, sentido de rotação de motores, avanço/retorno de cilindros pneumáticos) devem conter **intertravamento obrigatório por software** e, sempre que possível, por fiação física de hardware.
- É estritamente proibido criar lógicas que permitam estados de perigo operacional (ex: ambas as vias com sinal verde simultâneo).

### Validação em Simulação e Bancada
Antes de submeter um Pull Request:
1. Compile o projeto no MasterTool IEC (`F11` / *Rebuild All*) garantindo **0 erros e 0 avisos**.
2. Execute o teste completo no modo de simulação integrada, testando todas as transições e condições anômalas (ex: múltiplos acionamentos de botões).
3. Caso a bancada física do Kit Training Box DUO TB131 esteja disponível, realize o download e anexe a confirmação de funcionamento real no PR.

---

*Diretrizes de Contribuição — Disciplina DCA3607 (Automação Industrial) — UFRN*
