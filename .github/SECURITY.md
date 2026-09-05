# Política de Segurança — DCA3607 Automação Industrial

## Versões Suportadas

Apenas a versão mais recente disponibilizada na branch `main` recebe suporte ativo, correções e atualizações de segurança lógica e operacional.

| Versão | Suportada | Observações |
| :--- | :---: | :--- |
| `main` (Atual) | Sim | Versão consolidada e validada para simulação e bancada |
| Branches antigas / releases arquivadas | Não | Descontinuadas após a entrega e avaliação das práticas |

---

## Reportando uma Vulnerabilidade ou Falha Crítica

Em sistemas de controle e automação industrial com Controladores Lógicos Programáveis (CLP), a segurança abrange tanto a integridade do código e dados quanto a **segurança física e operacional das pessoas e dos equipamentos de bancada**.

Por favor, **não abra uma Issue pública** para relatar falhas críticas que envolvam riscos operacionais ou vulnerabilidades. 

Situações consideradas críticas incluem:

1. **Falhas de Intertravamento Operacional:** Lógicas que permitam estados de perigo físico (por exemplo: acionamento simultâneo de sinais verdes em direções concorrentes de tráfego, comando simultâneo de avanço e retorno em atuadores, reversão direta de motor sem tempo de frenagem ou sobreposição de sinais elétricos em saídas do CLP).
2. **Riscos de Dano ao Kit Didático:** Programações que possam causar sobrecorrente em saídas a relé/transistor ou curtos-circuitos no kit **Training Box DUO TB131** / controlador **DU350/DU351**.
3. **Vazamento de Credenciais ou Dados Sensíveis:** Inclusão acidental de chaves de acesso, senhas de proteção de POUs, arquivos com configurações confidenciais de rede industrial ou dados de acesso remoto.
4. **Vulnerabilidades em Arquivos de Configuração ou Bibliotecas:** Arquivos `.EXP` ou `.LIB` corrompidos que possam travar o software de engenharia MasterTool IEC ou causar comportamento anômalo no processador do CLP.

---

## Como Relatar

Caso você identifique uma falha crítica de segurança lógica ou operacional:

1. **Não divulgue publicamente** em *issues*, comentários abertos ou fóruns.
2. Envie um relatório detalhado diretamente para os mantenedores do projeto (**Franklin Luiz Soares do Nascimento Filho**, **Dante Emiliano Freitas Faustino Sinedino de Oliveira**, **Marlucy Barbosa Costa**, **João Vitor Evangelista da Silva**, **Hélio Valério Lima do Nascimento Filho**) por e-mail ou mensagem direta acadêmica.
3. Descreva no relatório:
   - A atividade prática afetada (ex: pasta `3-sistema_semaforo`).
   - A rede (*network*) ou bloco funcional que apresenta o risco.
   - O cenário de falha observado ou passível de ocorrência no simulador ou bancada física.
   - Sugestão de intertravamento ou correção lógica, se disponível.

### Prazo de Resposta
A equipe de mantenedores analisará o relato, testará a correção no ambiente do MasterTool IEC e aplicará um *patch* corretivo na branch `main` em até **3 dias úteis**.

---

*Política de Segurança — Disciplina DCA3607 (Automação Industrial) — UFRN*
