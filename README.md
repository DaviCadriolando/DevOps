[README.md](https://github.com/user-attachments/files/33307948/README.md)
# Plano de Implementação de Práticas DevOps — A Tech

Desafio da Rocketseat: simular a adoção de práticas DevOps em uma empresa fictícia de software, usando **CALMS** e as **Três Maneiras do DevOps**.

## Sumário

- [Resumo executivo](#resumo-executivo)
- [Diagnóstico Cultural (C de CALMS)](#diagnóstico-cultural-c-de-calms)
- [Automação (A de CALMS)](#automação-a-de-calms)
- [Mensuração (M de CALMS)](#mensuração-m-de-calms)
- [Compartilhamento de conhecimento (S de CALMS)](#compartilhamento-de-conhecimento-s-de-calms)
- [As Três Maneiras do DevOps](#as-três-maneiras-do-devops)
- [Riscos e conclusão](#riscos-e-conclusão)

## Resumo executivo

O gargalo da A Tech não é a qualidade dos 14 desenvolvedores, e sim o processo de entrega: um deploy manual, sem padrão, que leva 2 dias para sair, falha em 20% das vezes e gera 2 incidentes por semana com recuperação média de 4 horas.

Este plano propõe tratar o fluxo **entrega de código → deploy → testes → monitoramento** como um problema compartilhado entre Dev e Ops. A solução combina cultura (responsabilidade compartilhada), automação (pipeline de CI/CD, testes automatizados, observabilidade) e aprendizado contínuo (métricas, feedback e experimentos).

**Escopo:** o piloto começa na Plataforma de E-commerce, que é mais nova e mais fácil de automatizar. O Sistema de Gestão de Vendas (legado em Delphi) entra depois, com adaptações, por causa do risco do conhecimento concentrado em uma só pessoa.

| Indicador | Hoje | Meta proposta (6 meses) |
| --- | --- | --- |
| Tempo entre entrega do código e deploy | 2 dias | Menos de 4 horas |
| Taxa de sucesso dos deploys | 80% | 95% ou mais |
| Incidentes após deploy | 2 por semana | Menos de 1 por semana |
| MTTR | 4 horas | Menos de 1 hora |

> As metas são propostas deste plano, não dados do enunciado, e devem ser ajustadas depois da linha de base medida na Fase 0.

## Diagnóstico Cultural (C de CALMS)

O processo escolhido é a **entrega e o deploy de código para produção**, porque concentra os quatro números ruins da empresa e é onde Dev e Ops mais se separam.

### Como funciona hoje

1. Os desenvolvedores terminam o recurso, montam um pacote de implantação e o encaminham à equipe de Operações.
2. Operações faz o deploy manualmente em produção, sem procedimento padronizado.
3. Operações testa manualmente o ambiente depois do deploy.
4. Operações monitora os logs do servidor à mão para achar falhas.

### Pontos de atrito entre as equipes

| Atrito | Efeito |
| --- | --- |
| O código é "jogado por cima do muro": Dev entrega e deixa de ser dono do resultado | Quem escreveu o código não vê o que acontece em produção e não aprende com as falhas |
| Ops recebe um pacote sem contexto, sem checklist e sem ambiente igual ao de desenvolvimento | Falhas por diferenças de configuração; deploy de 2 dias e 20% de falha |
| Os testes só acontecem depois do deploy, em produção | Bugs chegam ao cliente; 2 incidentes por semana |
| Monitoramento manual de logs | Incidentes descobertos tarde; MTTR de 4 horas |
| Ops (4 pessoas) sustenta tudo para 14 desenvolvedores e ainda enfrenta problemas de escalabilidade | Fila, sobrecarga e dependência de pessoas específicas |
| Só 1 pessoa conhece Delphi, a linguagem do sistema legado | Risco de ponto único de falha e deploys do legado ainda mais frágeis |

### Oportunidades de melhoria

- Trocar a lógica de "culpar quem errou" por responsabilidade compartilhada: Dev e Ops são donos do serviço do início ao fim.
- Mover os testes para antes do deploy (shift-left) e automatizá-los.
- Padronizar o deploy num procedimento único e repetível, em vez de uma tarefa artesanal.
- Transformar logs em alertas e painéis, para que a falha chame a equipe, e não o contrário.
- Reduzir o risco de conhecimento concentrado, documentando o legado e treinando mais pessoas.

## Automação (A de CALMS)

A proposta é um **pipeline de CI/CD** que leva o código do commit até a produção sem passos manuais, com testes e verificações no caminho e monitoramento automático depois.

### Solução proposta

| Etapa | Hoje | Com automação |
| --- | --- | --- |
| Integração | Pacote montado à mão pelo desenvolvedor | Cada commit dispara build automático (ex.: GitHub Actions, GitLab CI ou Jenkins) |
| Testes | Manuais, em produção, após o deploy | Testes unitários, de integração e smoke tests automáticos antes do deploy |
| Ambientes | Sem padrão | Ambientes de homologação e produção iguais, descritos como código (Docker e Infraestrutura como Código) |
| Deploy | Manual, sem procedimento | Deploy automatizado e repetível, com estratégia de rollback automático |
| Monitoramento | Leitura manual de logs | Logs centralizados, métricas, painéis e alertas (ex.: Prometheus, Grafana, ELK) |

As ferramentas são exemplos; a escolha final deve considerar o que a equipe já conhece e o custo.

### Plano de implementação em fases

1. **Fase 0 (2 semanas): linha de base e alinhamento.** Medir os quatro indicadores atuais, mapear o fluxo ponta a ponta e acordar o objetivo com Dev e Ops.
2. **Fase 1 (4 semanas): CI no e-commerce.** Build e testes unitários automáticos a cada commit, com repositório e padrões de branch definidos.
3. **Fase 2 (4 semanas): deploy automatizado em homologação.** Containerização, ambientes idênticos e smoke tests; deploy em produção ainda com aprovação manual de um clique.
4. **Fase 3 (4 semanas): produção automatizada e observabilidade.** Deploy contínuo com rollback automático, painéis e alertas no lugar da leitura manual de logs.
5. **Fase 4 (contínua): legado em Delphi.** Empacotar e automatizar o build do legado em passos pequenos, junto com a documentação do sistema.

### Como minimizar resistências

- **Começar pequeno:** o piloto no e-commerce mostra resultado rápido antes de mexer no legado.
- **Envolver Ops desde o desenho:** a equipe de 4 pessoas ajuda a definir o pipeline, para não sentir que a automação veio para substituí-la, e sim para tirar o trabalho repetitivo.
- **Combinar a automação com treinamento** em CI/CD, containers e monitoramento, no horário de trabalho.
- **Manter uma saída:** nas primeiras semanas o processo manual continua disponível como plano B, o que reduz o medo de mudar.
- **Mostrar ganhos com números:** comparar com a linha de base a cada fase.

## Mensuração (M de CALMS)

O impacto da automação será medido com as quatro métricas DORA, que já têm linha de base no enunciado, mais duas métricas de qualidade e de processo.

| Métrica | O que mostra | Linha de base | Meta (6 meses) |
| --- | --- | --- | --- |
| Lead time de entrega (código pronto até produção) | Velocidade do fluxo | 2 dias | Menos de 4 horas |
| Taxa de sucesso dos deploys | Qualidade da entrega | 80% | 95% ou mais |
| Incidentes após deploy | Estabilidade | 2 por semana | Menos de 1 por semana |
| MTTR | Capacidade de recuperação | 4 horas | Menos de 1 hora |
| Frequência de deploy | Capacidade de entregar valor em pequenos lotes | A medir na Fase 0 | Pelo menos semanal, idealmente diária |
| Cobertura de testes automatizados | Confiança antes do deploy | A medir na Fase 0 | 70% ou mais nos módulos críticos |

A frequência de deploy e a cobertura de testes não aparecem no enunciado, por isso entram como "a medir". As metas são propostas e devem ser revisadas quando a linha de base estiver completa.

### Como medir

- **Coleta automática:** o pipeline registra horário de commit, de deploy e resultado; a ferramenta de monitoramento registra incidentes e tempo de recuperação.
- **Painel único** visível para Dev e Ops, atualizado sem trabalho manual.
- **Revisão mensal** das métricas numa reunião conjunta, comparando com a linha de base e com a meta.
- **Cuidado com o uso das métricas:** servem para melhorar o processo, nunca para ranquear ou punir pessoas.

## Compartilhamento de conhecimento (S de CALMS)

O conhecimento hoje está concentrado em pessoas, e o caso mais crítico é o Delphi: só um desenvolvedor domina o sistema legado. O plano de compartilhamento ataca esse risco e cria hábitos de colaboração entre Dev e Ops.

| Ação | Frequência | Resultado esperado |
| --- | --- | --- |
| Documentar o pipeline, o procedimento de deploy e o rollback num repositório de documentação (runbooks) | Contínua, revisada a cada fase | Qualquer pessoa consegue executar e entender o processo |
| Mapear e documentar o sistema legado em Delphi, com o desenvolvedor especialista como guia | Sessões semanais durante a Fase 4 | Reduz o risco de ponto único de falha |
| Pareamento (pair programming) do especialista em Delphi com mais 2 desenvolvedores | Semanal | Formação de um grupo mínimo com acesso ao legado |
| Pareamento entre Dev e Ops no desenho e na operação do pipeline | Durante as Fases 1 a 3 | Responsabilidade compartilhada e confiança mútua |
| Tech talks internas de 30 minutos sobre o que foi aprendido | Quinzenal | Difusão do conhecimento e reconhecimento do time |
| Revisões sem culpa (blameless postmortems) de cada incidente, publicadas no repositório | A cada incidente | Aprendizado coletivo sem medo de punição |
| Canal único de comunicação entre Dev e Ops para deploys e incidentes | Permanente | Menos ruído e resposta mais rápida |

A colaboração será reforçada por uma regra simples: quem escreve o código participa do plantão e do acompanhamento do deploy do que entregou ("você constrói, você opera"), com apoio de Operações no começo.

## As Três Maneiras do DevOps

### Primeira Maneira: acelerar o fluxo

O objetivo é reduzir o tempo entre o código pronto e o valor entregue ao cliente, hoje de 2 dias.

- **Eliminar esperas:** o pacote deixa de ser "encaminhado" a Operações; o pipeline executa o deploy sozinho depois que os testes passam.
- **Entregar em lotes pequenos:** mudanças menores e mais frequentes têm menos risco e são mais fáceis de reverter.
- **Padronizar e automatizar** o que é repetitivo (build, testes, deploy), liberando as 4 pessoas de Operações para trabalhar em escalabilidade e desempenho.
- **Tornar o trabalho visível** num quadro único (kanban) com o fluxo do commit à produção, para achar onde o trabalho fica parado.

### Segunda Maneira: ampliar o feedback

O objetivo é que problemas sejam descobertos o mais cedo e o mais perto de quem os causou.

- **Feedback técnico rápido:** testes automáticos a cada commit avisam o desenvolvedor em minutos, não depois do deploy.
- **Feedback de produção:** alertas e painéis mostram erros e desempenho para Dev e Ops ao mesmo tempo.
- **Feedback dos clientes:** coleta periódica por pesquisa curta após entregas relevantes, canal de suporte e análise dos incidentes; o resultado é discutido na revisão mensal.
- **Feedback entre equipes:** retrospectiva conjunta de Dev e Ops ao fim de cada ciclo, com itens de melhoria que entram no backlog do time.

### Terceira Maneira: experimentar e aprender

O objetivo é criar uma cultura em que errar com segurança é uma forma de aprender.

- **Revisões sem culpa** dos incidentes, focadas em causas do processo e não em pessoas.
- **Experimentos pequenos e controlados**, como liberar uma funcionalidade para 5% dos usuários (feature flags, canary release) e medir antes de ampliar.
- **Tempo protegido para melhoria:** uma parte fixa de cada ciclo (por exemplo, 10% a 20% da capacidade) para reduzir dívida técnica e testar ideias.
- **Exercícios de falha** (game days): simular uma queda em ambiente seguro para treinar a resposta e baixar o MTTR.
- **Celebrar o aprendizado:** registrar e divulgar o que cada experimento ensinou, mesmo quando não deu certo.

## Riscos e conclusão

Os principais riscos do plano são de pessoas e de legado, não de tecnologia.

| Risco | Mitigação |
| --- | --- |
| Resistência de Ops ou de Dev à mudança | Envolvimento desde o desenho, treinamento, piloto pequeno e ganhos mostrados com números |
| Dependência do único especialista em Delphi | Pareamento, documentação e formação de ao menos mais 2 pessoas com acesso ao legado |
| Automação mal feita gerar falhas mais rápidas | Testes automáticos antes do deploy, rollback automático e liberação gradual |
| Metas irreais ou métricas usadas para punir | Revisar metas após a linha de base e usar as métricas só para melhoria do processo |
| Sobrecarga de Ops durante a transição | Fases curtas, processo manual como plano B e priorização do que mais reduz trabalho repetitivo |

Em resumo, o plano atua nos três eixos do DevOps ao mesmo tempo: **cultura** (responsabilidade compartilhada e revisões sem culpa), **automação** (CI/CD, testes e observabilidade) e **medição com aprendizado** (métricas DORA, feedback e experimentos). Com 18 semanas de implantação, a expectativa é reduzir o tempo de entrega de 2 dias para horas, aumentar a taxa de sucesso dos deploys e diminuir incidentes e MTTR, entregando valor mais rápido e com mais segurança aos clientes da A Tech.

---

Feito como parte do desafio de DevOps da [Rocketseat](https://www.rocketseat.com.br/).
